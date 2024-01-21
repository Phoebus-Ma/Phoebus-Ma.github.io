
# Implementing I2C device drivers

# 1. Introduction

这是一个小指南，适合那些想要使用 Linux 作为协议主机/主机（而不是从机）为 I2C 或 SMBus 设备编写内核驱动程序的人。

要设置驱动程序，您需要做几件事。 有些是可选的，有些事情可以稍微或完全不同。 将此作为指南，而不是规则手册！


# 2. General remarks

尝试保持内核命名空间尽可能干净。 最好的方法是为所有全局符号使用唯一的前缀。 这对于导出的符号尤其重要，但对于非导出的符号也这样做是个好主意。 我们将在本教程中使用前缀 foo_。


# 3. The driver structure

通常，您将实现一个驱动程序结构，并从中实例化所有客户端。 请记住，驱动程序结构包含通用访问例程，并且除了包含您提供的数据的字段之外，应该进行零初始化。 客户端结构保存特定于设备的信息，例如驱动程序模型设备节点及其 I2C 地址。

i2c包含的属性和方法可以参考：`linux/include/linux/i2c.h`

i2c 设备驱动程序对象包含如下内容：

```c
struct i2c_driver {
    unsigned int class;
    int (*probe)(struct i2c_client *client, const struct i2c_device_id *id);
    void (*remove)(struct i2c_client *client);
    int (*probe_new)(struct i2c_client *client);
    void (*shutdown)(struct i2c_client *client);
    void (*alert)(struct i2c_client *client, enum i2c_alert_protocol protocol, unsigned int data);
    int (*command)(struct i2c_client *client, unsigned int cmd, void *arg);
    struct device_driver driver;
    const struct i2c_device_id *id_table;
    int (*detect)(struct i2c_client *client, struct i2c_board_info *info);
    const unsigned short *address_list;
    struct list_head clients;
    u32 flags;
};
```

```c
static struct i2c_device_id foo_idtable[] = {
      { "foo", my_id_for_foo },
      { "bar", my_id_for_bar },
      { }
};

MODULE_DEVICE_TABLE(i2c, foo_idtable);

static struct i2c_driver foo_driver = {
      .driver = {
              .name   = "foo",
              .pm     = &foo_pm_ops,  /* optional */
      },

      .id_table       = foo_idtable,
      .probe          = foo_probe,
      .remove         = foo_remove,
      /* if device autodetection is needed: */
      .class          = I2C_CLASS_SOMETHING,
      .detect         = foo_detect,
      .address_list   = normal_i2c,

      .shutdown       = foo_shutdown, /* optional */
      .command        = foo_command,  /* optional, deprecated */
}
```

名称字段是驱动程序名称，并且不能包含空格。 它应该与模块名称匹配（如果驱动程序可以编译为模块），尽管您可以使用 MODULE_ALIAS（在此示例中传递“foo”）为模块添加另一个名称。 如果驱动程序名称与模块名称不匹配，则不会自动加载模块（热插拔/冷插拔）。

所有其他字段用于回调函数，这将在下面解释。


# 4. Extra client data

每个客户端结构都有一个特殊的数据字段，可以指向任何结构。 您应该使用它来保存设备特定的数据。

```c
/* store the value */
void i2c_set_clientdata(struct i2c_client *client, void *data);

/* retrieve the value */
void *i2c_get_clientdata(const struct i2c_client *client);
```

请注意，从内核 2.6.34 开始，您不必在remove() 中或probe() 失败时将数据字段设置为NULL。 i2c 核心在这些情况下会自动执行此操作。 这也是核心唯一一次触及这个领域。


# 5. Accessing the client

假设我们有一个有效的客户端结构。 有时，我们需要从客户那里收集信息，或者向客户写入新的信息。

我发现为此定义 foo_read 和 foo_write 函数很有用。 对于某些情况，直接调用 I2C 函数会更容易，但许多芯片都有某种可以轻松封装的寄存器值思想。

以下函数是简单的示例，不应照字面复制：

```c
int foo_read_value(struct i2c_client *client, u8 reg)
{
      if (reg < 0x10) /* byte-sized register */
              return i2c_smbus_read_byte_data(client, reg);
      else            /* word-sized register */
              return i2c_smbus_read_word_data(client, reg);
}

int foo_write_value(struct i2c_client *client, u8 reg, u16 value)
{
      if (reg == 0x10)        /* Impossible to write - driver error! */
              return -EINVAL;
      else if (reg < 0x10)    /* byte-sized register */
              return i2c_smbus_write_byte_data(client, reg, value);
      else                    /* word-sized register */
              return i2c_smbus_write_word_data(client, reg, value);
}
```


# 6. Probing and attaching

Linux I2C 堆栈最初是为了支持访问 PC 主板上的硬件监控芯片而编写的，因此用于嵌入一些更适合 SMBus（和 PC）而不是 I2C 的假设。 这些假设之一是大多数适配器和设备驱动程序支持 SMBUS_QUICK 协议来探测设备是否存在。 另一个原因是，仅使用此类探测原语就可以充分配置设备及其驱动程序。

随着 Linux 及其 I2C 堆栈在嵌入式系统和 DVB 适配器等复杂组件中的使用越来越广泛，这些假设变得更加成问题。 发出中断的 I2C 设备驱动程序需要更多（且不同）的配置信息，处理无法通过协议探测区分的芯片变体的驱动程序也需要更多（且不同）的配置信息，或者需要一些特定于板的信息才能正确运行的驱动程序。


# 7. Device/Driver Binding

系统基础设施（通常是特定于板的初始化代码或启动固件）报告存在哪些 I2C 设备。 例如，内核中或引导加载程序中可能有一个表，用于识别 I2C 设备并将它们链接到有关 IRQ 和其他接线工件、芯片类型等的板特定配置信息。 这可用于为每个 I2C 设备创建 i2c_client 对象。

使用此绑定模型的 I2C 设备驱动程序的工作方式与 Linux 中任何其他类型的驱动程序一样：它们提供了一个probe()方法来绑定到这些设备，并提供了一个remove()方法来取消绑定。

```c
static int foo_probe(struct i2c_client *client);
static void foo_remove(struct i2c_client *client);
```

请记住，i2c_driver 不会创建这些客户端句柄。 该句柄可以在 foo_probe() 期间使用。 如果 foo_probe() 报告成功（零而不是负状态代码），它可能会保存句柄并使用它，直到 foo_remove() 返回。 大多数 Linux 驱动程序都使用该绑定模型。

当 id_table 名称字段中的条目与设备名称匹配时，将调用探测函数。 如果探测函数需要该条目，它可以使用以下命令检索它：

```c
const struct i2c_device_id *id = i2c_match_id(foo_idtable, client);
```


# 8. Device Creation

如果您知道 I2C 设备已连接到给定的 I2C 总线，则只需使用设备地址和驱动程序名称填充 i2c_board_info 结构并调用 i2c_new_client_device() 即可实例化该设备。 这将创建设备，然后驱动程序核心将负责查找正确的驱动程序并调用其probe()方法。 如果驱动程序支持不同的设备类型，您可以使用 type 字段指定所需的类型。 如果需要，您还可以指定 IRQ 和平台数据。

有时您知道某个设备已连接到给定的 I2C 总线，但您不知道它使用的确切地址。 例如，这种情况发生在电视适配器上，其中相同的驱动程序支持数十种略有不同的型号，并且 I2C 设备地址从一个型号更改为下一个型号。 在这种情况下，您可以使用 i2c_new_scanned_device() 变体，它与 i2c_new_client_device() 类似，只不过它需要额外的可能 I2C 地址列表来探测。 为列表中的第一个响应地址创建设备。 如果您希望地址范围内出现多个设备，只需多次调用 i2c_new_scanned_device() 即可。

对 i2c_new_client_device() 或 i2c_new_scanned_device() 的调用通常发生在 I2C 总线驱动程序中。 您可能想要保存返回的 i2c_client 引用以供以后使用。


# 9. Device Detection

有时您事先并不知道哪些 I2C 设备连接到给定的 I2C 总线。 例如，PC 的 SMBus 上的硬件监控设备就是这种情况。 在这种情况下，您可能希望让驱动程序自动检测支持的设备。 这就是旧模型的工作方式，现在可以作为标准驱动程序模型的扩展。

您只需定义一个检测回调，它将尝试识别支持的设备（对于支持的设备返回 0，对于不支持的设备返回 -ENODEV）、要探测的地址列表以及设备类型（或类），以便只有 I2C 总线 可能已连接该类型的设备（并且未以其他方式枚举）将被探测。 例如，需要自动检测的硬件监控芯片的驱动程序会将其类别设置为 I2C_CLASS_HWMON，并且该驱动程序只会探测类别包括 I2C_CLASS_HWMON 的 I2C 适配器。 请注意，缺少匹配类并不妨碍在给定 I2C 适配器上使用该类型的设备。 它所阻止的只是自动检测； 设备的显式实例化仍然是可能的。

请注意，此机制纯粹是可选的，并不适合所有设备。 您需要一些可靠的方法来识别支持的设备（通常使用特定于设备的专用识别寄存器），否则可能会发生误检测，并且很快就会出错。 请记住，I2C 协议不包含任何用于检测给定地址处芯片是否存在的标准方法，更不用说识别设备的标准方法了。 更糟糕的是缺乏与总线传输相关的语义，这意味着同一传输可以被视为一个芯片的读操作和另一个芯片的写操作。 由于这些原因，在可能的情况下，显式设备实例化应始终优先于自动检测。


# 10. Device Deletion

使用 i2c_new_client_device() 或 i2c_new_scanned_device() 创建的每个 I2C 设备都可以通过调用 i2c_unregister_device() 取消注册。 如果您没有显式调用它，它将在底层 I2C 总线本身被删除之前自动调用，因为设备无法在设备驱动程序模型中保留其父设备。


# 11. Initializing the driver

当内核启动时，或者当你的 foo 驱动模块被插入时，你必须做一些初始化。 幸运的是，通常只需注册驱动程序模块就足够了。

```c
static int __init foo_init(void)
{
      return i2c_add_driver(&foo_driver);
}
module_init(foo_init);

static void __exit foo_cleanup(void)
{
      i2c_del_driver(&foo_driver);
}
module_exit(foo_cleanup);

The module_i2c_driver() macro can be used to reduce above code.

module_i2c_driver(foo_driver);
```

请注意，某些函数由 __init 标记。 这些功能可以在内核启动（或模块加载）完成后删除。 同样，当代码构建到内核中时，由 __exit 标记的函数将被编译器删除，因为它们永远不会被调用。


# 12. Driver Information

```c
/* Substitute your own name and email address */
MODULE_AUTHOR("Frodo Looijaard <frodol@dds.nl>"
MODULE_DESCRIPTION("Driver for Barf Inc. Foo I2C devices");

/* a few non-GPL license types are also allowed */
MODULE_LICENSE("GPL");
```


# 13. Power Management

如果您的 I2C 设备在进入系统低功耗状态时需要特殊处理 - 例如将收发器置于低功耗模式，或激活系统唤醒机制 - 通过为驱动程序的 dev_pm_ops 实现适当的回调（例如挂起 并恢复）。

这些是标准驱动程序模型调用，它们的工作方式就像任何其他驱动程序堆栈一样。 这些调用可以休眠，并且可以使用 I2C 消息传递给正在挂起或恢复的设备（因为在发出这些调用时，它们的父 I2C 适配器处于活动状态，并且 IRQ 仍处于启用状态）。


# 14. System Shutdown

如果您的 I2C 设备在系统关闭或重新启动（包括 kexec）时需要特殊处理（例如关闭某些东西），请使用 shutdown() 方法。

同样，这是一个标准驱动程序模型调用，其工作方式与任何其他驱动程序堆栈一样：调用可以休眠，并且可以使用 I2C 消息传递。


# 15. Command function

支持通用的类似 ioctl 的函数回调。 您很少需要它，并且无论如何它的使用都已被弃用，因此较新的设计不应使用它。


# 16. Sending and receiving

如果您想与您的设备进行通信，可以使用多种函数来实现此目的。 您可以在 <linux/i2c.h> 中找到所有这些内容。

如果您可以在普通 I2C 通信和 SMBus 级通信之间进行选择，请使用后者。 所有适配器都理解 SMBus 级命令，但只有其中一些适配器理解普通 I2C！


# 17. Plain I2C communication

```c
int i2c_master_send(struct i2c_client *client, const char *buf,
                    int count);
int i2c_master_recv(struct i2c_client *client, char *buf, int count);
```

这些例程从客户端读取一些字节或向客户端写入一些字节。 客户端包含 I2C 地址，因此您不必包含它。 第二个参数包含要读/写的字节，第三个参数是要读/写的字节数（必须小于缓冲区的长度，也应该小于 64k，因为 msg.len 是 u16。）返回的是实际的 读/写的字节数。

```c
int i2c_transfer(struct i2c_adapter *adap, struct i2c_msg *msg,
                 int num);
```

这会发送一系列消息。 每条消息都可以读取或写入，并且可以以任何方式混合。 交易被合并：交易之间不发出停止条件。 i2c_msg 结构包含每条消息的客户端地址、消息的字节数以及消息数据本身。

您可以阅读文件[The I2C Protocol](https://www.kernel.org/doc/html/latest/i2c/i2c-protocol.html)以获取有关实际I2C协议的更多信息。


# 18. SMBus communication

```c
s32 i2c_smbus_xfer(struct i2c_adapter *adapter, u16 addr,
                   unsigned short flags, char read_write, u8 command,
                   int size, union i2c_smbus_data *data);
```

这是通用 SMBus 功能。 下面的所有功能都是根据它来实现的。 切勿直接使用此功能！

```c
s32 i2c_smbus_read_byte(struct i2c_client *client);
s32 i2c_smbus_write_byte(struct i2c_client *client, u8 value);
s32 i2c_smbus_read_byte_data(struct i2c_client *client, u8 command);
s32 i2c_smbus_write_byte_data(struct i2c_client *client,
                              u8 command, u8 value);
s32 i2c_smbus_read_word_data(struct i2c_client *client, u8 command);
s32 i2c_smbus_write_word_data(struct i2c_client *client,
                              u8 command, u16 value);
s32 i2c_smbus_read_block_data(struct i2c_client *client,
                              u8 command, u8 *values);
s32 i2c_smbus_write_block_data(struct i2c_client *client,
                               u8 command, u8 length, const u8 *values);
s32 i2c_smbus_read_i2c_block_data(struct i2c_client *client,
                                  u8 command, u8 length, u8 *values);
s32 i2c_smbus_write_i2c_block_data(struct i2c_client *client,
                                   u8 command, u8 length,
                                   const u8 *values);
```

这些被从 i2c-core 中删除，因为它们没有用户，但如果需要的话可以稍后添加回来：

```c
s32 i2c_smbus_write_quick(struct i2c_client *client, u8 value);
s32 i2c_smbus_process_call(struct i2c_client *client,
                           u8 command, u16 value);
s32 i2c_smbus_block_process_call(struct i2c_client *client,
                                 u8 command, u8 length, u8 *values);
```

所有这些事务在失败时都会返回负 errno 值。 “写入”交易成功时返回 0； “读取”事务返回读取的值，块事务除外，块事务返回读取的值的数量。 块缓冲区不需要长于 32 字节。

您可以阅读文件[SMBus协议](https://www.kernel.org/doc/html/latest/i2c/smbus-protocol.html)以获取有关实际SMBus协议的更多信息。


# 19. General purpose routines

下面列出了之前未提及的所有通用例程：

```c
/* Return the adapter number for a specific adapter */
int i2c_adapter_id(struct i2c_adapter *adap);
```
