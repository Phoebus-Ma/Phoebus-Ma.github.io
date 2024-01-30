
# Linux Gadget Serial Driver v2.0

11/20/2004

(updated 8-May-2008 for v2.3)


# 1. License and Disclaimer

该程序是免费软件； 您可以根据自由软件基金会发布的 GNU 通用公共许可证的条款重新分发和/或修改它； 许可证的版本 2，或（由您选择）任何更高版本。

分发此程序的目的是希望它有用，但不提供任何保证； 甚至没有适销性或特定用途适用性的默示保证。 有关更多详细信息，请参阅 GNU 通用公共许可证。

您应该随该程序一起收到 GNU 通用公共许可证的副本； 如果没有，请写信给 Free Software Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA 02111-1307 USA。

本文档和小工具串行驱动程序本身版权所有 (C) 2004，作者：Al Borchers (alborchers@steinerpoint.com)。

如果您对此驱动程序有任何疑问、问题或建议，请通过 alborchers@steinerpoint.com 联系 Al Borchers。


# 2. Prerequisites

gadget 串行驱动程序的版本可用于 2.4 Linux 内核，但本文档假设您在 2.6 Linux 内核中使用 2.3 版或更高版本的 gadget 串行驱动程序。

本文档假设您熟悉 Linux 和 Windows，并了解如何配置和构建 Linux 内核、运行标准实用程序、使用 minicom 和超级终端以及使用 USB 和串行设备。 它还假设您将 Linux 小工具和 USB 驱动程序配置为模块。

在驱动程序 2.3 版中，主要和次要设备节点不再是静态定义的。 基于 Linux 的系统应在 /sys 中挂载 sysfs，并使用“mdev”（在 Busybox 中）或“udev”使 /dev 节点与 sysfs /sys/class/tty 文件匹配。


# 3. Overview

gadget串行驱动程序是一个Linux USB gadget驱动程序，一个USB设备端驱动程序。 它运行在具有USB设备端硬件的Linux系统上； 例如，PDA、嵌入式Linux系统或带有USB开发卡的PC。

小工具串行驱动程序通过 USB 与 CDC ACM 驱动程序或主机 PC 上运行的通用 USB 串行驱动程序进行通信：

```bash
 Host
 --------------------------------------
| Host-Side   CDC ACM       USB Host   |
| Operating |   or        | Controller |   USB
| System    | Generic USB | Driver     |--------
| (Linux or | Serial      | and        |        |
| Windows)    Driver        USB Stack  |        |
 --------------------------------------         |
                                                |
                                                |
                                                |
 Gadget                                         |
 --------------------------------------         |
| Gadget                   USB Periph. |        |
| Device-Side |  Gadget  | Controller  |        |
| Linux       |  Serial  | Driver      |--------
| Operating   |  Driver  | and         |
| System                   USB Stack   |
 --------------------------------------
```

在设备端Linux系统上，小工具串行驱动程序看起来就像一个串行设备。

在主机端系统上，小工具串行设备看起来像 CDC ACM 兼容类设备或具有批量输入和批量输出端点的简单供应商特定设备，并且其处理方式与其他串行设备类似。

主机端驱动程序可能是任何符合 ACM 的驱动程序或任何可以通过简单的批量输入/输出接口与设备通信的驱动程序。 Gadget 串行已使用 Linux ACM 驱动程序、Windows usbser.sys ACM 驱动程序和 Linux USB 通用串行驱动程序进行了测试。

当小工具串行驱动程序和主机端 ACM 或通用串行驱动程序运行时，您应该能够在主机和小工具端系统之间进行通信，就像它们通过串行电缆连接一样。

小工具串行驱动程序仅提供简单的不可靠的数据通信。 它尚不处理流量控制或普通串行设备的许多其他功能。


# 4. Installing the Gadget Serial Driver

要使用小工具串行驱动程序，您必须为“USB 小工具支持”、“USB 外围控制器”（例如 net2280）和“串行小工具”驱动程序配置 Linux 小工具端内核。 配置内核时，所有这些都列在“USB Gadget Support”下。 然后重建并安装内核或模块。

然后您必须加载小工具串行驱动程序。 要将其加载为 ACM 设备（建议用于互操作性），请执行以下操作：

```bash
modprobe g_serial
```

要将其加载为供应商特定的批量输入/输出设备，请执行以下操作：

```bash
modprobe g_serial use_acm=0
```

这还将自动加载底层小工具外围控制器驱动程序。 每次重新启动设备端 Linux 系统时都必须执行此操作。 如果需要，您可以将其添加到启动脚本中。

您的系统应该使用 mdev（来自 busybox）或 udev 来创建设备节点。 设置此小工具驱动程序后，您应该会看到 /dev/ttyGS0 节点：

```bash
# ls -l /dev/ttyGS0 | cat
crw-rw----    1 root     root     253,   0 May  8 14:10 /dev/ttyGS0
#
```

请注意，主编号（上面的 253）是特定于系统的。 如果您需要手动创建 /dev 节点，则要使用的正确编号将位于 /sys/class/tty/ttyGS0/dev 文件中。

当您尽早链接此小工具驱动程序时，甚至可能静态链接，您可能需要设置一个 /etc/inittab 条目以在其上运行“getty”。 /dev/ttyGS0 线应该像大多数其他串行端口一样工作。

如果小工具串行作为 ACM 设备加载，您将需要在主机端使用 Windows 或 Linux ACM 驱动程序。 如果小工具串行作为批量输入/输出设备加载，您将需要在主机端使用 Linux 通用串行驱动程序。 请按照以下相应说明安装主机端驱动程序。


# 5. Installing the Windows Host ACM Driver

要使用 Windows ACM 驱动程序，您必须拥有支持所有最新版本的 Windows 的“linux-cdc-acm.inf”文件（随本文档提供）。

当加载小工具串行驱动程序并且使用 USB 电缆将 USB 设备连接到 Windows 主机时，Windows 应识别小工具串行设备并请求驱动程序。 告诉 Windows 在包含“linux-cdc-acm.inf”文件的文件夹中查找驱动程序。

例如，在 Windows XP 上，当首次插入小工具串行设备时，“发现新硬件向导”将启动。 选择“从列表或特定位置安装（高级）”，然后在下一个屏幕上选择“在搜索中包含此位置”并输入路径或浏览到包含“linux-cdc-acm.inf”文件的文件夹。 Windows 会抱怨 Gadget Serial 驱动程序未通过 Windows 徽标测试，但选择“仍然继续”并完成驱动程序安装。

在 Windows XP 上，在“设备管理器”（在“控制面板”、“系统”、“硬件”下）展开“端口（COM 和 LPT）”条目，您应该会看到“小工具串行”列为一个驱动程序 COM 端口的数量。

要卸载“Gadget Serial”的 Windows XP 驱动程序，请右键单击“设备管理器”中的“Gadget Serial”条目，然后选择“卸载”。


# 6. Installing the Linux Host ACM Driver

要使用 Linux ACM 驱动程序，您必须配置 Linux 主机端内核以实现“主机端 USB 支持”和“USB 调制解调器 (CDC ACM) 支持”。

加载小工具串行驱动程序并使用 USB 电缆将 USB 设备连接到 Linux 主机后，主机系统应该识别小工具串行设备。 例如，命令：

```bash
cat /sys/kernel/debug/usb/devices
```

should show something like this::

```bash
T:  Bus=01 Lev=01 Prnt=01 Port=01 Cnt=02 Dev#=  5 Spd=480 MxCh= 0
D:  Ver= 2.00 Cls=02(comm.) Sub=00 Prot=00 MxPS=64 #Cfgs=  1
P:  Vendor=0525 ProdID=a4a7 Rev= 2.01
S:  Manufacturer=Linux 2.6.8.1 with net2280
S:  Product=Gadget Serial
S:  SerialNumber=0
C:* #Ifs= 2 Cfg#= 2 Atr=c0 MxPwr=  2mA
I:  If#= 0 Alt= 0 #EPs= 1 Cls=02(comm.) Sub=02 Prot=01 Driver=acm
E:  Ad=83(I) Atr=03(Int.) MxPS=   8 Ivl=32ms
I:  If#= 1 Alt= 0 #EPs= 2 Cls=0a(data ) Sub=00 Prot=00 Driver=acm
E:  Ad=81(I) Atr=02(Bulk) MxPS= 512 Ivl=0ms
E:  Ad=02(O) Atr=02(Bulk) MxPS= 512 Ivl=0ms
```

如果主机端Linux系统配置正确，ACM驱动程序应该会自动加载。 命令“lsmod”应显示“acm”模块已加载。


# 7. Installing the Linux Host Generic USB Serial Driver

要使用 Linux 通用 USB 串行驱动程序，您必须为“主机端 USB 支持”、“USB 串行转换器支持”和“USB 通用串行驱动程序”配置 Linux 主机端内核。

加载小工具串行驱动程序并使用 USB 电缆将 USB 设备连接到 Linux 主机后，主机系统应该识别小工具串行设备。 例如，命令：

```bash
cat /sys/kernel/debug/usb/devices
```

应该显示这样的内容：

```bash
T:  Bus=01 Lev=01 Prnt=01 Port=01 Cnt=02 Dev#=  6 Spd=480 MxCh= 0
D:  Ver= 2.00 Cls=ff(vend.) Sub=00 Prot=00 MxPS=64 #Cfgs=  1
P:  Vendor=0525 ProdID=a4a6 Rev= 2.01
S:  Manufacturer=Linux 2.6.8.1 with net2280
S:  Product=Gadget Serial
S:  SerialNumber=0
C:* #Ifs= 1 Cfg#= 1 Atr=c0 MxPwr=  2mA
I:  If#= 0 Alt= 0 #EPs= 2 Cls=0a(data ) Sub=00 Prot=00 Driver=serial
E:  Ad=81(I) Atr=02(Bulk) MxPS= 512 Ivl=0ms
E:  Ad=02(O) Atr=02(Bulk) MxPS= 512 Ivl=0ms
```

您必须加载 usbserial 驱动程序并显式设置其参数以将其配置为识别小工具串行设备，如下所示：

```bash
echo 0x0525 0xA4A6 >/sys/bus/usb-serial/drivers/generic/new_id
```

传统方法是使用模块参数：

```bash
modprobe usbserial vendor=0x0525 product=0xA4A6
```

如果一切正常，usbserial 将在系统日志中打印一条消息，内容类似于“小工具串行转换器现在连接到 ttyUSB0”。


# 8. Testing with Minicom or HyperTerminal

一旦安装了小工具串行驱动程序和主机驱动程序，并且使用 USB 电缆将小工具设备连接到主机，您应该能够通过 USB 在小工具和主机系统之间进行通信。 您可以使用 minicom 或超级终端来尝试一下。

在小工具端运行“minicom -s”来配置新的 minicom 会话。 在“串行端口设置”下，将“/dev/ttygserial”设置为“串行设备”。 将波特率、数据位、奇偶校验和停止位设置为 9600、8、无和 1——这些设置大多无关紧要。 在“调制解调器和拨号”下删除所有调制解调器和拨号字符串。

在运行 ACM 驱动程序的 Linux 主机上，类似地配置 minicom，但使用“/dev/ttyACM0”作为“串行设备”。 （如果连接了其他 ACM 设备，请适当更改设备名称。）

在运行 USB 通用串行驱动程序的 Linux 主机上，类似地配置 minicom，但使用“/dev/ttyUSB0”作为“串行设备”。 （如果连接了其他USB串行设备，请适当更改设备名称。）

在 Windows 主机上配置新的超级终端会话以使用分配给 Gadget Serial 的 COM 端口。 当超级终端连接到小工具串行设备时，“端口设置”将自动设置，因此您可以将它们设置为默认值 - 这些设置通常并不重要。

通过在小工具端配置和运行 minicom，以及在主机端配置和运行 minicom 或超级终端，您应该能够在小工具端和主机端系统之间来回发送数据。 您在小工具端的终端窗口中键入的任何内容都应该出现在主机端的终端窗口中，反之亦然。
