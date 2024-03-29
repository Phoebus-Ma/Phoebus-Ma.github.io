
# i2c-stub

# 1. Description

该模块是一个非常简单的假 I2C/SMBus 驱动程序。 它实现六种类型的 SMBus 命令：快速写入、(r/w) 字节、(r/w) 字节数据、(r/w) 字数据、(r/w) I2C 块数据和 (r/w) SMBus 块数据。

加载此驱动程序时，您需要提供芯片地址作为模块参数，然后该驱动程序将仅对这些地址的 SMBus 命令做出反应。

不需要任何硬件，也不与该模块关联。 它将接受写入指定地址的快速命令； 它将通过读取或写入内存中的数组来响应其他命令（也包括指定的地址）。 它还会为其处理的每个命令向内核日志发送垃圾邮件。

所有字节操作都实现了具有自动递增功能的指针寄存器。 这允许像 EEPROM 等支持的那样进行连续字节读取。

默认情况下禁用 SMBus 块命令支持，必须通过设置功能模块参数中的相应位 (0x03000000) 显式启用。

必须编写 SMBus 块命令来配置 SMBus 命令以进行 SMBus 块操作。 写入可以是部分的。 块读取命令始终返回迄今为止最大写入所选择的字节数。

典型的用例是这样的：

1. 加载这个模块
2. 使用 i2cset（来自 i2c-tools 项目）预加载一些数据
3. 加载目标芯片驱动模块
4. 在内核日志中观察其行为

i2c-tools 包中有一个名为 i2c-stub-from-dump 的脚本，它可以从芯片转储中自动加载寄存器值。


# 2. Parameters

int chip_addr[10]:

&emsp;SMBus 地址用于模拟芯片。

unsigned long functionality:

&emsp;功能覆盖，禁用某些命令。 有关合适的值，请参阅 <linux/i2c.h> 中的 I2C_FUNC_* 常量。 例如，值 0x1f0000 将仅启用快速、字节和字节数据命令。

u8 bank_reg[10], u8 bank_mask[10], u8 bank_start[10], u8 bank_end[10]:

&emsp;可选银行设置。 它们告诉哪个寄存器中的哪些位选择活动组，以及组寄存器的范围。


# 3. Caveats

如果您的目标驱动程序轮询某个字节或字等待其更改，则存根可能会将其锁定。 使用 i2cset 解锁。

如果你发送的垃圾邮件足够多，printk 可能是有损的。 这个模块确实需要像relayfs 这样的东西。
