
# 1. Overview

为了统一辅助程序加载器 (SPL) 的所有现有实现并允许简单地添加新实现，创建了这个通用 SPL 框架。 有了这个框架，几乎所有板的源文件都可以重用。 不再需要代码重复或符号链接。


# 2. How it works

SPL 的目标文件是单独构建的，并放置在“spl”目录中。 最终生成的二进制文件是 u-boot-spl、u-boot-spl.bin 和 u-boot-spl.map。

Kconfig 为 SPL 启用名为 CONFIG_SPL_BUILD 的配置选项。 因此，可以使用不同的设置为 SPL 编译源文件。

例如：

```c
ifeq ($(CONFIG_SPL_BUILD),y)
obj-y += board_spl.o
else
obj-y += board.o
endif

obj-$(CONFIG_SPL_BUILD) += foo.o

#ifdef CONFIG_SPL_BUILD
        foo();
#endif
```

SPL 映像的构建可以通过 Kconfig 中的 CONFIG_SPL 选项来启用。

由于 SPL 图像通常具有不同的文本库，因此必须通过定义 CONFIG_SPL_TEXT_BASE 进行配置。 必须使用 CONFIG_SPL_LDSCRIPT 定义链接器脚本。

为了支持 SPL 二进制文件中的通用 U-Boot 库和驱动程序，可以选择定义 CONFIG_SPL_XXX_SUPPORT。 目前支持以下选项：

- CONFIG_SPL_LIBCOMMON_SUPPORT (common/libcommon.o)
- CONFIG_SPL_LIBDISK_SUPPORT (disk/libdisk.o)
- CONFIG_SPL_I2C (drivers/i2c/libi2c.o)
- CONFIG_SPL_GPIO (drivers/gpio/libgpio.o)
- CONFIG_SPL_MMC (drivers/mmc/libmmc.o)
- CONFIG_SPL_SERIAL (drivers/serial/libserial.o)
- CONFIG_SPL_SPI_FLASH_SUPPORT (drivers/mtd/spi/libspi_flash.o)
- CONFIG_SPL_SPI (drivers/spi/libspi.o)
- CONFIG_SPL_FS_FAT (fs/fat/libfat.o)
- CONFIG_SPL_FS_EXT4
- CONFIG_SPL_LIBGENERIC_SUPPORT (lib/libgeneric.o)
- CONFIG_SPL_POWER (drivers/power/libpower.o)
- CONFIG_SPL_NAND_SUPPORT (drivers/mtd/nand/raw/libnand.o)
- CONFIG_SPL_DRIVERS_MISC (drivers/misc)
- CONFIG_SPL_DMA (drivers/dma/libdma.o)
- CONFIG_SPL_POST_MEM_SUPPORT (post/drivers/memory.o)
- CONFIG_SPL_NAND_LOAD (drivers/mtd/nand/raw/nand_spl_load.o)
- CONFIG_SPL_SPI_LOAD (drivers/mtd/spi/spi_spl_load.o)
- CONFIG_SPL_RAM_DEVICE (common/spl/spl.c)
- CONFIG_SPL_WATCHDOG (drivers/watchdog/libwatchdog.o)


# 3. Adding SPL-specific code

要检查某个功能是否已启用，请使用 CONFIG_IS_ENABLED()：

```bash
if (CONFIG_IS_ENABLED(CLK))
    ...
```

这会检查主构建的 CONFIG_CLK、SPL 构建的 CONFIG_SPL_CLK、TPL 构建的 CONFIG_TPL_CLK 等。


# 4. U-Boot Boot Phases

U-Boot 经历以下启动阶段，其中 TPL、VPL、SPL 是可选的。 虽然许多主板使用 SPL，但很少使用 TPL。

**TPL**

非常早的初始化，尽可能小。 这将加载 SPL（或 VPL，如果启用）。

**VPL**

可选的验证步骤，如果启用了 A/B 验证启动，则可以选择多个 SPL 二进制文件之一。 VPL 逻辑的实施正在进行中。 现在它只是启动到 SPL。

**SPL**

辅助程序加载器。 设置 SDRAM 并正确加载 U-Boot。 它还可能加载其他固件组件。

**U-Boot**

U-Boot 本身，包含命令行和启动逻辑。

U-Boot SPL 的其他用途包括：

- 启动 ARM 可信固件的 BL31，将主 U-Boot 调用为 BL33；
- 启动 EDK II;
- 启动 Linux kernel;
- 启动调用主 U-Boot 的 RISC-V OpenSBI。


# 5. Checking the boot phase

使用 spl_phase() 查找当前的 U-Boot 阶段，例如 PHASE_SPL。 您还可以找到上一个和下一个阶段并获取阶段名称。


# 6. Device tree

U-Boot 设备树在构建过程中由 fdtgrep 工具过滤，以生成 SPL (spl/u-boot-spl.dtb) 中使用的更小的设备树，其中：

- 强制节点（/alias、/chosen、/config）；
- 具有一个预重定位属性的节点：“bootph-all”或“bootph-pre-ram”。

fdtgrep 还用于删除：

- CONFIG_OF_SPL_REMOVE_PROPS 中定义的属性；
- 所有预重定位属性（“bootph-all”、“bootph-pre-ram”（SPL）、“bootph-pre-sram”（TPL）和“bootph-verify”（TPL））。

SPL 设备树中剩余的所有节点均已绑定（请参阅 doc/driver-model/design.rst）。

注意：U-Boot 于 2023 年迁移到 u-boot、dm-* 标签的新架构。请更新以使用新的 bootph-* 标签，如 doc/device-tree-bindings/bootph.yaml 绑定文件中所述 。


# 7. Debugging

当使用 DEBUG 设置构建 SPL 时，您可能还需要设置 CONFIG_PANIC_HANG，因为在大多数情况下 do_reset 未在 SPL 中定义。


# 8. Estimating stack usage

使用 gcc 4.6（及更高版本）和 GNU cflow 的使用，可以估计 SPL 运行序列中各个点的堆栈使用情况。 gcc 的 -fstack-usage 选项将生成“.su”文件（例如 arch/arm/cpu/armv7/syslib.su），该文件将提供堆栈使用信息，cflow 可以构建程序流程。

必须有 gcc 4.6 或更高版本，支持 -fstack-usage：

1. 正常构建；
2. 执行以下shell命令生成SPL中使用的C文件列表；
3. `find spl -name ‘*.su’ | sed -e ‘s:^spl/::’ -e ‘s:[.]su$:.c:’ > used-spl.list`;
4. 执行 cflow: $ cflow –main=board_init_r $(cat used-spl.list) 2>&1 | $PAGER.

cflow 会发出许多警告，因为它不会解析配置文件并根据 #ifdef 选择函数。 相反，解析“.i”文件会带来另一组令人头痛的问题。 然而，这些警告对于理解流程通常并不重要。
