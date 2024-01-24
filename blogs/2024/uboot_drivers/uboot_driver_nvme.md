
# NVMe Support

# 1. What is NVMe

NVM Express (NVMe) 是一种寄存器级接口，允许主机软件与非易失性内存子系统进行通信。 该接口针对企业和客户端固态驱动器进行了优化，通常连接到 PCI Express 接口。 它是一个可扩展的主机控制器接口，旨在满足使用基于 PCI Express 的固态硬盘 (SSD) 的企业和客户端系统的需求。 该界面提供优化的命令提交和完成路径。 它支持并行操作，支持最多 64K I/O 队列，每个 I/O 队列最多支持 64K 命令。

该设备由一定数量的控制器组成，其中每个控制器由一定数量的命名空间组成，其中每个命名空间由一定数量的逻辑块组成。 命名空间是格式化为逻辑块的一定数量的非易失性存储器。 NVMe 命名空间相当于 SCSI LUN。 每个命名空间都作为一个独立的“设备”进行操作。


# 2. How it works

有一个 NVMe uclass 驱动程序（驱动程序名称“nvme”）、一个 NVMe 主机控制器驱动程序（驱动程序名称“nvme”）和一个 NVMe 命名空间块驱动程序（驱动程序名称“nvme_blk”）。 主机控制器驱动程序应该探测硬件并进行必要的初始化，以使控制器进入就绪状态，在该状态下它能够扫描附加到它的所有可用命名空间。 扫描命名空间由 NVMe uclass 驱动程序触发，实际工作在 NVMe 命名空间块驱动程序中完成。


# 3. Status

它仅支持NVMe驱动程序中的基本块读/写功能。


# 4. Config options

CONFIG_NVME 启用 NVMe 设备支持 CONFIG_NVME_PCI 启用 PCIe NVMe 设备支持 CONFIG_CMD_NVME 启用基本 NVMe 命令。


# 5. Usage in U-Boot

要从 U-Boot shell 使用 NVMe 硬盘，需要执行“nvme scan”命令以识别连接到 NVMe 控制器的所有 NVMe 硬盘。

要列出所有 NVMe 硬盘，请尝试：

```bash
=> nvme info
Device 0: Vendor: 0x8086 Rev: 8DV10131 Prod: CVFT535600LS400BGN
          Type: Hard Disk
          Capacity: 381554.0 MB = 372.6 GB (781422768 x 512)
```

并通过以下方式打印控制器和命名空间的详细信息：

```bash
=> nvme detail
```

原始块读/写可以通过“nvme读/写”命令完成：

```bash
=> nvme read a0000000 0 11000

=> tftp 80000000 /tftpboot/kernel.itb
=> nvme write 80000000 0 11000
```

当然，NVMe硬盘也可以使用文件系统命令：

```bash
=> fatls nvme 0:1
      32376967   kernel.itb
      22929408   100m

      2 file(s), 0 dir(s)

=> fatload nvme 0:1 a0000000 /kernel.itb
=> bootm a0000000
```


# 6. Testing NVMe with QEMU x86

QEMU 支持 NVMe 仿真，我们可以使用运行 U-Boot 的 QEMU x86 来测试 NVMe 驱动程序。 请参阅 README.x86 了解如何为 QEMU x86 构建 u-boot.rom 映像。

下面使用模拟 NVMe 设备调用 QEMU x86 的示例命令行：

```bash
$ ./qemu-system-i386 -drive file=nvme.img,if=none,id=drv0 -device nvme,drive=drv0,serial=QEMUNVME0001 -bios u-boot.rom
```
