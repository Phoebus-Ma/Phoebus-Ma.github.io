
# The I2C Protocol

本文档概述了基本 I2C 事务以及执行这些事务的内核 API。


# 1. Key to symbols

|               |                                                      |
| ------------- | ---------------------------------------------------- |
| S             | 启动条件                                              |
| P             | 停止条件                                              |
| Rd/Wr (1 bit) | 读/写位。 Rd 等于 1，Wr 等于 0                         |
| A, NA (1 bit) | 应答 (ACK) 和不应答 (NACK) 位                          |
| Addr (7 bits) | I2C 7 位地址。 请注意，这可以扩展以获得 10 位 I2C 地址   |
| Data (8 bits) | 一个纯数据字节                                         |
| [..]          | I2C 设备发送的数据，而不是主机适配器发送的数据           |


# 2. Simple send transaction

由i2c_master_send()实现：

```bash
S Addr Wr [A] Data [A] Data [A] ... [A] Data [A] P
```


# 3. Simple receive transaction

由i2c_master_recv()实现：

```bash
S Addr Rd [A] [Data] A [Data] A ... A [Data] NA P
```


# 4. Combined transactions

由 i2c_transfer() 实现。

它们与上述事务类似，但发送的不是停止条件 P，而是发送启动条件 S，并且事务继续。 字节读取后跟字节写入的示例：

```bash
S Addr Rd [A] [Data] NA S Addr Wr [A] Data [A] P
```


# 5. Modified transactions

还可以通过为 I2C 消息设置这些标志来生成对 I2C 协议的以下修改。 除了 I2C_M_NOSTART 之外，它们通常只需要解决设备问题：

**I2C_M_IGNORE_NAK:**

&emsp;通常，如果客户端发出[NA]，消息会立即中断。 设置此标志会将任何 [NA] 视为 [A]，并且发送所有消息。 这些消息可能仍无法达到 SCL lo->hi 超时。

**I2C_M_NO_RD_ACK:**

&emsp;在读取消息中，主 A/NA 位被跳过。

**I2C_M_NOSTART:**

&emsp;在组合事务中，在某个时刻不会生成“S Addr Wr/Rd [A]”。 例如，在第二部分消息上设置 I2C_M_NOSTART 会生成类似以下内容的内容：

```bash
S Addr Rd [A] [Data] NA Data [A] P
```

&emsp;如果您为第一个部分消息设置 I2C_M_NOSTART 变量，我们不会生成 Addr，但会生成启动条件 S。这可能会使总线上的所有其他客户端感到困惑，因此不要尝试此操作。

&emsp;这通常用于将系统内存中多个数据缓冲区的传输收集到显示为到 I2C 设备的单次传输中，但也可以在某些罕见设备的方向更改之间使用。

**I2C_M_REV_DIR_ADDR:**

&emsp;这会切换 Rd/Wr 标志。 也就是说，如果您想要执行写入操作，但需要发出 Rd 而不是 Wr，反之亦然，则可以设置此标志。 例如：

```bash
S Addr Rd [A] Data [A] Data [A] ... [A] Data [A] P
```

**I2C_M_STOP:**

消息后强制停止条件 (P)。 一些 I2C 相关协议（例如 SCCB）需要这样做。 通常，您确实不希望在一次传输的消息之间被打断。
