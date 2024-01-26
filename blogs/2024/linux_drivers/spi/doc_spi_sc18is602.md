
# Kernel driver spi-sc18is602

**Supported chips:**

- NXP SI18IS602/602B/603

- Datasheet: <https://www.nxp.com/documents/data_sheet/SC18IS602_602B_603.pdf>

**Author:**

Guenter Roeck <linux@roeck-us.net>


# 1. Description

该驱动程序提供将 NXP SC18IS602/603 I2C 总线转 SPI 桥连接到内核的 SPI 核心子系统的功能。

该驱动程序不会探测支持的芯片，因为 SI18IS602/603 不支持芯片 ID 寄存器。 您必须显式实例化设备。 详细信息请参见[如何实例化I2C设备](../i2c/intr_i2c_instantiate.md)。


# 2. Usage Notes

此驱动程序需要 I2C 适配器驱动程序来支持原始 I2C 消息。 不支持只能处理 SMBus 协议的 I2C 适配器驱动程序。

SC18IS602/603 支持的最大 SPI 消息大小为 200 字节。 尝试启动更长的传输将失败并显示 -EINVAL。 EEPROM 读取操作和类似的大型访问必须分成多个块，每个 SPI 消息不超过 200 字节（建议每个消息 128 字节数据）。 这意味着“cp”或“od”等自动使用大块大小访问设备的程序不能直接用于从 EEPROM 读取数据。 应改用诸如 dd 之类的可以指定块大小的程序。
