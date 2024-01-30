
# USB serial

# 1. Introduction

USB 串行驱动程序当前支持许多不同的 USB 到串行转换器产品，以及一些使用用户空间的串行接口与设备通信的设备。

有关不同设备的具体信息，请参阅下面的各个产品部分。


# 2. Configuration

目前该驱动程序可以同时处理多达 256 个不同的串行接口。

驱动程序使用的主编号是 188，因此要使用该驱动程序，请创建以下节点：

```bash
mknod /dev/ttyUSB0 c 188 0
mknod /dev/ttyUSB1 c 188 1
mknod /dev/ttyUSB2 c 188 2
mknod /dev/ttyUSB3 c 188 3
        .
        .
        .
mknod /dev/ttyUSB254 c 188 254
mknod /dev/ttyUSB255 c 188 255
```

当设备连接并被驱动程序识别时，驱动程序将打印到系统日志中，该设备已绑定到哪个节点。


# Specific Devices Supported

# 3. ConnectTech WhiteHEAT 4 port converter

ConnectTech 非常乐意提供有关其设备的信息，包括提供用于测试的单元。

该驱动程序由 Connect Tech Inc. 官方支持 <http://www.connecttech.com>

如果对此驱动程序有任何疑问或问题，请联系 Connect Tech 的支持部门：support@connecttech.com


# 4. HandSpring Visor, Palm USB, and Clié USB driver

该驱动程序适用于所有 HandSpring USB、Palm USB 和 Sony Clié USB 设备。

只有当设备尝试连接到主机时，设备才会向主机显示为有效的 USB 设备。 发生这种情况时，设备会被正确枚举，分配一个端口，然后通信应该可以进行。 当设备被移除或设备上的连接被取消时，驱动程序会正确清理。

笔记：

这意味着为了与设备通信，必须在尝试让任何程序与设备通信之前按下同步按钮。 这违背了 Pilot-xfer 和其他软件包的当前文档，但由于设备中的硬件，这是其工作的唯一方法。

连接设备后，尝试在第二个端口上与其通信（如果系统中没有任何其他 USB 串行设备，这通常是 /dev/ttyUSB1。）系统日志应该告诉您哪个端口是要连接的端口 用于 HotSync 传输。 “通用”端口可用于其他设备通信，例如 PPP 链路。

对于某些 Sony Clié 设备，必须使用 /dev/ttyUSB0 与设备通信。 对于所有操作系统版本 3.5 的设备以及大多数已闪存升级到较新版本操作系统的设备都是如此。 有关要使用的正确端口的信息，请参阅内核系统日志。

如果按下同步按钮后，系统日志中没有显示任何内容，请尝试重置设备，首先进行热重置，然后根据需要进行冷重置。 有些设备需要这个才能与 USB 端口正常通信。

未编译到内核中的设备可以使用模块参数来指定。 例如 modprobe 遮阳板供应商=0x54c 产品=0x66

驱动程序这部分的网页和邮件列表位于：<http://sourceforge.net/projects/usbvisor/>

如果对此驱动程序有任何疑问或问题，请通过 greg@kroah.com 联系 Greg Kroah-Hartman


# 5. PocketPC PDA Driver

该驱动程序可用于使用 USB 电缆/底座连接 Compaq iPAQ、HP Jornada、Casio EM500 和其他运行 Windows CE 3.0 或 PocketPC 2002 的 PDA。 ActiveSync 支持的大多数设备都是开箱即用的。 对于其他产品，请使用模块参数指定产品和供应商 ID。 例如 modprobe ipaq 供应商=0x3f0 产品=0x1125

该驱动程序提供一个串行接口（通常在 /dev/ttyUSB0 上），可以在该接口上运行 ppp 并建立到 PDA 的 TCP/IP 链接。 完成此操作后，您可以传输文件、备份、下载电子邮件等。使用 USB 最显着的优势是速度 - 我可以达到 73 到 113 kbytes/sec 的下载/上传到我的 iPAQ。

该驱动程序只是使用 USB 连接所需的一组组件之一。 请访问 <http://synce.sourceforge.net>，其中包含必要的软件包和简单的分步操作方法。

连接后，您可以使用 Win CE 程序，例如 PDA 中的 ftpView、Pocket Outlook 以及 Linux 端的 xcerdisp、synce 实用程序。

要使用 Pocket IE，请按照 <http://www.tekguru.co.uk/EM500/usbtonet.htm> 中给出的说明在 Win98 上实现相同的操作。 省略代理服务器部分； Linux 与 Win98 不同，转发数据包的能力很强。 至少 iPAQ 还需要进行另一项修改 - 通过转至开始/设置/连接菜单并取消选中“自动同步...”框来禁用自动同步。 转到“开始”/“程序”/“连接”，连接电缆并选择“usbdial”（或您为新 USB 连接命名的任何名称）。 您最终应该会看到“已连接到 usbdial”窗口，状态显示为已连接。 现在启动 PIE 并浏览。

如果由于某种原因它不起作用，请加载 usbserial 和 ipaq 模块，并将模块参数“debug”设置为 1 并检查系统日志。 您还可以在尝试连接之前尝试软重置 PDA。

根据您的 PDA，可能还有其他功能。 根据 Wes Cilldhaire <billybobjoehenrybob@hotmail.com> 的说法，对于东芝 E570，...如果您启动到引导加载程序（按下重置按钮时按住电源，继续按住电源直到显示引导加载程序屏幕） ，然后将其放入已加载 ipaq 驱动程序的底座中，在 /dev/ttyUSB0 上打开一个终端，它会为您提供一个“USB Reflash”终端，可用于刷新 ROM 以及 microP 代码。 需要东芝 350 美元的串行电缆来刷新！ :D 注意：这尚未经过测试。 使用风险自负。

如果对驱动程序有任何疑问或问题，请联系 Ganesh Varadarajan <ganesh@veritas.com>


# 6. Keyspan PDA Serial Adapter

单端口 DB-9 串行适配器，作为 iMac 的 PDA 适配器推出（主要在 Macintosh 目录中出售，采用半透明白色/绿色适配器）。 相当简单的设备。 固件是自制软件。 该驱动程序也适用于 Xircom/Entrega 单端口串行适配器。

- 当前状态：

    有效的事情：

    - 基本输入/输出（用“cu”测试）；
    - 当串行线跟不上时阻塞写入；
    - 更改波特率（最高 115200）；
    - 获取/设置调制解调器控制引脚（TIOCM{GET,SET,BIS,BIC}）；
    - 发送中断（尽管持续时间看起来很可疑）。

    不存在的事情：

    - 设备字符串（由内核记录）具有尾随二进制垃圾；
    - 设备ID不正确，可能与其他Keyspan产品冲突；
    - 更改波特率应该刷新 tx/rx 以避免损坏半字符。

    待办事项清单上的大事：

    - 奇偶校验，每个字符 7 位与 8 位，1 或 2 个停止位；
    - 硬件流量控制；
    - 并非所有标准 USB 描述符都得到处理：Get_Status、Set_Feature、O_NONBLOCK、select()。

如果对此驱动程序有任何疑问或问题，请通过 warner@lothar.com 联系 Brian Warner


# 7. Keyspan USA-series Serial Adapters

单端口、双端口和四端口适配器 - 驱动程序使用 Keyspan 提供的固件，并在他们的支持下进行开发。

当前状态：

支持 USA-18X、USA-28X、USA-19、USA-19W 和 USA-49W，并已在各种波特率和 8-N-1 字符设置下进行了相当彻底的测试。 其他字符长度和奇偶校验设置目前尚未测试。

尚不支持 USA-28，尽管这样做应该非常简单。 如果您需要此功能，请联系维护者。

更多信息请访问：

<http://www.carnationsoftware.com/carnation/Keyspan.html>

如果对此驱动程序有任何疑问或问题，请联系 Hugh Blemings：hugh@misc.nu


# 8. FTDI Single Port Serial Driver

这是一个单端口 DB-25 串行适配器。

支持的设备包括：

- TripNav TN-200 USB GPS;
- Navis Engineering Bureau CH-4711 USB GPS.

如果对此驱动程序有任何疑问或问题，请联系 Bill Ryder。


# 9. ZyXEL omni.net lcd plus ISDN TA

这是 ISDN TA。 请将成功和问题报告至 azummo@towertech.it


# 10. Cypress M8 CY4601 Family Serial Driver

该驱动程序大部分由 Neil "koyama" Whelchel 开发。 自以前的形式以来，它已得到改进，以支持动态串行线路设置和改进的线路处理。 该驱动程序大部分是稳定的，并且已经在 smp 机器上进行了测试。 （双p2）

CY4601 系列支持的芯片组：

CY7C63723, CY7C63742, CY7C63743, CY7C64013

支持的设备：

- DeLorme's USB Earthmate GPS (SiRF Star II lp arch)
- Cypress HID->COM RS232 adapter

笔记：

Cypress Semiconductor 声称与 hid->com 设备没有任何关系。

大多数使用 CY4601 系列芯片组的设备都应与该驱动程序配合使用。 只要它们符合 CY4601 usbserial 规范。

技术说明：

Earthmate 默认从 4800 8N1 开始...驱动程序将在启动时初始化到此设置。 usbserial 核心提供了 termios 设置的其余部分，以及一些自定义 termios，以便输出具有正确的格式和可解析性。

可以通过发出 NMEA 命令将设备置于 Sirf 模式：

```bash
$PSRF100,<protocol>,<baud>,<databits>,<stopbits>,<parity>*CHECKSUM
$PSRF100,0,9600,8,1,0*0C

It should then be sufficient to change the port termios to match this
to begin communicating.
```

据我所知，它支持固件 2.31 中在线记录的几乎所有 Sirf 命令，但有一些未知的消息 ID。

hid->com 适配器可以以 115200bps 的最大波特率运行。 请注意，设备出现故障或无法正确升高线路电压。 只要您不尝试将两个调制解调器链接在一起而不破解适配器以将线路设置为高电平，那么空调制解调器链接就可以了。

驱动程序是安全的。 使用驱动程序传输文件时，其性能相当低。 这正在处理中，但我愿意接受补丁。 urb 队列或数据包缓冲区可能适合这里的要求。

如果您有任何疑问、问题、补丁、功能请求等，您可以通过电子邮件与我联系：

dignome@gmail.com

（您的问题/补丁也可以提交给 usb-devel）


# 11. Digi AccelePort Driver

该驱动程序支持 Digi AccelePort USB 2 和 4 设备、2 端口（加上并行端口）和 4 端口 USB 串行转换器。 该驱动程序尚不支持 Digi AccelePort USB 8。

该驱动程序在 SMP 下与 usb-uhci 驱动程序一起工作。 它无法在带有 uhci 驱动程序的 SMP 下工作。

驱动程序总体上工作正常，尽管我们还有一些 ioctl 需要实现以及最终的测试和调试要做。 支持 USB 2 上的并行端口作为串行到并行转换器； 换句话说，它在 Linux 上显示为另一个 USB 串行端口，尽管实际上它实际上是一个并行端口。 尚不支持 Digi Acceleport USB 8。

如果对此驱动程序有任何疑问或问题，请联系 Peter Berger (pberger@brimson.com) 或 Al Borchers (alborchers@steinerpoint.com)。


# 12. Belkin USB Serial Adapter F5U103

Belkin 的单端口 DB-9/PS-2 串行适配器，带有 eTEK Labs 的固件。 Peracom 单端口串行适配器以及 GoHubs 适配器也可与此驱动程序配合使用。

当前状态：

以下内容已经过测试并有效：

- Baud rate 300-230400;
- Data bits 5-8;
- Stop bits 1-2;
- Parity N,E,O,M,S;
- Handshake None, Software (XON/XOFF), Hardware (CTSRTS,CTSDTR) [1];
- Break Set and clear;
- Line control Input/Output query and control [2].

[1]
仅当固件级别高于 2.06 时才启用硬件输入流控制。 阅读描述贝尔金固件勘误表的源代码注释。 硬件输出流控制适用于所有固件版本。

[2]
输入查询（CTS、DSR、CD、RI）显示最后报告的状态。 输出查询（DTR、RTS）显示最后请求的状态，并且可能不反映自动硬件流控制设置的当前状态。

待办事项清单：

- 添加真正的调制解调器控制线路查询功能。 当前跟踪中断报告的状态和请求的状态。
- 添加向应用程序返回 UART 错误情况的错误报告。
- 添加对刷新 ioctl 的支持。
- 添加所有其他缺少的内容。

如果对此驱动程序有任何疑问或问题，请通过 wgreathouse@smva.com 联系 William Greathouse


# 13. Empeg empeg-car Mark I/II Driver

这是一个实验性驱动程序，可为 Empeg empeg-car mp3 播放器的客户端同步工具提供连接支持。

提示：

- 不要忘记为 ttyUSB{0,1,2,...} 创建设备节点；
- modprobe empeg（modprobe 是你的朋友）；
- emtool --usb /dev/ttyUSB0 （或任何您命名的设备节点）。

如果对此驱动程序有任何疑问或问题，请通过 xavyer@ix.netcom.com 联系 Gary Brubaker


# 14. MCT USB Single Port Serial Adapter U232

该驱动程序适用于Magic Control Technology Corp. 的MCT USB-RS232 转换器（25 针，型号U232-P25）（还有9 针型号U232-P9）。 有关此设备的更多信息，请访问制造商的网站：<http://www.mct.com.tw>。

驱动程序总体上可以正常工作，但仍需要进行更多测试。 它源自 Belkin USB 串行适配器 F5U103 驱动程序，其 TODO 列表也对该驱动程序有效。

该驱动程序也被发现适用于具有相同供应商 ID 但不同产品 ID 的其他产品。 Sitecom 的 U232-P25 串行转换器使用产品 ID 0x230 和供应商 ID 0x711，并与此驱动程序配合使用。 此外，D-Link 的 DU-H3SP USB BAY 也适用于此驱动程序。

如果对此驱动程序有任何疑问或问题，请联系 Wolfgang Grandegger：wolfgang@ces.ch


# 15. Inside Out Networks Edgeport Driver

该驱动程序支持 Inside Out Networks 制造的所有设备，特别是以下型号：

- Edgeport/4
- Rapidport/4
- Edgeport/4t
- Edgeport/2
- Edgeport/4i
- Edgeport/2i
- Edgeport/421
- Edgeport/21
- Edgeport/8
- Edgeport/8 Dual
- Edgeport/2D8
- Edgeport/4D8
- Edgeport/8i
- Edgeport/2 DIN
- Edgeport/4 DIN
- Edgeport/16 Dual

如果对此驱动程序有任何疑问或问题，请通过 greg@kroah.com 联系 Greg Kroah-Hartman


# 16. REINER SCT cyberJack pinpad/e-com USB chipcard reader

与 ISO 7816 兼容的接触式芯片卡的接口，例如 GSM SIM 卡。

当前状态：

这是该USB读卡器驱动程序的内核部分。 还有一个 CT-API 驱动程序的用户部分可用。 下载站点待定。 目前，您可以向维护者 (linux-usb@sii.li) 请求。

如果对此驱动程序有任何疑问或问题，请联系 linux-usb@sii.li


# 17. Prolific PL2303 Driver

该驱动程序支持任何配备 Prolific PL2303 芯片的设备。 其中包括许多单端口 USB 转串口转换器、超过 70% 的 USB GPS 设备（2010 年）以及一些 USB UPS。 Aten (UC-232) 和 IO-Data 的设备与该驱动程序配合使用，DCU-11 手机电缆也是如此。

如果对此驱动程序有任何疑问或问题，请通过 greg@kroah.com 联系 Greg Kroah-Hartman


# 18. KL5KUSB105 chipset / PalmConnect USB single-port adapter

当前状态：

该驱动程序是通过查看 Windows 下 Palm 驱动程序完成的 USB 总线事务来组合而成的，因此仍然缺少很多功能。 值得注意的是，串行 ioctl 有时是伪造的或尚未实现。 然而，支持了解 DSR 和 CTS 线路状态（尽管不是很好），因此您最喜欢的 autopilot(1) 和 Pilot-manager -daemon 调用将起作用。 支持高达 115200 的波特率，但不支持握手（软件或硬件），这就是为什么在解决此问题之前，明智的做法是降低大型传输所使用的速率。

有关此驱动程序的最新信息，请参阅 <http://www.uuhaus.de/linux/palmconnect.html>。


# 19. Winchiphead CH341 Driver

该驱动程序适用于 Winchiphead CH341 USB-RS232 转换器。 该芯片还实现了 IEEE 1284 并行端口、I2C 和 SPI，但驱动程序不支持。 该协议是从 Windows 驱动程序的行为进行分析的，目前没有可用的数据表。

制造商的网站：<http://www.winchiphead.com/>。

如果对此驱动程序有任何疑问或问题，请联系 Frank@kingswood-consulting.co.uk。


# 20. Moschip MCS7720, MCS7715 driver

这些芯片存在于 Syba 和 Cables Unlimited 等多家制造商销售的设备中。 可能还有其他人。 7720提供2个串口，7715提供1个串口和1个标准PC并口。 对 7715 并行端口的支持通过单独的选项启用，除非首先在设备驱动程序配置菜单的顶层启用并行端口支持，否则该选项不会出现。 目前并行端口仅支持兼容模式（无 ECP/EPP）。

代办：

- 为并行端口实现 ECP/EPP 模式。
- 高于 115200 的波特率目前已被破坏。
- 基于 Moschip MCS7703 的具有单个串行端口的设备可以通过简单添加到 usb_device_id 表来与此驱动程序配合使用。 我没有这些设备之一，所以我不能确定。


# 21. Generic Serial driver

如果您的设备不是上面列出的设备之一，与上述型号兼容，您可以尝试“通用”界面。 该接口不提供发送到设备的任何类型的控制消息，并且不支持任何类型的设备流量控制。 您的设备所需要的只是至少有一个批量输入端点或一个批量输出端点。

要使通用驱动程序能够识别您的设备，请提供：

```bash
echo <vid> <pid> >/sys/bus/usb-serial/drivers/generic/new_id
```

其中 <vid> 和 <pid> 替换为设备供应商 ID 和产品 ID 的十六进制表示形式。 如果驱动程序被编译为模块，您还可以在加载模块时提供一个 id：

```bash
insmod usbserial vendor=0x#### product=0x####
```

该驱动程序已成功用于连接NetChip USB开发板，提供了一种无需编写自定义驱动程序即可开发USB固件的方法。

如果对此驱动程序有任何疑问或问题，请通过 greg@kroah.com 联系 Greg Kroah-Hartman


# 22. Contact

如果任何人在使用这些驱动程序以及上述任何指定产品时遇到任何问题，请联系上面列出的特定驱动程序的作者，或加入 Linux-USB 邮件列表（有关加入邮件列表的信息以及可搜索的链接） 存档位于 <http://www.linux-usb.org/> ）。

Greg Kroah-Hartman greg@kroah.com
