
# I2C Address Translators

- 作者: Luca Ceresoli <luca@lucaceresoli.net>
- 作者: Tomi Valkeinen <tomi.valkeinen@ideasonboard.com>


# 1. Description

I2C 地址转换器 (ATR) 是一种具有 I2C 从属父（“上游”）端口和 N 个 I2C 主子（“下游”）端口的设备，并使用修改后的从属地址将事务从上游转发到适当的下游端口。 父总线上使用的地址称为“别名”，并且（可能）与子总线的物理从地址不同。 地址转换由硬件完成。

ATR 看起来与 i2c-mux 类似，除了：

- 父总线和子总线上的地址可以不同
- 一般情况下不需要选择子端口； 父总线上使用的别名意味着它

ATR 功能可以由具有许多其他功能的芯片提供。 内核 i2c-atr 提供了在驱动程序中实现 ATR 的帮助程序。

ATR 在每个子总线上创建一个新的 I2C“子”适配器。 在子总线上添加设备最终会调用驱动程序代码来选择可用的别名。 维护适当的可用别名池并为每个新设备选择一个别名取决于驱动程序实现者。 ATR 维护当前分配的别名表，并使用它来修改定向到子总线上的设备的所有 I2C 事务。

下面是一个典型的例子。

拓扑:

```bash
                    Slave X @ 0x10
            .-----.   |
.-----.     |     |---+---- B
| CPU |--A--| ATR |
`-----'     |     |---+---- C
            `-----'   |
                    Slave Y @ 0x10
```

别名表：

A、B、C 是三个物理 I2C 总线，彼此电气独立。 ATR 接收在总线 A 上发起的事务，并将它们传播到总线 B 或总线 C 上，或者不传播到总线 B 或总线 C 上，具体取决于事务中的设备地址并基于别名表。

别名表：

| Client          | Alias |
| --------------- | ----- |
| X (bus B, 0x10) | 0x20  |
| Y (bus C, 0x10) | 0x30  |

事务:

- Slave X 驱动程序请求事务（在适配器 B 上），从属地址 0x10
- ATR 驱动程序发现从站 X 位于总线 B 上并且别名为 0x20，重写地址为 0x20 的消息，转发到适配器 A
- 总线 A 上的物理 I2C 事务，从地址 0x20
- ATR 芯片检测到地址 0x20 上的事务，在表中找到它，在总线 B 上传播事务并将地址转换为 0x10，保持总线 A 上的时钟延长等待回复
- Slave X芯片（在总线B上）在其自己的物理地址0x10处检测到事务并正常回复
- ATR 芯片停止时钟拉伸并在总线 A 上转发回复，地址转换回 0x20
- ATR 驱动程序收到回复后，按照最初的方式重写地址为 0x10 的消息
- 从属 X 驱动程序取回msgs[]，回复和地址 0x10

使用:

1. 在驱动程序中（通常在探测函数中）通过调用 i2c_atr_new() 传递附加/分离回调来添加 ATR
2. 当调用附加回调时，选择一个适当的别名，在芯片中配置它并在alias_id参数中返回所选的别名
3. 当调用分离回调时，从芯片中取消配置别名并将别名放回到池中以供以后使用


# 2. I2C ATR functions and data structures

- **struct i2c_atr_ops**

从 ATR 到设备驱动程序的回调。

定义:

```c
struct i2c_atr_ops {
    int (*attach_client)(struct i2c_atr *atr, u32 chan_id, const struct i2c_client *client, u16 alias);
    void (*detach_client)(struct i2c_atr *atr, u32 chan_id, const struct i2c_client *client);
};
```

**成员：**

**attach_client**

通知驱动程序子总线上连接了一个新设备，并为其分配了别名。 驱动程序必须配置硬件才能使用别名。

**detach_client**

通知驱动程序设备已断开连接。 驱动程序必须配置硬件才能停止使用别名。

**描述**

所有这些函数在成功时返回 0，否则返回负错误代码。

- **struct i2c_atr *i2c_atr_new(struct i2c_adapter *parent, struct device *dev, const struct i2c_atr_ops *ops, int max_adapters)**

分配并初始化 I2C ATR 助手。

**参数**

`struct i2c_adapter *parent`

&emsp;父（上游）适配器

`struct device *dev`

&emsp;充当 ATR 的设备

`const struct i2c_atr_ops *ops`

&emsp;驱动程序特定的回调

`int max_adapters`

&emsp;子适配器的最大数量

**描述**

新的 ATR 帮助程序已连接到父适配器，但没有子适配器。 调用 i2c_atr_add_adapter() 添加一些。

调用 i2c_atr_delete() 进行删除。

**返回值**

指向新 ATR 辅助对象的指针，或 ERR_PTR

- **void i2c_atr_delete(struct i2c_atr *atr)**

删除 I2C ATR 帮助程序。

**参数**

`struct i2c_atr *atr`

&emsp;要删除的 I2C ATR 帮助程序。

**描述**

前提条件：所有使用 i2c_atr_add_adapter() 添加的适配器必须通过调用 i2c_atr_del_adapter() 来删除。

- **int i2c_atr_add_adapter(struct i2c_atr *atr, u32 chan_id, struct device *adapter_parent, struct fwnode_handle *bus_handle)**

创建子（“下游”）I2C 总线。

**参数**

`struct i2c_atr *atr`

&emsp;I2C ATR

`u32 chan_id`

&emsp;新适配器的索引 (0 .. max_adapters-1)。 该值将传递给 struct i2c_atr_ops 中的回调。

`struct device *adapter_parent`

&emsp;用作新 i2c 适配器的父设备的设备，或 NULL 以使用 i2c-atr 设备作为父设备。

`struct fwnode_handle *bus_handle`

指向适配器的 i2c 外设的 fwnode 句柄，或 NULL。

**描述**

调用此函数后，将出现一个新的 i2c 总线。 在下游总线上添加和删除设备将导致调用 i2c_atr_ops->attach_client 和 i2c_atr_ops->detach_client 回调，以便驱动程序为设备分配别名。

适配器的fwnode设置为bus_handle，或者如果bus_handle为NULL，则该函数查找其“reg”属性与i2c-atr设备的“i2c-atr”节点下的chan_id匹配的子节点。

调用 i2c_atr_del_adapter() 删除适配器。

**返回值**

成功时为 0，否则为负错误代码。

- **void i2c_atr_del_adapter(struct i2c_atr *atr, u32 chan_id)**

删除由 i2c_atr_add_adapter() 添加的子（“下游”）I2C 总线。 如果未添加 I2C 总线，则此功能无效。

**参数**

`struct i2c_atr *atr`

&emsp;I2C ATR

`u32 chan_id`

&emsp;要删除的适配器的索引 (0 .. max_adapters-1)

- **void i2c_atr_set_driver_data(struct i2c_atr *atr, void *data)**

&emsp;将私有驱动程序数据设置为 i2c-atr 实例。

**参数**

`struct i2c_atr *atr`

&emsp;I2C ATR

`void *data`

&emsp;指向要存储的数据的指针

- **void *i2c_atr_get_driver_data(struct i2c_atr *atr)**

获取存储的驱动器数据。

**参数**

`struct i2c_atr *atr`

&emsp;I2C ATR

**返回值**

&emsp;指向存储数据的指针
