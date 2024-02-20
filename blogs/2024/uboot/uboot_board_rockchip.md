
# 1. Introduction

本文档介绍了Rockchip支持的板卡信息及其使用步骤。

参考 [U-boot rockchip](https://docs.u-boot.org/en/latest/board/rockchip/index.html)


# 2. Rockchip boards

Rockchip是平板电脑和PC、流媒体电视盒、AI音频和视觉、物联网硬件的SoC解决方案提供商。

主线 U-Boot 支持多种 Rockchip SoC 及其相关板。

Rockchip主线支持主板列表：

- px30
    - Rockchip Evb-PX30 (evb-px30)
    - Engicam PX30.Core C.TOUCH 2.0 (px30-core-ctouch2-px30)
    - Engicam PX30.Core C.TOUCH 2.0 10.1 (px30-core-ctouch2-of10-px30)
    - Engicam PX30.Core EDIMM2.2 Starter Kit (px30-core-edimm2.2-px30)
    - Firefly Core-PX30-JD4 (firefly-px30)
    - Theobroma Systems PX30-µQ7 SoM - Ringneck (ringneck-px30)

- rk3036
    - Rockchip Evb-RK3036 (evb-rk3036)
    - Kylin (kylin_rk3036)

- rk3066
    - Rikomagic MK808 (mk808)

- rk3128
    - Rockchip Evb-RK3128 (evb-rk3128)

- rk3188
    - Radxa Rock (rock)

- rk3229
    - Rockchip Evb-RK3229 (evb-rk3229)

- rk3288
    - Rockchip Evb-RK3288 (evb-rk3288)
    - Firefly-RK3288 (firefly-rk3288)
    - MQmaker MiQi (miqi-rk3288)
    - Phytec RK3288 PCM-947 (phycore-rk3288)
    - PopMetal-RK3288 (popmetal-rk3288)
    - Radxa Rock 2 Square (rock2)
    - Tinker-RK3288 (tinker-rk3288)
    - Google Jerry (chromebook_jerry)
    - Google Mickey (chromebook_mickey)
    - Google Minnie (chromebook_minnie)
    - Google Speedy (chromebook_speedy)
    - Amarula Vyasa-RK3288 (vyasa-rk3288)

- rk3308
    - Radxa ROCK Pi S (rock-pi-s-rk3308)
    - Rockchip Evb-RK3308 (evb-rk3308)
    - Roc-cc-RK3308 (roc-cc-rk3308)

- rk3326
    - ODROID-GO Advance (odroid-go2)

- rk3328
    - Rockchip Evb-RK3328 (evb-rk3328)
    - Pine64 Rock64 (rock64-rk3328)
    - Firefly-RK3328 (roc-cc-rk3328)
    - Radxa Rockpi E (rock-pi-e-rk3328)

- rk3368
    - GeekBox (geekbox)
    - PX5 EVB (evb-px5)
    - Rockchip Sheep (sheep-rk3368)
    - Theobroma Systems RK3368-uQ7 SoM - Lion (lion-rk3368)

- rk3399
    - 96boards RK3399 Ficus (ficus-rk3399)
    - 96boards Rock960 (rock960-rk3399)
    - Firefly-RK3399 (firefly_rk3399)
    - Firefly ROC-RK3399-PC
    - FriendlyElec NanoPC-T4 (nanopc-t4-rk3399)
    - FriendlyElec NanoPi M4 (nanopi-m4-rk3399)
    - FriendlyElec NanoPi M4B (nanopi-m4b-rk3399)
    - FriendlyARM NanoPi NEO4 (nanopi-neo4-rk3399)
    - Google Bob (chromebook_bob)
    - Google Kevin (chromebook_kevin)
    - Khadas Edge (khadas-edge-rk3399)
    - Khadas Edge-Captain (khadas-edge-captain-rk3399)
    - Khadas Edge-V (hadas-edge-v-rk3399)
    - Orange Pi RK3399 (orangepi-rk3399)
    - Pine64 RockPro64 (rockpro64-rk3399)
    - Radxa ROCK 4C+ (rock-4c-plus-rk3399)
    - Radxa ROCK 4SE (rock-4se-rk3399)
    - Radxa ROCK Pi 4A/B/A+/B+ (rock-pi-4-rk3399)
    - Radxa ROCK Pi 4C (rock-pi-4c-rk3399)
    - Rockchip Evb-RK3399 (evb_rk3399)
    - Theobroma Systems RK3399-Q7 SoM - Puma (puma_rk3399)

- rk3566
    - Anbernic RGxx3 (anbernic-rgxx3-rk3566)
    - Pine64 Quartz64-A Board (quartz64-a-rk3566)
    - Pine64 Quartz64-B Board (quartz64-b-rk3566)
    - Pine64 SOQuartz on Blade (soquartz-blade-rk3566)
    - Pine64 SOQuartz on CM4-IO (soquartz-cm4-rk3566)
    - Pine64 SOQuartz on Model A (soquartz-model-a-rk3566)
    - Radxa CM3 IO Board (radxa-cm3-io-rk3566)

- rk3568
    - Rockchip Evb-RK3568 (evb-rk3568)
    - Banana Pi BPI-R2 Pro (bpi-r2-pro-rk3568)
    - EmbedFire LubanCat 2 (lubancat-2-rk3568)
    - FriendlyElec NanoPi R5C (nanopi-r5c-rk3568)
    - FriendlyElec NanoPi R5S (nanopi-r5s-rk3568)
    - Generic RK3566/RK3568 (generic-rk3568)
    - Hardkernel ODROID-M1 (odroid-m1-rk3568)
    - Radxa E25 Carrier Board (radxa-e25-rk3568)
    - Radxa ROCK 3 Model A (rock-3a-rk3568)

- rk3588
    - Rockchip EVB (evb-rk3588)
    - Edgeble Neural Compute Module 6A SoM - Neu6a (neu6a-io-rk3588)
    - Edgeble Neural Compute Module 6B SoM - Neu6b (neu6b-io-rk3588)
    - FriendlyElec NanoPC-T6 (nanopc-t6-rk3588)
    - Pine64 QuartzPro64 (quartzpro64-rk3588)
    - Radxa ROCK 5A (rock5a-rk3588s)
    - Radxa ROCK 5B (rock5b-rk3588)
    - Xunlong Orange Pi 5 (orangepi-5-rk3588s)
    - Xunlong Orange Pi 5 Plus (orangepi-5-plus-rk3588)

- rv1108
    - Rockchip Evb-rv1108 (evb-rv1108)
    - Elgin-R1 (elgin-rv1108)

- rv1126
    - Edgeble Neural Compute Module 2 SoM - Neu2/Neu2k (neu2-io-r1126)


# 3. Building

## 3.1 TF-A

构建 ARM64 Rockchip SoC 映像时需要 TF-A。

构建 TF-A：

```bash
git clone --depth 1 https://github.com/ARM-software/arm-trusted-firmware.git
cd arm-trusted-firmware
make realclean
make CROSS_COMPILE=aarch64-linux-gnu- PLAT=rk3399
cd ..
```

指定 PLAT= 以及构建 TF-A 所需的 Rockchip 平台。

对于 TF-A 代码未开源的 SoC，请使用 Rockchip 提供的 BL31 二进制文件：

```bash
git clone --depth 1 https://github.com/rockchip-linux/rkbin
```


## 3.2 TPL

对于某些 SoC，U-Boot 源缺乏对初始化 DRAM 的支持。 在这些情况下，要按照以下步骤获得功能齐全的映像： [4.1 使用 U-Boot TPL/SPL 打包映像](#41-package-the-image-with-u-boot-tplspl)，请在构建 U-Boot 时使用 Rockchip rkbin 存储库提供的 DDR 二进制文件作为 ROCKCHIP_TPL。 否则，请按照 [4.2 使用Rockchip miniloader打包镜像](#42-package-the-image-with-rockchip-miniloader)。


## 3.3 U-Boot

```bash
git clone --depth 1 https://source.denx.de/u-boot/u-boot.git
cd u-boot
```

要构建 px30 板：

```bash
export BL31=../arm-trusted-firmware/build/px30/release/bl31/bl31.elf
make evb-px30_defconfig
make CROSS_COMPILE=aarch64-linux-gnu-
```

构建 rk3066 板：

```bash
make mk808_defconfig
make CROSS_COMPILE=arm-linux-gnueabihf-
```

构建 rk3288 板：

```bash
make evb-rk3288_defconfig
make CROSS_COMPILE=arm-linux-gnueabihf-
```

构建 rk3308 板：

```bash
export BL31=../rkbin/bin/rk33/rk3308_bl31_v2.26.elf
export ROCKCHIP_TPL=../rkbin/bin/rk33/rk3308_ddr_589MHz_uartX_mY_v2.07.bin
make evb-rk3308_defconfig
make CROSS_COMPILE=aarch64-linux-gnu-
```

构建 rk3328 板：

```bash
export BL31=../arm-trusted-firmware/build/rk3328/release/bl31/bl31.elf
make evb-rk3328_defconfig
make CROSS_COMPILE=aarch64-linux-gnu-
```

构建 rk3368 板：

```bash
export BL31=../arm-trusted-firmware/build/rk3368/release/bl31/bl31.elf
make evb-px5_defconfig
make CROSS_COMPILE=aarch64-linux-gnu-
```

要构建 rk3399 板：

```bash
export BL31=../arm-trusted-firmware/build/rk3399/release/bl31/bl31.elf
make evb-rk3399_defconfig
make CROSS_COMPILE=aarch64-linux-gnu-
```

构建 rk3568 板：

```bash
export BL31=../arm-trusted-firmware/build/rk3568/release/bl31/bl31.elf
[or]export BL31=../rkbin/bin/rk35/rk3568_bl31_v1.34.elf
export ROCKCHIP_TPL=../rkbin/bin/rk35/rk3568_ddr_1560MHz_v1.13.bin
make evb-rk3568_defconfig
make CROSS_COMPILE=aarch64-linux-gnu-
```

构建 rk3588 板：

```bash
export BL31=../rkbin/bin/rk35/rk3588_bl31_v1.33.elf
export ROCKCHIP_TPL=../rkbin/bin/rk35/rk3588_ddr_lp4_2112MHz_lp5_2736MHz_v1.09.bin
make evb-rk3588_defconfig
make CROSS_COMPILE=aarch64-linux-gnu-
```


# 4. Flashing

## 4.1 Package the image with U-Boot TPL/SPL

### 4.1.1 SD Card

所有 Rockchip 平台（不使用 SPL 的 rk3128 除外）现在都支持使用 binman 的单个启动映像。

要写入从 SD 卡启动的映像（假设为 /dev/sda）：

```bash
sudo dd if=u-boot-rockchip.bin of=/dev/sda seek=64
sync
```


### 4.1.2 eMMC

在大多数 Rockchip 平台中，eMMC 闪存将在 mmc0 上进行探测。

创建 $partitions 中定义的 GPT 分区布局：

```bash
mmc dev 0
gpt write mmc 0 $partitions
```

连接主机和目标设备之间的 USB-OTG 电缆。

使用以下命令在目标上启动 fastboot：

```bash
fastboot 0
```

成功连接小工具后，主机将显示 USB 设备：

```bash
lsusb
# Bus 001 Device 020: ID 2207:330c Fuzhou Rockchip Electronics Company RK3399 in Mask ROM mode
```

对闪存进行编程：

```bash
sudo fastboot -i 0x2207 flash loader1 idbloader.img
sudo fastboot -i 0x2207 flash loader2 u-boot.itb
```

注意:

对于 Rockchip 32 位平台，U-Boot 正确的映像是 u-boot-dtb.img


### 4.1.3 SPI

将u-boot-rockchip-spi.bin写入SPI flash的偏移0处。

将 u-boot-rockchip-spi.bin 复制到 SD 卡并从 SD 启动：

```bash
sf probe
load mmc 1:1 $kernel_addr_r u-boot-rockchip-spi.bin
sf update $fileaddr 0 $filesize
```


## 4.2 Package the image with Rockchip miniloader

Rockchip miniloader 的镜像包需要 rkbin [1]。

```bash
cd ..
git clone --depth 1 https://github.com/rockchip-linux/rkbin
```

创建 idbloader.img:

```bash
cd u-boot
./tools/mkimage -n px30 -T rksd -d ../rkbin/bin/rk33/px30_ddr_333MHz_v1.16.bin idbloader.img
cat ../rkbin/bin/rk33/px30_miniloader_v1.31.bin >> idbloader.img
sudo dd if=idbloader.img of=/dev/sda seek=64
```

创建 trust.img:

```bash
cd ../rkbin
./tools/trust_merger RKTRUST/PX30TRUST.ini
sudo dd if=trust.img of=/dev/sda seek=24576
```

创建 uboot.img [2]:

```bash
cd ../u-boot
../rkbin/tools/loaderimage --pack --uboot u-boot-dtb.bin uboot.img 0x200000
sudo dd if=uboot.img of=/dev/sda seek=16384
```

注意:

1. rkbin 二进制文件会定期更新，因此建议使用最新版本。
2. 0x200000 是加载地址，并且是某些平台的选项。


## 4.3 Package the RK3066 image with U-Boot TPL/SPL on NAND

与后来的 SoC 型号不同，rk3066 BootROM 不支持 SDMMC。 如果所有其他启动选项均失败，则它会在 USB OTG 端口上进入 BootROM 模式。 该方法通过 U-Boot 将 TPL/SPL 加载到 NAND 上，将内核加载到 SD 卡上。


### 4.3.1 SD Card

U-Boot 需要 GPT 分区映射和 SD 卡上包含文件的引导目录结构。

```bash
Partition Map for MMC device 0  --   Partition Type: EFI
Part     Start LBA         End LBA           Name
1        0x00000040        0x00001f7f        "loader1"
2        0x00004000        0x00005fff        "loader2"
3        0x00006000        0x00007fff        "trust"
4        0x00008000        0x0003ffff        "boot"
5        0x00040000        0x00ed7fde        "rootfs"
```

确保为引导分区设置了引导和 esp 标志。 RK3066 未使用 Loader1 分区。

Boot 分区:

```bash
extlinux
  extlinux.conf

zImage
rk3066a-mk808.dtb
```

将 U-Boot 映像写入 SD 卡（假设为 /dev/sda）：

```bash
sudo dd if=u-boot-dtb.img of=/dev/sda seek=16384
sync
```


### 4.3.2 NAND

将设备置于 BootROM 模式：

如果变砖并且没有显示 BootROM 模式，则用针连接 NAND 闪存的引脚 8 和 9，同时重新连接到 PC 的 USB OTG 端口。

显示已连接的设备：

```bash
lsusb
# Bus 001 Device 004: ID 2207:300a Fuzhou Rockchip Electronics Company RK3066 in Mask ROM mode
```

创建 NAND 镜像:

SPL 和 TPL 的大小必须对齐为 2kb。

使用 bash 脚本 ./flash.sh 中的命令进行编程：

```bash
#!/bin/sh

printf "RK30" | dd conv=notrunc bs=4 count=1 of=u-boot-tpl.bin
truncate -s %2048 u-boot-tpl.bin
truncate -s %2048 u-boot-spl.bin
../tools/boot_merger --verbose config-flash.ini
../tools/upgrade_tool ul ./RK30xxLoader_uboot.bin
```

config-flash.ini:

```bash
[CHIP_NAME]
NAME=RK30
[VERSION]
MAJOR=2
MINOR=21
[CODE471_OPTION]
NUM=1
Path1=30_LPDDR2_300MHz_DD.bin
[CODE472_OPTION]
NUM=1
Path1=rk30usbplug.bin
[LOADER_OPTION]
NUM=2
LOADER1=FlashData
LOADER2=FlashBoot
FlashData=u-boot-tpl.bin
FlashBoot=u-boot-spl.bin
[OUTPUT]
PATH=RK30xxLoader_uboot.bin
```


## 5. TODO

- 添加Rockchip idbloader镜像构建；
- 添加Rockchip TPL镜像构建；
- 记录 SPI 闪存启动；
- 添加缺少的 SoC 及其主板列表。

