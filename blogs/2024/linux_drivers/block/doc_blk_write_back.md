
# Explicit volatile write back cache control

# 1. Introduction

许多存储设备，尤其是消费市场的存储设备，都配备了易失性写回缓存。 这意味着设备在数据实际到达非易失性存储之前向操作系统发出 I/O 完成信号。 这种行为显然会加速各种工作负载，但这意味着操作系统在执行 fsync、sync 或 unmount 等数据完整性操作时需要将数据强制输出到非易失性存储。

Linux 块层提供了两种简单的机制，让文件系统控制存储设备的缓存行为。 这些机制是强制缓存刷新和请求的强制单元访问 (FUA) 标志。


# 2. Explicit cache flushes

REQ_PREFLUSH 标志可以与从文件系统提交的 BIOS 的读/写标志进行或运算，并确保在实际 I/O 操作开始之前存储设备的易失性缓存已被刷新。 这明确保证了在标记的生物启动之前先前完成的写入请求位于非易失性存储上。 此外，REQ_PREFLUSH 标志可以设置在空的 bio 结构上，这只会导致显式缓存刷新，而不会产生任何相关 I/O。 建议使用 blkdev_issue_flush() 帮助程序进行纯粹的缓存刷新。


# 3. Forced Unit Access

REQ_FUA 标志可以与从文件系统提交的 bio 的 r/w 标志进行或运算，并确保仅在数据提交到非易失性存储后才发出此请求的 I/O 完成信号。


# 4. Implementation details for filesystems

文件系统可以简单地设置 REQ_PREFLUSH 和 REQ_FUA 位，而不必担心底层设备是否需要任何显式缓存刷新以及如何实现强制单元访问。 REQ_PREFLUSH 和 REQ_FUA 标志都可以在单个 BIOS 上设置。


# 5. Implementation details for bio based block drivers

这些驱动程序将始终看到 REQ_PREFLUSH 和 REQ_FUA 位，因为它们位于 Submit_bio 接口的正下方。 对于重新映射驱动程序，需要将 REQ_FUA 位传播到底层设备，并且需要为设置了 REQ_PREFLUSH 位的 BIOS 实现全局刷新。 对于没有易失性缓存的实际设备驱动程序，可以简单地忽略非空 BIOS 上的 REQ_PREFLUSH 和 REQ_FUA 位，并且无需执行任何工作即可成功完成没有数据的 REQ_PREFLUSH 请求。 具有易失性缓存的设备的驱动程序需要自己实现对这些标志的支持，而无需块层的任何帮助。


# 6. Implementation details for request_fn based block drivers

对于不支持易失性写入缓存的设备，不需要驱动程序支持，块层在进入驱动程序之前完成空 REQ_PREFLUSH 请求，并从具有有效负载的请求中剥离 REQ_PREFLUSH 和 REQ_FUA 位。 对于具有易失性写入缓存的设备，驱动程序需要告诉块层它支持刷新缓存，方法是：

```bash
blk_queue_write_cache(sdkp->disk->queue, true, false);
```

并在其 prep_fn/request_fn 中处理空 REQ_OP_FLUSH 请求。 请注意，带有有效负载的 REQ_PREFLUSH 请求会自动转换为空 REQ_OP_FLUSH 请求序列，后跟块层的实际写入。 对于也支持 FUA 位的设备，需要使用以下命令告诉块层通过 REQ_FUA 位：

```bash
blk_queue_write_cache(sdkp->disk->queue, true, true);
```

并且驱动程序必须处理在 prep_fn/request_fn 中设置了 REQ_FUA 位的写入请求。 如果 FUA 位本身不支持，则块层在实际写入后将其转换为空 REQ_OP_FLUSH 请求。
