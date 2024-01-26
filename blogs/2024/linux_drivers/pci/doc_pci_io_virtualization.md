
# 3. PCI Express I/O Virtualization Howto

Copyright © 2009 Intel Corporation

Authors:

- Yu Zhao <yu.zhao@intel.com>
- Donald Dutile <ddutile@redhat.com>


## 3.1. Overview

### 3.1.1. What is SR-IOV

单根 I/O 虚拟化 (SR-IOV) 是一种 PCI Express 扩展功能，它使一个物理设备显示为多个虚拟设备。 物理设备称为物理功能(PF)，而虚拟设备称为虚拟功能(VF)。 VF 的分配可以由 PF 通过封装在功能中的寄存器动态控制。 默认情况下，此功能未启用，并且 PF 的行为与传统 PCIe 设备相同。 一旦开启，每个VF的PCI配置空间都可以通过自己的总线、设备和功能号（路由ID）来访问。 并且每个VF还有PCI Memory Space，用于映射其寄存器组。 VF 设备驱动程序在寄存器组上运行，因此它可以正常工作并显示为真实的现有 PCI 设备。


## 3.2. User Guide

### 3.2.1. How can I enable SR-IOV capability

有多种方法可用于启用 SR-IOV。 在第一种方法中，设备驱动程序（PF 驱动程序）将通过 SR-IOV 内核提供的 API 来控制功能的启用和禁用。 如果硬件具有 SR-IOV 功能，则加载其 PF 驱动程序将启用它以及与 PF 关联的所有 VF。 某些 PF 驱动程序需要设置模块参数来确定要启用的 VF 数量。 在第二种方法中，写入 sysfs 文件 sriov_numvfs 将启用和禁用与 PCIe PF 关联的 VF。 与适用于同一设备的所有 PF 的第一种方法相比，此方法启用每个 PF、VF 启用/禁用值。 此外，PCI SRIOV 核心支持确保启用/禁用操作有效，以减少多个驱动程序中进行相同检查的重复，例如，如果启用 VF，则检查 numvfs == 0，确保 numvfs <= Totalvfs。 第二种方法是新的/未来的 VF 设备的推荐方法。


### 3.2.2. How can I use the Virtual Functions

VF 在内核中被视为热插拔 PCI 设备，因此它们应该能够像真正的 PCI 设备一样工作。 VF 需要与普通 PCI 设备相同的设备驱动程序。


## 3.3. Developer Guide

### 3.3.1. SR-IOV API

要启用 SR-IOV 功能：

a. 对于第一种方法，在驱动程序中：

```c
int pci_enable_sriov(struct pci_dev *dev, int nr_virtfn);
```

'nr_virtfn' 是要启用的 VF 的数量。

b. 对于第二种方法，来自 sysfs：

```bash
echo 'nr_virtfn' > \
/sys/bus/pci/devices/<DOMAIN:BUS:DEVICE.FUNCTION>/sriov_numvfs
```

要禁用 SR-IOV 功能：

a. 对于第一种方法，在驱动程序中：

```c
void pci_disable_sriov(struct pci_dev *dev);
```

b. 对于第二种方法，来自 sysfs：

```bash
echo  0 > \
/sys/bus/pci/devices/<DOMAIN:BUS:DEVICE.FUNCTION>/sriov_numvfs
```

要通过主机上的兼容驱动程序启用自动探测 VF，请在启用 SR-IOV 功能之前运行以下命令。 这是默认行为。

```bash
echo 1 > \
/sys/bus/pci/devices/<DOMAIN:BUS:DEVICE.FUNCTION>/sriov_drivers_autoprobe
```

要通过主机上的兼容驱动程序禁用自动探测 VF，请在启用 SR-IOV 功能之前运行以下命令。 更新此条目不会影响已探测到的 VF。

```bash
echo  0 > \
/sys/bus/pci/devices/<DOMAIN:BUS:DEVICE.FUNCTION>/sriov_drivers_autoprobe
```


### 3.3.2. Usage example

以下代码段说明了 SR-IOV API 的用法。

```c
static int dev_probe(struct pci_dev *dev, const struct pci_device_id *id)
{
        pci_enable_sriov(dev, NR_VIRTFN);

        ...

        return 0;
}

static void dev_remove(struct pci_dev *dev)
{
        pci_disable_sriov(dev);

        ...
}

static int dev_suspend(struct device *dev)
{
        ...

        return 0;
}

static int dev_resume(struct device *dev)
{
        ...

        return 0;
}

static void dev_shutdown(struct pci_dev *dev)
{
        ...
}

static int dev_sriov_configure(struct pci_dev *dev, int numvfs)
{
        if (numvfs > 0) {
                ...
                pci_enable_sriov(dev, numvfs);
                ...
                return numvfs;
        }
        if (numvfs == 0) {
                ....
                pci_disable_sriov(dev);
                ...
                return 0;
        }
}

static struct pci_driver dev_driver = {
        .name =         "SR-IOV Physical Function driver",
        .id_table =     dev_id_table,
        .probe =        dev_probe,
        .remove =       dev_remove,
        .driver.pm =    &dev_pm_ops,
        .shutdown =     dev_shutdown,
        .sriov_configure = dev_sriov_configure,
};
```
