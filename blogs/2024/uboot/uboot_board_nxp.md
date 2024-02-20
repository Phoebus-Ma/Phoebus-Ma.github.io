
# 1. Introduction

imx8mm_evk:

i.MX 8M Mini EVK board, 4核 Cortex-A53，1核 Cortex-M4F，1核 GPU。


# 2. Quick Start

- 构建 ARM 可信固件二进制文件
- 获取DDR固件
- 构建U-Boot
- 启动


# 3. Get and Build the ARM Trusted firmware

Note: builddir 是 U-Boot 构建目录（树内构建的源目录） 从以下位置获取 ATF：<https://github.com/nxp-imx/imx-atf> 分支: imx_5.4.47_2.2.0

```bash
$ make PLAT=imx8mm bl31
$ cp build/imx8mm/release/bl31.bin $(builddir)
```


# 4. Get the ddr firmware

```bash
$ wget https://www.nxp.com/lgfiles/NMG/MAD/YOCTO/firmware-imx-8.9.bin
$ chmod +x firmware-imx-8.9.bin
$ ./firmware-imx-8.9
$ cp firmware-imx-8.9/firmware/ddr/synopsys/lpddr4*.bin $(builddir)
```


# 5. Build U-Boot for sd card

```bash
$ export CROSS_COMPILE=aarch64-poky-linux-
$ make imx8mm_evk_defconfig
$ make
```

将flash.bin烧录到MicroSD卡偏移33KB：

```bash
$sudo dd if=flash.bin of=/dev/sd[x] bs=1024 seek=33 conv=notrunc
```


# 6. Boot

将启动开关设置为 SD 启动。


# 7. Build U-Boot for qspi flash card

```bash
$ export CROSS_COMPILE=aarch64-poky-linux-
$ make imx8mm_evk_fspi_defconfig
$ make
```

目前，不直接支持写入 QSPI Flash。 从 SD 卡或通过网络将 flash.bin 复制到 ${loadaddr}，然后复制到 qspi flash。

从 SD 卡到内存：

```bash
$mmc dev 1
$mmc read ${loadaddr} 0x00 <size_of_flash.bin/512>
```

```bash
$ sf probe
$ sf erase 0 <size_of_flash.bin_in_hex>
$ sf write $loadaddr 0x00 <size_of_flash.bin_in_hex>
```


# 8. Boot from QSPI Flash

将启动开关设置为 QSPI 闪存。

imx8mm_revC evk 的引脚配置从 qspi 闪存启动 SW1101: 0110xxxxxx SW1102: 00100x0010。
