
# 4. The MSI Driver Guide HOWTO

作者：

Tom L Nguyen; Martine Silbermann; Matthew Wilcox

Copyright 2003, 2008 Intel Corporation


## 4.1. About this guide

本指南介绍了消息信号中断 (MSI) 的基础知识、使用 MSI 相对于传统中断机制的优势、如何更改驱动程序以使用 MSI 或 MSI-X，以及在设备不支持 MSI 时尝试的一些基本诊断。

## 4.2. What are MSIs?

消息信号中断是从设备到特殊地址的写入，这会导致 CPU 接收中断。

MSI 功能首先在 PCI 2.2 中指定，后来在 PCI 3.0 中得到增强，以允许单独屏蔽每个中断。 PCI 3.0 还引入了 MSI-X 功能。 它支持每个设备比 MSI 更多的中断，并允许独立配置中断。

设备可能同时支持 MSI 和 MSI-X，但一次只能启用其中之一。


## 4.3. Why use MSIs?

使用 MSI 比传统的基于引脚的中断更具优势，有以下三个原因。

基于引脚的 PCI 中断通常在多个设备之间共享。 为了支持这一点，内核必须调用与中断关联的每个中断处理程序，这会导致整个系统的性能下降。 MSI 永远不会共享，因此不会出现此问题。

当设备将数据写入内存，然后引发基于引脚的中断时，中断可能会在所有数据到达内存之前到达（对于 PCI-PCI 桥后面的设备，这种情况更常见）。 为了确保所有数据都已到达内存，中断处理程序必须读取引发中断的设备上的寄存器。 PCI 事务排序规则要求所有数据在从寄存器返回值之前到达内存。 使用 MSI 可以避免此问题，因为生成中断的写入无法传递数据写入，因此在引发中断时，驱动程序知道所有数据已到达内存。

PCI 设备只能支持每个功能一个基于引脚的中断。 通常，驱动程序必须查询设备以找出发生了什么事件，从而减慢了常见情况的中断处理速度。 借助 MSI，设备可以支持更多中断，从而允许每个中断专门用于不同的目的。 一种可能的设计为不常见的条件（例如错误）提供它们自己的中断，这允许驱动程序更有效地处理正常的中断处理路径。 其他可能的设计包括向网卡中的每个数据包队列或存储控制器中的每个端口提供一个中断。


## 4.4. How to use MSIs

PCI 设备初始化为使用基于引脚的中断。 设备驱动程序必须将设备设置为使用 MSI 或 MSI-X。 并非所有机器都正确支持 MSI，对于这些机器，下面描述的 API 将完全失败，并且设备将继续使用基于引脚的中断。


### 4.4.1. Include kernel support for MSIs

要支持 MSI 或 MSI-X，必须在启用 CONFIG_PCI_MSI 选项的情况下构建内核。 此选项仅在某些体系结构上可用，并且可能取决于还设置的其他一些选项。 例如，在 x86 上，您还必须启用 X86_UP_APIC 或 SMP 才能看到 CONFIG_PCI_MSI 选项。


### 4.4.2. Using MSI

大部分辛苦工作都是由 PCI 层的驱动程序完成的。 驱动程序只需请求 PCI 层为此设备设置 MSI 功能。

要自动使用 MSI 或 MSI-X 中断向量，请使用以下函数：

```c
int pci_alloc_irq_vectors(struct pci_dev *dev, unsigned int min_vecs,
              unsigned int max_vecs, unsigned int flags);
```

它为 PCI 设备分配最多 max_vecs 个中断向量。 它返回分配的向量数量或负错误。 如果设备对最小向量数有要求，则驱动程序可以传递设置为此限制的 min_vecs 参数，并且如果无法满足最小向量数，PCI 内核将返回 -ENOSPC。

flags 参数用于指定设备和驱动程序可以使用哪种类型的中断（PCI_IRQ_LEGACY、PCI_IRQ_MSI、PCI_IRQ_MSIX）。 还可以使用方便的简写 (PCI_IRQ_ALL_TYPES) 来请求任何可能类型的中断。 如果设置了 PCI_IRQ_AFFINITY 标志，pci_alloc_irq_vectors() 将在可用 CPU 周围传播中断。

要获取传递给 request_irq() 和 free_irq() 的 Linux IRQ 编号以及向量，请使用以下函数：

```c
int pci_irq_vector(struct pci_dev *dev, unsigned int nr);
```

在使用以下函数删除设备之前，应释放所有分配的资源：

```c
void pci_free_irq_vectors(struct pci_dev *dev);
```

如果设备同时支持 MSI-X 和 MSI 功能，则此 API 将优先使用 MSI-X 设施而不是 MSI 设施。 MSI-X 支持 1 到 2048 之间的任意数量的中断。相比之下，MSI 被限制为最多 32 个中断（并且必须是 2 的幂）。 此外，MSI 中断向量必须连续分配，因此系统可能无法为 MSI 分配与 MSI-X 一样多的向量。 在某些平台上，MSI 中断必须全部针对同一组 CPU，而 MSI-X 中断可以全部针对不同的 CPU。

如果设备既不支持 MSI-X 也不支持 MSI，它将回退到单个传统 IRQ 矢量。

MSI 或 MSI-X 中断的典型用法是分配尽可能多的向量，可能达到设备支持的限制。 如果 nvec 大于设备支持的数量，它将自动限制为支持的限制，因此无需事先查询支持的向量数量：

```c
nvec = pci_alloc_irq_vectors(pdev, 1, nvec, PCI_IRQ_ALL_TYPES)
if (nvec < 0)
        goto out_err;
```

如果驱动程序无法或不愿意处理可变数量的 MSI 中断，它可以通过将该数量作为“min_vecs”和“max_vecs”参数传递给 pci_alloc_irq_vectors() 函数来请求特定数量的中断：

```c
ret = pci_alloc_irq_vectors(pdev, nvec, nvec, PCI_IRQ_ALL_TYPES);
if (ret < 0)
        goto out_err;
```

上述请求类型最臭名昭著的示例是为设备启用单一 MSI 模式。 可以通过传递两个 1 作为“min_vecs”和“max_vecs”来完成：

```c
ret = pci_alloc_irq_vectors(pdev, 1, 1, PCI_IRQ_ALL_TYPES);
if (ret < 0)
        goto out_err;
```

某些设备可能不支持使用旧线路中断，在这种情况下，驱动程序可以指定仅接受 MSI 或 MSI-X：

```c
nvec = pci_alloc_irq_vectors(pdev, 1, nvec, PCI_IRQ_MSI | PCI_IRQ_MSIX);
if (nvec < 0)
        goto out_err;
```


### 4.4.3. Legacy APIs

新代码中不应使用以下用于启用和禁用 MSI 或 MSI-X 中断的旧 API：

```c
pci_enable_msi()              /* deprecated */
pci_disable_msi()             /* deprecated */
pci_enable_msix_range()       /* deprecated */
pci_enable_msix_exact()       /* deprecated */
pci_disable_msix()            /* deprecated */
```

此外，还有一些 API 可提供受支持的 MSI 或 MSI-X 向量的数量：pci_msi_vec_count() 和 pci_msix_vec_count()。 一般来说，应该避免这些，有利于让 pci_alloc_irq_vectors() 限制向量的数量。 如果您有一个合法的向量计数特殊用例，我们可能必须重新考虑该决定并添加一个 pci_nr_irq_vectors() 帮助程序来透明地处理 MSI 和 MSI-X。


### 4.4.4. Considerations when using MSIs

#### 4.4.4.1. Spinlocks

大多数设备驱动程序都有一个每个设备的自旋锁，该自旋锁在中断处理程序中获取。 对于基于引脚的中断或单个 MSI，无需禁用中断（Linux 保证不会重新进入相同的中断）。 如果设备使用多个中断，则驱动程序必须在锁定时禁用中断。 如果设备发送不同的中断，驱动程序将在尝试递归获取自旋锁时发生死锁。 可以使用 spin_lock_irqsave() 或 spin_lock_irq() 来避免此类死锁，它们禁用本地中断并获取锁（请参阅不可靠的锁定指南）。


### 4.4.5. How to tell whether MSI/MSI-X is enabled on a device

使用“lspci -v”（以 root 身份）可能会显示某些具有“MSI”、“消息信号中断”或“MSI-X”功能的设备。 这些功能中的每一个都有一个“启用”标志，后面带有“+”（启用）或“-”（禁用）。


## 4.5. MSI quirks

已知多种 PCI 芯片组或设备不支持 MSI。 PCI 堆栈提供了三种禁用 MSI 的方法：

1. 全局的；
2. 在特定网桥后面的所有设备上；
3. 在单个设备上。


### 4.5.1. Disabling MSIs globally

某些主机芯片组根本无法正确支持 MSI。 如果幸运的话，制造商知道这一点并已在 ACPI FADT 表中指出。 在这种情况下，Linux 会自动禁用 MSI。 有些主板的表中不包含这些信息，因此我们必须自己检测它们。 这些的完整列表可以在 drivers/pci/quirks.c 中的 quirk_disable_all_msi() 函数附近找到。

如果您的主板存在 MSI 问题，您可以在内核命令行上传递 pci=nomsi 以禁用所有设备上的 MSI。 将问题报告给 linux-pci@vger.kernel.org（包括完整的“lspci -v”）符合您的最佳利益，以便我们可以将怪癖添加到内核中。


### 4.5.2. Disabling MSIs below a bridge

某些 PCI 桥无法在总线之间正确路由 MSI。 在这种情况下，必须在桥接器后面的所有设备上禁用 MSI。

有些桥允许您通过更改 PCI 配置空间中的某些位来启用 MSI（尤其是 Hypertransport 芯片组，例如 nVidia nForce 和 Serverworks HT2000）。 与主机芯片组一样，Linux 大多了解它们，并在可能的情况下自动启用 MSI。 如果您有 Linux 未知的网桥，您可以使用您知道有效的任何方法在配置空间中启用 MSI，然后通过执行以下操作在该网桥上启用 MSI：

```bash
echo 1 > /sys/bus/pci/devices/$bridge/msi_bus
```

其中 $bridge 是您已启用的桥接器的 PCI 地址（例如 0000:00:0e.0）。

要禁用 MSI，请回显 0 而不是 1。应谨慎更改此值，因为它可能会中断此桥下所有设备的中断处理。

再次，请通知 linux-pci@vger.kernel.org 任何需要特殊处理的桥接器。


### 4.5.3. Disabling MSIs on a single device

已知某些设备的 MSI 实施有缺陷。 通常这是在单独的设备驱动程序中处理的，但有时需要通过特殊处理来处理。 某些驱动程序可以选择禁用 MSI。 虽然这对于驱动程序作者来说是一个方便的解决方法，但这不是一个好的做法，不应该被模仿。


### 4.5.4. Finding why MSIs are disabled on a device

从以上三节中，您可以看到无法为给定设备启用 MSI 的原因有很多。 您的第一步应该是仔细检查您的 dmesg，以确定您的计算机是否启用了 MSI。 您还应该检查 .config 以确保已启用 CONFIG_PCI_MSI。

然后，“lspci -t”给出设备上方的网桥列表。 读取 /sys/bus/pci/devices/*/msi_bus 将告诉您 MSI 是启用 (1) 还是禁用 (0)。 如果在属于 PCI 根和设备之间的桥接器的任何 msi_bus 文件中找到 0，则 MSI 将被禁用。

还值得检查设备驱动程序以查看它是否支持 MSI。 例如，它可能包含对带有 PCI_IRQ_MSI 或 PCI_IRQ_MSIX 标志的 pci_alloc_irq_vectors() 的调用。


## 4.6. List of device drivers MSI(-X) APIs

PCI/MSI 子系统为其导出的设备驱动程序 API 有一个专用的 C 文件 — drivers/pci/msi/api.c。 导出以下函数：

int pci_enable_msi(struct pci_dev *dev)
----------------

    在设备上启用 MSI 中断模式。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    描述

        传统设备驱动程序 API 用于在设备上启用 MSI 中断模式并分配单个中断向量。 成功后，分配的向量 Linux IRQ 将保存在 dev->irq 中。 驱动程序必须在清理时调用 pci_disable_msi()。

    注意

        一般来说，应该使用较新的 pci_alloc_irq_vectors() / pci_free_irq_vectors() API 对。

    返回值

        成功则返回 0，否则返回 errno


void pci_disable_msi(struct pci_dev *dev)
----------------

    在设备上禁用 MSI 中断模式。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    描述

        旧版设备驱动程序 API 用于禁用设备上的 MSI 中断模式、释放早期分配的中断向量并恢复 INTx 仿真。 PCI 设备 Linux IRQ (dev->irq) 恢复为其默认引脚断言 IRQ。 这是 pci_enable_msi() 的清理对。

    注意

        一般来说，应该使用较新的 pci_alloc_irq_vectors() / pci_free_irq_vectors() API 对。


int pci_msix_vec_count(struct pci_dev *dev)
----------------

    获取设备上 MSI-X 中断向量的数量。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    返回值

        该设备上可用的 MSI-X 中断向量的数量（即设备的 MSI-X 功能结构“表大小”），如果设备不支持 MSI-X，则为 -EINVAL，否则为其他 errnos。


int pci_enable_msix_range(struct pci_dev *dev, struct msix_entry *entries, int minvec, int maxvec)
----------------

    在设备上启用 MSI-X 中断模式。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    struct msix_entry *entries
        输入/输出参数，MSI-X 配置条目数组。

    int minvec
        MSI-X 向量所需的最小数量。

    int maxvec
        MSI-X 向量的最大所需数量。

    描述

        传统设备驱动程序 API，用于在设备上启用 MSI-X 中断模式并根据需要配置其 MSI-X 功能结构。 传递的条目数组必须将其每个成员“条目”字段设置为所需的（有效）MSI-X 向量编号，其中有效 MSI-X 向量编号的范围可以通过 pci_msix_vec_count() 查询。 如果成功，驱动程序必须在清理时调用 pci_disable_msix()。

    注意

        一般来说，应该使用较新的 pci_alloc_irq_vectors() / pci_free_irq_vectors() API 对。

    返回值

        分配的 MSI-X 向量数量（可能小于 maxvecs），其中此类分配向量的 Linux IRQ 编号保存回条目数组元素的“向量”字段中。 如果可用中断向量少于 minvecs，则返回 -ENOSPC。 如果传递的条目成员“条目”字段之一无效或重复，或者如果之前在设备上启用了普通 MSI 中断模式，则返回 -EINVAL。 否则返回其他错误。


bool pci_msix_can_alloc_dyn(struct pci_dev *dev)
----------------

    查询是否支持启用MSI-X后动态分配。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    返回值

        如果支持则为 true，否则为 false。


struct msi_map pci_msix_alloc_irq_at(struct pci_dev *dev, unsigned int index, const struct irq_affinity_desc *affdesc)
----------------

    在给定 MSI-X 向量索引或任何空闲向量索引处启用 MSI-X 后，分配 MSI-X 中断。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    unsigned int index
        要分配的索引。 如果索引 == MSI_ANY_INDEX，则会分配 MSI-X 表中的下一个空闲索引。

    const struct irq_affinity_desc *affdesc
        指向关联描述符结构的可选指针。 否则为 NULL。

    返回值

        - A struct msi_map
        - 成功时，msi_map::index 包含分配的索引 (>= 0)，msi_map::virq 包含分配的 Linux 中断号 (> 0)。
        - 失败时，msi_map::index 包含错误代码，并且 msi_map::virq 设置为 0。


void pci_msix_free_irq(struct pci_dev *dev, struct msi_map map)
----------------

    释放 PCI/MSIX 中断域上的中断。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    struct msi_map map
        描述要释放的中断的 struct msi_map。

    描述

        撤消中断向量分配。 不禁用 MSI-X。


void pci_disable_msix(struct pci_dev *dev)
----------------

    在设备上禁用 MSI-X 中断模式。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    描述

        旧版设备驱动程序 API 用于禁用设备上的 MSI-X 中断模式、释放早期分配的中断向量并恢复 INTx。 PCI 设备 Linux IRQ (dev->irq) 恢复为其默认引脚断言 IRQ。 这是 pci_enable_msix_range() 的清理对。

    注意

        一般来说，应该使用较新的 pci_alloc_irq_vectors() / pci_free_irq_vectors() API 对。


int pci_alloc_irq_vectors(struct pci_dev *dev, unsigned int min_vecs, unsigned int max_vecs, unsigned int flags)
----------------

    分配多个设备中断向量。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    unsigned int min_vecs
        所需的最小向量数（必须 >= 1）。

    unsigned int max_vecs
        最大所需向量数。

    unsigned int flags
    One or more of:

    - PCI_IRQ_MSIX 允许尝试 MSI-X 向量分配；
    - PCI_IRQ_MSI 允许尝试 MSI 向量分配；
    - PCI_IRQ_LEGACY 当且仅当 min_vecs == 1 时，允许尝试旧版 INTx 中断；
    - PCI_IRQ_AFFINITY 通过在可用 CPU 周围分布向量来自动管理 IRQ 关联性。

    描述

        在设备上分配最多 max_vecs 个中断向量。 MSI-X irq 矢量分配比普通 MSI 具有更高的优先级，而普通 MSI 又比传统 INTx 仿真具有更高的优先级。
        成功分配后，调用者应使用 pci_irq_vector() 获取要传递给 request_threaded_irq() 的 Linux IRQ 编号。 驱动程序必须在清理时调用 pci_free_irq_vectors()。

    返回值

        分配的向量数量（可能小于 max_vecs），如果可用的中断向量少于 min_vecs，则为 -ENOSPC，否则为其他 errnos。


int pci_alloc_irq_vectors_affinity(struct pci_dev *dev, unsigned int min_vecs, unsigned int max_vecs, unsigned int flags, struct irq_affinity *affd)
----------------

    分配具有关联性要求的多个设备中断向量。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    unsigned int min_vecs
        所需的最小向量数（必须 >= 1）。

    unsigned int max_vecs
        最大所需向量数。

    unsigned int flags
        分配标志，如 pci_alloc_irq_vectors() 中。

    struct irq_affinity *affd
        亲和力要求（可以为 NULL）。

    描述

        与 pci_alloc_irq_vectors() 相同，但带有额外的 affd 参数。 检查该函数文档和 struct irq_affinity 以获取更多详细信息。


int pci_irq_vector(struct pci_dev *dev, unsigned int nr)
----------------

    获取设备中断向量的 Linux IRQ 号。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    unsigned int nr
        设备相关中断向量索引（从0开始）； 根据中断模式的不同，有不同的含义：

        - MSI-X MSI-X向量表中的索引；
        - MSI 已启用的 MSI 向量的索引；
        - INTx 必须为 0。

    返回值

        Linux IRQ 号，如果 nr 超出范围，则为 -EINVAL。


const struct cpumask *pci_irq_get_affinity(struct pci_dev *dev, int nr)
----------------

    获取设备中断向量关联性。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    int nr
        设备相关中断向量索引（从0开始）； 根据中断模式的不同，有不同的含义：

        - MSI-X MSI-X向量表中的索引；
        - MSI 已启用的 MSI 向量的索引；
        - INTx 必须为 0。

    返回值

        MSI/MSI-X 向量亲和性，如果 nr 超出范围或在没有显式亲和性要求的情况下分配 MSI(-X) 向量（例如，通过 pci_enable_msi()、pci_enable_msix_range() 或 pci_alloc_irq_vectors() 不带 PCI_IRQ_AFFINITY 标志，则为 NULL ）。 如果设备处于传统 INTx 模式，则返回一组通用 CPU ID，表示系统引导期间可用的所有可能的 CPU。


struct msi_map pci_ims_alloc_irq(struct pci_dev *dev, union msi_instance_cookie *icookie, const struct irq_affinity_desc *affdesc)
----------------

    在 PCI/IMS 中断域上分配中断。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    union msi_instance_cookie *icookie
        指向此 IMS 实例的 IMS 实现特定 cookie 的指针（PASID、队列 ID、指针...）。 cookie 内容被复制到 MSI 描述符中，用于中断芯片回调或域特定设置函数。

    const struct irq_affinity_desc *affdesc
        指向中断关联描述符的可选指针。

    描述

        IMS 分配没有索引，因为 IMS 是特定于实现的存储，并且索引（可能是纯软件构造）和设备功能之间没有任何直接关联。 该关联由驱动程序通过索引建立（如果有硬件表），或者在纯软件管理的 IMS 实现的情况下，该关联通过实现特定中断芯片的 irq_write_msi_msg() 回调进行，该回调利用提供的 icookie 将 MSI 消息存储在适当的位置。

    返回值

        - 一个 struct msi_map；
        - 成功时，msi_map::index 包含分配的索引 (>= 0)，msi_map::virq 包含分配的 Linux 中断号 (> 0)；
        - 失败时，msi_map::index 包含错误代码，并且 msi_map::virq 设置为 0。


void pci_ims_free_irq(struct pci_dev *dev, struct msi_map map)
----------------

    在通过 pci_ims_alloc_irq() 分配的 PCI/IMS 中断域上分配中断。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    struct msi_map map
        描述从 pci_ims_alloc_irq() 返回的要释放的中断的 struct msi_map。


void pci_free_irq_vectors(struct pci_dev *dev)
----------------

    释放先前为设备分配的 IRQ。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    描述

        撤消之前通过 pci_alloc_irq_vectors_affinity() 或 pci_alloc_irq_vectors() 完成的中断向量分配和可能的设备 MSI/MSI-X 启用。


void pci_restore_msi_state(struct pci_dev *dev)
----------------

    恢复设备上缓存的 MSI(-X) 状态。

    参数

    struct pci_dev *dev
        要操作的 PCI 设备。

    描述

        将 Linux 缓存的 MSI(-X) 状态写回到设备上。 这通常在系统恢复时或错误恢复 PCI 适配器重置后很有用。


int pci_msi_enabled(void)
----------------

    MSI(-X) 中断是否在系统范围内启用？

    参数

    void
        没有参数

    返回值

        如果 MSI 尚未通过 ACPI FADT、PCI 桥怪癖或“pci=nomsi”内核命令行选项全局禁用，则为 true。
