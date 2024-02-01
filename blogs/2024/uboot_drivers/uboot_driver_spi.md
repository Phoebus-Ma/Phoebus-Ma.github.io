
# How to port a SPI driver to driver model

# 1. 简介

这是一个粗略的分步指南。 它基于将 exynos SPI 驱动程序转换为驱动程序模型 (DM)，示例代码基于 U-Boot v2014.10-rc2（commit be9f643）。 v2015.04 已更新。

它很长，因为它包含实际的代码示例。

在驱动程序模型之前，SPI驱动程序有自己的私有结构，其中包含“struct spi_slave”。 对于驱动程序模型，“struct spi_slave”仍然存在，但现在它是 SPI 总线的“每个子数据”。 SPI 总线的每个子级都是一个 SPI 从机。 存储在驱动程序特定从属结构中的信息现在可以移植到 SPI 总线的专用数据中。

例如，struct tegra_spi_slave 如下所示：

```c
struct tegra_spi_slave {
        struct spi_slave slave;
        struct tegra_spi_ctrl *ctrl;
};
```

在这种情况下，“slave”将位于每个子数据中，而“ctrl”将位于 SPI 总线的私有数据中。


# 2. 这需要多长时间？

您应该能够在 2 小时内完成此任务，包括测试但不包括准备补丁。 API 与以前基本相同，只有很小的变化：

- 设置速度和模式的方法被分开。
- cs_info 用于获取片选信息。


# 3. 启用 SPI 和 SPI 闪存的驱动程序模式

将这些添加到您的主板配置中：

- CONFIG_DM_SPI
- CONFIG_DM_SPI_FLASH


# 4. Add the skeleton

将此代码放在现有驱动程序文件的底部：

```c
struct spi_slave *spi_setup_slave(unsigned int busnum, unsigned int cs,
                                  unsigned int max_hz, unsigned int mode)
{
        return NULL;
}

struct spi_slave *spi_setup_slave_fdt(const void *blob, int slave_node,
                                      int spi_node)
{
        return NULL;
}

static int exynos_spi_of_to_plat(struct udevice *dev)
{
        return -ENODEV;
}

static int exynos_spi_probe(struct udevice *dev)
{
        return -ENODEV;
}

static int exynos_spi_remove(struct udevice *dev)
{
        return -ENODEV;
}

static int exynos_spi_claim_bus(struct udevice *dev)
{

        return -ENODEV;
}

static int exynos_spi_release_bus(struct udevice *dev)
{

        return -ENODEV;
}

static int exynos_spi_xfer(struct udevice *dev, unsigned int bitlen,
                           const void *dout, void *din, unsigned long flags)
{

        return -ENODEV;
}

static int exynos_spi_set_speed(struct udevice *dev, uint speed)
{
        return -ENODEV;
}

static int exynos_spi_set_mode(struct udevice *dev, uint mode)
{
        return -ENODEV;
}

static int exynos_cs_info(struct udevice *bus, uint cs,
                          struct spi_cs_info *info)
{
        return -EINVAL;
}

static const struct dm_spi_ops exynos_spi_ops = {
        .claim_bus      = exynos_spi_claim_bus,
        .release_bus    = exynos_spi_release_bus,
        .xfer           = exynos_spi_xfer,
        .set_speed      = exynos_spi_set_speed,
        .set_mode       = exynos_spi_set_mode,
        .cs_info        = exynos_cs_info,
};

static const struct udevice_id exynos_spi_ids[] = {
        { .compatible = "samsung,exynos-spi" },
        { }
};

U_BOOT_DRIVER(exynos_spi) = {
        .name   = "exynos_spi",
        .id     = UCLASS_SPI,
        .of_match = exynos_spi_ids,
        .ops    = &exynos_spi_ops,
        .of_to_plat = exynos_spi_of_to_plat,
        .probe  = exynos_spi_probe,
        .remove = exynos_spi_remove,
};
```


# 将上述代码中的“exynos”替换为您的驱动程序名称

# 5. #ifdef 删除驱动程序中除上述代码之外的所有代码

这将使您能够构建它，这意味着您可以增量工作。 由于所有方法最初都会返回错误，因此您意外留下某些内容的可能性较小。

此外，即使您的转换基本上是重写，如果您将函数保留在文件中的同一位置，这可能会对审阅者有所帮助，特别是对于大型驱动程序。


# 6. 增加一些头文件

将这些包含添加到您的驱动程序中：

```c
#include <dm.h>
#include <errno.h>
```


# 7. 构建

此时，您应该能够使用空的 SPI 驱动程序为您的主板构建 U-Boot。 您的驱动程序中仍然有空方法，但我们将一一编写它们。


# 8. 设置您的平台数据结构

这将保存您的驱动程序要操作的信息，例如其硬件地址或最大频率。

您可能已经有了这样的结构，或者您可能需要从驱动程序中的一些#defines 或全局变量创建一个结构。

请注意，此信息不是运行时信息。 它不应该包括变化的状态。 它应该在 U-Boot 的整个生命周期中得到修复。 运行时信息稍后出现。

以下是 exynos spi 驱动程序中的内容：

```c
struct spi_bus {
        enum periph_id periph_id;
        s32 frequency;          /* Default clock frequency, -1 for none */
        struct exynos_spi *regs;
        int inited;             /* 1 if this bus is ready for use */
        int node;
        uint deactivate_delay_us;       /* Delay to wait after deactivate */
};
```

其中，inited 由 DM 处理，node 是 DM 告诉您的设备树节点。 这个名字不太正确。 所以在这种情况下我们会使用：

```c
struct exynos_spi_plat {
        enum periph_id periph_id;
        s32 frequency;          /* Default clock frequency, -1 for none */
        struct exynos_spi *regs;
        uint deactivate_delay_us;       /* Delay to wait after deactivate */
};
```


# 9. 写 of_to_plat() [仅适用于设备树]

此方法会将设备树节点中的信息转换为驱动程序中的 C 结构（称为平台数据）。 如果您不使用设备树，请转至 8b。

当我们使用设备树时，DM会自动为我们分配结构体，但我们需要告诉它大小：

```c
U_BOOT_DRIVER(spi_exynos) = {
...
        .plat_auto = sizeof(struct exynos_spi_plat),
```

这是一个示例函数。 它获取指向平台数据的指针并填充设备树中的字段。

```c
static int exynos_spi_of_to_plat(struct udevice *bus)
{
        struct exynos_spi_plat *plat = bus->plat;
        const void *blob = gd->fdt_blob;
        int node = dev_of_offset(bus);

        plat->regs = (struct exynos_spi *)fdtdec_get_addr(blob, node, "reg");
        plat->periph_id = pinmux_decode_periph_id(blob, node);

        if (plat->periph_id == PERIPH_ID_NONE) {
                debug("%s: Invalid peripheral ID %d\n", __func__,
                        plat->periph_id);
                return -FDT_ERR_NOTFOUND;
        }

        /* Use 500KHz as a suitable default */
        plat->frequency = fdtdec_get_int(blob, node, "spi-max-frequency",
                                        500000);
        plat->deactivate_delay_us = fdtdec_get_int(blob, node,
                                        "spi-deactivate-delay", 0);
        debug("%s: regs=%p, periph_id=%d, max-frequency=%d, deactivate_delay=%d\n",
              __func__, plat->regs, plat->periph_id, plat->frequency,
              plat->deactivate_delay_us);

        return 0;
}
```


# 10. 添加平台数据 [仅限非设备树]

在板文件的 U_BOOT_DRVINFO() 声明中指定此数据：

```c
struct exynos_spi_plat platdata_spi0 = {
        .periph_id = ...
        .frequency = ...
        .regs = ...
        .deactivate_delay_us = ...
};

U_BOOT_DRVINFO(board_spi0) = {
        .name = "exynos_spi",
        .plat = &platdata_spi0,
};
```

不幸的是，在这种情况下，您需要将结构定义放入头文件中，以便您的板文件可以使用它。


# 11. 添加设备私有数据

大多数设备都有一些私人数据，用于在活动时跟踪事物。 这是运行时信息，需要存储在结构中。 驱动程序中可能有一个包含“struct spi_slave”的结构，因此您可以使用它。

```c
struct exynos_spi_slave {
        struct spi_slave slave;
        struct exynos_spi *regs;
        unsigned int freq;              /* Default frequency */
        unsigned int mode;
        enum periph_id periph_id;       /* Peripheral ID for this device */
        unsigned int fifo_size;
        int skip_preamble;
        struct spi_bus *bus;            /* Pointer to our SPI bus info */
        ulong last_transaction_us;      /* Time of last transaction end */
};
```

我们应该重命名它以使其目的更加明显，并摆脱从属结构，因此我们有：

```c
struct exynos_spi_priv {
        struct exynos_spi *regs;
        unsigned int freq;              /* Default frequency */
        unsigned int mode;
        enum periph_id periph_id;       /* Peripheral ID for this device */
        unsigned int fifo_size;
        int skip_preamble;
        ulong last_transaction_us;      /* Time of last transaction end */
};
```

DM 也可以自动分配：

```c
U_BOOT_DRIVER(spi_exynos) = {
...
        .priv_auto = sizeof(struct exynos_spi_priv),
```

请注意，这是在调用probe方法之前创建的，并在调用remove方法之后销毁的。 当调用probe方法时它将被清零。


# 12. 添加probe()和remove()方法

注意：最好在工作时重复构建，以避免最后进行大量工作编译。

探测方法应该设置硬件。 U-Boot 过去使用 spi_setup_slave() 来执行此操作。 因此，请查看此函数，看看您可以复制哪些内容来进行设置。

```c
static int exynos_spi_probe(struct udevice *bus)
{
        struct exynos_spi_plat *plat = dev_get_plat(bus);
        struct exynos_spi_priv *priv = dev_get_priv(bus);

        priv->regs = plat->regs;
        if (plat->periph_id == PERIPH_ID_SPI1 ||
            plat->periph_id == PERIPH_ID_SPI2)
                priv->fifo_size = 64;
        else
                priv->fifo_size = 256;

        priv->skip_preamble = 0;
        priv->last_transaction_us = timer_get_us();
        priv->freq = plat->frequency;
        priv->periph_id = plat->periph_id;

        return 0;
}
```

这种实现实际上并不涉及硬件，这对于驱动程序来说有点不寻常。 在这种情况下，当想要使用 SPI 总线的设备声明该设备时，我们将执行此操作。

为了删除，我们可以关闭时钟，但在这种情况下没有什么可做的。 DM 释放它分配的所有内存，因此我们可以删除 exynos_spi_remove() 及其在 U_BOOT_DRIVER 中的引用。


# 13. 实现 set_speed()

这应该设置时钟，以便 SPI 总线以正确的速度运行。 使用旧的 API spi_claim_bus() 通常会执行此操作以及以下几个函数，所以让我们看看该函数：

```c
int spi_claim_bus(struct spi_slave *slave)
{
        struct exynos_spi_slave *spi_slave = to_exynos_spi(slave);
        struct exynos_spi *regs = spi_slave->regs;
        u32 reg = 0;
        int ret;

        ret = set_spi_clk(spi_slave->periph_id,
                                        spi_slave->freq);
        if (ret < 0) {
                debug("%s: Failed to setup spi clock\n", __func__);
                return ret;
        }

        exynos_pinmux_config(spi_slave->periph_id, PINMUX_FLAG_NONE);

        spi_flush_fifo(slave);

        reg = readl(&regs->ch_cfg);
        reg &= ~(SPI_CH_CPHA_B | SPI_CH_CPOL_L);

        if (spi_slave->mode & SPI_CPHA)
                reg |= SPI_CH_CPHA_B;

        if (spi_slave->mode & SPI_CPOL)
                reg |= SPI_CH_CPOL_L;

        writel(reg, &regs->ch_cfg);
        writel(SPI_FB_DELAY_180, &regs->fb_clk);

        return 0;
}
```

它设置速度、模式、pinmux、反馈延迟并清除 FIFO。 对于 DM，这些将通过不同的方法进行。

这是速度部分的示例：

```c
static int exynos_spi_set_speed(struct udevice *bus, uint speed)
{
        struct exynos_spi_plat *plat = bus->plat;
        struct exynos_spi_priv *priv = dev_get_priv(bus);
        int ret;

        if (speed > plat->frequency)
                speed = plat->frequency;
        ret = set_spi_clk(priv->periph_id, speed);
        if (ret)
                return ret;
        priv->freq = speed;
        debug("%s: regs=%p, speed=%d\n", __func__, priv->regs, priv->freq);

        return 0;
}
```


# 14. 实现 set_mode()

这应该调整 SPI 模式（极性等）。 同样，这段代码可能来自旧的 spi_claim_bus()。 这是一个例子：

```c
static int exynos_spi_set_mode(struct udevice *bus, uint mode)
{
        struct exynos_spi_priv *priv = dev_get_priv(bus);
        uint32_t reg;

        reg = readl(&priv->regs->ch_cfg);
        reg &= ~(SPI_CH_CPHA_B | SPI_CH_CPOL_L);

        if (mode & SPI_CPHA)
                reg |= SPI_CH_CPHA_B;

        if (mode & SPI_CPOL)
                reg |= SPI_CH_CPOL_L;

        writel(reg, &priv->regs->ch_cfg);
        priv->mode = mode;
        debug("%s: regs=%p, mode=%d\n", __func__, priv->regs, priv->mode);

        return 0;
}
```


# 15. 实现 claim_bus()

这是客户想要使用巴士的地方，因此首先声明它。 此时，我们需要确保一切都已准备好进行数据传输。 请注意，此函数完全是驱动程序内部的 - 目前 SPI uclass 从未调用它。

在这里，我们再次查看旧的声明函数并查看所需的一些代码。 它与速度和模式无关：

```c
static int exynos_spi_claim_bus(struct udevice *bus)
{
        struct exynos_spi_priv *priv = dev_get_priv(bus);

        exynos_pinmux_config(priv->periph_id, PINMUX_FLAG_NONE);
        spi_flush_fifo(priv->regs);

        writel(SPI_FB_DELAY_180, &priv->regs->fb_clk);

        return 0;
}
```

spi_flush_fifo() 函数位于代码的删除部分，因此我们需要再次公开它（可能在其前面加上 #endif，在其后面加上“#if 0”）。 它只需要访问 priv->regs，这就是我们将其传入的原因：

```c
/**
 * Flush spi tx, rx fifos and reset the SPI controller
 *
 * @param regs  Pointer to SPI registers
 */
static void spi_flush_fifo(struct exynos_spi *regs)
{
        clrsetbits_le32(&regs->ch_cfg, SPI_CH_HS_EN, SPI_CH_RST);
        clrbits_le32(&regs->ch_cfg, SPI_CH_RST);
        setbits_le32(&regs->ch_cfg, SPI_TX_CH_ON | SPI_RX_CH_ON);
}
```


# 16. 实现 release_bus()

这会释放总线 - 在我们的示例中，spi_release_bus() 中的旧代码是对 spi_flush_fifo 的调用，因此我们添加：

```c
static int exynos_spi_release_bus(struct udevice *bus)
{
        struct exynos_spi_priv *priv = dev_get_priv(bus);

        spi_flush_fifo(priv->regs);

        return 0;
}
```


# 17. 实现 xfer()

这是我们需要创建的最终方法，也是所有工作发生的地方。 该方法参数与旧的 spi_xfer() 相同，但添加了“struct udevice”，因此转换非常容易。 首先将 spi_xfer() 的内容复制到新的 xfer() 方法，然后从那里继续。

如果 (flags & SPI_XFER_BEGIN) 非零，则 xfer() 通常调用激活函数，如下所示：

```c
void spi_cs_activate(struct spi_slave *slave)
{
        struct exynos_spi_slave *spi_slave = to_exynos_spi(slave);

        /* If it's too soon to do another transaction, wait */
        if (spi_slave->bus->deactivate_delay_us &&
            spi_slave->last_transaction_us) {
                ulong delay_us;         /* The delay completed so far */
                delay_us = timer_get_us() - spi_slave->last_transaction_us;
                if (delay_us < spi_slave->bus->deactivate_delay_us)
                        udelay(spi_slave->bus->deactivate_delay_us - delay_us);
        }

        clrbits_le32(&spi_slave->regs->cs_reg, SPI_SLAVE_SIG_INACT);
        debug("Activate CS, bus %d\n", spi_slave->slave.bus);
        spi_slave->skip_preamble = spi_slave->mode & SPI_PREAMBLE;
}
```

新版本如下所示：

```c
static void spi_cs_activate(struct udevice *dev)
{
        struct udevice *bus = dev->parent;
        struct exynos_spi_plat *pdata = dev_get_plat(bus);
        struct exynos_spi_priv *priv = dev_get_priv(bus);

        /* If it's too soon to do another transaction, wait */
        if (pdata->deactivate_delay_us &&
            priv->last_transaction_us) {
                ulong delay_us;         /* The delay completed so far */
                delay_us = timer_get_us() - priv->last_transaction_us;
                if (delay_us < pdata->deactivate_delay_us)
                        udelay(pdata->deactivate_delay_us - delay_us);
        }

        clrbits_le32(&priv->regs->cs_reg, SPI_SLAVE_SIG_INACT);
        debug("Activate CS, bus '%s'\n", bus->name);
        priv->skip_preamble = priv->mode & SPI_PREAMBLE;
}
```

我们在这里真正做的就是更改指针并打印设备名称而不是总线号。 其他局部静态函数可以同样的方式处理。


# 18. 设置每个子数据和子预探测功能

为了在驱动程序模型转换到位时最大限度地减少 SPI 子系统的痛苦和复杂性，使用 struct spi_slave 来引用 SPI 总线从设备，即使该从设备实际上是 struct udevice。 事实上 struct spi_slave 是设备的子数据。 我们需要确保这个空间可用。 可以分配 struct spi_slave 所需的更多空间，但这是最小值。

```c
U_BOOT_DRIVER(exynos_spi) = {
...
        .per_child_auto = sizeof(struct spi_slave),
}
```


# 19. 可选：如果需要，设置 cs_info()

有时了解 SPI 片选是否有效很有用，但这从驱动程序外部并不明显。 在这种情况下，您可以为 cs_info() 提供一个方法来处理此问题。 如果您不提供它，则设备树将用于确定哪些芯片选择有效。

如果提供的片选无效，则返回 -EINVAL；如果有效，则返回 0。 如果不提供 cs_info() 方法，则所有未出现在设备树中的芯片选择均假定为 0。


# 20. 测试它

现在您已经编写了代码并进行了编译，请尝试使用“sf test”命令对其进行测试。 您可能需要为您的主板启用 CONFIG_CMD_SF_TEST。


# 21. 准备补丁并将其发送到邮件列表

您可以使用“tools/patman/patman”为您的工作准备、检查和发送补丁。 有关详细信息，请参阅工具/patman/README。


# 22. 关于 SPI uclass 功能的一点说明

SPI uclass 保留有关总线上每个设备“dev”的一些信息：

**struct dm_spi_slave_plat:**

&emsp;这是 device_get_parent_plat(dev)。 这是存储片选号以及默认总线速度和模式的位置。 它是在 spi_child_post_bind() 中自动从设备树中读取的。 设置后不得在运行时更改，因为平台数据在运行时应该是不可变的。

**struct spi_slave:**

&emsp;这是device_get_parentdata(dev)。 上面已经提到了。 它保存有关设备的运行时信息。

还有一些在幕后调用的 SPI uclass 方法：

**spi_post_bind():**

&emsp;当绑定新总线时调用。 这将扫描设备树以查找总线上的设备，并绑定每个设备。 这反过来会导致为每个设备调用 spi_child_post_bind() ，它将设备树信息读入父（每个子）平台数据中。

**spi_child_post_bind():**

&emsp;当新的孩子被绑定时调用。 如上所述，这会将设备树信息读取到每个子平台数据中。

**spi_child_pre_probe():**

&emsp;在调查新孩子之前调用。 这通过从父级的平台数据复制该子级的数据来设置 struct spi_slave 中的模式和速度。 它还设置“dev”指针，需要允许在该位置传递“struct spi_slave”，而不需要单独的“struct udevice”指针。

上述整理工作使您可以更轻松地编写 SPI 驱动程序。

