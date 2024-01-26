
# SPI userspace API

# 1. Introduction

SPI 设备具有有限的用户空间 API，支持对 SPI 从设备的基本半双工 read() 和 write() 访问。 使用 ioctl() 请求、全双工传输和设备 I/O 配置也是可用的。

```c
#include <fcntl.h>
#include <unistd.h>
#include <sys/ioctl.h>
#include <linux/types.h>
#include <linux/spi/spidev.h>
```

您可能想要使用此编程接口的一些原因包括：

- 在不易发生崩溃的环境中进行原型设计； 用户空间中的杂散指针通常不会导致任何 Linux 系统崩溃。

- 开发简单的协议，用于与充当 SPI 从设备的微控制器进行通信，您可能需要经常更改该协议。

当然，有些驱动程序永远不能在用户空间中编写，因为它们需要访问用户空间无法访问的内核接口（例如 IRQ 处理程序或驱动程序堆栈的其他层）。


# 2. DEVICE CREATION, DRIVER BINDING

spidev 驱动程序包含不同硬件拓扑表示形式支持的 SPI 设备列表。

以下是spidev驱动支持的SPI设备表：

- struct spi_device_id spidev_spi_ids[]: 使用 struct spi_board_info 定义可绑定的设备列表，其 .modalias 字段与表中的条目之一匹配。

- struct of_device_id spidev_dt_ids[]: 当使用具有与表中条目之一匹配的兼容字符串的设备树节点定义这些设备时，可以绑定的设备列表。

- struct acpi_device_id spidev_acpi_ids[]: 当使用具有与表中条目之一匹配的 _HID 的 ACPI 设备对象定义这些设备时，可以绑定的设备列表。

如果相关表中还没有该设备的条目，建议您将 SPI 设备名称的条目添加到相关表中。 为此，请将 spidev 补丁发布到 <linux-spi@vger.kernel.org> 邮件列表。

过去支持使用“spidev”名称定义 SPI 设备。 例如，.modalias =“spidev”或兼容=“spidev”。 但 Linux 内核不再支持此功能，而是必须使用表之一中列出的真实 SPI 设备名称。

没有真实的 SPI 设备名称将导致打印错误并且 spidev 驱动程序无法探测。

Sysfs 还支持用户空间驱动的驱动程序绑定/取消绑定到不使用上表之一自动绑定的设备。 要使 spidev 驱动程序绑定到此类设备，请使用以下命令：

```bash
echo spidev > /sys/bus/spi/devices/spiB.C/driver_override echo spiB.C > /sys/bus/spi/drivers/spidev/bind
```

当 spidev 驱动程序绑定到 SPI 设备时，该设备的 sysfs 节点将包含一个具有“dev”属性的子设备节点，该属性将被 udev 或 mdev 理解（BusyBox 的 udev 替换；它的功能较少，但通常足够 ）。

对于总线 B 上具有片选 C 的 SPI 设备，您应该看到：

```bash
/dev/spidevB.C ...
```

&emsp;字符特殊设备，主设备号 153，带有动态选择的次设备号。 这是用户空间程序将打开的节点，由“udev”或“mdev”创建。

```bash
/sys/devices/.../spiB.C ...
```

&emsp;与往常一样，SPI 设备节点将是其 SPI 主控制器的子节点。

```bash
/sys/class/spidev/spidevB.C ...
```

当“spidev”驱动程序绑定到该设备时创建。 （目录或符号链接，取决于您是否启用了“已弃用的 sysfs 文件”Kconfig 选项。）

不要尝试手动管理 /dev 字符设备特殊文件节点。 这很容易出错，并且您需要特别注意系统安全问题； udev/mdev 应该已经安全配置。

如果您从该设备取消绑定“spidev”驱动程序，则这两个“spidev”节点（在 sysfs 和 /dev 中）应该自动删除（分别由内核和 udev/mdev）。 您可以通过删除“spidev”驱动程序模块来解除绑定，这将影响所有使用该驱动程序的设备。 您还可以通过让内核代码删除 SPI 设备来解除绑定，可能是通过删除其 SPI 控制器的驱动程序（因此其 spi_master 消失）。

由于这是一个标准的 Linux 设备驱动程序——即使它只是碰巧向用户空间公开了一个低级 API——它可以一次与任意数量的设备关联。 只需为每个此类 SPI 设备提供一个 spi_board_info 记录，您就会为每个设备获得一个 /dev 设备节点。


# 3. BASIC CHARACTER DEVICE API

/dev/spidevB.D 文件上的正常 open() 和 close() 操作如您所期望的那样工作。

标准的 read() 和 write() 操作显然只是半双工，并且片选在这些操作之间被停用。 使用 SPI_IOC_MESSAGE(N) 请求即可实现全双工访问和复合操作，无需停用片选。

多个 ioctl() 请求可让您的驱动程序读取或覆盖设备的数据传输参数的当前设置：

**SPI_IOC_RD_MODE, SPI_IOC_WR_MODE ...**

&emsp;传递一个指向字节的指针，该字节将返回 (RD) 或分配 (WR) SPI 传输模式。 使用常量 SPI_MODE_0..SPI_MODE_3； 或者，如果您愿意，可以组合 SPI_CPOL（时钟极性，当设置时空闲高电平）或 SPI_CPHA（时钟相位，当设置时在后沿采样）标志。 请注意，此请求仅限于适合单个字节的 SPI 模式标志。

**SPI_IOC_RD_MODE32, SPI_IOC_WR_MODE32 ...**

&emsp;传递一个指向 uin32_t 的指针，该指针将返回（RD）或分配（WR）完整的 SPI 传输模式，而不限于适合一个字节的位。

**SPI_IOC_RD_LSB_FIRST, SPI_IOC_WR_LSB_FIRST ...**

&emsp;传递一个指向字节的指针，该字节将返回（RD）或分配（WR）用于传输 SPI 字的位调整。 零表示 MSB 优先； 其他值表示不太常见的 LSB 优先编码。 在这两种情况下，每个字中的指定值都是右对齐的，因此未使用 (TX) 或未定义 (RX) 位位于 MSB 中。

**SPI_IOC_RD_BITS_PER_WORD, SPI_IOC_WR_BITS_PER_WORD ...**

&emsp;传递一个指向字节的指针，该字节将返回（RD）或分配（WR）每个SPI传输字中的位数。 值零表示八位。

**SPI_IOC_RD_MAX_SPEED_HZ, SPI_IOC_WR_MAX_SPEED_HZ ...**

&emsp;将指针传递给 u32，它将返回 (RD) 或分配 (WR) 最大 SPI 传输速度（以 Hz 为单位）。 控制器不一定可以分配特定的时钟速度。

NOTES:

- 此时还没有异步 I/O 支持； 一切都是纯粹同步的。
- 目前无法报告用于将数据移入/移出给定设备的实际比特率。
- 目前，您无法从用户空间更改片选极性； 这可能会破坏到共享 SPI 总线的其他设备的传输。 每个 SPI 设备在未使用时都会被取消选择，从而允许其他驱动程序与其他设备通信。
- 每个 I/O 请求可以传输到 SPI 设备的字节数有限制。 它默认为一页，但可以使用模块参数进行更改。
- 由于 SPI 没有低级传输确认，因此在与不存在的设备通信时通常不会看到任何 I/O 错误。


# 4. FULL DUPLEX CHARACTER DEVICE API

请参阅 spidev_fdx.c 示例程序，了解如何使用全双工编程接口的示例。 （尽管它不执行全双工传输。）该模型与内核 spi_sync() 请求中使用的模型相同； 各个传输提供与内核驱动程序可用的相同功能（除了它不是异步的）。

该示例显示了一个半双工 RPC 样式的请求和响应消息。 这些请求通常要求芯片在请求和响应之间不能被取消选择。 多个此类请求可以链接到单个内核请求中，甚至允许在每次响应后取消选择芯片。 （其他协议选项包括更改每个传输段的字大小和比特率。）

要发出全双工请求，请为同一传输提供 rx_buf 和 tx_buf。 如果它们是相同的缓冲区，那就更好了。
