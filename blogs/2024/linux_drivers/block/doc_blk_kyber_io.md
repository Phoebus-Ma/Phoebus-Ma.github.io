
# Kyber I/O scheduler tunables

Kyber 调度程序唯一的两个可调参数是读取和同步写入的目标延迟。 Kyber 将限制请求以满足这些目标延迟。

**read_lat_nsec**

读取的目标延迟（以纳秒为单位）。

**write_lat_nsec**

同步写入的目标延迟（以纳秒为单位）。
