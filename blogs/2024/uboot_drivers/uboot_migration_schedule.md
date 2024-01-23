
# Migration Schedule

自 2014 年推出以来，U-Boot 一直在迁移到新的驱动程序模型。此文件描述了弃用预驱动程序模型功能的时间表。

**CONFIG_DM**

- 状态: 进行中
- 截止日期: 2020.01

从 2010.01 版本开始，将为所有板启用 CONFIG_DM。 这与 CONFIG_DM_SPL 和 CONFIG_DM_TPL 无关。 这些配置项的转换日期仍需要定义。

**CONFIG_DM_MMC**

- 状态: 进行中
- 截止日期: 2019.04

子系统本身已转换，维护人员应及时提交补丁，切换为使用 CONFIG_DM_MMC 和其他基本驱动程序模型选项，以便包含在 2019.04 重新发布中。

**CONFIG_DM_USB**

- 状态: 进行中
- 截止日期: 2019.07

子系统本身已与许多主机控制器一起进行了转换，维护人员应及时提交补丁切换为使用 CONFIG_DM_USB 和其他基本驱动程序模型选项，以便包含在 2019.07 重新发布中。

**CONFIG_SATA**

- 状态: 进行中
- 截止日期: 2019.07

子系统本身已与许多主机控制器一起进行了转换，维护人员应及时提交补丁切换为使用 CONFIG_AHCI 和其他基本驱动程序模型选项，以便包含在 2019.07 重新发布中。

**CONFIG_BLK**

- 状态: 进行中
- 截止日期: 2019.07

为了配合维护人员将其块设备使用迁移到适当的 DM 驱动程序，还需要设置 CONFIG_BLK。 这里的最终截止日期与各个块子系统迁移的最终截止日期一致。 此时，我们将能够使用 CONFIG_PARTITIONS 和 CONFIG_SPL_LEGACY_BLOCK 审核和更正 Kconfig 中的逻辑，并根据需要使用 CONFIG_BLK / CONFIG_SPL_BLK。

**CONFIG_DM_SPI / CONFIG_DM_SPI_FLASH**

主板维护人员应在截止日期前提交启用 DM_SPI 和 DM_SPI_FLASH 的补丁以推动迁移。

部分转换:

```bash
drivers/spi/fsl_espi.c
drivers/spi/mxc_spi.c
drivers/spi/sh_qspi.c
```

- 状态: 进行中
- 截止日期: 2019.07

**CONFIG_DM_VIDEO**

截止日期: 2019.07

视频子系统自 2016 年初以来就支持驱动程序模型。维护者应及时提交切换为使用 CONFIG_VIDEO 和其他基本驱动程序模型选项的补丁，以便包含在 2019.07 版本中。

**CONFIG_DM_ETH**

截止日期: 2020.07

网络子系统自 2015 年初以来就支持该驱动程序模型。维护者应及时提交切换为使用 CONFIG_DM_ETH 和其他基本驱动程序模型选项的补丁，以便包含在 2020.07 版本中。

**CONFIG_DM_I2C**

截止日期: 2021.10

I2C 子系统自 2015 年初以来就支持该驱动程序模型。维护者应及时提交切换为使用 CONFIG_DM_I2C 和其他基本驱动程序模型选项的补丁，以便包含在 2021.10 版本中。

**CFG_SYS_TIMER_RATE and CFG_SYS_TIMER_COUNTER**

截止日期: 2023.01

这些是已被驱动程序模型取代的旧选项。 维护人员应及时提交补丁，切换为使用 CONFIG_TIMER 和其他基本驱动程序模型选项，以便包含在 2022.10 版本中。

只有一种方法可以实现，除非您想支持 bootstage，在这种情况下您还需要一个早期计时器。 有关示例驱动程序，请参阅 sandbox_timer.c 和 rockchip_timer.c

**CONFIG_DM_SERIAL**

截止日期: 2023.04

串行子系统自 2014 年底以来一直支持该驱动程序模型。维护者应及时提交切换为使用 CONFIG_DM_SERIAL 和其他基本驱动程序模型选项的补丁，以便包含在 2022.10 版本中。
