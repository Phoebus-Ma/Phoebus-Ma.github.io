
# I2C/SMBus Functionality

# 1. INTRODUCTION

由于并非每个 I2C 或 SMBus 适配器都实现了 I2C 规范中的所有内容，因此当客户端可以选择连接到适配器时，客户端不能相信它需要的所有内容都已实现：客户端需要某种方法来检查适配器是否具有所需的功能 功能。


# 2. FUNCTIONALITY CONSTANTS

有关最新的功能常量列表，请检查 <uapi/linux/i2c.h>！

|                                 |                                                                                    |
| ------------------------------- | ---------------------------------------------------------------------------------- |
| I2C_FUNC_I2C                    | 普通 i2c 级命令（纯 SMBus 适配器通常无法执行这些操作）                                 |
| I2C_FUNC_10BIT_ADDR             | 处理 10 位地址扩展                                                                  |
| I2C_FUNC_PROTOCOL_MANGLING      | 了解 I2C_M_IGNORE_NAK、I2C_M_REV_DIR_ADDR 和 I2C_M_NO_RD_ACK 标志（修改 I2C 协议！） |
| I2C_FUNC_NOSTART                | 可以跳过重复的启动序列                                                               |
| I2C_FUNC_SMBUS_QUICK            | 处理 SMBus write_quick 命令                                                         |
| I2C_FUNC_SMBUS_READ_BYTE        | 处理 SMBus read_byte 命令                                                           |
| I2C_FUNC_SMBUS_WRITE_BYTE       | 处理 SMBus write_byte 命令                                                          |
| I2C_FUNC_SMBUS_READ_BYTE_DATA   | 处理 SMBus read_byte_data 命令                                                      |
| I2C_FUNC_SMBUS_WRITE_BYTE_DATA  | 处理 SMBus write_byte_data 命令                                                     |
| I2C_FUNC_SMBUS_READ_WORD_DATA   | 处理 SMBus read_word_data 命令                                                      |
| I2C_FUNC_SMBUS_WRITE_WORD_DATA  | 处理 SMBus write_byte_data 命令                                                     |
| I2C_FUNC_SMBUS_PROC_CALL        | 处理 SMBus process_call 命令                                                        |
| I2C_FUNC_SMBUS_READ_BLOCK_DATA  | 处理 SMBus read_block_data 命令                                                     |
| I2C_FUNC_SMBUS_WRITE_BLOCK_DATA | 处理 SMBus write_block_data 命令                                                    |
| I2C_FUNC_SMBUS_READ_I2C_BLOCK   | 处理 SMBus read_i2c_block_data 命令                                                 |
| I2C_FUNC_SMBUS_WRITE_I2C_BLOCK  | 处理 SMBus write_i2c_block_data 命令                                                |

为了您的方便，还定义了上述标志的一些组合：

|                           |                                                           |
| ------------------------- | --------------------------------------------------------- |
| I2C_FUNC_SMBUS_BYTE       | 处理 SMBus read_byte 和 write_byte 命令                    |
| I2C_FUNC_SMBUS_BYTE_DATA  | 处理 SMBus read_byte_data 和 write_byte_data 命令          |
| I2C_FUNC_SMBUS_WORD_DATA  | 处理 SMBus read_word_data 和 write_word_data 命令          |
| I2C_FUNC_SMBUS_BLOCK_DATA | 处理 SMBus read_block_data 和 write_block_data 命令        |
| I2C_FUNC_SMBUS_I2C_BLOCK  | 处理 SMBus read_i2c_block_data 和 write_i2c_block_data 命令|
| I2C_FUNC_SMBUS_EMUL       | 处理可由真实 I2C 适配器模拟的所有 SMBus 命令（使用透明模拟层） |

在 3.5 之前的内核版本中，I2C_FUNC_NOSTART 是作为 I2C_FUNC_PROTOCOL_MANGLING 的一部分实现的。


# 3. ADAPTER IMPLEMENTATION

当您编写新的适配器驱动程序时，您将必须实现函数回调功能。 下面给出了典型的实现。

典型的仅 SMBus 适配器会列出它支持的所有 SMBus 事务。 此示例来自 i2c-piix4 驱动程序：

```c
static u32 piix4_func(struct i2c_adapter *adapter)
{
      return I2C_FUNC_SMBUS_QUICK | I2C_FUNC_SMBUS_BYTE |
             I2C_FUNC_SMBUS_BYTE_DATA | I2C_FUNC_SMBUS_WORD_DATA |
             I2C_FUNC_SMBUS_BLOCK_DATA;
}
```

典型的全 I2C 适配器将使用以下内容（来自 i2c-pxa 驱动程序）：

```c
static u32 i2c_pxa_functionality(struct i2c_adapter *adap)
{
      return I2C_FUNC_I2C | I2C_FUNC_SMBUS_EMUL;
}
```

I2C_FUNC_SMBUS_EMUL 包括 i2c-core 可以使用 I2C_FUNC_I2C 模拟的所有 SMBus 事务（加上 I2C 块事务），而无需适配器驱动程序的任何帮助。 这个想法是让客户端驱动程序检查 SMBus 功能的支持，而不必关心这些功能是由适配器在硬件中实现的，还是由 I2C 适配器之上的 i2c 核心在软件中模拟的。


# 4. CLIENT CHECKING

在客户端尝试连接到适配器之前，甚至进行测试以检查适配器上是否存在其支持的设备之一之前，它应该检查所需的功能是否存在。 执行此操作的典型方法是（来自 lm75 驱动程序）：

```c
static int lm75_detect(...)
{
      (...)
      if (!i2c_check_functionality(adapter, I2C_FUNC_SMBUS_BYTE_DATA |
                                   I2C_FUNC_SMBUS_WORD_DATA))
              goto exit;
      (...)
}
```

此处，lm75 驱动程序检查适配器是否可以执行 SMBus 字节数据和 SMBus 字数据事务。 如果没有，那么驱动程序将无法在此适配器上工作，并且继续下去没有意义。 如果上述检查成功，则驱动程序知道它可以调用以下函数：i2c_smbus_read_byte_data()、i2c_smbus_write_byte_data()、i2c_smbus_read_word_data() 和 i2c_smbus_write_word_data()。 根据经验，您使用 i2c_check_functionity() 测试的功能常量应该与您驱动程序调用的 i2c_smbus_* 函数完全匹配。

请注意，上面的检查并不能说明这些功能是由底层适配器在硬件中实现还是由 i2c 核心在软件中模拟。 客户端驱动程序不必关心这一点，因为 i2c 核心将在 I2C 适配器之上透明地实现 SMBus 事务。


# 5. CHECKING THROUGH /DEV

如果您尝试从用户空间程序访问适配器，则必须使用 /dev 接口。 当然，您仍然需要检查您需要的功能是否受支持。 这是使用 I2C_FUNCS ioctl 完成的。 下面是改编自 i2cdetect 程序的示例：

```c
int file;
if (file = open("/dev/i2c-0", O_RDWR) < 0) {
      /* Some kind of error handling */
      exit(1);
}
if (ioctl(file, I2C_FUNCS, &funcs) < 0) {
      /* Some kind of error handling */
      exit(1);
}
if (!(funcs & I2C_FUNC_SMBUS_QUICK)) {
      /* Oops, the needed functionality (SMBus write_quick function) is
         not available! */
      exit(1);
}
/* Now it is safe to use the SMBus write_quick command */
```
