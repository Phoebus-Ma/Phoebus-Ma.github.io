
# Linux ACM driver v0.16

Copyright (c) 1999 Vojtech Pavlik <vojtech@suse.cz>

Sponsored by SuSE


# 0. Disclaimer

该程序是免费软件； 您可以根据自由软件基金会发布的 GNU 通用公共许可证的条款重新分发和/或修改它； 许可证的版本 2，或（由您选择）任何更高版本。

分发此程序的目的是希望它有用，但不提供任何保证； 甚至没有适销性或特定用途适用性的默示保证。 有关更多详细信息，请参阅 GNU 通用公共许可证。

您应该随该程序一起收到 GNU 通用公共许可证的副本； 如果没有，请写信给 Free Software Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA 02111-1307 USA。

如果您需要联系我（作者），您可以通过电子邮件 - 将您的信息发送至 <vojtech@suse.cz>，或通过纸质邮件：Vojtech Pavlik, Ucitelska 1576,布拉格 8, 182 00 Czech Republic 。

为了您的方便，软件包中包含 GNU 通用公共许可证版本 2：请参阅文件 COPYING。


# 1. Usage

drivers/usb/class/cdc-acm.c 驱动程序与符合通用串行总线通信设备类抽象控制模型 (USB CDC ACM) 规范的 USB 调制解调器和 USB ISDN 终端适配器配合使用。

许多调制解调器都这样做，以下是我所知道的调制解调器的列表：

- 3Com OfficeConnect 56k
- 3Com Voice FaxModem Pro
- 3Com Sportster
- MultiTech MultiModem 56k
- Zoom 2986L FaxModem
- Compaq 56k FaxModem
- ELSA Microlink 56k

我知道有一个 ISDN TA 可以与 acm 驱动程序配合使用：

- 3Com USR ISDN Pro TA

有些手机还通过 USB 连接。 我知道以下手机可以使用：

- SonyEricsson K800i

不幸的是，许多调制解调器和大多数 ISDN TA 使用专有接口，因此无法与此驱动程序一起使用。 购买前检查是否符合 ACM。

要使用调制解调器，您需要加载这些模块：

```bash
usbcore.ko
uhci-hcd.ko ohci-hcd.ko or ehci-hcd.ko
cdc-acm.ko
```

之后，调制解调器应该可以访问。 您应该能够使用 minicom、ppp 和 mgetty。 


# 2. Verifying that it works

第一步是检查 /sys/kernel/debug/usb/devices，它应该如下所示：

```bash
T:  Bus=01 Lev=00 Prnt=00 Port=00 Cnt=00 Dev#=  1 Spd=12  MxCh= 2
B:  Alloc=  0/900 us ( 0%), #Int=  0, #Iso=  0
D:  Ver= 1.00 Cls=09(hub  ) Sub=00 Prot=00 MxPS= 8 #Cfgs=  1
P:  Vendor=0000 ProdID=0000 Rev= 0.00
S:  Product=USB UHCI Root Hub
S:  SerialNumber=6800
C:* #Ifs= 1 Cfg#= 1 Atr=40 MxPwr=  0mA
I:  If#= 0 Alt= 0 #EPs= 1 Cls=09(hub  ) Sub=00 Prot=00 Driver=hub
E:  Ad=81(I) Atr=03(Int.) MxPS=   8 Ivl=255ms
T:  Bus=01 Lev=01 Prnt=01 Port=01 Cnt=01 Dev#=  2 Spd=12  MxCh= 0
D:  Ver= 1.00 Cls=02(comm.) Sub=00 Prot=00 MxPS= 8 #Cfgs=  2
P:  Vendor=04c1 ProdID=008f Rev= 2.07
S:  Manufacturer=3Com Inc.
S:  Product=3Com U.S. Robotics Pro ISDN TA
S:  SerialNumber=UFT53A49BVT7
C:  #Ifs= 1 Cfg#= 1 Atr=60 MxPwr=  0mA
I:  If#= 0 Alt= 0 #EPs= 3 Cls=ff(vend.) Sub=ff Prot=ff Driver=acm
E:  Ad=85(I) Atr=02(Bulk) MxPS=  64 Ivl=  0ms
E:  Ad=04(O) Atr=02(Bulk) MxPS=  64 Ivl=  0ms
E:  Ad=81(I) Atr=03(Int.) MxPS=  16 Ivl=128ms
C:* #Ifs= 2 Cfg#= 2 Atr=60 MxPwr=  0mA
I:  If#= 0 Alt= 0 #EPs= 1 Cls=02(comm.) Sub=02 Prot=01 Driver=acm
E:  Ad=81(I) Atr=03(Int.) MxPS=  16 Ivl=128ms
I:  If#= 1 Alt= 0 #EPs= 2 Cls=0a(data ) Sub=00 Prot=00 Driver=acm
E:  Ad=85(I) Atr=02(Bulk) MxPS=  64 Ivl=  0ms
E:  Ad=04(O) Atr=02(Bulk) MxPS=  64 Ivl=  0ms
```

这三行（以及 Cls= 'comm' 和 'data' 类）的存在很重要，这意味着它是一个 ACM 设备。 Driver=acm 表示设备使用 acm 驱动程序。 如果您只看到 Cls=ff(vend.) 那么您就不走运了，您拥有一个具有供应商特定接口的设备：

```bash
D:  Ver= 1.00 Cls=02(comm.) Sub=00 Prot=00 MxPS= 8 #Cfgs=  2
I:  If#= 0 Alt= 0 #EPs= 1 Cls=02(comm.) Sub=02 Prot=01 Driver=acm
I:  If#= 1 Alt= 0 #EPs= 2 Cls=0a(data ) Sub=00 Prot=00 Driver=acm
```

在系统日志中您应该看到：

```bash
usb.c: USB new device connect, assigned device number 2
usb.c: kmalloc IF c7691fa0, numif 1
usb.c: kmalloc IF c7b5f3e0, numif 2
usb.c: skipped 4 class/vendor specific interface descriptors
usb.c: new device strings: Mfr=1, Product=2, SerialNumber=3
usb.c: USB device number 2 default language ID 0x409
Manufacturer: 3Com Inc.
Product: 3Com U.S. Robotics Pro ISDN TA
SerialNumber: UFT53A49BVT7
acm.c: probing config 1
acm.c: probing config 2
ttyACM0: USB ACM device
acm.c: acm_control_msg: rq: 0x22 val: 0x0 len: 0x0 result: 0
acm.c: acm_control_msg: rq: 0x20 val: 0x0 len: 0x7 result: 7
usb.c: acm driver claimed interface c7b5f3e0
usb.c: acm driver claimed interface c7b5f3f8
usb.c: acm driver claimed interface c7691fa0
```

如果这一切似乎都正常，请启动 minicom 并将其设置为与 ttyACM 设备通信，然后尝试输入“at”。 如果它响应“OK”，则一切正常。
