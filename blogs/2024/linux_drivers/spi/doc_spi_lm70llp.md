
# spi_lm70llp : LM70-LLP parport-to-SPI adapter

**Supported board/chip:**

- 美国国家半导体 LM70 LLP 评估板

- Datasheet: http://www.national.com/pf/LM/LM70.html

**Author:**

Kaiwan N Billimoria <kaiwan@designergraphix.com>


# 1. Description

该驱动程序提供将 National Semiconductor LM70 LLP 温度传感器评估板连接到内核的 SPI 核心子系统的粘合代码。

这是一个 SPI 主控制器驱动程序。 它可以与 LM70 逻辑驱动程序（“SPI 协议驱动程序”）结合使用（分层）。 实际上，该驱动程序将评估板上的并行端口接口转换为具有单个设备的 SPI 总线，该总线将由通用 LM70 驱动程序 (drivers/hwmon/lm70.c) 驱动。


# 2. Hardware Interfacing

此特定板 (LM70EVAL-LLP) 的原理图可在此处获取（第 4 页）：

<http://www.national.com/appinfo/tempsensors/files/LM70LLPEVALmanual.pdf>

LM70 LLP 评估板上的硬件接口如下：

| Parallel |    |           | LM70 LLP   |
| -------- | -- | --------- | ---------- |
| Port     | .  | Direction | JP2 Header |
| D0       | 2  | -         | -          |
| D1       | 3  | -->       | V+ 5       |
| D2       | 4  | -->       | V+ 5       |
| D3       | 5  | -->       | V+ 5       |
| D4       | 6  | -->       | V+ 5       |
| D5       | 7  | -->       | nCS 8      |
| D6       | 8  | -->       | SCLK 3     |
| D7       | 9  | -->       | SI/O 5     |
| GND      | 25 | -         | GND 7      |
| Select   | 13 | <--       | SI/O 1     |

请注意，由于 LM70 使用 SPI 的“3 线”变体，因此 SI/SO 引脚连接到引脚 D7（作为主输出）和选择（作为主输入），使用一种安排，让 parport 或 LM70 将引脚拉低。 这不能与真正的 SPI 设备共享，但其他 3 线设备可能共享相同的 SI/SO 引脚。

该驱动程序 (lm70_txrx) 中的 bitbanger 例程通过其 sysfs 挂钩，使用 spi_write_then_read() 调用从绑定的“hwmon/lm70”协议驱动程序回调。 它执行模式 0 (SPI/Microwire) bitbanging。 然后 lm70 驱动程序解释生成的数字温度值并将其通过 sysfs 导出。

“陷阱”：美国国家半导体的 LM70 LLP 评估板电路原理图显示，LM70 芯片的 SI/O 线连接到晶体管 Q1 的基极（还有一个上拉电阻和一个连接到 D7 的齐纳二极管）； 而集电极则连接至 VCC。

解释该电路时，当 LM70 SI/O 线为高电平（或三态且主机未通过 D7 接地）时，晶体管导通并将集电极切换至零，这反映在 DB25 parport 连接器的引脚 13 上。 另一方面，当 SI/O 为低电平（由 LM70 或主机驱动）时，晶体管被切断，连接到其集电极的电压在引脚 13 上反映为高电平。

因此：该驱动程序中的 getmiso 内联例程考虑了这一事实，反转在引脚 13 处读取的值。


# 3. Thanks to

- David Brownell 指导 SPI 端驱动程序开发。
- Craig Hollabaugh 博士提供（早期）“手动”bitbanging 驱动程序版本。
- Nadir Billimoria 帮助解释电路原理图。
