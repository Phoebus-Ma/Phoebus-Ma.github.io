
# Block layer statistics in /sys/block/<dev>/stat

# 1. Introduction

此文件记录 /sys/block/<dev>/stat 文件的内容。

stat 文件提供了有关块设备 <dev> 状态的多项统计信息。

为什么一个文件中有多个统计信息？ sysfs 通常不是每个文件包含一个值吗？

通过使用单个文件，内核可以保证统计信息代表设备状态的一致快照。 如果将统计数据导出为多个文件，每个文件包含一个统计数据，则无法保证一组读数代表单个时间点。

stat 文件由一行文本组成，其中包含由空格分隔的 17 个十进制值。 下表总结了这些字段，并在下面进行了更详细的描述。

| Name            | units        | description                    |
| --------------- | ------------ | ------------------------------ |
| read I/Os       | requests     | 处理的读取 I/O 数               |
| read merges     | requests     | 与队列中 I/O 合并的读取 I/O 数量 |
| read sectors    | sectors      | 读取的扇区数                    |
| read ticks      | milliseconds | 读取请求的总等待时间             |
| write I/Os      | requests     | 处理的写 I/O 数量               |
| write merges    | requests     | 与队列中 I/O 合并的写入 I/O 数量 |
| write sectors   | sectors      | 写入的扇区数                    |
| write ticks     | milliseconds | 写请求的总等待时间               |
| in_flight       | requests     | 当前正在运行的 I/O 数量          |
| io_ticks        | milliseconds | 该块设备处于活动状态的总时间      |
| time_in_queue   | milliseconds | 所有请求的总等待时间             |
| discard I/Os    | requests     | 已处理的丢弃 I/O 数             |
| discard merges  | requests     | 与队列中 I/O 合并的丢弃 I/O 数量 |
| discard sectors | sectors      | 丢弃的扇区数                    |
| discard ticks   | milliseconds | 丢弃请求的总等待时间             |
| flush I/Os      | requests     | 处理的刷新 I/O 数量             |
| flush ticks     | milliseconds | 刷新请求的总等待时间             |

**read I/Os, write I/Os, discard I/0s**

当 I/O 请求完成时，这些值会递增。


# 2. flush I/Os

当刷新 I/O 请求完成时，这些值会增加。

块层合并刷新请求，一次最多执行一个。 这对磁盘执行的刷新请求进行计数。 不跟踪分区。

**read merges, write merges, discard merges**

当 I/O 请求与已排队的 I/O 请求合并时，这些值会增加。

**read sectors, write sectors, discard_sectors**

这些值计算从此块设备读取、写入或丢弃的扇区数。 所讨论的“扇区”是标准 UNIX 512 字节扇区，而不是任何特定于设备或文件系统的块大小。 当 I/O 完成时，计数器会递增。

**read ticks, write ticks, discard ticks, flush ticks**

这些值计算 I/O 请求在此块设备上等待的毫秒数。 如果有多个 I/O 请求等待，这些值将以大于 1000/秒的速率增加； 例如，如果 60 个读请求平均等待 30 ms，则 read_ticks 字段将增加 60*30 = 1800。

**in_flight**

该值计算已向设备驱动程序发出但尚未完成的 I/O 请求数。 它不包括队列中但尚未发送到设备驱动程序的 I/O 请求。

**io_ticks**

该值计算设备 I/O 请求排队期间的毫秒数。

**time_in_queue**

该值计算 I/O 请求在此块设备上等待的毫秒数。 如果有多个 I/O 请求等待，则该值将随着毫秒数乘以等待请求数的乘积而增加（有关示例，请参阅上面的“读取周期”）。
