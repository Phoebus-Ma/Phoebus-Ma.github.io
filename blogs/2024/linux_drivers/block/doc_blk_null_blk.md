
# Null block device driver

# 1. Overview

空块设备 (/dev/nullb*) 用于对各种块层实现进行基准测试。 它模拟 X GB 大小的块设备。 它不执行任何读/写操作，只是在请求队列中将它们标记为完成。 以下情况是可能的：

多队列块层

- 基于请求。
- 每个设备可配置的提交队列。

无阻挡层（称为生物基）

- 基于Bio。 IO 请求直接提交给设备驱动程序。
- 直接接受bio数据结构并返回它们。

它们都为系统中的每个核心都有一个完成队列。


# 2. Module parameters

queue_mode=[0-2]: Default: 2-Multi-queue

&emsp;选择模块应实例化的块层。

|   |                       |
| - | --------------------- |
| 0 | Bio-based             |
| 1 | Single-queue (已弃用) |
| 2 | Multi-queue           |

home_node=[0--nr_nodes]: Default: NUMA_NO_NODE

&emsp;选择从哪个 CPU 节点分配数据结构。

gb=[Size in GB]: Default: 250GB

&emsp;向系统报告的设备的大小。

bs=[Block size (in bytes)]: Default: 512 bytes

&emsp;向系统报告的块大小。

nr_devices=[Number of devices]: Default: 1

&emsp;实例化的块设备数量。 它们被实例化为 /dev/nullb0 等。

irqmode=[0-2]: Default: 1-Soft-irq

&emsp;用于完成块层 IO 的完成模式。

|   |                                                                                          |
| - | ---------------------------------------------------------------------------------------- |
| 0 | None                                                                                     |
| 1 | 软中断。 使用IPI完成跨CPU节点的IO。 模拟从设备所连接的主节点以外的其他 CPU 节点发出 IO 时的开销 |
| 2 | Timer: 在完成之前等待每个 IN 特定的时间（完成纳秒）                                          |

completion_nsec=[ns]: Default: 10,000ns

&emsp;与 irqmode=2（定时器）结合。 每个完成事件必须等待的时间。

submit_queues=[1..nr_cpus]: Default: 1

&emsp;附加到设备驱动程序的提交队列的数量。 如果未设置，则默认为1。对于多队列，当use_per_node_hctx模块参数为1时，它被忽略。

hw_queue_depth=[0..qdepth]: Default: 64

&emsp;设备的硬件队列深度。

memory_backed=[0/1]: Default: 0

&emsp;是否使用内存缓冲区来响应 IO 请求。

|   |                            |
| - | -------------------------- |
| 0 | 不传输任何数据来响应 IO 请求 |
| 1 | 使用内存缓冲区来响应 IO 请求 |

discard=[0/1]: Default: 0

&emsp;支持丢弃操作（需要内存支持的 null_blk 设备）。

|   |                    |
| - | ------------------ |
| 0 | 不支持丢弃操作      |
| 1 | 启用对丢弃操作的支持 |

cache_size=[Size in MB]: Default: 0

&emsp;内存支持设备的缓存大小（以 MB 为单位）。

mbps=[Maximum bandwidth in MB/s]: Default: 0 (no limit)

&emsp;设备性能的带宽限制。


# 3. Multi-queue specific parameters

use_per_node_hctx=[0/1]: Default: 0

&emsp;硬件上下文队列的数量。

|   |                                               |
| - | --------------------------------------------- |
| 0 | 提交队列的数量设置为submit_queues参数的值        |
| 1 | 多队列块层实例化为系统中每个CPU节点的硬件调度队列 |

no_sched=[0/1]: Default: 0

&emsp;启用/禁用 io 调度程序。

|   |                                    |
| - | ---------------------------------- |
| 0 | nullb* 使用默认的 blk-mq io 调度程序 |
| 1 | nullb* 不使用 io 调度程序           |

blocking=[0/1]: Default: 0

&emsp;请求队列的阻塞行为。

|   |                                                                                                        |
| - | ------------------------------------------------------------------------------------------------------ |
| 0 | 注册为非阻塞 blk-mq 驱动设备                                                                             |
| 1 | 注册为阻塞blk-mq驱动设备，null_blk会设置BLK_MQ_F_BLOCKING标志，表明它有时/总是需要在其->queue_rq()函数中阻塞 |

shared_tags=[0/1]: Default: 0

&emsp;在设备之间共享标签。

|   |                                                                                    |
| - | ---------------------------------------------------------------------------------- |
| 0 | 标签集不共享                                                                        |
| 1 | blk-mq 的设备之间共享的标签集。 仅当 nr_devices > 1 时才有意义，否则没有设置可共享的标签 |

zoned=[0/1]: Default: 0

&emsp;设备是随机访问或分区块设备。

|   |                                                            |
| - | ---------------------------------------------------------- |
| 0 | 块设备作为随机访问块设备公开                                  |
| 1 | 块设备作为主机管理的分区块设备公开。 需要 CONFIG_BLK_DEV_ZONED |

zone_size=[MB]: Default: 256

&emsp;作为分区块设备公开时的每个区域大小。 必须是二的幂。

zone_nr_conv=[nr_conv]: Default: 0

&emsp;块设备分区时要创建的常规区域的数量。 如果 zone_nr_conv >= nr_zones，则会减少到 nr_zones - 1。
