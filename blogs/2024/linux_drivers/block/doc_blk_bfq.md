
# BFQ (Budget Fair Queueing)

BFQ 是一种比例共享 I/O 调度程序，具有一些额外的低延迟功能。 除了cgroups支持（blkio或io控制器）之外，BFQ的主要功能是：

- BFQ保证系统和应用程序的高响应能力，以及对时间敏感的应用程序（例如音频或视频播放器）的低延迟；

- BFQ 在进程或组之间分配带宽，而不仅仅是时间（在需要时切换回时间分配以保持高吞吐量）。

在默认配置中，BFQ 优先考虑延迟而不是吞吐量。 因此，当需要实现较低延迟时，BFQ 会构建可能导致吞吐量较低的调度。 如果对于给定设备，您的主要或唯一目标是始终实现最大可能的吞吐量，那么请通过将 low_latency 设置为 0 来关闭该设备的所有低延迟启发式方法。有关如何操作的详细信息，请参阅第 3 节 配置 BFQ 以获得延迟和吞吐量之间的所需权衡，或如何最大化吞吐量。

与每个 I/O 调度程序一样，BFQ 会为每个 I/O 请求处理增加一些开销。 为了了解这种开销，BFQ 的总的、单锁保护的、每个请求的处理时间（即请求插入、分派和完成钩子的执行时间之和）是，例如 ，在 Intel Core i7-2760QM@2.40GHz 上为 1.9 us（笔记本电脑的过时 CPU；使用简单代码检测并使用 S 套件 [1] 的吞吐量同步.sh 脚本在性能分析模式下测量的时间）。 为了将此结果放在上下文中，blk-mq（mq-deadline）中可用的最轻 I/O 调度程序的总的、单锁保护的、每个请求的执行时间为 0.7 us（mq-deadline 约为 800 LOC， 与 BFQ 的 ~10500 LOC 相比）。

调度开销进一步限制了 CPU 可以处理的最大 IOPS（已经受到 I/O 堆栈其余部分的执行的限制）。 为了了解 BFQ 在慢速或普通 CPU 上的限制，首先是三种不同 CPU 的 BFQ 限制，分别在普通笔记本电脑、旧台式机和廉价嵌入式系统上，以防万一 启用完整分层支持（即设置 CONFIG_BFQ_GROUP_IOSCHED），但未设置 CONFIG_BFQ_CGROUP_DEBUG（第 4-2 节）： - Intel i7-4850HQ：400 KIOPS - AMD A8-3850：250 KIOPS - ARM CortexTM-A53 八核： 80KIOPS

如果设置了 CONFIG_BFQ_CGROUP_DEBUG（当然启用了完整的分层支持），则 BFQ 的可持续吞吐量会降低，因为所有 blkio.bfq* 统计信息都会被创建和更新（第 4-2 节）。 对于 BFQ，这会在与上述相同的系统上产生以下最大可持续吞吐量： - Intel i7-4850HQ：310 KIOPS - AMD A8-3850：200 KIOPS - ARM CortexTM-A53 八核：56 KIOPS

BFQ 也适用于多队列设备。


# 1. When may BFQ be useful?

BFQ 在个人和服务器系统上提供以下优势。

## 1.1 Personal systems

**Low latency for interactive applications**

无论实际的后台工作负载如何，BFQ 都能保证，对于交互式任务，存储设备的响应速度实际上就像空闲时一样。 例如，即使正在执行以下一个或多个后台工作负载：

- 正在读取、写入或复制一个或多个大文件，
- 正在编译源文件树，
- 一个或多个虚拟机正在执行 I/O，
- 软件更新正在进行中，
- 索引守护进程正在扫描文件系统并更新其数据库，

启动应用程序或从应用程序内加载文件所需的时间与存储设备空闲的时间大致相同。 相比之下，使用 CFQ、NOOP 或 DEADLINE，在相同条件下，应用程序会遇到高延迟，甚至变得无响应，直到后台工作负载终止（也在 SSD 上）。

**Low latency for soft real-time applications**

此外，无论后台 I/O 工作负载如何，软实时应用程序（例如音频和视频播放器/流媒体）都享有低延迟和低丢包率。 因此，这些应用程序几乎不会因后台工作负载而出现任何故障。

**Higher speed for code-development tasks**

如果某些额外的工作负载恰好并行执行，则 BFQ 执行典型代码开发任务（编译、检出、合并等）的 I/O 相关组件的速度比 CFQ、NOOP 或 DEADLINE 快得多。

**High throughput**

在硬盘上，BFQ 的吞吐量比 CFQ 高出 30%，比 DEADLINE 和 NOOP 高出 150%，并且我们的测试中考虑了所有顺序工作负载。 对于随机工作负载以及基于闪存的设备上的所有工作负载，BFQ 实现的吞吐量与其他调度程序大致相同。

**Strong fairness, bandwidth and delay guarantees**

BFQ 在 I/O 密集型应用程序之间按其权重比例分配设备吞吐量，而不仅仅是设备时间，无论工作负载如何，无论设备参数如何。 根据这些带宽保证，可以通过一个简单的公式计算严格的每个 I/O 请求延迟保证。 如果未配置严格的服务保证，BFQ 会（仅）针对可能导致吞吐量损失的应用程序切换到基于时间的资源共享。


## 1.2 Server systems

服务器系统的大多数好处都来自与上述相同的服务属性。 特别是，无论是否提供额外的、可能繁重的工作负载，BFQ 都保证：

- 零或极低抖动和丢包率的音频和视频流；
- 快速检索网页和嵌入对象；
- 实时转储应用程序中的数据实时记录（例如数据包记录）；
- 本地和远程访问服务器的响应能力。


# 2. How does BFQ work?

BFQ是一个比例份额I/O调度器，其总体结构以及大量代码借鉴于CFQ。

- 在设备上执行 I/O 的每个进程都与一个权重和一个 (bfq_) 队列相关联。

- BFQ 一次授予一个队列（进程）对设备的独占访问权，并通过将每个队列与预算（以扇区数衡量）相关联来实现此服务模型。

    - 队列被授予对设备的访问权限后，队列的预算会在每次请求分派时减少请求的大小。

    - 仅当发生以下事件之一时，服务中队列才会过期，即其服务被暂停：1) 队列完成其预算，2) 队列清空，3) 触发“预算超时”。

        - 预算超时可防止执行随机 I/O 的进程占用设备时间过长，从而显着降低吞吐量。

        - 实际上，与CFQ中一样，与发出同步请求的进程关联的队列在清空时可能不会立即过期。 相反，BFQ 可能会在很短的时间间隔内使设备空闲，从而使进程在及时发出新请求的情况下有机会继续获得服务。 如果进程执行同步和顺序 I/O，设备空闲通常会提高旋转设备和非排队基于闪存的设备的吞吐量。 此外，在 BFQ 下，设备空闲也有助于保证发出同步请求的进程所需的吞吐量分数（请参阅本文档中 slice_idle 可调参数的描述，或 [1, 2]，了解更多详细信息）。

            - 对于服务保证的空闲，如果多个进程同时竞争设备，但所有进程和组具有相同的权重，则 BFQ 可以保证预期的吞吐量分配，而无需使设备空闲。 因此，在这种常见情况下，吞吐量尽可能高。

            - 在具有内部命令队列（通常是 NCQ）的基于闪存的存储上，设备空闲总是对吞吐量不利。 因此，对于这些设备，BFQ 仅在服务保证严格需要时才执行空闲，即保证低延迟或公平性。 在这些情况下，总体吞吐量可能不是最佳的。 目前还没有解决方案可以在具有内部队列的设备上提供强大的服务保证和最佳吞吐量。

    - 如果启用低延迟模式（默认配置），BFQ 会执行一些特殊的启发式方法来检测交互式和软实时应用程序（例如视频或音频播放器/流媒体），并减少其延迟。 为实现此目标而采取的最重要的操作是为与这些应用程序关联的队列提供超过其公平份额的设备吞吐量。 为简洁起见，我们将 BFQ 为赋予这些队列特权而采取的整套操作称为“权重提升”。 特别是，BFQ 为交互式应用提供了更温和的增重形式，为软实时应用提供了更强的形式。

    - BFQ 自动停用在队列创建突发中产生的队列的空闲状态。 事实上，这些队列通常与主要受益于高吞吐量的应用程序和服务的进程相关联。 示例包括启动期间的 systemd 或 git grep。

    - 与 CFQ 一样，BFQ 合并执行交错 I/O 的队列，即执行随机 I/O，如果合并，这些队列将变得大部分是顺序的。 与 CFQ 不同，BFQ 通过一种更具反应性的机制来实现这一目标，称为早期队列合并（EQM）。 EQM 在检测交错 I/O（协作进程）方面反应灵敏，使得 BFQ 通过队列合并实现高吞吐量，甚至对于 CFQ 需要不同机制（抢占）以获得高吞吐量的队列也是如此。 因此，EQM 是一种通过交错 I/O 实现高吞吐量的统一机制。

    - 队列根据 WF2Q+ 的变体（名为 B-WF2Q+）进行调度，并使用增强的 rb 树来实现，以保持 O(log N) 的整体复杂性。 更多细节请参见[2]。 B-WF2Q+ 还准备好进行分层调度，详细信息请参见第 4 节。

    - B-WF2Q+保证了与理想、完全公平和平稳服务的严格偏差。 特别是，B-WF2Q+ 保证每个队列接收与其权重成比例的设备吞吐量的一部分，即使吞吐量波动，并且无论：设备参数、当前工作负载和分配给队列的预算。

    - 最后一个，预算独立的财产（尽管一开始可能违反直觉）绝对是有益的，原因如下：

        - 首先，对于任何比例共享调度程序，理想服务的最大偏差与分配给队列的最大预算（切片）成正比。 因此，BFQ 可以保持这种偏差紧密，不仅因为 B-WF2Q+ 的准确服务，而且因为 BFQ 不需要为队列分配更大的预算来让队列接收更高比例的设备吞吐量。

        - 其次，BFQ 可以为每个进程（队列）自由选择最适合进程需求的预算，或者最好地利用进程的 I/O 模式。 特别是，BFQ 使用简单的反馈循环算法更新队列预算，从而实现高吞吐量，同时仍然为时间敏感的应用程序提供严格的延迟保证。 当服务队列到期时，该算法计算队列的下一个预算，以便：

            - 最终将大量预算分配给与执行顺序 I/O 的 I/O 密集型应用程序关联的队列：事实上，一旦访问设备，这些应用程序的服务时间越长，吞吐量就越高。

            - 让小预算最终分配给与时间敏感的应用程序（通常执行零星和短 I/O）相关的队列，因为分配给等待服务的队列的预算越小，B-WF2Q+ 服务越早 该队列（[2] 中的第 3.3 节）。

- 如果多个进程同时竞争设备，但所有进程和组都具有相同的权重，则 BFQ 可以保证预期的吞吐量分布，而不会导致设备空闲。 它使用抢占代替。 在这种常见情况下，吞吐量会高得多。

- ioprio 类按严格的优先级顺序提供服务，即只要存在较高优先级队列，就不会提供较低优先级队列。 同一类的队列之间，按照每个队列的权重比例分配带宽。 然而，为空闲类保证了非常薄的额外带宽，以防止其饥饿。


# 3. What are BFQ's tunables and how to properly configure BFQ?

大多数 BFQ 可调参数都会影响服务保证（主要是延迟和公平性）和吞吐量。 有关如何在服务保证和吞吐量之间选择所需权衡的完整详细信息，请参阅参数 slice_idle、strict_guarantees 和 low_latency。 有关如何最大化吞吐量的详细信息，请参阅 slice_idle、timeout_sync 和 max_budget。 其他与性能相关的参数继承自 CFQ，并保留下来主要是为了与 CFQ 兼容。 到目前为止，在改变 BFQ 中的后面的参数后，还没有任何性能改进的报告。

特别是，下面的可调参数 back_seek-max、back_seek_penalty、fifo_expire_async 和 fifo_expire_sync 与 CFQ 中的相同。 他们的描述只是从 CFQ 的描述中复制而来。 slice_idle 描述中的一些注意事项也复制自 CFQ。

**per-process ioprio and weight**

除非使用 cgroups 接口（参见“4. BFQ 组调度”），否则权重只能通过 I/O 优先级间接分配给进程，并且根据以下关系：权重 = (IOPRIO_BE_NR - ioprio) * 10。

请注意，如果设置了低延迟，BFQ 会自动提高与交互式和软实时应用程序关联的队列的权重。 如果您需要/想要控制权重，请取消设置此可调参数。

**slice_idle**

此参数指定当某些同步 BFQ 队列变空时，BFQ 应为下一个 I/O 请求空闲多长时间。 默认情况下，slice_idle 是一个非零值。 空闲有双重目的：提高吞吐量并确保遵守所需的吞吐量分布（请参阅 BFQ 如何工作的描述，如果需要，请参阅那里引用的论文）。

至于吞吐量，空闲对于单轴 SATA/SAS 磁盘等高寻道介质非常有帮助，我们可以减少寻道总数并提高吞吐量。

将 slice_idle 设置为 0 将消除队列上的所有空闲，并且人们应该会看到更快的存储设备（例如硬件 RAID 配置中的多个 SATA/SAS 磁盘）以及具有内部命令队列（和并行性）的基于闪存的存储设备上吞吐量的整体提高。

因此，根据存储和工作负载，设置 slice_idle=0 可能会很有用。 一般来说，对于 SATA/SAS 磁盘和 SATA/SAS 磁盘的软件 RAID，保持 slice_idle 启用应该很有用。 对于单个 LUN（基于主机的硬件 RAID 控制器或存储阵列）后面有多个轴的任何配置，或者具有基于闪存的快速存储，设置 slice_idle=0 可能最终会获得更好的吞吐量和可接受的延迟。

然而，为了在不同权重或不同 I/O 请求长度的情况下强制实施服务保证，空闲是必要的。 要了解原因，假设给定的 BFQ 队列 A 必须为另一个队列 B 提供的每个请求提供多个 I/O 请求。空闲可确保，如果 A 在变空后不久发出新的 I/O 请求，则不会有任何请求 B 在中间被调度，因此 A 不会失去在调度 B 的下一个请求之前调度多个请求的可能性。 请注意，空闲仅在 I/O 请求分派方面保证队列的所需差异化处理。 为了保证实际的服务顺序与调度顺序相对应，还必须设置 strict_guarantees 可调参数。

空闲有一个重要的反面：除了上述对吞吐量也有利的情况外，空闲还会严重影响吞吐量。 一种重要的情况是随机工作负载。 由于这个问题，BFQ 倾向于尽可能避免空闲，当它对吞吐量也没有好处时（如第 2 节所述）。 由于此行为以及为 strict_guarantees 可调参数描述的进一步问题，短期服务保证可能偶尔会被违反。 而且，在某些情况下，这些保证可能比保证最大吞吐量更重要。 例如，在视频播放/流传输中，非常低的丢包率可能比最大吞吐量更重要。 在这些情况下，请考虑设置 strict_guarantees 参数。

**slice_idle_us**

控制与 slice_idle 相同的调整参数，但以微秒为单位。 任一可调参数均可用于设置空闲行为。 之后，另一个可调参数将反映 sysfs 中新设置的值。

**strict_guarantees**

如果设置了该参数（默认：未设置），则BFQ

- 当服务队列变空时总是执行空闲；
- 仅在没有未完成的请求时才分派新请求，强制设备一次服务一个 I/O 请求。

在存在不同权重或 I/O 请求大小的情况下，需要满足上述两个条件才能保证每个 BFQ 队列收到其分配的带宽份额。 需要第一个条件的原因在 slice_idle 可调参数的描述中进行了解释。 需要第二个条件是因为所有现代存储设备都会对内部排队的请求进行重新排序，这可能会轻微破坏 I/O 调度程序强制执行的服务保证。

设置 strict_guarantees 显然可能会影响吞吐量。

**back_seek_max**

这指定了向后查找的最大“距离”（以千字节为单位）。 该距离是从当前磁头位置到距离靠后的扇区的空间量。

此参数允许调度程序预测“向后”方向上的请求，并在距当前头部位置的此距离内将它们视为“下一个”。

**back_seek_penalty**

该参数用于计算向后搜索的成本。 如果请求的后向距离距“前”请求仅为 1/back_seek_penalty，则两个请求的查找成本被认为是相等的。

因此调度程序不会偏向一个或另一个请求（否则调度程序将偏向前端请求）。 back_seek_penalty 的默认值为 2。

**fifo_expire_async**

该参数用于设置异步请求的超时时间。 默认值为 250ms。

**fifo_expire_sync**

该参数用于设置同步请求的超时时间。 默认值为 125ms。 为了使同步请求优于异步请求，该值应相对于 fifo_expire_async 减小。

**low_latency**

该参数用于启用/禁用BFQ的低延迟模式。 默认情况下，启用低延迟模式。 如果启用，交互式和软实时应用程序将享有特权并体验较低的延迟，如 BFQ 工作原理的描述中更详细地解释的那样。

如果您需要完全控制带宽分配，请禁用此模式。 事实上，如果启用它，BFQ 会自动增加特权应用程序的带宽份额，作为保证它们较低延迟的主要手段。

此外，正如本文档开头所强调的那样，如果您的唯一目标是实现高吞吐量，请禁用此模式。 事实上，使某些应用程序的 I/O 优先于其他应用程序可能会导致吞吐量降低。 为了在非旋转设备上实现尽可能高的吞吐量，可能还需要将 slice_idle 设置为 0（代价是放弃对公平性和低延迟的任何有力保证）。

**timeout_sync**

选择任务（队列）进行服务后可以分配给该任务（队列）的最大设备时间。 在具有昂贵搜索成本的设备上，增加该时间通常会增加最大吞吐量。 另一方面，增加此时间会粗化短期带宽和延迟保证的粒度，特别是在以下参数设置为零的情况下。

**max_budget**

一旦 BFQ 队列投入使用，就可以向该队列提供最大服务量（以扇区为单位）（当然在上述超时限制内）。 根据算法描述中所述，较大的值与发出的顺序 I/O 请求的百分比成比例地增加吞吐量。 较大值的代价是它们会粗化短期带宽和延迟保证的粒度。

默认值为 0，启用自动调整：BFQ 根据估计的峰值速率将 max_budget 设置为 timeout_sync 期间可以服务的最大扇区数。

对于特定设备，一些用户偶尔会报告通过显式设置 max_budget（即将 max_budget 设置为高于 0 的值）达到了更高的吞吐量。特别是，他们将 max_budget 设置为高于 BFQ 设置的值 它具有自动调整功能。 实现此目标的另一种方法是仅增加 timeout_sync 的值，使 max_budget 等于 0。


# 4. Group scheduling with BFQ

BFQ 同时支持 cgroups-v1 和 cgroups-v2 io 控制器，即 blkio 和 io。 特别是，BFQ 支持基于权重的比例共享。 要激活 cgroup 支持，请设置 BFQ_GROUP_IOSCHED。


## 4.1 Service guarantees provided

对于 BFQ，比例共享意味着根据组权重，设备带宽的真正比例共享。 例如，权重为 200 的组获得的带宽是权重 100 的组的两倍，而不仅仅是时间的两倍。

BFQ 支持任意深度的层次结构（组树）。 带宽以预期的方式在组和进程之间分配：对于每个组，该组的子组按照其权重的比例共享该组的整个带宽。 特别是，这意味着，对于每个叶组，该组的每个进程都会接收整个组带宽的相同份额，除非该进程的 ioprio 被修改。

如果向组提供带宽保证使吞吐量降低太多，则该组的资源共享保证可以部分或全部从带宽切换到时间。 这种切换发生在每个进程的基础上：如果叶组的某个进程以接收其带宽份额的方式提供服务，导致吞吐量损失，则 BFQ 会切换回该进程的基于时间的比例共享。


## 4.2 Interface

为了与给定设备的 BFQ 实现按比例共享带宽，BFQ 当然必须是该设备的活动调度程序。

在每个组目录中，与特定于 BFQ 的 cgroup 参数和统计信息关联的文件的名称以“bfq”开头。 字首。 因此，对于 cgroups-v1 或 cgroups-v2，BFQ 特定文件的完整前缀是“blkio.bfq”。 或“io.bfq”。 例如，使用 BFQ 设置组权重的组参数是 blkio.bfq.weight 或 io.bfq.weight。

对于 cgroups-v1（blkio 控制器），bfq 创建并保持最新的 stat 文件集取决于是否设置了 CONFIG_BFQ_CGROUP_DEBUG。 如果已设置，则 bfq 将创建块 IO 控制器中记录的所有统计文件。 相反，如果未设置 CONFIG_BFQ_CGROUP_DEBUG，则 bfq 仅创建以下文件：

```bash
blkio.bfq.io_service_bytes
blkio.bfq.io_service_bytes_recursive
blkio.bfq.io_serviced
blkio.bfq.io_serviced_recursive
```

CONFIG_BFQ_CGROUP_DEBUG 的值极大地影响了 bfq 可持续的最大吞吐量，因为更新 blkio.bfq.* 统计信息的成本相当高，特别是对于 CONFIG_BFQ_CGROUP_DEBUG 启用的某些统计信息。

**Parameters**

对于每个组，可以设置以下参数：

**weight**

&emsp;这指定了 cgroup 在其父级中的默认权重。 可用值：1..1000（默认值：100）。

&emsp;对于cgroup v1，它是通过将值写入blkio.bfq.weight来设置的。

&emsp;对于cgroup v2，它是通过将值写入io.bfq.weight来设置的。 （带有可选的默认前缀和空格）。

&emsp;在可调部分开头描述的 ioprio 和权重之间的线性映射仍然有效，但所有高于 IOPRIO_BE_NR*10 的权重都映射到 ioprio 0。

&emsp;回想一下，如果设置了低延迟，那么 BFQ 会自动提高与交互式和软实时应用程序关联的队列的权重。 如果您需要/想要控制权重，请取消设置此可调参数。

**weight_device**

&emsp;这指定了 cgroup 的每个设备的权重。 语法为次要：主要权重。 权重 0 可用于重置为默认权重。

&emsp;对于cgroup v1，它是通过将值写入blkio.bfq.weight_device来设置的。

&emsp;对于 cgroup v2，文件名是 io.bfq.weight。

[1] P. Valente、A. Avanzini，“BFQ 存储 I/O 调度程序的演变”，第一届移动系统技术研讨会论文集 (MST-2015)，2015 年 5 月。

<http://algogroup.unimore.it/people/paolo/disk_sched/mst-2015.pdf>

[2] P. Valente 和 M. Andreolini，“使用 BFQ 磁盘 I/O 调度程序提高应用程序响应能力”，第五届年度国际系统和存储会议 (SYSTOR '12) 会议记录，2012 年 6 月。

稍微扩展的版本：

<http://algogroup.unimore.it/people/paolo/disk_sched/bfq-v1-suite-results.pdf>

[3]

<https://github.com/Algodev-github/S>