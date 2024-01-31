
# Gadget Testing

该文件总结了小工具提供的 USB 功能的基本测试信息。


# 1. ACM function

该功能由 usb_f_acm.ko 模块提供。


## 1.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“acm”。 ACM 函数在其函数目录中仅提供一个属性：

&emsp;port_num

该属性是只读的。

系统中最多可以有 4 个 ACM/通用串行/OBEX 端口。

## 1.2 Testing the ACM function

在主机上：

```bash
cat > /dev/ttyACM<X>
```

在设备上：

```bash
cat /dev/ttyGS<Y>
```

然后反过来，

在设备上：

```bash
cat > /dev/ttyGS<Y>
```

在主机上：

```bash
cat /dev/ttyACM<X>
```


# 2. ECM function

该功能由 usb_f_ecm.ko 模块提供。


## 2.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“ecm”。 ECM 函数在其函数目录中提供以下属性：

|           |                                       |
| --------- | ------------------------------------- |
| ifname    | 与该函数实例关联的网络设备接口名称       |
| qmult     | 高速和超高速的队列长度乘数               |
| host_addr | 该以太网通过 USB 链路的主机端的 MAC 地址 |
| dev_addr  | 通过 USB 链路的该以太网设备端的 MAC 地址 |

创建functions/ecm.<instance name>后，它们包含默认值：qmult为5，dev_addr和host_addr是随机选择的。 如果函数未绑定，则可以写入 ifname。 写入必须是诸如“usb%d”之类的接口模式，这将导致net core选择下一个空闲的usbX接口。 默认情况下，它设置为“usb%d”。


## 2.2 Testing the ECM function

配置设备和主机的IP地址。 然后：

在设备上：

```bash
ping <host's IP>
```

在主机上：

```bash
ping <device's IP>
```


# 3. ECM subset function

该功能由 usb_f_ecm_subset.ko 模块提供。


## 3.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“geth”。 ECM 子集函数在其函数目录中提供以下属性：

|           |                                       |
| --------- | ------------------------------------- |
| ifname    | 与该函数实例关联的网络设备接口名称       |
| qmult     | 高速和超高速的队列长度乘数               |
| host_addr | 该以太网通过 USB 链路的主机端的 MAC 地址 |
| dev_addr  | 通过 USB 链路的该以太网设备端的 MAC 地址 |

创建functions/ecm.<instance name>后，它们包含默认值：qmult为5，dev_addr和host_addr是随机选择的。 如果函数未绑定，则可以写入 ifname。 写入必须是诸如“usb%d”之类的接口模式，这将导致net core选择下一个空闲的usbX接口。 默认情况下，它设置为“usb%d”。


## 3.2 Testing the ECM subset function

配置设备和主机的IP地址。 然后：

在设备上：

```bash
ping <host's IP>
```

在主机上：

```bash
ping <device's IP>
```


# 4. EEM function

该功能由 usb_f_eem.ko 模块提供。


## 4.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“eem”。 EEM 函数在其函数目录中提供以下属性：

|           |                                       |
| --------- | ------------------------------------- |
| ifname    | 与该函数实例关联的网络设备接口名称       |
| qmult     | 高速和超高速的队列长度乘数               |
| host_addr | 该以太网通过 USB 链路的主机端的 MAC 地址 |
| dev_addr  | 通过 USB 链路的该以太网设备端的 MAC 地址 |

创建函数/eem.<实例名称>后，它们包含默认值：qmult为5，dev_addr和host_addr是随机选择的。 如果函数未绑定，则可以写入 ifname。 写入必须是诸如“usb%d”之类的接口模式，这将导致net core选择下一个空闲的usbX接口。 默认情况下，它设置为“usb%d”。


## 4.2 Testing the EEM function

配置设备和主机的IP地址。 然后：

在设备上：

```bash
ping <host's IP>
```

在主机上：

```bash
ping <device's IP>
```


# 5. FFS function

该功能由usb_f_fs.ko模块提供。


## 5.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“ffs”。 函数目录故意为空且不可修改。

创建目录后，系统中就会有一个新的 FunctionFS 实例（“设备”）可用。 一旦“设备”可用，用户应该遵循使用 FunctionFS 的标准程序（安装它，运行正确实现该功能的用户空间进程）。 应通过将合适的字符串写入 usb_gadget/<gadget>/UDC 来启用小工具。


## 5.2 Testing the FFS function

在设备上： 启动该函数的用户空间守护进程，启用该小工具。

在主机上： 使用设备提供的USB功能。


# 6. HID function

该功能由usb_f_hid.ko模块提供。


## 6.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“hid”。 HID 函数在其函数目录中提供以下属性：

|               |                                                     |
| ------------- | --------------------------------------------------- |
| protocol      | 使用的 HID 协议                                      |
| report_desc   | HID 报告中使用的数据，通过 /dev/hidg<X> 传递的数据除外 |
| report_length | HID 报告长度                                         |
| subclass      | 要使用的 HID 子类                                    |

对于键盘，协议和子类为 1，report_length 为 8，而 report_desc 为：

```bash
$ hd my_report_desc
00000000  05 01 09 06 a1 01 05 07  19 e0 29 e7 15 00 25 01  |..........)...%.|
00000010  75 01 95 08 81 02 95 01  75 08 81 03 95 05 75 01  |u.......u.....u.|
00000020  05 08 19 01 29 05 91 02  95 01 75 03 91 03 95 06  |....).....u.....|
00000030  75 08 15 00 25 65 05 07  19 00 29 65 81 00 c0     |u...%e....)e...|
0000003f
```

这样的字节序列可以使用 echo 存储到属性中：

```bash
$ echo -ne \\x05\\x01\\x09\\x06\\xa1.....
```


## 6.2 Testing the HID function

Device:

- 创建小工具；
- 将小工具连接到主机，最好不是用于控制小工具的主机；
- 运行写入 /dev/hidg<N> 的程序，例如 Linux USB HID 小工具驱动程序中发现的用户空间程序：

```bash
$ ./hid_gadget_test /dev/hidg0 keyboard
```

Host:

- 观察设备上的击键。


# 7. LOOPBACK function

该功能由 usb_f_ss_lb.ko 模块提供。


## 7.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“Loopback”。 LOOPBACK 函数在其函数目录中提供以下属性：

|             |             |
| ----------- | ----------- |
| qlen        | 环回队列深度 |
| bulk_buflen | 缓冲区长度   |


## 7.2 Testing the LOOPBACK function

device: 运行gadget.

host: test-usb (tools/usb/testusb.c)


# 8. MASS STORAGE function

该功能由 usb_f_mass_storage.ko 模块提供。


## 8.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“mass_storage”。 MASS STORAGE 函数在其目录中提供以下属性： 文件：

|             |                                                                                       |
| ----------- | ------------------------------------------------------------------------------------- |
| stall       | 设置为允许函数停止批量端点。 在某些已知无法正常工作的 USB 设备上禁用。 你应该将其设置为 true |
| num_buffers | 管道缓冲区的数量。 有效数字为 2..4。 仅当设置 CONFIG_USB_GADGET_DEBUG_FILES 时可用        |

以及对应于 SCSI LUN #0 的默认 lun.0 目录。

可以使用 mkdir 添加新的 lun：

```bash
$ mkdir functions/mass_storage.0/partition.5
```

除了默认创建的 lun #0 之外，Lun 编号不必是连续的。 最多可以指定 8 个 lun，并且它们都必须按照 <name>.<number> 方案命名。 数字可以是 0..8。 也许一个好的约定是将 lun 命名为“lun.<number>”，尽管这不是强制性的。

每个lun目录中都有以下属性文件：

|              |                                                                                                                        |
| ------------ | ---------------------------------------------------------------------------------------------------------------------- |
| file         | LUN 的备份文件的路径。 如果 LUN 未标记为可移动，则为必需                                                                   |
| ro           | 指定对 LUN 的访问的标志应为只读。 如果启用 CD-ROM 模拟以及无法在 R/W 模式下打开“文件名”，则暗示此情况                         |
| removable    | 指定 LUN 应指示为可移动的标志                                                                                            |
| cdrom        | 指定 LUN 应报告为 CD-ROM 的标志                                                                                          |
| nofua        | 指定 SCSI WRITE(10,12) 中的 FUA 标志的标志                                                                               |
| forced_eject | 该只写文件仅当该功能处于活动状态时才有用。 它会导致备份文件被强制从 LUN 分离，无论主机是否允许。 写入的任何非零字节数都将导致弹出 |


## 8.2 Testing the MASS STORAGE function

设备：连接设备，启用主机：dmesg，看到 USB 驱动器出现（如果系统配置为自动安装）。


# 9. MIDI function

该功能由usb_f_midi.ko模块提供。


## 9.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“midi”。 MIDI 函数在其函数目录中提供了这些属性：

|           |                            |
| --------- | -------------------------- |
| buflen    | MIDI 缓冲区长度             |
| id        | USB MIDI 适配器的 ID 字符串 |
| in_ports  | MIDI 输入端口数量           |
| index     | USB MIDI 适配器的索引值     |
| out_ports | MIDI输出端口数量            |
| qlen      | USB读请求队列长度           |


## 9.2 Testing the MIDI function

有两种情况：从设备播放mid到主机和从主机播放mid到设备。

1. 从设备到主机播放 mid：

host:

```bash
$ arecordmidi -l
 Port    Client name                      Port name
 14:0    Midi Through                     Midi Through Port-0
 24:0    MIDI Gadget                      MIDI Gadget MIDI 1
$ arecordmidi -p 24:0 from_gadget.mid
```

gadget:

```bash
$ aplaymidi -l
 Port    Client name                      Port name
 20:0    f_midi                           f_midi

$ aplaymidi -p 20:0 to_host.mid
```

2. 从主机到设备播放 mid：

gadget:

```bash
$ arecordmidi -l
 Port    Client name                      Port name
 20:0    f_midi                           f_midi

$ arecordmidi -p 20:0 from_host.mid
```

host:

```bash
$ aplaymidi -l
 Port    Client name                      Port name
 14:0    Midi Through                     Midi Through Port-0
 24:0    MIDI Gadget                      MIDI Gadget MIDI 1

$ aplaymidi -p24:0 to_gadget.mid
```

from_gadget.mid 听起来应该与 to_host.mid 相同。

from_host.id 听起来应该与 to_gadget.mid 相同。

MIDI 文件可以通过扬声器/耳机播放。 胆怯装：

```bash
$ aplaymidi -l
 Port    Client name                      Port name
 14:0    Midi Through                     Midi Through Port-0
 24:0    MIDI Gadget                      MIDI Gadget MIDI 1
128:0    TiMidity                         TiMidity port 0
128:1    TiMidity                         TiMidity port 1
128:2    TiMidity                         TiMidity port 2
128:3    TiMidity                         TiMidity port 3

$ aplaymidi -p 128:0 file.mid
```

MIDI 端口可以使用 aconnect 实用程序进行逻辑连接，例如：

```bash
$ aconnect 24:0 128:0 # try it on the host
```

将小工具的 MIDI 端口连接到 timidity 的 MIDI 端口后，使用 aplaymidi -l 在小工具端播放的任何内容都可以在主机的扬声器/耳机中听到。


# 10. NCM function

该功能由usb_f_ncm.ko模块提供。


## 10.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“ncm”。 NCM 函数在其函数目录中提供以下属性：

创建函数/ncm.<实例名称>后，它们包含默认值：qmult为5，dev_addr和host_addr是随机选择的。 如果函数未绑定，则可以写入 ifname。 写入必须是诸如“usb%d”之类的接口模式，这将导致net core选择下一个空闲的usbX接口。 默认情况下，它设置为“usb%d”。


## 10.2 Testing the NCM function

配置设备和主机的IP地址。 然后：

在设备上：

```bash
ping <host's IP>
```

在主机上：

```bash
ping <device's IP>
```


# 11. OBEX function

该功能由 usb_f_obex.ko 模块提供。


## 11.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“obex”。 OBEX 函数在其函数目录中仅提供一个属性：

&emsp;port_num

该属性是只读的。

系统中最多可以有 4 个 ACM/通用串行/OBEX 端口。


## 11.2 Testing the OBEX function

在设备上：

```bash
seriald -f /dev/ttyGS<Y> -s 1024
```

在主机上：

```bash
serialc -v <vendorID> -p <productID> -i<interface#> -a1 -s1024 \
        -t<out endpoint addr> -r<in endpoint addr>
```

其中seriald和serialc是Felipe的实用程序，可以在此处找到：

<https://github.com/felipebalbi/usb-tools.git> master


# 12. PHONET function

该功能由 usb_f_phonet.ko 模块提供。


## 12.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“phonet”。 PHONET 函数在其函数目录中仅提供一个属性：

|        |                                 |
| ------ | ------------------------------- |
| ifname | 与该函数实例关联的网络设备接口名称 |


## 12.2 Testing the PHONET function

如果没有特定的硬件，则无法测试 SOCK_STREAM 协议，因此仅测试了 SOCK_DGRAM。 为了使后者起作用，过去我必须应用这里提到的补丁：

<http://www.spinics.net/lists/linux-usb/msg85689.html>

需要这些工具：

<git://git.gitorious.org/meego-cellular/phonet-utils.git>

在主机上：

```bash
$ ./phonet -a 0x10 -i usbpn0
$ ./pnroute add 0x6c usbpn0
$./pnroute add 0x10 usbpn0
$ ifconfig usbpn0 up
```

在设备上：

```bash
$ ./phonet -a 0x6c -i upnlink0
$ ./pnroute add 0x10 upnlink0
$ ifconfig upnlink0 up
```

然后可以使用测试程序：

```bash
http://www.spinics.net/lists/linux-usb/msg85690.html
```

在设备上：

```bash
$ ./pnxmit -a 0x6c -r
```

在主机上：

```bash
$ ./pnxmit -a 0x10 -s 0x6c
```

因此，一些数据应该从主机发送到设备。然后反过来:

在主机上：

```bash
$ ./pnxmit -a 0x10 -r
```

在设备上：

```bash
$ ./pnxmit -a 0x6c -s 0x10
```


# 13. RNDIS function

该功能由usb_f_rndis.ko模块提供。


## 13.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“rndis”。 RNDIS 函数在其函数目录中提供以下属性：

|           |                                       |
| --------- | ------------------------------------- |
| ifname    | 与该函数实例关联的网络设备接口名称       |
| qmult     | 高速和超高速的队列长度乘数               |
| host_addr | 该以太网通过 USB 链路的主机端的 MAC 地址 |
| dev_addr  | 通过 USB 链路的该以太网设备端的 MAC 地址 |

创建函数/rndis.<实例名称>后，它们包含默认值：qmult为5，dev_addr和host_addr是随机选择的。 如果函数未绑定，则可以写入 ifname。 写入必须是诸如“usb%d”之类的接口模式，这将导致net core选择下一个空闲的usbX接口。 默认情况下，它设置为“usb%d”。


## 13.2 Testing the RNDIS function

配置设备和主机的IP地址。 然后：

在设备上：

```bash
ping <host's IP>
```

在主机上：

```bash
ping <device's IP>
```


# 14. SERIAL function

该功能由 usb_f_gser.ko 模块提供。


## 14.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“gser”。 SERIAL 函数在其函数目录中仅提供一个属性：

&emsp;port_num

该属性是只读的。

系统中最多可以有 4 个 ACM/通用串行/OBEX 端口。


## 14.2 Testing the SERIAL function

在主机上：

```bash
insmod usbserial
echo VID PID >/sys/bus/usb-serial/drivers/generic/new_id
```

在主机上：

```bash
cat > /dev/ttyUSB<X>
```

在 target:

```bash
cat /dev/ttyGS<Y>
```

然后反过来，

在 target:

```bash
cat > /dev/ttyGS<Y>
```

在主机上：

```bash
cat /dev/ttyUSB<X>
```


# 15. SOURCESINK function

该功能由 usb_f_ss_lb.ko 模块提供。


## 15.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“SourceSink”。 SOURCESINK 函数在其函数目录中提供以下属性：

|                |                                    |
| -------------- | ---------------------------------- |
| pattern        | 0 (all zeros), 1 (mod63), 2 (none) |
| isoc_interval  | 1..16                              |
| isoc_maxpacket | 0 - 1023 (fs), 0 - 1024 (hs/ss)    |
| isoc_mult      | 0..2 (hs/ss only)                  |
| isoc_maxburst  | 0..15 (ss only)                    |
| bulk_buflen    | 缓冲区长度                          |
| bulk_qlen      | 批量队列深度                        |
| iso_qlen       | ISO 队列深度                        |


## 15.2 Testing the SOURCESINK function

device: 运行 gadget

host: test-usb (tools/usb/testusb.c)


# 16. UAC1 function (legacy implementation)

该功能由 usb_f_uac1_legacy.ko 模块提供。


## 16.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“uac1_legacy”。 uac1 函数在其函数目录中提供以下属性：

|                |                           |
| -------------- | ------------------------- |
| audio_buf_size | 音频缓冲区大小             |
| fn_cap         | 捕获pcm设备文件名          |
| fn_cntl        | 控制设备文件名             |
| fn_play        | 播放pcm设备文件名          |
| req_buf_size   | ISO OUT 端点请求缓冲区大小  |
| req_count      | ISO OUT 端点请求计数       |

这些属性具有合理的默认值。


## 16.2 Testing the UAC1 function

device: 运行 gadget

host:

```bash
aplay -l # 应该列出我们的 USB 音频 gadget
```


# 17. UAC2 function

该功能由usb_f_uac2.ko模块提供。


## 17.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“uac2”。 uac2 函数在其函数目录中提供以下属性：

|                  |                                             |
| ---------------- | ------------------------------------------- |
| c_chmask         | 捕获通道掩码                                 |
| c_srate          | 捕获采样率列表（逗号分隔）                    |
| c_ssize          | 捕获样本大小（字节）                         |
| c_sync           | 捕获同步类型（异步/自适应）                   |
| c_mute_present   | 捕捉静音控制使能                             |
| c_volume_present | 捕获音量控制启用                             |
| c_volume_min     | 捕获音量控制最小值（以 1/256 dB 为单位）      |
| c_volume_max     | 捕获音量控制最大值（以 1/256 dB 为单位）      |
| c_volume_res     | 捕获音量控制分辨率（以 1/256 dB 为单位）      |
| c_hs_bint        | HS/SS 的捕获 bInterval（1-4：固定，0：自动） |
| fb_max           | 异步模式下的最大额外带宽                     |
| p_chmask         | 播放通道掩码                                |
| p_srate          | 播放采样率列表（以逗号分隔）                  |
| p_ssize          | 播放样本大小（字节）                         |
| p_mute_present   | 播放静音控制启用                             |
| p_volume_present | 播放音量控制启用                             |
| p_volume_min     | 播放音量控制最小值（以 1/256 dB 为单位）      |
| p_volume_max     | 播放音量控制最大值（以1/256 dB为单位）        |
| p_volume_res     | 播放音量控制分辨率（以1/256 dB为单位）        |
| p_hs_bint        | bHS/SS 播放间隔（1-4：固定，0：自动）         |
| req_number       | 为捕获和回放预先分配的请求数量                |
| function_name    | 接口名称                                    |
| c_terminal_type  | 捕获终端类型代码                             |
| p_terminal_type  | 播放终端类型代码                             |

这些属性具有合理的默认值。


## 17.2 Testing the UAC2 function

device: 运行 gadget

host: aplay -l # 应该列出我们的 USB 音频 Gadget

该功能不需要真正的硬件支持，它只是向主机发送音频数据流或从主机发送音频数据流。 为了在设备端实际听到某些内容，必须在设备端使用类似于此的命令：

```bash
$ arecord -f dat -t wav -D hw:2,0 | aplay -D hw:0,0 &
```

例如：

```bash
$ arecord -f dat -t wav -D hw:CARD=UAC2Gadget,DEV=0 | \
  aplay -D default:CARD=OdroidU3
```


# 18. UVC function

该功能由usb_f_uvc.ko模块提供。


## 18.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“uvc”。 uvc 函数在其函数目录中提供了这些属性：

|                     |                                              |
| ------------------- | -------------------------------------------- |
| streaming_interval  | 轮询数据传输端点的时间间隔                     |
| streaming_maxburst  | bMaxBurst 用于超高速伴随描述符                 |
| streaming_maxpacket | 选择此配置时该端点能够发送或接收的最大数据包大小 |
| function_name       | 接口名称                                      |

还有“control”和“streaming”子目录，每个子目录都包含许多子目录。 提供了一些合理的默认值，但用户必须提供以下内容：

|                    |                                                                                                |
| ------------------ | ---------------------------------------------------------------------------------------------- |
| control header     | 在 control/header 中创建，从 control/class/fs 和/或 control/class/ss 链接                        |
| streaming header   | 在streaming/header中创建，从streaming/class/fs和/或streaming/class/hs和/或streaming/class/ss链接 |
| format description | 在流式传输/mjpeg 和/或流式传输/未压缩中创建                                                       |
| frame description  | 在streaming/mjpeg/<format>和/或streaming/uncompressed/<format>中创建                            |

每个帧描述都包含帧间隔规范，并且每个这样的规范由多行组成，每行中都有一个间隔值。 通过一个例子可以很好地说明上述规则：

```bash
# mkdir functions/uvc.usb0/control/header/h
# cd functions/uvc.usb0/control/
# ln -s header/h class/fs
# ln -s header/h class/ss
# mkdir -p functions/uvc.usb0/streaming/uncompressed/u/360p
# cat <<EOF > functions/uvc.usb0/streaming/uncompressed/u/360p/dwFrameInterval
666666
1000000
5000000
EOF
# cd $GADGET_CONFIGFS_ROOT
# mkdir functions/uvc.usb0/streaming/header/h
# cd functions/uvc.usb0/streaming/header/h
# ln -s ../../uncompressed/u
# cd ../../class/fs
# ln -s ../../header/h
# cd ../../class/hs
# ln -s ../../header/h
# cd ../../class/ss
# ln -s ../../header/h
```


## 18.2 Testing the UVC function

device: 运行 gadget, modprobe vivid:

```bash
# uvc-gadget -u /dev/video<uvc video node #> -v /dev/video<vivid video node #>
```

其中 uvc-gadget 是这个程序：

<http://git.ideasonboard.org/uvc-gadget.git>

有了这些补丁：

<http://www.spinics.net/lists/linux-usb/msg99220.html>

host:

```bash
luvcview -f yuv
```


# 19. PRINTER function

该功能由 usb_f_printer.ko 模块提供。


## 19.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“printer”。 打印机函数在其函数目录中提供以下属性：

|            |                                |
| ---------- | ------------------------------ |
| pnp_string | 以 pnp 字符串形式传递给主机的数据 |
| q_len      | 每个端点的请求数                 |


## 19.2 Testing the PRINTER function

最基本的测试：

device: 运行 gadget:

```bash
# ls -l /devices/virtual/usb_printer_gadget/
```

应显示 g_printer<number>。

如果 udev 处于活动状态，则 /dev/g_printer<number> 应自动出现。

主持人：

如果 udev 处于活动状态，则例如 /dev/usb/lp0 应该出现。

主机->设备传输：

device:

```bash
# cat /dev/g_printer<number>
```

host:

```bash
# cat > /dev/usb/lp0
```

device->host transmission:

```bash
# cat > /dev/g_printer<number>
```

host:

```bash
# cat /dev/usb/lp0
```

可以使用 Linux USB 打印机小工具驱动程序中描述的 prn_example 来完成更高级的测试。


# 20. UAC1 function (virtual ALSA card, using u_audio API)

该功能由usb_f_uac1.ko模块提供。 它将创建一个虚拟 ALSA 卡，并且音频流只是从该卡接收和发出。


## 20.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“uac1”。 uac1 函数在其函数目录中提供以下属性：

|                  |                                       |
| ---------------- | ------------------------------------- |
| c_chmask         | 捕获通道掩码                           |
| c_srate          | 捕获采样率列表（逗号分隔）               |
| c_ssize          | 捕获样本大小（字节）                    |
| c_mute_present   | 捕捉静音控制使能                        |
| c_volume_present | 捕获音量控制启用                        |
| c_volume_min     | 捕获音量控制最小值（以 1/256 dB 为单位） |
| c_volume_max     | 捕获音量控制最大值（以 1/256 dB 为单位） |
| c_volume_res     | 捕获音量控制分辨率（以 1/256 dB 为单位） |
| p_chmask         | 播放通道掩码                           |
| p_srate          | 播放采样率列表（以逗号分隔）             |
| p_ssize          | 播放样本大小（字节）                    |
| p_mute_present   | 播放静音控制启用                        |
| p_volume_present | 播放音量控制启用                        |
| p_volume_min     | 播放音量控制最小值（以 1/256 dB 为单位） |
| p_volume_max     | 播放音量控制最大值（以1/256 dB为单位）   |
| p_volume_res     | 播放音量控制分辨率（以1/256 dB为单位）   |
| req_number       | 预先分配的捕获和回放请求数               |
| function_name    | 接口名称                               |

这些属性具有合理的默认值。


## 20.2 Testing the UAC1 function

device: 运行 gadget

host: aplay -l # 应该列出我们的 USB 音频 gadget

该功能不需要真正的硬件支持，它只是向主机发送音频数据流或从主机发送音频数据流。 为了在设备端实际听到某些内容，必须在设备端使用类似于此的命令：

```bash
$ arecord -f dat -t wav -D hw:2,0 | aplay -D hw:0,0 &
```

例如：

```bash
$ arecord -f dat -t wav -D hw:CARD=UAC1Gadget,DEV=0 | \
  aplay -D default:CARD=OdroidU3
```


# 21. MIDI2 function

该功能由usb_f_midi2.ko模块提供。 它将创建一个包含 UMP rawmidi 设备的虚拟 ALSA 卡，UMP 数据包将在其中环回。 此外，还创建了一个旧版 rawmidi 设备。 UMP rawmidi 也与 ALSA 音序器客户端绑定。


## 21.1 Function-specific configfs interface

创建函数目录时使用的函数名称是“midi2”。 midi2 函数在其函数目录中提供这些属性作为卡顶级信息：

|              |                                      |
| ------------ | ------------------------------------ |
| process_ump  | 用于处理 UMP 流消息的布尔标志（0 或 1） |
| static_block | 静态块的布尔标志（0 或 1）             |
| iface_name   | 可选的接口名称字符串                   |

该目录包含一个子目录“ep.0”，它提供 UMP 端点（一对 USB MIDI 端点）的属性：

|               |                                                      |
| ------------- | ---------------------------------------------------- |
| protocol_caps | MIDI协议能力； 1：MIDI 1.0、2：MIDI 2.0 或 3：两种协议 |
| protocol      | 默认 MIDI 协议（1 或 2）                              |
| ep_name       | UMP 端点名称字符串                                    |
| product_id    | 产品 ID 字符串                                        |
| manufacturer  | 制造商 ID 号（24 位）                                 |
| family        | 设备系列 ID 号（16 位）                               |
| model         | 设备型号 ID 号（16 位）                               |
| sw_revision   | 软件版本（32位）                                      |

每个Endpoint子目录都包含一个子目录“block.0”，它代表Block 0信息的Function Block。 它的属性是：

|                   |                                                                                 |
| ----------------- | ------------------------------------------------------------------------------- |
| name              | 功能块名称字符串                                                                  |
| direction         | 该FB的方向1：输入，2：输出，或3：双向                                              |
| first_group       | 第一个UMP组编号（0-15）                                                           |
| num_groups        | 本FB的组数(1-16)                                                                 |
| midi1_first_group | MIDI 1.0 的第一个 UMP 组编号 (0-15)                                               |
| midi1_num_groups  | MIDI 1.0 的组数 (0-16)                                                           |
| ui_hint           | 该FB的UI提示0：未知，1：接收者，2：发送者，3：两者                                  |
| midi_ci_verison   | 支持的 MIDI-CI 版本号（8 位）                                                     |
| is_midi1          | 旧版 MIDI 1.0 设备 (0-2) 0：MIDI 2.0 设备，1：无限制 MIDI 1.0，或 2：低速 MIDI 1.0 |
| sysex8_streams    | SysEx8 流的最大数量（8 位）                                                       |
| active            | FB 活动的布尔标志（0 或 1）                                                       |

如果需要多个功能块，您可以通过创建具有相应功能块编号（1、2、...）的子目录“block.<num>”来添加更多功能块。 FB 子目录也可以动态删除。 请注意，功能块编号必须是连续的。

同样，如果需要多个 UMP 端点，您可以通过创建子目录“ep.<num>”来添加更多端点。 数字必须是连续的。

要模拟不支持 UMP v1.1 的旧 MIDI 2.0 设备，请将 0 传递给 process_ump 标志。 然后整个 UMP v1.1 请求将被忽略。


## 21.2 Testing the MIDI2 function

在设备上： 运行 gadget, and running:

```bash
$ cat /proc/asound/cards
```

将显示包含 MIDI2 设备的新声卡。

OTOH, 在主机上：

```bash
$ cat /proc/asound/cards
```

将显示包含 MIDI1 或 MIDI2 设备的新声卡，具体取决于 USB 音频驱动程序配置。

在两者上，当主机上启用 ALSA 音序器时，您可以找到 UMP MIDI 客户端，例如“MIDI 2.0 Gadget”。

由于驱动程序只是环回数据，因此不需要仅用于测试的真实设备。

要测试从小工具到主机的 MIDI 输入（例如模拟 MIDI 键盘），您可以发送如下所示的 MIDI 流。

在 gadget 上:

```bash
$ aconnect -o
....
client 20: 'MIDI 2.0 Gadget' [type=kernel,card=1]
    0 'MIDI 2.0        '
    1 'Group 1 (MIDI 2.0 Gadget I/O)'
$ aplaymidi -p 20:1 to_host.mid
```

在主机上：

```bash
$ aconnect -i
....
client 24: 'MIDI 2.0 Gadget' [type=kernel,card=2]
    0 'MIDI 2.0        '
    1 'Group 1 (MIDI 2.0 Gadget I/O)'
$ arecordmidi -p 24:1 from_gadget.mid
```

如果您有支持 UMP 的应用程序，则也可以使用 UMP 端口发送/接收原始 UMP 数据包。 例如，支持UMP的aseqdump程序可以从UMP端口接收。在主机上：

```bash
$ aseqdump -u 2 -p 24:1
Waiting for data. Press Ctrl+C to end.
Source  Group    Event                  Ch  Data
 24:1   Group  0, Program change          0, program 0, Bank select 0:0
 24:1   Group  0, Channel pressure        0, value 0x80000000
```

为了测试从设备到主机的 MIDI 输出（例如模拟 MIDI 合成器），情况正好相反。

在 gadget 上:

```bash
$ arecordmidi -p 20:1 from_host.mid
```

在主机上：

```bash
$ aplaymidi -p 24:1 to_gadget.mid
```

支持对主机上 altset 0 上的 MIDI 1.0 的访问，并且它在小工具上从/到 UMP 数据包进行转换。 它仅绑定到功能块 0。

当前的操作模式可以在 SND_CTL_IFACE_RAWMIDI 的 ALSA 控制元素“操作模式”中观察。 例如：

```bash
$ amixer -c1 contents
numid=1,iface=RAWMIDI,name='Operation Mode'
  ; type=INTEGER,access=r--v----,values=1,min=0,max=2,step=0
  : values=2
```

其中 0 = 未使用，1 = MIDI 1.0（替代集 0），2 = MIDI 2.0（替代集 1）。 上面的示例显示它在 2 中运行，即 MIDI 2.0。
