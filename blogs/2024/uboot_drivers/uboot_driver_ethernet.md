
# Ethernet Driver Guide

# 1. Introduction

Das U-Boot 中的网络堆栈专为在运行时轻松添加和控制多个网络设备而设计。 本指南适用于希望查看网络驱动程序堆栈并着眼于实现您自己的以太网设备驱动程序的人员。 在这里我们将描述一个新的伪“APE”驱动程序。

大多数现有驱动程序已经使用 U-Boot 核心驱动程序模型，并且新的网络驱动程序必须使用 U-Boot 核心驱动程序模型。 有关此内容的通用信息可以在 doc/driver-model/design.rst 中找到，因此本文档将重点关注网络特定的代码部分。 有些驱动程序仍然使用旧的以太网接口，两者之间的差异以及有关移植的提示将在最后处理。


# 2. Driver framework

遵循驱动程序模型的网络驱动程序必须使用 U-Boot 驱动程序结构中的 UCLASS_ETH .id 字段声明自身：

```c
U_BOOT_DRIVER(eth_ape) = {
        .name           = "eth_ape",
        .id             = UCLASS_ETH,
        .of_match       = eth_ape_ids,
        .of_to_plat     = eth_ape_of_to_plat,
        .probe          = eth_ape_probe,
        .ops            = &eth_ape_ops,
        .priv_auto      = sizeof(struct eth_ape_priv),
        .plat_auto      = sizeof(struct eth_ape_pdata),
        .flags          = DM_FLAG_ALLOC_PRIV_DMA,
};
```

struct eth_ape_priv 包含运行时每个实例的数据，如缓冲区、指向当前描述符的指针、当前速度设置、指向 PHY 相关数据的指针（如 struct mii_dev）等。 在 .priv_auto 中声明其大小将使驱动程序框架在正确的时间分配它。 可以使用 dev_get_priv(dev) 调用来检索它。

struct eth_ape_pdata 包含静态平台数据，例如 MMIO 基地址、硬件变体、MAC 地址。 struct eth_pdata eth_pdata 作为该结构的第一个成员有助于避免重复的代码。 如果除了标准成员之外不需要更多平台数据，只需对 plat_auto 使用 sizeof(struct eth_pdata) 即可。

PCI 设备添加一行指向支持的供应商/设备 ID 对：

```c
static struct pci_device_id supported[] = {
        { PCI_DEVICE(PCI_VENDOR_ID_APE, 0x4223) },
        {}
};

U_BOOT_PCI_DEVICE(eth_ape, supported);
```

还可以声明对一整类 PCI 设备的支持：

```c
{ PCI_DEVICE_CLASS(PCI_CLASS_SYSTEM_SDHCI << 8, 0xffff00) },
```

设备探测和实例化将由驱动程序模型框架处理，因此请遵循那里的指南。 probe() 函数将初始化硬件的平台特定部分，如时钟、重置、GPIO、MDIO 总线。 它还会处理任何特殊的 PHY 设置（电源轨、内部 PHY 的启用位等）。


# 3. Driver methods

真正的工作将在驱动程序提供的驱动程序方法函数中通过定义 struct eth_ops 的成员来完成：

```c
struct eth_ops {
        int (*start)(struct udevice *dev);
        int (*send)(struct udevice *dev, void *packet, int length);
        int (*recv)(struct udevice *dev, int flags, uchar **packetp);
        int (*free_pkt)(struct udevice *dev, uchar *packet, int length);
        void (*stop)(struct udevice *dev);
        int (*mcast)(struct udevice *dev, const u8 *enetaddr, int join);
        int (*write_hwaddr)(struct udevice *dev);
        int (*read_rom_hwaddr)(struct udevice *dev);
};
```

该结构的最新版本以及更多信息可以在 include/net.h 中找到。

只有 start、stop、send 和 receive 是必需的，其余的都是可选的，由通用代码处理，如果没有提供则被忽略。

启动函数初始化硬件并使其准备好进行发送/接收操作。 您经常在这里执行一些操作，例如重置 MAC 和/或 PHY，以及等待链接自动协商。 您还应该借此机会使用通用结构 eth_pdata 的 enetaddr 成员（这将是您自己的 plat 结构的第一个成员）对设备的 MAC 地址进行编程。 这允许 U-Boot 的其余部分动态更改 MAC 地址并尊重新设置。

发送函数按照您的想法进行操作 - 传输指定的数据包，其大小由长度（以字节为单位）指定。 数据包缓冲区可以（并且将会！）在后续调用 send() 时重用，因此当 send() 函数返回时必须不再使用它。 实现此目的的最简单方法是等待传输完成。 或者，如果硬件支持，则只需等待缓冲区被消耗（由某些 DMA 引擎）也可能是一种选择。 另一种消耗缓冲区的方法是复制要发送的数据，然后将复制的数据包排队（例如将其移交给 DMA 引擎），然后立即返回。 在任何情况下，您都应该保持这样的状态，以便可以连续多次调用发送函数。

recv 函数轮询新数据包的可用性。 如果没有可用的，则必须返回 -EAGAIN。 如果已收到数据包，请确保 CPU 可以访问该数据包（如果需要，则使缓存无效），然后将其地址写入 packetp 指针，并返回长度。 如果出现错误（接收错误、数据包太短或太长），如果需要正常清理数据包，则返回 0，否则返回负错误代码（不需要清理或已完成清理）。 然后，U-Boot 网络堆栈将处理该数据包。

如果定义了 free_pkt，U-Boot 将在处理接收到的数据包后调用它，因此可以释放或回收数据包缓冲区。 通常，您会将其交还给硬件以获取另一个数据包。 对于同一个数据包，free_pkt() 将在 recv() 之后调用，因此您不一定需要推断缓冲区以从数据包指针中释放，但可以依赖于这是 recv() 处理的最后一个数据包。 通用代码已在 .bss (net_rx_packets) 中为您设置数据包缓冲区，因此无需分配您自己的数据包缓冲区。 但这并不意味着您必须使用 net_rx_packets 数组； 您可以随意使用任何您想要的缓冲区。

停止功能应关闭/禁用硬件并将其放回重置状态。 它可以随时调用（在调用相关的 start() 函数之前），因此请确保它可以处理此类事情。

（可选）write_hwaddr 函数应将 pdata->enetaddr 中存储的 MAC 地址编程到以太网控制器中。

所以这个阶段的调用图看起来像这样：

```c
(some net operation (ping / tftp / whatever...))
eth_init()
        ops->start()
eth_send()
        ops->send()
eth_rx()
        ops->recv()
        (process packet)
        if (ops->free_pkt)
                ops->free_pkt()
eth_halt()
        ops->stop()
```


# 4. CONFIG_PHYLIB / CONFIG_CMD_MII

如果您的设备支持在 MII 总线上更改任意值（几乎每个设备都支持），则应该添加对 mii 命令的支持。 这样做相当简单，并且使运行时调试 mii 问题变得更加容易。

在驱动程序的probe()函数中，添加对mdio_alloc()和mdio_register()的调用，如下所示：

```c
bus = mdio_alloc();
if (!bus) {
        ...
        return -ENOMEM;
}

bus->read = ape_mii_read;
bus->write = ape_mii_write;
mdio_register(bus);
```

然后定义 mii_read 和 mii_write 函数（如果尚未定义）。 它们的语法很简单：

```c
int mii_read(struct mii_dev *bus, int addr, int devad, int reg);
int mii_write(struct mii_dev *bus, int addr, int devad, int reg,
              u16 val);
```

读取函数应从地址“addr”处的 phy 读取寄存器“reg”，并将结果返回给其调用者。 写函数的实现应该在逻辑上遵循。


# 5. Legacy network drivers

!!! 警告 !!!

下面的本节描述了旧的做事方式。 不应以这种方式实现新的以太网驱动程序。 所有新驱动程序都应根据 U-Boot 核心驱动程序模型编写，如上所述。

实际的回调函数非常相似，区别在于：

start() 调用 init()

stop() 调用 halt()

recv() 函数必须循环直到接收到所有数据包，对于每个数据包，它必须调用 net_process_received_packet() 函数，并传递指针和长度。 然后它应该在检查新数据之前释放数据包。

要将旧驱动程序移植到新驱动程序模型，请将现有的recv()函数拆分为实际的新recv()函数，仅获取一个数据包，删除对net_process_received_packet()的调用，然后将数据包清理移至free_pkt() 功能。

注册驱动程序和探测设备的处理方式非常不同，请遵循驱动程序模型设计文档中的建议，了解如何移植它的说明。 作为记录，初始化网络驱动程序的旧方法如下：


## 5.1 旧网络驱动程序注册

U-Boot初始化时，会调用常用函数eth_initialize()。 这将依次调用特定于板的 board_eth_init() （或者如果失败，则调用特定于 cpu 的 cpu_eth_init()）。 这些特定于板的函数可以进行随机系统处理，但最终它们将调用特定于驱动程序的寄存器函数，该函数又负责初始化该特定实例。

请记住，您应该对驱动程序进行编码，以避免将状态存储在全局数据中，因为有人可能想要将两个相同的设备连接到一块板上。 特定于接口的任何此类信息都应存储在私有的、驱动程序定义的数据结构中，并由 eth->priv 指向（见下文）。

所以这个阶段的调用图看起来像这样：

```c
board_init()
    eth_initialize()
        board_eth_init() / cpu_eth_init()
            driver_register()
                initialize eth_device
                eth_register()
```

此时，您唯一需要担心的是驱动程序的注册函数。 伪代码看起来像：

```c
int ape_register(struct bd_info *bis, int iobase)
{
        struct ape_priv *priv;
        struct eth_device *dev;
        struct mii_dev *bus;

        priv = malloc(sizeof(*priv));
        if (priv == NULL)
                return -ENOMEM;

        dev = malloc(sizeof(*dev));
        if (dev == NULL) {
                free(priv);
                return -ENOMEM;
        }

        /* setup whatever private state you need */

        memset(dev, 0, sizeof(*dev));
        sprintf(dev->name, "APE");

        /*
         * if your device has dedicated hardware storage for the
         * MAC, read it and initialize dev->enetaddr with it
         */
        ape_mac_read(dev->enetaddr);

        dev->iobase = iobase;
        dev->priv = priv;
        dev->init = ape_init;
        dev->halt = ape_halt;
        dev->send = ape_send;
        dev->recv = ape_recv;
        dev->write_hwaddr = ape_write_hwaddr;

        eth_register(dev);

#ifdef CONFIG_PHYLIB
        bus = mdio_alloc();
        if (!bus) {
                free(priv);
                free(dev);
                return -ENOMEM;
        }

        bus->read = ape_mii_read;
        bus->write = ape_mii_write;
        mdio_register(bus);
#endif

        return 1;
}
```

初始化设备所需的确切参数取决于您。 如果您需要传递更多/更少的参数，那也没关系。 您还应该将新寄存器函数的原型添加到 include/netdev.h。

该函数的返回值应如下所示： < 0 - 失败（硬件故障，而不是探测失败） >=0 - 检测到的接口数量。

您可能会注意到许多驱动程序似乎使用 xxx_initialize() 而不是 xxx_register()。 这是旧的命名约定，应该避免，因为它会导致与特定于驱动程序的 init 函数混淆。

除了在专用硬件存储中查找 MAC 地址之外，您不应该以任何方式接触硬件。 该步骤在特定于驱动程序的 init 函数中处理。 请记住，我们只是在这里注册设备，我们不会检查其状态或进行随机探测。
