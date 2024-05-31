
# U-Boot add NXP board

# 1. 说明

- 主机系统   ：Ubuntu 20.04.3 LTS
- 开发板     ：TOPEET-IMX6ULL
- Uboot版本  ：imx_v2020.04_5.4.70_2.3.0
- 交叉编译器 ：gcc-arm-10.3-2021.07-x86_64-arm-none-linux-gnueabihf

u-boot for arm启动流程:

- arm32，没有安全启动选项；
- arm64，bl0 --> bl1 --> bl31 --> bl2 --> kernel --> rootfs --> applications.


# 2. 环境搭建

## 2.1 uboot源码

1. 获取NXP维护的uboot源码：

```bash    
$ git clone https://source.codeaurora.org/external/imx/uboot-imx
```
    
2. 使用命令`git branch -a`查看分支然后切换到目标分支

```bash
$ git checkout imx_v2020.04_5.4.70_2.3.0
```
    
3. 接下来就基于该分支源码进行uboot的移植(根目录下README文件有助于了解uboot的编译移植)
    

## 2.2 交叉编译器

1. 在[armDeveloper](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-a/downloads) 网站下载交叉编译工具:

```bash
GNU Toolchain for the A-profile Architecture: 10.3-2021.07
|
|__ x86_64 Linux hosted cross compilers
    |
    |__ AArch32 target with hard float (arm-linux-none-gnueabihf)
        |
        |__ gcc-arm-10.3-2021.07-x86_64-arm-none-linux-gnueabihf.tar.xz 
```
    
2. 将工具解压至本地

```bash
# 创建文件夹
$ sudo mkdir /usr/local/arm
# 拷贝文件
$ sudo cp gcc-arm-10.3-2021.07-x86_64-arm-none-linux-gnueabihf.tar.xz /usr/local/arm/
# 解压文件
$ sudo tar -vxf /usr/local/arm/gcc-arm-10.3-2021.07-x86_64-arm-none-linux-gnueabihf.tar.xz 
```
    
3. 可以导入工具链到环境变量中

```bash
# 导入到系统环境变量(对所有用户有效)
$ sudo vim /etc/profile
$ export PATH=$PATH:/usr/local/arm/gcc-arm-10.3-2021.07-x86_64-arm-none-linux-gnueabihf/bin
# 或导入到用户环境变量(仅对当前用户有效)
$ sudo vim ~/.bashrc 
$ export PATH=$PATH:/usr/local/arm/gcc-arm-10.3-2021.07-x86_64-arm-none-linux-gnueabihf/bin
```


# 3. 编译源码

> 手上的开发板参考NXP官方的EVK开发板设计，所以移植都是参考mx6ullevk开发板相关代码进行.

1. 查看`board/freescale/mx6ullevk`文件夹下面的README文件说明
    
2. 新建编译脚本文件`make.sh` 并增加可执行权限`chmod +x make.sh`
    
```bash
#!/bin/bash

# 若之前已经导入到环境变量则不需要
export PATH=$PATH:/usr/local/arm/gcc-arm-10.3-2021.07-x86_64-arm-none-linux-gnueabihf/bin
# 若已经在顶层Makefile文件中指定则不需要
export ARCH=arm
# 若已经在顶层Makefile文件中指定则不需要
export CROSS_COMPILE=arm-none-linux-gnueabihf-

make distclean
# make mrproper
make mx6ull_14x14_evk_emmc_defconfig
make
```

3. 执行编译`./make.sh`并等待完成。
    
4. 编译源码这一步的目的：一是为了验证编译环境是否正常；二是为了下一步移植过程中的代码分析（一些文件只有在编译后才会生成）

注意：若是在系统上第一次编译uboot源码可能会遇到缺少`bison`、`flex`的报错信息导致编译失败，只需安装后重新执行编译即可

```bash
# 语法分析工具
$ sudo apt install bison 
# 词法分析工具
$ sudo apt install flex
```


# 4. 移植

## 4.1 添加文件

> 这一步主要是参考mx6ullevk开发板在源码中添加自己的开发板文件.

1. 添加配置文件

```bash
$ cp configs/mx6ull_14x14_evk_emmc_defconfig configs/mx6ull_14x14_topeet_emmc_defconfig
```

```bash
$ cp include/configs/mx6ullevk.h include/configs/mx6ull_topeet.h
```
    
2. 添加开发板文件夹

```bash
$ cp -r board/freescale/mx6ullevk/ board/freescale/mx6ull_topeet/
```
    
更改文件名：

```bash
$ mv board/freescale/mx6ull_topeet/mx6ullevk.c board/freescale/mx6ull_topeet/mx6ull_topeet.c
```
    
3. 添加设备树文件

新版本uboot更多的参考了Linux内核引入了[设备树](https://www.devicetree.org/) 和 [驱动模型(Driver Model)](https://u-boot.readthedocs.io/en/latest/develop/driver-model/index.html)。 所以移植内容就更多的是设备树文件的修改。
    
3.1. `imx6ull-14x14-topeet-emmc.dts`文件：

添加：

```bash
$ cp arch/arm/dts/imx6ull-14x14-evk-emmc.dts arch/arm/dts/imx6ull-14x14-topeet-emmc.dts
```

修改：

```bash
#include "imx6ull-14x14-evk.dts"
```

改为:

```bash
#include "imx6ull-14x14-topeet.dts"
```

3.2. `imx6ull-14x14-topeet.dts`文件：

添加:

```bash
$ cp arch/arm/dts/imx6ull-14x14-evk.dts arch/arm/dts/imx6ull-14x14-topeet.dts
```

修改：

```bash
#include "imx6ul-14x14-evk.dtsi"
#include "imx6ul-14x14-evk-u-boot.dtsi"
```

改为:

```bash
#include "imx6ul-14x14-topeet.dtsi"
#include "imx6ul-14x14-topeet-u-boot.dtsi"
```

3.3. 添加 `imx6ul-14x14-topeet.dtsi`和`imx6ul-14x14-topeet-u-boot.dtsi` 文件

```bash
$ cp arch/arm/dts/imx6ul-14x14-evk.dtsi arch/arm/dts/imx6ul-14x14-topeet.dtsi
$ cp arch/arm/dts/imx6ul-14x14-evk-u-boot.dtsi arch/arm/dts/imx6ul-14x14-topeet-u-boot.dtsi 
```


## 4.2 修改文件

### 4.2.1 mx6ull_14x14_topeet_emmc_defconfig文件

1. 第一处（目标板）

```bash
CONFIG_TARGET_MX6ULL_14X14_EVK=y
```

改为:

```bash
CONFIG_TARGET_MX6ULL_14X14_TOPEET=y
```
    
2. 第二处（imximage配置文件）

```bash
CONFIG_SYS_EXTRA_OPTIONS="IMX_CONFIG=board/freescale/mx6ullevk/imximage.cfg"
```

改为:

```bash
CONFIG_SYS_EXTRA_OPTIONS="IMX_CONFIG=board/freescale/mx6ull_topeet/imximage.cfg"
```

3. 第三处（设备树）

```bash
CONFIG_DEFAULT_DEVICE_TREE="imx6ull-14x14-evk-emmc"
```

改为:

```bash
CONFIG_DEFAULT_DEVICE_TREE="imx6ull-14x14-topeet-emmc"
```
    
4. 第四处（74lv595相关，自己的开发板没有该芯片）

```bash
CONFIG_DM_74X164=y
CONFIG_SOFT_SPI=y
```

改为:

```bash
# CONFIG_DM_74X164 is not set
# CONFIG_SOFT_SPI is not set
```


### 4.2.2 mx6ull_topeet.h文件修改

添加自己开发板的内容，修改如下：

```bash
"findfdt="\
    "if test $fdt_file = undefined; then " \
        "if test $board_name = ULZ-EVK && test $board_rev = 14X14; then " \
            "setenv fdt_file imx6ulz-14x14-evk.dtb; fi; " \
        "if test $board_name = EVK && test $board_rev = 9X9; then " \
            "setenv fdt_file imx6ull-9x9-evk.dtb; fi; " \
        "if test $board_name = EVK && test $board_rev = 14X14; then " \
            "setenv fdt_file imx6ull-14x14-evk.dtb; fi; " \
        "if test $board_name = TOPEET && test $board_rev = 14X14; then " \
            "setenv fdt_file imx6ull-14x14-topeet.dtb; fi; " \
        "if test $fdt_file = undefined; then " \
            "echo WARNING: Could not determine dtb to use; " \
        "fi; " \
    "fi;\0" \
"findtee="\
    "if test $tee_file = undefined; then " \
        "if test $board_name = ULZ-EVK && test $board_rev = 14X14; then " \
            "setenv tee_file uTee-6ulzevk; fi; " \
        "if test $board_name = EVK && test $board_rev = 9X9; then " \
            "setenv tee_file uTee-6ullevk; fi; " \
        "if test $board_name = EVK && test $board_rev = 14X14; then " \
            "setenv tee_file uTee-6ullevk; fi; " \
        "if test $board_name = TOPEET && test $board_rev = 14X14; then " \
            "setenv tee_file uTee-6ull_topeet; fi; " \
        "if test $tee_file = undefined; then " \
            "echo WARNING: Could not determine tee to use; " \
        "fi; " \
    "fi;\0" \
```


### 4.2.3 mx6ull_topeet文件夹

1. `Kconfig`文件修改如下（最后endif之后务必添加换行）

```bash
if TARGET_MX6ULL_14X14_TOPEET

config SYS_BOARD
    default "mx6ull_topeet"

config SYS_VENDOR
    default "freescale"

config SYS_CONFIG_NAME
    default "mx6ull_topeet"

config SYS_TEXT_BASE
    default 0x87800000
endif
```
    
2. `Makefile`文件修改如下

```bash
$ obj-y  := mx6ull_topeet.o
```
    
3. 由于`CONFIG_USE_IMXIMG_PLUGIN`没有使能，所以`imximage_lpddr2.cfg`文件和和`imximage.cfg`无需修改。否则改动如下

```bash
PLUGIN	board/freescale/mx6ullevk/plugin.bin 0x00907000
```

改为:

```bash
PLUGIN	board/freescale/mx6ull_topeet/plugin.bin 0x00907000
```

4. `mx6ull_topeet.c`文件修改
    
4.1. LCD电源引脚

```bash
static iomux_v3_cfg_t const lcd_pads[] = {
    /* Use GPIO for Brightness adjustment, duty cycle = period. */
    MX6_PAD_GPIO1_IO08__GPIO1_IO08 | MUX_PAD_CTRL(NO_PAD_CTRL),
    /* Used for LCD power control*/
    MX6_PAD_LCD_DATA08__GPIO3_IO13 | MUX_PAD_CTRL(NO_PAD_CTRL),
};

static int setup_lcd(void)
{
    enable_lcdif_clock(LCDIF1_BASE_ADDR, 1);

    imx_iomux_v3_setup_multiple_pads(lcd_pads, ARRAY_SIZE(lcd_pads));

    /* Power on the LCD */
    gpio_request(IMX_GPIO_NR(3, 13), "lcd power");
    gpio_direction_output(IMX_GPIO_NR(3, 13) , 1);

    /* Reset the LCD */
    gpio_request(IMX_GPIO_NR(5, 9), "lcd reset");
    gpio_direction_output(IMX_GPIO_NR(5, 9) , 0);
    udelay(500);
    gpio_direction_output(IMX_GPIO_NR(5, 9) , 1);

    /* Set Brightness to high */
    gpio_request(IMX_GPIO_NR(1, 8), "backlight");
    gpio_direction_output(IMX_GPIO_NR(1, 8) , 1);

    return 0;
}
```

4.2. 输出信息修改

```bash
int board_late_init(void)
{
#ifdef CONFIG_CMD_BMODE
    add_board_boot_modes(board_boot_modes);
#endif

    env_set("tee", "no");
#ifdef CONFIG_IMX_OPTEE
    env_set("tee", "yes");
#endif

#ifdef CONFIG_ENV_VARS_UBOOT_RUNTIME_CONFIG
    env_set("board_name", "TOPEET");

    if (is_mx6ull_9x9_evk())
        env_set("board_rev", "9X9");
    else
        env_set("board_rev", "14X14");

    if (is_cpu_type(MXC_CPU_MX6ULZ)) {
        env_set("board_name", "ULZ-EVK");
        env_set("usb_net_cmd", "usb start");
    }
#endif

    setup_lcd();

#ifdef CONFIG_ENV_IS_IN_MMC
    board_late_mmc_env_init();
#endif

    set_wdog_reset((struct wdog_regs *)WDOG1_BASE_ADDR);

    return 0;
}


int checkboard(void)
{
    if (is_mx6ull_9x9_evk())
        puts("Board: MX6ULL 9x9 EVK\n");
    else if (is_cpu_type(MXC_CPU_MX6ULZ))
        puts("Board: MX6ULZ 14x14 EVK\n");
    else
        puts("Board: MX6ULL 14x14 TOPEET\n");

    return 0;
}
```


### 4.2.4 设备树

- `imx6ull-14x14-topeet.dts`文件

```bash
model = "i.MX6 ULL 14x14 EVK Board";
compatible = "fsl,imx6ull-14x14-evk", "fsl,imx6ull";
```

改为:

```bash
model = "i.MX6 ULL 14x14 TOPEET Board";
compatible = "fsl,imx6ull-14x14-topeet", "fsl,imx6ull";
```
    
- `imx6ul-14x14-topeet.dtsi`文件
    
1. 官方evk开发板使用了一颗扩展IO的芯片`74lv595`，手上的开发板没有该芯片，所以需要去掉该芯片相关内容。屏蔽或删除以下内容：

```bash
aliases {
    /*
    spi5 = &{/spi4};
    */
};

/*
    reg_can_3v3: regulator-can-3v3 {
        compatible = "regulator-fixed";
        regulator-name = "can-3v3";
        regulator-min-microvolt = <3300000>;
        regulator-max-microvolt = <3300000>;
        gpios = <&gpio_spi 3 GPIO_ACTIVE_LOW>;
    };
*/
/*
    spi4 {
        compatible = "spi-gpio";
        pinctrl-names = "default";
        pinctrl-0 = <&pinctrl_spi4>;
        status = "okay";
        pinctrl-assert-gpios = <&gpio5 8 GPIO_ACTIVE_LOW>;
        gpio-sck = <&gpio5 11 0>;
        gpio-mosi = <&gpio5 10 0>;
        cs-gpios = <&gpio5 7 0>;
        num-chipselects = <1>;
        #address-cells = <1>;
        #size-cells = <0>;

        gpio_spi: gpio@0 {
            compatible = "fairchild,74hc595";
            gpio-controller;
            #gpio-cells = <2>;
            reg = <0>;
            registers-number = <1>;
            registers-default = /bits/ 8 <0x57>;
            spi-max-frequency = <100000>;
        };
    };
*/

&can1 {
    pinctrl-names = "default";
    pinctrl-0 = <&pinctrl_flexcan1>;
    /*
    xceiver-supply = <&reg_can_3v3>;
    */
    status = "okay";
};

&can2 {
    pinctrl-names = "default";
    pinctrl-0 = <&pinctrl_flexcan2>;
    /*
    xceiver-supply = <&reg_can_3v3>;
    */
    status = "okay";
};

/*
    pinctrl_spi4: spi4grp {
        fsl,pins = <
            MX6UL_PAD_BOOT_MODE0__GPIO5_IO10	0x70a1
            MX6UL_PAD_BOOT_MODE1__GPIO5_IO11	0x70a1
            MX6UL_PAD_SNVS_TAMPER7__GPIO5_IO07	0x70a1
            MX6UL_PAD_SNVS_TAMPER8__GPIO5_IO08	0x80000000
        >;
    };
*/
```

2. LCD修改。手上的开发板使用的是1024x600的rgb液晶屏，主要修改`display-timings`以及电源引脚:
    
`pinctrl_lcdif_dat`修改：

```bash
pinctrl_lcdif_dat: lcdifdatgrp {
    fsl,pins = <
        /*
        MX6UL_PAD_LCD_DATA00__LCDIF_DATA00  0x79
        MX6UL_PAD_LCD_DATA01__LCDIF_DATA01  0x79
        MX6UL_PAD_LCD_DATA02__LCDIF_DATA02  0x79
        */
        MX6UL_PAD_LCD_DATA03__LCDIF_DATA03  0x79
        MX6UL_PAD_LCD_DATA04__LCDIF_DATA04  0x79
        MX6UL_PAD_LCD_DATA05__LCDIF_DATA05  0x79
        MX6UL_PAD_LCD_DATA06__LCDIF_DATA06  0x79
        MX6UL_PAD_LCD_DATA07__LCDIF_DATA07  0x79
        /*
        MX6UL_PAD_LCD_DATA08__LCDIF_DATA08  0x79
        MX6UL_PAD_LCD_DATA09__LCDIF_DATA09  0x79
        */
        MX6UL_PAD_LCD_DATA10__LCDIF_DATA10  0x79
        MX6UL_PAD_LCD_DATA11__LCDIF_DATA11  0x79
        MX6UL_PAD_LCD_DATA12__LCDIF_DATA12  0x79
        MX6UL_PAD_LCD_DATA13__LCDIF_DATA13  0x79
        MX6UL_PAD_LCD_DATA14__LCDIF_DATA14  0x79
        MX6UL_PAD_LCD_DATA15__LCDIF_DATA15  0x79
        /*
        MX6UL_PAD_LCD_DATA16__LCDIF_DATA16  0x79
        MX6UL_PAD_LCD_DATA17__LCDIF_DATA17  0x79
        MX6UL_PAD_LCD_DATA18__LCDIF_DATA18  0x79
        */
        MX6UL_PAD_LCD_DATA19__LCDIF_DATA19  0x79
        MX6UL_PAD_LCD_DATA20__LCDIF_DATA20  0x79
        MX6UL_PAD_LCD_DATA21__LCDIF_DATA21  0x79
        MX6UL_PAD_LCD_DATA22__LCDIF_DATA22  0x79
        MX6UL_PAD_LCD_DATA23__LCDIF_DATA23  0x79
    >;
};
```

`pinctrl_lcdif_ctrl`修改：

```bash
pinctrl_lcdif_ctrl: lcdifctrlgrp {
    fsl,pins = <
        MX6UL_PAD_LCD_CLK__LCDIF_CLK	    0x79
        MX6UL_PAD_LCD_ENABLE__LCDIF_ENABLE  0x79
        MX6UL_PAD_LCD_HSYNC__LCDIF_HSYNC    0x79
        MX6UL_PAD_LCD_VSYNC__LCDIF_VSYNC    0x79
        /* used for lcd reset */
        MX6UL_PAD_SNVS_TAMPER9__GPIO5_IO09  0x79
        /* used for lcd power*/
        MX6UL_PAD_LCD_DATA08__GPIO3_IO13	0x79
    >;
};
```

`display-timings`修改：

```bash
&lcdif {
    pinctrl-names = "default";
    pinctrl-0 = <&pinctrl_lcdif_dat
             &pinctrl_lcdif_ctrl>;

    display = <&display0>;
    status = "okay";

    display0: display@0 {
        bits-per-pixel = <24>;
        bus-width = <24>;

        display-timings {
            native-mode = <&timing0>;
            timing0: timing0 {
            clock-frequency = <51200000>;
            hactive = <1024>;
            vactive = <600>;
            hfront-porch = <160>;
            hback-porch = <140>;
            hsync-len = <20>;
            vback-porch = <20>;
            vfront-porch = <12>;
            vsync-len = <3>;

            hsync-active = <0>;
            vsync-active = <0>;
            de-active = <1>;
            pixelclk-active = <0>;
            };
        };
    };
};
```

关于display-timings的说明可以参考uboot源码下的该文档：`doc/device-tree-bindings/video/display-timing.txt`
    
3. 网络芯片修改。手上开发板使用的网络芯片与官方板同款`KSZ8081`，除了复位引脚不同外其他一样，官方开发板是通过74lv595扩展IO控制复位引脚。修改如下：

```bash
&fec2 {
    pinctrl-names = "default";
    pinctrl-0 = <&pinctrl_enet2>;
    phy-mode = "rmii";
    phy-handle = <&ethphy1>;
    status = "okay";

    mdio {
        #address-cells = <1>;
        #size-cells = <0>;

        ethphy0: ethernet-phy@2 {
            reg = <2>;
            micrel,led-mode = <1>;
            clocks = <&clks IMX6UL_CLK_ENET_REF>;
            clock-names = "rmii-ref";
            phy-reset-gpios = <&gpio5 7 GPIO_ACTIVE_LOW>;
            phy-reset-duration = <10>;
            phy-reset-post-delay = <10>;
        };

        ethphy1: ethernet-phy@1 {
            reg = <1>;
            micrel,led-mode = <1>;
            clocks = <&clks IMX6UL_CLK_ENET2_REF>;
            clock-names = "rmii-ref";
            phy-reset-gpios = <&gpio5 8 GPIO_ACTIVE_LOW>;
            phy-reset-duration = <10>;
            phy-reset-post-delay = <10>;
        };
    };
};
```
    
`phy-reset-gpios`、`phy-reset-duration`、`phy-reset-post-delay`参数在文件`drivers/net/fec_mxc.c`中用到。
    
4. 修改设备树Makefile文件`arch/arm/dts/Makefile`，添加自己的开发板。

```bash
dtb-$(CONFIG_MX6ULL) += \
    imx6ull-14x14-ddr3-val.dtb \
    imx6ull-14x14-ddr3-val-epdc.dtb \
    imx6ull-14x14-ddr3-val-emmc.dtb \
    imx6ull-14x14-ddr3-val-gpmi-weim.dtb \
    imx6ull-14x14-ddr3-val-tsc.dtb \
    imx6ull-14x14-evk.dtb \
    imx6ull-14x14-evk-emmc.dtb \
    imx6ull-14x14-topeet-emmc.dtb \
    imx6ull-14x14-evk-gpmi-weim.dtb \
    imx6ull-9x9-evk.dtb \
    imx6ull-colibri.dtb \
    imx6ull-phytec-segin-ff-rdk-emmc.dtb \
    imx6ull-dart-6ul.dtb \
    imx6ull-somlabs-visionsom.dtb \
    imx6ulz-14x14-evk.dtb \
    imx6ulz-14x14-evk-emmc.dtb \
    imx6ulz-14x14-evk-gpmi-weim.dtb
```

### 4.2.5 其他修改

`arch/arm/mach-imx/mx6/Kconfig`文件修改:

添加自己开发板平台

```bash
config TARGET_MX6ULL_14X14_TOPEET
    bool "Support mx6ull_14x14_topeet"
    select BOARD_LATE_INIT
    select DM
    select DM_THERMAL
    select MX6ULL
    imply CMD_DM
```

添加自己的开发板Kconfig文件

```bash
$ source "board/freescale/mx6ull_topeet/Kconfig"
```


# 5. 编译启动

1. 修改`make.sh`文件

```bash
$ make mx6ull_14x14_evk_emmc_defconfig
```

改为:

```bash
$ make mx6ull_14x14_topeet_emmc_defconfig
```
    
执行`./make.sh`等待编译完成。
    
2. 烧录至SD卡

```bash
$ sudo dd if=u-boot-dtb.imx of=/dev/sdb bs=1K seek=1 && sync
```

编译完成后使用上述命令将uboot固件烧录至SD卡，`sdb`是我的SD卡挂载到Ubuntu下的名字，可以通过命令`ls /dev/sd*`查看挂载了哪些设备来确定烧录对象。
    
3. 启动开发板

开发板拨码开关对应启动方式：

| 1   | 2   | 3   | 4   | 5   | 6   | 7   | 8   | 启动设备                    |
| --- | --- | --- | --- | --- | --- | --- | --- | --------------------------- |
| 0   | 1   | -   | -   | -   | -   | -   | -   | 串行下载（USB_OTG烧写镜像） |
| 1   | 0   | 0   | 0   | 0   | 0   | 1   | 0   | SD 卡启动                   |
| 1   | 0   | 1   | 0   | 0   | 1   | 1   | 0   | EMMC 启动                   |
| 1   | 0   | 0   | 0   | 1   | 0   | 0   | 1   | NAND FLASH 启动             |


# 6. 其他

## 6.1 endif错误

```bash
board/freescale/mx6ull_topeet/Kconfig:14: 'endif' in different file than 'if'
board/freescale/mx6ull_topeet/Kconfig:3: location of the 'if'
arch/arm/mach-imx/mx6/Kconfig:913: 'endif' in different file than 'if'
board/freescale/mx6ull_topeet/Kconfig:3: location of the 'if'
arch/arm/Kconfig:1893: 'endmenu' in different file than 'menu'
board/freescale/mx6ull_topeet/Kconfig:3: location of the 'menu'
make[1]: *** [scripts/kconfig/Makefile:128：mx6ull_14x14_topeet_emmc_defconfig] 错误 1
make: *** [Makefile:539：mx6ull_14x14_topeet_emmc_defconfig] 错误 2
scripts/kconfig/conf  --syncconfig Kconfig
board/freescale/mx6ull_topeet/Kconfig:14: 'endif' in different file than 'if'
board/freescale/mx6ull_topeet/Kconfig:3: location of the 'if'
arch/arm/mach-imx/mx6/Kconfig:913: 'endif' in different file than 'if'
board/freescale/mx6ull_topeet/Kconfig:3: location of the 'if'
arch/arm/Kconfig:1893: 'endmenu' in different file than 'menu'
board/freescale/mx6ull_topeet/Kconfig:3: location of the 'menu'
make[2]: *** [scripts/kconfig/Makefile:47：syncconfig] 错误 1
make[1]: *** [Makefile:539：syncconfig] 错误 2
make: *** 没有规则可制作目标“include/config/auto.conf”，由“include/config/uboot.release” 需求。 停止。
```

出现该错误的原因是因为我在修改`board/freescale/mx6ull_topeet/Kconfig`文件时没有在文件最后`endif`之后添加换行，在该文件最后敲个换行即可解决。


## 6.2 Net Error错误

```bash
U-Boot 2020.04-00001-g44f5949dd9-dirty (Oct 17 2021 - 19:25:28 +0800)

CPU:   i.MX6ULL rev1.1 792 MHz (running at 396 MHz)
CPU:   Industrial temperature grade (-40C to 105C) at 37C
Reset cause: POR
Model: i.MX6 ULL 14x14 TOPEET Board
Board: MX6ULL 14x14 TOPEET
DRAM:  512 MiB
MMC:   FSL_SDHC: 0, FSL_SDHC: 1
Loading Environment from MMC... *** Warning - bad CRC, using default environment

[*]-Video Link 0 (1024 x 600)
        [0] lcdif@21c8000, video
In:    serial
Out:   serial
Err:   serial
switch to partitions #0, OK
mmc0 is current device
flash target is MMC:0
Net:
Error: ethernet@20b4000 address not set.

Error: ethernet@20b4000 address not set.

Error: ethernet@20b4000 address not set.
FEC: can't find phy-handle

Error: ethernet@20b4000 address not set.
Could not get PHY for FEC0: addr 2

Error: ethernet@20b4000 address not set.
FEC: can't find phy-handle

Error: ethernet@20b4000 address not set.
Could not get PHY for FEC0: addr 2
No ethernet found.
```

若uboot启动后终端输出如上错误信息，有两种解决方案：

**方案一：**

在文件`configs/mx6ull_14x14_topeet_emmc_defconfig`添加以下内容使能uboot给网卡分配随机MAC地址的功能，然后重新编译后烧录启动

```bash
CONFIG_NET_RANDOM_ETHADDR=y
```

使能随机分配MAC功能后结果：

```bash
U-Boot 2020.04-00001-g44f5949dd9-dirty (Oct 17 2021 - 21:57:35 +0800)

CPU:   i.MX6ULL rev1.1 792 MHz (running at 396 MHz)
CPU:   Industrial temperature grade (-40C to 105C) at 36C
Reset cause: POR
Model: i.MX6 ULL 14x14 TOPEET Board
Board: MX6ULL 14x14 TOPEET
DRAM:  512 MiB
MMC:   FSL_SDHC: 0, FSL_SDHC: 1
Loading Environment from MMC... *** Warning - bad CRC, using default environment

[*]-Video Link 0 (1024 x 600)
        [0] lcdif@21c8000, video
In:    serial
Out:   serial
Err:   serial
switch to partitions #0, OK
mmc0 is current device
flash target is MMC:0
Net:
Warning: ethernet@20b4000 (eth1) using random MAC address - 5a:49:f1:d9:07:cd
eth1: ethernet@20b4000 [PRIME]Get shared mii bus on ethernet@2188000

Warning: ethernet@2188000 (eth0) using random MAC address - ca:85:77:84:17:ae
, eth0: ethernet@2188000
```

**方案二：**

在终端中使用以下命令设置MAC地址

```bash
# 设置网卡1
setenv ethaddr ca:38:29:c3:28:30
# 设置网卡2
setenv eth1addr 76:7d:33:a8:f0:e7
# 保存
saveenv
# 重启
reset
```

重启后输出结果：

```bash
U-Boot 2020.04-00001-g44f5949dd9-dirty (Oct 17 2021 - 22:03:25 +0800)

CPU:   i.MX6ULL rev1.1 792 MHz (running at 396 MHz)
CPU:   Industrial temperature grade (-40C to 105C) at 45C
Reset cause: POR
Model: i.MX6 ULL 14x14 TOPEET Board
Board: MX6ULL 14x14 TOPEET
DRAM:  512 MiB
MMC:   FSL_SDHC: 0, FSL_SDHC: 1
Loading Environment from MMC... OK
[*]-Video Link 0 (1024 x 600)
        [0] lcdif@21c8000, video
In:    serial
Out:   serial
Err:   serial
switch to partitions #0, OK
mmc0 is current device
flash target is MMC:0
Net:   eth1: ethernet@20b4000 [PRIME]Get shared mii bus on ethernet@2188000
, eth0: ethernet@2188000
```
