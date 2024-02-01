
# Block io priorities

# 1. Introduction

io 优先级功能使用户能够 io 好的进程或进程组，类似于长期以来的 cpu 调度。 对 io 优先级的支持取决于 io 调度程序，目前由 bfq 和 mq-deadline 支持。


# 2. Scheduling classes

为确定如何为进程提供 io 服务的 io 优先级实现了三个通用调度类。

IOPRIO_CLASS_RT：这是实时 io 类。 该调度类的优先级高于系统中的任何其他调度类，此类进程每次都会首先访问磁盘。 因此使用时需要小心，一个 io RT 进程可能会导致整个系统挨饿。 在 RT 类中，有 8 个级别的类数据，用于准确确定该进程在每个服务上需要磁盘的时间。 将来，这可能会通过传递所需的数据速率来更直接地映射到性能。

IOPRIO_CLASS_BE：这是尽力而为的调度类，它是任何未设置特定 io 优先级的进程的默认值。 类数据决定了进程将获得多少 io 带宽，它可以直接映射到 cpu 的良好级别，只是更粗略地实现。 0 是最高的 BE prio 级别，7 是最低的。 cpu Nice Level 和 io Nice Level 之间的映射确定为：io_nice = (cpu_nice + 20) / 5。

IOPRIO_CLASS_IDLE：这是空闲调度类，运行在该级别的进程仅在没有其他人需要磁盘时获得 io 时间。 空闲类没有类数据，因为它在这里并不真正适用。


# 3. Tools

请参阅下面的 ionice 工具示例。 用法：

```bash
# ionice -c<class> -n<level> -p<pid>
```

如果未给出 pid，则假定为当前进程。 IO 优先级设置在 fork 上继承，因此您可以使用 ionice 在给定级别启动进程：

```bash
# ionice -c2 -n0 /bin/ls
```

将以最高优先级的尽力而为调度类别运行 ls。 对于正在运行的进程，您可以提供 pid：

```bash
# ionice -c1 -n2 -p100
```

将更改 pid 100 以在实时调度类、优先级 2 上运行。

ionice.c tool:

```c
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <getopt.h>
#include <unistd.h>
#include <sys/ptrace.h>
#include <asm/unistd.h>

extern int sys_ioprio_set(int, int, int);
extern int sys_ioprio_get(int, int);

#if defined(__i386__)
#define __NR_ioprio_set               289
#define __NR_ioprio_get               290
#elif defined(__ppc__)
#define __NR_ioprio_set               273
#define __NR_ioprio_get               274
#elif defined(__x86_64__)
#define __NR_ioprio_set               251
#define __NR_ioprio_get               252
#else
#error "Unsupported arch"
#endif

static inline int ioprio_set(int which, int who, int ioprio)
{
    return syscall(__NR_ioprio_set, which, who, ioprio);
}

static inline int ioprio_get(int which, int who)
{
    return syscall(__NR_ioprio_get, which, who);
}

enum {
    IOPRIO_CLASS_NONE,
    IOPRIO_CLASS_RT,
    IOPRIO_CLASS_BE,
    IOPRIO_CLASS_IDLE,
};

enum {
    IOPRIO_WHO_PROCESS = 1,
    IOPRIO_WHO_PGRP,
    IOPRIO_WHO_USER,
};

#define IOPRIO_CLASS_SHIFT    13

const char *to_prio[] = { "none", "realtime", "best-effort", "idle", };

int main(int argc, char *argv[])
{
    int ioprio = 4, set = 0, ioprio_class = IOPRIO_CLASS_BE;
    int c, pid = 0;

    while ((c = getopt(argc, argv, "+n:c:p:")) != EOF) {
        switch (c) {
        case 'n':
            ioprio = strtol(optarg, NULL, 10);
            set = 1;
            break;
        case 'c':
            ioprio_class = strtol(optarg, NULL, 10);
            set = 1;
            break;
        case 'p':
            pid = strtol(optarg, NULL, 10);
            break;
        }
    }

    switch (ioprio_class) {
        case IOPRIO_CLASS_NONE:
            ioprio_class = IOPRIO_CLASS_BE;
            break;
        case IOPRIO_CLASS_RT:
        case IOPRIO_CLASS_BE:
            break;
        case IOPRIO_CLASS_IDLE:
            ioprio = 7;
            break;
        default:
            printf("bad prio class %d\n", ioprio_class);
            return 1;
    }

    if (!set) {
        if (!pid && argv[optind])
            pid = strtol(argv[optind], NULL, 10);

        ioprio = ioprio_get(IOPRIO_WHO_PROCESS, pid);

        printf("pid=%d, %d\n", pid, ioprio);

        if (ioprio == -1)
            perror("ioprio_get");
        else {
            ioprio_class = ioprio >> IOPRIO_CLASS_SHIFT;
            ioprio = ioprio & 0xff;
            printf("%s: prio %d\n", to_prio[ioprio_class], ioprio);
        }
    } else {
        if (ioprio_set(IOPRIO_WHO_PROCESS, pid, ioprio | ioprio_class << IOPRIO_CLASS_SHIFT) == -1) {
            perror("ioprio_set");
            return 1;
        }

        if (argv[optind])
            execvp(argv[optind], &argv[optind]);
    }

    return 0;
}
March 11 2005, Jens Axboe <jens.axboe@oracle.com>
```
