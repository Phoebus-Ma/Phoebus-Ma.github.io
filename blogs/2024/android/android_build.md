
# Android Build

# 1. Rockchip
----------------

- Target   : Android 11
- Hardware : Rockchip 3566 (Cortex-A55 x 4)
- SBC      : Orange PI 3B
- Platform : Ubuntu 22.04
- Source   : <https://pan.baidu.com/share/init?surl=nw-WUCUZ5LjlUnA1TiFCog&pwd=xnut>, code: <xnut>


## 1.1 Enviorments

```bash
$ sudo apt update
$ sudo apt install -y java-8-openjdk-amd64
$ sudo apt install -y build-essential bison flex bc gawk texinfo u-boot-tool \
    git ssh gnupg gperf zip unzip curl zlib1g-dev libncurses-dev liblz4-tool \
    gcc-multilib g++-multilib libc6-dev-i386 x11proto-core-dev libx11-dev    \
    lib32z1-dev ccache libgl1-mesa-dev libxml2-utils xsltproc vim file clang \
    libssl-dev
```


## 1.2 Build

```bash
$ ls
RK356X_Android11.tar.gz.md5sum  RK356X_Android11.tar.gz00
RK356X_Android11.tar.gz01       RK356X_Android11.tar.gz02
RK356X_Android11.tar.gz03       RK356X_Android11.tar.gz04
RK356X_Android11.tar.gz05       RK356X_Android11.tar.gz06
```

```bash
$ md5sum -c RK356X_Android11.tar.gz.md5sum
RK356X_Android11.tar.gz00: OK
RK356X_Android11.tar.gz01: OK
RK356X_Android11.tar.gz02: OK
RK356X_Android11.tar.gz03: OK
RK356X_Android11.tar.gz04: OK
RK356X_Android11.tar.gz05: OK
RK356X_Android11.tar.gz06: OK
```

```bash
$ cat RK356X_Android11.tar.gz0* | tar -xvzf -
$ rm RK356X_Android11.tar.gz*
```

源码中有 build.sh 编译脚本，编译参数如下:

    -U：编译 uboot;
    -K：编译 kernel;
    -A：编译 android;
    -u：打包生成 update.img 与 update_spi_nvme.img;
    -o：编译 OTA 包;
    -d：指定 kernel dts;

- 编译支持HDMI 4K显示镜像(默认关闭LCD):

```bash
$ cd RK356X_Android11/
$ export BOARD=orangepi3b
$ source build/envsetup.sh
$ lunch rk3566_r-userdebug
$ ./build.sh -AUKu
```

- 编译支持 LCD 显示镜像(默认关闭HDMI):

```bash
$ cd RK356X_Android11/
$ export BOARD=orangepi3b
$ export DUAL_LCD=true
$ source build/envsetup.sh
$ lunch rk3566_r-userdebug
$ ./build.sh -AUKu
```

最终生成的镜像文件会放在 rockdev/Image-rk3566_r/目录下.
其中update.img是支持 TF 卡和 eMMC 启动镜像，update_spi_nvme.img 是NVME SSD启动镜像.


# 2. Mediatek
----------------

- Target   : Android 8.1
- Hardware : MediaTek 6737 (Cortex-A53 x 4)
- SBC      : Orange PI 4G-IOT
- Platform : Ubuntu 22.04
- Source   : <https://pan.baidu.com/share/init?surl=kHFpg7c25QDgZGbJQk6YjQ>, code: asji


## 2.1 Enviorments

```bash
$ sudo apt update
$ sudo apt install -y java-8-openjdk-amd64
$ sudo apt install -y build-essential bison flex bc gawk texinfo u-boot-tool \
    git ssh gnupg gperf zip unzip curl zlib1g-dev libncurses-dev liblz4-tool \
    gcc-multilib g++-multilib libc6-dev-i386 x11proto-core-dev libx11-dev    \
    lib32z1-dev ccache libgl1-mesa-dev libxml2-utils xsltproc vim file clang \
    libssl-dev
```


## 2.2 Build

```bash
$ ls
x00  x01  x02  x03
x04  x05  x06  x07
x08  x09  x10
```

```bash
$ cat x* > MTK_Android8.1.tar.gz
$ rm x*
$ tar -xvf MTK_Android8.1.tar.gz
$ rm MTK_Android8.1.tar.gz
```

```bash
$ cd MTK_Android8.1/code/
$ cd orangepi/scripts/
$ ls
anr_LM.sh auto.sh clean.sh codegen.sh init_project.sh tar_img.sh
```

auto.sh  : 为自动编译脚本;
clean.sh : 为自动清除编译结果脚本;

```bash
$ ./auto.sh IoT_k37mv1_bsp_ry_smt_hd720_pcb_v2 v00 eng
```

执行命令编译:

```bash
$ source build/envsetup.sh
$ lunch full_k37mv1_bsp-eng
$ make -j8
```

生成如下固件:

```bash
$ tree IoT_op_smt_hd720_pcb_v2
IoT_op_smt_hd720_pcb_v2
    ├── images
    │   ├── boot.img
    │   ├── cache.img
    │   ├── lk.bin
    │   ├── logo.bin
    │   ├── MT6737M_Android_scatter.txt
    │   ├── preloader_k37mv1_bsp.bin
    │   ├── recovery.img
    │   ├── secro.img
    │   ├── system.img
    │   ├── trustzone.bin
    │   └── userdata.img
    └── modem
        ├── APDB_MT6735_S01_alps-mp-m0.mp1_W18.04
        ├── _APDB_MT6735_S01_alps-mp-m0.mp1_W18.04.check
        └── APDB_MT6735_S01_alps-mp-m0.mp1_W18.04_ENUM
```


# 3. Allwinner
----------------

- Target   : Android 8.1
- Hardware : AllWinner H618 (Cortex-A53 x 4)
- SBC      : Orange PI Zero 3
- Platform : Ubuntu 22.04
- Source   : <https://pan.baidu.com/share/init?surl=BUsudU_XahzB_4eR3s83Ug&pwd=umdt>, code: umdt


## 3.1 Enviorments

```bash
$ sudo apt update
$ sudo apt install -y java-8-openjdk-amd64
$ sudo apt install -y build-essential bison flex bc gawk texinfo u-boot-tool \
    git ssh gnupg gperf zip unzip curl zlib1g-dev libncurses-dev liblz4-tool \
    gcc-multilib g++-multilib libc6-dev-i386 x11proto-core-dev libx11-dev    \
    lib32z1-dev ccache libgl1-mesa-dev libxml2-utils xsltproc vim file clang \
    libssl-dev
```


## 3.2 Build

```bash
$ ls
H618-Android12-Src.tar.gz.md5sum    H618-Android12-Src.tar.gzaa
H618-Android12-Src.tar.gzab         H618-Android12-Src.tar.gzac
H618-Android12-Src.tar.gzad         H618-Android12-Src.tar.gzae
H618-Android12-Src.tar.gzaf         H618-Android12-Src.tar.gzag
H618-Android12-Src.tar.gzah         H618-Android12-Src.tar.gzai
H618-Android12-Src.tar.gzaj         H618-Android12-Src.tar.gzak
H618-Android12-Src.tar.gzal         H618-Android12-Src.tar.gzam
H618-Android12-Src.tar.gzan         H618-Android12-Src.tar.gzao
H618-Android12-Src.tar.gzap         H618-Android12-Src.tar.gzaq
H618-Android12-Src.tar.gzar
```

```bash
$ md5sum -c H618-Android12-Src.tar.gz.md5sum
H618-Android12-Src.tar.gzaa: OK
H618-Android12-Src.tar.gzab: OK
...
H618-Android12-Src.tar.gzaq: OK
H618-Android12-Src.tar.gzar: OK
```

```bash
$ cat H618-Android12-Src.tar.gza* > H618-Android12.tar.gz
$ rm H618-Android12-Src.tar.gza*
$ tar -xvf H618-Android12.tar.gz
$ rm H618-Android12.tar.gz
```

编译u-boot和linux :

```bash
$ cd H618-Android12/longan/
$ ./build.sh config

Welcome to mkscript setup progress
All available platform:
    0. android
    1. linux
Choice [android]: 0
All available ic:
    0. h618
Choice [h618]: 0
All available board:
    0. ft
    1. p1
    2. p2
    3. p7
    4. p7l
    5. perf1
    6. perf2
    7. perf3
    8. qa
Choice [p2]: 2
All available flash:
    0. default
    1. nor
Choice [default]: 0
All available kern_ver:
    0. linux-5.4
Choice [linux-5.4]: 0
All available arch:
    0. arm
    1. arm64
Choice [arm64]: 1
......
*** Default configuration is based on 'sun50iw9p1smp_h618_android_defconfig'
#
# configuration written to .config
#
make[1]: Leaving directory '/home/test/H618-Android12-Src/longan/out/kernel/build'
make: Leaving directory '/home/test/H618-Android12-Src/longan/kernel/linux-5.4'
INFO: clean buildserver
INFO: prepare_buildserver
```

```bash
$ ./build.sh
```

编译Android和生成镜像:

```bash
$ cd H618-Android12/
$ source build/envsetup.sh
$ lunch apollo_p2-userdebug
$ make -j8
$ pack
```

生成的文件:

```bash
longan/out/h618_android12_p2_uart0.img
```


# 4. Amlogic
----------------

- Target   : Android 8.1
- Hardware : Amlogic S905X3 (Cortex-A55 x 4)
- SBC      : Banana PI M5
- Platform : Ubuntu 22.04
- Source1  : <https://github.com/BPI-SINOVOIP/BPI-S905X3-Android9>
- Source2  : <https://pan.baidu.com/share/init?surl=TmmR_075b49lPSt1Phq0ag&pwd=8888>, code: 8888


## 4.1 Enviorments

Download the [toolchains](https://download.banana-pi.dev/d/3ebbfa04265d4dddb81b/?p=/Tools/toolchains/bpi-m5&mode=list) 
and extract each tarball to /opt/ and set the PATH before build:

```bash
$ export PATH=$PATH:/opt/gcc-linaro-aarch64-none-elf-4.9-2014.09_linux/bin
$ export PATH=$PATH:/opt/gcc-linaro-arm-none-eabi-4.8-2014.04_linux/bin
$ export PATH=$PATH:/opt/gcc-linaro-6.3.1-2017.02-x86_64_aarch64-linux-gnu/bin
$ export PATH=$PATH:/opt/gcc-linaro-6.3.1-2017.02-x86_64_arm-linux-gnueabihf/bin
```

```bash
$ sudo apt update
$ sudo apt install -y java-8-openjdk-amd64
$ sudo apt install -y build-essential bison flex bc gawk texinfo u-boot-tool \
    git ssh gnupg gperf zip unzip curl zlib1g-dev libncurses-dev liblz4-tool \
    gcc-multilib g++-multilib libc6-dev-i386 x11proto-core-dev libx11-dev    \
    lib32z1-dev ccache libgl1-mesa-dev libxml2-utils xsltproc vim file clang \
    libssl-dev
```


## 4.2 Build

```bash
$ source env.sh
$ ./device/bananapi/common/quick_compile.sh

[NUM]   [       PROJECT]     [  SOC TYPE]  [   HARDWARE TYPE]
---------------------------------------------------------------
[ 1]    [       m5_mbox]     [    S905X3]  [     BANANAPI_M5]
[ 2]    [     m5_tablet]     [    S905X3]  [     BANANAPI_M5]
[ 3]    [      m2s_mbox]     [     S922X]  [    BANANAPI_M2S]
[ 4]    [    m2s_tablet]     [     S922X]  [    BANANAPI_M2S]
...
---------------------------------------------------------------
please select platform type (default 1(Ampere)):2
...
```
