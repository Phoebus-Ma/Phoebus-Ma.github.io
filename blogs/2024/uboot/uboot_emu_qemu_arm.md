
# QEMU ARM

# 1. Introdcution

QEMU for ARM 支持专为仿真和虚拟化目的而设计的特殊“虚拟”机器。 本文档介绍了如何在其下运行U-Boot。 支持 32 位 ARM 和 AArch64。

“virt”平台提供以下基本功能：

- 可自由配置的CPU核心数量
- U-Boot 在地址 0x0 处的模拟闪存中加载并执行
- 生成的设备树 blob 放置在 RAM 的开头
- 可自由配置的 RAM 量，由 DTB 描述
- PL011串行端口，可通过DTB发现
- ARMv7/ARMv8 架构的定时器
- PSCI 用于重新启动系统
- 基于 ECAM 的通用 PCI 主机控制器，可通过 DTB 发现

此外，许多可选外设可以添加到 PCI 总线上。

有关如何查看 QEMU 实际生成的设备树的信息，请参阅[QEMU 中的设备树](https://docs.u-boot.org/en/latest/develop/devicetree/dt_qemu.html)。


# 2. Building U-Boot

像往常一样设置 CROSS_COMPILE 环境变量，然后运行：

- 对于ARM：

```bash
make qemu_arm_defconfig
make
```

- 对于AArch64:

```bash
make qemu_arm64_defconfig
make
```


# 3. Running U-Boot

启动并运行 U-Boot 的最小 QEMU 命令行是：

- 对于ARM：

```bash
qemu-system-arm -machine virt -nographic -bios u-boot.bin
```

- 对于AArch64:

```bash
qemu-system-aarch64 -machine virt -nographic -cpu cortex-a57 -bios u-boot.bin
```

请注意，由于某些奇怪的原因，需要明确告诉 qemu-system-aarch64 使用 64 位 CPU，否则它将以 32 位模式启动。 -ngraphic 参数确保输出出现在终端上。 使用 Ctrl-A X 退出。

可以添加额外的持久 U-Boot 环境支持，如下所示：

- 使用 qemu-img 创建 envstore.img：

```bash
qemu-img create -f raw envstore.img 64M
```

- 将 pflash 驱动器参数添加到命令行：

```bash
-drive if=pflash,format=raw,index=1,file=envstore.img
```

可以使用以下命令行参数启用经测试可在 U-Boot 和 Linux 中工作的其他外设：

- 要添加视频控制台，请删除“-ngraphic”并添加例如：

```bash
-serial stdio -device VGA
```

- 要通过 Intel ICH9 AHCI 控制器添加串行 ATA 磁盘，请传递以下命令：

```bash
-drive if=none,file=disk.img,format=raw,id=mydisk \
-device ich9-ahci,id=ahci -device ide-drive,drive=mydisk,bus=ahci.0
```

- 要添加 Intel E1000 网络适配器，请传递例如：

```bash
-netdev user,id=net0 -device e1000,netdev=net0
```

- 要添加符合 EHCI 标准的 USB 主机控制器，请传递例如：

```bash
-device usb-ehci,id=ehci
```

- 要添加连接到模拟 xHCI 控制器的 USB 键盘，请传递例如：

```bash
-device qemu-xhci,id=xhci -device usb-kbd,bus=xhci.0
```

- 要添加 NVMe 磁盘，请传递例如：

```bash
-drive if=none,file=disk.img,id=mydisk -device nvme,drive=mydisk,serial=foo
```

- 要添加随机数生成器，请传递例如：

```bash
-device virtio-rng-pci
```

这些已经在 QEMU 2.9.0 中进行了测试，但至少也应该在 2.5.0 中工作。


# 4. Booting distros

可以通过设置根磁盘来使用 qemu_arm64 安装和启动标准 Linux 发行版：

```bash
qemu-img create root.img 20G
```

然后使用安装程序进行安装。 例如，对于 Debian 12：

```bash
qemu-system-aarch64 \
  -machine virt -cpu cortex-a53 -m 4G -smp 4 \
  -bios u-boot.bin \
  -serial stdio -device VGA \
  -nic user,model=virtio-net-pci \
  -device virtio-rng-pci \
  -device qemu-xhci,id=xhci \
  -device usb-kbd -device usb-tablet \
  -drive if=virtio,file=debian-12.0.0-arm64-netinst.iso,format=raw,readonly=on,media=cdrom \
  -drive if=virtio,file=root.img,format=raw,media=disk
```

输出将是这样的：

```bash
U-Boot 2023.10-rc2-00075-gbe8fbe718e35 (Aug 11 2023 - 08:38:49 +0000)

DRAM:  4 GiB
Core:  51 devices, 14 uclasses, devicetree: board
Flash: 64 MiB
Loading Environment from Flash... *** Warning - bad CRC, using default environment

In:    serial,usbkbd
Out:   serial,vidconsole
Err:   serial,vidconsole
Bus xhci_pci: Register 8001040 NbrPorts 8
Starting the controller
USB XHCI 1.00
scanning bus xhci_pci for devices... 3 USB Device(s) found
Net:   eth0: virtio-net#32
Hit any key to stop autoboot:  0
Scanning for bootflows in all bootdevs
Seq  Method       State   Uclass    Part  Name                      Filename
---  -----------  ------  --------  ----  ------------------------  ----------------
Scanning global bootmeth 'efi_mgr':
Scanning bootdev 'fw-cfg@9020000.bootdev':
fatal: no kernel available
scanning bus for devices...
Scanning bootdev 'virtio-blk#34.bootdev':
  0  efi          ready   virtio       2  virtio-blk#34.bootdev.par efi/boot/bootaa64.efi
** Booting bootflow 'virtio-blk#34.bootdev.part_2' with efi
Using prior-stage device tree
Failed to load EFI variables
Error: writing contents
** Unable to write file ubootefi.var **
Failed to persist EFI variables
Missing TPMv2 device for EFI_TCG_PROTOCOL
Booting /efi\boot\bootaa64.efi
Error: writing contents
** Unable to write file ubootefi.var **
Failed to persist EFI variables
Welcome to GRUB!
```

标准启动会浏览各种可用设备并找到 virtio 磁盘，然后从第一个启动。 大约一秒钟后，grub 菜单就会出现，您可以正常完成安装程序流程。

安装完成后，您可以通过再次运行 QEMU 来启动已安装的系统，而无需与安装程序 CD 映像对应的驱动器参数。


# 5. Enabling TPMv2 support

为了模拟 TPM，可以使用 swtpm 包。 它可以从以下存储库构建：

<https://github.com/stefenberger/swtpm.git>

Swtpm 为 TPM 仿真提供了一个可供 QEMU 使用的套接字。

在第一个控制台中使用以下命令调用 swtpm：

```bash
swtpm socket --tpmstate dir=/tmp/mytpm1   \
--ctrl type=unixio,path=/tmp/mytpm1/swtpm-sock --log level=20
```

在第二个控制台中使用以下命令调用 qemu-system-aarch64：

```bash
-chardev socket,id=chrtpm,path=/tmp/mytpm1/swtpm-sock \
-tpmdev emulator,id=tpm0,chardev=chrtpm \
-device tpm-tis-device,tpmdev=tpm0
```

在 U-Boot 命令行上启用 TPM：

```bash
tpm autostart
```


# 6. Debug UART

ARM 虚拟板上的调试 UART 使用以下设置：

```bash
CONFIG_DEBUG_UART=y
CONFIG_DEBUG_UART_PL010=y
CONFIG_DEBUG_UART_BASE=0x9000000
CONFIG_DEBUG_UART_CLOCK=0
```
