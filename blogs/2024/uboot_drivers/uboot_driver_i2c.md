
# How to port an I2C driver to driver model

截至 2016 年 11 月，超过一半的 I2C 驱动程序已被转换。这些驱动程序仍然存在：

- adi_i2c
- davinci_i2c
- fti2c010
- ihs_i2c
- kona_i2c
- lpc32xx_i2c
- pca9564_i2c
- ppc4xx_i2c
- rcar_i2c
- sh_i2c
- soft_i2c
- zynq_i2c

这项工作的截止日期是 2017 年 6 月。如果没有人主动转换这些内容，那么在某个时候可能会出现一个补丁来删除它们！

以下是将 I2C 驱动程序转换为驱动程序模型的建议方法。 请随时根据您的想法和建议更新此文件。

- #ifdef 输出所有您自己的 I2C 驱动程序代码 (#if !CONFIG_IS_ENABLED(DM_I2C))
- 为您的主板、供应商或架构定义 CONFIG_DM_I2C
- 如果主板尚未使用驱动程序模型，则还需要 CONFIG_DM
- 然后您的板应该会构建，但不会完全工作，因为没有 I2C 驱动程序
- 在末尾添加 U_BOOT_DRIVER 部分（例如复制 tegra_i2c.c）
- 为驱动程序数据添加私有结构 - 避免使用静态变量
- 实现每个驱动程序方法，也许通过调用旧方法
- 您可能需要调整函数参数，以便新旧实现可以共享大部分现有代码
- 如果转换驱动程序的所有现有用户，请删除预驱动程序模型代码

就补丁而言，转换系列通常具有以下补丁： - 清理/准备转换驱动程序 - 添加驱动程序模型代码 - 转换至少一个现有板以使用驱动程序模型序列 - （如果没有剩余板不使用驱动程序 模型）删除旧代码

这可能也是让您的主板也使用设备树的好时机。 主要涉及以下步骤：

- 定义 CONFIG_OF_CONTROL 和 CONFIG_OF_SEPARATE
- 将设备树文件添加到 arch/<arch>/dts
- 在那里更新 Makefile
- 将 stdout-path 添加到 /chosen 设备树节点（如果尚不存在）
- 构建并获取 u-boot-dtb.bin 以便您可以测试它
- 您的驱动程序现在可以使用设备树
- 对于SPL中的设备树，定义CONFIG_SPL_OF_CONTROL
