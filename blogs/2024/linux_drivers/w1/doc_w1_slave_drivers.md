
# 1-wire Slave Drivers

# 1. w1_ds2406 kernel driver

支持芯片：

Maxim DS2406 (and other family 0x12) addressable switches

作者： Scott Alfter <scott@alfter.us>


## 1.1 Description

w1_ds2406 驱动程序允许打开和关闭连接的设备。 这些芯片还提供128字节的OTP EPROM，但不支持读/写。 在 TSOC-6 形式中，DS2406 提供两个开关输出，并可通过专用输入供电。 在 TO-92 形式中，它提供一个输出并且仅使用寄生功率。

该驱动程序提供了两个 sysfs 文件。 状态可读； 它给出了每个开关的当前状态，其中 PIO A 在位 0 中，PIO B 在位 1 中。驱动程序将此状态与 0x30 进行“或”运算，因此 shell 脚本会使用 ASCII 0/1/2/3。 输出可写； 位 0 和 1 分别控制 PIO A 和 B。 位 2-7 被忽略，因此写入 ASCII 数据是安全的。

CRC 在读取和写入时进行检查。 失败的检查会导致返回 I/O 错误。 写入失败时，开关状态不会更改。


# 2. Kernel driver w1_ds2413

支持芯片：

Maxim DS2413 1-Wire Dual Channel Addressable Switch

支持的家庭代码：

W1_FAMILY_DS2413, 0x3A

作者： Mariusz Bialonczyk <manio@skyboo.net>


## 2.1 Description

DS2413 芯片有两个开漏输出（PIO A 和 PIO B）。 通过 sysfs 文件“output”和“state”提供支持。


## 2.2 Reading state

“状态”文件提供一字节值，其格式与芯片 PIO_ACCESS_READ 命令相同（详细信息请参阅数据表）：

|          |                                    |
| -------- | ---------------------------------- |
| Bit 0:   | PIOA Pin State                     |
| Bit 1:   | PIOA Output Latch State            |
| Bit 2:   | PIOB Pin State                     |
| Bit 3:   | PIOB Output Latch State            |
| Bit 4-7: | 位 3 与位 0 的补码 (由内核模块验证)  |

该文件是只读的。


## 2.3 Writing output

您可以使用“输出”文件设置 PIO 引脚。 它是可写的，您可以向此 sysfs 文件写入一字节值。 同样，字节格式与 PIO_ACCESS_WRITE 命令相同：

|          |                                |
| -------- | ------------------------------ |
| Bit 0:   | PIOA                           |
| Bit 1:   | PIOB                           |
| Bit 2-7: | 没关系（驱动程序会将其设置为“1”） |

该芯片具有某种针对传输错误的基本保护。 读取状态时，有四个补码位。 驱动程序正在检查此补码，如果错误，则返回 I/O 错误。

写入输出时，主机必须以其反转形式重复 PIO 输出数据字节，并等待确认。 如果连续3次写入不成功，也会返回I/O错误。


# 3. Kernel driver w1_ds2423

支持芯片：

Maxim DS2423 based counter devices.

支持的家庭代码：

W1_THERM_DS2423, 0x1D

作者： Mika Laitio <lamikr@pilppa.org>


## 3.1 Description

通过 sysfs w1_slave 文件提供支持。 w1_slave 文件的每个打开和读取序列都会启动 DS2423 第 12 - 15 页中可用的计数器和 RAM 的读取。

每个页面的结果以 ASCII 输出的形式提供，其中每个计数器值和关联的 RAM 缓冲区都输出到自己的行。

每行将包含从计数器和内存页读取的 42 个字节的值，以及 crc=YES 或 NO，用于指示读取操作是否成功以及 CRC 是否匹配。 如果操作成功，则在每行末尾还有一个计数器值，以整数形式表示在 c= 后面

42字节代表的含义如下：

- RAM 页中的 1 个字节
- 4 个字节用于计数器值
- 4 个零字节
- crc16 为 2 个字节，是根据自前一个 crc 字节以来读取的数据计算得出的
- RAM 页剩余 31 个字节
- crc=YES/NO 表示读取是否正常且crc是否匹配
- c=<int> 当前计数器值

成功读取的示例：

```bash
00 02 00 00 00 00 00 00 00 6d 38 00 ff ff 00 00 fe ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff crc=YES c=2
00 02 00 00 00 00 00 00 00 e0 1f 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff crc=YES c=2
00 29 c6 5d 18 00 00 00 00 04 37 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff crc=YES c=408798761
00 05 00 00 00 00 00 00 00 8d 39 ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff crc=YES c=5
```

带有 crc 错误的读取示例：

```bash
00 02 00 00 00 00 00 00 00 6d 38 00 ff ff 00 00 fe ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff crc=YES c=2
00 02 00 00 22 00 00 00 00 e0 1f 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff crc=NO
00 e1 61 5d 19 00 00 00 00 df 0b 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff 00 00 ff ff crc=NO
00 05 00 00 20 00 00 00 00 8d 39 ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff crc=NO
```


# 4. Kernel driver w1_ds2438

支持芯片：

Maxim DS2438 Smart Battery Monitor

支持的家庭代码：

W1_FAMILY_DS2438, 0x26

作者： Mariusz Bialonczyk <manio@skyboo.net>


## 4.1 Description

DS2438 芯片提供了电池组所需的多种功能。 它还具有 40 字节的非易失性 EEPROM。 由于具有温度、电流和电压测量功能，该芯片也常用于气象站和应用，例如：雨量计、风速/风向测量、湿度传感等。

当前支持通过以下 sysfs 文件提供（除“iad”和“offset”之外的所有文件都是只读的）：


## 4.2 "iad"

该文件控制状态/配置寄存器中的“当前 A/D 控制位”(IAD)。 写入零值将清除 IAD 位并禁用电流测量。 写入值“1”设置 IAD 位（启用测量）。 DS2438 中 IAD 位默认使能。

写入 sysfs 文件时，第 2-7 位将被忽略，因此写入 ASCII 是安全的。 当设置新值时出现问题时，会返回 I/O 错误。


## 4.3 "page0"

该文件提供了芯片第 0 页 (00h) 的完整 8 个字节。 此页面包含 DS2438 最常访问的信息。 在内部读取该文件时，还会从从设备获取附加的 CRC 字节。 如果正确，则将 8 字节页面数据传递到用户空间，否则返回 I/O 错误。


## 4.4 "page1"

该文件提供了芯片第 1 页 (01h) 的完整 8 个字节。 该页面包含 DS2438 的 ICA、经过时间计和当前偏移数据。 在内部读取该文件时，还会从从设备获取附加的 CRC 字节。 如果正确，则将 8 字节页面数据传递到用户空间，否则返回 I/O 错误。


## 4.5 "offset"

该文件控制芯片的2字节偏移寄存器。 写入 2 字节值将更改偏移寄存器，从而更改芯片完成的当前测量。 将此寄存器更改为当前寄存器的二进制补码，同时强制通过负载的电流为零，将校准芯片，从而消除当前 ADC 中的偏移误差。


## 4.6 "temperature"

打开并读取该文件会启动芯片的 CONVERT_T（温度转换）命令，然后从设备寄存器中读取温度并以 ASCII 十进制值的形式提供。

重要提示：返回值必须除以 256 才能得到实际温度（以摄氏度为单位）。


## 4.7 "vad", "vdd"

打开并读取该文件会启动芯片的CONVERT_V（电压转换）命令。

根据 sysfs 文件名，将选择 A/D 的不同输入：

vad:

&emsp;通用 A/D 输入 (VAD)

vdd:

&emsp;电池输入 (VDD)

电压转换后，该值以十进制 ASCII 形式返回。 注意：要得到伏特值，必须除以 100。


# 5. Kernel driver w1_ds28e04

支持芯片：

Maxim DS28E04-100 4096-Bit Addressable 1-Wire EEPROM with PIO

支持的家庭代码：

W1_FAMILY_DS28E04, 0x1C

作者： Markus Franke, <franke.m@sebakmt.com> <franm@hrz.tu-chemnitz.de>


## 5.1 Description

通过 sysfs 文件“eeprom”和“pio”提供支持。 可以通过设备属性“crccheck”选择启用/禁用内存访问期间的 CRC 检查。 可以通过模块参数“w1_strong_pullup”选择启用/禁用强上拉。

**Memory Access**

对“eeprom”文件的读取操作从 DS28E04 的 EEPROM 中读取给定数量的字节。

对“eeprom”文件的写操作将给定的字节序列写入DS28E04的EEPROM。 如果启用 CRC 检查模式，则仅允许写入具有有效 CRC16 值（字节 30 和 31）的完全对齐的 32 字节块。

**PIO Access**

DS28E04-100 的 2 个 PIO 可通过“pio”sysfs 文件访问。

PIO 的当前状态以 8 位值的形式返回。 位0/1代表PIO_0/PIO_1的状态。 位 2..7 不关心。 PIO 被驱动为低电平有效，即驱动器提供/期望低电平有效值。


# 6. Kernel driver w1_ds28e17

支持芯片：

Maxim DS28E17 1-Wire-to-I2C Master Bridge

支持的家庭代码：

W1_FAMILY_DS28E17, 0x19

作者： Jan Kandziora <jjj@gmx.de>


## 6.1 Description

DS28E17 是一个 Onewire 从设备，充当 I2C 总线主设备。

该驱动程序为检测到的任何 DS28E17 设备创建新的 I2C 总线。 I2C 总线随着 DS28E17 设备的出现和消失而出现和消失。 连接到 DS28E17 的 I2C 从设备可以由内核或用户空间工具访问，就像它们连接到“本机”I2C 总线主设备一样。

udev 规则如下：

```bash
SUBSYSTEM=="i2c-dev", KERNEL=="i2c-[0-9]*", ATTRS{name}=="w1-19-*", \
        SYMLINK+="i2c-$attr{name}"
```

可用于根据 DS28E17 芯片的唯一 id 创建稳定的 /dev/i2c- 条目。

驱动参数为：

**speed:**

这设置了 DS28E17 连接后立即配置的默认 I2C 速度。 DS28E17 的加电默认值为 400kBaud，但芯片可能会在 Onewire 总线上来来去去，而无需断电，并且一旦“w1_ds28e17”驱动程序注意到 Onewire 总线上有新连接或重新连接的 DS28E17 设备，它就会 重新应用此设置。

有效值为 100、400、900 [kBaud]。 任何其他值都意味着在检测时不考虑当前的 DS28E17 设置。 默认值为 100。

**stretch:**

这将设置用于新连接的 DS28E17 设备的默认拉伸值。 它是用于计算 I2C 传输繁忙等待时间的乘数。 这是为了考虑到大量使用 I2C 时钟拉伸功能的 I2C 从设备，因此无法正确预先计算所需的超时。 由于 w1_ds28e17 驱动程序在预先计算的等待时间后循环检查 DS28E17 的繁忙标志，因此几乎不需要调整此设置。

将其保留为 1，除非您在内核日志中收到 ETIMEDOUT 错误和“w1_slave_driver 19-00000002dbd8：繁忙超时”。

有效值为 1 到 9。默认值为 1。

驱动程序为每个设备创建 sysfs 文件 /sys/bus/w1/devices/19-<id>/speed 和 /sys/bus/w1/devices/19-<id>/stretch，并预加载驱动程序的默认设置 参数。 它们可能随时更改。 此外，还会为 I2C 总线主控 sysfs 结构创建目录 /sys/bus/w1/devices/19-<id>/i2c-<nnn>。

有关更多信息，请参阅 <https://github.com/ianka/w1_ds28e17>。


# 7. Kernel driver w1_therm

支持芯片：

Maxim ds18*20 基础温度传感器。

Maxim ds1825 基础温度传感器。

GXCAS GX20MH01 温度传感器。

Maxim MAX31850 热电偶接口。

作者： Evgeniy Polyakov <johnpol@2ka.mipt.ru>


## 7.1 Description

w1_therm为ds18*20、ds28ea00、GX20MH01和MAX31850器件提供基本温度转换。

支持的家庭代码：

|                   |      |
| ----------------- | ---- |
| W1_THERM_DS18S20  | 0x10 |
| W1_THERM_DS1822   | 0x22 |
| W1_THERM_DS18B20  | 0x28 |
| W1_THERM_DS1825   | 0x3B |
| W1_THERM_DS28EA00 | 0x42 |

通过 sysfs 条目 w1_slave 提供支持。 每个打开和读取序列都会启动温度转换，然后提供两行 ASCII 输出。 第一行包含读取的九个十六进制字节以及计算出的 crc 值以及 YES 或 NO（如果匹配）。 如果 crc 匹配，则保留返回值。 第二行显示 t= 后保留的值以及以毫摄氏度为单位的温度。

或者，可以使用温度 sysfs 读取温度，它仅返回以毫摄氏度为单位的温度。

总线上所有设备的批量读取可以通过将触发器写入 w1_bus_master 级别的 therm_bulk_read 条目来完成。 这会将转换命令发送到总线上的所有设备，如果在总线上检测到寄生供电设备（并且模块中启用了强上拉），它将在寄生供电设备所需的较长转换时间内将线路驱动为高电平 在线上。 如果没有待处理的批量转换，读取 therm_bulk_read 将返回 0；如果至少有一个传感器仍在转换中，则返回 -1；如果转换已完成但至少有一个传感器值尚未读取，则读取 therm_bulk_read 将返回 1。 然后通过读取每个设备的温度条目来访问结果温度，如果转换仍在进行中，则可能返回空。 请注意，如果发送批量读取但未立即读取一个传感器，则下次访问该设备上的温度将返回发出批量读取命令时测量的温度（而不是当前温度）。

如果需要，将在转换期间应用强上拉。

conv_time 用于获取当前转换时间（读取），并调整它（写入）。 温度转换时间取决于设备类型及其当前分辨率。 默认转换时间由驱动程序根据设备数据表设置。 许多原始设备克隆的转换时间与数据表规格不同。 有三种选择：1）通过向 conv_time 写入以毫秒为单位的值来手动设置正确的转换时间； 2) 通过向 conv_time 写入 1 自动测量并设置转换时间； 3) 使用轮询功能启用转换完成。 选项 2、3 不能在寄生电源模式下使用。 要恢复默认转换时间，请将 0 写入 conv_time。

将分辨率值（以位为单位）写入 w1_slave 将更改传感器下一次读数的精度。 允许的分辨率由传感器定义。 当传感器重新上电时，分辨率会重置。

要将当前分辨率存储在 EEPROM 中，请将 0 写入 w1_slave。 由于 EEPROM 的写入次数有限 (>50k)，因此应明智地使用此命令。

或者，如果传感器支持，可以使用每个设备上的专用分辨率条目读取或写入分辨率。

一些非正品 DS18B20 芯片仅固定为 12 位模式，因此实际分辨率是从芯片读回并验证的。

注意：更改分辨率会将转换时间恢复为默认值。

只写 sysfs 条目 eeprom_cmd 是 EEPROM 操作的替代方案。 写入保存以将器件 RAM 保存到 EEPROM。 写入恢复可恢复器件 RAM 中的 EEPROM 数据。

ext_power 条目允许检查每个设备的电源状态。 如果设备由寄生供电，则读取 0；如果设备由外部供电，则读取 1。

Sysfs 警报允许读取或写入 TH 和 TL（温度高和低）警报。 值应以空间分隔且位于设备范围内（典型为 -55 ℃ 至 125 ℃）。 值是整数，因为它们存储在设备的 8 位寄存器中。 最低值自动放入 TL。 设置后，可以在主级别搜索警报。

模块参数strong_pullup可以设置为0以禁用强上拉，1以启用自动检测或2以强制强上拉。 在自动检测的情况下，驱动程序将使用“READ POWER SUPPLY”命令来检查总线上是否有 pariste 供电的设备。 如果是这样，它将激活主控的强上拉。 如果使用此命令检测寄生设备失败（某些 DS18S20 似乎就是这种情况），可以强制启用强上拉。

如果启用强上拉，则在转换发生时将驱动主机的强上拉，前提是主驱动器确实支持强上拉（或者回落至上拉电阻）。 DS18b20 温度传感器规格列出了 1.5mA 的最大电流消耗，5k 上拉电阻是不够的。 强上拉旨在提供所需的额外电流。

DS28EA00 提供额外的两个引脚用于实现序列检测算法。 此功能允许您确定芯片在 1 线总线中的物理位置，而无需预先了解总线排序。 通过 sysfs w1_seq 提供支持。 该文件将包含一行，其中包含一个整数值，表示总线中从 0 开始的设备索引。

功能 sysfs 条目控制每个设备的可选驱动程序设置。 寄生模式功率不足、线路噪声和转换时间不足都可能导致转换失败。 原始 DS18B20 和一些克隆版本允许检测无效转换。 将位掩码 1 写入功能以启用检查转换是否成功。 如果转换后暂存器存储器的字节 6 为 0xC，并且温度读取为 85.00（上电值）或 127.94（电量不足），则驱动程序将返回转换错误。 位掩码 2 通过在转换开始后在总线上生成读取周期来启用轮询转换完成（仅限正常电源）。 在寄生电源模式下，此功能不可用。 特征位掩码可以被组合（OR）。 更多详细信息，请参阅 Documentation/ABI/testing/sysfs-driver-w1_therm。

GX20MH01 器件与 DS18*20 共享系列号 0x28。 该器件通常与 DS18B20 兼容。 在配置寄存器中添加了最低 2-5、2-6 温度位； 配置寄存器中的 R2 位启用 13 和 14 位分辨率。 该器件以 14 位分辨率模式启动。 数据表中指定的转换时间太短，必须增加。 该设备支持驱动程序功能 1 和 2。

MAX31850 器件与 DS1825 共享系列号 0x3B。 该器件通常与 DS1825 兼容。 Config 寄存器的高 4 位读全 1，表示 15，但器件始终工作在 14 位分辨率模式。
