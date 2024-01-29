
# 1. How To Write Linux PCI Drivers

Authors:

- Martin Mares <mj@ucw.cz>
- Grant Grundler <grundler@parisc-linux.org>

PCI 的世界广阔且充满（大多是令人不快的）惊喜。 由于每种 CPU 架构实现不同的芯片组，并且 PCI 设备有不同的要求（呃，“特性”），因此 Linux 内核中的 PCI 支持并不像人们希望的那么简单。 这篇简短的文章试图向所有潜在的驱动程序作者介绍 PCI 设备驱动程序的 Linux API。

更完整的资源是 Jonathan Corbet、Alessandro Rubini 和 Greg Kroah-Hartman 编写的第三版“Linux 设备驱动程序”。 LDD3 可从以下网站免费获得（根据知识共享许可）： <https://lwn.net/Kernel/LDD3/>.

但是，请记住，所有文档都会受到“位腐烂”的影响。 如果事情没有按照此处所述进行，请参阅源代码。

请将有关 Linux PCI API 的问题/评论/补丁发送到“Linux PCI” <linux-pci@atrey.karlin.mff.cuni.cz> 邮件列表。


## 1.1. Structure of PCI drivers

PCI 驱动程序通过 pci_register_driver()“发现”系统中的 PCI 设备。 事实上，情况恰恰相反。 当 PCI 通用代码发现新设备时，将通知具有匹配“描述”的驱动程序。 详细信息如下。

pci_register_driver() 将大部分设备探测工作留给 PCI 层，并支持在线插入/移除设备 [从而在单个驱动程序中支持热插拔 PCI、CardBus 和 Express-Card]。 pci_register_driver() 调用需要传入函数指针表，从而指示驱动程序的高级结构。

一旦驱动程序知道 PCI 设备并取得所有权，驱动程序通常需要执行以下初始化：

- 启用设备；
- 请求MMIO/IOP资源；
- 设置 DMA 掩码大小（对于相干和流 DMA）；
- 分配并初始化共享控制数据（pci_allocate_coherent()）；
- 访问设备配置空间（如果需要）；
- 注册IRQ处理程序(request_irq());
- 初始化非 PCI（即芯片的 LAN/SCSI/等部分）；
- 启用 DMA/处理引擎。

使用完设备后，可能需要卸载模块，驱动程序需要执行以下步骤：

- 禁止设备生成IRQ；
- 释放IRQ（free_irq()）；
- 停止所有 DMA 活动；
- 释放 DMA 缓冲区（流式和相干式）；
- 从其他子系统（例如 scsi 或 netdev）取消注册；
- 释放MMIO/IOP资源；
- 禁用该设备。

以下各节将介绍其中的大部分主题。 其余的请查看 LDD3 或 <linux/pci.h> 。

如果未配置 PCI 子系统（未设置 CONFIG_PCI），则下面描述的大多数 PCI 函数都被定义为内联函数，要么完全为空，要么只是返回适当的错误代码，以避免驱动程序中出现大量 ifdef。


## 1.2. pci_register_driver() call

PCI 设备驱动程序在初始化期间调用 pci_register_driver()，并使用指向描述驱动程序的结构 (struct pci_driver) 的指针：


struct pci_driver
--------

PCI driver structure

定义：

```c
struct pci_driver {
    const char              *name;
    const struct pci_device_id *id_table;
    int (*probe)(struct pci_dev *dev, const struct pci_device_id *id);
    void (*remove)(struct pci_dev *dev);
    int (*suspend)(struct pci_dev *dev, pm_message_t state);
    int (*resume)(struct pci_dev *dev);
    void (*shutdown)(struct pci_dev *dev);
    int (*sriov_configure)(struct pci_dev *dev, int num_vfs);
    int (*sriov_set_msix_vec_count)(struct pci_dev *vf, int msix_vec_count);
    u32 (*sriov_get_vf_total_msix)(struct pci_dev *pf);
    const struct pci_error_handlers *err_handler;
    const struct attribute_group **groups;
    const struct attribute_group **dev_groups;
    struct device_driver    driver;
    struct pci_dynids       dynids;
    bool driver_managed_dma;
};
```

- 成员

    - name

    驱动名。

    - id_table

    指向驱动程序感兴趣的设备 ID 表的指针。大多数驱动程序应使用 MODULE_DEVICE_TABLE(pci,...) 导出此表。

    - probe

    对于与 ID 表匹配且尚未被其他驱动程序“拥有”的所有 PCI 设备，将调用此探测函数（在对现有设备执行 pci_register_driver() 期间或稍后插入新设备时）。 对于 ID 表中的条目与设备匹配的每个设备，此函数都会传递一个“struct pci_dev *”。 当驱动程序选择获取设备的“所有权”时，探测函数返回零，否则返回错误代码（负数）。 探测函数总是从进程上下文中调用，因此它可以休眠。

    - remove

    每当删除该驱动程序处理的设备时（无论是在驱动程序注销期间还是在手动将其从热插拔插槽中拔出时），都会调用remove()函数。 删除函数总是从进程上下文中调用，因此它可以休眠。

    - suspend

    将设备置于低功耗状态。

    - resume

    将设备从低功耗状态唤醒。 （有关 PCI 电源管理及相关功能的说明，请参见 PCI 电源管理。）

    - shutdown

    挂接到reboot_notifier_list (kernel/sys.c)。 旨在停止任何空闲的 DMA 操作。 对于启用 LAN 唤醒 (NIC) 或在重新启动之前更改设备的电源状态非常有用。 例如 驱动程序/net/e100.c。

    - sriov_configure

    可选的驱动程序回调，允许配置 VF 数量以通过 sysfs“sriov_numvfs”文件启用。

    - sriov_set_msix_vec_count

    PF 驱动程序回调，用于更改 VF 上的 MSI-X 矢量数量。 通过 sysfs“sriov_vf_msix_count”触发。 这将更改 VF 消息控制寄存器中的 MSI-X 表大小。

    - sriov_get_vf_total_msix

    PF 驱动程序回调以获取可分发到 VF 的 MSI-X 矢量总数。

    - err_handler

    查看 PCI Error Recovery

    - groups

    Sysfs 属性组。

    - dev_groups

    附加到设备的属性，一旦绑定到驱动程序就会创建。

    - driver

    驱动程序模型结构。

    - dynids

    动态添加的设备 ID 列表。

    - driver_managed_dma

    设备驱动程序不使用内核 DMA API 进行 DMA。 对于大多数设备驱动程序来说，只要所有 DMA 都是通过内核 DMA API 处理的，就无需关心此标志。 对于一些特殊的驱动程序，例如 VFIO 驱动程序，它们知道如何自己管理 DMA 并设置此标志，以便 IOMMU 层允许它们设置和管理自己的 I/O 地址空间。

ID 表是一个以全零条目结尾的 struct pci_device_id 条目数组。 通常首选使用 static const 的定义。

struct pci_device_id
--------

PCI device ID structure

定义：

```c
struct pci_device_id {
    __u32 vendor, device;
    __u32 subvendor, subdevice;
    __u32 class, class_mask;
    kernel_ulong_t driver_data;
    __u32 override_only;
};

```

- 成员

    - vendor

    要匹配的供应商 ID（或 PCI_ANY_ID）

    - device

    要匹配的设备 ID（或 PCI_ANY_ID）

    - subvendor

    要匹配的子系统供应商 ID（或 PCI_ANY_ID）

    - subdevice

    要匹配的子系统设备 ID（或 PCI_ANY_ID）

    - class

    要匹配的设备类、子类和“接口”。 请参阅 PCI 本地总线规范的附录 D 或 include/linux/pci_ids.h 以获取完整的类列表。 大多数驱动程序不需要指定 class/class_mask，因为供应商/设备通常就足够了。

    - class_mask

    限制比较类字段的哪些子字段。 有关使用示例，请参阅 drivers/scsi/sym53c8xx_2/。

    - driver_data

    驱动程序私有的数据。 大多数驱动程序不需要使用 driver_data 字段。 最佳实践是使用 driver_data 作为等效设备类型的静态列表的索引，而不是将其用作指针。

    - override_only

    仅当 dev->driver_override 是该驱动程序时才匹配。

大多数驱动程序只需要 PCI_DEVICE() 或 PCI_DEVICE_CLASS() 来设置 pci_device_id 表。

新的 PCI ID 可以在运行时添加到设备驱动程序 pci_ids 表中，如下所示：

```bash
echo "vendor device subvendor subdevice class class_mask driver_data" > \
/sys/bus/pci/drivers/{driver}/new_id
```

所有字段均以十六进制值形式传入（无前导 0x）。 供应商和设备字段是强制性的，其他字段是可选的。 用户只需传递必要数量的可选字段：

- 子供应商和子设备字段默认为 PCI_ANY_ID (FFFFFFFF)；
- 类和类掩码字段默认为 0；
- driver_data 默认为 0UL；
- override_only 字段默认为 0。

请注意，driver_data 必须与驱动程序中定义的任何 pci_device_id 条目使用的值匹配。 如果所有 pci_device_id 条目都具有非零 driver_data 值，则这会使 driver_data 字段成为强制字段。

添加后，将针对其（新更新的）pci_ids 列表中列出的任何未声明的 PCI 设备调用驱动程序探测例程。

当驱动程序退出时，它只调用 pci_unregister_driver()，PCI 层会自动为驱动程序处理的所有设备调用删除钩子。


### 1.2.1. "Attributes" for driver functions/data

请在适当的地方标记初始化和清理函数（相应的宏定义在<linux/init.h>中）：

|        |                                       |
| ------ | ------------------------------------- |
| __init | 初始化代码。 驱动程序初始化后丢弃。      |
| __exit | 退出代码。 对于非模块化驱动程序将被忽略。 |

关于何时/何地使用上述属性的提示：

- module_init()/module_exit() 函数（以及所有仅从这些函数中调用的初始化函数）应标记为 __init/__exit。
- 不要标记 struct pci_driver。
- 如果您不确定使用哪个标记，请勿标记函数。 不标记该函数比将函数标记为错误更好。


## 1.3. How to find PCI devices manually

PCI 驱动程序应该有充分的理由不使用 pci_register_driver() 接口来搜索 PCI 设备。 PCI 设备被多个驱动程序控制的主要原因是一个 PCI 设备实现了多个不同的硬件服务。 例如。 组合串行/并行端口/软盘控制器。

可以使用以下结构来执行手动搜索：

按供应商和设备 ID 搜索：

```c
struct pci_dev *dev = NULL;
while (dev = pci_get_device(VENDOR_ID, DEVICE_ID, dev))
        configure_device(dev);
```

按类 ID 搜索（以类似方式迭代）：

```c
pci_get_class(CLASS_ID, dev)
```

按供应商/设备和子系统供应商/设备 ID 搜索：

```c
pci_get_subsys(VENDOR_ID,DEVICE_ID, SUBSYS_VENDOR_ID, SUBSYS_DEVICE_ID, dev).
```

您可以使用常量 PCI_ANY_ID 作为 VENDOR_ID 或 DEVICE_ID 的通配符替换。 例如，这允许搜索来自特定供应商的任何设备。

这些功能是热插拔安全的。 它们增加返回的 pci_dev 的引用计数。 您最终必须（可能在模块卸载时）通过调用 pci_dev_put() 来减少这些设备上的引用计数。


## 1.4. Device Initialization Steps

如简介中所述，大多数 PCI 驱动程序需要以下步骤来进行设备初始化：

- 启用设备；
- 请求MMIO/IOP资源；
- 设置 DMA 掩码大小（对于相干和流 DMA）；
- 分配并初始化共享控制数据（pci_allocate_coherent()）；
- 访问设备配置空间（如果需要）；
- 注册IRQ处理程序(request_irq());
- 初始化非 PCI（即芯片的 LAN/SCSI/等部分）；
- 启用 DMA/处理引擎。

驱动程序可以随时访问 PCI 配置空间寄存器。 （嗯，几乎。运行 BIST 时，配置空间可能会消失……但这只会导致 PCI 总线主控中止，并且配置读取将返回垃圾）。


### 1.4.1. Enable the PCI device

在接触任何设备寄存器之前，驱动程序需要通过调用 pci_enable_device() 来启用 PCI 设备。 这会：

- 如果设备处于挂起状态，则唤醒设备，
- 分配设备的 I/O 和内存区域（如果 BIOS 没有），
- 分配一个 IRQ（如果 BIOS 没有）。

**Note**

pci_enable_device() 可能会失败！ 检查返回值。

**Warning**

操作系统错误：在启用这些资源之前，我们不检查资源分配。 如果我们在调用 pci_enable_device() 之前调用 pci_request_resources()，则该序列会更有意义。 目前，当两个设备分配了相同的范围时，设备驱动程序无法检测到该错误。 这不是一个常见问题，不太可能很快得到解决。

这一点之前已经讨论过，但从 2.6.19 开始没有改变： <https://lore.kernel.org/r/20060302180025.GC28895@flint.arm.linux.org.uk/>

pci_set_master() 将通过设置 PCI_COMMAND 寄存器中的总线主控位来启用 DMA。 如果 BIOS 设置为虚假值，它还会修复延迟计时器值。 pci_clear_master() 将通过清除总线主控位来禁用 DMA。

如果 PCI 设备可以使用 PCI Memory-Write-Invalidate 事务，请调用 pci_set_mwi()。 这会启用 Mem-Wr-Inval 的 PCI_COMMAND 位，并确保缓存行大小寄存器设置正确。 检查 pci_set_mwi() 的返回值，因为并非所有架构或芯片组都支持 Memory-Write-Invalidate。 或者，如果 Mem-Wr-Inval 很好但不是必需的，请调用 pci_try_set_mwi() 以使系统尽最大努力启用 Mem-Wr-Inval。


### 1.4.2. Request MMIO/IOP resources

内存 (MMIO) 和 I/O 端口地址不应直接从 PCI 设备配置空间读取。 使用 pci_dev 结构中的值，因为 PCI“总线地址”可能已通过架构/芯片组特定的内核支持重新映射到“主机物理”地址。

有关如何访问设备寄存器或设备内存，请参阅[io_mapping 函数](https://www.kernel.org/doc/html/latest/driver-api/io-mapping.html)。

设备驱动程序需要调用 pci_request_region() 来验证没有其他设备已在使用相同的地址资源。 相反，驱动程序应在调用 pci_disable_device() 之后调用 pci_release_region()。 这个想法是为了防止两个设备在同一地址范围上发生冲突。

**Tip**

请参阅上面的操作系统 BUG 评论。 目前（2.6.19），驱动程序只能在调用 pci_enable_device() 之后确定 MMIO 和 IO 端口资源可用性。

pci_request_region() 的通用类型是 request_mem_region() （对于 MMIO 范围）和 request_region() （对于 IO 端口范围）。 将它们用于“普通”PCI BAR 未描述的地址资源。

另请参阅下面的 pci_request_selected_regions()。


### 1.4.3. Set the DMA mask size

**Note**

如果以下任何内容没有意义，请参阅使用通用设备的动态 DMA 映射。 本节只是提醒您，驱动程序需要指示设备的 DMA 功能，并不是 DMA 接口的权威来源。

虽然所有驱动程序都应明确指示 PCI 总线主控器的 DMA 功能（例如 32 或 64 位），但具有超过 32 位总线主控器流数据能力的设备需要驱动程序通过调用 dma_set_mask() 来“注册”此功能 适当的参数。 一般来说，这可以在系统 RAM 高于 4G 物理地址的系统上实现更高效的 DMA。

所有 PCI-X 和 PCIe 兼容设备的驱动程序都必须调用 dma_set_mask()，因为它们是 64 位 DMA 设备。

同样，如果设备可以通过调用 dma_set_coherent_mask() 直接寻址系统 RAM 中高于 4G 物理地址的“一致内存”，则驱动程序也必须“注册”此功能。 同样，这包括所有 PCI-X 和 PCIe 兼容设备的驱动程序。 许多 64 位“PCI”设备（PCI-X 之前）和一些 PCI-X 设备支持 64 位 DMA，可处理有效负载（“流”）数据，但不能处理控制（“一致”）数据。


### 1.4.4. Setup shared control data

一旦设置了 DMA 掩码，驱动程序就可以分配“一致”（也称为共享）内存。 有关 DMA API 的完整说明，请参阅[使用通用设备的动态 DMA 映射](https://www.kernel.org/doc/html/latest/core-api/dma-api.html)。 本节只是提醒您需要在设备上启用 DMA 之前完成此操作。


### 1.4.5. Initialize device registers

某些驱动程序需要对特定“功能”字段进行编程或对其他“供应商特定”寄存器进行初始化或重置。 例如。 清除挂起的中断。


### 1.4.6. Register IRQ handler

虽然调用 request_irq() 是此处描述的最后一步，但这通常只是初始化设备的另一个中间步骤。 此步骤通常可以推迟到设备打开使用时。

IRQ 线的所有中断处理程序都应使用 IRQF_SHARED 进行注册，并使用 devid 将 IRQ 映射到设备（请记住，所有 PCI IRQ 线都可以共享）。

request_irq() 将中断处理程序和设备句柄与中断号关联起来。 历史上，中断号代表从 PCI 设备运行到中断控制器的 IRQ 线。 对于 MSI 和 MSI-X（详见下文），中断号是一个 CPU“向量”。

request_irq() 还允许中断。 在注册中断处理程序之前，请确保设备已停顿并且没有任何待处理的中断。

MSI 和 MSI-X 是 PCI 功能。 两者都是“消息信号中断”，通过 DMA 写入本地 APIC 向 CPU 传送中断。 MSI 和 MSI-X 之间的根本区别在于如何分配多个“向量”。 MSI 需要连续的向量块，而 MSI-X 可以分配多个单独的向量。

在调用 request_irq() 之前，可以通过使用 PCI_IRQ_MSI 和/或 PCI_IRQ_MSIX 标志调用 pci_alloc_irq_vectors() 来启用 MSI 功能。 这会导致 PCI 支持将 CPU 矢量数据编程到 PCI 设备功能寄存器中。 许多架构、芯片组或 BIOS 不支持 MSI 或 MSI-X，并且仅使用 PCI_IRQ_MSI 和 PCI_IRQ_MSIX 标志调用 pci_alloc_irq_vectors 将失败，因此也尝试始终指定 PCI_IRQ_LEGACY。

对于 MSI/MSI-X 和旧版 INTx 有不同中断处理程序的驱动程序应在调用 pci_alloc_irq_vectors 后根据 pci_dev 结构中的 msi_enabled 和 msix_enabled 标志选择正确的中断处理程序。

使用 MSI 有（至少）两个很好的理由：

1. 根据定义，MSI 是独占中断向量。 这意味着中断处理程序不必验证其设备导致中断。

2. MSI 避免了 DMA/IRQ 竞争条件。 当 MSI 传送时，保证主机内存的 DMA 对主机 CPU 可见。 这对于数据一致性和避免过时的控制数据都很重要。 此保证允许驱动程序省略 MMIO 读取以刷新 DMA 流。

有关 MSI/MSI-X 用法的示例，请参阅 drivers/infiniband/hw/mthca/ 或 drivers/net/tg3.c。


## 1.5. PCI device shutdown

卸载 PCI 设备驱动程序时，需要执行以下大部分步骤：

- 禁止设备生成IRQ；
- 释放IRQ（free_irq()）；
- 停止所有 DMA 活动；
- 释放 DMA 缓冲区（流式和相干式）；
- 从其他子系统（例如 scsi 或 netdev）取消注册；
- 禁止设备响应MMIO/IO端口地址；
- 释放 MMIO/IO 端口资源。


### 1.5.1. Stop IRQs on the device

如何做到这一点是特定于芯片/设备的。 如果不这样做，当（且仅当）IRQ 与另一个设备共享时，就有可能出现“尖叫中断”。

当共享 IRQ 处理程序“取消挂钩”时，使用同一 IRQ 线的其余设备仍需要启用 IRQ。 因此，如果“脱钩”设备断言 IRQ 线，系统将做出响应，假设它是断言 IRQ 线的其余设备之一。 由于其他设备都不会处理 IRQ，因此系统将“挂起”，直到它决定不处理 IRQ 并屏蔽 IRQ（100,000 次迭代后）。 一旦共享 IRQ 被屏蔽，其余设备将停止正常运行。 情况不太好。

这是使用 MSI 或 MSI-X（如果可用）的另一个原因。 MSI 和MSI-X 被定义为独占中断，因此不易受到“尖叫中断”问题的影响。


### 1.5.2. Release the IRQ

一旦设备静止（不再有 IRQ），就可以调用 free_irq()。 一旦处理了任何挂起的 IRQ，该函数将返回控制权，从该 IRQ 中“脱钩”驱动程序 IRQ 处理程序，最后在没有其他人使用该 IRQ 时释放该 IRQ。


### 1.5.3. Stop all DMA activity

在尝试释放 DMA 控制数据之前停止所有 DMA 操作非常重要。 否则可能会导致内存损坏、挂起，并且在某些芯片组上会导致严重崩溃。

停止 IRQ 后停止 DMA 可以避免 IRQ 处理程序可能重新启动 DMA 引擎的竞争。

虽然这一步听起来显而易见且微不足道，但一些“成熟”的司机过去并没有正确执行这一步。


### 1.5.4. Release DMA buffers

一旦 DMA 停止，首先清理流 DMA。 IE。 取消映射数据缓冲区并将缓冲区返回给“上游”所有者（如果有）。

然后清理包含控制数据的“一致”缓冲区。

有关取消映射接口的详细信息，请参阅[使用通用设备的动态 DMA 映射](https://www.kernel.org/doc/html/latest/core-api/dma-api.html)。


### 1.5.5. Unregister from other subsystems

大多数低级 PCI 设备驱动程序支持其他一些子系统，如 USB、ALSA、SCSI、NetDev、Infiniband 等。确保您的驱动程序不会丢失其他子系统的资源。 如果发生这种情况，通常症状是当子系统尝试调用已卸载的驱动程序时出现 Oops（恐慌）。


### 1.5.6. Disable Device from responding to MMIO/IO Port addresses

io_unmap() MMIO 或 IO 端口资源，然后调用 pci_disable_device()。 这与 pci_enable_device() 对称相反。 调用 pci_disable_device() 后不要访问设备寄存器。


### 1.5.7. Release MMIO/IO Port Resource(s)

调用 pci_release_region() 将 MMIO 或 IO 端口范围标记为可用。 如果不这样做通常会导致无法重新加载驱动程序。


## 1.6. How to access PCI config space

您可以使用 pci_(read|write)_config_(byte|word|dword) 访问 struct pci_dev * 表示的设备的配置空间。 所有这些函数在成功时返回 0，或者返回一个错误代码 (PCIBIOS_...)，该错误代码可以通过 pcibios_strerror 转换为文本字符串。 大多数驱动程序希望对有效 PCI 设备的访问不会失败。

如果没有可用的 struct pci_dev，则可以调用 pci_bus_(read|write)_config_(byte|word|dword) 来访问该总线上的给定设备和函数。

如果您访问配置头的标准部分中的字段，请使用 <linux/pci.h> 中声明的位置和位的符号名称。

如果您需要访问扩展 PCI 功能寄存器，只需针对特定功能调用 pci_find_capability()，它就会为您找到相应的寄存器块。


## 1.7. Other interesting functions

|                               |                                                                               |
| ----------------------------- | ----------------------------------------------------------------------------- |
| pci_get_domain_bus_and_slot() | 查找与给定域、总线、插槽和编号相对应的 pci_dev。 如果找到该设备，其引用计数就会增加 |
| pci_set_power_state()         | 设置 PCI 电源管理状态 (0=D0 ... 3=D3)                                          |
| pci_find_capability()         | 在设备的能力列表中查找指定的能力                                                |
| pci_resource_start()          | 返回给定 PCI 区域的总线起始地址                                                 |
| pci_resource_end()            | 返回给定 PCI 区域的总线结束地址                                                 |
| pci_resource_len()            | 返回 PCI 区域的字节长度                                                        |
| pci_set_drvdata()             | 为 pci_dev 设置私有驱动程序数据指针                                             |
| pci_get_drvdata()             | 返回 pci_dev 的私有驱动程序数据指针                                             |
| pci_set_mwi()                 | 启用内存-写入-无效事务                                                         |
| pci_clear_mwi()               | 禁用内存写入无效事务                                                           |


## 1.8. Miscellaneous hints

当向用户显示 PCI 设备名称时（例如，当驱动程序想要告诉用户它找到了什么卡时），请使用 pci_name(pci_dev)。

始终通过指向 pci_dev 结构的指针来引用 PCI 设备。 所有 PCI 层功能都使用此标识，并且它是唯一合理的标识。 除非出于非常特殊的目的，否则不要使用总线/插槽/功能号——在具有多个主总线的系统上，它们的语义可能非常复杂。

不要尝试在驱动程序中打开快速背对背写入。 总线上的所有设备都需要能够执行此操作，因此这需要由平台和通用代码而不是单独的驱动程序来处理。


## 1.9. Vendor and device identifications

不要将新的设备或供应商 ID 添加到 include/linux/pci_ids.h 中，除非它们在多个驱动程序之间共享。 如果有用的话，您可以在驱动程序中添加私有定义，或者仅使用普通的十六进制常量。

设备 ID 是任意十六进制数字（供应商控制），通常仅在单个位置（pci_device_id 表）中使用。

请务必将新的供应商/设备 ID 提交至 <https://pci-ids.ucw.cz/>。 <https://github.com/pciutils/pciids> 中有 pci.ids 文件的镜像。


## 1.10. Obsolete functions

当尝试将旧驱动程序移植到新 PCI 接口时，您可能会遇到几个函数。 它们不再存在于内核中，因为它们与热插拔或 PCI 域不兼容或具有合理的锁定。

|                   |                                       |
| ----------------- | ------------------------------------- |
| pci_find_device() | 被 pci_get_device() 取代              |
| pci_find_subsys() | 被 pci_get_subsys() 取代              |
| pci_find_slot()   | 被 pci_get_domain_bus_and_slot() 取代 |
| pci_get_slot()    | 被 pci_get_domain_bus_and_slot() 取代 |

另一种选择是传统的 PCI 设备驱动程序，它遍历 PCI 设备列表。 这仍然是可能的，但不鼓励。


## 1.11. MMIO Space and "Write Posting"

将驱动程序从使用 I/O 端口空间转换为使用 MMIO 空间通常需要一些额外的更改。 具体来说，需要处理“写帖子”。 许多驱动程序（例如 tg3、acenic、sym53c8xx_2）已经这样做了。 I/O 端口空间保证写事务在 CPU 继续之前到达 PCI 设备。 对 MMIO 空间的写入允许 CPU 在事务到达 PCI 设备之前继续进行。 HW weenies 将此称为“写入发布”，因为写入完成会在事务到达其目的地之前“发布”到 CPU。

因此，对时间敏感的代码应该添加 readl()，其中 CPU 在执行其他工作之前预计会等待。 经典的“位敲击”序列适用于 I/O 端口空间：

```c
for (i = 8; --i; val >>= 1) {
        outb(val & 1, ioport_reg);      /* write bit */
        udelay(10);
}
```

MMIO 空间的相同顺序应该是：

```c
for (i = 8; --i; val >>= 1) {
        writeb(val & 1, mmio_reg);      /* write bit */
        readb(safe_mmio_reg);           /* flush posted write */
        udelay(10);
}
```

重要的是“safe_mmio_reg”没有任何干扰设备正确操作的副作用。

另一种需要注意的情况是重置 PCI 设备时。 使用 PCI 配置空间读取来刷新 writel()。 如果预计 PCI 设备不响应 readl()，这将在所有平台上优雅地处理 PCI 主设备中止。 大多数 x86 平台将允许 MMIO 读取主中止（也称为“软故障”）并返回垃圾（例如 ~0）。 但许多 RISC 平台会崩溃（又名“硬故障”）。
