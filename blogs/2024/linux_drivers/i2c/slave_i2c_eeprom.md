
# Linux I2C slave EEPROM backend

Wolfram Sang <wsa@sang-engineering.com> 于 2014 年第 20 周编辑。

该后端模拟连接的 I2C 总线上的 EEPROM。 可以通过位于 sysfs 中的文件从用户空间访问其内存内容：

```bash
/sys/bus/i2c/devices/<device-directory>/slave-eeprom
```

提供以下类型：24c02、24c32、24c64 和 24c512。 还支持只读变体。 实例化所需的名称格式为“slave-<type>[ro]”。 示例如下：

24c02, read/write, address 0x64:

```bash
# echo slave-24c02 0x1064 > /sys/bus/i2c/devices/i2c-1/new_device
```

24c512, read-only, address 0x42:

```bash
# echo slave-24c512ro 0x1042 > /sys/bus/i2c/devices/i2c-1/new_device
```

如果名为“固件名称”的设备属性包含有效的文件名（仅限 DT 或 ACPI），您还可以在引导期间预加载数据。

截至 2015 年，Linux 不支持对二进制 sysfs 文件进行轮询，因此当另一个 master 更改内容时不会收到通知。
