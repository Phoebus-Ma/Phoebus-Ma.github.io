
# VirtIO Support

本文档介绍了有关 [VirtIO](http://docs.oasis-open.org/virtio/virtio/v1.0/virtio-v1.0.pdf) 设备的 U-Boot 支持信息，包括支持的主板、版本 说明、驱动程序详细信息等


# 1. What’s VirtIO?

VirtIO 是网络和磁盘设备驱动程序的虚拟化标准，其中只有来宾设备驱动程序“知道”它正在虚拟环境中运行，并与虚拟机管理程序协作。 这使来宾能够获得高性能的网络和磁盘操作，并提供半虚拟化的大部分性能优势。 在 U-Boot 情况下，来宾是 U-Boot 本身，而虚拟环境通常是 [QEMU](https://www.qemu.org/) 目标，例如 ARM、RISC-V 和 x86。


# 2. Status

VirtIO 可以使用各种不同的总线，也称为规范中描述的传输。 虽然 VirtIO 设备通常在 x86 上实现为 PCI 设备，但通常不支持 PCI 的嵌入式设备模型（如 ARM/RISC-V）可能会使用简单内存映射设备 (MMIO) 而不是 PCI 设备。 内存映射的 virtio 设备行为基于 PCI 设备规范。 因此，大多数操作（包括设备初始化、队列配置和缓冲区传输）几乎相同。 U-Boot 支持 MMIO 和 PCI 传输选项。

VirtIO 规范定义了许多 VirtIO 设备类型，但目前仅支持网络和块设备这两种最常用的设备。

支持以下 QEMU 目标。

- qemu_arm_defconfig
- qemu_arm64_defconfig
- qemu-riscv32_defconfig
- qemu-riscv64_defconfig
- qemu-x86_defconfig
- qemu-x86_64_defconfig

注意 ARM 和 RISC-V 目标配置有 VirtIO MMIO 传输驱动程序，在 x86 上它是 PCI 传输驱动程序。


# 3. Build Instructions

为预配置的 QEMU 目标构建 U-Boot 与其他目标没有什么不同。 例如，我们可以通过将 CROSS_COMPILE 环境变量正确设置为 ARM 的工作工具链来执行以下操作：

```bash
$ make qemu_arm_defconfig
$ make
```

您甚至可以创建 QEMU ARM 目标，其中 VirtIO 设备同时显示在 MMIO 和 PCI 总线上。 在这种情况下，您可以通过“make menuconfig”启用 PCI 传输驱动程序：

```bash
Device Drivers  --->
      ...
      VirtIO Drivers  --->
              ...
              [*] PCI driver for virtio devices
```

其他驱动程序位于同一位置，可以根据需要进行调整。


# 4. Requirements

要求使用 QEMU v2.5.0+ 来测试 QEMU ARM 和 x86 上的 U-Boot VirtIO 支持，以及 QEMU RISC-V 上的 v2.12.0+ 测试支持。


# 5. Testing

以下 QEMU 命令行用于启动 U-Boot 并在 ARM 上与 VirtIO 网络和块设备一起运行。

```bash
$ qemu-system-arm -nographic -machine virt -bios u-boot.bin \
  -netdev tap,ifname=tap0,id=net0 \
  -device virtio-net-device,netdev=net0 \
  -drive if=none,file=test.img,format=raw,id=hd0 \
  -device virtio-blk-device,drive=hd0
```

在 x86 上，创建 PCI VirtIO 设备的命令略有不同。

```bash
$ qemu-system-i386 -nographic -bios u-boot.rom \
  -netdev tap,ifname=tap0,id=net0 \
  -device virtio-net-pci,netdev=net0 \
  -drive if=none,file=test.img,format=raw,id=hd0 \
  -device virtio-blk-pci,drive=hd0
```

可以通过附加更多“-device”参数来创建其他网络和块设备。 还可以指定 MMIO 和 PCI VirtIO 设备。 例如，以下命令创建 3 个 VirtIO 设备，其中 1 个位于 MMIO 上，2 个位于 PCI 总线上。

```bash
$ qemu-system-arm -nographic -machine virt -bios u-boot.bin \
  -netdev tap,ifname=tap0,id=net0 \
  -device virtio-net-pci,netdev=net0 \
  -drive if=none,file=test0.img,format=raw,id=hd0 \
  -device virtio-blk-device,drive=hd0 \
  -drive if=none,file=test1.img,format=raw,id=hd1 \
  -device virtio-blk-pci,drive=hd1
```

默认情况下，QEMU 创建 VirtIO 遗留设备。 要创建非旧版（又称现代）设备，请传递其他设备属性/值对，如下所示：

```bash
$ qemu-system-i386 -nographic -bios u-boot.rom \
  -netdev tap,ifname=tap0,id=net0 \
  -device virtio-net-pci,netdev=net0,disable-legacy=true,disable-modern=false \
  -drive if=none,file=test.img,format=raw,id=hd0 \
  -device virtio-blk-pci,drive=hd0,disable-legacy=true,disable-modern=false
```

U-Boot shell 中提供了“virtio”命令。

```bash
=> virtio
virtio - virtio block devices sub-system

Usage:
virtio scan - initialize virtio bus
virtio info - show all available virtio block devices
virtio device [dev] - show or set current virtio block device
virtio part [dev] - print partition table of one or all virtio block devices
virtio read addr blk# cnt - read `cnt' blocks starting at block
     `blk#' to memory address `addr'
virtio write addr blk# cnt - write `cnt' blocks starting at block
     `blk#' from memory address `addr'
```

要探测所有 VirtIO 设备，请键入：

```bash
=> virtio scan
```

然后我们可以通过以下方式显示连接的块设备详细信息：

```bash
=> virtio info
Device 0: QEMU VirtIO Block Device
            Type: Hard Disk
            Capacity: 4096.0 MB = 4.0 GB (8388608 x 512)
```

并通过以下方式列出磁盘上的目录和文件：

```bash
=> ls virtio 0 /
<DIR>       4096 .
<DIR>       4096 ..
<DIR>      16384 lost+found
<DIR>       4096 dev
<DIR>       4096 proc
<DIR>       4096 sys
<DIR>       4096 var
<DIR>       4096 etc
<DIR>       4096 usr
<SYM>          7 bin
<SYM>          8 sbin
<SYM>          7 lib
<SYM>          9 lib64
<DIR>       4096 run
<DIR>       4096 boot
<DIR>       4096 home
<DIR>       4096 media
<DIR>       4096 mnt
<DIR>       4096 opt
<DIR>       4096 root
<DIR>       4096 srv
<DIR>       4096 tmp
               0 .autorelabel
```


# 6. Driver Internals

VirtIO 驱动程序系列中有 3 个级别的驱动程序。

```bash
+---------------------------------------+
|        virtio device drivers          |
|    +-------------+ +------------+     |
|    | virtio-net  | | virtio-blk |     |
|    +-------------+ +------------+     |
+---------------------------------------+
+---------------------------------------+
|       virtio transport drivers        |
|    +-------------+ +------------+     |
|    | virtio-mmio | | virtio-pci |     |
|    +-------------+ +------------+     |
+---------------------------------------+
        +----------------------+
        | virtio uclass driver |
        +----------------------+
```

根驱动程序是 virtio uclass 驱动程序 (virtio-uclass.c)，它为传输驱动程序（virtio_mmio.c、virtio_pci.c）执行许多常见操作。 真正的virtio设备是在传输驱动程序的probe()方法中发现的，它的设备ID保存在传输设备的virtio uclass的私有数据中。 然后在 virtio uclass 的 post_probe() 方法中，如果设备 ID 匹配，则绑定真正的 virtio 设备驱动程序（virtio_net.c、virtio_blk.c）。

virtio uclass 驱动程序的 child_post_bind()、child_pre_probe() 和 child_post_probe() 方法有助于使 virtio 设备驱动程序联机。 它们执行诸如确认设备、功能协商等操作，这些对于所有 virtio 设备来说都很常见。

传输驱动程序提供一组操作（struct dm_virtio_ops）供真正的 virtio 设备驱动程序调用。 这些ops API的参数旨在提醒调用者传递virtio设备的正确“struct udevice”id，例如：

```c
int virtio_get_status(struct udevice *vdev, u8 *status)
```

所以参数‘vdev’表明该设备应该是真正的virtio设备。 但我们也有一个 API，例如：

```c
struct virtqueue *vring_create_virtqueue(unsigned int index, unsigned int num,
                                       unsigned int vring_align,
                                       struct udevice *udev)
```

这里参数‘udev’表示该设备应该是传输设备。 类似的命名也适用于其他甚至不是 API 的函数，例如：

```c
static int virtio_uclass_post_probe(struct udevice *udev)
static int virtio_uclass_child_pre_probe(struct udevice *vdev)
```

因此很容易判断这些功能在哪个设备上运行。


# 7. Development Flow

目前仅支持 VirtIO 网卡（设备 ID 1）和块设备（设备 ID 2）。 如果您想为新设备开发新驱动程序，请遵循以下指南。

1. 增加新的设备ID 在 virtio.h

```c
#define VIRTIO_ID_XXX         X
```

2. 将 VIRTIO_ID_MAX_NUM 更新为最大设备 ID 加 1
3. 添加新的驱动程序名称字符串在 virtio.h

```c
#define VIRTIO_XXX_DRV_NAME   "virtio-xxx"
```

创建一个新的驱动程序，名称设置为上面的名称字符串。

```c
U_BOOT_DRIVER(virtio_xxx) = {
      .name = VIRTIO_XXX_DRV_NAME,
      ...
      .remove = virtio_reset,
      .flags = DM_FLAG_ACTIVE_DMA,
}
```

请注意，驱动程序需要提供删除方法，通常可以将其挂接到 virtio_reset()。 驱动程序标志应包含 DM_FLAG_ACTIVE_DMA，以便在跳转到操作系统之前调用删除方法。

5. 在驱动程序中提供bind()方法，其中应该调用virtio_driver_features_init()以便驱动程序与设备协商功能支持。

6. 和驱动一起做一些有趣的事情。
