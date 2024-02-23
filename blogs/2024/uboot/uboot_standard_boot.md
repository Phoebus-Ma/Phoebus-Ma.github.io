
# 1. Introduction

标准启动为 U-Boot 提供了一种内置方式来自动启动操作系统，无需自定义脚本和其他自定义。 它引入了以下概念：

- bootdev  - 可以保存或访问发行版的设备（例如 MMC、以太网）;
- bootmeth - 扫描 bootdev 以查找引导流程的方法（例如发行版引导）；
- bootflow - 如何启动的描述（由发行版提供）。

对于 Linux，发行版（Linux 发行版，例如 Debian、Fedora）负责为其想要提供的每个内核组合创建引导流程。 这些引导流程存储在介质上，以便 U-Boot 可以发现它们。 此功能通常称为发行版启动，因为它是发行版在任何硬件上启动的一种方式 (查看[通用发行版配置概念](https://docs.u-boot.org/en/latest/develop/distro.html))。

传统上，U-Boot 依靠脚本来实现此功能。 有关详细信息，请参阅 [distro_bootcmd](https://github.com/u-boot/u-boot/blob/master/include/config_distro_bootcmd.h)。 这样做是因为 U-Boot 本身不支持扫描设备。 虽然这些脚本运行得非常好，但它们可能很难理解和扩展，而且该功能不包括测试。 它们还使得摆脱临时配置变得困难，因为它们是使用环境和大量#define 来实现的。

标准启动是发行版启动的概括。 它提供了一种更内置的 U-Boot 引导方式。 该功能可扩展到不同的操作系统（例如 Chromium OS）和设备（不仅仅是块和网络设备）。 它也支持 EFI boot 和 EFI bootmgr。

最后，标准启动支持以下操作 [嵌入式验证启动 (VBE)](https://docs.u-boot.org/en/latest/develop/vbe.html).


# 2. Bootflow

引导流程是描述如何引导发行版的文件。 从概念上讲，该文件可以有不同的格式，但目前 U-Boot 仅支持 [BootLoaderSpec](http://www.freedesktop.org/wiki/Specifications/BootLoaderSpec/) 格式。 看起来像这样：

```bash
menu autoboot Welcome to Fedora-Workstation-armhfp-31-1.9. Automatic boot in # second{,s}. Press a key for options.
menu title Fedora-Workstation-armhfp-31-1.9 Boot Options.
menu hidden

label Fedora-Workstation-armhfp-31-1.9 (5.3.7-301.fc31.armv7hl)
    kernel /vmlinuz-5.3.7-301.fc31.armv7hl
    append ro root=UUID=9732b35b-4cd5-458b-9b91-80f7047e0b8a rhgb quiet LANG=en_US.UTF-8 cma=192MB cma=256MB
    fdtdir /dtb-5.3.7-301.fc31.armv7hl/
    initrd /initramfs-5.3.7-301.fc31.armv7hl.img
```

正如您所看到的，它指定了一个内核、一个 ramdisk (initrd) 和一个从中加载 devicetree 文件的目录。 详细信息在 [distro_bootcmd](https://github.com/u-boot/u-boot/blob/master/include/config_distro_bootcmd.h) 中描述。

引导流程由发行版提供。 它不是 U-Boot 的一部分。 U-Boot 的工作只是解释文件并执行指令。 这使得发行版基本上可以在 U-Boot 支持的任何设备上启动。

通常会选择并引导第一个可用的引导流程。 如果失败，则尝试下一个。


# 3. Bootdev

U-Boot 在哪里找到保存操作系统的介质？ 这就是 bootdev 的工作。 Bootdev 只是媒体设备（例如 MMC、NVMe）之上的一层。 bootdev 访问设备，包括可能包含与操作系统相关的内容的分区和文件系统。

例如，MMC bootdev 提供对 MMC 设备上各个分区的访问。 它扫描这些文件系统以查找设置了引导标志的文件系统，然后提供这些文件系统的列表以供考虑。

有些引导设备在枚举总线之前不可见，例如 通过 USB 连接的闪存棒。 为了解决这个问题，每个 bootdev 都有一个关联的“hunters”，它可以寻找特定 uclass 类型的 bootdev。 例如，SCSI bootdev 扫描 SCSI 总线来查找设备，并为其找到的每个逻辑单元号 (LUN) 创建一个 bootdev。


# 4. Bootmeth

一旦提供了文件系统列表，U-Boot 如何在这些文件系统中找到引导流程文件。 这就是 bootmeth 的工作。 每种启动方法都有自己的方式来执行此操作。

例如，发行版 bootmeth 只是在提供的文件系统中查找名为 extlinux/extlinux.conf 的文件。 该文件构成了引导流程。 如果在多个分区上使用发行版 bootmeth，则可能会产生多个引导流程。

注意：可以有一个直接使用分区或整个设备的 bootmethod，但更常见的是使用文件系统。

请注意，某些 bootmeths 是“全局”的，这意味着它们自己选择 bootdev。 示例包括 VBE 和 EFI 启动管理器。 在这种情况下，他们提供了一个 read_bootflow() 方法，该方法检查它喜欢的任何引导设备，然后返回引导流程（如果找到）。 如果扫描大量设备，其中一些引导方法可能会非常慢。


# 5. Boot process

U-Boot 尝试尽可能使用“延迟初始化”方法，发行版启动也不例外。 算法是：

```bash
while (get next bootdev)
   while (get next bootmeth)
       while (get next bootflow)
           try to boot it
```

因此，U-Boot 通过 bootdevs 来工作，依次尝试每个 bootmethod 来获取引导流程，直到它启动或耗尽可用选项。

不需要 500 行#defines 和 4KB 启动脚本，所需要的只是以下命令：

```bash
bootflow scan -lb
```

它扫描可用的引导流程，可选地列出它找到的每个发现（-l）并尝试引导它（-b）。

当全局 bootmeths 可用时，通常会在上述 bootdev 扫描之前检查这些方法。


# 6. Controlling ordering

默认情况下，首先使用更快的引导设备（或那些被认为更快的引导设备），因为它们更有可能快速引导设备。

有几个选项可用于控制启动扫描的顺序：


## 6.1 boot_targets

此环境变量可用于控制搜索的 bootdev 列表及其顺序，例如：

```bash
setenv boot_targets "mmc0 mmc1 usb pxe"
```

可以删除或重新排序此列表中的条目以影响启动顺序。 如果该变量为空，则根据 bootdev 的优先级及其序列号使用默认排序。


## 6.2 bootmeths

默认情况下，bootmeths 按名称顺序进行检查。 使用 bootmeth list 查看排序。 请注意，extlinux 和脚本 bootmeth 是第一个，以保留旧发行版脚本使用的行为。

此环境变量可用于控制所使用的 bootmeths 列表及其顺序，例如：

```bash
setenv bootmeths "extlinux efi"
```

此列表中的条目可能会被删除或重新排序，以影响在每个 bootdev 上尝试 bootmeths 的顺序。 如果变量为空，则使用基于 bootmeth 序列号的默认排序，可以通过别名控制。

[bootmeth 命令](https://docs.u-boot.org/en/latest/usage/cmd/bootmeth.html#bootmeth-command) (bootmeth 命令) 的操作方式与设置此变量相同。


# 7. Bootdev uclass

bootdev uclass 提供了一个简单的 API 调用来从设备获取引导流程：

```bash
int bootdev_get_bootflow(struct udevice *dev, struct bootflow_iter *iter,
                         struct bootflow *bflow);
```

这需要一个迭代器来指示要使用的 bootdev、分区和 bootmeth。 它返回引导流程。 这是bootdev实现的核心。 实现此功能的 bootdev 驱动程序根据它们读取的介质而有所不同，但每个驱动程序都负责返回有效的引导流程（如果可用）。

名为 bootdev_find_in_blk() 的帮助程序可以非常轻松地通过几行代码为每个媒体设备 uclass 实现此函数。 对于许多类型的 bootdev，get_bootflow 成员可以为 NULL，表示使用默认处理程序。 这称为default_get_bootflow()，它仅适用于块设备。


# 8. Bootdev drivers

bootdev 驱动程序通常相当简单。 这是 mmc 的一个：

```bash
static int mmc_bootdev_bind(struct udevice *dev)
{
    struct bootdev_uc_plat *ucp = dev_get_uclass_plat(dev);

    ucp->prio = BOOTDEVP_2_INTERNAL_FAST;

    return 0;
}

struct bootdev_ops mmc_bootdev_ops = {
};

static const struct udevice_id mmc_bootdev_ids[] = {
    { .compatible = "u-boot,bootdev-mmc" },
    { }
};

U_BOOT_DRIVER(mmc_bootdev) = {
    .name        = "mmc_bootdev",
    .id        = UCLASS_BOOTDEV,
    .ops        = &mmc_bootdev_ops,
    .bind        = mmc_bootdev_bind,
    .of_match    = mmc_bootdev_ids,
};
```

您可能会注意到，未提供 get_bootflow 内存，因此为 NULL。 这意味着使用了default_get_bootflow()。 这只是获取块设备并调用 bootdev 帮助函数来完成其余的工作。 bootdev_find_in_blk() 的实现检查分区表，并尝试从 @iter->part 参数指定的分区号上的文件系统读取文件。 如果表中有任何可引导分区，则仅考虑可引导分区。

每个 bootdev 都有一个优先级，表示在不使用 boot_targets 的情况下使用它的顺序。 首先使用更快的引导设备，因为它们更有可能快速引导设备。


# 9. Environment Variables

标准引导使用各种环境变量。 这些允许主板在启动操作系统时控制东西的放置位置。 您应该确保您的董事会为这些设定价值观。

**fdtfile**

要加载的扁平化设备树 (FDT) 文件的名称，例如 “rockchip/rk3399-rockpro64.dtb”

**fdt_addr_r**

加载 FDT 的地址，例如 0x01f00000

**fdtoverlay_addr_r (needed if overlays are used)**

加载 FDT 覆盖层的地址，例如 0x02000000

**kernel_addr_r**

加载内核的地址，例如 0x02080000

**kernel_comp_addr_r**

解压内核的地址，例如 0x08000000

**kernel_comp_size**

解压内核的可用空间大小，例如 0x2000000

**pxefile_addr_r**

加载 PXE 文件的地址，例如 0x00600000

**ramdisk_addr_r**

加载 ramdisk 的地址，例如 0x06000000

**scriptaddr**

加载 U-Boot 脚本的地址，例如 0x00500000

**script_offset_f**

加载 U-Boot 脚本的 SPI 闪存偏移量，例如 0xffe000

**script_size_f**

要加载的脚本的大小，例如 0x2000

一些变量由脚本 bootmeth 设置：

**devtype**

用于启动的设备类型，例如 MMC

**devnum**

用于启动的设备号，例如 1

**distro_bootpart**

用于引导的分区，例如 2

**prefix**

包含脚本的目录

**mmc_bootdev**

用于启动的设备编号（例如 1）。 这仅由sunxi板上的MMC使用。


# 10. Device hierarchy

bootdev 设备是媒体设备的子设备。 在此示例中，您可以看到 bootdev 是块设备的兄弟设备，并且都是媒体设备的子设备：

```bash
mmc           0  [ + ]   bcm2835-sdhost        |   |-- mmc@7e202000
blk           0  [ + ]   mmc_blk               |   |   |-- mmc@7e202000.blk
bootdev       0  [   ]   mmc_bootdev           |   |   `-- mmc@7e202000.bootdev
mmc           1  [ + ]   sdhci-bcm2835         |   |-- sdhci@7e300000
blk           1  [   ]   mmc_blk               |   |   |-- sdhci@7e300000.blk
bootdev       1  [   ]   mmc_bootdev           |   |   `-- sdhci@7e300000.bootdev
```

bootdev 设备通常是通过调用 bootdev_setup_for_dev() 或 bootdev_setup_for_sibling_blk() 在媒体 uclass 的 post_bind() 方法中自动创建的。 代码通常是这样的：

```bash
/* dev is the Ethernet device */
ret = bootdev_setup_for_dev(dev, "eth_bootdev");
if (ret)
    return log_msg_ret("bootdev", ret);
```

or:

```bash
/* blk is the block device (child of MMC device)
ret = bootdev_setup_for_sibling_blk(blk, "mmc_bootdev");
if (ret)
    return log_msg_ret("bootdev", ret);
```

这里，eth_bootdev 是以太网 bootdev 驱动程序的名称，dev 是以太网设备。 即使未启用标准引导，也可以安全地调用此函数，因为在这种情况下它不会执行任何操作。 它可以添加到所有实现合适媒体的 uclass 中。


# 11. The bootstd device

标准启动需要 bootstd 设备的单个实例才能正常工作。 这包括有关标准启动状态的全局信息。 有关此结构，请参阅 struct bootstd_priv，可通过 bootstd_get_priv() 访问。

在设备树中，如果添加 bootmeth 设备，它们应该是 bootstd 设备的子设备。 有关示例，请参阅 arch/sandbox/dts/test.dts。


# 12. Automatic devices

可以手动定义设备树中的所有所需设备，但这不是必需的。 bootstd uclass 包含 dm_scan_other() 函数，如果未找到，该函数将创建 bootstd 设备。 如果根本没有找到 bootmeth 设备，它会为每个可用的 bootmeth 驱动程序创建一个。

如果您的设备树有任何 bootmeth 设备，它必须具有您想要使用的所有设备，因为在这种情况下不会自动创建 bootmeth 设备。


# 13. Using devicetree

如果 bootdev 很复杂或需要配置信息，则可以将其作为媒体设备的子设备添加到设备树中。 例如，假设一个 bootdev 从 SPI 闪存读取引导流程。 devicetree 片段可能如下所示：

```bash
spi@0 {
    flash@0 {
        reg = <0>;
        compatible = "spansion,m25p16", "jedec,spi-nor";
        spi-max-frequency = <40000000>;

        bootdev {
            compatible = "u-boot,sf-bootdev";
            offset = <0x2000>;
            size = <0x1000>;
        };
    };
};
```

sf-bootdev 驱动程序可以实现一种使用提供的偏移量和大小从 SPI 闪存读取数据的方法，并将引导流程文件返回给调用者。 当发行版启动想要读取内核时，它会调用 distro_getfile()，它必须提供一种从 SPI 闪存读取的方法。 有关更多详细信息，请参阅 distro_boot 中的 distro_boot() 。

当然，这都是 U-Boot 内部的。 所有发行版看到的都是另一种启动方式。


# 14. Configuration

标准启动通过 CONFIG_BOOTSTD 启用。 每个 bootmeth 也有自己的 CONFIG 选项。 例如，CONFIG_BOOTMETH_EXTLINUX 支持使用 extlinux.conf 文件从磁盘启动。

要启用标准启动的所有功能，请使用 CONFIG_BOOTSTD_FULL。 这包括完整的命令集、出现问题时的更多错误消息以及使用 bootmeths 环境变量进行 bootmeth 排序。

您可能还应该启用 CONFIG_BOOTSTD_DEFAULTS，它提供了多种文件系统和网络功能（如果启用了 CONFIG_NET），以便可以选择良好的引导选项。


# 15. Available bootmeth drivers

Bootmeth 驱动程序适用于：

- extlinux / syslinux boot from a disk
- extlinux boot from a network (PXE)
- U-Boot scripts from disk, network or SPI flash
- EFI boot using bootefi from disk
- VBE
- EFI boot using boot manager


# 16. Command interface

三个命令可用：

**bootdev**

允许列出可用的引导设备、选择特定的引导设备并获取有关它的信息。查看 [bootdev 命令](https://docs.u-boot.org/en/latest/usage/cmd/bootdev.html)

**bootflow**

允许扫描一个或多个 bootdev 的引导流程、列出可用的引导流程、选择一个、获取有关它的信息并引导它。查看 [bootflow 命令](https://docs.u-boot.org/en/latest/usage/cmd/bootflow.html)

**bootmeth**

允许列出可用的引导方法并设置尝试它们的顺序。查看 [bootmeth command](https://docs.u-boot.org/en/latest/usage/cmd/bootmeth.html)


# 17. Bootflow states

以下是引导流程可能处于的状态列表：

| State | Meaning                                                                       |
| ----- | ----------------------------------------------------------------------------- |
| base  | 起始状态，表示未找到介质/分区。 对于 SD 卡插槽，它可能表明卡未插入。                |
| media | 找到媒体（例如插入 SD 卡）但未找到分区信息。 它可能缺少分区表或有读取错误。          |
| part  | 已找到分区，但无法读取文件系统。 这可能是因为该分区不包含文件系统或文件系统严重损坏。 |
| fs    | 已找到文件系统，但无法读取该文件。 它可能丢失或位于错误的子目录中。                 |
| file  | 已找到文件并检测到其大小，但无法读取。 这可能表明文件系统损坏。                     |
| ready | 文件已加载并可供使用。 在此状态下，引导流程已准备好引导。                          |


# 18. Migrating from distro_boot

要从 distro_boot 迁移：

1. 更新您的板头文件以删除 BOOTENV 和 BOOT_TARGET_xxx 定义。 标准启动会自动查找可用的启动设备。
2. 除非需要，否则删除“boot_targets”变量。 标准启动使用从最快到最慢的默认顺序，这通常与主板使用的顺序相匹配。
3. 确保您的主板启用了 CONFIG_BOOTSTD_DEFAULTS，以便它可以引导常见的 Linux 发行版。

示例补丁位于 [migrate_patch](https://patchwork.ozlabs.org/project/uboot/patch/20230727215433.578830-2-sjg@chromium.org/).

如果您正在为您的主板使用自定义启动脚本，请考虑创建您自己的 bootmeth 来保存逻辑。 boot/bootmeth_... 中有各种示例。


# 19. Theory of operation

这描述了标准引导如何进展到引导操作系统。

开始。 必须绑定所有必需的设备，包括 bootstd，它提供包含可选配置信息的顶级结构 bootstd_priv。 bootstd 设备还保存扫描时使用的各种列表。 此步骤通常由驱动程序模型自动处理，如 [Automatic Devices](https://docs.u-boot.org/en/latest/develop/bootstd.html#automatic-devices) 中所述。

还需要 Bootdev 来提供对要使用的介质的访问。 这些本身并没有用：需要 bootmeths 来提供扫描这些 bootdev 的方法。 因此，总而言之，我们需要一个 bootstd 设备、一个或多个 bootdev 设备以及一个或多个 bootmeth 设备。

一旦这些准备就绪，通常会发出引导流程扫描命令。 这就是迭代过程的开始，其中包括寻找 bootdev 并逐一查看 bootdev 及其分区以查找 bootflow。

使用 bootflow_scan_first() 开始迭代。

迭代器通过 bootflow_iter_init() 设置。 这只是用给定的标志创建一个空的。 标志用于控制是否显示每个迭代、是否返回迭代（即使它们没有产生有效的引导流程）、是否仅迭代单个 bootdev 等。

然后根据给定的参数设置迭代器：

- 当提供 dev 时，将扫描单个 bootdev。 在这种情况下，设置了 BOOTFLOWIF_SKIP_GLOBAL 和 BOOTFLOWIF_SINGLE_DEV。 在这种情况下没有使用 hunters；

- 否则，当提供标签时，将扫描单个标签或名为 bootdev 的标签。 在这种情况下，设置了 BOOTFLOWIF_SKIP_GLOBAL 并且有三个选项（对稍后描述的 iter_incr() 函数有影响）:
    - 如果标签指示数字 bootdev 编号（例如“2”），则设置 BOOTFLOW_METHF_SINGLE_DEV。 在这种情况下，移动到下一个 bootdev 就会简单停止，因为只有一个。 没有使用 hunters；
    - 如果标签指示特定媒体设备（例如“mmc1”），则设置 BOOTFLOWIF_SINGLE_MEDIA。 在这种情况下，移动到下一个 bootdev 仅处理媒体设备的子设备。 使用hunters，在本例中仅使用“mmc” hunters；
    - 如果标签指示媒体 uclass（例如“mmc”），则设置 BOOTFLOWIF_SINGLE_UCLASS。 在这种情况下，将使用该 uclass 中的所有 bootdev。 使用hunters，在本例中仅使用“mmc” hunters。

- 否则，不会设置上述任何标志，并设置迭代以按顺序通过 boot_targets 环境变量（或 bootdev-order 设备树属性）工作，首先运行相关的hunters。 在这种情况下 cur_label 用于指示正在处理的标签。 如果没有标签列表，则按优先级顺序处理所有 bootdev，并按顺序运行 hunters。

因此，通过上述内容，可以以多种方式进行迭代。

不会尝试确定 bootdev 的顺序，因为如果我们使用hunters，则无法提前知道这一点。 任何hunters都可能会发现新的引导程序并扰乱原来的顺序。

接下来，bootmeths 的顺序由 bootmeth_setup_iter_order() 确定。 默认情况下，排序再次按序列号（即 /aliases 节点）进行排序，否则按设备树中的顺序排序。 但可以使用 bootmeth order 命令或 bootmeths 环境变量来设置排序。 如果已完成，则排序位于 struct bootstd_priv 中，以便将排序简单地复制到迭代器中。 无论哪种方式，它都会设置 method_order 数组以及 num_methods。

请注意，全局 bootmeths 始终放在排序的末尾。 如果存在，则将 cur_method 设置为第一个，以便首先完成全局 bootmeths。 一旦使用了所有方法，这些引导方法就会从迭代中删除。 当没有全局 bootmeths 时，cur_method 设置为 0。

此时，迭代器已准备好使用，并选择了第一个 bootmeth。 大多数其他字段都是 0。这意味着当前分区是 0，这意味着整个设备，因为分区编号从 1 开始。这也意味着 max_part 是 0，即我们知道的最大分区编号是 0，这意味着，据我们所知，此 bootdev 上没有分区表。

迭代器准备就绪后， bootflow_scan_first() 检查当前设置是否产生有效的引导流程。 这是由 bootflow_check() 处理的，它要么返回 0（如果有东西），要么返回错误（稍后会详细介绍）。 如果设置了 BOOTFLOWIF_ALL 迭代器标志，则即使错误也会作为不完整的引导流程返回，但通常错误会导致进入下一个迭代。

请注意，bootflow_check() 通过在每个 bootmeths 上调用 bootmeth_get_bootflow() 来显式处理全局 bootmeths。 doing_global 标志指示迭代器何时处于该状态。

bootflow_scan_next() 函数处理进入下一次迭代并检查它。 事实上，它处于一个循环中，反复执行此操作，直到找到它想要返回的内容。

实际的“继续”部分是在 iter_incr() 中实现的。 这是一个相当简单的函数。 它增加第一个计数器。 如果达到最大值，它将其设置为零并增加第二个计数器。 您可以将所有计数器视为一个具有三位数的数字，这些数字按顺序递增，最低有效位在右侧，如下所示：

| bootdev | part | method |
| ------- | ---- | ------ |
| 0       | 0    | 0      |
| 0       | 0    | 1      |
| 0       | 0    | 2      |
| 0       | 1    | 0      |
| 0       | 1    | 1      |
| 0       | 1    | 2      |
| 1       | 0    | 0      |
| 1       | 0    | 1      |
| …       |      |        |

method 的最大值是 num_methods - 1，因此当它超过该值时，它会返回到 0 并考虑下一部分。 其最大值是 max_part，对于所有 bootdevs 来说它最初为零。 如果我们在该 bootdev 上找到分区表，则 max_part 可以在迭代过程中更新为更高的值 - 请参阅 bootdev_find_in_blk() ，稍后介绍。 如果超过其最大值，则使用下一个 bootdev。 通过这种方式，iter_incr() 会遍历所有可能性，每次调用时向前移动一次。

请注意，全局引导方法在上述描述中引入了微妙之处。 当doing_global为true时，迭代仅在bootmeths之间发生，即上面的最后一列。 全局引导方法位于列表的末尾。 假设它们是列表中的条目 3 和 4，则迭代如下所示：

| bootdev | part | method | notes                                  |
| ------- | ---- | ------ | -------------------------------------- |
| .       | .    | 3      | doing_global = true, method_count = 5  |
| .       | .    | 4      |                                        |
| 0       | 0    | 0      | doing_global = false, method_count = 3 |
| 0       | 0    | 1      |                                        |
| 0       | 0    | 2      |                                        |
| 0       | 1    | 0      |                                        |
| 0       | 1    | 1      |                                        |
| 0       | 1    | 2      |                                        |
| 1       | 0    | 0      |                                        |
| 1       | 0    | 1      |                                        |
| …       |      |        |                                        |

doing_global 的值从 true 到 false 的转换也在 iter_incr() 中处理。

请注意，上面 bootdev 列中的值实际上并未存储 - 它仅用于说明。 实际上，iter_incr() 使用标志来确定是否移动到 uclass 中的下一个 bootdev、媒体设备的下一个子设备、下一个标签或下一个优先级，具体取决于标志设置（请参阅 BOOTFLOW_METHF_SINGLE_DEV 等） 。 多于）。

并不期望迭代会真正完成。 通常，有效的引导流程会在早期被发现。 使用 bootflow scan -b，会导致引导流程立即启动。 假设它成功了，迭代永远不会完成。

另请注意，迭代器保存当前正在考虑的组合。 因此，当调用 iter_incr() 时，它会递增到下一个并返回它，即新的当前组合。

另请注意 struct bootflow_iter 中的 err 字段。 通常为 0，因此对 iter_inc() 没有影响。 但如果它不为零，则发出错误信号，它向迭代器指示调用时应该执行的操作。 例如，它可以强制移动到下一个分区或 bootdev。 特殊值 BF_NO_MORE_PARTS 和 BF_NO_MORE_DEVICES 可以处理此问题。 当 iter_incr 看到 BF_NO_MORE_PARTS 时，它知道应该立即移动到下一个 bootdev。 当它看到 BF_NO_MORE_DEVICES 时，它知道它无能为力，因此它应该立即返回。 iter_incr() 的调用者负责根据它看到的返回值更新 err 字段。

上面描述了高层的迭代过程。 它基本上是一个非常简单的增量函数，带有一个名为 bootflow_check() 的检查器，用于检查生成的每次迭代的结果，以确定它是否可以生成引导流程。

那么 bootflow_check() 内部发生了什么？ 它只是调用 uclass 方法 bootdev_get_bootflow() 来要求 bootdev 返回引导流程。 它将迭代器传递给 bootdev 方法，以便该函数知道我们在说什么。 首先，引导流程在 BOOTFLOWST_BASE 状态下设置，仅初始化方法和开发。 但 bootdev 可能会填写更多，例如 根据发现的内容更新状态。 对于全局 bootmeths，将调用 bootmeth_get_bootflow() 函数而不是 bootdev_get_bootflow()。

根据 bootdev 或 bootmeth 的响应， bootflow_check() 要么返回有效的引导流程，要么返回部分有错误的引导流程。 部分引导流程是指设置了一些字段，但未达到 BOOTFLOWST_READY 状态的引导流程。 如前所述，如果设置了 BOOTFLOWIF_ALL 迭代器标志，则返回所有引导流程，甚至是部分引导流程。 这可以帮助调试。

因此，此时您可以看到，是否可以从特定迭代生成引导流程的完全控制取决于 bootdev（或全局 bootmeth）。 每个人都可以采取自己的方法。

再往下看，bootdev 在其 get_bootflow() 方法中做了什么？ 让我们考虑 MMC bootdev。 在这种情况下，对 bootdev_get_bootflow() 的调用最终会在 default_get_bootflow() 中结束。 它找到 bootdev 的父设备，即 UCLASS_MMC 设备本身，然后找到与其关联的块设备。 然后它调用辅助函数 bootdev_find_in_blk() 来完成所有工作。 这对于任何基于媒体设备的 bootdev 都很常见。

bootdev_find_in_blk() 帮助器在 bootdev uclass 中实现。 它命名引导流程并从迭代器复制分区号。 然后它调用 bootmeth 设备来检查是否可以支持该设备。 这很重要，因为例如某些引导方法仅适用于网络设备。 如果该检查失败，则停止。

假设 bootmeth 很高兴，或者至少表明它愿意尝试（通过从其 check() 方法返回 0），下一步就是尝试分区。 如果有效，它会尝试检测文件系统。 如果有效，那么它会再次调用 bootmeth 设备，这次是为了读取引导流程。

注意：通常在块设备上调用 bootmeth 需要一个文件系统，但是不需要文件系统的 bootmeth 可以设置 BOOTMETHF_ANY_PART 标志来指示它们可以扫描任何分区。 一个例子是 ChromiumOS bootmeth，它可以将内核存储在原始分区中。 另请注意，沙箱是一种特殊情况，因为在这种情况下，即使块设备为 NULL，也可以访问主机文件系统。

如果我们以 bootmeth_extlinux 驱动程序为例，该调用将以 extlinux_read_bootflow() 结束。 它已经准备好文件系统，因此尝试各种文件名来尝试找到 extlinux.conf 文件，如果可能的话读取它。 如果一切顺利，引导流程最终会进入 BOOTFLOWST_READY 状态。

此时，我们从 bootmeth 驱动程序回退到 bootdev_find_in_blk()，然后回到 default_get_bootflow()，然后到 bootdev_get_bootflow()，然后到 bootflow_check()，最后到其调用者，即 bootflow_scan_first() 或 bootflow_scan_next()。 无论哪种情况，引导流程都会作为本次迭代的结果返回，假设它已进入 BOOTFLOWST_READY 状态。

这就是扫描引导流程的基本操作。 引导引导流程的过程由该引导流程的 bootmeth 驱动程序处理。 在 extlinux 引导的情况下，它将解析并处理读取的 extlinux.conf 文件。 请参阅 extlinux_boot() 了解其工作原理。 该处理可能涉及读取其他文件，这是由 read_file() 方法处理的，在本例中为 extlinux_read_file()。 所有 bootmethds 都应该支持读取文件，因为引导流程通常只是基本指令，不包括操作系统本身、ramdisk、设备树等。

绝大多数 bootstd 代码涉及迭代 bootdev 上的分区并使用 bootmethds 查找引导流程。

不是块设备的 bootdev 怎么样？ 它们的处理方法与上述相同，但实现方式不同。 例如，PXE 启动（通过网络）的 bootmeth 使用 tftp 来读取文件，而不是 fs_read()。 但除此之外，它非常相似。


# 20. Tests

测试位于 test/boot 中，涵盖核心功能和命令。 所有测试都使用沙箱，因此可以在标准 Linux 计算机和 U-Boot 的 CI 中运行。

为了进行测试，使用了 DOS 格式的磁盘映像，其中包含 FAT 分区和第二个未使用的分区。 这是在 setup_bootflow_image() 中创建的，如果无法创建（例如在 CI 中），则使用源树中的固定镜像。


# 21. Bootflow internals

bootstd 设备保存扫描的引导流程以及当前选择的 bootdev 和引导流程（供命令使用）的链接列表。 这是在 struct bootstd_priv 中。

每个 bootdev 设备都有自己的 struct bootdev_uc_plat ，其中包含仅针对该设备的扫描引导流程列表。

引导流程本身记录在 [bootflow_h](https://github.com/u-boot/u-boot/blob/master/include/bootflow.h) 中。 它包括有关引导流程的各种信息和用于保存文件的缓冲区。


# 22. Future

除了下面的待办事项之外，将来可能会实现不同类型的引导流程文件，例如 Chromium 操作系统支持目前仅作为 chromebook_coral 中的脚本提供。


# 23. To do

要完全替换发行版启动脚本，需要做一些事情：

- 实施扩展（带有附加板的设备树覆盖）

其他想法:

- bootflow prep 加载准备启动的所有内容，以便 bootflow boot 可以执行启动。
- 自动加载内核、FDT等到合适的地址，因此板不需要指定诸如pxefile_addr_r之类的东西。
