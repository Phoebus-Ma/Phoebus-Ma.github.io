
# Infinity Usb Unlimited Readme

大家好，

该模块提供串行接口以在 phoenix 模式下使用 IUU 装置。 加载该模块将带来ttyUSB[0-x]接口。 您最喜欢的应用程序必须使用此驱动程序来试点 IUU

该驱动程序仍处于测试阶段，因此可能会出现错误并且您的系统可能会冻结。 到目前为止，我从来没有遇到过任何问题，但我不是真正的大师，所以如果你的系统不稳定，请不要怪我

您可以插入多个 IUU。 每个单元都会有自己的设备文件(/dev/ttyUSB0,/dev/ttyUSB1,...)


# 1. How to tune the reader speed?

加载时可以使用一些参数 要使用参数，只需卸载模块（如果已加载）并使用 modprobe iuu_phoenix param=value。 如果是预构建模块，请使用命令 insmod iuu_phoenix param=value。

示例：

```bash
modprobe iuu_phoenix clockmode=3
```

参数为：

clockmode:

&emsp;1=3Mhz579,2=3Mhz680,3=6Mhz (int)

boost:

&emsp;超频提升百分比 100 至 500 (int)

cdmode:

&emsp;卡检测模式 0=none, 1=CD, 2=!CD, 3=DSR, 4=!DSR, 5=CTS, 6=!CTS, 7=RING, 8=!RING (int)

xmas:

&emsp;是否启用xmas颜色 (bool)

debug:

&emsp;调试是否启用 (bool)

- 时钟模式将提供不同软件常用的 3 种不同的基本设置：

1. 3Mhz579
2. 3Mhz680
3. 6Mhz

- boost 提供了一种超频阅读器的方法（我最喜欢的）例如，要获得比简单的时钟模式=3 更好的性能，请尝试以下操作：

```bash
modprobe boost=195
```

&emsp;这将使读者的基频达到 3Mhz579，但提升了 195%！ 真实时钟现在为：6979050 Hz (6Mhz979)，并将速度提高到比简单时钟模式 = 3 好 10 到 20%！

- cdmode 允许设置用于通知用户区的信号（ioctl 应答）卡是否存在。 可能有八个信号。

- 圣诞节除了你的眼睛之外完全没用。 这是我的一位朋友，他很遗憾拥有像 iuu 这样的好设备却没有看到所有可用的颜色范围。 所以我添加了这个选项，让他看到很多颜色（每个活动随机改变颜色和频率）

- debug 会产生很多调试消息...


# 2. Last notes

不用担心串行设置，串行仿真是一个抽象，因此使用任何速度或奇偶校验设置都可以。 （这不会改变任何东西）。稍后我可能会使用此设置来推导 de boost，但该功能真的有必要吗？ 使用的自动检测功能是串行 CD。 如果这对您的软件不起作用，请禁用其中的检测机制。

玩得开心 ！

Alain Degreffe

eczema(at)ecze.com
