
# 1. Introduction

本文档介绍了 Samsung A(7/5/3) 2017 中端手机的信息以及 u-boot 使用步骤。

U-Boot 可以用作链式加载引导加载程序，以取代三星原来的 SBOOT 引导加载程序。 它通过 SBOOT 作为 Android 启动映像加载。


# 2. Phone specs

- A3 (SM-A320) (a3y17lte)
    - 4.7 AMOLED display
    - Exynos 7870 SoC
    - 16GB flash
    - 2GB RAM

- A5 (SM-A520) (a5y17lte)
    - 5.2 AMOLED display
    - Exynos 7880 SoC
    - 32GB flash
    - 3GB RAM

- A7 (SM-A720) (a5y17lte)
    - 5.7 AMOLED display
    - Exynos 7880 SoC
    - 32GB flash
    - 3GB RAM


# 3. Installation

## 3.1 Building u-boot

首先，为 aarch64 设置 CROSS_COMPILE。 然后，为您的手机构建 U-Boot，例如 a5y17lte：

```bash
$ export CROSS_COMPILE=<aarch64 toolchain prefix>
$ make a5y17lte_defconfig
$ make
```

这将在配置的输出目录中构建 u-boot.bin。


## 3.2 Payload

### 3.2.1 What is a payload?

有效负载文件是在 Android 启动映像中用来代替 Linux 内核的文件。 该文件将被加载到内存中，并由 SBOOT 执行，因此是 SBOOT 的有效负载。 它可能是纯 u-boot（考虑到从闪存加载 u-boot 的有效负载），或者 u-boot + u-boot 的有效负载。


### 3.2.2 Creating payload file

为您的内核组装 FIT 映像。


### 3.2.3 Creating android boot image

创建有效负载后，就该创建 Android 映像了：

```bash
uboot=<path to u-boot.bin file>
ramdisk=<path to FIT payload file>
mkbootimg --base 0x40000000 --kernel_offset 0x00000000 --ramdisk_offset 0x01000000 --tags_offset 0x00000100 --pagesize 2048 --second_offset 0x00f00000 --kernel "$uboot" --ramdisk "$ramdisk" -o uboot.img
```

请注意，三星现有的引导加载程序会忽略在 mkbootimg 中设置的偏移量。


## 3.3 Flashing

像常规 Android 启动映像一样闪烁。
