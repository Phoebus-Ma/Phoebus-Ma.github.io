
# QEMU PPC E500

PPC 的 QEMU 支持专为仿真和虚拟化目的而设计的特殊“ppce500”机器。 本文档介绍了如何在其下运行U-Boot。

QEMU ppce500 机器模拟通用 PowerPC e500 虚拟机，支持连接到内置 PCI 主机控制器的 VirtIO 标准网络设备。 对 CCSBAR 空间中的一些常见设备进行了建模，包括 MPIC、16550A UART 设备、GPIO、I2C 和 PCI 主机控制器，并将 MSI 传送到 MPIC。 它使用设备树将配置信息传递给客户软件。


# 1. Building U-Boot

像往常一样设置 CROSS_COMPILE 环境变量，然后运行：

```bash
$ make qemu-ppce500_defconfig
$ make
```


# 2. Running U-Boot

启动并运行 U-Boot 的最小 QEMU 命令行是：

```bash
$ qemu-system-ppc -nographic -machine ppce500 -bios u-boot
```

您还可以使用“qemu-system-ppc64”运行 U-Boot：

```bash
$ qemu-system-ppc64 -nographic -machine ppce500 -bios u-boot
```

上面的命令默认创建一个具有 128 MiB 内存的目标。 可以通过“-m”参数创建可自由配置的 RAM 量。 例如，“-m 2G”为目标创建 2 GiB 内存，QEMU 创建的嵌入式 DTB 中的内存节点反映了新设置。

qemu-system-ppc 和 qemu-system-ppc64 都提供对以下 32 位 PowerPC CPU 的仿真：

- e500v1
- e500v2
- e500mc

此外，qemu-system-ppc64 还提供对以下 64 位 CPU 的支持：

- e5500
- e6500

CPU 类型可以通过“-cpu”命令行指定。 如果未指定，它将创建一台具有 e500v2 核心的机器。 以下示例显示了基于 e6500 的计算机创建：

```bash
$ qemu-system-ppc64 -nographic -machine ppce500 -cpu e6500 -bios u-boot
```

当U-Boot启动时，您会注意到以下内容：

```bash
CPU:   Unknown, Version: 0.0, (0x00000000)
Core:  e6500, Version: 2.0, (0x80400020)
```

这是因为我们只为 QEMU 指定了一个核心名称，并且它没有有意义的 SVR 值来代表集成该核心的实际 SoC。 您可以指定 QEMU 内置支持的真实世界 SoC 设备，但所有这些 SoC 都是基于 e500v1/e500v2 的 MPC85xx 系列，因此您无法测试为 P10xx/P2010/P2020 (e500v2)、P204x/P304x/P40xx (e500mc) 构建的任何内容 ）、P50xx/T10xx (e5500) 和 T208x/T4080/T4160/T4240 (e6500)。

默认情况下，VirtIO 标准 PCI 网络设备作为以太网接口连接到 PCI 地址 0.1.0，但我们可以通过以下方式将其切换到 e1000 NIC：

```bash
$ qemu-system-ppc -nographic -machine ppce500 -bios u-boot \
                  -nic tap,ifname=tap0,script=no,downscript=no,model=e1000
```

如果为 QEMU 提供“-device eTSEC”，则 QEMU ppce500 机器还可以动态实例化 eTSEC 设备：

```bash
-netdev tap,ifname=tap0,script=no,downscript=no,id=net0 -device eTSEC,netdev=net0
```

VirtIO BLK 驱动程序还支持从存储内核映像的磁盘映像启动。 将以下内容附加到 QEMU：

```bash
-drive file=disk.img,format=raw,id=disk0 -device virtio-blk-pci,drive=disk0
```

支持 Pericom pt7c4338 RTC，因此我们可以使用“date”命令：

```bash
=> date
Date: 2021-02-18 (Thursday)    Time: 15:33:20
```

此外，还支持“poweroff”命令来关闭 QEMU 会话：

```bash
=> poweroff
poweroff ...
```

这些已经在 QEMU 5.2.0 中进行了测试。
