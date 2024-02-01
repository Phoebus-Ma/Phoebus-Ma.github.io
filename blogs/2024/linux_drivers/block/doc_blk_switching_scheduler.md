
# Switching Scheduler

每个 io 队列都有一组与其关联的 io 调度程序可调参数。 这些可调参数控制 io 调度程序的工作方式。 您可以在以下位置找到这些条目：

```bash
/sys/block/<device>/queue/iosched
```

假设您已将 sysfs 安装在 /sys 上。 如果您没有安装 sysfs，可以输入以下命令：

```bash
# mount none /sys -t sysfs
```

可以动态更改给定块设备的 IO 调度程序，以选择 mq-deadline、none、bfq 或 kyber 调度程序之一 - 这可以提高该设备的吞吐量。

要设置特定的调度程序，只需执行以下操作：

```bash
echo SCHEDNAME > /sys/block/DEV/queue/scheduler
```

其中 SCHEDNAME 是定义的 IO 调度程序的名称，DEV 是设备名称（hda、hdb、sga 或您碰巧拥有的任何名称）。

只需执行“cat /sys/block/DEV/queue/scheduler”即可找到定义的调度程序列表 - 将显示有效名称列表，当前选择的调度程序位于括号中：

```bash
# cat /sys/block/sda/queue/scheduler
[mq-deadline] kyber bfq none
# echo none >/sys/block/sda/queue/scheduler
# cat /sys/block/sda/queue/scheduler
[none] mq-deadline kyber bfq
```
