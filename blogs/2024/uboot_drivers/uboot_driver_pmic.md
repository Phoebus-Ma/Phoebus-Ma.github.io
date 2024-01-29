
# PMIC framework based on Driver Model

# 1. Introduction

这是对驱动程序模型多 uclass PMIC IC 支持的介绍。 目前它基于两种 uclass 类型：

**UCLASS_PMIC:**

PMIC I/O 的基本 uclass 类型，提供通用的读/写接口。

**UCLASS_REGULATOR:**

用于特定 PMIC 功能的附加 uclass 类型，即电压/电流调节器。

新文件：

**UCLASS_PMIC:**

- drivers/power/pmic/pmic-uclass.c
- include/power/pmic.h

**UCLASS_REGULATOR:**

- drivers/power/regulator/regulator-uclass.c
- include/power/regulator.h

Commands: - common/cmd_pmic.c - common/cmd_regulator.c


# 2. How doees it work

电源管理集成电路（PMIC）用于嵌入式系统，提供稳定、精确和特定的电压电源，并具有过压和热保护电路。

单个PMIC可以通过单个或多个接口提供各种功能，如下例所示：

```bash
-- SoC
 |
 |            ______________________________________
 | BUS 0     |       Multi interface PMIC IC        |--> LDO out 1
 | e.g.I2C0  |                                      |--> LDO out N
 |-----------|---- PMIC device 0 (READ/WRITE ops)   |
 | or SPI0   |    |_ REGULATOR device (ldo/... ops) |--> BUCK out 1
 |           |    |_ CHARGER device (charger ops)   |--> BUCK out M
 |           |    |_ MUIC device (microUSB con ops) |
 | BUS 1     |    |_ ...                            |---> BATTERY
 | e.g.I2C1  |                                      |
 |-----------|---- PMIC device 1 (READ/WRITE ops)   |---> USB in 1
 . or SPI1   |    |_ RTC device (rtc ops)           |---> USB in 2
 .           |______________________________________|---> USB out
 .
```

由于 U-Boot 为 I2C 和 SPI 总线驱动程序提供驱动程序模型功能，因此 PMIC 设备也应该支持此功能。 通过PMIC和调节器API，PMIC驱动程序可以简单地提供通用功能，以实现多接口和多实例设备支持。

基本设计假设：

**Common I/O API:**

UCLASS_PMIC。 对于多功能 PMIC 设备，它可以用作每个 IC 接口的父 I/O 设备。 然后，每个孩子使用相同的 dev 进行读/写。

**Common regulator API:**

UCLASS_REGULATOR。 用于驱动调节器属性、自动设置功能或命令行界面，基于内核式调节器设备树约束。

对于简单的实现，不需要调节器驱动程序，因此代码可以直接使用 pmic 读/写。


# 3. Pmic uclass

基本信息：

- Uclass: ‘UCLASS_PMIC’
- Header: ‘include/power/pmic.h’
- Core: ‘drivers/power/pmic/pmic-uclass.c’ (config ‘CONFIG_DM_PMIC’)
- Command: ‘common/cmd_pmic.c’ (config ‘CONFIG_CMD_PMIC’)
- Example: ‘drivers/power/pmic/max77686.c’

详细的API说明请参考头文件。

作为pmic驱动程序的示例，请参考MAX77686驱动程序。

请注意驱动程序的bind()方法。 正是函数调用：‘pmic_bind_children()’，它用于通过使用调节器节点数组、兼容前缀来绑定调节器。

“PMIC”； 命令还支持新的 API。 所以可以通过添加CONFIG_CMD_PMIC来启用pmic命令。 新的 pmic 命令允许： - 列出 pmic 设备 - 选择当前设备（如 mmc 命令） - 读取或写入 pmic 寄存器 - 转储所有 pmic 寄存器

该命令只能使用 UCLASS_PMIC 设备，因为该 uclass 仅设计用于 pmic I/O 操作。

欲了解更多信息，请参阅核心文件。


# 4. Regulator uclass

基本信息：

- Uclass: ‘UCLASS_REGULATOR’
- Header: ‘include/power/regulator.h’
- Core: ‘drivers/power/regulator/regulator-uclass.c’ (config ‘CONFIG_DM_REGULATOR’)
- Binding: ‘doc/device-tree-bindings/regulator/regulator.txt’
- Command: ‘common/cmd_regulator.c’ (config ‘CONFIG_CMD_REGULATOR’)
- Example: ‘drivers/power/regulator/max77686.c’ ‘drivers/power/pmic/max77686.c’ (required I/O driver for the above)
- Example: ‘drivers/power/regulator/fixed.c’ (config ‘CONFIG_DM_REGULATOR_FIXED’)

详细的API说明请参考头文件。

对于示例稳压器驱动程序，请参考 MAX77686 稳压器驱动程序，但该驱动程序必须在没有 pmic 示例驱动程序的情况下才能运行，该驱动程序为 MAX77686 稳压器提供了 I/O 接口。

第二个例子是通用的固定电压/电流调节器。

“regulator”命令也支持新的 API。 该命令允许： - 列出调节器设备 - 选择当前设备（如 mmc 命令） - 执行所有调节器特定的操作。

欲了解更多信息，请参阅命令文件。
