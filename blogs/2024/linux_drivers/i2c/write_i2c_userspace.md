
# Implementing I2C device drivers in userspace

# 1. Introduction

通常，I2C 设备由内核驱动程序控制。 但也可以通过 /dev 接口从用户空间访问适配器上的所有设备。 为此，您需要加载模块 i2c-dev。

每个注册的 I2C 适配器都会获得一个编号，从 0 开始计数。您可以检查 /sys/class/i2c-dev/ 以查看哪个编号对应于哪个适配器。 或者，您可以运行“i2cdetect -l”来获取给定时间系统上存在的所有 I2C 适配器的格式化列表。 i2cDetect 是 i2c-tools 包的一部分。

I2C 设备文件是字符设备文件，其主设备号为 89，次设备号对应于如上所述分配的编号。 它们应该被称为“i2c-%d”（i2c-0、i2c-1、...、i2c-10、...）。 所有 256 个次要设备号均保留给 I2C。


# 2. C example

假设您想从 C 程序访问 I2C 适配器。 首先，您需要包含这两个标头：

```c
#include <linux/i2c-dev.h>
#include <i2c/smbus.h>
```

现在，您必须决定要访问哪个适配器。 您应该检查 /sys/class/i2c-dev/ 或运行“i2cdetect -l”来决定这一点。 适配器编号是动态分配的，因此您不能对它们进行太多假设。 他们甚至可以从一只靴子换成另一只靴子。

接下来，打开设备文件，如下：

```c
int file;
int adapter_nr = 2; /* probably dynamically determined */
char filename[20];

snprintf(filename, 19, "/dev/i2c-%d", adapter_nr);
file = open(filename, O_RDWR);
if (file < 0) {
    /* ERROR HANDLING; you can check errno to see what went wrong */
    exit(1);
}
```

打开设备后，您必须指定要与哪个设备地址进行通信：

```c
int addr = 0x40; /* The I2C address */

if (ioctl(file, I2C_SLAVE, addr) < 0) {
    /* ERROR HANDLING; you can check errno to see what went wrong */
    exit(1);
}
```

好了，现在一切都准备好了。 您现在可以使用 SMBus 命令或普通 I2C 与您的设备进行通信。 如果设备支持 SMBus 命令，则首选这些命令。 两者如下图所示：

```c
__u8 reg = 0x10; /* Device register to access */
__s32 res;
char buf[10];

/* Using SMBus commands */
res = i2c_smbus_read_word_data(file, reg);
if (res < 0) {
    /* ERROR HANDLING: I2C transaction failed */
} else {
    /* res contains the read word */
}

/*
 * Using I2C Write, equivalent of
 * i2c_smbus_write_word_data(file, reg, 0x6543)
 */
buf[0] = reg;
buf[1] = 0x43;
buf[2] = 0x65;
if (write(file, buf, 3) != 3) {
    /* ERROR HANDLING: I2C transaction failed */
}

/* Using I2C Read, equivalent of i2c_smbus_read_byte(file) */
if (read(file, buf, 1) != 1) {
    /* ERROR HANDLING: I2C transaction failed */
} else {
    /* buf[0] contains the read byte */
}
```

请注意，通过 read() 和 write() 调用只能实现 I2C 和 SMBus 协议的子集。 特别是，不支持所谓的组合事务（在同一事务中混合读取和写入消息）。 因此，用户空间程序几乎从不使用该接口。

重要提示：由于使用内联函数，因此在编译程序时必须使用“-O”或某些变体！


# 3. Full interface description

定义了以下 IOCTL：

**ioctl(file, I2C_SLAVE, long addr)**

更改从机地址。 地址在参数的低 7 位中传递（10 位地址除外，在本例中是在低 10 位中传递）。

**ioctl(file, I2C_TENBIT, long select)**

如果 select 不等于 0，则选择 10 位地址；如果 select 等于 0，则选择正常的 7 位地址。默认为 0。仅当适配器具有 I2C_FUNC_10BIT_ADDR 时，此请求才有效。

**ioctl(file, I2C_PEC, long select)**

如果 select 不等于 0，则选择 SMBus PEC（数据包错误检查）生成和验证；如果 select 等于 0，则禁用。默认 0。仅用于 SMBus 事务。 仅当适配器具有 I2C_FUNC_SMBUS_PEC 时，此请求才有效； 即使没有，它仍然是安全的，只是没有任何效果。

**ioctl(file, I2C_FUNCS, unsigned long *funcs)**

获取适配器功能并将其放入 *funcs 中。

**ioctl(file, I2C_RDWR, struct i2c_rdwr_ioctl_data *msgset)**

执行组合读/写事务，中间不停止。 仅当适配器具有 I2C_FUNC_I2C 时才有效。 参数是一个指向 a 的指针：

```c
struct i2c_rdwr_ioctl_data {
    struct i2c_msg *msgs;  /* ptr to array of simple messages */
    int nmsgs;             /* number of messages to exchange */
}
```

msgs[] 本身包含更多指向数据缓冲区的指针。 该函数将根据特定消息中是否设置了 I2C_M_RD 标志，向该缓冲区写入数据或从该缓冲区读取数据。 从机地址以及是否使用十位地址模式必须在每个消息中设置，覆盖使用上述 ioctl 设置的值。

**ioctl(file, I2C_SMBUS, struct i2c_smbus_ioctl_data *args)**

如果可能，请使用下面描述的提供的 i2c_smbus_* 方法，而不是直接发出 ioctl。

您可以使用 read(2) 和 write(2) 调用执行普通 I2C 事务。 您不需要传递地址字节； 相反，请在尝试访问设备之前通过 ioctl I2C_SLAVE 设置它。

您可以通过以下函数进行 SMBus 级别的事务（有关详细信息，请参阅文档文件 SMBus 协议）：

```c
__s32 i2c_smbus_write_quick(int file, __u8 value);
__s32 i2c_smbus_read_byte(int file);
__s32 i2c_smbus_write_byte(int file, __u8 value);
__s32 i2c_smbus_read_byte_data(int file, __u8 command);
__s32 i2c_smbus_write_byte_data(int file, __u8 command, __u8 value);
__s32 i2c_smbus_read_word_data(int file, __u8 command);
__s32 i2c_smbus_write_word_data(int file, __u8 command, __u16 value);
__s32 i2c_smbus_process_call(int file, __u8 command, __u16 value);
__s32 i2c_smbus_block_process_call(int file, __u8 command, __u8 length,
                                   __u8 *values);
__s32 i2c_smbus_read_block_data(int file, __u8 command, __u8 *values);
__s32 i2c_smbus_write_block_data(int file, __u8 command, __u8 length,
                                 __u8 *values);
```

所有这些交易失败时返回 -1； 你可以阅读 errno 来看看发生了什么。 “写入”交易成功时返回 0； “读取”事务返回读取的值，但 read_block 除外，它返回读取的值的数量。 块缓冲区不需要长于 32 字节。

上述函数可通过链接 i2c-tools 项目提供的 libi2c 库来使用。查看: <https://git.kernel.org/pub/scm/utils/i2c-tools/i2c-tools.git/>。


# 4. Implementation details

对于感兴趣的人，以下是当您使用 I2C 的 /dev 接口时内核内部发生的代码流程：

1. 您的程序打开 /dev/i2c-N 并对其调用 ioctl()，如上面的“C 示例”部分所述。

2. 这些 open() 和 ioctl() 调用由 i2c-dev 内核驱动程序处理：分别参见 i2c-dev.c:i2cdev_open() 和 i2c-dev.c:i2cdev_ioctl()。 您可以将 i2c-dev 视为可以从用户空间进行编程的通用 I2C 芯片驱动程序。

3. 一些 ioctl() 调用用于管理任务，由 i2c-dev 直接处理。 示例包括 I2C_SLAVE（设置要访问的设备的地址）和 I2C_PEC（启用或禁用对未来事务的 SMBus 错误检查。）

4. 其他 ioctl() 调用由 i2c-dev 转换为内核函数调用。 示例包括 I2C_FUNCS（使用 i2c.h:i2c_get_functionity() 查询 I2C 适配器功能）和 I2C_SMBUS（使用 i2c-core-smbus.c:i2c_smbus_xfer() 执行 SMBus 事务）。

i2c-dev 驱动程序负责检查来自用户空间的所有参数的有效性。 此后，通过 i2c-dev 来自用户空间的这些调用与直接由内核 I2C 芯片驱动程序执行的调用之间没有区别。 这意味着 I2C 总线驱动程序不需要实现任何特殊的功能来支持从用户空间的访问。

5. 这些 i2c.h 函数是 I2C 总线驱动程序实际实现的包装器。 每个适配器必须声明实现这些标准调用的回调函数。 i2c.h:i2c_get_functionity() 调用 i2c_adapter.algo->featureity()，而 i2c-core-smbus.c:i2c_smbus_xfer() 调用适配器.algo->smbus_xfer()（如果已实现），否则 i2c- core-smbus.c:i2c_smbus_xfer_emulated() 又调用 i2c_adapter.algo->master_xfer()。

I2C 总线驱动程序处理完这些请求后，执行会沿着调用链向上运行，几乎不进行任何处理，除了 i2c-dev 以适合 ioctl 的格式打包返回的数据（如果有）。
