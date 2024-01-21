
# Linux I2C slave testunit backend

# 1. Introduction

Wolfram Sang <wsa@sang-engineering.com> 于 2020 年编辑。

该后端可用于触发 I2C 总线主控器的测试用例，这些主控器需要具有某些功能的远程设备（并且通常不太容易获得）。 示例包括多主机测试和 SMBus 主机通知测试。 对于某些测试，I2C 从控制器必须能够在主从模式之间切换，因为它也需要发送数据。

请注意，这是一个用于测试和调试的设备。 不应在生产版本中启用它。 虽然有一些版本控制，并且我们努力保持向后兼容性，但没有稳定的 ABI 保证！

实例化设备是常规的。 总线 0、地址 0x30 的示例：

```bash
# echo "slave-testunit 0x1030" > /sys/bus/i2c/devices/i2c-0/new_device
```

之后，您将有一个只写设备正在监听。 读取只会返回测试单元的 8 位版本号。 写入时，设备由 4 个 8 位寄存器组成，除了一些“部分”命令外，必须写入所有寄存器才能启动测试用例，即通常向设备写入 4 个字节。 寄存器是：

0x00 CMD - 触发哪个测试 0x01 DATAL - 用于测试的配置字节 1 0x02 DATAH - 用于测试的配置字节 2 0x03 DELAY - 延迟 n * 10ms，直到测试开始

使用 i2c-tools 包中的“i2cset”，通用命令如下所示：

```bash
# i2cset -y <bus_num> <testunit_address> <CMD> <DATAL> <DATAH> <DELAY> i
```

DELAY 是一个通用参数，它将延迟 CMD 中测试的执行。 当命令正在运行时（包括延迟），新命令将不会被确认。 您需要等到旧的完成。

这些命令将在下一节中描述。 无效命令将导致传输不被确认。


# 2. Commands

0x00 NOOP (保留供将来使用)

0x01 READ_BYTES (还需要主模式)

&emsp;DATAL - 从中读取数据的地址（低 7 位，当前未使用的最高位） DATAH - 要读取的字节数

这对于测试总线主控驱动程序是否正确处理多主控非常有用。 您可以触发测试单元从总线上的另一个设备读取字节。 如果被测总线主控器同时也想访问总线，则总线将繁忙。 延迟 50 毫秒后从设备 0x50 读取 128 字节的示例：

```bash
# i2cset -y 0 0x30 0x01 0x50 0x80 0x05 i
```

0x02 SMBUS_HOST_NOTIFY (还需要主模式)

&emsp;DATAL - 要发送的状态字的低字节 DATAH - 要发送的状态字的高字节

此测试将向主机发送 SMBUS_HOST_NOTIFY 消息。 请注意，状态字当前在 Linux 内核中被忽略。 10 毫秒后发送通知的示例：

```bash
# i2cset -y 0 0x30 0x02 0x42 0x64 0x01 i
```

0x03 SMBUS_BLOCK_PROC_CALL (部分命令)

&emsp;DATAL - 必须为“1”，即将写入另外一个字节 DATAH - 要发回的字节数 DELAY - 不适用，部分命令！

该测试将响应 SMBus 规范定义的块进程调用。 写入的一个数据字节指定在接下来的读取传输中将发回多少字节。 请注意，在此读取传输中，测试单元将为后面的字节长度添加前缀。 因此，如果您的主机总线驱动程序像大多数那样模拟 SMBus 调用，则它需要支持 i2c_msg 的 I2C_M_RECV_LEN 标志。 这是一个很好的测试用例。 返回的数据首先包含长度，然后包含从 length-1 到 0 的字节数组。这是一个使用 i2ctransfer 模拟 i2c_smbus_block_process_call() 的示例（需要 i2c-tools v4.2 或更高版本）：

```bash
# i2ctransfer -y 0 w3@0x30 0x03 0x01 0x10 r? 0x10 0x0f 0x0e 0x0d 0x0c 0x0b 0x0a 0x09 0x08 0x07 0x06 0x05 0x04 0x03 0x02 0x01 0x00
```
