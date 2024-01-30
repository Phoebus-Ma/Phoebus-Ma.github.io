
# Multifunction Composite Gadget

# 1. Overview

多功能复合小工具（或 g_multi）是一个复合小工具，它广泛使用复合框架来提供...多功能小工具。

在其标准配置中，它提供具有 RNDIS[1]（即以太网）、USB CDC[2] ACM（即串行）和 USB 海量存储功能的单个 USB 配置。

CDC ECM（以太网）功能可以通过 Kconfig 选项打开，并且 RNDIS 可以关闭。 如果它们都启用，小工具将有两种配置 - 一种使用 RNDIS，另一种使用 CDC ECM[3]。

请注意，如果您使用非标准配置（即启用 CDC ECM），您可能需要更改供应商和/或产品 ID。


# 2. Host drivers

要使用该小工具，需要使其在主机端工作——否则，就没有希望使用该小工具实现任何目标。 正如人们所预料的那样，不同系统需要做的事情非常不同。


## 2.1 Linux host drivers

由于该小工具使用标准复合框架并且对于 Linux 主机而言也是如此，因此在 Linux 主机端不需要任何额外的驱动程序。 所有功能均由为其开发的相应驱动程序处理。

这对于两个配置设置也是如此，其中 RNDIS 配置是第一个。 Linux 主机将使用带有 CDC ECM 的第二种配置，这应该在 Linux 下工作得更好。


## 2.2 Windows host drivers

要使小工具在 Windows 下工作，必须满足两个条件：


## 2.3 Detecting as composite gadget

首先，Windows 需要将该设备检测为 USB 复合设备，而 USB 复合设备本身具有一些条件[4]。 如果满足，Windows 让 USB 通用父驱动程序 [5] 处理设备，然后尝试为每个单独的接口匹配驱动程序（某种程度上，不要涉及太多细节）。

好消息是：您不必担心大多数情况！

唯一需要担心的是，该小工具必须具有单一配置，因此双 RNDIS 和 CDC ECM 小工具将无法工作，除非您创建正确的 INF - 当然，如果您确实提交了它！


## 2.4 Installing drivers for each function

另一个更棘手的事情是让 Windows 为每个单独的功能安装驱动程序。

对于大容量存储来说，这是微不足道的，因为 Windows 检测到它是实现 USB 大容量存储类的接口并选择适当的驱动程序。

使用 RDNIS 和 CDC ACM 时事情会变得更加困难。


## 2.5 RNDIS

要使 Windows 为小工具中的第一个功能选择 RNDIS 驱动程序，需要使用本文档提供的`file:linux.inf`文件。 它将 Window 的 RNDIS 驱动程序“附加”到小工具的第一个接口。

请注意，在测试时，当 RNDIS 不是第一个接口时，我们遇到了一些问题[6]。 您不必担心它，除非您正在尝试开发自己的小工具，在这种情况下请注意此错误。


## 2.6 CDC ACM

同样，为 CDC ACM 提供了`file:linux-cdc-acm.inf`。


## 2.7 Customising the gadget

如果您打算破解 g_multi 小工具，请注意重新排列功能显然会更改每个功能的接口编号。 作为一个效果，提供的 INF 将不起作用，因为它们有硬编码的接口号（尽管更改这些并不难[7]）。

这也意味着，在尝试 g_multi 并更改提供的功能后，应该更改小工具的供应商和/或产品 ID，这样就不会与其他自定义小工具或原始小工具发生冲突。

在意识到 Windows 缓存了一些驱动程序信息之前，不遵守可能会导致大脑损伤，因为您可能会想了几个小时为什么事情没有按预期工作（更改 USB 端口有时可能会有所帮助，而且您可以尝试使用 USBDeview[8] 来删除幻像设备）。


## 2.8 INF testing

提供的 INF 文件已在 Windows XP SP3、Windows Vista 和 Windows 7（所有 32 位版本）上进行了测试。 它也应该适用于 64 位版本。 它很可能无法在 Windows XP SP2 之前的 Windows 上运行。


## 2.9 Other systems

目前，尚未测试任何其他系统的驱动程序。 了解 MacOS 是如何基于 BSD 的，并且 BSD 是开源的，我们相信它应该（读：“我不知道它是否会”）开箱即用。

对于更奇特的系统我什至没什么可说的......

欢迎任何测试和驱动程序！


# 3. Authors

本文档由 Michal Nazarewicz (<mailto:mina86@mina86.com>) 撰写。 INF 文件已在 Marek Szyprowski (<mailto:m.szyprowski@samsung.com>) 和 Xiaofan Chen (<mailto:xiaofanc@gmail.com>) 的支持下，基于 MS RNDIS 模板 [9]、Microchip 的 CDC ACM 被黑客攻击 INF 文件和 David Brownell (<mailto:dbrownell@users.sourceforge.net>) 的原始 INF 文件。


# 4. Footnotes

[1] 远程网络驱动程序接口规范，<https://msdn.microsoft.com/en-us/library/ee484414.aspx>.

[2] 通信设备类抽象控制模型、该类和其他 USB 类的规范可以在以下位置找到： <http://www.usb.org/developers/devclass_docs/>.

[3] CDC Ethernet Control Model.

[4] <https://msdn.microsoft.com/en-us/library/ff537109(v=VS.85).aspx>

[5] <https://msdn.microsoft.com/en-us/library/ff539234(v=VS.85).aspx>

[6] 换句话说，Windows 无法响应任何用户输入。

[7] <http://www.cygnal.org/ubb/Forum9/HTML/001050.html>

[8] <https://www.nirsoft.net/utils/usb_devices_view.html>

[9] <https://msdn.microsoft.com/en-us/library/ff570620.aspx>
