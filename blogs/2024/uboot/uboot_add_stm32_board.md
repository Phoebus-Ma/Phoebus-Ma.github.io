
# U-Boot add STM32 board

# 如何为您的主板配置 U-Boot

适用于STM32MP13x系列、STM32MP15x系列.

本页介绍如何为您的主板配置 U-Boot 源.

许多现有资源解释了 U-Boot 在新板上的移植. 本文仅提供STM32MP1系列的基本指南.

总而言之, 您需要通过以下步骤为 <VENDOR> <BOARD> 配置 U-Boot: 

1. 添加您的板设备树: arch/arm/dts/<board>.dts 和 <board>-u-boot.dtsi;
2. 创建自己的板卡支持目录: board/<vendor>/<board>;
3. 在Kconfig中添加TARGET_<VENDOR>_<BOARD>;
4. 创建您的主板 defconfig: defconfig/<board>_defconfig;
5. 添加配置文件: include/configs/<board>.h.


# 1. Device tree

在 arch/arm/dts 中添加板设备树文件 <board>.dts 和 <board>-u-boot.dtsi, 步骤如下: 

- 将 Linux 内核设备树复制到目录 arch/arm/dts: <board>.dts 中.
- 更新 arch/arm/dts/Makefile 以编译您的设备树: 

例如, 对于 STM32MP13x 系列:

```bash
dtb-$(CONFIG_STM32MP13X) += \
 	<STM32MP13 board>.dtb
```

例如, 对于 STM32MP15x 系列: 

```bash
dtb-$(CONFIG_STM32MP15X) += \
 	<STM32MP15 board>.dtb 
```

- 在 arch/arm/dts 目录中为您的主板添加 U-Boot 插件设备树: <board>-u-boot.dtsi.

该文件在 <board>.dts 处理期间自动包含.

它包括STMicroElectronics提供的<STM32MP系列>-u-boot.dtsi文件(arch/arm/dts/stm32mp13-u-boot.dtsi或arch/arm/dts/stm32mp15-scmi-u-boot.dtsi)并添加 U-Boot 所需的节点上的所有属性(添加的节点、配置节点和 u-boot、dm-pre-reloc 或 u-boot、dm-pre-proper 属性, 如 U-Boot 概述、设备树中所述).

此时, 您可以将通用 STM32 MPU defconfig 与 STMicroElectronics 板目录一起使用, 但与您的板设备树一起使用.

电路板特定的功能未正确管理, 但对于源自意法半导体设计的简单电路板来说应该足够了.

您可以将 STMicroElectronics 代码与您的设备树一起使用, 如下所示: 

例如, 对于 STM32MP13x 系列: 

```bash
$ make stm32mp13_defconfig
$ make DEVICE_TREE=<STM32MP13 board> all
```

例如, 对于 STM32MP15x 系列: 

```bash
$ make stm32mp15_defconfig
$ make DEVICE_TREE=<STM32MP15 board> all
```

如果 STMicroElectronics 通用板不符合您的板的所有预期行为方面(例如 MAC 地址不在 OTP 中、特定 PMIC、特定引导命令), 则需要执行后续步骤.


## 1.1. Console

U-Boot 控制台使用 Linux 内核设备树的所选节点中定义的 stdout-path, 如 Linux 内核绑定中所述.

由于在重定位之前使用控制台, 因此必须为每个关联节点(和子节点)添加标志 u-boot,dm-pre-reloc.

例如, 在 kernel = <board>.dts 的设备树中: 

```bash
/ {
    ...
    chosen {
        stdout-path = " serial0:115200n8";
    };
    aliases {
        serial0 = &uart4;
    };
    ...
};
...
&uart4 {
    pinctrl-names = "default";
    pinctrl-0 = <&uart4_pins_a>;
    status = "okay";
};
```

在 U-Boot 附加文件 = <board>-u-boot.dtsi 中: 

```bash
&uart4 {
    u-boot,dm-pre-reloc;
};

&uart4_pins_a {
    u-boot,dm-pre-reloc;
    pins1 {
        u-boot,dm-pre-reloc;
    };
    pins2 {
        u-boot,dm-pre-reloc;
    };
};
```

变量波特率, 用CONFIG_BAUDRATE值初始化, 用于配置所需的波特率; 默认值为 115200 波特.

在设备树解析之前, 还可以使用 CONFIG_DEBUG_UART 和 board_debug_uart_init() 激活早期调试跟踪(有关详细信息, 请参阅 U-Boot-如何调试页面).


## 1.2. Config node

"config"节点中的一些属性也用于动态配置板设备树中的 U-Boot 行为, 如下所示: 

- 通用 U-Boot 配置: doc/device-tree-bindings/config.txt:
    - u-boot,mmc-env-partition = 用于保存U-Boot环境的分区名称;
    - u-boot,boot-led = 启动进度指示灯 LED 的名称;
    - u-boot,error-led = 错误指示灯 LED 名称.

- 意法半导体的一些特性: 
    - st,adc_usb_pd = 用于检查 USB 电源的 ADC 通道;
    - st,stm32prog-gpios = 用于强制STM32CubeProgrammer模式的GPIO;
    - st,fastboot-gpios = 用于强制 Android Fastboot 模式的 GPIO.

这些参数在U-Boot通用代码或意法半导体板代码中进行管理(与STM32 MPU板上的LED和按钮一致); 仅当您的主板重用相关代码时才能使用它们.

例如, 对于 STM32MP15x 系列 More info.png, 在 arch/arm/dts/stm32mp157a-dk1-u-boot.dtsi 中, 属性为: 

```bash
config {
    u-boot,boot-led = "heartbeat";
    u-boot,error-led = "error";
    u-boot,mmc-env-partition = "u-boot-env";
    st,adc_usb_pd = <&adc1 18>, <&adc1 19>;
    st,fastboot-gpios = <&gpioa 13 GPIO_ACTIVE_LOW>;
    st,stm32prog-gpios = <&gpioa 14 GPIO_ACTIVE_LOW>;
};
```


## 1.3. USB OTG node

用于 USB OTG 内部外设的 U-Boot 驱动程序, 与兼容的 st、stm32mp15-hsotg, 不具有 USB 主机和 USB 设备检测的完整 OTG 支持; 为了避免问题, STM32 MPU 板中仅使用 gadget 驱动程序, 并通过 CONFIG_USB_GADGET_DWC2_OTG 激活 drivers/usb/gadget/dwc2_udc_otg.c; 主机驱动程序 drivers/usb/host/dwc2.c 未使用 CONFIG_USB_DWC2 激活.

**Warning**

如果您想使用 CONFIG_USB_DWC2 配置激活 HOST 驱动程序, 则需要在您的开发板代码中实现 VBUS 保护以保护 USB 电源线, 避免在 USB HOST 连接到 USB OTG 端口时给 VBUS 上电.

此外, 由于必须激活同一节点的 OTG 内部外设的一个驱动程序, 或者只有第一个驱动程序会在 U-Boot 中绑定, 因此当您使用 CONFIG_USB_DWC2 激活 USB HOST 驱动程序时, 必须停用使用 CONFIG_USB_GADGET_DWC2_OTG 的 USB 设备驱动程序.

添加了两个 U-Boot 特定属性来支持板上 ID 引脚和 VBUS 可能的 USB 连接: 

- u-boot,force-b-session-valid: 此属性强制 USB B 会话(通过停用 ID 引脚的检测)并停用 VBus 感测. 当所有这些功能都由董事会管理时使用它.
- u-boot,force-vbus-detection: 当使用 u-boot,force-b-session-valid 时(通常是在 ID 引脚未正确管理时), 此属性强制 Vbus 感测.

<board>-u-boot.dtsi 中的 USB 配置示例: 

- B 会话强制为设备模式, 未使用 USB ID 引脚且 VBus 感应已停用: 

```bash
&usbotg_hs {
    u-boot,force-b-session-valid;
    dr_mode = "peripheral";
};
```

- 强制 B 会话和 VBus 感应(默认情况下通过"force-b-session-valid"停用):

```bash
&usbotg_hs {
    u-boot,force-b-session-valid;
    u-boot,force-vbus-detection;
    dr_mode = "peripheral";
};
```


# 2. Board subdirectory

创建您自己的板支持子目录 = board/<vendor>/<board> 并添加 Makefile 文件.

您可以添加您的主板所需的任何源文件.

在此 Makefile 中, 您可以编译 STMicroElectronics 文件(在 board/st/stm32mp1 或 board/st/common 中), 或者只是将它们复制为起点然后进行修改.

例如 board/dh electronics/dh_stm32mp1/Makefile 使用 ../../st/stm32mp1 目录中的 STMicroelectronics 板文件和本地文件"board.c".

```bash
ifdef CONFIG_SPL_BUILD
obj-y += ../../st/stm32mp1/spl.o
endif

obj-y += ../../st/stm32mp1/board.o board.o
```


# 3. Kconfig

您需要使用新的配置标志在 Kconfig 中支持您的主板: TARGET_<VENDOR>_<BOARD>.


## 3.1. Arch Kconfig: arch/arm/mach-stm32mp/Kconfig

1. 增加新的配置: CONFIG_TARGET_<VENDOR>_<BOARD>.
2. 选择所需的选项; 至少, 选择以下选项: 
    - 对于STM32MP13x系列: CONFIG_STM32MP13x.
    - 对于STM32MP15x系列: CONFIG_STM32MP15x.
3. 将您的主板 Kconfig(源"board/<vendor>/<board>/Kconfig")包含在: 
    - 对于STM32MP13x系列: arch/arm/mach-stm32mp/Kconfig.13x.
    - 对于STM32MP15x系列: arch/arm/mach-stm32mp/Kconfig.15x.

例如, 对于STM32MP15x线More info.png, 修改文件arch/arm/mach-stm32mp/Kconfig.15x: 

```bash
if STM32MP15x

[...]
choice
    prompt "STM32MP15x board select"
    optional

[...]

config TARGET_<VENDOR>_<BOARD>
    bool "<vendor> <board> board"
    select STM32MP15x
    help
        target the <vendor> <board> board with SOC STM32MP15x
        managed by board/<vendor>/<board>

endchoice

[...]
source "board/st/stm32mp1/Kconfig"
source "board/dhelectronics/dh_stm32mp1/Kconfig"
source "board/<vendor>/<board>/Kconfig"

endif
```


## 3.2. Board Kconfig: board/<vendor>/<board>/Kconfig

该文件的最少内容是: 

```bash
if TARGET_<VENDOR>_<BOARD>

config SYS_BOARD
    default "<board>"

config SYS_VENDOR
    default "<vendor>"

config SYS_CONFIG_NAME
    default "<config>"
endif
```

例如, 请参阅 board/dh electronics/dh_stm32mp1/Kconfig.

SYS_CONFIG_NAME 选择使用的配置文件: include/configs/SYS_CONFIG_NAME.h.


# 4. Defconfig

在 configs 中为您的主板添加新的 defconfig.

- 将正确的 defconfig 复制到你的 "<vendor>_<board>_defconfig" (configs/stm32mp13_defconfig 或 configs/stm32mp15_defconfig);
- 选择你新的 defconfig: make <vendor>_<board>_defconfig;
- 使用 make menuconfig 更改您的 defconfig;
    - 删除 STMicroElectronics 板配置 (CONFIG_TARGET_ST_STM32MP13x 或 CONFIG_TARGET_ST_STM32MP15x);
    - 增加 CONFIG_TARGET_<VENDOR>_<BOARD>;
    - 将 CONFIG_DEFAULT_DEVICE_TREE 更改为板设备树的名称: "<board>";
- 保存更新的 defconfig: make savedefconfig;
- 验证并复制文件"defconfig": configs/<vendor>_<board>_defconfig.

例如, 请参阅文件 configs/stm32mp15_dhcom_basic_defconfig.


# 5. Configuration file

配置文件是Makefile.autoconf中CONFIG_SYS_CONFIG_NAME选择的include/configs目录中的包含文件.

该文件定义了未使用 Kconfig 定义的板的 CONFIG_ 标志.

例如, 在这个配置文件中, 可以通过CONFIG_EXTRA_ENV_SETTINGS选择初始的U-Boot环境, 包括启动命令.

为每个系列提供通用配置文件: 

- STM32MP13x系列: include/configs/stm32mp13_common.h;
- STM32MP15x系列: include/configs/stm32mp15_common.h.

对于 OpenSTLinux 中的 STMicroElectronics 板, 一些配置 (bootcmd) 是修改在 CONFIG_SYS_CONFIG_NAME =

- STM32MP13x系列: stm32mp13_st_common, 配置文件为include/configs/stm32mp13_st_common.h;
- STM32MP15x系列: stm32mp15_st_common, 配置文件为include/configs/stm32mp15_st_common.h.

这些意法半导体配置文件可以是: 

- 无需修改即可在您的项目中使用 (通过 CONFIG_SYS_CONFIG_NAME="stm32mp13_common.h", "stm32mp13_st_common.h", "stm32mp15_common.h" 或 " stm32mp15_st_common.h"), 或
- 在覆盖它们之前包含在您的板配置文件中(例如#include"stm32mp13_common.h")或
- 使用配置文件的起点(复制并更新它).


# 6. Configuration example

## 6.1. MTD partitions

在 U-Boot 中,所有 mtd 固定分区都根据 Linux 内核文件 Documentation/devicetree/bindings/mtd/partitions/fixed-partitions.yaml 中定义的绑定在 U-Boot 设备树中进行管理.

例如在 arch/arm/dts/stm32mp157c-ev1-u-boot.dtsi 中:

```bash
&flash0 {
    partitions {
        compatible = "fixed-partitions";
        #address-cells = <1>;
        #size-cells = <1>;

        partition@0 {
            label = "fsbl1";
            reg = <0x00000000 0x00040000>;
        };
        partition@40000 {
            label = "fsbl2";
            reg = <0x00040000 0x00040000>;
        };
        partition@80000 {
            label = "metadata1";
            reg = <0x00080000 0x00040000>;
        };
        partition@c0000 {
            label = "metadata2";
            reg = <0x000c0000 0x00040000>;
        };
        partition@100000 {
            label = "fip-a";
            reg = <0x00100000 0x00400000>;
        };
        partition@500000 {
            label = "fip-b";
            reg = <0x00500000 0x00400000>;
        };
        partition@900000 {
            label = "u-boot-env";
            reg = <0x00900000 0x00080000>;
        };
        partition@980000 {
            label = "nor-user";
            reg = <0x00980000 0x03680000>;
        };
    };
};
```

```bash
&nand {
    partitions {
        compatible = "fixed-partitions";
        #address-cells = <1>;
        #size-cells = <1>;

        partition@0 {
            label = "fsbl1";
            reg = <0x00000000 0x00080000>;
        };
        partition@80000 {
            label = "fsbl2";
            reg = <0x00080000 0x00080000>;
        };
        partition@100000 {
            label = "metadata1";
            reg = <0x00100000 0x00080000>;
        };
        partition@180000 {
            label = "metadata2";
            reg = <0x00180000 0x00080000>;
        };
        partition@200000 {
            label = "fip-a1";
            reg = <0x00200000 0x00400000>;
        };
        partition@600000 {
            label = "fip-a2";
            reg = <0x00600000 0x00400000>;
        };
        partition@a00000 {
            label = "fip-b1";
            reg = <0x00a00000 0x00400000>;
        };
        partition@e00000 {
            label = "fip-b2";
            reg = <0x00e00000 0x00400000>;
        };
        partition@1200000 {
            label = "UBI";
            reg = <0x01200000 0x3ee00000>;
        };
    };
};
```

客户可以修改其主板的 U-Boot 设备树中的 MTD.

**Warning**

其他 U-Boot 配置取决于 MTD 分区的偏移量.

例如,对于 STM32MP1 系列,名为"u-boot-env"的 NOR 中 U-Boot ENV 的偏移量与以下内容对齐:

- CONFIG_ENV_SECT_SIZE 0x40000, 与 NOR 支持的最大擦除块 256 KB 对齐;
- CONFIG_ENV_OFFSET 0x900000, ENV 分区在 NOR 中的偏移量;
- CONFIG_ENV_OFFSET_REDUND 0x940000, ENV 分区中第二个"扇区"的偏移量.

U-Boot 通过调用 board/st/stm32mp1/stm32mp1.c ::ft_board_setup() 中 CONFIG_OF_BOARD_SETUP 下的 fdt_copy_fixed_partitions() 动态更新 Linux® 内核设备树.

此设备树更新实现了 U-Boot 和 Linux 之间 MTD 配置的对齐,并避免了 STM32CubeProgrammer 支持问题,因为 MTD 分区大小必须与 Flashlayout 对齐.

要在 Linux 设备树中定义 MTD 分区,无需再在开发板代码中调用 fdt_copy_fixed_partitions() 或在客户 defconfig 中停用 CONFIG_OF_BOARD_SETUP. 然而,这可能会导致与 U-Boot 不一致.

有关信息,您还可以直接通过此 U-Boot 配置定义 MTD 配置:

- CONFIG_MTDPARTS_DEFAULT 和 CONFIG_MTDIDS_DEFAULT
- 或者直接在板配置文件中设置变量 CONFIG_EXTRA_ENV_SETTINGS :
    - mtdparts="mtdparts=nand0:2m(fsbl),4m(fip1),4m(fip2),-(UBI)";
    - mtdids="nand0=nand0".

这对应于通用 U-Boot 配置(有关其他详细信息,请参阅 U-Boot 文档).


## 6.2. U-Boot memory mapping

U-Boot 使用的内存由多种配置定义.


### 6.2.1. Before relocation

配置由Kconfig定义:

- CONFIG_SYS_TEXT_BASE: U-Boot 的加载地址, 与 TF-A 配置对齐(请参阅 TF-A 固件配置中的 STM32MP_BL33_BASE);
- CONFIG_CUSTOM_SYS_INIT_SP_ADDR: 开始堆栈(相反顺序);
- CONFIG_PRE_CON_BUF_ADDR 和 CONFIG_PRE_CON_BUF_SZ (4096): 预控制台缓冲区;
- CONFIG_BOOTSTAGE_STASH_ADDR: SPL 的引导阶段存储缓冲区;
- CONFIG_SYS_MALLOC_F_LEN: 为堆栈中分配保留的内存.

DDR 的开头被映射,受 CONFIG_DDR_CACHEABLE_SIZE 限制以避免与 OP-TEE 保留内存重叠,并由 U-Boot 使用.

如果更改板 defconfig 文件中的默认配置,则需要避免重叠,特别是对于堆栈和 OP-TEE. 用于微电子板的默认值定义为:

- 对于 STM32MP13x 系列更多 info.png,位于 arch/arm/mach-stm32mp/Kconfig.13x 中:

| Address  | Comment                | Config                                                           |
| -------- | ---------------------- | ---------------------------------------------------------------- |
| C3000000 | Bootstage              | CONFIG_BOOTSTAGE_STASH_ADDR                                      |
| C0800000 | PreConsole             | CONFIG_PRE_CON_BUF_ADDR + CONFIG_PRE_CON_BUF_SZ                  |
| C0400000 | start for stack        | CONFIG_CUSTOM_SYS_INIT_SP_ADDR including CONFIG_SYS_MALLOC_F_LEN |
| C0000000 | Load Address of U-Boot | CONFIG_SYS_TEXT_BASE                                             |

- 对于 STM32MP15x 系列更多 info.png,位于 arch/arm/mach-stm32mp/Kconfig.15x:

| Address  | Comment                | Config                                                                                    |
| -------- | ---------------------- | ----------------------------------------------------------------------------------------- |
| C3000000 | Bootstage              | CONFIG_BOOTSTAGE_STASH_ADDR                                                               |
| C02FF000 | PreConsole             | CONFIG_PRE_CON_BUF_ADDR + CONFIG_PRE_CON_BUF_SZ                                           |
| C0100000 | Load Address of U-Boot | CONFIG_SYS_TEXT_BASE                                                                      |
| C0100000 | Stack (reverse order)  | CONFIG_CUSTOM_SYS_INIT_SP_ADDR including CONFIG_SYS_MALLOC_F_LEN, limited by start of DDR |
| C0000000 | Stack (reverse order)  | CONFIG_CUSTOM_SYS_INIT_SP_ADDR including CONFIG_SYS_MALLOC_F_LEN, limited by start of DDR |


### 6.2.2. After relocation

U-Boot 在 DDR 末尾重新加载,它在 common/board_r.c 中找到足够的可用内存用于其代码和其他保留内存,例如 CONFIG_SYS_MALLOC_LEN 和帧缓冲区.

可以在U-Boot控制台中使用bdinfo命令检查重定位地址.

DDR 的大小是通过 ADDRMAP1-6 寄存器的 DDRCTRL 配置自动计算的(请参阅 drivers/ram/stm32mp1/stm32mp1_ram.c 中的 stm32mp1_ddr_size(),并且其设备树中存在的保留内存节点由 U-Boot 保留(请参阅 board_get_usable_ram_top () 在 arch/arm/mach-stm32mp/dram_init.c 中).

为了避免 U-Boot 违规访问,使用 OP-TEE 的安全内存需要与板设备树的 U-Boot 设备树中的 OP-TEE 保留内存节点对齐,例如 arch/arm/dts/stm32mp135f-dk. 数据传输:

```bash
optee@de000000 {
    reg = <0xde000000 0x2000000>;
    no-map;
};
```

DDR 的开头由 U-Boot 命令(例如 bootm)和脚本(DISTRO)使用,如 U-Boot 环境或配置中的变量所定义:

- CONFIG_SYS_LOAD_ADDR=0xc2000000, 由一些 U-Boot 命令使用;
- kernel_addr_r=0xc2000000;
- fdt_addr_r=0xc4000000;
- scriptaddr=0xc4100000;
- pxefile_addr_r=0xc4200000;
- fdtoverlay_addr_r=0xc4300000;
- ramdisk_addr_r=0xc4400000.

这些默认值在通用配置文件中定义,允许加载 32MB 内核或 FIT(未压缩/压缩)、1MB 设备树文件 (fdt)、1MB 脚本、1MB pxe 文件以及最后的 1MB 覆盖文件和 ramdisk. 内核通过 bootm 命令加载到其最终位置,通常为 0xC0000000.

您可以在板的 #Configuration_file 中更改这些值,以允许更大的文件或优化的加载地址,例如 无需使用 fdt_high=0xFFFFFFFF 进行重定位或在最终位置加载内核(请参阅arm/booting.html 中的 zImage).

有关这些变量的详细信息,请参阅 U-Boot 文档:usage/environment.html (v2022.10).



# 7. References

1. <https://archive.fosdem.org/2018/schedule/event/state_of_the_art_uboot/attachments/slides/2051/export/events/attachments/state_of_the_art_uboot/slides/2051/fosdem_2018_u_boot.pdf>.
2. <https://elinux.org/images/2/2a/Schulz-how-to-support-new-board-u-boot-linux.pdf>.
3. dtschema/schemas/chosen.yaml 所选节点的 Linux 内核绑定.
