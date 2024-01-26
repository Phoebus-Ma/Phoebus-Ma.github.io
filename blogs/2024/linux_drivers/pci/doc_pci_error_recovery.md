
# 7. PCI Error Recovery

Authors

- Linas Vepstas <linasvepstas@gmail.com>
- Richard Lary <rlary@us.ibm.com>
- Mike Mason <mmlnx@us.ibm.com>

许多 PCI 总线控制器能够检测总线上的各种硬件 PCI 错误，例如数据和地址总线上的奇偶校验错误，以及 SERR 和 PERR 错误。 一些更先进的芯片组能够处理这些错误； 其中包括 PCI-E 芯片组以及基于 IBM Power4、Power5 和 Power6 的 pSeries 机箱上的 PCI 主机桥。 采取的典型操作是断开受影响的设备，停止其所有 I/O。 断开连接的目的是避免系统损坏； 例如，阻止由于 DMA 到“狂野”地址而导致的系统内存损坏。 通常，还提供重新连接机制，以便受影响的 PCI 设备被重置并恢复到工作状态。 重置阶段需要受影响的设备驱动程序和 PCI 控制器芯片之间的协调。 本文档描述了一个通用 API，用于通知设备驱动程序总线断开连接，然后执行错误恢复。 该 API 目前在 2.6.16 及更高版本的内核中实现。

报告和恢复分几个步骤执行。 首先，当 PCI 硬件错误导致总线断开连接时，会尽快向所有受影响的设备驱动程序报告该事件，包括多功能卡上设备驱动程序的多个实例。 这允许设备驱动程序避免自旋循环中的死锁，等待某些 i/o 空间寄存器发生变化，而实际上它永远不会发生变化。 它还使驱动程序有机会根据需要推迟传入的 I/O。

接下来，恢复分几个阶段进行。 大多数复杂性是由于需要处理多功能设备（即具有多个与其关联的设备驱动程序的设备）而造成的。 在第一阶段，每个驱动程序都可以指示其所需的重置类型，选择是简单地重新启用 I/O 或请求插槽重置。

如果任何驱动程序请求重置插槽，就会执行此操作。

重置和/或重新启用 I/O 后，所有驱动程序都会再次收到通知，以便他们可以执行可能需要的任何设备设置/配置。 这些都完成后，将发出最终的“恢复正常操作”事件。

选择基于内核的实现而不是用户空间实现的最大原因是需要处理连接到存储介质的 PCI 设备的总线断开连接，特别是与保存根文件系统的设备的断开连接。 如果根文件系统断开连接，用户空间机制将必须经过大量的扭曲才能完成恢复。 几乎所有当前的 Linux 文件系统都不能容忍与底层块设备的断开/重新连接。 相比之下，总线错误在设备驱动程序中很容易管理。 事实上，大多数设备驱动程序已经处理非常相似的恢复过程； 例如，SCSI通用层已经提供了处理SCSI总线错误和SCSI总线重置的重要机制。


## 7.1. Detailed Design

下面的设计和实现细节基于 2005 年 4 月 5 日左右与 Ben Herrenschmidt 进行的一系列公共电子邮件讨论。

错误恢复 API 支持以函数指针结构的形式向驱动程序公开，该结构由 struct pci_driver 中的新字段指向。 无法提供该结构的驱动程序是“无意识的”，并且所采取的实际恢复步骤取决于平台。 arch/powerpc 实现将模拟 PCI 热插拔移除/添加。

该结构的形式为：

```c
struct pci_error_handlers
{
        int (*error_detected)(struct pci_dev *dev, pci_channel_state_t);
        int (*mmio_enabled)(struct pci_dev *dev);
        int (*slot_reset)(struct pci_dev *dev);
        void (*resume)(struct pci_dev *dev);
        void (*cor_error_detected)(struct pci_dev *dev);
};

```

可能的通道状态有：

```c
typedef enum {
        pci_channel_io_normal,  /* I/O channel is in normal state */
        pci_channel_io_frozen,  /* I/O to channel is blocked */
        pci_channel_io_perm_failure, /* PCI card is dead */
} pci_channel_state_t;
```

可能的返回值为：

```c
enum pci_ers_result {
        PCI_ERS_RESULT_NONE,        /* no result/none/not supported in device driver */
        PCI_ERS_RESULT_CAN_RECOVER, /* Device driver can recover without slot reset */
        PCI_ERS_RESULT_NEED_RESET,  /* Device driver wants slot to be reset. */
        PCI_ERS_RESULT_DISCONNECT,  /* Device has completely failed, is unrecoverable */
        PCI_ERS_RESULT_RECOVERED,   /* Device driver is fully recovered and operational */
};
```

驱动程序不必实现所有这些回调； 然而，如果它实现了任何一个，它就必须实现 error_Detected()。 如果未实现回调，则认为相应的功能不受支持。 例如，如果 mmio_enabled() 和resume() 不存在，则假定驱动程序未执行任何直接恢复并且需要插槽重置。 通常，驱动程序会想了解 slot_reset()。

平台从 PCI 错误事件中恢复所采取的实际步骤将取决于平台，但将遵循下面描述的一般顺序。


### 7.1.1. STEP 0: Error Event

PCI 硬件检测到 PCI 总线错误。 在 powerpc 上，插槽是隔离的，因为所有 I/O 都被阻止：所有读取都返回 0xffffffff，所有写入都被忽略。


### 7.1.2. STEP 1: Notification

平台在受错误影响的每个驱动程序的每个实例上调用 error_Detected() 回调。

此时，设备可能无法再访问，具体取决于平台（该插槽将在 powerpc 上被隔离）。 由于 I/O 失败，驱动程序可能已经“注意到”错误，但这是正确的“同步点”，也就是说，它为驱动程序提供了清理的机会，等待待处理的内容（计时器等） ...） 去完成; 它可以使用信号量、时间表等……除了触摸设备之外的一切。 在此函数内以及返回后，驱动程序不应执行任何新的 IO。 在任务上下文中调用。 这是一种“静止”点。 请参阅本文档末尾有关中断的注释。

参与该系统的所有驱动程序都必须执行此调用。 驱动程序必须返回以下结果代码之一：

- PCI_ERS_RESULT_CAN_RECOVER

&emsp;如果驱动程序认为可以通过敲击 IO 来恢复硬件，或者想要有机会提取一些诊断信息（请参阅下面的 mmio_enable），则驱动程序会返回此信息。

- PCI_ERS_RESULT_NEED_RESET

&emsp;如果驱动程序在不重置插槽的情况下无法恢复，则会返回此信息。

- PCI_ERS_RESULT_DISCONNECT

&emsp;如果驱动程序根本不想恢复，则会返回此信息。

下一步采取的步骤将取决于驱动程序返回的结果代码。

如果段/插槽上的所有驱动程序都返回 PCI_ERS_RESULT_CAN_RECOVER，则平台应重新启用插槽上的 IO（或者，如果平台不隔离插槽，则不执行任何操作），然后恢复继续执行步骤 2（MMIO 启用）。

如果任何驱动程序请求插槽重置（通过返回 PCI_ERS_RESULT_NEED_RESET），则恢复将继续执行步骤 4（插槽重置）。

如果平台无法恢复插槽，则下一步是第 6 步（永久故障）。

**Note**

当前的 powerpc 实现假设设备驱动程序不会在此例程中进行调度或信号量； 当前的 powerpc 实现使用一个内核线程来通知所有设备； 因此，如果一台设备休眠/调度，所有设备都会受到影响。 做得更好需要在错误恢复实现中使用复杂的多线程逻辑（例如，在继续恢复之前等待所有通知线程“加入”。）这似乎过于复杂并且不值得实现。

当前的 powerpc 实现不太关心设备此时是否尝试 I/O。 I/O 将失败，读取时返回 0xff 值，并且写入将被删除。 如果尝试对冻结适配器进行超过 EEH_MAX_FAILS I/O，则 EEH 会假定设备驱动程序已进入无限循环并向 syslog 打印错误。 然后需要重新启动才能使设备再次工作。


### 7.1.3. STEP 2: MMIO Enabled

平台重新启用设备的 MMIO（但通常不是 DMA），然后在所有受影响的设备驱动程序上调用 mmio_enabled() 回调。

这就是“早日康复”的号召。 再次允许 IO，但不允许 DMA，但有一些限制。 这不是驱动程序再次开始操作的回调，只是为了查看/戳设备，提取诊断信息（如果有），并最终执行诸如触发设备本地重置等操作，但不重新启动操作。 如果网段上的所有驱动程序都同意它们可以尝试恢复并且硬件未执行自动链接重置，则会进行此回调。 如果平台无法在没有插槽重置或链接重置的情况下重新启用 IO，则不会调用此回调，而是直接进入步骤 3（链接重置）或步骤 4（插槽重置）

**Note**

提议如下； 目前还没有平台实现此功能：建议：所有 I/O 都应在此回调中_同步_完成，由它们触发的错误将通过正常的 pci_check_whatever() API 返回，不会因发生错误而发出新的 error_detected() 回调 这里。 然而，这样的错误可能会导致整个段的 IO 重新阻塞，从而使同一段上的其他设备可能所做的恢复无效，迫使整个段进入下一个状态之一，即链路重置 或插槽重置。

驱动程序应返回以下结果代码之一：

- PCI_ERS_RESULT_RECOVERED

&emsp;如果驱动程序认为设备功能齐全并且已准备好再次启动正常的驱动程序操作，则驱动程序会返回此信息。 无法保证该驱动程序实际上会被允许继续进行，因为同一网段上的另一个驱动程序可能已失败，从而在支持它的平台上触发了插槽重置。

- PCI_ERS_RESULT_NEED_RESET

&emsp;如果驱动程序认为设备在当前状态下不可恢复并且需要重置插槽才能继续，则驱动程序会返回此信息。

- PCI_ERS_RESULT_DISCONNECT

&emsp;与上面相同。 完全失败，即使重置驱动程序死机后也无法恢复。 （更准确的定义）

下一步采取的措施取决于驱动程序返回的结果。 如果所有驱动程序都返回 PCI_ERS_RESULT_RECOVERED，则平台将继续执行第 3 步（链路重置）或第 5 步（恢复操作）。

如果任何驱动程序返回 PCI_ERS_RESULT_NEED_RESET，则平台将继续执行步骤 4（插槽重置）


### 7.1.4. STEP 3: Link Reset

平台重置链接。 这是 PCI-Express 特定的步骤，只要检测到可通过重置链接“解决”的致命错误，就会执行此步骤。


### 7.1.5. STEP 4: Slot Reset

为了响应 PCI_ERS_RESULT_NEED_RESET 返回值，平台将对请求的 PCI 设备执行插槽重置。 平台执行插槽重置所采取的实际步骤将取决于平台。 插槽重置完成后，平台将调用设备 slot_reset() 回调。

Powerpc 平台实现两个级别的插槽复位：软复位（默认）和基本（可选）复位。

Powerpc 软重置包括断言适配器 #RST 线，然后将 PCI BAR 和 PCI 配置标头恢复到与全新系统加电后的状态相同，然后加电 BIOS/系统固件初始化。 软复位也称为热复位。

Powerpc 基本复位仅受 PCI Express 卡支持，并导致设备的状态机、硬件逻辑、端口状态和配置寄存器初始化为其默认条件。

对于大多数 PCI 设备，软重置足以恢复。 提供可选的基本重置以支持有限数量的 PCI Express 设备，对于这些设备，软重置不足以恢复。

如果平台支持 PCI 热插拔，则可以通过关闭/打开插槽电源来执行重置。

对于平台来说，将 PCI 配置空间恢复到“新上电”状态而不是“最后状态”非常重要。 插槽重置后，设备驱动程序几乎总是使用其标准设备初始化例程，并且不寻常的配置空间设置可能会导致设备挂起、内核恐慌或静默数据损坏。

此调用使驱动程序有机会重新初始化硬件（重新下载固件等）。 此时，驱动程序可能会认为该卡处于全新状态并且功能齐全。 插槽被解冻，驱动程序可以完全访问 PCI 配置空间、内存映射 I/O 空间和 DMA。 中断（传统、MSI 或 MSI-X）也将可用。

此时驱动程序不应重新启动正常的 I/O 处理操作。 如果所有设备驱动程序都报告此回调成功，平台将调用resume()来完成序列，并让驱动程序重新启动正常的I/O处理。

如果驱动程序在重置后无法使设备运行，它仍然可以为此函数返回严重故障。 如果平台之前尝试过软重置，现在可能会尝试硬重置（电源循环），然后再次调用 slot_reset()。 如果设备仍然无法恢复，则无能为力； 在这种情况下，平台通常会报告“永久故障”。 在这种情况下，该设备将被视为“死亡”。

多功能卡的驱动程序需要相互协调，以确定哪个驱动程序实例将执行任何“一次性”或全局设备初始化。 例如，Symbios sym53cxx2 驱动程序仅从 PCI 功能 0 执行设备初始化：

```c
+       if (PCI_FUNC(pdev->devfn) == 0)
+               sym_reset_scsi_bus(np, 0);
```

结果代码：

- PCI_ERS_RESULT_DISCONNECT Same as above.

需要基本重置的 PCI Express 卡驱动程序必须在其探测函数中设置 pci_dev 结构中的 need_freset 位。 例如，QLogic qla2xxx 驱动程序为某些 PCI 卡类型设置 need_freset 位：

```c
+       /* Set EEH reset type to fundamental if required by hba  */
+       if (IS_QLA24XX(ha) || IS_QLA25XX(ha) || IS_QLA81XX(ha))
+               pdev->needs_freset = 1;
+
```

平台继续执行步骤 5（恢复操作）或步骤 6（永久故障）。

**Note**

如果驱动程序返回 PCI_ERS_RESULT_DISCONNECT，当前的 powerpc 实现不会尝试重新启动电源。 然而，它可能应该。


### 7.1.6. STEP 5: Resume Operations

如果该段上的所有驱动程序均已从前 3 个回调之一返回 PCI_ERS_RESULT_RECOVERED，则平台将在所有受影响的设备驱动程序上调用resume() 回调。 此回调的目标是告诉驱动程序重新启动活动，一切都恢复并运行。 此回调不返回结果代码。

此时，如果发生新的错误，平台将重新启动新的错误恢复序列。


### 7.1.7. STEP 6: Permanent Failure

发生“永久故障”，平台无法恢复设备。 平台将使用 pci_channel_state_t 值为 pci_channel_io_perm_failure 调用 error_Detected()。

此时，设备驱动程序应该假设最坏的情况。 它应该取消所有挂起的 I/O，拒绝所有新的 I/O，将 -EIO 返回到更高层。 然后，设备驱动程序应该清理其所有内存并将其自身从内核操作中删除，就像在系统关闭期间一样。

平台通常会以某种方式通知系统操作员永久性故障。 如果设备支持热插拔，操作员可能会想要移除并更换该设备。 但请注意，并非所有故障都是真正的“永久性”。 有些是由于过热引起的，有些是由于卡安装不良引起的。 许多 PCI 错误事件是由软件错误引起的，例如 由于编程错误，DMA 到野生地址或虚假分割事务。 有关软件错误原因的实际经验的更多详细信息，请参阅 PCI 总线 EEH 错误恢复中的讨论。


### 7.1.8. Conclusion; General Remarks

调用回调的方式是平台策略。 没有插槽重置功能的平台可能只想“忽略”无法恢复（断开它们）的驱动程序，并尝试让同一网段上的其他卡恢复。 但请记住，在大多数现实生活中，每个路段只有一名驾驶员。

现在，关于中断的说明。 如果您收到中断并且您的设备死机或已被隔离，则存在问题。 目前的政策是把它变成平台政策。 也就是说，恢复API只需要：

- 无法保证从错误检测开始到调用 slot_reset 回调之前可以从段上的任何设备进行中断传递，此时中断预计将完全可操作。

- 无法保证中断传递会停止，也就是说，驱动程序在检测到错误后获取中断，或者在中断处理程序中检测到错误，从而阻止正确确认中断（从而阻止删除 source) 应该只返回 IRQ_NOTHANDLED。 由平台来处理这种情况，通常是在错误处理期间屏蔽 IRQ 源。 期望平台“知道”哪些中断被路由到具有错误管理能力的插槽，并且可以在错误处理期间临时禁用该 IRQ 号（这并不是非常复杂）。 这意味着共享中断的其他设备会出现一些 IRQ 延迟，但没有其他办法。 无论如何，高端平台不应该在许多设备之间共享中断。

**Note**

powerpc 平台的实现细节在文件 [PCI 总线 EEH 错误恢复](https://www.kernel.org/doc/html/latest/arch/powerpc/eeh-pci-error-recovery.html) 中讨论。

截至撰写本文时，越来越多的设备驱动程序带有实现错误恢复的补丁。 并非所有这些补丁都已进入主线。 这些可以用作“示例”：

- drivers/scsi/ipr
- drivers/scsi/sym53c8xx_2
- drivers/scsi/qla2xxx
- drivers/scsi/lpfc
- drivers/next/bnx2.c
- drivers/next/e100.c
- drivers/net/e1000
- drivers/net/e1000e
- drivers/net/ixgbe
- drivers/net/cxgb3
- drivers/net/s2io.c

当错误严重性为“可纠正”时，将在handle_error_source() 中调用cor_error_Detected() 回调。 回调是可选的，并且允许在需要时进行额外的日志记录。 参见示例：

- drivers/cxl/pci.c

### 7.1.9. The End
