
# I2C Bus Drivers

# 1. Kernel driver i2c-ali1535
--------

**支持的适配器：**

Acer Labs, Inc. ALI 1535 （南桥）

**数据表：**

现在处于 NDA 状态

<http://www.ali.com.tw/>


**作者：**

- Frodo Looijaard <frodol@dds.nl>,
- Philip Edelbrock <phil@netroedge.com>,
- Mark D. Studebaker <mdsxyz123@yahoo.com>,
- Dan Eaton <dan.eaton@rocketlogix.com>,
- Stephen Rousset<stephen.rousset@rocketlogix.com>


## 1.1 Description

这是 Acer Labs Inc. (ALI) M1535 南桥上的 SMB 主机控制器的驱动程序。

M1535 是便携式系统的南桥。 它与同样由 Acer Labs Inc. 生产的 M15x3 南桥非常相似。该部件中的一些寄存器已移动，有些已稍微重新定义。 此外，SMBus 事务的排序已被修改，以便与制造商推荐的并通过测试观察到的排序更加一致。 这些更改反映在该驱动程序中，并且可以通过将该驱动程序与 i2c-ali15x3 驱动程序进行比较来识别。 有关这些芯片的概述，请参阅 <http://www.acerlabs.com>

SMB 控制器是 M7101 设备的一部分，该设备是符合 ACPI 的电源管理单元 (PMU)。

必须启用整个 M7101 设备才能使 SMB 工作。 您不能仅单独启用 SMB。 SMB 和 ACPI 具有独立的 I/O 空间。 我们确保 SMB 已启用。 我们不去管 ACPI。


## 1.2 Features

该驱动程序仅控制 SMB 主机。 该驱动程序不使用中断。


# 2. Kernel driver i2c-ali1563
--------

**支持的适配器：**

Acer Labs, Inc. ALI 1563 （南桥）

**数据表：**

现在处于 NDA 状态

<http://www.ali.com.tw/>


**作者：**

- Patrick Mochel <mochel@digitalimplant.org>


## 2.1 Description

这是 Acer Labs Inc. (ALI) M1563 南桥上的 SMB 主机控制器的驱动程序。

有关这些芯片的概述，请参阅 <http://www.acerlabs.com>

M1563 南桥与 M1533 看似相似，但有一些值得注意的例外。 其中之一就是他们将 i2c 核心升级为 SMBus 2.0 兼容，并且恰好与 Intel 801 南桥中的 i2c 控制器几乎相同。


## 2.2 Features

该驱动程序仅控制 SMB 主机。 该驱动程序不使用中断。


# 3. Kernel driver i2c-ali15x3
--------

**支持的适配器：**

Acer Labs, Inc. ALI 1533 and 1543C （南桥）

**数据表：**

现在处于 NDA 状态

<http://www.ali.com.tw/>


**作者：**

- Frodo Looijaard <frodol@dds.nl>,
- Philip Edelbrock <phil@netroedge.com>,
- Mark D. Studebaker <mdsxyz123@yahoo.com>


## 3.1 Module Parameters

- force_addr: int

&emsp;初始化i2c控制器的基地址


**Notes**

force_addr 参数对于不在 BIOS 中设置地址的主板很有用。 不做 PCI 强制； 该设备必须仍然存在于 lspci 中。 除非驱动程序抱怨未设置基地址，否则不要使用此选项。

示例:

```bash
modprobe i2c-ali15x3 force_addr=0xe800
```

华硕 P5A 主板上的 SMBus 会定期挂起，并且只能通过电源循环清除。 原因未知（请参阅下面的问题）。


## 3.2 Description

这是 Acer Labs Inc. (ALI) M1541 和 M1543C 南桥上 SMB 主机控制器的驱动程序。

M1543C 是桌面系统的南桥。

M1541 是便携式系统的南桥。

它们是以下 ALI 芯片组的一部分：

- “Aladdin Pro 2”包括带有 AGP 和 100MHz CPU 前端总线的 M1621 Slot 1 北桥

- “Aladdin V”包括带有 AGP 和 100MHz CPU 前端总线的 M1541 Socket 7 北桥

    &emsp;一些阿拉丁V主板：

    - Asus P5A
    - Atrend ATC-5220
    - BCM/GVC VP1541
    - Biostar M5ALA
    - Gigabyte GA-5AX (通常不起作用，因为 BIOS 没有启用 7101 设备！)
    - Iwill XA100 Plus
    - Micronics C200
    - Microstar (MSI) MS-5169

- “Aladdin IV”包括 M1541 Socket 7 北桥，主机总线高达 83.3 MHz。

有关这些芯片的概述，请参见 <http://www.acerlabs.com>。 目前，网站上的完整数据表受密码保护，但如果您联系位于圣何塞的 ALI 办事处，他们可能会给您密码。

M1533/M1543C 设备在 PCI 总线上显示为四个独立的设备。 lspci 的输出将显示类似于以下内容的内容：

```bash
00:02.0 USB Controller: Acer Laboratories Inc. M5237 (rev 03)
00:03.0 Bridge: Acer Laboratories Inc. M7101      <= THIS IS THE ONE WE NEED
00:07.0 ISA bridge: Acer Laboratories Inc. M1533 (rev c3)
00:0f.0 IDE interface: Acer Laboratories Inc. M5229 (rev c1)
```


## 3.3 Important

如果板上有 M1533 或 M1543C，并且您收到“ali15x3：错误：无法检测 ali15x3！” 然后运行lspci。

如果您看到 1533 和 5229 设备，但没有看到 7101 设备，则必须在 BIOS 中启用 ACPI、PMU、SMB 或类似功能。

如果找不到 M7101 设备，驱动程序将无法工作。

SMB 控制器是 M7101 设备的一部分，该设备是符合 ACPI 的电源管理单元 (PMU)。

必须启用整个 M7101 设备才能使 SMB 工作。 您不能仅单独启用 SMB。 SMB 和 ACPI 具有独立的 I/O 空间。 我们确保 SMB 已启用。 我们不去管 ACPI。


## 3.4 Features

该驱动程序仅控制 SMB 主机。 M15X3 上的 SMB 从站控制器未启用。 该驱动程序不使用中断。


## 3.5 Issues

该驱动程序仅请求 SMB 寄存器的 I/O 空间。 它不使用 ACPI 区域。

在华硕 P5A 主板上，有多个报告称 SMBus 将挂起，只能通过关闭计算机电源来解决。 当主板变热时，例如在 CPU 负载较重或在夏天时，情况似乎会更糟。 该板上可能存在电气问题。 在 P5A 上，W83781D 传感器芯片位于 ISA 和 SMBus 上。 因此，通常可以通过仅访问 ISA 总线上的 W83781D 来避免 SMBus 挂起。


# 4. Kernel driver i2c-amd756
--------

**支持的适配器：**

- AMD 756
- AMD 766
- AMD 768
- AMD 8111

**数据表：**

可在 AMD 网站上公开获取

nVidia nForce

**数据表：**

不可用

**作者：**

- Frodo Looijaard <frodol@dds.nl>,
- Philip Edelbrock <phil@netroedge.com>


## 4.1 Description

该驱动程序支持 AMD 756、766、768 和 8111 外设总线控制器以及 nVidia nForce。

请注意，对于 8111，有两个 SMBus 适配器。 该驱动程序支持 SMBus 1.0 适配器，i2c-amd8111 驱动程序支持 SMBus 2.0 适配器。


# 5. Kernel driver i2c-adm8111
--------

**支持的适配器：**

AMD-8111 SMBus 2.0 PCI interface

**数据表：**

AMD 数据表尚未提供，但几乎所有内容都可以在适配器遵循的公开 ACPI 2.0 规范中找到。

**作者：**

Vojtech Pavlik <vojtech@suse.cz>


## 5.1 Description

如果你看到这样的东西：

```bash
00:07.2 SMBus: Advanced Micro Devices [AMD] AMD-8111 SMBus 2.0 (rev 02)
        Subsystem: Advanced Micro Devices [AMD] AMD-8111 SMBus 2.0
        Flags: medium devsel, IRQ 19
        I/O ports at d400 [size=32]
```

在您的 lspci -v 中，则此驱动程序适用于您的芯片组。


## 5.2 Process Call Support

支持。


## 5.3 SMBus 2.0 Support

支持的。 实现了 PEC 和块进程调用支持。 从机模式或主机通知尚未实现。

**Notes**

请注意，对于 8111，有两个 SMBus 适配器。 该驱动程序支持 SMBus 2.0 适配器，i2c-amd756 驱动程序支持 SMBus 1.0 适配器。


# 6. Kernel driver i2c-amd-mp2
--------

**支持的适配器：**

AMD MP2 PCIe interface

**数据表：**

不公开。

**作者：**

- Shyam Sundar S K <Shyam-sundar.S-k@amd.com>
- Nehal Shah <nehal-bakulchandra.shah@amd.com>
- Elie Morisse <syniurge@gmail.com>


## 6.1 Description

MP2 是一个 ARM 处理器，编程为 I2C 控制器，并通过 PCI 与 x86 主机通信。

如果你看到这样的东西：

```bash
03:00.7 MP2 I2C controller: Advanced Micro Devices, Inc. [AMD] Device 15e6
```

在您的 lspci -v 中，则此驱动程序适用于您的设备。


# 7. Kernel driver i2c-diolan-u2c
--------

**支持的适配器：**

Diolan U2C-12 I2C-USB adapter

**Documentation:**

<http://www.diolan.com/i2c/u2c12.html>

**作者：**

Guenter Roeck <linux@roeck-us.net>


## 7.1 Description

这是 Diolan U2C-12 USB-I2C 适配器的驱动程序。

Diolan U2C-12 I2C-USB 适配器提供了一种低成本解决方案，可使用 USB 接口将计算机连接到 I2C 从设备。 它还支持与 SPI 设备的连接。

该驱动仅支持U2C-12的I2C接口。 驱动程序不使用中断。


## 7.2 Module parameters

- 频率: I2C bus 频率


# 8. Kernel driver i2c-i801
--------

**支持的适配器：**

- Intel 82801AA and 82801AB (ICH 和 ICH0 -“810”和“810E”芯片组的一部分)
- Intel 82801BA (ICH2 -“815E”芯片组的一部分)
- Intel 82801CA/CAM (ICH3)
- Intel 82801DB (ICH4) (HW PEC 支持)
- Intel 82801EB/ER (ICH5) (HW PEC 支持)
- Intel 6300ESB
- Intel 82801FB/FR/FW/FRW (ICH6)
- Intel 82801G (ICH7)
- Intel 631xESB/632xESB (ESB2)
- Intel 82801H (ICH8)
- Intel 82801I (ICH9)
- Intel EP80579 (Tolapai)
- Intel 82801JI (ICH10)
- Intel 5/3400 Series (PCH)
- Intel 6 Series (PCH)
- Intel Patsburg (PCH)
- Intel DH89xxCC (PCH)
- Intel Panther Point (PCH)
- Intel Lynx Point (PCH)
- Intel Avoton (SOC)
- Intel Wellsburg (PCH)
- Intel Coleto Creek (PCH)
- Intel Wildcat Point (PCH)
- Intel BayTrail (SOC)
- Intel Braswell (SOC)
- Intel Sunrise Point (PCH)
- Intel Kaby Lake (PCH)
- Intel DNV (SOC)
- Intel Broxton (SOC)
- Intel Lewisburg (PCH)
- Intel Gemini Lake (SOC)
- Intel Cannon Lake (PCH)
- Intel Cedar Fork (PCH)
- Intel Ice Lake (PCH)
- Intel Comet Lake (PCH)
- Intel Elkhart Lake (PCH)
- Intel Tiger Lake (PCH)
- Intel Jasper Lake (SOC)
- Intel Emmitsburg (PCH)
- Intel Alder Lake (PCH)
- Intel Raptor Lake (PCH)
- Intel Meteor Lake (SOC 和 PCH)
- Intel Birch Stream (SOC)

**数据表：**

可在英特尔网站上公开获取

在 Intel Patsburg 及更高版本的芯片组上，支持普通主机 SMBus 控制器和附加“集成设备功能”控制器。

**作者：**

- Mark Studebaker <mdsxyz123@yahoo.com>
- Jean Delvare <jdelvare@suse.de>


## 8.1 Module Parameters

- disable_features (位向量)

禁用设备通常支持的选定功能。 如果相关功能因任何原因无法按预期工作，这使得解决可能的驱动程序或硬件错误成为可能。 位值：

|      |                    |
| ---- | ------------------ |
| 0x01 | 禁用 SMBus PEC     |
| 0x02 | 禁用块缓冲区        |
| 0x08 | 禁用 I2C 块读取功能 |
| 0x10 | 不要使用中断        |
| 0x20 | 禁用 SMBus 主机通知 |


## 8.2 Description

ICH（正确称为 82801AA）、ICH0 (82801AB)、ICH2 (82801BA)、ICH3 (82801CA/CAM) 和更高版本的设备 (PCH) 是 Intel 芯片，是基于 Celeron 的 PC 的 Intel '810' 芯片组的一部分 、用于基于 Pentium 的 PC 的“810E”芯片组、“815E”芯片组等。

ICH 芯片在两个逻辑 PCI 设备中至少包含七个独立的 PCI 功能。 lspci 的输出将显示类似于以下内容的内容：

```bash
00:1e.0 PCI bridge: Intel Corporation: Unknown device 2418 (rev 01)
00:1f.0 ISA bridge: Intel Corporation: Unknown device 2410 (rev 01)
00:1f.1 IDE interface: Intel Corporation: Unknown device 2411 (rev 01)
00:1f.2 USB Controller: Intel Corporation: Unknown device 2412 (rev 01)
00:1f.3 Unknown class [0c05]: Intel Corporation: Unknown device 2413 (rev 01)
```

SMBus 控制器是设备 1f 中的功能 3。 0c05 类是 SMBus 串行控制器。

ICH 芯片与英特尔的 PIIX4 芯片非常相似，至少在 SMBus 控制器方面如此。


## 8.3 Process Call Support

82801EB (ICH5) 及更高版本的芯片支持块进程调用。


## 8.4 I2C Block Read Support

82801EB (ICH5) 及更高版本的芯片支持 I2C 块读取。


## 8.5 SMBus 2.0 Support

82801DB (ICH4) 及更高版本的芯片支持多种 SMBus 2.0 功能。


## 8.6 Interrupt Support

82801EB (ICH5) 及更高版本的芯片支持 PCI 中断支持。


## 8.7 Hidden ICH SMBus

如果您的系统有一个 Intel ICH 南桥，但您在 lspci 中没有在 00:1f.3 处看到 SMBus 设备，并且您无法在 BIOS 中找到任何方式来启用它，这意味着它已被隐藏 BIOS 代码。 众所周知，华硕首先在其 P4B 主板上实现了这一点，之后又在许多其他主板上实现了这一点。 一些供应商的机器也会受到影响。

首先要尝试的是“i2c-scmi”ACPI 驱动程序。 SMBus 可能是故意隐藏的，因为它将由 ACPI 驱动。 如果 i2c-scmi 驱动程序适合您，请忘记 i2c-i801 驱动程序，并且不要尝试取消隐藏 ICH SMBus。 即使 i2c-scmi 不起作用，您最好确保 ACPI 代码不使用 SMBus。 尝试加载“风扇”和“热”驱动程序，并检查/sys/class/ Thermal。 如果您发现类型为“acpitz”的热区域，则 ACPI 可能正在访问 SMBus，并且最好不要取消隐藏它。 只有当您确定 ACPI 没有使用 SMBus 时，您才可以尝试取消隐藏它。

为了取消隐藏 SMBus，我们需要在内核枚举 PCI 设备之前更改 PCI 寄存器的值。 这是在 drivers/pci/quirks.c 中完成的，其中必须列出所有受影响的板（请参阅函数 asus_hides_smbus_hostbridge。）如果 SMBus 设备丢失，并且您认为 SMBus 上有一些有趣的东西（例如硬件监控芯片），则您可以 需要将您的主板添加到列表中。

使用主桥 PCI 设备的子供应商和子设备 ID 来识别主板。 使用 lspci -n -v -s 00:00.0 获取：

```bash
00:00.0 Class 0600: 8086:2570 (rev 02)
        Subsystem: 1043:80f2
        Flags: bus master, fast devsel, latency 0
        Memory at fc000000 (32-bit, prefetchable) [size=32M]
        Capabilities: [e4] #09 [2106]
        Capabilities: [a0] AGP version 3.0
```

这里主桥 ID 是 2570 (82865G/PE/P)，子供应商 ID 是 1043 (Asus)，子设备 ID 是 80f2 (P4P800-X)。 您可以在 include/linux/pci_ids.h 中找到桥 ID 和子供应商 ID 的符号名称，然后在 drivers/pci/quirks.c 中的正确位置添加子设备 ID 的大小写。 然后请对其进行良好的测试，以确保未隐藏的 SMBus 不会与例如 SMBus 发生冲突。 ACPI。

如果它有效，证明有用（即 SMBus 上有可用的芯片）并且看起来安全，请提交一个补丁以包含到内核中。

注意：lm_sensors 2.10.2 及更高版本中有一个有用的脚本，名为 unhide_ICH_SMBus（在 prog/hotplug 中），它使用 fakephp 驱动程序暂时取消隐藏 SMBus，而无需修补和重新编译内核。 如果您只想检查隐藏的 ICH SMBus 上是否有任何有趣的内容，这是非常方便的。

lm_sensors 项目衷心感谢德州仪器 (TI) 在此驱动程序的初始开发过程中提供的支持。

lm_sensors 项目衷心感谢英特尔对该驱动程序的 SMBus 2.0 / ICH4 功能开发的支持。


# 9. Kernel driver i2c-ismt
--------

**支持的适配器：**

Intel S12xx series SOCs

**作者：**

Bill Brown <bill.e.brown@intel.com>


## 9.1 Module Parameters

- bus_speed (unsigned int)

允许更改总线速度。 通常，总线速度由 BIOS 设置，无需更改。 然而，某些 SMBus 分析仪在调试期间监控总线速度太慢，因此需要此模块参数。 指定总线速度（以 kHz 为单位）。

可用的总线频率设置：

|      |     |
| ---- | --- |
| 0    | no change |
| 80   | kHz |
| 100  | kHz |
| 400  | kHz |
| 1000 | kHz |


## 9.2 Description

S12xx 系列 SOC 有一对集成的 SMBus 2.0 控制器，主要针对微服务器和存储市场。

S12xx 系列包含一对 PCI 功能。 lspci 的输出将显示类似于以下内容的内容：

```bash
00:13.0 System peripheral: Intel Corporation Centerton SMBus 2.0 Controller 0
00:13.1 System peripheral: Intel Corporation Centerton SMBus 2.0 Controller 1
```


# 10. Driver i2c-mlxcpld
--------

**作者：**

Michael Shych <michaelsh@mellanox.com>

这是 Mellanox I2C 控制器逻辑，在莱迪思 CPLD 器件中实现。

**Device supports:**

- Master mode.
- One physical bus.
- Polling mode.

该控制器配备在下一个 Mellanox 系统中：“msx6710”、“msx6720”、“msb7700”、“msn2700”、“msx1410”、“msn2410”、“msb7800”、“msn2740”、“msn2100”。

支持以下交易类型：

- Receive Byte/Block.
- Send Byte/Block.
- Read Byte/Block.
- Write Byte/Block.

寄存器:

|          |     |                                                                                             |
| -------- | --- | ------------------------------------------------------------------------------------------- |
| CPBLTY   | 0x0 | 能力注册。 位 [6:5] - 事务长度。 支持 b01 - 72B，其他情况下支持 36B。 位 7 - SMBus 块读取支持。  |
| CTRL     | 0x1 | 控制寄存器。 重置所有寄存器。                                                                 |
| HALF_CYC | 0x4 | 循环注册。 配置I2C SCL半时钟周期的宽度（以4个LPC_CLK为单位）。                                  |
| I2C_HOLD | 0x5 | 保留注册。 OE（输出使能）延迟了该寄存器设置的值（以 LPC_CLK 为单位）                             |
| CMD      |     | 0x6 - 命令寄存器。 位 0，0 = 写入，1 = 读取。 位 [7:1] - I2C 设备的 7 位地址。 它应该最后写入，因为它会触发 I2C 事务。 |
| NUM_DATA | 0x7 | 数据大小寄存器。 读事务中要写入的数据字节数                                                    |
| NUM_ADDR | 0x8 | 地址寄存器。 读事务中要写入的地址字节数。                                                      |
| STATUS   | 0x9 | 状态寄存器。 位 0 - 交易完成。 位 4 - ACK/NACK。                                              |
| DATAx    | 0xa | 0x54 - 68 字节数据缓冲区寄存器。 对于写事务，地址在前四个字节 (DATA1 - DATA4) 中指定，数据从 DATA4 开始。 对于读取事务，地址在单独的事务中发送，并在前四个字节 (DATA0 - DATA3) 中指定。 数据从DATA0 开始读取。 |


# 11. Kernel driver i2c-nforce2
--------

**支持的适配器：**

- nForce2 MCP 10de:0064
- nForce2 Ultra 400 MCP 10de:0084
- nForce3 Pro150 MCP 10de:00D4
- nForce3 250Gb MCP 10de:00E4
- nForce4 MCP 10de:0052
- nForce4 MCP-04 10de:0034
- nForce MCP51 10de:0264
- nForce MCP55 10de:0368
- nForce MCP61 10de:03EB
- nForce MCP65 10de:0446
- nForce MCP67 10de:0542
- nForce MCP73 10de:07D8
- nForce MCP78S 10de:0752
- nForce MCP79 10de:0AA2

**数据表：**

未公开提供，但似乎与 AMD-8111 SMBus 2.0 适配器类似。

**作者：**

- Hans-Frieder Vogt <hfvogt@gmx.net>,
- Thomas Leibold <thomas@plx.com>,
- Patrick Dreker <patrick@dreker.de>


## 11.1 Description

i2c-nforce2 是 nVidia nForce2 MCP 中包含的 SMBuse 驱动程序。

如果您的 lspci -v 列表显示类似以下内容：

```bash
00:01.1 SMBus: nVidia Corporation: Unknown device 0064 (rev a2)
        Subsystem: Asustek Computer, Inc.: Unknown device 0c11
        Flags: 66Mhz, fast devsel, IRQ 5
        I/O ports at c000 [size=32]
        Capabilities: <available only to root>
```

那么这个驱动程序应该支持您主板的 SMBuse。

**Notes**

nForce2芯片组中的SMBus适配器似乎与AMD-8111南桥中的SMBus 2.0适配器非常相似。 然而，我只能让驱动程序使用直接 I/O 访问，这与 AMD-8111 的 EC 接口不同。 在华硕 A7N8X 上测试。 Asus A7N8X 的 ACPI DSDT 表列出了两个 SMBuse，该驱动程序均支持这两个 SMBuse。


# 12. Kernel driver i2c-nvidia-gpu
--------

**数据表：**

不公开。

**作者：**

Ajay Gupta <ajayg@nvidia.com>


## 12.1 Description

i2c-nvidia-gpu 是 NVIDIA Turing 及更高版本 GPU 中包含的 I2C 控制器的驱动程序，用于与 GPU 上的 Type-C 控制器进行通信。

如果您的 lspci -v 列表显示类似以下内容：

```bash
01:00.3 Serial bus controller [0c80]: NVIDIA Corporation Device 1ad9 (rev a1)
```

那么这个驱动程序应该支持你的 GPU 的 I2C 控制器。


# 13. Kernel driver i2c-ocores
--------

**支持的适配器：**

OpenCores.org I2C 控制器，作者：Richard Herveille（请参阅数据表链接）<https://opencores.org/project/i2c/overview>

**作者：**

Peter Korsgaard <peter@korsgaard.com>


## 13.1 Description

i2c-ocores 是 Richard Herveille 为 OpenCores.org I2C 控制器 IP 核设计的 i2c 总线驱动程序。


## 13.2 Usage

i2c-ocores使用平台总线，因此您需要提供一个带有基地址和中断号的struct platform_device。 设备的 dev.platform_data 还应该指向 struct ocores_i2c_platform_data （请参阅 linux/platform_data/i2c-ocores.h），描述寄存器之间的距离和输入时钟速度。 还可以附加 i2c_board_info 列表，i2c-ocores 驱动程序将在创建时将其添加到总线。

例如，就像是：

```c
static struct resource ocores_resources[] = {
      [0] = {
              .start  = MYI2C_BASEADDR,
              .end    = MYI2C_BASEADDR + 8,
              .flags  = IORESOURCE_MEM,
      },
      [1] = {
              .start  = MYI2C_IRQ,
              .end    = MYI2C_IRQ,
              .flags  = IORESOURCE_IRQ,
      },
};

/* optional board info */
struct i2c_board_info ocores_i2c_board_info[] = {
      {
              I2C_BOARD_INFO("tsc2003", 0x48),
              .platform_data = &tsc2003_platform_data,
              .irq = TSC_IRQ
      },
      {
              I2C_BOARD_INFO("adv7180", 0x42 >> 1),
              .irq = ADV_IRQ
      }
};

static struct ocores_i2c_platform_data myi2c_data = {
      .regstep        = 2,            /* two bytes between registers */
      .clock_khz      = 50000,        /* input clock of 50MHz */
      .devices        = ocores_i2c_board_info, /* optional table of devices */
      .num_devices    = ARRAY_SIZE(ocores_i2c_board_info), /* table size */
};

static struct platform_device myi2c = {
      .name                   = "ocores-i2c",
      .dev = {
              .platform_data  = &myi2c_data,
      },
      .num_resources          = ARRAY_SIZE(ocores_resources),
      .resource               = ocores_resources,
};
```


# 14. Kernel driver i2c-parport
--------

**作者：**

Jean Delvare <jdelvare@suse.de>

这是多个 i2c-over-parallel-port 适配器的统一驱动程序，例如 Philips、Velleman 或 ELV 制造的适配器。 该驱动程序旨在替代旧的单个驱动程序：

- i2c-philips-par
- i2c-elv
- i2c-velleman
- video/i2c-parport (与此不同，专用于自制图文电视适配器)

目前它支持以下设备：

- (type=0) Philips adapter
- (type=1) home brew teletext adapter
- (type=2) Velleman K8000 adapter
- (type=3) ELV adapter
- (type=4) Analog Devices ADM1032 evaluation board
- (type=5) Analog Devices evaluation boards: ADM1025, ADM1030, ADM1031
- (type=6) Barco LPT->DVI (K5800236) adapter
- (type=7) One For All JP1 parallel port adapter
- (type=8) VCT-jig

这些设备使用不同的引脚配置，因此您必须使用类型模块参数告诉驱动程序您拥有什么。 无法自动检测设备。 需要时可以轻松添加对不同引脚配置的支持。

早期内核默认为 type=0 (Philips)。 但现在，如果缺少类型参数，驱动程序将无法初始化。

将该线路正确连接到并行端口中断引脚的适配器可提供 SMBus 警报支持。


## 14.1 Building your own adapter

如果您想构建自己的 i2c-over-parallel-port 适配器，这里有一个电子架构示例（版权归 Sylvain Munaut 所有）：

```bash
Device                                                      PC
Side          ___________________Vdd (+)                    Side
               |    |         |
              ---  ---       ---
              | |  | |       | |
              |R|  |R|       |R|
              | |  | |       | |
              ---  ---       ---
               |    |         |
               |    |    /|   |
SCL  ----------x--------o |-----------x-------------------  pin 2
                    |    \|   |       |
                    |         |       |
                    |   |\    |       |
SDA  ----------x----x---| o---x---------------------------  pin 13
               |        |/            |
               |                      |
               |         /|           |
               ---------o |----------------x--------------  pin 3
                         \|           |    |
                                      |    |
                                     ---  ---
                                     | |  | |
                                     |R|  |R|
                                     | |  | |
                                     ---  ---
                                      |    |
                                     ###  ###
                                     GND  GND
```

**Remarks:**

- 这是 Analog Devices 评估板上使用的确切引脚排列和电子器件。

- 所有逆变器：

```bash
  /|
-o |-
  \|
```

&emsp;必须是74HC05，必须是集电极开路输出。

- 所有电阻均为10k。

- 并行端口的引脚 18-25 连接到 GND。

- 引脚 4-9 (D2-D7) 可以用作 VDD，驱动器将它们驱动为高电平。 ADM1032评估板使用D4-D7。 请注意，可以从并行端口汲取的电流量是有限的。 另请注意，所有连接的线路必须在同一状态下驱动，否则您将导致输出缓冲器短路！ 因此，在加载 i2c-parport 模块后插入 I2C 适配器可能是一个很好的安全措施，因为初始化之前的数据线状态可能未知。

- 这是5V！

- 显然你无法读取 SCL（因此它并不真正符合标准）。 添加起来非常简单，只需复制 SDA 部分并使用另一个输入引脚即可。 这将给出（ELV 兼容引脚排列）：

```bash
Device                                                      PC
Side          ______________________________Vdd (+)         Side
               |    |            |    |
              ---  ---          ---  ---
              | |  | |          | |  | |
              |R|  |R|          |R|  |R|
              | |  | |          | |  | |
              ---  ---          ---  ---
               |    |            |    |
               |    |      |\    |    |
SCL  ----------x--------x--| o---x------------------------  pin 15
                    |   |  |/         |
                    |   |             |
                    |   |   /|        |
                    |   ---o |-------------x--------------  pin 2
                    |       \|        |    |
                    |                 |    |
                    |                 |    |
                    |      |\         |    |
SDA  ---------------x---x--| o--------x-------------------  pin 10
                        |  |/              |
                        |                  |
                        |   /|             |
                        ---o |------------------x---------  pin 3
                            \|             |    |
                                           |    |
                                          ---  ---
                                          | |  | |
                                          |R|  |R|
                                          | |  | |
                                          ---  ---
                                           |    |
                                          ###  ###
                                          GND  GND
```

如果可能，您应该使用与现有适配器相同的引脚配置，这样您甚至不必更改代码。


## 14.2 Similar (but different) drivers

该驱动程序与 i2c 包中的 i2c-pport 驱动程序不同。 i2c-pport 驱动程序利用现代并行端口功能，因此您不需要额外的电子设备。 但它还有其他限制，并且尚未移植到 Linux 2.6。

该驱动程序也与 lm_sensors 包中的 i2c-pcf-epp 驱动程序不同。 i2c-pcf-epp 驱动程序不直接使用并行端口作为 I2C 总线。 相反，它使用它来控制外部 I2C 总线主机。 该驱动程序也尚未移植到 Linux 2.6。


## 14.3 Legacy documentation for Velleman adapter

有用的链接：

- Velleman <http://www.velleman.be/>
- Velleman K8000 Howto <http://howto.htlw16.ac.at/k8000-howto.html>

该项目为 Velleman K8000 和 K8005 提供了新的库：

&emsp;LIBK8000 v1.99.1 和 LIBK8005 v0.21

有了这些库，您可以使用原始 Velleman 软件中的简单命令来控制 K8000 接口卡和 K8005 步进电机卡，例如 SetIOchannel、ReadADchannel、SendStepCCWFull 等，使用 /dev/velleman。

- <http://home.wanadoo.nl/hihihi/libk8000.htm>
- <http://home.wanadoo.nl/hihihi/libk8005.htm>
- <http://struyve.mine.nu:8080/index.php?block=k8000>
- <http://sourceforge.net/projects/libk8005/>


## 14.4 One For All JP1 parallel port adapter

JP1 项目围绕一组远程控制展开，这些远程控制通过电池仓中的 6 针跳线暴露其内部配置 EEPROM 所依赖的 I2C 总线。 更多详情请参见：

<http://www.hifi-remote.com/jp1/>

简单并行端口硬件的详细信息可以在以下位置找到：

<http://www.hifi-remote.com/jp1/hardware.shtml>


# 15. Kernel driver i2c-pca-isa
--------

**支持的适配器：**

该驱动程序支持使用 Philips PCA 9564 并行总线到 I2C 总线控制器的 ISA 板

**作者：**

Ian Campbell <icampbell@arcom.com>, Arcom Control Systems


## 15.1 Module Parameters

- base int

&emsp;I/O 基地址

- irq int

&emsp;IRQ 中断

- clock int

&emsp;时钟速率如 PCA9564 数据表表 1 中所述


## 15.2 Description

该驱动程序支持使用 Philips PCA 9564 并行总线到 I2C 总线控制器的 ISA 板


# 16. Kernel driver i2c-piix4
--------

**支持的适配器：**

- Intel 82371AB PIIX4 和 PIIX4E
- Intel 82443MX (440MX) 数据表: 可在英特尔网站上公开获取
- ServerWorks OSB4, CSB5, CSB6, HT-1000 and HT-1100 南桥数据表：只能通过 ServerWorks 的 NDA 获得
- ATI IXP200, IXP300, IXP400, SB600, SB700 和 SB800 南桥数据表：未公开提供的 SB700 寄存器参考可从以下位置获取： <http://support.amd.com/us/Embedded_TechDocs/43009_sb7xx_rrg_pub_1.00.pdf>
- AMD SP5100 (在某些服务器主板上发现了 SB700 衍生品) 数据表: 可在 AMD 网站上公开获取 <http://support.amd.com/us/Embedded_TechDocs/44413.pdf>
- AMD Hudson-2, ML, CZ 数据表: 不公开
- Hygon CZ 数据表: 不公开
- Standard Microsystems (SMSC) SLC90E66 (Victory66) 南桥数据表：可在 SMSC 网站上公开获取 <http://www.smsc.com>

**作者：**

- Frodo Looijaard <frodol@dds.nl>
- Philip Edelbrock <phil@netroedge.com>


## 16.1 Module Parameters

- force: int 强制启用 PIIX4。 危险的！
- force_addr: int 强制启用给定地址处的 PIIX4。 极其危险！


## 16.2 Description

PIIX4（正式名称为 82371AB）是一款具有多种功能的 Intel 芯片。 除此之外，它还实现了 PCI 总线。 它的次要功能之一是实现系统管理总线。 这是真正的 SMBus - 您无法在 I2C 级别上访问它。 好消息是它本身理解 SMBus 命令，您不必担心时序问题。 坏消息是连接到它的非 SMBus 设备可能会严重混淆它。 是的，已知会发生这种情况......

执行 lspci -v 并查看它是否包含如下条目：

```bash
0000:00:02.3 Bridge: Intel Corp. 82371AB/EB/MB PIIX4 ACPI (rev 02)
             Flags: medium devsel, IRQ 9
```

总线和设备编号可能不同，但功能编号必须相同（与许多 PCI 设备一样，PIIX4 包含许多不同的“功能”，可以将其视为单独的设备）。 如果您找到这样的条目，则您有一个 PIIX4 SMBus 控制器。

在某些计算机（尤其是某些戴尔计算机）上，默认情况下禁用 SMBus。 如果您使用insmod参数'force=1'，内核模块将尝试启用它。 这非常危险！ 如果 BIOS 没有为此模块设置正确的地址，您可能会遇到大麻烦（例如：崩溃、数据损坏等）。 仅将此作为最后的手段（例如，首先尝试 BIOS 更新），并首先备份！ 更危险的选项是“force_addr=<IOPORT>”。 这不仅会像“force”那样启用 PIIX4，而且还会设置新的基本 I/O 端口地址。 PIIX4 的 SMBus 部分需要 8 个这样的地址才能正常工作。 如果这些地址已经被其他设备保留，你就会遇到大麻烦！ 如果您不太确定自己在做什么，请不要使用此功能！

PIIX4E只是PIIX4的新版本； 它也受到支持。 PIIX/PIIX3 不实现 SMBus 或 I2C 总线，因此您无法在这些主板上使用此驱动程序。

ServerWorks 南桥、Intel 440MX 和 Victory66 在 I2C/SMBus 支持方面与 PIIX4 相同。

AMD SB700、SB800、SP5100 和 Hudson-2 芯片组实现了两个 PIIX4 兼容的 SMBus 控制器。 如果您的 BIOS 初始化辅助控制器，则该驱动程序会将其检测为“辅助 SMBus 主机控制器”。

如果您拥有 Force CPCI735 主板或其他基于 OSB4 的系统，您可能需要更改 SMBus 中断选择寄存器，以便 SMBus 控制器使用 SMI 模式。

1. 使用 lspci 命令并通过 SMBus 控制器找到 PCI 设备：00:0f.0 ISA 桥：ServerWorks OSB4 南桥（rev 4f） 该行可能因不同芯片组而异。 请查阅驱动程序源以了解所有可能的 PCI id（以及 lspci -n 来匹配它们）。 假设设备位于 00:0f.0。

2. 现在您只需更改 0xD2 寄存器中的值即可。 首先使用命令获取： lspci -xxx -s 00:0f.0 如果值为 0x3 则需要将其更改为 0x1： setpci -s 00:0f.0 d2.b=1

请注意，您不需要在所有情况下都这样做，只有当 SMBus 无法正常工作时才需要这样做。


## 16.3 Hardware-specific issues

该驱动程序将拒绝在具有 Intel PIIX4 SMBus 的 IBM 系统上加载。 其中一些机器具有连接到 SMBus 的 RFID EEPROM (24RF08)，该 EEPROM 很容易因状态机错误而损坏。 这些大多是 Thinkpad 笔记本电脑，但台式机系统也可能受到影响。 我们没有所有受影响系统的列表，因此唯一安全的解决方案是阻止访问所有 IBM 系统上的 SMBus（使用 DMI 数据检测到）。


# 17. Kernel driver i2c-sis5595
--------

**作者：**

- Frodo Looijaard <frodol@dds.nl>,
- Mark D. Studebaker <mdsxyz123@yahoo.com>,
- Philip Edelbrock <phil@netroedge.com>

**支持的适配器：**

Silicon Integrated Systems Corp. SiS5595 南桥数据表：可在 Silicon Integrated Systems Corp. 网站上公开获取。

注：均有mfr。 ID 0x1039。

| SUPPORTED | PCI ID |
| --------- | ------ |
| 5595      | 0008   |

注意：这些芯片包含与 5595 不兼容的 0008 设备。我们通过列出的“黑名单”PCI ID 的存在来识别这些设备并拒绝加载。

| NOT SUPPORTED | PCI ID | BLACKLIST PCI ID |
| ------------- | ------ | ---------------- |
| 540           | 0008   | 0540             |
| 550           | 0008   | 0550             |
| 5513          | 0008   | 5511             |
| 5581          | 0008   | 5597             |
| 5582          | 0008   | 5597             |
| 5597          | 0008   | 5597             |
| 5598          | 0008   | 5597/5598        |
| 630           | 0008   | 0630             |
| 645           | 0008   | 0645             |
| 646           | 0008   | 0646             |
| 648           | 0008   | 0648             |
| 650           | 0008   | 0650             |
| 651           | 0008   | 0651             |
| 730           | 0008   | 0730             |
| 735           | 0008   | 0735             |
| 745           | 0008   | 0745             |
| 746           | 0008   | 0746             |


## 17.1 Module Parameters

|                   |                       |
| ----------------- | --------------------- |
| force_addr=0xaddr | 设置I/O基地址。 对于不在 BIOS 中设置地址的主板很有用。 不做 PCI 强制； 该设备必须仍然存在于 lspci 中。 除非驱动程序抱怨未设置基地址，否则不要使用此选项。 |


## 17.2 Description

i2c-sis5595 是一款真正的 SMBus 主机驱动程序，适用于带有 SiS5595 南桥的主板。

警告：如果您尝试访问 SiS5595 芯片上的集成传感器，则需要 sis5595 驱动程序，而不是此驱动程序。 该驱动程序是总线驱动程序，而不是芯片驱动程序。 总线驱动程序被其他芯片驱动程序用来访问总线上的芯片。


# 18. Kernel driver i2c-sis630
--------

**支持的适配器：**

- Silicon Integrated Systems Corp (SiS)

&emsp;630 芯片组（数据表：可在 <http://www.sfr-fresh.com/linux> 获取） 730 芯片组 964 芯片组

- Possible other SiS chipsets ?

**作者：**

- Alexander Malysh <amalysh@web.de>
- Amaury Decrême <amaury.decreme@gmail.com> - SiS964 support


## 18.1 Module Parameters

|                    |              |
| ------------------ | ------------ |
| force = [1|0]      | 强制启用SIS630。 危险的！ 对于上面未命名的芯片组来说，这可能很有趣，可以检查它是否适用于您的芯片组，但很危险！ |
| high_clock = [1|0] | 强制将主机主时钟设置为 56KHz（默认值，您的 BIOS 使用的值）。 危险的！ 这应该会快一点，但会冻结某些系统（即我的笔记本电脑）。 仅限 SIS630/730 芯片。|


## 18.2 Description

据了解，此仅 SMBus 驱动程序可在具有上述芯片组的主板上工作。

如果你看到这样的东西：

```bash
00:00.0 Host bridge: Silicon Integrated Systems [SiS] 630 Host (rev 31)
00:01.0 ISA bridge: Silicon Integrated Systems [SiS] 85C503/5513
```

或者像这样：

```bash
00:00.0 Host bridge: Silicon Integrated Systems [SiS] 730 Host (rev 02)
00:01.0 ISA bridge: Silicon Integrated Systems [SiS] 85C503/5513
```

或者像这样：

```bash
00:00.0 Host bridge: Silicon Integrated Systems [SiS] 760/M760 Host (rev 02)
00:02.0 ISA bridge: Silicon Integrated Systems [SiS] SiS964 [MuTIOL Media IO]
                                                      LPC Controller (rev 36)
```

在您的 lspci 输出中，则该驱动程序适用于您的芯片组。


# 19. Kernel driver i2c-sis96x
--------

取代 2.4.x i2c-sis645

**支持的适配器：**

- Silicon Integrated Systems Corp (SiS)

这些主桥的任意组合：

645, 645DX (aka 646), 648, 650, 651, 655, 735, 745, 746

和这些南桥：

961, 962, 963(L)

**作者：**

Mark M. Hoffman <mhoffman@lightlink.com>


## 19.1 Description

据了解，此仅 SMBus 驱动程序可在具有上述芯片组组合的主板上工作。 该驱动程序的开发没有受益于 SiS 的正确数据表。 假定 SMBus 寄存器与 SiS630 的寄存器兼容，尽管它们位于完全不同的位置。 感谢 Alexander Malysh <amalysh@web.de> 提供 SiS630 数据表（和驱动程序）。

以 root 身份执行 lspci 命令应该会生成类似以下几行的内容：

```bash
00:00.0 Host bridge: Silicon Integrated Systems [SiS]: Unknown device 0645
00:02.0 ISA bridge: Silicon Integrated Systems [SiS] 85C503/5513
00:02.1 SMBus: Silicon Integrated Systems [SiS]: Unknown device 0016
```

或者也许是这个：

```bash
00:00.0 Host bridge: Silicon Integrated Systems [SiS]: Unknown device 0645
00:02.0 ISA bridge: Silicon Integrated Systems [SiS]: Unknown device 0961
00:02.1 SMBus: Silicon Integrated Systems [SiS]: Unknown device 0016
```

（2.4.18以后的内核版本可以填写“未知”）

如果您看不到它，请查看 quirk_sis_96x_smbus (drivers/pci/quirks.c)（如果南桥检测失败）

我怀疑该驱动程序也可以适用于以下 SiS 芯片组：635 和 635T。 如果有人拥有带有这些芯片的主板，并且愿意以进步的名义冒着崩溃和烧毁原本表现良好的内核的风险……请通过 <mhoffman@lightlink.com> 或通过 linux-i2c 邮件列表与我联系： <linux-i2c@vger.kernel.org>。 请同时发送错误报告和/或成功案例。


## 19.2 TO DOs

驱动程序不支持SMBus块读/写； 如果发现需要它们的场景，我可能会添加它们。


# 20. Kernel driver i2c-taos-evm
--------

**作者：**

Jean Delvare <jdelvare@suse.de>

这是 TAOS I2C/SMBus 芯片评估模块的驱动程序。 这些模块包括一个功能有限的 SMBus 主设备，可通过串行端口进行控制。 几乎所有评估模块均受支持，但需要为每个新模块添加几行代码，以实例化总线上正确的 I2C 芯片。 显然，还需要相关芯片的驱动程序。

目前支持的设备有：

- TAOS TSL2550 EVM

有关 TAOS 产品的更多信息，请访问 <http://www.taosinc.com/>


## 20.1 Using this driver

为了使用此驱动程序，您需要 serport 驱动程序和 inputattach 工具，该工具是 input-utils 包的一部分。 以下命令将告诉内核您在第一个串行端口上有一个 TAOS EVM：

```bash
# modprobe serport
# inputattach --taos-evm /dev/ttyS0
```

## 20.2 Technical details

TAOS 评估模块仅支持 4 种 SMBus 事务类型： * 接收字节 * 发送字节 * 读取字节 * 写入字节

通信协议是基于文本的并且非常简单。 评估模块附带的 CD 上的 PDF 文档对此进行了描述。 通信速度相当慢，因为串行端口必须以 1200 bps 运行。 然而，我认为这在实践中并不是一个大问题，因为这些模块仅用于评估和测试。


# 21. Kernel driver i2c-viapro
--------

**支持的适配器：**

- VIA Technologies, Inc. VT82C596A/B Datasheet: 有时可在 VIA 网站上找到
- VIA Technologies, Inc. VT82C686A/B Datasheet: 有时可在 VIA 网站上找到
- VIA Technologies, Inc. VT8231, VT8233, VT8233A Datasheet: 可根据 VIA 要求提供
- VIA Technologies, Inc. VT8235, VT8237R, VT8237A, VT8237S, VT8251 Datasheet: 可根据要求并根据 VIA 的保密协议提供
- VIA Technologies, Inc. CX700 Datasheet: 可根据要求并根据 VIA 的保密协议提供
- VIA Technologies, Inc. VX800/VX820 Datasheet: 可用于 <http://linux.via.com.tw>
- VIA Technologies, Inc. VX855/VX875 Datasheet: 可用于 <http://linux.via.com.tw>
- VIA Technologies, Inc. VX900 Datasheet: 可用于 <http://linux.via.com.tw>

**作者：**

- Kyösti Mälkki <kmalkki@cc.hut.fi>,
- Mark D. Studebaker <mdsxyz123@yahoo.com>,
- Jean Delvare <jdelvare@suse.de>


## 21.1 Module Parameters

- force: int 强制启用 SMBus 控制器。 危险的！
- force_addr: int 强制启用给定地址处的 SMBus。 极其危险！


## 21.2 Description

i2c-viapro 是一款真正的 SMBus 主机驱动程序，适用于具有受支持的 VIA 南桥之一的主板。

您的 lspci -n 列表必须显示以下之一：

|                  |                        |
| ---------------- | ---------------------- |
| device 1106:3050 | (VT82C596A function 3) |
| device 1106:3051 | (VT82C596B function 3) |
| device 1106:3057 | (VT82C686 function 4)  |
| device 1106:3074 | (VT8233)               |
| device 1106:3147 | (VT8233A)              |
| device 1106:8235 | (VT8231 function 4)    |
| device 1106:3177 | (VT8235)               |
| device 1106:3227 | (VT8237R)              |
| device 1106:3337 | (VT8237A)              |
| device 1106:3372 | (VT8237S)              |
| device 1106:3287 | (VT8251)               |
| device 1106:8324 | (CX700)                |
| device 1106:8353 | (VX800/VX820)          |
| device 1106:8409 | (VX855/VX875)          |
| device 1106:8410 | (VX900)                |

如果这些都没有出现，您应该在 BIOS 中查找设置，例如启用 ACPI / SMBus 甚至 USB。

除了最旧的芯片（VT82C596A/B、VT82C686A 和最有可能的 VT8231）外，该驱动程序支持 I2C 块事务。 此类事务主要用于读取和写入 EEPROM。

CX700/VX800/VX820 似乎还支持 SMBus PEC，尽管该驱动程序尚未实现。


# 22. Kernel driver i2c-via
--------

**支持的适配器：**

VIA Technologies, InC. VT82C586B Datasheet: 可在 VIA 网站上公开获取

**作者：**

Kyösti Mälkki <kmalkki@cc.hut.fi>


## 22.1 Description

i2c-via 是一个 i2c 总线驱动程序，适用于采用 VIA 芯片组的主板。

支持以下 VIA pci 芯片组：

- MVP3, VP3, VP2/97, VPX/97
- others with South bridge VT82C586B

您的 lspci 清单必须显示此内容

```bash
Bridge: VIA Technologies, Inc. VT82C586B ACPI (rev 10)
```

Problems?

Q:

&emsp;您的主板上有 VT82C586B，但列表中没有。

A:

&emsp;转至 BIOS 设置、PCI 设备或类似部分。 打开 USB 支持，然后重试。

Q:

&emsp;没有错误消息，但 i2c 似乎仍然无法工作。

A:

&emsp;这可能会发生。 该驱动程序使用 VIA 在其数据表中推荐的引脚，但主板制造商实际上可以通过多种方式连接线路。


# 23. Kernel driver scx200_acb
--------

**作者：**

Christer Weinigel <wingel@nano-system.com>

该驱动程序取代了名为 i2c-nscacb 的较旧的、从未合并的驱动程序。


## 23.1 Module Parameters

- base: SCx200 和 SC1100 设备上的 ACCESS.bus 控制器最多 4 个整数基地址

默认情况下，驱动程序使用两个基地址 0x820 和 0x840。 如果您只需要一个基地址，请将第二个基地址指定为 0 以覆盖此默认值。


## 23.2 Description

允许在 Geode SCx200 和 SC1100 处理器以及 CS5535 和 CS5536 Geode 配套设备上使用 ACCESS.bus 控制器。


## 23.3 Device-specific notes

SC1100 WRAP 板已知使用基地址 0x810 和 0x820。 如果 scx200_acb 驱动程序内置于内核中，请将以下参数添加到引导命令行中：

```bash
scx200_acb.base=0x810,0x820
```

如果 scx200_acb 驱动程序构建为模块，请将以下行添加到 /etc/modprobe.d/ 中的配置文件中：

```bash
options scx200_acb base=0x810,0x820
```
