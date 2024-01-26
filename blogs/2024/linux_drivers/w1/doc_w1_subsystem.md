
# Introduction to the 1-wire (w1) subsystem

# 1. Introduction

1 线总线是一种简单的主从总线，通过单根信号线（加上地线，所以有两根线）进行通信。

设备通过开漏输出将信号拉至地并采样信号线的逻辑电平来在总线上进行通信。

w1 子系统提供了管理 w1 主站以及与从站通信的框架。

所有 w1 从设备必须连接到 w1 总线主设备。

w1 主设备示例：

- DS9490 usb 设备
- W1-over-GPIO
- DS2482 (i2c 转 w1 桥接器)
- 仿真设备，例如RS232转换器、并行端口适配器等


# 2. What does the w1 subsystem do?

当 w1 主驱动程序向 w1 子系统注册时，会发生以下情况：

- 为该 w1 master 创建 sysfs 条目
- w1 总线定期搜索新的从设备

当在总线上找到设备时，w1 内核会尝试加载其系列的驱动程序并检查是否已加载。 如果是这样，家庭司机就依附于奴隶。 如果家庭没有驱动程序，则会分配默认驱动程序，这允许执行几乎任何类型的操作。 每个逻辑操作本质上都是一个事务，它可以包含多个（两个或一个）低级操作。 让我们看看如何读取 EEPROM 上下文： 1. 必须写入控制缓冲区，即包含命令字节和两个字节地址的缓冲区。 在此步骤中，总线将复位，并使用 W1_SKIP_ROM 或 W1_MATCH_ROM 命令选择适当的设备。 然后将提供的控制缓冲区写入线路。 2.阅读。 这将发出读取 eeprom 响应。

有可能在 1. 和 2. w1 之间，主线程将重置总线以进行搜索，甚至从设备将被删除，但在这种情况下，将读取 0xff，因为没有选择设备。


# 3. W1 device families

从设备由为 w1 设备系列编写的驱动程序处理。

系列驱动程序填充 struct w1_family_ops（请参阅 w1_family.h）并向 w1 子系统注册。

当前 family 驱动:

**w1_therm**

- (ds18?20 温度传感器系列驱动) 提供温度读取功能，该功能与上述w1_family_ops结构体的->rbin()方法绑定。

**w1_smem**

- 驱动程序为简单的64位存储单元提供了ID读取方法。

您可以通过读取适当的 sysfs 文件来调用上述方法。


# 4. What does a w1 master driver need to implement?

w1 总线主控的驱动程序必须至少提供两个功能。

仿真设备必须能够设置输出信号电平 (write_bit) 和采样信号电平 (read_bit)。

本身支持 1-wire 的设备必须提供写入和采样位 (touch_bit) 以及复位总线 (reset_bus) 的能力。

大多数硬件提供更高级别的功能来减轻 w1 处理的负担。 有关详细信息，请参阅 w1.h 中的 `struct w1_bus_master` 定义。


# 5. w1 master sysfs interface

|                       |                                   |
| --------------------- | --------------------------------- |
| <xx-xxxxxxxxxxxx>     | 找到的设备的目录。形式是家庭系列     |
| bus                   | (standard) 到 w1 总线的符号链接    |
| driver                | (standard) 指向w1驱动程序的符号链接 |
| w1_master_add         | (rw) 手动注册从机设备              |
| w1_master_attempts    | (ro) 尝试搜索的次数                |
| w1_master_max_slave_count | (rw) 一次搜索的最大从机数量     |
| w1_master_name        | (ro) 设备名称 (w1_bus_masterX)    |
| w1_master_pullup      | (rw) 5V 强上拉 0 启用，1 禁用      |
| w1_master_remove      | (rw) 手动删除从设备                |
| w1_master_search      | (rw) 剩余搜索次数，-1=连续（默认）  |
| w1_master_slave_count | (ro) 发现的从机数量                |
| w1_master_slaves      | (ro) 从机的名字，每行一个           |
| w1_master_timeout     | (ro) 搜索之间的延迟（以秒为单位）   |
| w1_master_timeout_us  | (ro) 搜索之间的延迟（以微秒为单位） |

如果您有一个永远不会改变的 w1 总线（您不添加或删除设备），则可以将模块参数 search_count 设置为一个较小的正数，以进行最初少量的总线搜索。 或者可以将其设置为零，然后通过 w1_master_add 设备文件手动添加从设备序列号。 w1_master_add 和 w1_master_remove 文件通常仅在禁用搜索时才有意义，因为搜索将重新检测存在的手动删除的设备，并使总线上不存在的手动添加的设备超时。

只要 w1_master_search 保持大于 0 或为 -1，总线搜索就会按一定时间间隔发生，指定为 timeout 和 timeout_us 模块参数（其中任何一个都可以为 0）的总和。 每次搜索尝试都会将 w1_master_search 递减 1（降至 0），并将 w1_master_attempts 递增 1。


# 6. w1 slave sysfs interface

|          |                                                             |
| -------- | ----------------------------------------------------------- |
| bus      | (standard) 到 w1 总线的符号链接                               |
| driver   | (standard) w1 驱动程序的符号链接                              |
| name     | 设备名称，通常与目录名称相同                                   |
| w1_slave | （可选）一个二进制文件，其含义取决于系列驱动程序                 |
| rw       | （可选）为没有适当系列驱动程序的从设备创建。 允许读/写二进制数据。|
