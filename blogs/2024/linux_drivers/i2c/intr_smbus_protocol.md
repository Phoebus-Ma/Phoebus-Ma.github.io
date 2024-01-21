
# The SMBus Protocol

# 1. Introduction

以下是 SMBus 协议的摘要。 它适用于协议的所有修订版（1.0、1.1 和 2.0）。 本文档末尾简要描述了该软件包不支持的某些协议功能。

某些适配器仅支持 SMBus（系统管理总线）协议，该协议是 I2C 协议的子集。 幸运的是，许多设备仅使用相同的子集，这使得将它们放在 SMBus 上成为可能。

如果您为某些 I2C 设备编写驱动程序，请尽可能尝试使用 SMBus 命令（如果设备仅使用 I2C 协议的该子集）。 这使得可以在 SMBus 适配器和 I2C 适配器上使用设备驱动程序（SMBus 命令集在 I2C 适配器上自动转换为 I2C，但在大多数纯 SMBus 适配器上根本无法处理普通 I2C 命令）。

下面是 SMBus 协议操作以及执行它们的函数的列表。 请注意，SMBus 协议规范中使用的名称通常与这些函数名称不匹配。 对于某些传递单个数据字节的操作，使用 SMBus 协议操作名称的函数完全执行不同的协议操作。

每个事务类型对应一个功能标志。 在调用事务函数之前，设备驱动程序应始终检查（仅一次）相应的功能标志，以确保底层 I2C 适配器支持相关事务。 有关详细信息，请参阅[I2C/SMBus 功能](write_i2c_functionity.md)。


# 2. Key to symbols

|                |                                                                  |
| -------------- | ---------------------------------------------------------------- |
| S              | 启动条件                                                          |
| Sr             | 重复启动条件，用于从写模式切换到读模式。                             |
| P              | 停止条件                                                          |
| Rd/Wr (1 bit)  | 读/写位。 Rd 等于 1，Wr 等于 0。                                   |
| A, NA (1 bit)  | 应答 (ACK) 和不应答 (NACK) 位                                      |
| Addr (7 bits)  | I2C 7 位地址。 请注意，这可以扩展以获得 10 位 I2C 地址。             |
| Comm (8 bits)  | 命令字节，通常选择设备上的寄存器的数据字节。                          |
| Data (8 bits)  | 一个纯数据字节。 DataLow 和 DataHigh 表示 16 位字的低字节和高字节。   |
| Count (8 bits) | 包含块操作长度的数据字节。                                          |
| [..]           | 由 I2C 设备发送的数据，而不是由主机适配器发送的数据。                 |


# 3. SMBus Quick Command

这会向设备发送一个位，位于 Rd/Wr 位的位置：

```bash
S Addr Rd/Wr [A] P
```

功能标志： I2C_FUNC_SMBUS_QUICK


# 4. SMBus Receive Byte

由 i2c_smbus_read_byte() 实现

这从设备读取单个字节，而不指定设备寄存器。 有些设备非常简单，这个接口就足够了； 对于其他人，如果您想读取与之前的 SMBus 命令相同的寄存器，则这是一个简写：

```bash
S Addr Rd [A] [Data] NA P
```

功能标志： I2C_FUNC_SMBUS_READ_BYTE


# 5. SMBus Send Byte

由 i2c_smbus_write_byte() 实现

此操作与接收字节相反：它将单个字节发送到设备。 有关详细信息，请参阅接收字节。

```bash
S Addr Wr [A] Data [A] P
```

功能标志： I2C_FUNC_SMBUS_WRITE_BYTE


# 6. SMBus Read Byte

由 i2c_smbus_read_byte_data() 实现

这从设备的指定寄存器读取单个字节。 该寄存器通过 Comm 字节指定：

```bash
S Addr Wr [A] Comm [A] Sr Addr Rd [A] [Data] NA P
```

功能标志： I2C_FUNC_SMBUS_READ_BYTE_DATA


# 7. SMBus Read Word

由 i2c_smbus_read_word_data() 实现

这个操作很像读取字节； 再次，从设备、通过 Comm 字节指定的指定寄存器读取数据。 但这一次，数据是一个完整的字（16 位）：

```bash
S Addr Wr [A] Comm [A] Sr Addr Rd [A] [DataLow] A [DataHigh] NA P
```

功能标志： I2C_FUNC_SMBUS_READ_WORD_DATA

请注意，方便的函数 i2c_smbus_read_word_swapped() 可用于读取，其中两个数据字节是相反的（不符合 SMBus，但非常流行。）


# 8. SMBus Write Byte

由 i2c_smbus_write_byte_data() 实现

这会将单个字节写入设备的指定寄存器。 该寄存器通过 Comm 字节指定。 这与读字节操作相反。

```bash
S Addr Wr [A] Comm [A] Data [A] P
```

功能标志： I2C_FUNC_SMBUS_WRITE_BYTE_DATA


# 9. SMBus Write Word

由 i2c_smbus_write_word_data() 实现

这与读字操作相反。 16 位数据写入设备，写入通过 Comm 字节指定的指定寄存器：

```bash
S Addr Wr [A] Comm [A] DataLow [A] DataHigh [A] P
```

功能标志： I2C_FUNC_SMBUS_WRITE_WORD_DATA

请注意，便利函数 i2c_smbus_write_word_swapped() 可用于两个数据字节相反的写入（不符合 SMBus，但非常流行。）


# 10. SMBus Process Call

该命令选择一个设备寄存器（通过 Comm 字节），向其发送 16 位数据，并读取 16 位数据作为返回：

```bash
S Addr Wr [A] Comm [A] DataLow [A] DataHigh [A]
                            Sr Addr Rd [A] [DataLow] A [DataHigh] NA P
```

功能标志： I2C_FUNC_SMBUS_PROC_CALL


# 11. SMBus Block Read

由i2c_smbus_read_block_data()实现

该命令从设备中通过 Comm 字节指定的指定寄存器读取最多 32 字节的块。 数据量由设备在计数字节中指定。

```bash
S Addr Wr [A] Comm [A]
          Sr Addr Rd [A] [Count] A [Data] A [Data] A ... A [Data] NA P
```

功能标志： I2C_FUNC_SMBUS_READ_BLOCK_DATA


# 12. SMBus Block Write

由i2c_smbus_write_block_data()实现

与块读取命令相反，此命令将最多 32 个字节写入设备，写入通过 Comm 字节指定的指定寄存器。 数据量在计数字节中指定。

```bash
S Addr Wr [A] Comm [A] Count [A] Data [A] Data [A] ... [A] Data [A] P
```

功能标志： I2C_FUNC_SMBUS_WRITE_BLOCK_DATA


# 13. SMBus Block Write - Block Read Process Call

SMBus 块写入 - 块读取过程调用是在规范修订版 2.0 中引入的。

该命令选择一个设备寄存器（通过 Comm 字节），向其发送 1 到 31 字节的数据，并返回读取 1 到 31 字节的数据：

```bash
S Addr Wr [A] Comm [A] Count [A] Data [A] ...
                            Sr Addr Rd [A] [Count] A [Data] ... A P
```

功能标志： I2C_FUNC_SMBUS_BLOCK_PROC_CALL


# 14. SMBus Host Notify

该命令从充当主设备的 SMBus 设备发送到充当从设备的 SMBus 主机。 它与Write Word 的形式相同，只是将命令代码替换为警报设备的地址。

```bash
[S] [HostAddr] [Wr] A [DevAddr] A [DataLow] A [DataHigh] A [P]
```

Linux 内核中是通过以下方式实现的：

- 支持 SMBus Host Notify 的 I2C 总线驱动程序应报告 I2C_FUNC_SMBUS_HOST_NOTIFY。

- I2C 总线驱动程序通过调用 i2c_handle_smbus_host_notify() 来触发 SMBus 主机通知。

- 如果没有其他人指定另一个，则可以触发 SMBus 主机通知的设备的 I2C 驱动程序会将 client->irq 分配给主机通知 IRQ。

目前无法从客户端检索数据参数。


# 15. Packet Error Checking (PEC)

规范修订版 1.1 中引入了数据包错误检查。

PEC 在终止 STOP 之前立即向使用它的传输添加 CRC-8 错误检查字节。


# 16. Address Resolution Protocol (ARP)

地址解析协议是在规范修订版 2.0 中引入的。 它是使用上述消息的高层协议。

ARP 在协议中添加了设备枚举和动态地址分配。 所有 ARP 通信都使用从地址 0x61 并需要 PEC 校验和。


# 17. SMBus Alert

SMBus Alert 在规范修订版 1.0 中引入。

SMBus 警报协议允许多个 SMBus 从设备共享 SMBus 主设备上的单个中断引脚，同时仍允许主设备知道哪个从设备触发了中断。

Linux 内核中是通过以下方式实现的：

- 支持 SMBus 警报的 I2C 总线驱动程序应调用 i2c_new_smbus_alert_device() 来安装 SMBus 警报支持。

- 可以触发 SMBus 警报的设备的 I2C 驱动程序应实现可选的alert()回调。


# 18. I2C Block Transactions

以下 I2C 块事务类似于 SMBus 块读取和写入操作，但这些操作没有计数字节。 它们受 SMBus 层支持，并在此处进行描述以确保完整性，但它们并非由 SMBus 规范定义。

I2C 块事务不限制传输的字节数，但 SMBus 层限制为 32 字节。


# 19. I2C Block Read

由 i2c_smbus_read_i2c_block_data() 实现

此命令从设备、通过 Comm 字节指定的指定寄存器读取字节块：

```bash
S Addr Wr [A] Comm [A]
          Sr Addr Rd [A] [Data] A [Data] A ... A [Data] NA P
```

功能标志： I2C_FUNC_SMBUS_READ_I2C_BLOCK


# 20. I2C Block Write

由 i2c_smbus_write_i2c_block_data() 实现

与块读取命令相反，此命令将字节写入设备，写入通过通信字节指定的指定寄存器。 请注意，支持 0、2 或更多字节的命令长度，因为它们与数据无法区分。

```bash
S Addr Wr [A] Comm [A] Data [A] Data [A] ... [A] Data [A] P
```

功能标志： I2C_FUNC_SMBUS_WRITE_I2C_BLOCK
