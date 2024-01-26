
# 2. The PCI Express Port Bus Driver Guide HOWTO

Author: Tom L Nguyen tom.l.nguyen@intel.com 11/03/2004

Copyright © 2004 Intel Corporation


## 2.1. About this guide

本指南介绍 PCI Express 端口总线驱动程序的基础知识，并提供有关如何启用服务驱动程序向 PCI Express 端口总线驱动程序注册/取消注册的信息。


## 2.2. What is the PCI Express Port Bus Driver

PCI Express 端口是逻辑 PCI-PCI 桥结构。 PCI Express 端口有两种类型：根端口和交换机端口。 根端口从 PCI Express 根联合体发起 PCI Express 链路，而交换机端口将 PCI Express 链路连接到内部逻辑 PCI 总线。 交换机端口的辅助总线代表交换机的内部路由逻辑，称为交换机的上行端口。 交换机的下游端口从交换机的内部路由总线桥接到代表来自 PCI Express 交换机的下游 PCI Express 链路的总线。

PCI Express 端口最多可以提供四种不同的功能（在本文档中称为服务），具体取决于其端口类型。 PCI Express 端口的服务包括本机热插拔支持 (HP)、电源管理事件支持 (PME)、高级错误报告支持 (AER) 和虚拟通道支持 (VC)。 这些服务可以由单个复杂的驱动程序处理，或者由相应的服务驱动程序单独分布和处理。


## 2.3. Why use the PCI Express Port Bus Driver?

在现有的 Linux 内核中，Linux 设备驱动程序模型允许仅由单个驱动程序处理物理设备。 PCI Express 端口是具有多种不同服务的 PCI-PCI 桥设备。 为了保持干净简单的解决方案，每个服务可能有自己的软件服务驱动程序。 在这种情况下，多个服务驱动程序将竞争单个 PCI-PCI 桥设备。 例如，如果首先加载 PCI Express 根端口本机热插拔服务驱动程序，则它会声明 PCI-PCI 桥根端口。 因此，内核不会加载该根端口的其他服务驱动程序。 换句话说，使用当前的驱动程序模型不可能在 PCI-PCI 桥设备上同时加载和运行多个服务驱动程序。

要使多个服务驱动程序同时运行，需要有一个 PCI Express 端口总线驱动程序，该驱动程序管理所有填充的 PCI Express 端口，并根据需要将所有提供的服务请求分发到相应的服务驱动程序。 下面列出了使用 PCI Express 端口总线驱动程序的一些主要优点：

- 允许多个服务驱动程序在 PCI-PCI 桥端口设备上同时运行。
- 允许以独立的分阶段方法实施服务驱动程序。
- 允许一个服务驱动程序在多个 PCI-PCI 桥端口设备上运行。
- 管理 PCI-PCI 桥端口设备的资源并将其分配给请求的服务驱动程序。


## 2.4. Configuring the PCI Express Port Bus Driver vs. Service Drivers

### 2.4.1. Including the PCI Express Port Bus Driver Support into the Kernel

包含 PCI Express 端口总线驱动程序取决于内核配置中是否包含 PCI Express 支持。 当内核中启用 PCI Express 支持时，内核将自动包含 PCI Express 端口总线驱动程序作为内核驱动程序。


### 2.4.2. Enabling Service Driver Support

PCI设备驱动程序是基于Linux设备驱动程序模型实现的。 所有服务驱动程序都是 PCI 设备驱动程序。 如上所述，一旦内核加载了 PCI Express 端口总线驱动程序，就不可能加载任何服务驱动程序。 为了满足 PCI Express 端口总线驱动程序模型，需要对现有服务驱动程序进行一些最小的更改，而不会影响现有服务驱动程序的功能。

服务驱动程序需要使用下面所示的两个 API 来向 PCI Express 端口总线驱动程序注册其服务（请参阅第 5.2.1 和 5.2.2 节）。 服务驱动程序在调用这些 API 之前初始化 pcie_port_service_driver 数据结构（包含在头文件 /include/linux/pcieport_if.h 中）非常重要。 否则将导致身份不匹配，从而阻止 PCI Express 端口总线驱动程序加载服务驱动程序。


#### 2.4.2.1. pcie_port_service_register

```c
int pcie_port_service_register(struct pcie_port_service_driver *new)
```

此 API 取代了 Linux 驱动程序模型的 pci_register_driver API。 服务驱动程序应始终在模块初始化时调用 pcie_port_service_register。 请注意，加载服务驱动程序后，不再需要诸如 pci_enable_device(dev) 和 pci_set_master(dev) 之类的调用，因为这些调用是由 PCI 端口总线驱动程序执行的。


#### 2.4.2.2. pcie_port_service_unregister

```c
void pcie_port_service_unregister(struct pcie_port_service_driver *new)
```

pcie_port_service_unregister 替换了 Linux 驱动程序模型的 pci_unregister_driver。 当模块退出时，它总是由服务驱动程序调用。


#### 2.4.2.3. Sample Code

下面是用于初始化端口服务驱动程序数据结构的示例服务驱动程序代码。

```c
static struct pcie_port_service_id service_id[] = { {
  .vendor = PCI_ANY_ID,
  .device = PCI_ANY_ID,
  .port_type = PCIE_RC_PORT,
  .service_type = PCIE_PORT_SERVICE_AER,
  }, { /* end: all zeroes */ }
};

static struct pcie_port_service_driver root_aerdrv = {
  .name               = (char *)device_name,
  .id_table   = &service_id[0],

  .probe              = aerdrv_load,
  .remove             = aerdrv_unload,

  .suspend    = aerdrv_suspend,
  .resume             = aerdrv_resume,
};
```

下面是注册/注销服务驱动程序的示例代码。

```c
static int __init aerdrv_service_init(void)
{
  int retval = 0;

  retval = pcie_port_service_register(&root_aerdrv);
  if (!retval) {
    /*
    * FIX ME
    */
  }
  return retval;
}

static void __exit aerdrv_service_exit(void)
{
  pcie_port_service_unregister(&root_aerdrv);
}

module_init(aerdrv_service_init);
module_exit(aerdrv_service_exit);
```


## 2.5. Possible Resource Conflicts

由于 PCI-PCI 桥端口设备的所有服务驱动程序都允许同时运行，因此下面列出了与建议的解决方案可能发生的一些资源冲突。


### 2.5.1. MSI and MSI-X Vector Resource

一旦设备上启用了 MSI 或 MSI-X 中断，它就会保持此模式，直到再次禁用为止。 由于同一 PCI-PCI 桥端口的服务驱动程序共享相同的物理设备，因此如果单个服务驱动程序启用或禁用 MSI/MSI-X 模式，则可能会导致不可预测的行为。

为了避免这种情况，所有服务驱动程序都不允许在其设备上切换中断模式。 PCI Express 端口总线驱动程序负责确定中断模式，这对于服务驱动程序应该是透明的。 服务驱动程序只需要知道分配给 struct pcie_device 的 irq 字段的向量 IRQ，该向量 IRQ 在 PCI Express 端口总线驱动程序探测每个服务驱动程序时传入。 服务驱动程序应使用 (struct pcie_device*)dev->irq 来调用 request_irq/free_irq。 另外，中断模式存储在struct pcie_device的interrupt_mode字段中。


### 2.5.2. PCI Memory/IO Mapped Regions

PCI Express 电源管理 (PME)、高级错误报告 (AER)、热插拔 (HP) 和虚拟通道 (VC) 的服务驱动程序访问 PCI Express 端口上的 PCI 配置空间。 在所有情况下，访问的寄存器都是彼此独立的。 此补丁假定所有服务驱动程序都表现良好并且不会覆盖其他服务驱动程序的配置设置。


### 2.5.3. PCI Config Registers

每个服务驱动程序都在其自己的功能结构上运行其 PCI 配置操作，但 PCI Express 功能结构除外，该结构在包括服务驱动程序在内的许多驱动程序之间共享。 RMW 功能访问器（pcie_capability_clear_and_set_word()、pcie_capability_set_word() 和 pcie_capability_clear_word()）保护一组选定的 PCI Express 功能寄存器（链路控制寄存器和根控制寄存器）。 对这些寄存器的任何更改都应使用 RMW 访问器执行，以避免并发更新引起的问题。 有关受保护寄存器的最新列表，请参见 pcie_capability_clear_and_set_word()。
