
# 8. The PCI Express Advanced Error Reporting Driver Guide HOWTO

Authors

- Long Nguyen <tom.l.nguyen@intel.com>
- Yanmin Zhang <yanmin.zhang@intel.com>

Copyright © 2006 Intel Corporation


## 8.1. Overview

### 8.1.1. About this guide

本指南介绍 PCI Express (PCIe) 高级错误报告 (AER) 驱动程序的基础知识，并提供有关如何使用它以及如何使端点设备的驱动程序符合 PCIe AER 驱动程序的信息。


### 8.1.2. What is the PCIe AER Driver?

PCIe 错误信令可以发生在 PCIe 链路本身上，也可以代表链路上发起的事务。 PCIe 定义了两种错误报告范例：基线功能和高级错误报告功能。 所有 PCIe 组件都需要基线功能，提供一组最小定义的错误报告要求。 高级错误报告功能通过 PCIe 高级错误报告扩展功能结构实现，提供更强大的错误报告。

PCIe AER 驱动程序提供支持 PCIe 高级错误报告功能的基础架构。 PCIe AER 驱动程序提供三个基本功能：

- 如果发生错误，则收集全面的错误信息。
- 向用户报告错误。
- 执行错误恢复操作。

AER 驱动程序仅连接到支持 PCIe AER 功能的根端口和 RCEC。


## 8.2. User Guide

### 8.2.1. Include the PCIe AER Root Driver into the Linux Kernel

PCIe AER 驱动程序是通过 PCIe 端口总线驱动程序连接的根端口服务驱动程序。 如果用户想要使用它，就必须编译驱动程序。 它通过 CONFIG_PCIEAER 启用，这取决于 CONFIG_PCIEPORTBUS。


### 8.2.2. Load PCIe AER Root Driver

某些系统的固件支持 AER。 在固件处理 AER 的同时启用 Linux AER 支持将导致不可预测的行为。 因此，Linux 不会处理 AER 事件，除非固件通过 ACPI _OSC 方法将 AER 控制权授予操作系统。 有关 _OSC 使用的详细信息，请参阅 PCI 固件规范。


### 8.2.3. AER error output

当捕获到 PCIe AER 错误时，错误消息将输出到控制台。 如果它是可纠正的错误，则会将其输出为信息消息。 否则，它会被打印为错误。 因此用户可以选择不同的日志级别来过滤掉可纠正的错误消息。

下面显示了一个示例：

```bash
0000:50:00.0: PCIe Bus Error: severity=Uncorrected (Fatal), type=Transaction Layer, id=0500(Requester ID)
0000:50:00.0:   device [8086:0329] error status/mask=00100000/00000000
0000:50:00.0:    [20] Unsupported Request    (First)
0000:50:00.0:   TLP Header: 04000001 00200a03 05010000 00050100
```

在示例中，“请求者 ID”表示向根端口发送错误消息的设备的 ID。 其他字段请参考PCIe规范。


### 8.2.4. AER Statistics / Counters

捕获 PCIe AER 错误时，计数器/统计信息也会以 sysfs 属性的形式公开，这些属性记录在 Documentation/ABI/testing/sysfs-bus-pci-devices-aer_stats


## 8.3. Developer Guide

要启用错误恢复，软件驱动程序必须提供回调。

为了更好地支持 AER，开发人员需要了解 AER 的工作原理。

PCIe错误分为两类：可纠正错误和不可纠正错误。 此分类基于这些错误的影响，这些错误可能会导致性能下降或功能故障。

可纠正的错误不会对界面的功能造成影响。 PCIe 协议可以在没有任何软件干预或任何数据丢失的情况下恢复。 这些错误由硬件检测并纠正。

与可纠正的错误不同，不可纠正的错误会影响界面的功能。 不可纠正的错误可能会导致特定事务或特定 PCIe 链路不可靠。 根据这些错误情况，不可纠正的错误进一步分为非致命错误和致命错误。 非致命错误会导致特定事务不可靠，但 PCIe 链路本身功能齐全。 另一方面，致命错误会导致链接不可靠。

启用 PCIe 错误报告后，设备在捕获错误时会自动向其上方的根端口发送错误消息。 根端口在接收到错误报告消息后，在内部处理错误消息并将其记录在其 AER 能力结构中。 被记录的错误信息包括将错误报告代理的请求者ID存储到错误源识别寄存器中并相应地设置根错误状态寄存器的错误位。 如果在根错误命令寄存器中启用了 AER 错误报告，则根端口在检测到错误时会生成中断。

请注意，上述错误与 PCIe 层次结构和链路有关。 这些错误不包括任何设备特定错误，因为设备特定错误仍会直接发送到设备驱动程序。


### 8.3.1. Provide callbacks

#### 8.3.1.1. callback reset_link to reset PCIe link

该回调用于在发生致命错误时重置 PCIe 物理链路。 根端口 AER 服务驱动程序提供默认的 Reset_link 函数，但不同的上游端口可能具有不同的重置 PCIe 链路规范，因此上游端口驱动程序可能提供自己的 Reset_link 函数。

第 3.2.2.2 节提供了有关何时调用 reset_link 的更多详细信息。


#### 8.3.1.2. PCI error-recovery callbacks

在执行错误恢复操作时，PCIe AER 根驱动程序使用错误回调来与与相关层次结构关联的下游设备驱动程序进行协调。

数据结构 pci_driver 有一个指针 err_handler，指向由几个回调函数指针组成的 pci_error_handlers。 AER 驱动程序遵循 PCI 错误恢复中定义的规则，但 PCIe 特定部分（例如 Reset_link）除外。 回调的详细定义请参考[PCI错误恢复](https://www.kernel.org/doc/html/latest/PCI/pci-error-recovery.html)。

以下部分指定何时调用错误回调函数。


#### 8.3.1.3. Correctable errors

可纠正的错误不会对界面的功能造成影响。 PCIe 协议可以在没有任何软件干预或任何数据丢失的情况下恢复。 这些错误不需要任何恢复操作。 AER 驱动程序相应地清除设备的可纠正错误状态寄存器并记录这些错误。


#### 8.3.1.4. Non-correctable (non-fatal and fatal) errors

如果错误消息指示非致命错误，则不需要在上游执行链路重置。 AER 驱动程序对相关层次结构中关联的所有驱动程序调用 error_Detected(dev, pci_channel_io_normal)。 例如：

```bash
Endpoint <==> Downstream Port B <==> Upstream Port A <==> Root Port
```

如果上游端口 A 捕获 AER 错误，则层次结构由下游端口 B 和端点组成。

驱动程序可能会返回 PCI_ERS_RESULT_CAN_RECOVER、PCI_ERS_RESULT_DISCONNECT 或 PCI_ERS_RESULT_NEED_RESET，具体取决于它是否可以恢复或者 AER 驱动程序接下来调用 mmio_enabled。

如果错误消息指示致命错误，内核将向相关层次结构中的所有驱动程序广播 error_Detected(dev, pci_channel_io_frozen)。 那么，需要在上游进行链路重置。 由于不同类型的设备可能使用不同的方式来重置链路，因此 AER 端口服务驱动程序需要提供通过 pcie_do_recovery() 函数的回调参数来重置链路的功能。 如果reset_link不为NULL，恢复函数将使用它来重置链接。 如果 error_Detected 返回 PCI_ERS_RESULT_CAN_RECOVER 并且 reset_link 返回 PCI_ERS_RESULT_RECOVERED，则错误处理将转到 mmio_enabled。


### 8.3.2. Frequent Asked Questions

Q:

&emsp;如果 PCIe 设备驱动程序不提供错误恢复处理程序（pci_driver->err_handler 等于 NULL），会发生什么情况？

A:

&emsp;与驱动程序连接的设备将无法恢复。 如果错误是致命的，内核将打印出警告消息。 请参阅第 3 节了解更多信息。

Q:

&emsp;如果上游端口服务驱动程序不提供回调reset_link，会发生什么情况？

A:

&emsp;如果错误是由服务驱动程序附加的上游端口报告的，则致命错误恢复将失败。


## 8.4. Software error injection

调试 PCIe AER 错误恢复代码相当困难，因为很难触发真正的硬件错误。 基于软件的错误注入可用于伪造各种 PCIe 错误。

首先，您应该在内核配置中启用 PCIe AER 软件错误注入，即您的 .config 中应包含以下项目。

CONFIG_PCIEAER_INJECT=y 或 CONFIG_PCIEAER_INJECT=m

使用新内核重新启动或插入模块后，应创建一个名为 /dev/aer_inject 的设备文件。

然后，您需要一个名为 aer-inject 的用户空间工具，可以从以下位置获取该工具：

<https://git.kernel.org/cgit/linux/kernel/git/gong.chen/aer-inject.git/>

有关 aer-inject 的更多信息可以在其源代码文档中找到。
