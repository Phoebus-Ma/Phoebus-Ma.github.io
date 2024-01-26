
# Overview of Linux kernel SPI support

编辑于：2012/2/2。

# 1. What is SPI?

“串行外设接口”(SPI) 是一种同步四线串行链路，用于将微控制器连接到传感器、存储器和外设。 这是一个简单的“事实上的”标准，还没有复杂到需要建立标准化机构。 SPI 使用主/从配置。

三个信号线保存时钟（SCK，通常约为 10 MHz），以及具有“主输出，从输入”（MOSI）或“主输入，从输出”（MISO）信号的并行数据线。 （也使用其他名称。）有四种时钟模式用于交换数据； mode-0 和 mode-3 最常用。 每个时钟周期都会将数据移出和移入； 除非有数据位需要移位，否则时钟不会循环。 但并非所有数据位都被使用； 并非每个协议都使用这些全双工功能。

SPI 主设备使用第四条“芯片选择”线来激活给定的 SPI 从设备，因此这三个信号线可以并行连接到多个芯片。 所有SPI从机均支持片选； 它们通常是低电平有效信号，标记为 nCSx 表示从机“x”（例如 nCS0）。 有些设备有其他信号，通常包括对主设备的中断。

与 USB 或 SMBus 等串行总线不同，即使是 SPI 从机功能的低级协议通常也无法在供应商之间互操作（SPI 存储芯片等商品除外）。

- SPI 可用于请求/响应类型的设备协议，如触摸屏传感器和存储芯片。
- 它还可用于在任一方向（半双工）或同时在两个方向（全双工）传输数据。
- 某些设备可能使用八位字。 其他可能使用不同的字长，例如 12 位或 20 位数字样本流。
- 通常，字的最高有效位 (MSB) 首先发送，但有时最低有效位 (LSB) 首先发送。
- 有时 SPI 用于菊花链设备，例如移位寄存器。

同样，SPI 从机很少支持任何类型的自动发现/枚举协议。 从给定 SPI 主设备可访问的从设备树通常使用配置表手动设置。

SPI 只是此类四线协议使用的名称之一，大多数控制器都可以毫无问题地处理“MicroWire”（将其视为半双工 SPI，用于请求/响应协议）、SSP（“同步串行协议”） 、PSP（“可编程串行协议”）以及其他相关协议。

一些芯片通过结合 MOSI 和 MISO 来消除信号线，并在硬件级别将自身限制为半双工。 事实上，一些 SPI 芯片将此信号模式作为捆绑选项。 可以使用与 SPI 相同的编程接口来访问它们，但它们当然不能处理全双工传输。 您可能会发现此类芯片被描述为使用“三线”信号：SCK、数据、nCSx。 （该数据线有时称为 MOMI 或 SISO。）

微控制器通常支持 SPI 协议的主端和从端。 本文档（和 Linux）支持 SPI 交互的主端和从端。


# 2. Who uses it? On what kinds of systems?

使用 SPI 的 Linux 开发人员可能正在为嵌入式系统板编写设备驱动程序。 SPI用于控制外部芯片，也是每个MMC或SD存储卡都支持的协议。 （较旧的“DataFlash”卡早于 MMC 卡，但使用相同的连接器和卡形状，仅支持 SPI。）某些 PC 硬件使用 SPI 闪存来存储 BIOS 代码。

SPI从属芯片的范围从用于模拟传感器和编解码器的数字/模拟转换器，到存储器，再到USB控制器或以太网适配器等外设； 和更多。

大多数使用 SPI 的系统都会在主板上集成一些设备。 有些在扩展连接器上提供 SPI 链接；有些则在扩展连接器上提供 SPI 链接。 在不存在专用 SPI 控制器的情况下，GPIO 引脚可用于创建低速“bitbanging”适配器。 很少有系统会“热插拔”SPI 控制器； 使用 SPI 的原因集中在低成本和简单操作，如果动态重新配置很重要，那么 USB 通常是更合适的低引脚数外设总线。

许多可以运行 Linux 的微控制器集成了一个或多个具有 SPI 模式的 I/O 接口。 有了 SPI 支持，他们就可以使用 MMC 或 SD 卡，而不需要特殊用途的 MMC/SD/SDIO 控制器。


# 3. I'm confused. What are these four SPI "clock modes"?

这里很容易让人困惑，而且您会发现的供应商文档不一定有帮助。 这四种模式结合了两个模式位：

CPOL表示初始时钟极性。 CPOL=0 表示时钟从低电平开始，因此第一个（前沿）边沿为上升沿，第二个（后沿）边沿为下降沿。 CPOL=1 表示时钟开始为高电平，因此第一个（前沿）边沿为下降沿。

CPHA表示用于采样数据的时钟相位； CPHA=0 表示样本位于前缘，CPHA=1 表示样本位于后缘。

由于信号在采样之前需要稳定，因此 CPHA=0 意味着其数据在第一个时钟边沿之前的半个时钟写入。 片选可能使其变得可用。

芯片规格不会总是用那么多字眼说明“使用 SPI 模式 X”，但它们的时序图将使 CPOL 和 CPHA 模式变得清晰。

在SPI模式编号中，CPOL是高位，CPHA是低位。 因此，当芯片的时序图显示时钟从低电平开始 (CPOL=0) 并且数据在后时钟沿 (CPHA=1) 期间稳定采样时，即为 SPI 模式 1。

请注意，一旦片选变为活动状态，时钟模式就相关。 因此，主设备必须在选择从设备之前将时钟设置为非活动状态，而从设备可以在其选择线变为活动状态时通过采样时钟电平来判断所选择的极性。 这就是为什么许多设备都支持模式 0 和 3：它们不关心极性，并且始终在时钟上升沿输入/输出时钟数据。


# 4. How do these driver programming interfaces work?

<linux/spi/spi.h> 头文件包括 kerneldoc，主要源代码也是如此，您当然应该阅读内核 API 文档的该章。 这只是一个概述，因此您可以先了解大局，然后再了解细节。

SPI 请求始终进入 I/O 队列。 对给定 SPI 设备的请求始终按 FIFO 顺序执行，并通过完成回调异步完成。 这些调用还有一些简单的同步包装器，包括用于常见事务类型的包装器，例如编写命令然后读取其响应。

有两种类型的 SPI 驱动程序，这里称为：

**Controller drivers ...**

&emsp;控制器可以内置到片上系统处理器中，并且通常支持主站和从站角色。 这些驱动程序接触硬件寄存器并可能使用 DMA。 或者它们可以是 PIO bitbangers，只需要 GPIO 引脚。

**Protocol drivers ...**

&emsp;它们通过控制器驱动程序传递消息，以便与 SPI 链路另一端的从设备或主设备进行通信。

例如，一个协议驱动程序可能会与 MTD 层通信，将数据导出到存储在 SPI 闪存（如 DataFlash）上的文件系统； 其他人可能会控制音频接口，将触摸屏传感器作为输入接口，或者在工业处理过程中监控温度和电压水平。 这些可能都共享相同的控制器驱动程序。

“struct spi_device”封装了这两种类型的驱动程序之间的控制器端接口。

SPI 编程接口有一个最小的核心，重点是使用驱动程序模型来使用板特定初始化代码提供的设备表来连接控制器和协议驱动程序。 SPI 显示在 sysfs 中的几个位置：

```bash
/sys/devices/.../CTLR ... physical node for a given SPI controller

/sys/devices/.../CTLR/spiB.C ... spi_device on bus "B",
     chipselect C, accessed through CTLR.

/sys/bus/spi/devices/spiB.C ... symlink to that physical
     .../CTLR/spiB.C device

/sys/devices/.../CTLR/spiB.C/modalias ... identifies the driver
     that should be used with this device (for hotplug/coldplug)

/sys/bus/spi/drivers/D ... driver for one or more spi*.* devices

/sys/class/spi_master/spiB ... symlink to a logical node which could hold
     class related state for the SPI master controller managing bus "B".
     All spiB.* devices share one physical SPI bus segment, with SCLK,
     MOSI, and MISO.

/sys/devices/.../CTLR/slave ... virtual file for (un)registering the
     slave device for an SPI slave controller.
     Writing the driver name of an SPI slave handler to this file
     registers the slave device; writing "(null)" unregisters the slave
     device.
     Reading from this file shows the name of the slave device ("(null)"
     if not registered).

/sys/class/spi_slave/spiB ... symlink to a logical node which could hold
     class related state for the SPI slave controller on bus "B".  When
     registered, a single spiB.* device is present here, possible sharing
     the physical SPI bus segment with other SPI slave devices.
```

此时，唯一的类特定状态是总线号（“spiB”中的“B”），因此这些 /sys/class 条目仅用于快速识别总线。


# 5. How does board-specific init code declare SPI devices?

Linux 需要多种信息来正确配置 SPI 设备。 该信息通常由特定于板的代码提供，即使对于支持某些自动发现/枚举的芯片也是如此。


## 5.1 Declare Controllers

第一种信息是存在的 SPI 控制器的列表。 对于基于片上系统 (SOC) 的板，这些通常是平台设备，并且控制器可能需要一些 platform_data 才能正常运行。 “struct platform_device”将包括控制器第一个寄存器的物理地址及其 IRQ 等资源。

平台通常会抽象“注册 SPI 控制器”操作，可能将其与代码耦合以初始化引脚配置，以便多个板的 arch/.../mach-/board-.c 文件都可以共享相同的基本控制器设置 代码。 这是因为大多数 SOC 都有多个支持 SPI 的控制器，并且通常只应设置和注册给定板上实际可用的控制器。

因此，例如 arch/.../mach-/board-.c 文件可能具有如下代码：

```c
#include <mach/spi.h>   /* for mysoc_spi_data */

/* if your mach-* infrastructure doesn't support kernels that can
 * run on multiple boards, pdata wouldn't benefit from "__init".
 */
static struct mysoc_spi_data pdata __initdata = { ... };

static __init board_init(void)
{
        ...
        /* this board only uses SPI controller #2 */
        mysoc_register_spi(2, &pdata);
        ...
}
```

特定于 SOC 的实用程序代码可能类似于：

```c
#include <mach/spi.h>

static struct platform_device spi2 = { ... };

void mysoc_register_spi(unsigned n, struct mysoc_spi_data *pdata)
{
        struct mysoc_spi_data *pdata2;

        pdata2 = kmalloc(sizeof *pdata2, GFP_KERNEL);
        *pdata2 = pdata;
        ...
        if (n == 2) {
                spi2->dev.platform_data = pdata2;
                register_platform_device(&spi2);

                /* also: set up pin modes so the spi2 signals are
                 * visible on the relevant pins ... bootloaders on
                 * production boards may already have done this, but
                 * developer boards will often need Linux to do it.
                 */
        }
        ...
}
```

请注意，即使使用相同的 SOC 控制器，主板的 platform_data 也可能不同。 例如，在一块板上 SPI 可能使用外部时钟，而另一块板则从某个主时钟的当前设置导出 SPI 时钟。


## 5.2 Declare Slave Devices

第二种信息是目标板上存在的 SPI 从设备的列表，通常包含驱动程序正常工作所需的一些特定于板的数据。

通常，您的 arch/.../mach-/board-.c 文件会提供一个小表，列出每个板上的 SPI 设备。 （这通常只是一小部分。）这可能看起来像：

```c
static struct ads7846_platform_data ads_info = {
        .vref_delay_usecs       = 100,
        .x_plate_ohms           = 580,
        .y_plate_ohms           = 410,
};

static struct spi_board_info spi_board_info[] __initdata = {
{
        .modalias       = "ads7846",
        .platform_data  = &ads_info,
        .mode           = SPI_MODE_0,
        .irq            = GPIO_IRQ(31),
        .max_speed_hz   = 120000 /* max sample rate at 3V */ * 16,
        .bus_num        = 1,
        .chip_select    = 0,
},
};
```

再次注意如何提供特定于董事会的信息； 每个芯片可能需要多种类型。 此示例显示了通用约束，例如允许的最快 SPI 时钟（在本例中是板电压的函数）或 IRQ 引脚的接线方式，以及芯片特定的约束，例如由一个引脚的电容改变的重要延迟。

（还有“controller_data”，这些信息可能对控制器驱动程序有用。一个示例是外设特定的 DMA 调整数据或片选回调。稍后将其存储在 spi_device 中。）

board_info 应提供足够的信息，以便系统在不加载芯片驱动程序的情况下工作。 其中最麻烦的方面可能是 spi_device.mode 字段中的 SPI_CS_HIGH 位，因为在基础设施知道如何取消选择之前，不可能与“向后”解释片选的设备共享总线。

然后，您的板初始化代码将向 SPI 基础设施注册该表，以便稍后在注册 SPI 主控制器驱动程序时可用：

```c
spi_register_board_info(spi_board_info, ARRAY_SIZE(spi_board_info));
```

与其他静态板特定设置一样，您不会取消注册它们。

广泛使用的“卡”式计算机将内存、CPU 和其他很少的东西捆绑到一张可能只有 30 平方厘米的卡上。 在此类系统上，您的 arch/.../mach-.../board-*.c 文件将主要提供有关主板上插入此类卡的设备的信息。 这当然包括通过卡连接器连接的 SPI 设备！


## 5.3 Non-static Configurations

当 Linux 通过 SPI 支持 MMC/SD/SDIO/DataFlash 卡时，这些配置也将是动态的。 幸运的是，此类设备都支持基本的设备识别探针，因此它们应该可以正常热插拔。


# 6. How do I write an "SPI Protocol Driver"?

目前大多数 SPI 驱动程序都是内核驱动程序，但也支持用户空间驱动程序。 这里我们只讨论内核驱动。

SPI 协议驱动程序有点类似于平台设备驱动程序：

```c
static struct spi_driver CHIP_driver = {
        .driver = {
                .name           = "CHIP",
                .owner          = THIS_MODULE,
                .pm             = &CHIP_pm_ops,
        },

        .probe          = CHIP_probe,
        .remove         = CHIP_remove,
};
```

驱动程序核心将自动尝试将此驱动程序绑定到其 board_info 给出模式别名“CHIP”的任何 SPI 设备。 你的probe()代码可能看起来像这样，除非你正在创建一个管理总线的设备（出现在/sys/class/spi_master下）。

```c
static int CHIP_probe(struct spi_device *spi)
{
        struct CHIP                     *chip;
        struct CHIP_platform_data       *pdata;

        /* assuming the driver requires board-specific data: */
        pdata = &spi->dev.platform_data;
        if (!pdata)
                return -ENODEV;

        /* get memory for driver's per-chip state */
        chip = kzalloc(sizeof *chip, GFP_KERNEL);
        if (!chip)
                return -ENOMEM;
        spi_set_drvdata(spi, chip);

        ... etc
        return 0;
}
```

一旦进入probe()，驱动程序就可以使用“struct spi_message”向SPI设备发出I/O请求。 当remove()返回时，或者在probe()失败后，驱动程序保证不再提交任何此类消息。

- spi_message 是一系列协议操作，作为一个原子序列执行。 SPI 驱动器控制包括：

    - 当双向读写开始时...通过其 spi_transfer 请求序列的排列方式；
    - 使用哪些 I/O 缓冲区...每个 spi_transfer 为每个传输方向包装一个缓冲区，支持全双工（两个指针，两种情况下可能相同）和半双工（一个指针为 NULL）传输；
    - 可选择在传输后定义短延迟...使用 spi_transfer.delay.value 设置（如果缓冲区长度为零，则此延迟可能是唯一的协议效果）...指定此延迟时，默认 spi_transfer.delay.unit 为微秒 ，但是如果需要，可以将其调整为时钟周期或纳秒；
    - 通过使用 spi_transfer.cs_change 标志，片选是否在传输和任何延迟后变为非活动状态；
    - 暗示下一条消息是否可能发送到同一设备...在该原子组中的最后一次传输上使用 spi_transfer.cs_change 标志，并可能节省芯片取消选择和选择操作的成本。

- 遵循标准内核规则，并在消息中提供 DMA 安全缓冲区。 这样，使用 DMA 的控制器驱动程序就不会被迫制作额外的副本，除非硬件需要（例如，解决强制使用反弹缓冲的硬件勘误表）。

- 如果这些缓冲区的标准 dma_map_single() 处理不合适，您可以使用 spi_message.is_dma_mapped 告诉控制器驱动程序您已经提供了相关的 DMA 地址。

- 基本的 I/O 原语是 spi_async()。 异步请求可以在任何上下文（irq 处理程序、任务等）中发出，并使用消息提供的回调来报告完成情况。 检测到任何错误后，将取消选择该芯片并中止对该 spi_message 的处理。

- 还有同步包装器，如 spi_sync()，以及包装器，如 spi_read()、spi_write() 和 spi_write_then_read()。 这些可能仅在可能休眠的上下文中发出，并且它们都是 spi_async() 上的干净（且小且“可选”）层。

- spi_write_then_read() 调用及其周围的便利包装器只能用于少量数据，其中额外副本的成本可以忽略不计。 它旨在支持常见的 RPC 式请求，例如编写 8 位命令并读取 16 位响应—— spi_w8r16() 是它的包装器之一，正是这样做的。

某些驱动程序可能需要修改 spi_device 特性，例如传输模式、字大小或时钟速率。 这是通过 spi_setup() 完成的，通常在对设备执行第一个 I/O 之前从probe() 调用它。 但是，也可以在该设备没有待处理消息的任何时间调用。

虽然“spi_device”将是驱动程序的底部边界，但上部边界可能包括 sysfs（尤其是传感器读数）、输入层、ALSA、网络、MTD、字符设备框架或其他 Linux 子系统。

请注意，作为与 SPI 设备交互的一部分，您的驱动程序必须管理两种类型的内存。

- I/O 缓冲区使用通常的 Linux 规则，并且必须是 DMA 安全的。 您通常会从堆或空闲页池中分配它们。 不要使用堆栈或任何声明为“静态”的东西。

- spi_message 和 spi_transfer 元数据用于将这些 I/O 缓冲区粘合到一组协议事务中。 这些可以分配在任何方便的地方，包括作为其他一次性分配驱动程序数据结构的一部分。 对这些进行零初始化。

如果您愿意，可以使用 spi_message_alloc() 和 spi_message_free() 便捷例程通过多次传输来分配和零初始化 spi_message。


# 7. How do I write an "SPI Master Controller Driver"?

SPI 控制器可能会在 platform_bus 上注册； 编写一个驱动程序来绑定到设备，无论涉及哪条总线。

此类驱动程序的主要任务是提供一个“spi_master”。 使用 spi_alloc_master() 分配主设备，并使用 spi_master_get_devdata() 获取为该设备分配的驱动程序私有数据。

```c
struct spi_master       *master;
struct CONTROLLER       *c;

master = spi_alloc_master(dev, sizeof *c);
if (!master)
        return -ENODEV;

c = spi_master_get_devdata(master);
```

驱动程序将初始化该 spi_master 的字段，包括总线号（可能与平台设备 ID 相同）以及用于与 SPI 内核和 SPI 协议驱动程序交互的三种方法。 它还将初始化自己的内部状态。 （有关总线编号和这些方法的信息，请参阅下文。）

初始化 spi_master 后，使用 spi_register_master() 将其发布到系统的其余部分。 那时，控制器的设备节点和任何预先声明的 spi 设备将可用，并且驱动程序模型核心将负责将它们绑定到驱动程序。

如果您需要删除 SPI 控制器驱动程序，spi_unregister_master() 将反转 spi_register_master() 的效果。


## 7.1 Bus Numbering

总线编号很重要，因为这就是 Linux 识别给定 SPI 总线（共享 SCK、MOSI、MISO）的方式。 有效的总线编号从零开始。 在 SOC 系统上，总线编号应与芯片制造商定义的编号相匹配。 例如，硬件控制器 SPI2 的总线编号为 2，连接到它的设备的 spi_board_info 将使用该编号。

如果您没有这样的硬件分配的总线号，并且由于某种原因您不能直接分配它们，那么请提供一个负的总线号。 然后，该号码将被动态分配的号码替换。 然后，您需要将其视为非静态配置（见上文）。


## 7.2 SPI Master Methods

```c
master->setup(struct spi_device *spi)
```

这将设置器件时钟速率、SPI 模式和字大小。 驱动程序可以更改 board_info 提供的默认值，然后调用 spi_setup(spi) 来调用此例程。 它可能会睡觉。

除非每个 SPI 从机都有自己的配置寄存器，否则不要立即更改它们...否则驱动程序可能会损坏其他 SPI 设备正在进行的 I/O。

**Note**

BUG 警告：由于某种原因，许多 spi_master 驱动程序的第一个版本似乎出现了这个错误。 当您编写 setup() 代码时，假设控制器正在主动处理另一个设备的传输。

```c
master->cleanup(struct spi_device *spi)
```

您的控制器驱动程序可以使用 spi_device.controller_state 来保存与该设备动态关联的状态。 如果您这样做，请务必提供 cleanup() 方法来释放该状态。

```c
master->prepare_transfer_hardware(struct spi_master *master)
```

队列机制将调用此函数，向驱动程序发出消息即将到来的信号，因此子系统通过发出此调用来请求驱动程序准备传输硬件。 这可能会睡觉。

```c
master->unprepare_transfer_hardware(struct spi_master *master)
```

这将由队列机制调用，以向驱动程序发出信号，表明队列中不再有待处理的消息，并且它可能会放松硬件（例如通过电源管理调用）。 这可能会睡觉。

```c
master->transfer_one_message(struct spi_master *master, struct spi_message *mesg)
```

子系统调用驱动程序来传输单个消息，同时对同时到达的传输进行排队。 当驱动程序完成此消息时，它必须调用 spi_finalize_current_message()，以便子系统可以发出下一条消息。 这可能会睡觉。

```c
master->transfer_one(struct spi_master *master, struct spi_device *spi, struct spi_transfer *transfer)
```

子系统调用驱动程序来传输单个传输，同时对同时到达的传输进行排队。 当驱动程序完成此传输时，它必须调用 spi_finalize_current_transfer()，以便子系统可以发出下一个传输。 这可能会睡觉。 注意：transfer_one和transfer_one_message是互斥的； 当两者都设置时，通用子系统不会调用您的transfer_one回调。

返回值：

- negative errno: error
- 0: 传输完成
- 1: 传输仍在进行中

```c
master->set_cs_timing(struct spi_device *spi, u8 setup_clk_cycles, u8 hold_clk_cycles, u8 inactive_clk_cycles)
```

此方法允许 SPI 客户端驱动程序请求 SPI 主控制器配置设备特定的 CS 设置、保持和非活动时序要求。


## 7.3 Deprecated Methods

```c
master->transfer(struct spi_device *spi, struct spi_message *message)
```

这个一定不能睡。 它的职责是安排传输发生并发出其complete() 回调。 这两种情况通常会在其他传输完成后发生，并且如果控制器空闲，则需要启动。 此方法不在排队控制器上使用，并且如果实现了transfer_one_message()和(un)prepare_transfer_hardware()，则该方法必须为NULL。


## 7.4 SPI Message Queue

如果您对 SPI 子系统提供的标准排队机制感到满意，只需实现上面指定的排队方法即可。 使用消息队列的优点是可以集中大量代码并提供方法的纯进程上下文执行。 对于高优先级 SPI 流量，消息队列还可以提升到实时优先级。

除非选择 SPI 子系统中的排队机制，否则驱动程序的大部分将管理由现已弃用的函数 Transfer() 提供的 I/O 队列。

该队列可能纯粹是概念性的。 例如，仅用于低频传感器访问的驱动程序可能可以使用同步 PIO。

但队列可能是非常真实的，使用消息->队列、PIO、通常是 DMA（特别是如果根文件系统位于 SPI 闪存中）以及 IRQ 处理程序、tasklet 或工作队列（例如 keventd）等执行上下文。 您的驱动程序可以根据您的需要而精美或简单。 这样的transfer()方法通常只是将消息添加到队列中，然后启动一些异步传输引擎（除非它已经在运行）。


# 8. THANKS TO

Linux-SPI 讨论的贡献者包括（按姓氏字母顺序排列）：

- Mark Brown
- David Brownell
- Russell King
- Grant Likely
- Dmitry Pervushin
- Stephen Street
- Mark Underwood
- Andrew Victor
- Linus Walleij
- Vitaly Wool
