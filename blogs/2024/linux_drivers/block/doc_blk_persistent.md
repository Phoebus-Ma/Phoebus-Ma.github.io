
# Block layer support for Persistent Reservations

Linux 内核支持用于简化持久保留的用户空间接口，该接口映射到支持这些的块设备（如 SCSI）。 持久保留允许将对块设备的访问限制为共享存储设置中的特定启动器。

本文档概述了支持 ioctl 命令。 有关更详细的参考信息，请参阅 SCSI 主命令标准，特别是有关保留以及“PERSISTENT RESERVE IN”和“PERSISTENT RESERVE OUT”命令的部分。

所有实现都应确保预留在断电时能够幸存，并覆盖多路径环境中的所有连接。 这些行为在 SPC 中是可选的，但将由 Linux 自动应用。


# 1. The following types of reservations are supported

- PR_WRITE_EXCLUSIVE

&emsp;只有拥有预留的启动器才能写入设备。 任何发起者都可以从设备读取。

- PR_EXCLUSIVE_ACCESS

&emsp;只有拥有预留的发起者才能访问该设备。

- PR_WRITE_EXCLUSIVE_REG_ONLY

&emsp;只有具有注册密钥的启动器才能写入设备，任何启动器都可以从设备读取。

- PR_EXCLUSIVE_ACCESS_REG_ONLY

&emsp;只有拥有注册密钥的启动器才能访问设备。

- PR_WRITE_EXCLUSIVE_ALL_REGS

&emsp;只有具有注册密钥的启动器才能写入设备，任何启动器都可以从设备读取。 所有具有注册密钥的发起者都被视为保留持有者。 如果您想使用此类型，请参阅 SPC 规范以了解保留持有者的含义。

- PR_EXCLUSIVE_ACCESS_ALL_REGS

&emsp;只有拥有注册密钥的启动器才能访问设备。 所有具有注册密钥的发起者都被视为保留持有者。 如果您想使用此类型，请参阅 SPC 规范以了解保留持有者的含义。


# 2. The following ioctl are supported

## 2.1 IOC_PR_REGISTER

如果 new_key 参数非空，则此 ioctl 命令会注册新的保留。 如果不存在现有预留，则 old_key 必须为零，如果应替换现有预留，则 old_key 必须包含旧预留密钥。

如果 new_key 参数为 0，则取消注册在 old_key 中传递的现有保留。


## 2.2 IOC_PR_RESERVE

此 ioctl 命令保留设备，从而根据类型参数限制其他设备的访问。 key 参数必须是通过 IOC_PR_REGISTER、IOC_PR_REGISTER_IGNORE、IOC_PR_PREEMPT 或 IOC_PR_PREEMPT_ABORT 命令获取的设备的现有保留密钥。


## 2.3 IOC_PR_RELEASE

此 ioctl 命令释放由 key 和 flags 指定的保留，从而删除它暗示的任何访问限制。


## 2.4 IOC_PR_PREEMPT

此 ioctl 命令释放 old_key 引用的现有预留，并将其替换为预留密钥 new_key 类型的新预留。


## 2.5 IOC_PR_PREEMPT_ABORT

此 ioctl 命令的工作方式类似于 IOC_PR_PREEMPT，只不过它还会中止通过 old_key 标识的连接发送的任何未完成的命令。


## 2.6 IOC_PR_CLEAR

此 ioctl 命令取消注册密钥和向设备注册的任何其他保留密钥，并删除任何现有保留。


# 3. Flags

所有 ioctl 都有一个标志字段。 目前仅支持一个标志：

- PR_FL_IGNORE_KEY

&emsp;忽略现有的预订密钥。 IOC_PR_REGISTER 通常支持这一点，并且某些实现可能支持 IOC_PR_RESERVE 标志。

对于所有未知标志，内核将返回 -EOPNOTSUPP。
