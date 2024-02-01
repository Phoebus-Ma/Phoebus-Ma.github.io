
# Multi-Queue Block IO Queueing Mechanism (blk-mq)

多队列块 IO 排队机制是一种 API，使快速存储设备能够通过对块设备同时排队和提交 IO 请求来实现每秒大量的输入/输出操作 (IOPS)，从而受益于现代存储提供的并行性 设备。


# 1. Introduction

## 1.1 Background

从内核开发之初起，磁硬盘就已经成为事实上的标准。 块 IO 子系统旨在为那些在进行随机访问时具有较高惩罚的设备实现最佳性能，而瓶颈是机械移动部件，比存储堆栈上的任何层都要慢得多。 这种优化技术的一个示例涉及根据硬盘磁头的当前位置对读/写请求进行排序。

然而，随着固态硬盘和非易失性存储器的发展，没有机械部件，也没有随机访问惩罚，并且能够执行高并行访问，堆栈的瓶颈已经从存储设备转移到操作系统。 为了利用这些设备设计中的并行性，引入了多队列机制。

以前的设计有一个队列来存储带有单个锁的块 IO 请求。 由于缓存中的脏数据以及多个处理器具有单个锁的瓶颈，这在 SMP 系统中无法很好地扩展。 当不同进程（或移动到不同 CPU 的同一进程）想要执行块 IO 时，此设置也会遇到拥塞。 取而代之的是，blk-mq API 生成多个队列，其中具有 CPU 本地的各个入口点，从而消除了对锁的需要。 以下部分（操作）将更深入地解释其工作原理。


## 1.2 Operation

当用户空间对块设备执行 IO（例如读取或写入文件）时，blk-mq 会采取行动：它将存储和管理对块设备的 IO 请求，充当用户空间（和文件系统、 如果存在）和块设备驱动程序。

blk-mq 有两组队列：软件暂存队列和硬件调度队列。 当请求到达块层时，它会尝试尽可能最短的路径：直接发送到硬件队列。 然而，有两种情况它可能不会这样做：如果该层附加了 IO 调度程序，或者我们想尝试合并请求。 在这两种情况下，请求都会发送到软件队列。

然后，在软件队列处理请求后，它们将被放置在硬件队列中，这是第二级队列，硬件可以直接访问处理这些请求。 但是，如果硬件没有足够的资源来接受更多请求，blk-mq 会将请求放入临时队列中，以便在将来硬件有能力时发送。


## 1.3 Software staging queues

块 IO 子系统将请求添加到软件暂存队列（由 struct blk_mq_ctx 表示）中，以防它们没有直接发送到驱动程序。 一个请求是一个或多个 BIO。 他们通过数据结构struct bio到达了块层。 然后，块层将从中构建一个新的结构，即用于与设备驱动程序通信的结构请求。 每个队列都有自己的锁，队列的数量是按每个 CPU 或每个节点定义的。

暂存队列可用于合并对相邻扇区的请求。 例如，对扇区 3-6、6-7、7-9 的请求可以成为对 3-9 的一个请求。 即使对 SSD 和 NVM 的随机访问与顺序访问相比具有相同的响应时间，顺序访问的分组请求也会减少单个请求的数量。 这种合并请求的技术称为插入。

除此之外，IO 调度程序还可以对请求进行重新排序，以确保系统资源的公平性（例如，确保没有应用程序遭受饥饿）和/或提高 IO 性能。


### 1.3.1 IO Schedulers

块层实现了多个调度程序，每个调度程序都遵循启发式方法来提高 IO 性能。 它们是“可插入的”（即即插即用），即可以使用 sysfs 在运行时选择它们。 您可以在此处阅读有关 Linux IO 调度程序的更多信息。 调度仅发生在同一队列中的请求之间，因此不可能合并来自不同队列的请求，否则会出现缓存垃圾并且需要为每个队列加锁。 调度后，请求就可以发送到硬件。 可以选择的调度程序之一是 NONE 调度程序，这是最简单的一种。 它只会将请求放在进程正在运行的任何软件队列上，而不进行任何重新排序。 当设备开始处理硬件队列中的请求（也称为运行硬件队列）时，映射到该硬件队列的软件队列将根据其映射顺序依次排出。


# 1.4 Hardware dispatch queues

硬件队列（由 struct blk_mq_hw_ctx 表示）是设备驱动程序用来映射设备提交队列（或设备 DMA 环形缓冲区）的结构体，是低级设备驱动程序取得所有权之前块层提交代码的最后一步。 的请求。 为了运行该队列，块层从关联的软件队列中删除请求并尝试分派到硬件。

如果无法将请求直接发送到硬件，它们将被添加到请求的链接列表（hctx->dispatch）中。 然后，下次块层运行队列时，它将首先发送位于调度列表中的请求，以确保对那些准备好首先发送的请求进行公平调度。 硬件队列的数量取决于硬件及其设备驱动程序支持的硬件上下文的数量，但不会超过系统的核心数量。 此阶段没有重新排序，每个软件队列都有一组硬件队列来发送请求。

**Note**

块层和设备协议都不保证请求的完成顺序。 这必须由更高层处理，例如文件系统。


## 1.5 Tag-based completion

为了指示哪个请求已完成，每个请求都由一个整数标识，范围从 0 到调度队列大小。 该标记由块层生成，随后由设备驱动程序重用，从而无需创建冗余标识符。 当驱动程序中完成请求时，标记将被发送回块层以通知其完成。 这样就无需进行线性搜索来找出哪个 IO 已完成。


## 1.6 Further reading

- [Linux Block IO: Introducing Multi-queue SSD Access on Multi-core Systems](http://kernel.dk/blk-mq.pdf)
- [NOOP scheduler](https://en.wikipedia.org/wiki/Noop_scheduler)
- [Null block device driver](https://www.kernel.org/doc/html/latest/block/null_blk.html)


# 2. Source code documentation

```c
// 将结构请求从一个列表移动到另一个列表。
void rq_list_move(struct request **src, struct request **dst, struct request *rq, struct request *prev)

// 超时处理程序应如何继续。
enum blk_eh_timer_return

// 面向硬件块设备的硬件队列的状态。
struct blk_mq_hw_ctx

// 将软件队列映射到硬件队列。
struct blk_mq_queue_map

// 硬件队列的类型。
enum hctx_type

// 可以在请求队列之间共享的标记集。
struct blk_mq_tag_set

// 有关插入队列中的请求的数据。
struct blk_mq_queue_data

// 实现块驱动程序行为的回调函数。
struct blk_mq_ops

// 读取请求的当前 MQ_RQ_* 状态。
enum mq_rq_state blk_mq_rq_state(struct request *rq)

// 将 PDU 转换为请求。
struct request *blk_mq_rq_from_pdu(void *pdu)

// 将请求投射到 PDU。
void *blk_mq_rq_to_pdu(struct request *rq)

// 检查 rq 是否需要写入序列化。
bool blk_rq_is_seq_zoned_write(struct request *rq)

// 等待正在进行的停顿完成。
void blk_mq_wait_quiesce_done(struct blk_mq_tag_set *set)

// 等待所有正在进行的调度完成。
void blk_mq_quiesce_queue(struct request_queue *q)

// 完成多个字节而不完成请求。
bool blk_update_request(struct request *req, blk_status_t error, unsigned int nr_bytes)

// 结束请求的 I/O。
void blk_mq_complete_request(struct request *rq)

// 开始处理请求。
void blk_mq_start_request(struct request *rq)

// 向 I/O 调度程序插入一个请求来执行。
void blk_execute_rq_nowait(struct request *rq, bool at_head)

// 将请求插入队列以供执行。
blk_status_t blk_execute_rq(struct request *rq, bool at_head)

// 异步运行硬件队列。
void blk_mq_delay_run_hw_queue(struct blk_mq_hw_ctx *hctx, unsigned long msecs)

// 开始运行硬件队列。
void blk_mq_run_hw_queue(struct blk_mq_hw_ctx *hctx, bool async)

// 运行请求队列中的所有硬件队列。
void blk_mq_run_hw_queues(struct request_queue *q, bool async)

// 异步运行所有硬件队列。
void blk_mq_delay_run_hw_queues(struct request_queue *q, unsigned long msecs)

// 在调度列表中插入请求。
void blk_mq_request_bypass_insert(struct request *rq, blk_insert_t flags)

// 尝试直接向设备驱动程序发送请求。
void blk_mq_try_issue_directly(struct blk_mq_hw_ctx *hctx, struct request *rq)

// 创建请求并将其发送到块设备。
void blk_mq_submit_bio(struct bio *bio)

// 堆栈驱动程序提交请求的帮助程序。
blk_status_t blk_insert_cloned_request(struct request *rq)

// 帮助函数释放克隆请求中的所有 BIOS。
void blk_rq_unprep_clone(struct request *rq)

// 设置克隆请求的辅助函数。
int blk_rq_prep_clone(struct request *rq, struct request *rq_src, struct bio_set *bs, gfp_t gfp_mask, int (*bio_ctr)(struct bio*, struct bio*, void*), void *data)

// 为每个克隆生物调用设置函数。 返回 0 表示成功，非 0 表示失败。
int (*bio_ctr)(struct bio *, struct bio *, void *)

// 关闭请求队列。
void blk_mq_destroy_queue(struct request_queue *q)
```
