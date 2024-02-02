
# QEMU MIPS

Qemu for MIPS 基于 MIPS Malta 板。 构建的 Malta U-Boot 映像可用于 Qemu 和物理硬件。 Malta 板支持小端和大端以及 32 位和 64 位的所有组合。


# 1. Limitations & comments

Qemu 的内存大小硬编码为 256 MiB。 对于 Malta Little Endian 目标，会生成一个名为 u-boot-swap.bin 的额外字节序交换映像，并且是 Qemu 所需要的。


# 2. Example usage

为 32 位、大端构建：

```bash
make malta_defconfig
make
UBOOT_BIN=u-boot.bin
QEMU_BIN=qemu-system-mips
QEMU_CPU=24Kc
```

为 32 位、小端构建：

```bash
make maltael_defconfig
make
UBOOT_BIN=u-boot-swap.bin
QEMU_BIN=qemu-system-mipsel
QEMU_CPU=24Kc
```

为 64 位、大端构建：

```bash
make malta64_defconfig
make
UBOOT_BIN=u-boot.bin
QEMU_BIN=qemu-system-mips64
QEMU_CPU=MIPS64R2-generic
```

为 64 位、小端构建：

```bash
make malta64el_defconfig
make
UBOOT_BIN=u-boot-swap.bin
QEMU_BIN=qemu-system-mips64el
QEMU_CPU=MIPS64R2-generic
```

使用 U-Boot 二进制文件生成 NOR 闪存映像：

```bash
dd if=/dev/zero bs=1M count=4 | tr '\000' '\377' > pflash.img
dd if=${UBOOT_BIN} of=pflash.img conv=notrunc
```

启动Qemu:

```bash
mkdir tftproot
${QEMU_BIN} -nographic -cpu ${QEMU_CPU} -m 256 -drive if=pflash,file="$(pwd)/pflash.img",format=raw -netdev user,id=net0,tftp="$(pwd)/tftproot" -device pcnet,netdev=net0
```

```bash
U-Boot 2021.04-00963-g60279a2b1d (Apr 21 2021 - 19:54:32 +0200)

Board: MIPS Malta CoreLV
DRAM:  256 MiB
Flash: 4 MiB
Loading Environment from Flash... *** Warning - bad CRC, using default environment

In:    serial@3f8
Out:   serial@3f8
Err:   serial@3f8
Net:   pcnet#0
IDE:   Bus 0: not available
maltael #
```


# 3. How to debug U-Boot

为了调试 U-Boot，您需要启动带有 gdb 服务器支持 (-s) 的 qemu，并等待连接来启动 CPU (-S)。 在第一个控制台中启动 Qemu：

```bash
mkdir tftproot
${QEMU_BIN} -s -S -nographic -cpu ${QEMU_CPU} -m 256 -drive if=pflash,file="$(pwd)/pflash.img",format=raw -netdev user,id=net0,tftp="$(pwd)/tftproot" -device pcnet,netdev=net0
```

在第二个控制台中启动 gdb：

```bash
gdb-multiarch --eval-command "target remote :1234" u-boot
```

```bash
GNU gdb (Ubuntu 9.2-0ubuntu1~20.04) 9.2
Copyright (C) 2020 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from u-boot...
Remote debugging using :1234
0xbfc00000 in ?? ()
(gdb) c
Continuing.
```
