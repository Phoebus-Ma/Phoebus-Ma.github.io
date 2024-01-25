
# NVM XIP Block Storage Emulation Driver

# 1. Summary

具有可寻址存储器的非易失性存储器设备（例如：QSPI NOR 闪存）可用于满足块存储需求（例如：解析原始 QSPI NOR 闪存中的 GPT 布局）。

NVMXIP Uclass 提供此功能，可用于任何 64 位平台。

NVMXIP Uclass 提供以下驱动程序：

nvmxip-blk 块设备驱动:

&emsp;允许从 XIP 闪存读取的通用块驱动程序。 该驱动程序属于 UCLASS_BLK。 drivers/mtd/nvmxip/nvmxip.c实现的驱动。

nvmxip Uclass 驱动:

&emsp;当设备在DT中描述并与UCLASS_NVMXIP关联时，Uclass创建一个块设备并将其与nvmxip-blk绑定。 drivers/mtd/nvmxip/nvmxip-uclass.c实现的Uclass驱动。

nvmxip_qspi driver :

&emsp;使用 DT 探测的驱动程序是 blk#<id> 设备的父设备。 nvmxip_qspi 可以被其他平台重用。 如果平台在使用闪存之前有要应用的自定义设置，则平台可以提供自己的属于 UCLASS_NVMXIP 的父驱动程序并重用 nvmxip-blk。 除了平台自定义设置之外，还可以像 nvmxip_qspi 一样实现自定义驱动程序。 nvmxip_qspi 驱动程序属于 UCLASS_NVMXIP。 驱动实现由drivers/mtd/nvmxip/nvmxip_qspi.c。

例如，如果我们在 DT 中描述了两个 NVMXIP 设备，则设备层次结构如下：

```bash
=> dm tree

     Class     Index  Probed  Driver                Name
 -----------------------------------------------------------
 ...
  nvmxip        0  [ + ]   nvmxip_qspi           |-- nvmxip-qspi1@08000000
  blk           3  [ + ]   nvmxip-blk                    |   `-- nvmxip-qspi1@08000000.blk#1
  nvmxip        1  [ + ]   nvmxip_qspi           |-- nvmxip-qspi2@08200000
  blk           4  [ + ]   nvmxip-blk                    |   `-- nvmxip-qspi2@08200000.blk#2
```

该实现是通用的，可以被不同平台使用。


# 2. Supported hardware

任何支持 readq() 的平台。


# 3. Configuration

**config NVMXIP**

此选项允许在直接访问非易失性存储器 XIP 闪存设备之上模拟块存储设备。 该支持提供了读操作。 此选项提供块存储驱动程序 nvmxip-blk 来处理读取操作。 该驱动程序与硬件无关，可以同时支持多个闪存设备。

**config NVMXIP_QSPI**

此选项允许在 QSPI XIP 闪存之上模拟块存储设备。 任何需要模拟一个或多个 QSPI XIP 闪存设备的平台都可以打开此选项以启用该功能。 自动选择 NVMXIP 配置。 需要在访问闪存之前添加自定义处理的平台可以编写自己的驱动程序（除了自定义设置之外，与 nvmxip_qspi 相同）。


# 4. Device Tree nodes

通过DT节点描述，可以同时使用多个QSPI XIP闪存设备。

请参阅 DT 绑定的文档：

doc/device-tree-bindings/nvmxip/nvmxip_qspi.txt


# 5. Contributors

- Abdellatif El Khlifi <abdellatif.elkhlifi@arm.com>
