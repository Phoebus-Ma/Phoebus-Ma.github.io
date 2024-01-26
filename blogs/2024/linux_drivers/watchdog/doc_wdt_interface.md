
# WDT Watchdog Timer Interfaces For The Linux Operating System

最后评论：2007/10/05

Alan Cox <alan@lxorguk.ukuu.org.uk>

- ICS WDT501-P
- ICS WDT501-P (无风扇转速表)
- ICS WDT500-P

# 1. Introduction

所有接口都提供/dev/watchdog，打开时必须在超时内写入，否则机器将重新启动。 每次写入都会延迟重新启动时间另一个超时。 对于软件看门狗，重新启动的能力将取决于机器和中断的状态。 硬件板从物理上将机器从它们自己的板载定时器中拉下来，并且几乎可以从任何东西重新启动。

WDT501P 卡上提供第二个温度监控接口。 这提供了/dev/温度。 这是机器内部温度（以华氏度为单位）。 每次读取都会返回一个给出温度的字节。

第三个接口记录有关其他警报事件的内核消息。

无法安全探测 ICS ISA 总线 wdt 卡。 相反，您需要传递 IO 地址和 IRQ 启动参数。 例如。：

```c
wdt.io=0x240 wdt.irq=11
```

其他“wdt”驱动程序参数有：

|            |                                       |
| ---------- | ------------------------------------- |
| heartbeat  | 看门狗心跳（以秒为单位）（默认 60）      |
| nowayout   | 看门狗一旦启动就无法停止（内核构建参数）  |
| tachometer | WDT501-P 风扇转速计支持（0=禁用，默认=0）|
| type       | WDT501-P 卡类型（500 或 501，默认=500） |


# 2. Features

|                  |   |   |
| ---------------- | - | - |
| Reboot Timer     | X | X |
| External Reboot  | X | X |
| I/O Port Monitor | o | o |
| Temperature      | X | o |
| Fan Speed        | X | o |
| Power Under      | X | o |
| Power Over       | X | o |
| Overheat         | X | o |

目前不支持 WDT 板上的外部事件接口。 然而，为其分配了次要编号。

看门狗驱动程序示例：

samples/watchdog/watchdog-simple.c
