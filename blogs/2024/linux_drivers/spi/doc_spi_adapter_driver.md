
# spi_butterfly - parport-to-butterfly adapter driver

这是一个硬件和软件项目，包括构建和使用并行端口适配器电缆以及“AVR Butterfly”来运行用于用户接口和/或传感器的固件。 Butterfly 是一款价值 20 美元的电池供电卡，配有 AVR 微控制器和许多好东西：传感器、LCD、闪光灯、拨动杆等。 您可以使用 AVR-GCC 为此开发固件，并使用此适配器电缆对其进行刷新。

您可以用旧打印机电缆制作此适配器，然后将东西直接焊接到蝴蝶上。 或者（如果您有零件和技能）您可以想出一些更奇特的东西，为蝴蝶和打印机端口提供电路保护，或者使用比打印机端口的两个信号引脚更好的电源。 或者就此而言，您可以使用类似的电缆与许多 AVR 板（甚至面包板）进行通信。

这比“ISP 编程”电缆更强大，因为它允许内核 SPI 协议驱动程序与 AVR 交互，甚至可以让 AVR 向它们发出中断。 稍后，您的协议驱动程序应该可以轻松地使用“真正的 SPI 控制器”，而不是这个 bitbanger。

第一个电缆连接将 Linux 连接到一个 SPI 总线，并带有 AVR 和 DataFlash 芯片； 并连接至 AVR 复位线。 这就是刷新固件所需的全部内容，引脚是标准 Atmel“ISP”连接器引脚（也用于非 Butterfly AVR 板）。 在 parport 方面，这就像“sp12”编程电缆。

| Signal | Butterfly     | Parport (DB-25) |
| ------ | ------------- | --------------- |
| SCK    | J403.PB1/SCK  | pin 2/D0        |
| RESET  | J403.nRST     | pin 3/D1        |
| VCC    | J403.VCC_EXT  | pin 8/D6        |
| MOSI   | J403.PB2/MOSI | pin 9/D7        |
| MISO   | J403.PB3/MISO | pin 11/S7,nBUSY |
| GND    | J403.GND      | pin 23/GND      |

然后，为了让 Linux 掌握该总线与 DataFlash 芯片通信，您必须 (a) 刷新禁用 SPI 的新固件（设置 PRR.2，并通过清除 PORTB.[0-3] 禁用上拉）； (b) 配置mtd_dataflash驱动程序； (c) 片选电缆。

| Signal | Butterfly    | Parport (DB-25)   |
| ------ | ------------ | ----------------- |
| VCC    | J400.VCC_EXT | pin 7/D5          |
| SELECT | J400.PB0/nSS | pin 17/C3,nSELECT |
| GND    | J400.GND     | pin 24/GND        |

或者，您可以刷新固件，使 AVR 成为 SPI 从设备（使 DataFlash 保持复位状态），并调整 spi_butterfly 驱动程序，使其绑定到您自定义的基于 SPI 协议的驱动程序。

使用 J405 的“USI”控制器也可用于第二个 SPI 总线。 这样您就可以使用自定义 SPI-with-USI 固件与 AVR 进行通信，同时让 Linux 或 AVR 使用 DataFlash。 有大量备用 parport 引脚可用于连接此引脚，例如：

| Signal | Butterfly     | Parport (DB-25)     |
| ------ | ------------- | ------------------- |
| SCK    | J403.PE4/USCK | pin 5/D3            |
| MOSI   | J403.PE5/DI   | pin 6/D4            |
| MISO   | J403.PE6/DO   | pin 12/S5,nPAPEROUT |
| GND    | J403.GND      | pin 22/GND          |
| IRQ    | J402.PF4      | pin 10/S6,ACK       |
| GND    | J402.GND(P2)  | pin 25/GND          |
