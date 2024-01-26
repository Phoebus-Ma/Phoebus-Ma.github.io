
# 1-wire Master Drivers

# 1. Kernel driver ds2482

支持芯片：

Maxim DS2482-100, Maxim DS2482-800

前缀: 'ds2482'

扫描地址： None

数据表:

<http://datasheets.maxim-ic.com/en/ds/DS2482-100.pdf>

<http://datasheets.maxim-ic.com/en/ds/DS2482-800.pdf>

作者： Ben Gardner <bgardner@wabtec.com>

## 1.1 Description

Maxim/Dallas Semiconductor DS2482 是一款 I2C 器件，提供一条 (DS2482-100) 或八条 (DS2482-800) 1 线总线。


## 1.2 General Remarks

有效地址是 0x18, 0x19, 0x1a, and 0x1b.

但是，如果不写入 i2c 总线，则无法检测到该设备，因此不进行任何检测。 您应该显式实例化该设备。

```bash
$ modprobe ds2482
$ echo ds2482 0x18 > /sys/bus/i2c/devices/i2c-0/new_device
```


# 2. Kernel driver ds2490

支持芯片：

Maxim DS2490 based

作者： Evgeniy Polyakov <johnpol@2ka.mipt.ru>


## 2.1 Description

Maxim/Dallas Semiconductor DS2490 是一款可以构建 USB <-> W1 桥接器的芯片。

DS9490(R)是一款USB<->W1总线主控器件，具有0x81系列ID集成芯片和DS2490低级运算芯片。

注意事项和限制。

- 弱上拉电流最小为 0.9mA，最大为 6.0mA。
- 支持 5V 强上拉，最小电流为 5.9mA，最大电流为 30.4mA。 （来自 DS2490.pdf）
- 硬件将检测设备何时在下一个总线（重置？）操作中连接到总线，但是仅打印一条消息，因为核心 w1 代码不使用该信息。 连接一台设备往往会发出多个新设备通知。
- 如果将 w1_reset_send 添加到 API，则可以减少 USB 总线事务的数量。 该名称只是一个建议。 它将需要一个写入缓冲区和一个读取缓冲区（以及大小）作为参数。 ds2490块I/O命令支持复位、写缓冲、读缓冲和强上拉全部在一个命令中，而不是当前的1复位总线，2写匹配rom命令和从属rom id，3块写和读数据。 写入缓冲区需要将匹配 rom 命令和从机 rom id 前置到请求的写入缓冲区的前面，驱动程序都知道这两者。
- 硬件支持正常、灵活和超速总线通信速度，但仅支持正常。
- 注册的 w1_bus_master 函数不定义错误条件。 如果正在进行总线搜索并且移除了 ds2490，则在总线搜索完成之前可能会产生大量错误输出。
- 硬件支持检测某些错误条件，例如复位时短暂的、令人震惊的存在以及复位时不存在，但驱动程序不会查询这些值。
- ds2490 规范没有详细介绍短批量读取，但我的观察是，如果请求的字节数少于可用字节数，批量读取将返回错误，并且硬件将清除缓冲区中的整个批量。 可以读取最大缓冲区大小以避免遇到此错误情况，只有缓冲区中的额外字节才是驱动程序中的逻辑错误。 代码应与读取和写入以及数据大小相匹配。 读取和写入是串行化的，并且在执行读取之前状态会验证芯片是否空闲（并且数据可用），因此不应发生这种情况。
- 使用 OHCI 控制器在 x86_64 2.6.22-rc6 下的 qemu 0.9.0 下运行 x86_64 2.6.24 UHCI，在 qemu 连接 ds2490 硬件后第一次加载模块时，在来宾中运行的 ds2490 将正常运行，但如果模块是 卸载，然后重新加载，大多数情况下都是批量输出或批量输入之一，并且通常批量输入会失败。 qemu 设置了 50ms 超时，即使状态显示数据可用，批量输入也会超时。 批量输出写入将显示成功完成，但 ds2490 状态寄存器将显示写入 0 字节。 将 qemu 从 ds2490 硬件上分离并重新连接即可解决问题。 来宾和主机中的 usbmon 输出并没有说明问题。 我的猜测是 qemu 或主机操作系统中的错误，更有可能是主机操作系统中的错误。

2008/6/3 David Fries <David@Fries.net>


# 3. Kernel driver mxc_w1

支持芯片：

Freescale MX27, MX31 and probably other i.MX SoCs

Datasheets:

<http://www.freescale.com/files/32bit/doc/data_sheet/MCIMX31.pdf?fpsp=1>

<http://cache.freescale.com/files/dsp/doc/archive/MCIMX27.pdf?fsrch=1&WT_TYPE=Data%20Sheets&WT_VENDOR=FREESCALE&WT_FILE_FORMAT=pdf&WT_ASSET=Documentation>

作者：

最初基于 Freescale 代码，由 Sascha Hauer 为主线准备 <s.hauer@pengutronix.de>


# 4. Kernel driver for omap HDQ/1-wire module

支持芯片：

HDQ/1-wire controller on the TI OMAP 2430/3430 platforms.

有关 HDQ 基础知识的有用链接：

<http://focus.ti.com/lit/an/slua408a/slua408a.pdf>


## 4.1 Description

TI OMAP2430/3430 平台的 HDQ/1-Wire 模块实现 Benchmark HDQ 和 Dallas Semiconductor 1-Wire 协议的主功能的硬件协议。 这些协议使用单线在主设备（HDQ/1-Wire 控制器）和从设备（HDQ/1-Wire 外部兼容设备）之间进行通信。

HDQ/1-Wire 模块的典型应用是与电池监视器（电量计）集成电路的通信。

该控制器支持 HDQ 和 1-wire 模式下的操作。 HDQ 和 1-wire 模式的本质区别在于从设备如何响应初始化脉冲。在 HDQ 模式下，固件不需要主机向从设备创建初始化脉冲。但是，从设备可以通过使用 初始化脉冲（也称为中断脉冲）。从机不会像 1-Wire 协议中那样响应存在脉冲。


## 4.2 Remarks

驱动程序(drivers/w1/masters/omap_hdq.c)支持控制器的HDQ模式。 在这种模式下，由于我们无法读取遵守 W1 规范的 ID（family:id:crc），因此可以将模块参数传递给驱动程序，该驱动程序将用于计算 CRC 并将适当的从站 ID 传回给驱动程序 W1核心。

默认情况下，主驱动程序和 BQ 从机 i/f 驱动程序(drivers/w1/slaves/w1_bq27000.c) 将 ID 设置为 1。请注意，如果需要，请使用不同的 ID 加载两个模块，但请注意所使用的 ID 主驱动程序和从驱动程序加载应该相同。

e.g:

```bash
insmod omap_hdq.ko W1_ID=2
insmod w1_bq27000.ko F_ID=2
```

该驱动程序还支持 1 线模式。 在此模式下，无需传递从站 ID 作为参数。 驱动程序将使用 SEARCH_ROM 过程自动检测连接到总线的从机。 可以通过在 DT 中将“ti,mode”属性设置为“1w”来选择 1 线模式（有关更多详细信息，请参阅 Documentation/devicetree/bindings/w1/omap-hdq.txt）。 默认情况下，驱动程序处于 HDQ 模式。


# 5. Kernel driver w1-gpio

作者： Ville Syrjala <syrjala@sci.fi>


## 5.1 Description

GPIO 1 线总线主驱动器。 驱动程序使用 GPIO API 来控制线路，并且可以使用 GPIO 机器描述符表指定 GPIO 引脚。 也可以使用设备树定义主设备，请参阅 Documentation/devicetree/bindings/w1/w1-gpio.yaml


## 5.2 Example (mach-at91)

```c
#include <linux/gpio/machine.h>
#include <linux/w1-gpio.h>

static struct gpiod_lookup_table foo_w1_gpiod_table = {
      .dev_id = "w1-gpio",
      .table = {
              GPIO_LOOKUP_IDX("at91-gpio", AT91_PIN_PB20, NULL, 0,
                      GPIO_ACTIVE_HIGH|GPIO_OPEN_DRAIN),
      },
};

static struct w1_gpio_platform_data foo_w1_gpio_pdata = {
      .ext_pullup_enable_pin  = -EINVAL,
};

static struct platform_device foo_w1_device = {
      .name                   = "w1-gpio",
      .id                     = -1,
      .dev.platform_data      = &foo_w1_gpio_pdata,
};

...
      at91_set_GPIO_periph(foo_w1_gpio_pdata.pin, 1);
      at91_set_multi_drive(foo_w1_gpio_pdata.pin, 1);
      gpiod_add_lookup_table(&foo_w1_gpiod_table);
      platform_device_register(&foo_w1_device);
```
