
# usbmon

# 1. Introduction

小写的名称“usbmon”指的是内核中的一个工具，用于收集 USB 总线上的 I/O 痕迹。 此函数类似于 tcpdump(1) 或 Ethereal 等网络监控工具使用的数据包套接字。 同样，预计使用 usbdump 或 USBMon（大写字母）等工具来检查 usbmon 生成的原始跟踪。

usbmon 向主机控制器驱动程序 (HCD) 报告外设特定驱动程序发出的请求。 因此，如果 HCD 有错误，usbmon 报告的跟踪可能无法准确对应总线事务。 这与 tcpdump 的情况相同。

目前实现了两个 API：“text”和“binary”。 二进制 API 可通过 /dev 命名空间中的字符设备使用，并且是一个 ABI。 文本 API 自 2.6.35 起已弃用，但为了方便起见仍可用。


# 2. How to use usbmon to collect raw text traces

与数据包套接字不同，usbmon 有一个以文本格式提供跟踪的接口。 这有两个目的。 首先，它作为工具的通用跟踪交换格式，同时最终确定了更复杂的格式。 其次，在没有工具的情况下，人类可以阅读它。

要收集原始文本跟踪，请执行以下步骤。

1. Prepare

挂载 debugfs（必须在内核配置中启用它），并加载 usbmon 模块（如果构建为模块）。 如果内核内置了 usbmon，则跳过第二步：

```bash
# mount -t debugfs none_debugs /sys/kernel/debug
# modprobe usbmon
#
```

验证总线插座是否存在：

```bash
# ls /sys/kernel/debug/usb/usbmon
0s  0u  1s  1t  1u  2s  2t  2u  3s  3t  3u  4s  4t  4u
#
```

现在，您可以选择使用套接字“0u”（捕获所有总线上的数据包），并跳到步骤 #3，或者通过步骤 #2 查找设备使用的总线。 这可以过滤掉不断说话的烦人设备。

2. Find which bus connects to the desired device

运行“cat /sys/kernel/debug/usb/devices”，找到设备对应的T线。 通常，您可以通过查找供应商字符串来完成此操作。 如果您有许多类似的设备，请拔下其中一个并比较两个 /sys/kernel/debug/usb/devices 输出。 T 线将有巴士号码。

示例:

```bash
T:  Bus=03 Lev=01 Prnt=01 Port=00 Cnt=01 Dev#=  2 Spd=12  MxCh= 0
D:  Ver= 1.10 Cls=00(>ifc ) Sub=00 Prot=00 MxPS= 8 #Cfgs=  1
P:  Vendor=0557 ProdID=2004 Rev= 1.00
S:  Manufacturer=ATEN
S:  Product=UC100KM V2.00
```

“Bus=03”表示它是总线 3。或者，您可以查看“lsusb”的输出并从相应的线路获取总线号。 例子：

总线 003 设备 002：ID 0557:2004 ATEN UC100KM V2.00

3. Start 'cat'

```bash
# cat /sys/kernel/debug/usb/usbmon/3u > /tmp/1.mon.out
```

要侦听单个总线，否则要侦听所有总线，请键入：

```bash
# cat /sys/kernel/debug/usb/usbmon/0u > /tmp/1.mon.out
```

该进程将一直读取直到被杀死。 当然，输出可以重定向到所需的位置。 这是首选，因为它会很长。

4. Perform the desired operation on the USB bus

您可以在此处执行一些创建流量的操作：插入闪存密钥、复制文件、控制网络摄像头等。

5. Kill cat

通常它是通过键盘中断（Control-C）来完成的。

此时，可以保存输出文件（本例中为/tmp/1.mon.out）、通过电子邮件发送或使用文本编辑器检查。 在最后一种情况下，请确保文件大小对于您喜欢的编辑器来说不会太大。


# 3. Raw text data format

目前支持两种格式：原始格式或“1t”格式和“1u”格式。 内核 2.6.21 中已弃用“1t”格式。 “1u”格式添加了一些字段，例如 ISO 帧描述符、间隔等。它产生的行稍长，但在其他方面是“1t”格式的完美超集。

如果需要在程序中识别一个，请查看“地址”字（见下文），其中“1u”格式添加总线编号。 如果存在 2 个冒号，则为“1t”格式，否则为“1u”。

任何文本格式的数据都由事件流组成，例如URB提交、URB回调、提交错误。 每个事件都是一个文本行，由空格分隔的单词组成。 单词的数量或位置可能取决于事件类型，但有一组单词对所有类型都是通用的。

这是单词列表，从左到右：

- URB Tag. 它用于标识 URB，通常是十六进制 URB 结构的内核地址，但在合理范围内也可以是序列号或任何其他唯一字符串。

- 时间戳（以微秒为单位），十进制数。 时间戳的分辨率取决于可用时钟，因此它可能比微秒差得多（例如，如果实现使用 jiffies）。

- 事件类型。 该类型指的是事件的格式，而不是URB类型。 可用类型有：S - 提交、C - 回调、E - 提交错误。

- “地址”一词（以前是“管道”）。 它由四个字段组成，用冒号分隔：URB 类型和方向、总线号、设备地址、端点号。 类型和方向按以下方式用两个字节进行编码：

|    |    |               |
| -- | -- | ------------- |
| Ci | Co | 控制输入输出   |
| Zi | Zo | 同步输入和输出 |
| Ii | Io | 中断输入和输出 |
| Bi | Bo | 批量输入和输出 |

&emsp;总线号、设备地址和端点都是十进制数，但为了方便读者阅读，它们可能有前导零。

- URB Status word. 它可以是一个字母，也可以是用冒号分隔的几个数字：URB 状态、间隔、起始帧和错误计数。 与“地址”字不同，保存状态的所有字段都是可选的。 仅针对中断和等时 URB 打印间隔。 仅针对同步 URB 打印起始帧。 仅针对同步回调事件打印错误计数。

&emsp;状态字段是十进制数，有时为负数，表示URB的“状态”字段。 该字段对于提交没有任何意义，但无论如何都会存在以帮助脚本进行解析。 当发生错误时，该字段包含错误代码。

&emsp;在提交控制数据包的情况下，该字段包含设置标签而不是一组数字。 很容易判断设置标签是否存在，因为它从来都不是数字。 因此，如果脚本在该字中找到一组数字，它们将继续读取数据长度（同步 URB 除外）。 如果他们发现其他东西，比如字母，他们会在读取数据长度或等时描述符之前读取设置数据包。

- 设置数据包（如果存在）由 5 个字组成：bmRequestType、bRequest、wValue、wIndex、wLength 各一个，如 USB 规范 2.0 所指定。 如果设置标签是“s”，则这些字可以安全解码。 否则，设置数据包存在，但未捕获，并且字段包含填充符。

- 同步帧描述符和描述符本身的数量。 如果同步传输事件有一组描述符，则首先打印 URB 中的描述符总数，然后打印每个描述符一个字，总共最多 5 个。该字由 3 个以冒号分隔的十进制数字组成，分别表示状态、偏移量 、 和 分别是长度。 对于提交的内容，会报告初始长度。 对于回调，报告实际长度。

- 数据长度。 对于提交，这是所要求的长度。 对于回调，这是实际长度。

- 数据标签。 即使长度非零，usbmon 也可能并不总是捕获数据。 仅当该标签为“=”时，数据字才会出现。

- 随后是大端十六进制格式的数据字。 请注意，它们不是机器字，而实际上只是分割成字的字节流，以便于阅读。 因此，最后一个字可能包含一到四个字节。 收集的数据的长度是有限的，可以小于数据长度字中报告的数据长度。 在等时输入 (Zi) 完成的情况下，接收到的数据在缓冲区中稀疏，收集到的数据的长度可能大于数据长度值（因为数据长度仅计算接收到的字节，而数据字 包含整个传输缓冲区）。

示例：

用于获取端口状态的输入控制传输：

```bash
d5ea89a0 3575914555 S Ci:1:001:0 s a3 00 0000 0003 0004 4 <
d5ea89a0 3575914560 C Ci:1:001:0 0 4 = 01050000
```

输出批量传输，将 31 字节批量包装器中的 SCSI 命令 0x28 (READ_10) 发送到地址 5 处的存储设备：

```bash
dd65f0e8 4128379752 S Bo:1:005:2 -115 31 = 55534243 ad000000 00800000 80010a28 20000000 20000040 00000000 000000
dd65f0e8 4128379808 C Bo:1:005:2 0 31 >
```


# 4. Raw binary format and API

API的整体架构与上面的架构大致相同，只是事件以二进制格式传递。 每个事件都按以下结构发送（其名称是虚构的，以便我们参考）：

```bash
struct usbmon_packet {
      u64 id;                 /*  0: URB ID - from submission to callback */
      unsigned char type;     /*  8: Same as text; extensible. */
      unsigned char xfer_type; /*    ISO (0), Intr, Control, Bulk (3) */
      unsigned char epnum;    /*     Endpoint number and transfer direction */
      unsigned char devnum;   /*     Device address */
      u16 busnum;             /* 12: Bus number */
      char flag_setup;        /* 14: Same as text */
      char flag_data;         /* 15: Same as text; Binary zero is OK. */
      s64 ts_sec;             /* 16: gettimeofday */
      s32 ts_usec;            /* 24: gettimeofday */
      int status;             /* 28: */
      unsigned int length;    /* 32: Length of data (submitted or actual) */
      unsigned int len_cap;   /* 36: Delivered length */
      union {                 /* 40: */
              unsigned char setup[SETUP_LEN]; /* Only for Control S-type */
              struct iso_rec {                /* Only for ISO */
                      int error_count;
                      int numdesc;
              } iso;
      } s;
      int interval;           /* 48: Only for Interrupt and ISO */
      int start_frame;        /* 52: For ISO */
      unsigned int xfer_flags; /* 56: copy of URB's transfer_flags */
      unsigned int ndesc;     /* 60: Actual number of ISO descriptors */
};                            /* 64 total length */
```

可以通过使用 read(2)、ioctl(2) 读取或使用 mmap 访问缓冲区来从字符设备接收这些事件。 但是，出于兼容性原因，read(2) 仅返回前 48 个字节。

字符设备通常称为 /dev/usbmonN，其中 N 是 USB 总线号。 数字零 (/dev/usbmon0) 很特殊，表示“所有总线”。 请注意，特定的命名策略由您的 Linux 发行版设置。

如果您手动创建 /dev/usbmon0，请确保它由 root 所有并且模式为 0600。否则，非特权用户将能够窥探键盘流量。

以下 ioctl 调用可用，MON_IOC_MAGIC 0x92：

&emsp;MON_IOCQ_URB_LEN, defined as _IO(MON_IOC_MAGIC, 1)

此调用返回下一个事件中的数据长度。 请注意，大多数事件不包含数据，因此如果此调用返回零，并不意味着没有可用的事件。

&emsp;MON_IOCG_STATS, defined as _IOR(MON_IOC_MAGIC, 3, struct mon_bin_stats)

参数是指向以下结构的指针：

```bash
struct mon_bin_stats {
      u32 queued;
      u32 dropped;
};
```

成员“queued”是指当前在缓冲区中排队的事件数（而不是指自上次重置以来处理的事件数）。

成员“dropped”是自上次调用 MON_IOCG_STATS 以来丢失的事件数。

&emsp;MON_IOCT_RING_SIZE, defined as _IO(MON_IOC_MAGIC, 4)

该调用设置缓冲区大小。 参数是以字节为单位的大小。 大小可以向下舍入到下一个块（或页）。 如果请求的大小超出该内核的[未指定]范围，则调用将失败并返回 -EINVAL。

&emsp;MON_IOCQ_RING_SIZE, defined as _IO(MON_IOC_MAGIC, 5)

此调用返回缓冲区的当前大小（以字节为单位）。

&emsp;MON_IOCX_GET, defined as _IOW(MON_IOC_MAGIC, 6, struct mon_get_arg) MON_IOCX_GETX, defined as _IOW(MON_IOC_MAGIC, 10, struct mon_get_arg)

如果内核缓冲区中没有事件，这些调用将等待事件到达，然后返回第一个事件。 参数是指向以下结构的指针：

```bash
struct mon_get_arg {
      struct usbmon_packet *hdr;
      void *data;
      size_t alloc;           /* Length of data (can be zero) */
};
```

在调用之前，应填写 hdr、data 和 alloc。 返回时，hdr 指向的区域包含下一个事件结构，数据缓冲区包含数据（如果有）。 该事件将从内核缓冲区中删除。

MON_IOCX_GET 复制 48 个字节到 hdr 区域，MON_IOCX_GETX 复制 64 个字节。

&emsp;MON_IOCX_MFETCH, defined as _IOWR(MON_IOC_MAGIC, 7, struct mon_mfetch_arg)

该 ioctl 主要在应用程序使用 mmap(2) 访问缓冲区时使用。 它的参数是指向以下结构的指针：

```bash
struct mon_mfetch_arg {
      uint32_t *offvec;       /* Vector of events fetched */
      uint32_t nfetch;        /* Number of events to fetch (out: fetched) */
      uint32_t nflush;        /* Number of events to flush */
};
```

ioctl 分 3 个阶段运行。

首先，它从内核缓冲区中删除并丢弃最多 nflush 事件。 实际丢弃的事件数在 nflush 中返回。

其次，它等待缓冲区中出现事件，除非使用 O_NONBLOCK 打开伪设备。

第三，它将最多 nfetch 偏移量提取到 mmap 缓冲区中，并将它们存储到 offvec 中。 事件偏移的实际数量存储在 nfetch 中。

&emsp;MON_IOCH_MFLUSH, defined as _IO(MON_IOC_MAGIC, 8)

此调用从内核缓冲区中删除许多事件。 它的参数是要删除的事件数。 如果缓冲区包含的事件少于请求的事件，则删除所有存在的事件，并且不报告错误。 当没有事件可用时，这也有效。

&emsp;FIONBIO

如果有需要，ioctl FIONBIO 可能会在将来实现。

除了 ioctl(2) 和 read(2) 之外，二进制 API 的特殊文件还可以使用 select(2) 和 poll(2) 进行轮询。 但 lseek(2) 不起作用。

- Memory-mapped access of the kernel buffer for the binary API

基本思想很简单：

为了准备，通过获取当前大小来映射缓冲区，然后使用 mmap(2)。 然后，执行类似于下面用伪代码编写的循环：

```c
struct mon_mfetch_arg fetch;
struct usbmon_packet *hdr;
int nflush = 0;
for (;;) {
   fetch.offvec = vec; // Has N 32-bit words
   fetch.nfetch = N;   // Or less than N
   fetch.nflush = nflush;
   ioctl(fd, MON_IOCX_MFETCH, &fetch);   // Process errors, too
   nflush = fetch.nfetch;       // This many packets to flush when done
   for (i = 0; i < nflush; i++) {
      hdr = (struct ubsmon_packet *) &mmap_area[vec[i]];
      if (hdr->type == '@')     // Filler packet
         continue;
      caddr_t data = &mmap_area[vec[i]] + 64;
      process_packet(hdr, data);
   }
}
```

因此，主要思想是每 N 个事件仅执行一个 ioctl。

虽然缓冲区是循环的，但返回的标头和数据不会越过缓冲区的末尾，因此上面的伪代码不需要任何聚集。
