
# Emulation of block devices

QEMU 可以通过在 qemu-system-<arch> 命令行中添加以下参数来模拟常见的块设备：

- MMC

```bash
-device sdhci-pci,sd-spec-version=3 \
-drive if=none,file=disk.img,format=raw,id=MMC1 \
-device sd-card,drive=MMC1
```

- NVMe

```bash
-drive if=none,file=disk.img,format=raw,id=NVME1 \
-device nvme,drive=NVME1,serial=nvme-1
```

- SATA

```bash
-device ahci,id=ahci0 \
-drive if=none,file=disk.img,format=raw,id=SATA1 \
-device ide-hd,bus=ahci0.0,drive=SATA1
```

- USB

```bash
-device qemu-xhci \
-drive if=none,file=disk.img,format=raw,id=USB1 \
-device usb-storage,drive=USB1
```

- Virtio

```bash
-drive if=none,file=disk.img,format=raw,id=VIRTIO1 \
-device virtio-blk,drive=VIRTIO1
```

笔记

从 v2023.07 开始，U-Boot 没有 virtio-scsi-pci 的驱动程序。
