
# Userspace block device driver (ublk driver)

# 1. Overview

ublk 是一个用于从用户空间实现块设备逻辑的通用框架。 其背后的动机是将虚拟块驱动程序移动到用户空间，例如循环、nbd 和类似的驱动程序可能非常有帮助。 它可以帮助实现新的虚拟块设备，例如ublk-qcow2（在内核中实现qcow2驱动程序有多种尝试）。

用户空间块设备之所以有吸引力，是因为：

- 它们可以用多种编程语言编写。
- 他们可以使用内核中不可用的库。
- 可以使用应用程序开发人员熟悉的工具对其进行调试。
- 崩溃不会使机器发生内核恐慌。
- 错误对安全的影响可能比内核代码中的错误要小。
- 它们可以独立于内核进行安装和更新。
- 它们可用于通过用户指定的参数/设置轻松模拟块设备以进行测试/调试

ublk 块设备 (/dev/ublkb*) 由 ublk 驱动程序添加。 设备上的任何 IO 请求都会转发到 ublk 用户空间程序。 为了方便起见，在本文档中，ublk 服务器指的是通用 ublk 用户空间程序。 ublksrv 1 就是此类实现之一。 它提供了libublksrv 2库，可以方便地开发特定的用户块设备，同时也包含了通用类型的块设备，例如loop和null。 理查德·W.M. Jones 基于 libublksrv 2 编写了用户空间 nbd 设备 nbdublk 3。

用户空间处理完IO后，将结果提交回驱动程序，从而完成请求周期。 这样，任何特定的IO处理逻辑都完全由用户空间完成，例如loop的IO处理，NBD的IO通信，或者qcow2的IO映射。

/dev/ublkb* 由 blk-mq 基于请求的驱动程序驱动。 每个请求都分配有一个队列范围的唯一标记。 ublk服务器也为每个IO分配唯一的标签，与/dev/ublkb*的IO 1:1映射。

IO请求转发和IO处理结果提交都是通过io_uring passthrough命令完成的； 这就是为什么 ublk 也是一种基于 io_uring 的块驱动程序。 据观察，使用 io_uring passthrough 命令可以提供比块 IO 更好的 IOPS； 这就是为什么 ublk 是用户空间块设备的高性能实现之一：不仅 IO 请求通信是通过 io_uring 完成的，而且 ublk 服务器中首选的 IO 处理也是基于 io_uring 的方法。

ublk 提供控制接口来设置/获取 ublk 块设备参数。 该接口是可扩展的并且兼容kabi：基本上任何ublk请求队列的参数或ublk通用功能参数都可以通过该接口设置/获取。 因此，ublk 是通用的用户空间块设备框架。 例如，可以很容易地从用户空间设置具有指定块参数的 ublk 设备。


# 2. Using ublk

ublk 需要用户空间 ublk 服务器来处理真正的块设备逻辑。

下面是使用 ublksrv 提供基于 ublk 的循环设备的示例。

添加设备：

```bash
ublk add -t loop -f ublk-loop.img
```

使用 xfs 格式化，然后使用它：

```bash
mkfs.xfs /dev/ublkb0
mount /dev/ublkb0 /mnt
# do anything. all IOs are handled by io_uring
...
umount /mnt
```

列出设备及其信息：

```bash
ublk list
```

删除设备：

```bash
ublk del -a
ublk del -n $ublk_dev_id
```

请参阅 ublksrv 4 的 README 中的使用详细信息。


# 3. Design

**Control plane**

ublk 驱动程序提供全局杂项设备节点 (/dev/ublk-control)，用于借助多个控制命令来管理和控制 ublk 设备：

- UBLK_CMD_ADD_DEV

添加一个 ublk 字符设备（/dev/ublkc*），与 ublk 服务器 WRT IO 命令通信。 基本设备信息与此命令一起发送。 它设置ublksrv_ctrl_dev_info的UAPI结构，例如nr_hw_queues、queue_depth和最大IO请求缓冲区大小，这些信息与驱动程序协商并发送回服务器。 当该命令完成后，基本设备信息是不可变的。

- UBLK_CMD_SET_PARAMS / UBLK_CMD_GET_PARAMS

设置或获取设备的参数，这些参数可以是通用功能相关的，也可以是请求队列限制相关的，但不能是特定于 IO 逻辑的，因为驱动程序不处理任何 IO 逻辑。 该命令必须在发送 UBLK_CMD_START_DEV 之前发送。

- UBLK_CMD_START_DEV

服务器准备好用户空间资源（例如创建每个队列的 pthread 和 io_uring 来处理 ublk IO）后，此命令将发送到驱动程序以分配和公开 /dev/ublkb*。 通过 UBLK_CMD_SET_PARAMS 设置的参数应用于创建设备。

- UBLK_CMD_STOP_DEV

停止 /dev/ublkb* 上的 IO 并删除设备。 当此命令返回时，ublk 服务器将释放资源（例如销毁每个队列的 pthread 和 io_uring）。

- UBLK_CMD_DEL_DEV

删除 /dev/ublkc*。 当该命令返回时，分配的ublk设备号可以重新使用。

- UBLK_CMD_GET_QUEUE_AFFINITY

添加 /dev/ublkc 时，驱动程序会创建块层标记集，以便每个队列的关联信息可用。 服务器发送 UBLK_CMD_GET_QUEUE_AFFINITY 来检索队列亲和性信息。 它可以有效地设置每队列上下文，例如将仿射 CPU 与 IO pthread 绑定，并尝试在 IO 线程上下文中分配缓冲区。

- UBLK_CMD_GET_DEV_INFO

用于通过 ublksrv_ctrl_dev_info 检索设备信息。 服务器负责在用户空间中保存 IO 目标特定信息。

- UBLK_CMD_GET_DEV_INFO2 与 UBLK_CMD_GET_DEV_INFO 相同，但 ublk 服务器必须提供 /dev/ublkc* 字符设备的路径供内核运行权限检查，添加此命令是为了支持非特权 ublk 设备，与 UBLK_F_UNPRIVILEGED_DEV 一起引入。 只有拥有所请求设备的用户才能检索设备信息。

    如何处理用户空间/内核兼容性：

    1. 内核是否能够处理 UBLK_F_UNPRIVILEGED_DEV

    如果 ublk 服务器支持 UBLK_F_UNPRIVILEGED_DEV：

    ublk 服务器应发送 UBLK_CMD_GET_DEV_INFO2，只要非特权应用程序需要查询当前用户拥有的设备，当应用程序不知道是否设置了 UBLK_F_UNPRIVILEGED_DEV 时，由于功能信息是无状态的，并且应用程序应始终通过 UBLK_CMD_GET_DEV_INFO2 检索它

    如果 ublk 服务器不支持 UBLK_F_UNPRIVILEGED_DEV：

    UBLK_CMD_GET_DEV_INFO 始终发送到内核，并且 UBLK_F_UNPRIVILEGED_DEV 功能对用户不可用

    2. 如果内核无法处理 UBLK_F_UNPRIVILEGED_DEV

    如果 ublk 服务器支持 UBLK_F_UNPRIVILEGED_DEV：

    首先尝试 UBLK_CMD_GET_DEV_INFO2，会失败，然后由于无法设置 UBLK_F_UNPRIVILEGED_DEV，需要重试 UBLK_CMD_GET_DEV_INFO

    如果 ublk 服务器不支持 UBLK_F_UNPRIVILEGED_DEV：

    UBLK_CMD_GET_DEV_INFO 始终发送到内核，并且 UBLK_F_UNPRIVILEGED_DEV 功能对用户不可用。

- UBLK_CMD_START_USER_RECOVERY

如果启用了 UBLK_F_USER_RECOVERY 功能，则此命令有效。 在旧进程退出、 ublk 设备停顿并释放 /dev/ublkc* 后接受此命令。 用户应该在启动重新打开 /dev/ublkc* 的新进程之前发送此命令。 当此命令返回时，ublk 设备已准备好进行新进程。

- UBLK_CMD_END_USER_RECOVERY

如果启用了 UBLK_F_USER_RECOVERY 功能，则此命令有效。 在 ublk 设备停顿并且新进程打开 /dev/ublkc* 并让所有 ublk 队列准备就绪后，接受此命令。 当此命令返回时，ublk 设备将停止静默，新的 I/O 请求将传递到新进程。

- 用户恢复功能说明

    添加了两个用于用户恢复的新功能：UBLK_F_USER_RECOVERY 和 UBLK_F_USER_RECOVERY_REISSUE。

    设置 UBLK_F_USER_RECOVERY 后，在一个 ubq_daemon（ublk 服务器的 io 处理程序）死亡后，ublk 在整个恢复阶段不会删除 /dev/ublkb* 并且保留 ublk 设备 ID。 ublk 服务器负责根据自己的知识恢复设备上下文。 尚未发送到用户空间的请求将被重新排队。 已发出到用户空间的请求将被中止。

    设置 UBLK_F_USER_RECOVERY_REISSUE 后，在一个 ubq_daemon（ublk 服务器的 io 处理程序）死亡后，与 UBLK_F_USER_RECOVERY 相反，已向用户空间发出的请求将重新排队，并在处理 UBLK_CMD_END_USER_RECOVERY 后重新向新进程发出。 UBLK_F_USER_RECOVERY_REISSUE 是为容忍双写的后端设计的，因为驱动程序可能会两次发出相同的 I/O 请求。 它对于只读 FS 或 VM 后端可能很有用。

通过传递 UBLK_F_UNPRIVILEGED_DEV 支持非特权 ublk 设备。 一旦设置了该标志，非特权用户就可以发送所有控制命令。 除了UBLK_CMD_ADD_DEV命令外，ublk驱动程序对所有其他控制命令都会对指定的字符设备（/dev/ublkc*）进行权限检查，为此，必须在这些命令的有效负载中提供字符设备的路径 ublk服务器。 这样，ublk设备就成为容器件，在一个容器中创建的设备可以在该容器内进行控制/访问。


# 4. Data plane

ublk 服务器需要创建每个队列 IO pthread 和 io_uring，以便通过 io_uring 直通处理 IO 命令。 每队列 IO pthread 专注于 IO 处理，不应该处理任何控制和管理任务。

其IO由唯一的标签分配，与/dev/ublkb*的IO请求1:1映射。

ublksrv_io_desc 的 UAPI 结构被定义用于描述来自驱动程序的每个 IO。 /dev/ublkc* 上提供了一个固定的映射区域（数组），用于将 IO 信息导出到服务器； 例如 IO 偏移量、长度、OP/标志和缓冲区地址。 每个 ublksrv_io_desc 实例都可以直接通过队列 id 和 IO 标记进行索引。

以下IO命令通过io_uring passthrough命令进行通信，每个命令仅用于转发IO并提交命令数据中指定IO标记的结果：

- UBLK_IO_FETCH_REQ

从服务器 IO pthread 发送，用于获取未来发往 /dev/ublkb* 的传入 IO 请求。 该命令仅从服务器 IO pthread 发送一次，供 ublk 驱动程序设置 IO 转发环境。

- UBLK_IO_COMMIT_AND_FETCH_REQ

当IO请求的目的地是/dev/ublkb*时，驱动程序将IO的ublksrv_io_desc存储到指定的映射区域； 那么该IO标签之前接收到的IO命令（UBLK_IO_FETCH_REQ或UBLK_IO_COMMIT_AND_FETCH_REQ）已完成，因此服务器通过io_uring获取IO通知。

服务器处理 IO 后，通过发送 UBLK_IO_COMMIT_AND_FETCH_REQ 将其结果提交回驱动程序。 一旦 ublkdrv 收到此命令，它就会解析结果并完成对 /dev/ublkb* 的请求。 同时设置环境以使用相同的 IO 标签获取未来的请求。 也就是说，UBLK_IO_COMMIT_AND_FETCH_REQ 被重用于获取请求和提交回 IO 结果。

- UBLK_IO_NEED_GET_DATA

当UBLK_F_NEED_GET_DATA启用时，WRITE请求将首先向ublk服务器发出，而不进行数据复制。 然后，ublk服务器的IO后端接收到请求，它可以分配数据缓冲区并将其地址嵌入到这个新的io命令中。 内核驱动程序收到命令后，将数据从请求页面复制到该后端的缓冲区。 最后，后端再次接收到需要写入数据的请求，才能真正处理该请求。

UBLK_IO_NEED_GET_DATA 添加了一个额外的往返和一个 io_uring_enter() 系统调用。 任何认为可能会降低性能的用户都不应该启用 UBLK_F_NEED_GET_DATA。 ublk服务器默认为每个IO预先分配IO缓冲区。 任何新项目都应该尝试使用此缓冲区与 ublk 驱动程序进行通信。 然而，现有的项目可能会崩溃或无法使用新的缓冲区接口； 这就是为什么添加此命令是为了向后兼容，以便现有项目仍然可以使用现有缓冲区。

ublk服务器IO缓冲区和ublk块IO请求之间的数据复制

驱动程序需要先将块IO请求页面复制到服务器缓冲区（页面）中进行WRITE，然后再通知服务器即将到来的IO，以便服务器可以处理WRITE请求。

当服务器处理READ请求并向服务器发送UBLK_IO_COMMIT_AND_FETCH_REQ时，ublkdrv需要将读取的服务器缓冲区（页面）复制到IO请求页面。


# 5. Future development

**Zero copy**

零拷贝是 nbd、fuse 或类似驱动程序的通用要求。 小光6提到的一个问题是，映射到用户空间的页面无法在内核中用现有的mm接口重新映射。 当将直接 IO 指定为 /dev/ublkb* 时，可能会发生这种情况。 此外，他还报告说，大请求（IO 大小 >= 256 KB）可能会从零复制中受益匪浅。

**References**

[1]

<https://github.com/ming1/ubdsrv>

[2(1,2)]

<https://github.com/ming1/ubdsrv/tree/master/lib>

[3]

<https://gitlab.com/rwmjones/libnbd/-/tree/nbdublk>

[4]

<https://github.com/ming1/ubdsrv/blob/master/README>

[5]

<https://lore.kernel.org/linux-block/YoOr6jBfgVm8GvWg@stefanha-x1.localdomain/>

[6]

<https://lore.kernel.org/linux-block/YoOr6jBfgVm8GvWg@stefanha-x1.localdomain/>
