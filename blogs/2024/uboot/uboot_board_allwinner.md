
# 1. Introduction

iROM --> u-boot-spl.bin --> bl31.bin --> u-boot.bin --> zImage --> rootfs --> applications.


# 2. Allwinner SoC based boards

对于使用基于 Allwinner ARM 的 SoC（“sunxi”）的主板，U-Boot 构建系统会生成一个集成映像文件：u-boot-sunxi-with-spl.bin。 该文件可用于 SD 卡、eMMC 设备、SPI 闪存以及基于 USB-OTG 的启动方法 (FEL)。 要构建此文件：

- 对于 64 位 SoC，首先构建可信固件（TF-A，以前称为 ATF），您将需要它的 bl31.bin。 请参阅下面的更多细节。
- （可选）在 64 位 SoC 上构建 [crust](https://github.com/crust-firmware/crust) 管理处理器固件，您将需要其 scp.bin。 请参阅下面的更多细节。
- 构建 U-Boot:

```bash
$ export BL31=/path/to/bl31.bin               # required for 64-bit SoCs
$ export SCP=/path/to/scp.bin                 # optional for some 64-bit SoCs
$ make <yourboardname>_defconfig
$ make
```

- 传输到（微型）SD 卡（有关更多详细信息，请参阅下文）：

```bash
$ sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/sdX bs=8k seek=1
```

- 启动并享受吧！

**Note**

全志 BootROM 加载的传统 SD 卡位置为 8KB（扇区 16）。 这适用于旧的 MBR 分区方案，大多数 SD 卡都采用该方案进行格式化。 然而，这是在潜在的 GPT 分区表的中间，该分区表在此步骤中将变得无效。 较新的 SoC（从 2014 年底的 H3 开始）还支持从 128KB 启动，这甚至超出了 GPT，因此是一个更安全的位置。

有关更多详细信息以及替代引导位置或安装，请参阅下文。


# 3. Building Arm Trusted Firmware (TF-A)

使用 64 位 Soc（A64、H5、H6、H616、R329）的主板需要 [Arm Trusted Firmware-A](https://www.trustedfirmware.org/projects/tf-a/) 固件的 BL31 阶段 。 这为 Armv8-A 提供了安全软件的参考实现，并提供 PSCI 和 SMCCC 服务。 全志支持已完全主流化。 构建 bl31.bin：

```bash
$ git clone https://git.trustedfirmware.org/TF-A/trusted-firmware-a.git
$ cd trusted-firmware-a
$ make CROSS_COMPILE=aarch64-linux-gnu- PLAT=sun50i_a64 DEBUG=1
$ export BL31=$(pwd)/build/sun50i_a64/debug/bl31.bin
```

A64 和 H5 SoC 的目标平台 (PLAT=) 为 sun50i_a64，H6 sun50i_h6、H616 sun50i_h616 和 R329 sun50i_r329 的目标平台为 sun50i_a64。 使用：

```bash
$ find plat/allwinner -name platform.mk
```

查找所有支持的平台。 TF-A 的 [docs/plat/allwinner.rst](https://trustedfirmware-a.readthedocs.io/en/latest/plat/allwinner.html) 包含更多信息并列出了一些构建选项。


# 4. Building the Crust management processor firmware

对于某些 SoC 和主板，集成的 OpenRISC 管理控制器可用于提供电源管理服务，最重要的是挂起到 RAM。 有一个社区支持的开源实现，称为 Crust，它在大多数具有管理控制器的 SoC 上运行。

该固件部分是可选的，将 SCP 环境变量设置为 /dev/null 可以避免在没有环境变量的情况下构建时出现警告消息。

为了构建crust的scp.bin，你需要一个OpenRISC（or1k）交叉编译器，然后：

```bash
$ git clone https://github.com/crust-firmware/crust.git
$ cd crust
$ make <yourboard>_defconfig
$ make CROSS_COMPILE=or1k-none-elf- scp
$ export SCP=$(pwd)/build/scp/scp.bin
```

在 [configs/](https://github.com/crust-firmware/crust/tree/master/configs) 目录中查找支持的主板配置列表。 [crust README](https://github.com/crust-firmware/crust/blob/master/README.md#building-the-firmware) 有有关构建过程的更多信息，包括有关在哪里获取 OpenRISC 交叉的信息 编译器。


# 5. Building the U-Boot image

首先找到您的主板的 U-Boot defconfig 文件。 这些文件位于 configs/ 目录中； 您可以 grep 查找 devicetree 文件的存根名称（如果您知道的话），或者查找 SoC 名称以找到正确的版本：

```bash
$ git grep -l MACH_SUN8I_H3 configs
$ git grep -l sun50i-h6-orangepi-3 configs
```

[linux-sunxi](https://linux-sunxi.org/) wiki 还在相应的板页面中列出了 defconfig 文件的名称。 然后使用这个 defconfig 文件创建 .config 文件，并构建映像：

```bash
$ make <yourboard>_defconfig
$ make
```

对于 64 位板，这需要设置 BL31 环境变量（如上面 TF-A 构建示例中所示），或者在构建命令行上提供：

```bash
$ make BL31=/src/tf-a.git/build/sun50i_h616/debug/bl31.bin
```

这同样适用于（可选）SCP 固件。

包含您需要的所有内容的文件称为 u-boot-sunxi-with-spl.bin，您可以在 U-Boot（构建）树的根文件夹中找到它。 除了原始 NAND 闪存设备外，这个相同的文件可用于任何启动源。 它将包含 SPL 映像，配有 BROM 识别的正确签名以及所需的校验和。 此外，它将至少包含正确的 U-Boot，或者以传统 U-Boot 映像格式包装，或者以 FIT 映像包装。 该板的设备树也包含在内，要么附加到 U-Boot 适当的映像，要么包含在 FIT 映像中。 如果 SoC 需要，此 FIT 文件还将包含其他固件映像。


# 6. Installing U-Boot

## 6.1 Installing on a (micro-) SD card

所有 Allwinner SoC 都会尝试在连接到第一个 MMC 控制器的 SD 卡的第 16 扇区 (8KB) 处查找启动映像。 要将生成的图像传输到 SD 卡，请从任何带有（微型）SD 卡读卡器的 Linux 设备（包括开发板本身），输入：

```bash
$ sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/sdX bs=1k seek=8
```

/dev/sdx需要替换为SD卡读卡器的块设备名称。 在某些机器上，这可能是 /dev/mmcblkX。 较新的 SoC（从 2014 年的 H3 开始，包括所有 ARM64 SoC）也会查看扇区 256 (128KB) 的签名（在检查了 8KB 位置之后）。 在那里安装固件的优点是不与 GPT 分区表重叠。 只需将上面的“seek=8”替换为“seek=128”即可。

您还可以使用现有的（主线）U-Boot 写入 SD 卡。 将生成的 U-Boot 映像加载到 DRAM 中的某个位置（通过 ext4load、fatload 或 tftpboot），然后写入 MMC 设备 0：

```bash
=> fatload mmc 0:1 $kernel_addr_r u-boot-sunxi-with-spl.bin
=> mmc dev 0
=> mmc write $kernel_addr_r 0x10 0x7f0
```

要在较新的 SoC 上使用备用引导位置：

```bash
=> mmc write $kernel_addr_r 0x100 0x700
```


## 6.2 Installing on eMMC (on-board flash memory)

有些主板具有焊接的 eMMC 芯片，其他一些主板具有 eMMC 插槽以容纳可选的 eMMC 模块。 U-Boot可以安装到这些芯片上，无需插入SD卡即可启动。 Boot-ROM 可以从常规用户数据分区启动，也可以从单独的 eMMC 启动分区之一启动。 U-Boot 可以从设备上正在运行的 Linux 实例、正在运行的（主线）U-Boot 或通过（可移动）eMMC 模块的适配器安装。


## 6.3 Installing on an eMMC user data partition from Linux

如果您的设备上有正在运行的 Linux 实例，并且已以某种方式将映像文件复制到该设备，您可以从那里将映像直接写入 eMMC 设备。 首先找到块设备文件的名称，它是/dev/mmcblk<X>设备之一。 eMMC 设备通常还会列出 /dev/mmcblk<X>boot0 分区（见下文），这可以帮助您将其与 SD 卡设备区分开来。 要安装到用户数据分区：

```bash
$ sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/dev/mmcblkX bs=1k seek=8
```

与 SD 卡类似，较新的 SoC（H3 及更高版本）中的 BROM 也会检查 eMMC 的扇区 256，因此您也可以使用“seek=128”。 eMMC 设备上的 GPT 比 SD 卡上的可能性大得多，因此您应该坚持使用替代位置，或使用启动分区之一。


## 6.4 Installing on an eMMC boot partition from Linux

以下示例中的/dev/mmcblkX需要替换为eMMC设备的块设备名称。 eMMC 设备还可以通过在 /proc/partitions 中列出引导分区 (/dev/mmcblkXboot0) 来识别。

要允许从 eMMC 启动分区之一启动，需要首先启用该分区。 这只需要完成一次，因为此设置是持久的，即使启动分区可以在以后随时被禁用或再次更改：

```bash
# apt-get install mmc-utils
# mmc bootbus set single_hs x1 x4 /dev/mmcblkX
# mmc bootpart enable 1 1 /dev/mmcblkX
```

最后一个命令中的第一个“1”指向要使用的引导分区号，通常设备提供两个引导分区。

默认情况下，Linux 禁用对引导分区的写访问，以防止意外覆盖。 您需要禁用写保护（直到下次重新启动），然后才能将 U-Boot 映像写入所选启动分区的第一个扇区：

```bash
# echo 0 > /sys/block/mmcblkXboot0/force_ro
# dd if=u-boot-sunxi-with-spl.bin of=/dev/mmcblkXboot0 bs=1k
```


## 6.5 Installing on an eMMC user data partition from U-Boot

您还可以将生成的映像文件写入 SD 卡，从那里启动设备，然后从 U-Boot 将相同的映像刻录到 eMMC 设备。 以下命令将图像从 SD 卡复制到 eMMC 设备：

```bash
=> mmc dev 0
=> mmc read $kernel_addr_r 0x10 0x7f0
=> mmc dev 1
=> mmc write $kernel_addr_r 0x10 0x7f0
```

您还可以将图像从 SD 卡的 8K 偏移量复制到 eMMC 的 128K 偏移量（或任意组合），只需将上面的“0x10 0x7f0”分别更改为“0x100 0x700”即可。 当然，可以通过任何其他加载方式加载镜像文件，包括fatload、ext4load、tftpboot。


## 6.6 Installing on an eMMC boot partition from U-Boot

所选的 eMMC 启动分区需要首先启用（与上面的 Linux 中相同），您可以通过 U-Boot 执行此操作：

```bash
=> mmc dev 1
=> mmc bootbus 1 1 0 0
=> mmc partconf 1 1 1 1
```

两个命令中的第一个“1”表示 MMC 设备编号。 partconf命令中的第二个“1”设置所需的BOOT_ACK选项，最后两个“1”分别选择活动引导分区和下一次数据访问的目标。 因此，对于下一个寻址引导分区之一的“mmc write”命令，最后一个数字必须是“1”或“2”，“0”将切换（返回）到正常用户数据分区。

然后将 u-boot-sunxi-with-spl.bin 映像文件加载到 DRAM 中，方法是直接从 SD 卡或 eMMC 用户数据分区读取，或者从文件系统或 TFTP（见上文）读取，并将其传输到 启动分区：

```bash
=> tftpboot $kernel_addr_r u-boot-sunxi-with-spl.bin
=> mmc write $kernel_addr_r 0 0x7f0
```

之后，设备应从选定的启动分区启动，这优先于从用户数据分区启动。


## 6.7 Installing on SPI flash

有些设备的板上焊接有 SPI NOR 闪存芯片。 如果它连接到 PortC 上的 SPI0 引脚，BROM 也可以从那里启动。 通常，SPI 闪存的启动优先级最低，因此将首先考虑 SD 卡和 eMMC 设备。


## 6.8 Installing on SPI flash from Linux

如果设备树启用并描述了 SPI 闪存设备，您可以使用 [MTD utils](http://www.linux-mtd.infradead.org/) 从 Linux 访问 SPI 闪存内容：

```bash
# apt-get install mtd-utils
# mtdinfo
# flashcp -v u-boot-sunxi-with-spl.bin /dev/mtdX
```

/dev/mtdX 需要替换为相应的设备名称，如 mtdinfo 的输出中列出的。


## 6.9 Installing on SPI flash from U-Boot

如果在 U-Boot 配置中启用了 SPI 闪存驱动程序和命令支持 (CONFIG_CMD_SF)，则也可以通过 U-Boot 安装映像文件：

```bash
=> tftpboot $kernel_addr_r u-boot-sunxi-with-spl.bin
=> sf probe
=> sf erase 0 +0xf0000
=> sf write $kernel_addr_r 0 $filesize
```


## 6.10 Installing on SPI flash via USB in FEL mode

如果设备处于 FEL 模式（见下文），则还可以使用 sunxi-fel 实用程序通过 USB(-OTG) 电缆从任何 USB 主机写入 SPI 闪存：

```bash
$ sunxi-fel spiflash-write 0 u-boot-sunxi-with-spl.bin
```


# 7. Booting via the USB(-OTG) FEL mode

如果 BROM 检查的启动位置均不包含介质或有效签名，则 BROM 将进入所谓的 FEL 模式，在该模式下，它将在 SoC 的 USB-OTG 接口上侦听来自主机的命令。 这些命令允许读取和写入任意内存位置，还可以在任何地址开始执行，从而允许仅通过 USB 电缆引导开发板。 某些主板具有“FEL”或“U-Boot”按钮，尽管存在有效的引导位置，但仍会强制进入 FEL 模式。 通过 SD 卡上的 [magic binary](https://github.com/linux-sunxi/sunxi-tools/raw/master/bin/fel-sdboot.sunxi) 也可以实现同样的效果，它允许进入 FEL 任何板上的模式。

要使用 FEL 启动，请通过上述任何方法（无启动介质、FEL 按钮、带有 FEL 二进制文件的 SD 卡）让开发板进入 FEL 模式，然后将 USB 电缆连接到开发板的 USB OTG 端口。 有些板（Pine64、电视盒）没有单独的 OTG 端口。 在这种情况下，大多数 USB-A 端口之一连接到 USB0，并且可以通过非标准 USB-A 到 USB-A 电缆使用。

通常，除了连接的主机上出现新的 USB 设备外，板上没有 FEL 模式指示。 USB 供应商/设备 ID 为 1f3a:efe8。 大多数情况下，这将被识别为“处于 FEL/闪烁模式的 sunxi SoC OTG 连接器”，但较旧的发行版可能仍会报告“处于闪烁模式的昂达（未经验证）V972 平板电脑”。

[sunxi_fel](https://github.com/linux-sunxi/sunxi-tools) 工具实现了专有的 BROM 协议，并且允许通过提供我们古老的 u-boot-sunxi-with-spl 来引导 U-Boot。 垃圾桶：

```bash
$ sudo apt-get install sunxi-tools
$ sunxi-fel uboot u-boot-sunxi-with-spl.bin
```

其他二进制文件（如内核、初始 ramdisk 或启动脚本）也可以通过 FEL 上传，请查看 Wiki 的 [FEL 页面](https://linux-sunxi.org/FEL/USBBoot) 了解更多详细信息。

