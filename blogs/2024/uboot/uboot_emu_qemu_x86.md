
# QEMU x86

# 1. Build instructions for bare mode

要为 QEMU x86 目标构建 u-boot.rom，只需运行：

```bash
$ make qemu-x86_defconfig (for 32-bit)
$ make qemu-x86_64_defconfig (for 64-bit)
$ make all
```

请注意，此默认配置将为 QEMU x86 i440FX 板构建 U-Boot。 要针对 QEMU x86 Q35 板构建 U-Boot，您可以在“make menuconfig”过程中更改构建配置，如下所示：

```bash
Device Tree Control  --->
    ...
    (qemu-x86_q35) Default Device Tree for DT control
```


# 2. Test with QEMU for bare mode

QEMU 是一个奇特的模拟器，可以让我们在不访问真正的 x86 板的情况下测试 U-Boot。 请确保您的 QEMU 版本为 2.3.0 或以上测试 U-Boot。 要使用 u-boot.rom 启动 QEMU，请按如下方式调用 QEMU：

```bash
$ qemu-system-i386 -nographic -bios path/to/u-boot.rom
```

这将实例化一个带有 i440FX 和 PIIX 芯片组的模拟 x86 板。 QEMU 还支持使用基于 Q35 和 ICH9 的芯片组模拟 x86 板，U-Boot 也支持该功能。 要实例化这样的机器，请使用以下命令调用 QEMU：

```bash
$ qemu-system-i386 -nographic -bios path/to/u-boot.rom -M q35
```

请注意，默认情况下，QEMU 实例化板仅具有 128 MiB 系统内存。 但有U-Boot启动并正常运行就足够了。 如果您想要更多内存，可以通过向 QEMU 传递“-m”参数来增加系统内存：

```bash
$ qemu-system-i386 -nographic -bios path/to/u-boot.rom -m 1024
```

这将创建一个具有 1 GiB 系统内存的板。 目前 U-Boot for QEMU 仅支持 3 GiB 最大系统内存，并为 PCI 设备内存映射 I/O 和其他内容保留最后 1 GiB 地址空间，因此“-m”的最大值将为 3072。

QEMU 模拟 U-Boot 支持的显卡。 删除“-ngraphic”将显示 QEMU 的 VGA 控制台窗口。 请注意，这将禁用 QEMU 的串行输出。 如果您想检查两个控制台，请使用“-serial stdio”。

QEMU 还通过“-smp n”支持多核，其中 n 是要实例化的核心数量。 请注意，QEMU 中支持的最大 CPU 数量为 255。

在 x86 QEMU 上启动时，U-Boot 默认使用“distro_bootcmd”。 它尝试从几个不同的接口加载启动脚本、内核和 ramdisk。 有关默认启动顺序，请参阅“qemu-x86.h”。 有关更多信息，请参阅“doc/develop/distro.rst”。 大多数 Linux 发行版都可以通过编写 uboot 脚本来启动。 例如，可以通过创建名为“boot.txt”的脚本文件来启动 Debian（stretch），其内容为：

```bash
setenv bootargs root=/dev/sda1 ro
load ${devtype} ${devnum}:${distro_bootpart} ${kernel_addr_r} /vmlinuz
load ${devtype} ${devnum}:${distro_bootpart} ${ramdisk_addr_r} /initrd.img
zboot ${kernel_addr_r} - ${ramdisk_addr_r} ${filesize}
```

然后使用以下命令编译并安装它：

```bash
$ apt install u-boot-tools && \
  mkimage -T script -C none -n "Boot script" -d boot.txt /boot/boot.scr
```

QEMU 中的 fw_cfg 接口还提供有关内核数据、initrd、命令行参数等的信息。 U-Boot支持直接从fw_cfg接口访问这些信息，这样可以节省通过模拟设备再次从硬盘或网络加载它们的时间。 要使用它，只需在 QEMU 命令行中提供它们：

```bash
$ qemu-system-i386 -nographic -bios path/to/u-boot.rom -m 1024 \
  -kernel /path/to/bzImage -append 'root=/dev/ram console=ttyS0' \
  -initrd /path/to/initrd -smp 8
```

注意：-initrd 和 -smp 都是可选的

然后启动 QEMU，在 U-Boot 命令行中使用以下 U-Boot 命令来设置内核：

```bash
=> qfw
qfw - QEMU firmware interface

Usage:
qfw <command>
    - list                             : print firmware(s) currently loaded
    - cpus                             : print online cpu number
    - load <kernel addr> <initrd addr> : load kernel and initrd (if any) and setup for zboot

=> qfw load
loading kernel to address 01000000 size 5d9d30 initrd 04000000 size 1b1ab50
```

这里内核（bzImage）被加载到01000000，initrd被加载到04000000。然后，'zboot'可以用来引导内核：

```bash
=> zboot 01000000 - 04000000 1b1ab50
```

要运行 64 位 U-Boot，应使用 qemu-system-x86_64，例如：

```bash
$ qemu-system-x86_64 -nographic -bios path/to/u-boot.rom
```

可以通过“-cpu”参数指定特定的CPU，但请确保指定的CPU支持64位，如“-cpu core2duo”。 相反，“-cpu pentium”将不起作用，原因很明显，处理器仅支持 32 位。


# 3. Booting distros

可以通过设置根磁盘来使用 qemu-x86_64 安装和引导标准 Linux 发行版：

```bash
qemu-img create root.img 10G
```

然后使用安装程序进行安装。 例如，对于 Ubuntu 2023.04：

```bash
qemu-system-x86_64 -m 8G -smp 4 -bios /tmp/b/qemu-x86_64/u-boot.rom \
  -drive file=root.img,if=virtio,driver=raw \
  -drive file=ubuntu-23.04-desktop-amd64.iso,if=virtio,driver=raw
```

如果您希望显示串行控制台以及视频，您还可以添加 -serial mon:stdio。

输出将是这样的：

```bash
U-Boot SPL 2023.07 (Jul 23 2023 - 08:00:12 -0600)
Trying to boot from SPI
Jumping to 64-bit U-Boot: Note many features are missing


U-Boot 2023.07 (Jul 23 2023 - 08:00:12 -0600)

CPU:   QEMU Virtual CPU version 2.5+
DRAM:  8 GiB
Core:  20 devices, 13 uclasses, devicetree: separate
Loading Environment from nowhere... OK
Model: QEMU x86 (I440FX)
Net:   e1000: 52:54:00:12:34:56
       eth0: e1000#0
Hit any key to stop autoboot:  0
Scanning for bootflows in all bootdevs
Seq  Method       State   Uclass    Part  Name                      Filename
---  -----------  ------  --------  ----  ------------------------  ----------------
Scanning global bootmeth 'efi_mgr':
Hunting with: nvme
Hunting with: qfw
Hunting with: scsi
scanning bus for devices...
Hunting with: virtio
Scanning bootdev 'qfw_pio.bootdev':
fatal: no kernel available
Scanning bootdev 'virtio-blk#0.bootdev':
Scanning bootdev 'virtio-blk#1.bootdev':
  0  efi          ready   virtio       2  virtio-blk#1.bootdev.part efi/boot/bootx64.efi
** Booting bootflow 'virtio-blk#1.bootdev.part_2' with efi
EFI using ACPI tables at f0060
     efi_install_fdt() WARNING: Can't have ACPI table and device tree - ignoring DT.
       efi_run_image() Booting /efi\boot\bootx64.efi
error: file `/boot/' not found.
```

标准启动会浏览各种可用设备并找到 virtio 磁盘，然后从第一个启动。 大约一秒钟后，grub 菜单就会出现，您可以正常完成安装程序流程。

请注意，标准启动不会找到 32 位发行版，因为它会查找不同的文件名。


# 4. Current limitations

只有 qemu-x86-64 可用于引导发行版，因为 qemu-x86（U-Boot 的 32 位版本）似乎有一个 EFI 错误，导致从 grub 选择 Linux 后进行引导处理，例如 使用 debian-12.1.0-i386-netinst.iso：

```bash
** Booting bootflow 'virtio-blk#1.bootdev.part_2' with efi
EFI using ACPI tables at f0180
     efi_install_fdt() WARNING: Can't have ACPI table and device tree - ignoring DT.
       efi_run_image() Booting /efi\boot\bootia32.efi
Failed to open efi\boot\root=/dev/sdb3 - Not Found
Failed to load image 큀緃: Not Found
start_image() returned Not Found, falling back to default loader
Welcome to GRUB!
```

在操作系统能够显示显示之前，bochs 视频驱动程序似乎也会引起问题。

QEMU -cdrom 选项旨在使用原始 ISO 格式映像，而不是最近发明的 ISOHybrid 映像。

最后，使用 -M accel=kvm 的目的是利用本机 CPU 的虚拟机功能来加速运行，但这会导致 U-Boot 在跳转 64 位模式时挂起，至少在 AMD 机器上是这样。 这可能是 U-Boot 中的错误或其他错误。
