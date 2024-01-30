
# mtouchusb driver

# 1. Changes

0.3 - 基于扫描仪创建并从免费代码上的原始触摸屏驱动程序安装 <http://freecode.com/projects/3mtouchscreendriver>。

Amended for linux-2.4.18, then 2.4.19

0.5 - 在 2.6.3 中使用 Linux 输入完成重写 不幸的是，目前不支持校准。

1.4 - 支持 EXII 5000UC 和内部清理的多项更改 将重置从标准 USB 开发重置更改为供应商重置 将发送到主机的数据从补偿坐标更改为原始坐标 消除了供应商/产品模块参数 使用 EXII-5010UC 执行了多次成功的测试。


# 2. Supported Hardware

```bash
All controllers have the Vendor: 0x0596 & Product: 0x0001


Controller Description          Part Number
------------------------------------------------------

USB Capacitive - Pearl Case     14-205  (Discontinued)
USB Capacitive - Black Case     14-124  (Discontinued)
USB Capacitive - No Case        14-206  (Discontinued)

USB Capacitive - Pearl Case     EXII-5010UC
USB Capacitive - Black Case     EXII-5030UC
USB Capacitive - No Case        EXII-5050UC
```


# 3. Driver Notes

安装很简单，只需要将Linux Input、Linux USB以及驱动程序添加到内核中即可。 该驱动程序还可以选择构建为模块。

该驱动程序似乎是可能的 2 个 Linux USB 输入触摸屏驱动程序之一。 虽然 3M 提供了可供下载的纯二进制驱动程序，但我坚持更新此驱动程序，因为我想使用 QTMedded、DirectFB 等嵌入式应用程序的触摸屏。所以我觉得合理的选择是使用 Linux 输入。

目前无法通过此驱动程序校准设备。 即使设备可以校准，驱动程序也会从控制器获取原始坐标数据。 这意味着校准必须在用户空间内执行。

对于报告原始触摸数据的 X 和 Y，控制器屏幕分辨率现在为 0 到 16384。 这对于新旧电容式 USB 控制器都是一样的。

也许在某个时候，一个抽象函数将被放入 evdev 中，因此可以从用户空间请求诸如校准、重置和供应商信息之类的通用函数（并且驱动程序将处理供应商特定的任务）。


# 4. TODO

再次实现控制 urb 来处理设备发出和发送的请求，例如校准等（一旦/如果它可用）。


# 5. Disclaimer

我现在不是 MicroTouch/3M 员工，以前也不是。 3M不支持该驱动！ 如果您想要仅在 X 内支持触摸驱动程序，请访问：

<http://www.3m.com/3MTouchSystems/>


# 6. Thanks

非常感谢 3M Touch Systems 对 EXII-5010UC 控制器进行测试！
