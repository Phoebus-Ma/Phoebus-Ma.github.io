
# U-Boot add TI board

# 1. Overview

SPL 和 U-Boot 共享共同的代码库. 将自定义板添加到 U-Boot 时, 建议从在某些方面类似于自定义硬件设计的 TI EVM 开始, 例如在 DDR (内存类型和大小) 、MMC (使用哪个模块) 领域 、是否连接了 eMMC 或者是否正在使用 SD 卡) 、网络设置 (连接的 PHY 的类型和数量) 以及 UART 设置 (哪个 UART 旨在用于控制台目的？) . 就本操作方法文档而言, 我们将该 EVM 称为"Originating TI EVM".

对于定制 AM437x 板, 许多人的设计基于 [AM437x 通用 EVM](https://www.ti.com/tool/TMDSEVM437X) 或 [AM437x 入门套件](https://www.ti.com/tool/TMDXSK437X). 对于这里的讨论, 我们假设 AM437x 通用 EVM 作为我们的起点, 但是相同的概念也适用于从不同的基础平台开始的板端口. 新板将像其他现有板集成到 U-Boot 中一样干净地集成.


# 2. 将对新板的支持集成到 U-Boot 树中

添加支持基本上是通过克隆、剥离和扁平化 TI EVM 板支持来完成的, 同时将生成的文件集成到 U-Boot 构建流程中, 从而生成自定义 defconfig、自定义板特定头文件、自定义顶级文件 宣传新板名称的设备树文件, 以及用于平台设置 (例如 DDR 和 pinmux) 的一组自定义板文件. 一旦建立了这种扁平化基线并验证了其可以成功构建并在原始平台上运行, 下一步就是执行实际的定制工作.


## 2.1. 通过克隆和扁平化 TI EVM 支持创建初始基线

这些步骤包括在板端口过程中需要替换的各种占位符, 如下所示:

| Placeholder | Usage                                                                                                                    |
| ----------- | ------------------------------------------------------------------------------------------------------------------------ |
| <MYBOARD>   | 用大写字母标识您的自定义板的名称. 它将用作与您的主板关联的新 U-Boot CONFIG 符号的名称, 允许自定义各种构建和运行时方面.   |
| <myboard>   | 以小写字母标识您的自定义板的名称. 用于在 U-Boot 源树层次结构中建立自定义板平台文件, 并作为板特定文件名的一部分等.        |
| <mycompany> | 贵公司的名称. 用于在 U-Boot 源树层次结构中建立一个文件夹, 其中包含 <myboard> 的板特定文件作为板端口的一部分.             |

创建初始基线的步骤:

1. 建立自定义板特定的 CONFIG 选项, 可用于识别自定义硬件并相应地直接代码和构建流程.

    - 将 arch/arm/mach-omap2/am33xx/Kconfig 中的整个配置 TARGET_AM43XX_EVM 部分克隆到名为 config TARGET_AM43XX_<MYBOARD> 的新部分.

    - 将新克隆部分中的配置 TARGET_AM43XX_<MYBOARD> 选项描述从 bool "Support am43xx_evm"更新为 bool "Support am43xx_<myboard>".

    - 同样, 更新新克隆部分的帮助描述, 以反映它适用于自定义板.

    - 从新部分中删除选择的 TI_I2C_BOARD_DETECT 条目. 在大多数情况下, 我们不需要或不希望自定义板具有此功能, 因为我们将围绕我们的特定平台定制解决方案, 所以让我们立即删除它.

    - 将源" board/<mycompany>/<myboard>/Kconfig "添加到包含位于 arch/arm/mach-omap2/Kconfig 中的源位置的部分.

2. 将板文件复制并更新到新文件夹.

    - 将所有文件从 board/ti/am43xx/ 复制到名为 board/<mycompany>/<myboard> 的新文件夹.

    - 根据需要删除 (或更新) README 和 MAINTAINERS 文件 (如果存在) .

    - 从 board.c 中删除 #include "../common/board_detect.h".

    - 从 board.c 中删除 #ifdef TI_I2C_BOARD_DETECT 和 #endif 之间的代码.

    - 返工并删除 board.c、board.h 和 mux.c 中所有与板检测相关的代码, 仅保留并压平支持自定义板所基于的实际平台所需的部分. 原始的 board.c、board.h 和 mux.c 文件的编写方式使得它们支持多种不同的板以及给定板的不同版本, 所有这些都具有自己的板特定功能集, 包括但不限于 DDR 配置、pinmux、器件操作点/速度以及其他外设初始化代码. 通过遵循由于各种 board_is_\*() 函数调用而执行的代码路径来进行返工. 例如, 要扁平化平台代码并将其定制为当前发布的 AM437x GP EVM 硬件版本, 假设 board_is_evm() 评估为 true, 所有其他 board_is_\*() 函数评估为 false, 并相应地简化平台代码.

    - 编辑 board/<mycompany>/<myboard>/Kconfig, 如下所示.

        - 将 TARGET_AM43XX_EVM 更新为 TARGET_AM43XX_<MYBOARD>.

        - 将配置 SYS_BOARD 下的默认"am43xx"更新为默认"<myboard>".

        - 将配置 SYS_VENDOR 下的默认"ti"更新为默认"<mycompany>".

        - 将配置 SYS_CONFIG_NAME 下的默认"am43xx_evm"更新为默认"am43xx_<myboard>".

3. 复制并更新板特定的头文件.

    - 将 include/configs/am43xx_evm.h 复制到新文件 include/configs/am43xx_<myboard>.h.

    - 删除#define CONFIG_ENV_EEPROM_IS_ON_I2C、#define CONFIG_SYS_I2C_EEPROM_ADDR、#define CONFIG_SYS_I2C_EEPROM_ADDR_LEN 定义, 因为我们通常不想使用外部 EEPROM 进行配置存储, 而是希望使用启动介质.

    - 将通过 CONFIG_EXTRA_ENV_SETTINGS 进行的 findfdt U-Boot 环境变量定义更新为硬编码用于引导 Linux 内核的板特定 DTB 文件"findfdt=setenv fdtfile am437x-<myboard>.dtb\0".

    - 请记住, 当尝试使用此配置引导系统时, 在这种情况下, 您必须提供名为 am437x-<myboard>.dtb 的内核 DTB 文件. 不提供此文件可能会导致基于 ENV 的 U-Boot 加载期间出现无提示故障, 并且内核无法启动.

4. 复制并更新顶级设备树文件并集成到构建过程中.

    - 将 arch/arm/dts/am437x-gp-evm.dts 复制到 arch/arm/dts/am437x-<myboard>.dts.

    - 编辑 arch/arm/dts/am437x-<myboard>.dts 并使用自定义的、特定于板的字符串更新模型节点.

    - 编辑 arch/arm/dts/am437x-<myboard>.dts 以包含隐式包含的 am437x-gp-evm-u-boot.dtsi 文件中的内容.

    **Note**

    许多 TI 板还附带 U-Boot 特定设备树包含文件, 其基本名称与主设备树文件相同, 但以 -u-boot.dtsi 结尾, 该文件由 U-Boot 的设备树构建过程隐式包含. 例如, 在 arch/arm/dts/am437x-gp-evm.dts 的情况下, 隐式包含的文件称为 arch/arm/dts/am437x-gp-evm-u-boot.dtsi. 建议简单地从此类包含文件中获取内容并将其添加到板的主设备树文件中, 从而提供更简单且更易于管理的活动配置视图.

    - 编辑 arch/arm/dts/Makefile 将 am437x-<myboard>.dtb 添加到 dtb-$(CONFIG_AM43XX) 构建目标.

5. 复制并更新 U-Boot defconfig 文件.

    - 将 configs/am43xx_evm_defconfig 复制到 configs/am43xx_<myboard>_defconfig.

    - 按如下方式编辑 configs/am43xx_<myboard>_defconfig.

        - 在 CONFIG_AM43XX=y 下面添加 CONFIG_TARGET_AM43XX_<MYBOARD>=y.

        - 更新 CONFIG_DEFAULT_DEVICE_TREE="am437x-<myboard>".

        - 删除 CONFIG_OF_LIST 条目.

此时, 初始基线已完成, 我们应该有一个自定义板, 该板将在板端口所基于的平台 (TI EVM) 上运行. 现在要完成此步骤, 请执行以下操作:

1. 使用通常的流程构建我们的自定义板端口, 首先构建新创建的 defconfig 文件, 然后执行 SPL 和 U-Boot 的实际构建. 修复您可能遇到的任何构建错误并重新构建, 直到构建干净地执行, 没有任何构建警告.确保 [toolchain path](https://software-dl.ti.com/processor-sdk-linux/esd/docs/06_03_00_106/AM437X/linux/Overview/GCC_ToolChain.html) 已正确设置.

```bash
$ make ARCH=arm CROSS_COMPILE='arm-linux-gnueabihf-' mrproper
$ make ARCH=arm CROSS_COMPILE='arm-linux-gnueabihf-' <device>_<myboard>_defconfig
$ make ARCH=arm CROSS_COMPILE='arm-linux-gnueabihf-'
```

2. 将新添加的文件检入 Git 以建立已知良好的检查点.


## 2.2. 定制新建立的基线以支持实际的目标平台

下一步, 我们希望创建一个可用于启动开发板的初始尝试的最小配置. 一旦基础知识发挥作用, 它将成为逐步构建设置的良好基础, 以完全支持定制板的所有所需功能. 请注意, 在第一次尝试中一切顺利的机会相当低, 因此通常会采用迭代方法, 包括进行更改、确保它们构建、将它们签入 Git 源代码树 (以便可以跟踪、理解更改并记录更改) . 如果需要更容易地恢复) , 并在硬件上对其进行测试, 直到实现完全工作且功能齐全的板端口.

U-Boot 使用相同的代码库为 SPL 和 U-Boot 本身构建映像. 当您使用不同的源文件和头文件时, 请注意某些语句如何包装在 #ifdef CONFIG_SPL ... #endif 语句预处理器宏中, 这意味着包含的部分仅适用并在构建 SPL 时进行构建. 同样, 当您在 [Kconfig](https://gitlab.denx.de/u-boot/u-boot/-/blob/master/doc/README.kconfig) 中遇到以 CONFIG_SPL_\* 开头的配置符号时 工具 (意思是 make [...] menuconfig) , 在 U-Boot 树中的实际 Kconfig 文件 (在这种情况下省略前导 CONFIG_ 前缀) 中, 或在任何源文件和 Makefile 文件中, 这意味着某个功能或 部分仅激活或适用于 SPL.

**Note**

使用 Kconfig 工具中的搜索功能可以快速找到下面讨论的各种 CONFIG_\* 选项. 按"/"键可以激活搜索.

- 如果您的 DDR 设置 (设备、时钟速度等) 与原始平台 (EVM) 不同, 则端口 DDR 配置.

    - DDR 时序和配置数据在 board.c 文件中设置.

    - 请按照 [AM43xx EMIF 工具应用报告](https://www.ti.com/lit/pdf/sprac70) 及其相关的 [配置工具](https://www.ti.com/lit/zip/sprac70) 详细信息. 此应用报告还包括对 DDR 启动有用的信息.

    - 如果需要任何其他自定义步骤 (例如添加额外定义) , 请尝试限制对 board/<mycompany>/<myboard> 的自定义板特定文件夹中的文件所做的任何更改.

    - 当 DDR 时序和参数设置正确时, U-Boot 将在运行时使用 get_ram_size() 在架构文件中自动检测、验证和配置 DDR 大小.

- 为定制板建立初始最小 pinmux 设置.

    - 需要最小的 pinmux 设置来避免运行专门用于仅在自定义板上运行的 TI EVM 的配置时可能发生的任何潜在信号冲突.

    - U-Boot 中执行的 Pinmux 通常仅限于直接参与启动过程的外设 (例如 GPMC、DDR、MMC、SPI 等) 、用于 PMIC 连接的 I2C 模块以及控制台 UART.

    - 对于基于 TI EVM 的 defconfig, pinmux 是通过 mux.c 板文件执行的, 这可以通过 .config 文件中未设置的 CONFIG_PINCTRL、CONFIG_PINCTRL_FULL 和 CONFIG_PINCTRL_SINGLE 选项进行验证.

    - 使用自定义板所需的 pinmux 设置更新 enable_board_pin_mux() 函数. 对于 TI EVM, 此文件通常使用不同的 board_is_\*() 函数来激活不同板的不同 pinmux 设置, 但我们应该更早地扁平化该功能. 现在我们需要删除所有不适用于我们的自定义板的内容, 并添加/更新我们需要的项目, 以实现允许通过进行适当的configure_module_pin_mux()调用来证明正确 (可能更新) 的数据结构来启动的最小环境.

    - 为了快速导出所需的 pinmux 设置, 有两种可能的路径:

        1. 使用 TI 提供的 [Pin MUX Utility](https://www.ti.com/tool/PINMUXTOOL), 该实用程序有在云中运行的版本以及可以手动安装的版本. 请注意, 由于 pinmux 是通过 mux.c 板文件执行的, 因此需要通过建模并与现有的 struct module_pin_mux 定义进行比较, 将 Pin MUX 实用程序中显示的 pinmux 设置转换为 enable_board_pin_mux() 所期望的内容.

        2. 从使用相同 SoC 的其他板的板文件中搜索并使用 struct module_pin_mux 定义.

**Note**

设备树文件中进行的任何 pinmux 设置均不适用且不被 U-Boot 使用. 之所以存在这些文件, 是因为 U-Boot 设备树文件通常是 Linux 设备树文件的副本, 因为这可以简化保持这些文件同步的过程. 但这并不意味着 pinmux 等所有条目都适用于 U-Boot.

- 更新 PMIC 配置.

    - TI AMxxx SoC 通常由通过 I2C 接口连接的外部电源管理 IC (PMIC) 供电. PMIC 的工作之一是根据满足数据表要求的所需操作性能点 (OPP) 提供 VDD_MPU 和 VDD_CORE 电压.

    - PMIC 是通过 board.c 文件内的 scale_vcores() 函数进行配置的, 该函数在设置 SoC 的 PLL 之前由架构驱动程序调用.

    - 实际的 PMIC 配置取决于为给定板配置的时钟频率 (请参阅下一步) . 它还可能需要依赖于芯片版本, 因此请仔细检查原始 TI EVM 的代码, 该代码主要基于 board_is_\*() 调用.

    - 通过设备特定头文件 include/configs/<myboard>.h 中的 #define CONFIG_POWER_\* 启用所需的驱动程序, 配置要使用的 PMIC 驱动程序.

    - 仅应启用板上实际使用的 PMIC 驱动程序. 有关可用驱动程序的列表, 请尝试搜索适当的 CONFIG 选项, 如下所示:git grep 'CONFIG_POWER_TPS' drivers/power/pmic.

- 更新 SoC 时钟配置.

    - TI AMxxx SoC 提供不同的速度等级, 每个速度等级都支持与特定 OPP 相关的最大工作频率.

    - board.c 文件内的 get_dpll_mpu_params() 函数的实现负责确定允许的最大工作频率, 然后架构驱动程序使用该频率来设置设备的 PLL.

    - 不要尝试将设备工作频率增加到超出 eFuse 读数允许的范围, 但是在某些情况下, 不以最高 OPP 运行可能会有所帮助, 在这种情况下, 可以更新此函数以返回不同的 struct dpll_params \* 对象 适应这一点.

- 自定义控制台 UART 设置.

    - UART pinmus 是通过 board.c 中的 set_uart_mux_conf() 完成的, 为 mux.c 中的特定 UART 接口调用 pinmux 配置函数enable_uart\*\_pin_mux. 更新调用以及enable_uart\*\_pin_mux 函数本身, 以根据新UART 接口的需要使用更新的pinmux 结构.

    - 更新 arch/arm/dts/am437x-<myboard>.dts 设备树文件, 如下所示:

        - 使用新 UART 的新句柄更新 stdout-path 属性.

        - 使用正确的 pinmux 参考覆盖相应 UART 的设备树节点, 并确保将其设置为 status ="okay".

    - 将板特定头文件 include/configs/<myboard>.h 中 CONFIG_EXTRA_ENV_SETTINGS 定义的 `console=` 变量部分更新为用于 Linux 内核启动的所需 UART. 将此 ENV 变量设置为 ttyS0,115200n8 (对于 UART0) , ttyS1,115200n8 (对于 UART1) , 依此类推.

- 尽早设置 (调试) UART.

    - 由于驱动程序和其他依赖性, 主控制台 UART 仅在 SPL 启动过程中启动, 因此很难启动和检测早期启动代码, 包括启动外设配置、PMIC 设置、DDR 设置、板 ID/EEPROM 相关代码 (按照前面的步骤, 现在应该已经删除了) , 除此之外, 还无法执行基本的 printf() 风格的检测.

    - 为了使板端口和启动更容易, 强烈建议至少在开发和启动工作期间打开 U-Boot 的调试 UART 功能, 这是通过配置和硬编码各种 UART 外设参数来完成的. 这样做将在 SPL 流程早期的 Early_system_init() 执行期间启用 UART, 作为 SPL board_init_f() 函数的一部分.

    - 通常, 调试 UART 配置为与主控制台 UART 匹配 (例如, 两者都配置为使用 UART0) 以实现单个控制台输出.

    - 请注意, 要使调试 UART 功能正常工作, board.c 函数中的 set_uart_mux_conf() 必须按照之前的步骤进行更新, 以设置调试 UART 所需的 pinmux.

    - 要使用 UART0 模块启用调试 UART 功能, 请使用 Kconfig 工具配置以下参数. 要使用任何其他 UART 模块, 请将 CONFIG_DEBUG_UART_BASE 参数调整为适合该 UART 的基地址, 该基地址可以在 TRM (外设内存映射部分) 中找到, 或者直接从设备特定的设备树中获取, 包括文件 uart\*: { } 定义.

```bash
CONFIG_DEBUG_UART_BASE=0x44e09000
CONFIG_DEBUG_UART_CLOCK=48000000
CONFIG_DEBUG_UART=y
CONFIG_DEBUG_UART_OMAP=y
CONFIG_DEBUG_UART_SHIFT=2
CONFIG_DEBUG_UART_ANNOUNCE=y
```

**Note**

早期 (调试) UART 的建议设置包括 CONFIG_DEBUG_UART_ANNOUNCE=y, 这会在大多数/任何 SoC 和外设配置发生之前, 在 SPL 启动流程的早期阶段输出 <debug_uart>. 启用此功能是在主板启动期间看到某种"早期生命迹象"的好方法, 让人们相信启动过程的基本原理正在工作, 即 ROM 启动加载程序从所需的启动介质加载 SPL 和 SPL 开始执行.

- 使用以下方法之一从 <device>-<myboard>.dts 设备树文件中停用除基本引导支持 (如 UART、MMC 等) 之外的所有外设初始化:

    1. 删除所有不适用的设备树节点, 包括它们的引用, 例如时钟、电源调节器和 pinmux 设置.

    2. 通过添加 status ="disabled"来停用不需要的外设; 属性到各自的节点.

- 根据需要通过 U-Boot 菜单配置停用可能不需要的功能.

    - 通过构建新的 defconfig 文件 make ARCH=arm CROSS_COMPILE='arm-linux-gnueabihf-' <device>_<myboard>\_defconfig 来建立新的工作 .config 文件.

    - 通过 make ARCH=arm CROSS_COMPILE='arm-linux-gnueabihf-' menuconfig 调用 Kconfig 工具来执行 U-Boot 配置. 这将使用正在执行的任何更改来更新存储在 U-Boot 目录根目录中的当前工作配置文件 .config.

    - 通过执行 make ARCH=arm CROSS_COMPILE='arm-linux-gnueabihf- savedefconfig 将当前 .config U-Boot 配置转换为更新的 defconfig 文件. 这将生成/更新一个名为 defconfig 的文件.

    - 将新生成的 defconfig 复制到 configs/<device>_<myboard>\_defconfig, 有效地覆盖/更新先前在克隆我们作为端口基础的现有板时建立的 defconfig 文件. 这样做还可以让我们看到自我们之前通过 git diff 提交检查点以来引入的更改.

- 通过自定义特定于板的头文件, 根据需要停用其他可能不必要的功能.

    - 一些 SPL 和 U-Boot 功能尚未完全迁移到 Kconfig, 并通过之前创建的板特定头文件 include/configs/<myboard>.h 进行控制/启用.

    - 请注意, 特定于板的头文件可能包括激活和配置功能的附加头文件. 确保理解包含层次结构. 要禁用或重新配置某些功能, 请考虑在包含通用头文件的自定义板特定头文件中使用 #undef 和 #define 预处理器语句的组合. 这样就可以避免对共享 U-Boot 文件进行任何修改.

- U-Boot 环境.

    - 默认的 U-Boot 环境很大程度上是通过特定于板的头文件 include/configs/<myboard>.h 中的 CONFIG_EXTRA_ENV_SETTINGS 定义来定义的, 并且应该根据特定的系统需求进一步定制.

    - 进行支持主引导模式所需的任何更改, 例如配置 bootpart= 以防止 MMC/SD 卡引导到正确的分区.

    - 虽然在环境中拥有额外的定义通常不会有什么坏处, 但人们应该利用这个机会删除与引导模式相关的任何不需要的定义, 以产生一个不那么混乱且更容易理解的整体 U-Boot 环境.

        - 删除不适用的 BOOT_TARGET_\* 定义.

        - 删除不适用的 DEFAULT_\*\_TI_ARGS 定义.

        - 删除不适用的 \*ARGS 定义.

    - 如果您需要在启动期间将任何额外参数传递给内核, 请将 optargs= ENV 定义添加到 CONFIG_EXTRA_ENV_SETTINGS 中.

    - 使用 Kconfig 工具禁用所有 CONFIG_ENV_IS_IN_\* 定义, 以从本质上禁用持久 ENV 存储.

现在进行自定义后, 生成的 SPL/U-Boot 不应再在原始 TI EVM 上运行, 而是在自定义硬件上运行. 我们现在应该能够在新板的硬件启动上下文中尝试自定义硬件平台的初始启动. 目标应该是进入 U-Boot 提示符.


## 2.3. 建立对目标平台的全面支持

一旦我们到达 U-Boot 提示符, 我们就可以专注于 (重新) 向 U-Boot 添加我们可能需要更全面地支持我们的自定义系统的任何功能, 然后转向引导 Linux 内核. 建议一一添加功能, 同时使用 Git 跟踪任何更改并在添加功能时在实际硬件上测试/验证功能, 直到添加并集成所有所需功能.

定制步骤可能涉及但不限于添加...

- 支持附加存储介质.

- 支持其他启动模式.

- 支持网络接口.

- 支持额外的 U-Boot 命令 (CONFIG_CMD_\*) 以帮助调试或运行系统.

添加功能时, 通常最好分析 U-Boot 中已存在的使用相同 TI SoC 的其他板, 然后将功能移植到我们自己的板文件、板特定头文件和 defconfig 中.

要识别 U-Boot 中哪些其他板使用相同的 SoC, 请使用以下命令:

```bash
git grep CONFIG_AM43XX=y
```

检查最新的上游 [U-Boot 树](https://gitlab.denx.de/u-boot/u-boot) 是否有可能已经可用的其他板也很有帮助. 然而, 在将代码向后移植到 TI SDK 的 U-Boot 部分时必须小心, 以考虑所有必需的依赖项和可能影响特定功能的更改.


# 3. U-Boot 启动调试技巧

进行 U-Boot 板移植通常是一个迭代过程, 涉及一定量的调试和故障排除, 尤其是在与 TI EVM 存在很大差异的定制硬件平台上. 下面的列表提供了一些在调试和 U-Boot 启动过程中可能会有所帮助的想法.

- 最高效、最强大的电路板启动工具是通过 JTAG 调试器访问 SoC, 并使用 TI Code Composer Studio (CCS) 等工具来检查器件和代码.

    - 与 SPL (./spl/u-boot-spl) 和 U-Boot (./u-boot) ELF 文件结合使用以进行完全符号调试.

    - 一个非常有用的工具是使用 CCS 特定的 [AM43xx 调试服务器脚本包](https://git.ti.com/cgit/sitara-dss-files/am43xx-dss-files/) 进行低级设备状态和启动分析. 有关详细信息, 请参阅随附的自述文件.

- 执行基本的 printf() 式调试.

    - 当 JTAG 不可用或不实用时使用.

    - 如前所述, 为了最大限度地提高此方法的实用性, 通常需要配置和激活早期 (调试) UART (这将作为 SPL 的 board_init_f() 的一部分进行) , 因为当前 TI EVM 上的大多数关键低级代码都会执行 而常规控制台 UART 尚不可用, 在这种情况下, 在与启动、PMIC 设置、时钟设置、DDR 设置和其他关键阶段相关的任何故障期间不会打印任何内容, 从而导致"黑屏"类型的故障留下任何线索 检查什么.

    - 许多 U-Boot 模块 (源文件) 已经包含各种形式的 debug() 打印语句, 可以通过在源文件顶部添加 #define DEBUG 来针对每个模块激活这些语句.

    - 除此之外, 在整个引导流程中添加打印语句以跟踪程序执行也会很有帮助. 例如, 下面所示的简单语句可以通过复制和粘贴轻松复制, 但通常会提供足够的信息来查明特定的代码行:

```bash
printf("%s: %d:\n", __func__, __LINE__);
```

- 仔细检查最终的设备树文件内容.

    - 由于内置到 U-Boot 中的设备树文件 (以及 SPL, 因为在给定平台上使用 CONFIG_SPL_OF_CONTROL 的情况) 不仅仅从顶层 <device>-<myboard>.dts 设备树源创建 文件, 而且还来自显式 (和隐式) 包含的头文件 (以及 SPL 特定的 DTS 属性, 如 u-boot、dm-spl 和 u-boot、dm-pre-reloc) 的整个层次结构, 最好仔细检查一下什么 实际的最终设备树 blob 看起来像. 最好的方法是将其反编译回源代码, 对于 SPL 和 U-Boot 设备树, 可以使用以下命令来完成:

```bash
dtc -I dtb spl/u-boot-spl.dtb
dtc -I dtb u-boot.dtb
```
