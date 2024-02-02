
# QEMU RISC-V

# 1. Introduction

适用于 RISC-V 的 QEMU 支持专为仿真和虚拟化目的而设计的特殊“虚拟”机器和“尖峰”机器。 本文档介绍了如何在其下运行U-Boot。 支持 32 位和 64 位目标，在机器模式或管理程序模式下运行。

QEMU 虚拟机模拟通用 RISC-V 虚拟机，支持 VirtIO 标准网络和块存储设备。 除了 VirtIO 之外，它还具有 CLINT、PLIC、16550A UART 设备，并且还使用设备树将配置信息传递给客户软件。 它实现了最新的RISC-V特权架构。

有关如何查看 QEMU 实际生成的设备树的信息，请参阅[QEMU 中的设备树](https://docs.u-boot.org/en/latest/develop/devicetree/dt_qemu.html)。

QEMU 尖峰机模拟了一个仅包含 CLIINT 和 HTIF 设备的简约 RISC-V 虚拟机。 它还使用设备树将配置信息传递给客户软件并实现最新的 RISC-V 特权架构。


# 2. Building U-Boot

像往常一样设置 CROSS_COMPILE 环境变量，然后运行：

- 对于 32-bit RISC-V：

```bash
make qemu-riscv32_defconfig
make
```

- 对于 64-bit RISC-V:

```bash
make qemu-riscv64_defconfig
make
```

这将为机器模式编译 U-Boot。 要构建管理程序模式二进制文件，请改用配置 qemu-riscv32_smode_defconfig 和 qemu-riscv64_smode_defconfig。 请注意，在管理员模式下运行的 U-Boot 需要管理员二进制接口 (SBI)，例如 RISC-V OpenSBI。


# 3. Running U-Boot

启动并运行 U-Boot 的最小 QEMU 命令行是：

- 对于 32 位 RISC-V 虚拟机：

```bash
qemu-system-riscv32 -nographic -machine virt -bios u-boot.bin
```

- 对于 64 位 RISC-V 虚拟机：

```bash
qemu-system-riscv64 -nographic -machine virt -bios u-boot.bin
```

- 对于64位RISC-V秒杀机：

```bash
qemu-system-riscv64 -nographic -machine spike -bios u-boot.bin
```

上面的命令默认创建具有 128MiB 内存的目标。 可以通过“-m”参数创建可自由配置的 RAM 量。 例如，“-m 2G”为目标创建 2GiB 内存，QEMU 创建的嵌入式 DTB 中的内存节点反映了新设置。

有关如何使用 OpenSBI 在 QEMU 上以管理员模式运行 U-Boot 的说明，请参阅 OpenSBI 提供的文档：<https://github.com/riscv/opensbi/blob/master/docs/platform/qemu_virt.md> <https://github.com/riscv/opensbi/blob/master/docs/platform/spike.md>

这些已经在 QEMU 5.0.0 中进行了测试。


# 4. Running U-Boot SPL

在默认 SPL 配置中，U-Boot SPL 以机器模式启动。 U-Boot 本身和 OpenSBI（FW_DYNAMIC 固件）捆绑为 FIT 映像并可供 U-Boot SPL 使用。 然后，两者都由 U-Boot SPL 加载，并且 U-Boot 的正确位置被传递到 OpenSBI。 初始化后，U-Boot 会由 OpenSBI 在管理模式下启动。

在编译 U-Boot 之前必须先编译 OpenSBI。 U-Boot 支持 0.4 及更高版本。 克隆 OpenSBI 存储库并运行以下命令。

```bash
git clone https://github.com/riscv/opensbi.git
cd opensbi
make PLATFORM=generic
```

有关完整详细信息，请参阅 OpenSBI 文档：<https://github.com/riscv/opensbi/blob/master/docs/platform/qemu_virt.md> <https://github.com/riscv/opensbi/blob/master/docs/platform/spike.md>

要使 FW_DYNAMIC 二进制文件 (build/platform/generic/firmware/fw_dynamic.bin) 可用于 U-Boot，请将其复制到 U-Boot 根目录或使用 OPENSBI 环境变量指定其位置。 然后，使用以下命令编译 U-Boot。

- 对于 32-bit RISC-V:

```bash
make qemu-riscv32_spl_defconfig
make
```

- 对于 64-bit RISC-V:

```bash
make qemu-riscv64_spl_defconfig
make
```

在 32 位和 64 位配置中运行 U-Boot SPL 的最小 QEMU 命令是：

- 对于 32 位 RISC-V 虚拟机：

```bash
qemu-system-riscv32 -nographic -machine virt -bios spl/u-boot-spl.bin \
-device loader,file=u-boot.itb,addr=0x80200000
```

- 对于 64 位 RISC-V 虚拟机：

```bash
qemu-system-riscv64 -nographic -machine virt -bios spl/u-boot-spl.bin \
-device loader,file=u-boot.itb,addr=0x80200000
```

- 对于64位RISC-V秒杀机：

```bash
qemu-system-riscv64 -nographic -machine spike -bios spl/u-boot-spl.bin \
-device loader,file=u-boot.itb,addr=0x80200000
```

可以通过添加以下内容在 RISC-V 虚拟机中模拟附加磁盘：

```bash
-device ich9-ahci,id=ahci \
-drive if=none,file=riscv64.img,format=raw,id=mydisk \
-device ide-hd,drive=mydisk,bus=ahci.0
```

或者附加一个模拟 UFS：

```bash
-device ufs,id=ufs0 \
-drive if=none,file=test.img,format=raw,id=lun0 \
-device ufs-lu,drive=lun0,bus=ufs0
```

您必须运行“scsi scan”才能使用它们。

通过删除“-ngraphic”并添加以下内容，可以在 RISC-V 虚拟机中模拟视频控制台：

```bash
-serial stdio -device VGA
```

此外，通过添加以下内容，可以将 USB 键盘作为输入设备的选项连接到 RISC-V 虚拟机中的模拟 xHCI 控制器：

```bash
-device qemu-xhci,id=xhci -device usb-kbd,bus=xhci.0
```


# 5. Running with KVM

使用 KVM 运行 QEMU 需要由 qemu-riscv64_smode_defconfig 创建的 S 模式 U-Boot 二进制文件。

提供 U-Boot S 模式 ELF 映像作为 -kernel 参数，并且不要添加 -bios 参数，例如

```bash
qemu-system-riscv64 -accel kvm -nographic -machine virt -kernel u-boot
```


# 6. Debug UART

以下设置为虚拟机提供调试 UART：

```bash
CONFIG_DEBUG_UART=y
CONFIG_DEBUG_UART_NS16550=y
CONFIG_DEBUG_UART_BASE=0x10000000
CONFIG_DEBUG_UART_CLOCK=3686400
```
