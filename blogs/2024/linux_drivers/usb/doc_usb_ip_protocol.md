
# USB/IP protocol

# 1. Architecture

USB/IP 协议遵循服务器/客户端架构。 服务器导出 USB 设备，客户端导入它们。 导出的 USB 设备的设备驱动程序在客户端计算机上运行。

客户端可能会询问导出的 USB 设备的列表。 为了获取该列表，客户端打开到服务器的 TCP/IP 连接，并在 TCP/IP 连接之上发送 OP_REQ_DEVLIST 数据包（因此实际的 OP_REQ_DEVLIST 可能会在低层传输层以一个或多个块的形式发送）。 服务器发回 OP_REP_DEVLIST 数据包，其中列出了导出的 USB 设备。 最后，TCP/IP 连接关闭。

```bash
virtual host controller                                 usb host
     "client"                                           "server"
 (imports USB devices)                             (exports USB devices)
         |                                                 |
         |                  OP_REQ_DEVLIST                 |
         | ----------------------------------------------> |
         |                                                 |
         |                  OP_REP_DEVLIST                 |
         | <---------------------------------------------- |
         |                                                 |
```

一旦客户端知道导出的 USB 设备列表，它可能会决定使用其中之一。 首先，客户端打开到服务器的 TCP/IP 连接并发送 OP_REQ_IMPORT 数据包。 服务器回复 OP_REP_IMPORT。 如果导入成功，TCP/IP 连接将保持打开状态，并将用于在客户端和服务器之间传输 URB 流量。 客户端可以发送两种类型的数据包：用于提交 URB 的 USBIP_CMD_SUBMIT 和用于取消链接先前提交的 URB 的 USBIP_CMD_UNLINK。 服务器的回答可以分别是USBIP_RET_SUBMIT和USBIP_RET_UNLINK。

```bash
virtual host controller                                 usb host
     "client"                                           "server"
 (imports USB devices)                             (exports USB devices)
         |                                                 |
         |                  OP_REQ_IMPORT                  |
         | ----------------------------------------------> |
         |                                                 |
         |                  OP_REP_IMPORT                  |
         | <---------------------------------------------- |
         |                                                 |
         |                                                 |
         |            USBIP_CMD_SUBMIT(seqnum = n)         |
         | ----------------------------------------------> |
         |                                                 |
         |            USBIP_RET_SUBMIT(seqnum = n)         |
         | <---------------------------------------------- |
         |                        .                        |
         |                        :                        |
         |                                                 |
         |            USBIP_CMD_SUBMIT(seqnum = m)         |
         | ----------------------------------------------> |
         |                                                 |
         |            USBIP_CMD_SUBMIT(seqnum = m+1)       |
         | ----------------------------------------------> |
         |                                                 |
         |            USBIP_CMD_SUBMIT(seqnum = m+2)       |
         | ----------------------------------------------> |
         |                                                 |
         |            USBIP_RET_SUBMIT(seqnum = m)         |
         | <---------------------------------------------- |
         |                                                 |
         |            USBIP_CMD_SUBMIT(seqnum = m+3)       |
         | ----------------------------------------------> |
         |                                                 |
         |            USBIP_RET_SUBMIT(seqnum = m+1)       |
         | <---------------------------------------------- |
         |                                                 |
         |            USBIP_CMD_SUBMIT(seqnum = m+4)       |
         | ----------------------------------------------> |
         |                                                 |
         |            USBIP_RET_SUBMIT(seqnum = m+2)       |
         | <---------------------------------------------- |
         |                        .                        |
         |                        :                        |
```

对于UNLINK，请注意，成功USBIP_RET_UNLINK后，未链接的URB提交将不会有相应的USBIP_RET_SUBMIT（这在drivers/usb/usbip/stub_rx.c的函数stub_recv_cmd_unlink中进行了解释）。

```bash
virtual host controller                                 usb host
     "client"                                           "server"
 (imports USB devices)                             (exports USB devices)
         |                                                 |
         |            USBIP_CMD_SUBMIT(seqnum = p)         |
         | ----------------------------------------------> |
         |                                                 |
         |               USBIP_CMD_UNLINK                  |
         |         (seqnum = p+1, unlink_seqnum = p)       |
         | ----------------------------------------------> |
         |                                                 |
         |               USBIP_RET_UNLINK                  |
         |        (seqnum = p+1, status = -ECONNRESET)     |
         | <---------------------------------------------- |
         |                                                 |
         |         Note: No USBIP_RET_SUBMIT(seqnum = p)   |
         | <--X---X---X---X---X---X---X---X---X---X---X--- |
         |                        .                        |
         |                        :                        |
         |                                                 |
         |            USBIP_CMD_SUBMIT(seqnum = q)         |
         | ----------------------------------------------> |
         |                                                 |
         |            USBIP_RET_SUBMIT(seqnum = q)         |
         | <---------------------------------------------- |
         |                                                 |
         |               USBIP_CMD_UNLINK                  |
         |         (seqnum = q+1, unlink_seqnum = q)       |
         | ----------------------------------------------> |
         |                                                 |
         |               USBIP_RET_UNLINK                  |
         |           (seqnum = q+1, status = 0)            |
         | <---------------------------------------------- |
         |                                                 |
```

这些字段采用网络（大端）字节顺序，这意味着最高有效字节 (MSB) 存储在最低地址。


# 2. Protocol Version

记录的 USBIP 版本是 v1.1.1。 消息标头中此版本的二进制表示形式是 0x0111。

这是在tools/usb/usbip/configure.ac中定义的。


# 3. Message Format

OP_REQ_DEVLIST:

检索导出的 USB 设备列表。

| Offset | Length | Value      | Description                 |
| ------ | ------ | ---------- | --------------------------- |
| 0      | 2      |            | USBIP 版本                  |
| 2      | 2      | 0x8005     | 命令码：获取导出的USB设备列表 |
| 4      | 4      | 0x00000000 | 状态：未使用，应设置为0       |

OP_REP_DEVLIST:

回复导出的 USB 设备列表。

| Offset | Length | Value      | Description                |
| ------ | ------ | ---------- | -------------------------- |
| 0      | 2      |            | USBIP 版本                  |
| 2      | 2      | 0x0005     | 回复码：导出的USB设备列表    |
| 4      | 4      | 0x00000000 | 状态：0 表示正常             |
| 8      | 4      | n          | 导出设备数量：0表示无导出设备 |
| 0x0C   |        |            | 从现在开始，将描述导出的 n 个设备（如果有）。 如果没有导出任何设备，则消息将以之前的“导出设备数量”字段结束 |
|        | 256    |            | path：导出 USB 设备的主机上的设备路径，以零字节结束的字符串，例如 "/sys/devices/pci0000:00/0000:00:1d.1/usb3/3-2" 未使用的字节应用零字节填充 |
| 0x10C  | 32     |            | busid：导出设备的总线ID，以零字节结束的字符串，例如 “3-2”。 未使用的字节应用零字节填充s |
| 0x12C  | 4      |            | busnum                     |
| 0x130  | 4      |            | devnum                     |
| 0x134  | 4      |            | speed                      |
| 0x138  | 2      |            | idVendor                   |
| 0x13A  | 2      |            | idProduct                  |
| 0x13C  | 2      |            | bcdDevice                  |
| 0x13E  | 1      |            | bDeviceClass               |
| 0x13F  | 1      |            | bDeviceSubClass            |
| 0x140  | 1      |            | bDeviceProtocol            |
| 0x141  | 1      |            | bConfigurationValue        |
| 0x142  | 1      |            | bNumConfigurations         |
| 0x143  | 1      |            | bNumInterfaces             |
| 0x144  | m_0    |            | 从现在开始，对每个接口进行描述，总共 bNumInterfaces 次，具有以下 4 个字段 |
|        | 1      |            | bInterfaceClass            |
| 0x145  | 1      |            | bInterfaceSubClass         |
| 0x146  | 1      |            | bInterfaceProtocol         |
| 0x147  | 1      |            | 用于对齐的填充字节，应设置为零 |
| 0xC + i*0x138 + m_(i-1)*4 | || 第二个导出的 USB 设备从 i=1 开始，路径字段为 |

OP_REQ_IMPORT:

请求导入（连接）远程 USB 设备。

| Offset | Length | Value      | Description           |
| ------ | ------ | ---------- | --------------------- |
| 0      | 2      |            | USBIP 版本             |
| 2      | 2      | 0x8003     | 命令码：导入远程USB设备 |
| 4      | 4      | 0x00000000 | 状态：未使用，应设置为0 |
| 8      | 32     |            | busid：远程主机上导出设备的busid。 可能的值取自消息字段 OP_REP_DEVLIST.busid。 以零结束的字符串，未使用的字节应用零填充 |

OP_REP_IMPORT:

回复导入（连接）远程 USB 设备。

| Offset | Length | Value      | Description                |
| ------ | ------ | ---------- | -------------------------- |
| 0      | 2      |            | USBIP 版本                 |
| 2      | 2      | 0x0003     | 回复码：回复导入            |
| 4      | 4      | 0x00000000 | 状态：0 表示正常，1 表示错误 |
| 8      |        |            | 从现在开始，如果之前的状态字段为 OK (0)，则显示导入设备的详细信息，否则回复以状态字段结束 |
|        | 256    |            | path：导出 USB 设备的主机上的设备路径，以零字节结束的字符串，例如 "/sys/devices/pci0000:00/0000:00:1d.1/usb3/3-2" 未使用的字节应用零字节填充 |
| 0x108  | 32     |            | busid：导出设备的总线ID，以零字节结束的字符串，例如 “3-2”。 未使用的字节应用零字节填充 |
| 0x128  | 4      |            | busnum                     |
| 0x12C  | 4      |            | devnum                     |
| 0x130  | 4      |            | speed                      |
| 0x134  | 2      |            | idVendor                   |
| 0x136  | 2      |            | idProduct                  |
| 0x138  | 2      |            | bcdDevice                  |
| 0x139  | 1      |            | bDeviceClass               |
| 0x13A  | 1      |            | bDeviceSubClass            |
| 0x13B  | 1      |            | bDeviceProtocol            |
| 0x13C  | 1      |            | bConfigurationValue        |
| 0x13D  | 1      |            | bNumConfigurations         |
| 0x13E  | 1      |            | bNumInterfaces             |

以下四个命令有一个称为“usbip_header_basic”的通用基本标头，并且它们的标头（称为“usbip_header”）（在transfer_buffer有效负载之前）具有相同的长度，因此需要填充。

usbip_header_basic:

| Offset | Length | Description                                                          |
| ------ | ------ | -------------------------------------------------------------------- |
| 0      | 4      | 命令                                                                 |
| 4      | 4      | seqnum：标识请求和相应响应的序列号； 每个连接递增                        |
| 8      | 4      | devid：唯一指定一个远程USB设备，而不是busnum和devnum； 对于客户端（请求），该值为 ((busnum << 16) | devnum)； 对于服务器（响应），应设置为 0 |
| 0xC    | 4      | 方向：0：USBIP_DIR_OUT，1：USBIP_DIR_IN，仅由客户端使用，对于服务器应为0 |
| 0x10   | 4      | ep：仅由客户端使用的端点号，对于服务器来说应为0； 对于 UNLINK，该值为 0   |

USBIP_CMD_SUBMIT:

Submit an URB

| Offset | Length | Description                                            |
| ------ | ------ | ------------------------------------------------------ |
| 0      | 20     | usbip_header_basic，“命令”应为 0x00000001               |
| 0x14   | 4      | Transfer_flags：可能的值取决于 USBIP_URB Transfer_flags。 请参阅 include/uapi/linux/usbip.h 和 USB 请求块 (URB)。 参考drivers/usb/usbip/usbip_common.c中的usbip_pack_cmd_submit()和tweak_transfer_flags()           |
| 0x18   | 4      | Transfer_buffer_length：使用URB Transfer_buffer_length  |
| 0x1C   | 4      | start_frame: 使用URB start_frame； ISO 传输的初始帧； 如果不是 ISO 传输则应设置为 0 |
| 0x20   | 4      | number_of_packets: ISO 数据包数量； 如果不是 ISO 传输，则应设置为 0xffffffff |
| 0x24   | 4      | interval: 服务器端主机控制器上请求的最长时间              |
| 0x28   | 8      | setup: USB 设置的数据字节，如果不使用则用零填充           |
| 0x30   | n      | 传输缓冲区。 如果方向是USBIP_DIR_OUT，则n等于transfer_buffer_length； 否则 n 等于 0。对于 ISO 传输，不传输每个 ISO 数据包之间的填充 |
| 0x30+n | m      | iso_packet_descriptor                                  |

USBIP_RET_SUBMIT:

回复提交 URB。

| Offset | Length | Description                                    |
| ------ | ------ | ---------------------------------------------- |
| 0      | 20     | usbip_header_basic, “命令”应为 0x00000003       |
| 0x14   | 4      | status: 成功的 URB 交易为零，否则发生某种错误     |
| 0x18   | 4      | actual_length: URB 数据字节数； 使用URB实际长度  |
| 0x1C   | 4      | start_frame: 使用URB start_frame； ISO 传输的初始帧； 如果不是 ISO 传输则应设置为 0 |
| 0x20   | 4      | number_of_packets: ISO 数据包数量； 如果不是 ISO 传输，则应设置为 0xffffffff |
| 0x24   | 4      | error_count                                    |
| 0x28   | 8      | 填充，应设置为 0                                |
| 0x30   | n      | 传输缓冲区。 如果方向为 USBIP_DIR_IN，则 n 等于实际长度； 否则 n 等于 0。对于 ISO 传输，不传输每个 ISO 数据包之间的填充 |
| 0x30+n | m      | iso_packet_descriptor                          |

USBIP_CMD_UNLINK:

Unlink an URB

| Offset | Length | Description                               |
| ------ | ------ | ----------------------------------------- |
| 0      | 20     | usbip_header_basic, “命令”应为 0x00000002  |
| 0x14   | 4      | unlink_seqnum, 取消链接的 SUBMIT 请求      |
| 0x18   | 24     | 填充，应设置为 0                           |

USBIP_RET_UNLINK:

回复 URB 取消链接。

| Offset | Length | Description                              |
| ------ | ------ | ---------------------------------------- |
| 0      | 20     | usbip_header_basic, “命令”应为 0x00000004 |
| 0x14   | 4      | status: 这与USBIP_RET_SUBMIT的状态类似（共享相同的内存偏移量）。 UNLINK成功时，状态为-ECONNRESET； 当USBIP_CMD_UNLINK在USBIP_RET_SUBMIT之后状态为0时 |
| 0x18   | 24     | 填充，应设置为 0                          |


# 4. EXAMPLE

以下数据是通过人机接口设备 (HID) 有效负载从线路捕获的：

```bash
CmdIntrIN:  00000001 00000d05 0001000f 00000001 00000001 00000200 00000040 ffffffff 00000000 00000004 00000000 00000000
CmdIntrOUT: 00000001 00000d06 0001000f 00000000 00000001 00000000 00000040 ffffffff 00000000 00000004 00000000 00000000
            ffffffff860008a784ce5ae212376300000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
RetIntrOut: 00000003 00000d06 00000000 00000000 00000000 00000000 00000040 ffffffff 00000000 00000000 00000000 00000000
RetIntrIn:  00000003 00000d05 00000000 00000000 00000000 00000000 00000040 ffffffff 00000000 00000000 00000000 00000000
            ffffffff860011a784ce5ae2123763612891b1020100000400000000000000000000000000000000000000000000000000000000000000000000000000000000
```
