
# 1. Introduction

BananaPi BPI-M2-PRO是Sinovoip生产的单板计算机，其规格如下：

- Amlogic S905X3 Arm Cortex-A55 quad-core SoC
- 2GB DDR4 SDRAM
- 16GB eMMC
- Gigabit Ethernet
- RTL8821CU USB WiFi (a/b/g/n/ac) + BT 5.0
- HDMI 2.1 display
- 40-pin GPIO header
- 2x USB 3.0 Host
- 1x DC Jack (power)
- microSD
- UART serial
- Infrared receiver

原理图可从制造商处获得： <https://wiki.banana-pi.org/Banana_Pi_BPI-M2_Pro>


# 2. U-Boot Compilation

```bash
$ export CROSS_COMPILE=aarch64-none-elf-
$ make bananapi-m2pro_defconfig
$ make
```


# 3. U-Boot Signing with Pre-Built FIP repo

```bash
$ git clone https://github.com/LibreELEC/amlogic-boot-fip --depth=1
$ cd amlogic-boot-fip
$ mkdir my-output-dir
$ ./build-fip.sh bananapi-m2pro /path/to/u-boot/u-boot.bin my-output-dir
```


# 4. U-Boot Manual Signing

Amlogic 不提供创建引导加载程序映像所需的固件和工具的源代码，因此有必要从主板供应商发布的源代码中获取二进制文件：

```bash
$ wget https://releases.linaro.org/archive/13.11/components/toolchain/binaries/gcc-linaro-aarch64-none-elf-4.8-2013.11_linux.tar.xz
$ wget https://releases.linaro.org/archive/13.11/components/toolchain/binaries/gcc-linaro-arm-none-eabi-4.8-2013.11_linux.tar.xz
$ tar xvfJ gcc-linaro-aarch64-none-elf-4.8-2013.11_linux.tar.xz
$ tar xvfJ gcc-linaro-arm-none-eabi-4.8-2013.11_linux.tar.xz
$ export PATH=$PWD/gcc-linaro-aarch64-none-elf-4.8-2013.11_linux/bin:$PWD/gcc-linaro-arm-none-eabi-4.8-2013.11_linux/bin:$PATH

$ DIR=bananapi-m2pro
$ git clone --depth 1 https://github.com/Dangku/amlogic-u-boot.git -b odroidg12-v2015.01-c4-m5 $DIR

$ cd $DIR
$ make bananapi_m2pro_defconfig
$ make
$ export UBOOTDIR=$PWD
```

然后返回主线 U-Boot 源代码树：

```bash
$ mkdir fip

$ wget https://github.com/BayLibre/u-boot/releases/download/v2017.11-libretech-cc/blx_fix_g12a.sh -O fip/blx_fix.sh
$ cp $UBOOTDIR/build/scp_task/bl301.bin fip/
$ cp $UBOOTDIR/build/board/bananapi/bananpi_m5/firmware/acs.bin fip/
$ cp $UBOOTDIR/fip/g12a/bl2.bin fip/
$ cp $UBOOTDIR/fip/g12a/bl30.bin fip/
$ cp $UBOOTDIR/fip/g12a/bl31.img fip/
$ cp $UBOOTDIR/fip/g12a/ddr3_1d.fw fip/
$ cp $UBOOTDIR/fip/g12a/ddr4_1d.fw fip/
$ cp $UBOOTDIR/fip/g12a/ddr4_2d.fw fip/
$ cp $UBOOTDIR/fip/g12a/diag_lpddr4.fw fip/
$ cp $UBOOTDIR/fip/g12a/lpddr3_1d.fw fip/
$ cp $UBOOTDIR/fip/g12a/lpddr4_1d.fw fip/
$ cp $UBOOTDIR/fip/g12a/lpddr4_2d.fw fip/
$ cp $UBOOTDIR/fip/g12a/piei.fw fip/
$ cp $UBOOTDIR/fip/g12a/aml_ddr.fw fip/
$ cp u-boot.bin fip/bl33.bin

$ sh fip/blx_fix.sh \
     fip/bl30.bin \
     fip/zero_tmp \
     fip/bl30_zero.bin \
     fip/bl301.bin \
     fip/bl301_zero.bin \
     fip/bl30_new.bin \
     bl30

$ sh fip/blx_fix.sh \
     fip/bl2.bin \
     fip/zero_tmp \
     fip/bl2_zero.bin \
     fip/acs.bin \
     fip/bl21_zero.bin \
     fip/bl2_new.bin \
     bl2

$ $UBOOTDIR/fip/g12a/aml_encrypt_g12a --bl30sig --input fip/bl30_new.bin \
                                      --output fip/bl30_new.bin.g12a.enc \
                                      --level v3
$ $UBOOTDIR/fip/g12a/aml_encrypt_g12a --bl3sig --input fip/bl30_new.bin.g12a.enc \
                                      --output fip/bl30_new.bin.enc \
                                      --level v3 --type bl30
$ $UBOOTDIR/fip/g12a/aml_encrypt_g12a --bl3sig --input fip/bl31.img \
                                      --output fip/bl31.img.enc \
                                      --level v3 --type bl31
$ $UBOOTDIR/fip/g12a/aml_encrypt_g12a --bl3sig --input fip/bl33.bin --compress lz4 \
                                      --output fip/bl33.bin.enc \
                                      --level v3 --type bl33 --compress lz4
$ $UBOOTDIR/fip/g12a/aml_encrypt_g12a --bl2sig --input fip/bl2_new.bin \
                                      --output fip/bl2.n.bin.sig
$ $UBOOTDIR/fip/g12a/aml_encrypt_g12a --bootmk \
                                      --output fip/u-boot.bin \
                                      --bl2 fip/bl2.n.bin.sig \
                                      --bl30 fip/bl30_new.bin.enc \
                                      --bl31 fip/bl31.img.enc \
                                      --bl33 fip/bl33.bin.enc \
                                      --ddrfw1 fip/ddr4_1d.fw \
                                      --ddrfw2 fip/ddr4_2d.fw \
                                      --ddrfw3 fip/ddr3_1d.fw \
                                      --ddrfw4 fip/piei.fw \
                                      --ddrfw5 fip/lpddr4_1d.fw \
                                      --ddrfw6 fip/lpddr4_2d.fw \
                                      --ddrfw7 fip/diag_lpddr4.fw \
                                      --ddrfw8 fip/aml_ddr.fw \
                                      --ddrfw9 fip/lpddr3_1d.fw \
                                      --level v3
```

然后使用以下命令将映像写入 SD 或 eMMC：

```bash
$ DEV=/dev/boot_device
$ dd if=fip/u-boot.bin.sd.bin of=$DEV conv=fsync,notrunc bs=512 skip=1 seek=1
$ dd if=fip/u-boot.bin.sd.bin of=$DEV conv=fsync,notrunc bs=1 count=440
```
