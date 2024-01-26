
# 10. Boot Interrupts

Author: Sean V Kelley <sean.v.kelley@linux.intel.com>


## 10.1. Overview

在 PCI Express 上，中断由 MSI 或入站中断消息 (Assert_INTx/Deassert_INTx) 表示。 给定 Core IO 中的集成 IO-APIC 将传统中断消息从 PCI Express 转换为 MSI 中断。 如果 IO-APIC 被禁用（通过 IO-APIC 表条目中的掩码位），则消息将路由到旧 PCH。 传统上，对于不支持 IO-APIC 和引导的系统来说，这种带内中断机制是必需的。 英特尔过去曾使用术语“启动中断”来描述这种机制。 此外，PCI Express 协议还描述了 I/O 设备的带内传统线路中断 INTx 机制，以发出 PCI 类型级别的中断信号。 后续段落描述了 Core IO 处理 INTx 消息路由至 PCH 的问题以及 BIOS 和操作系统内的缓解措施。


## 10.2. Issue

当带内传统 INTx 消息转发到 PCH 时，它们会依次触发一个新中断，操作系统可能缺少该中断的处理程序。 当中断随着时间的推移而未被处理时，Linux 内核会将其作为虚假中断进行跟踪。 当 IRQ 达到特定计数后，Linux 内核将禁用该 IRQ，并显示错误“无人关心”。 现在，此禁用的 IRQ 会阻止现有中断的有效使用，而现有中断可能会共享 IRQ 线：

```bash
irq 19: nobody cared (try booting with the "irqpoll" option)
CPU: 0 PID: 2988 Comm: irq/34-nipalk Tainted: 4.14.87-rt49-02410-g4a640ec-dirty #1
Hardware name: National Instruments NI PXIe-8880/NI PXIe-8880, BIOS 2.1.5f1 01/09/2020
Call Trace:

<IRQ>
 ? dump_stack+0x46/0x5e
 ? __report_bad_irq+0x2e/0xb0
 ? note_interrupt+0x242/0x290
 ? nNIKAL100_memoryRead16+0x8/0x10 [nikal]
 ? handle_irq_event_percpu+0x55/0x70
 ? handle_irq_event+0x4f/0x80
 ? handle_fasteoi_irq+0x81/0x180
 ? handle_irq+0x1c/0x30
 ? do_IRQ+0x41/0xd0
 ? common_interrupt+0x84/0x84
</IRQ>

handlers:
irq_default_primary_handler threaded usb_hcd_irq
Disabling IRQ #19
```


## 10.3. Conditions

如今，使用线程中断是最有可能触发此问题的条件。 IRQ 处理程序唤醒后，线程中断可能无法重新启用。 这些“一次性”条件意味着线程中断需要保持中断线被屏蔽，直到线程处理程序运行为止。 特别是在处理高数据率中断时，线程需要运行完成； 否则，由于发出设备的中断仍然处于活动状态，某些处理程序将最终导致堆栈溢出。


## 10.4. Affected Chipsets

如今，许多设备中都存在传统中断转发机制，包括但不限于 AMD/ATI、Broadcom 和 Intel 的芯片组。 通过以下缓解措施所做的更改已应用于 drivers/pci/quirks.c

从 ICX 开始，Core IO 设备中不再有任何 IO-APIC。 IO-APIC仅在PCH中。 连接到 Core IO 的 PCIe 根端口的设备将使用本机 MSI/MSI-X 机制。


## 10.5. Mitigations

缓解措施采用 PCI 怪癖的形式。 优先选择是首先确定并使用一种方法来禁用到 PCH 的路由。 在这种情况下，可以添加一个怪癖来禁用引导中断生成。 [1]

Intel® 6300ESB I/O Controller Hub

&emsp;备用基地址寄存器：

&emsp;&emsp;BIE: Boot Interrupt Enable

|   |                 |
| - | --------------- |
| 0 | 启动中断已启用。 |
| 1 | 启动中断被禁用。 |

英特尔® Sandy Bridge 通过基于 Sky Lake 的至强服务器：

&emsp;一致接口协议中断控制

&emsp;&emsp;dis_intx_route2pch/dis_intx_route2ich/dis_intx_route2dmi2:

&emsp;&emsp;当该位被设置时。 从英特尔® Quick Data DMA/PCI Express 端口接收的本地 INTx 消息不会路由到传统 PCH - 它们要么通过集成 IO-APIC 转换为 MSI（如果在相应条目中清除了 IO-APIC 掩码位），要么 不引起进一步的操作（当设置掩码位时）。

在没有直接禁用路由的方法的情况下，另一种方法是利用 PCI 中断引脚到 INTx 路由表，默认情况下将中断处理程序重定向到重新路由的中断线。 因此，在无法禁用此 INTx 路由的芯片组上，Linux 内核会将有效中断重新路由到其旧中断。 处理程序的这种重定向将防止发生虚假中断检测，该检测通常会由于过多的未处理计数而禁用 IRQ 线。[2]

配置选项 X86_REROUTE_FOR_BROKEN_BOOT_IRQS 的存在是为了启用（或禁用）中断处理程序到 PCH 中断线的重定向。 该选项可以由 pci=ioapicreroute 或 pci=noioapicreroute 覆盖。[3]


## 10.6. More Documentation

多个数据表（下面的 6300ESB 和 6700PXH）中概述了传统中断处理。 虽然基本相同，但它提供了对芯片组处理演变的深入了解。


### 10.6.1. Example of disabling of the boot interrupt

英特尔® 6300ESB I/O 控制器中枢（文档号 300641-004US）5.7.3 启动中断 <https://www.intel.com/content/dam/doc/datasheet/6300esb-io-controller-hub-datasheet.pdf>

英特尔® 至强® 处理器 E5-1600/2400/2600/4600 v3 产品系列数据表 - 第 2 卷：寄存器（文档号 330784-003）6.6.41 cipintrc 一致接口协议中断控制 <https://www.intel.com/content/dam/www/public/us/en/documents/datasheets/xeon-e5-v3-datasheet-vol-2.pdf>


### 10.6.2. Example of handler rerouting

英特尔® 6700PXH 64 位 PCI 集线器（文档号 302628）2.15.2 PCI Express Legacy INTx 支持和引导中断 <https://www.intel.com/content/dam/doc/datasheet/6700pxh-64-bit-pci-hub-datasheet.pdf>

如果您有任何未解答的传统 PCI 中断问题，请给我发送电子邮件。

Cheers,

&emsp;Sean V Kelley <sean.v.kelley@linux.intel.com>

[1] <https://lore.kernel.org/r/12131949181903-git-send-email-sassmann@suse.de/>

[2] <https://lore.kernel.org/r/12131949182094-git-send-email-sassmann@suse.de/>

[3] <https://lore.kernel.org/r/487C8EA7.6020205@suse.de/>
