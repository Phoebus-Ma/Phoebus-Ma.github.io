
# PXA2xx SPI on SSP driver HOWTO

# 1. Overview

这是关于 pxa2xx_spi 驱动程序的迷你 HOWTO。 该驱动程序将 PXA2xx 同步串口转变为 SPI 主机控制器（请参阅[Linux 内核 SPI 支持概述](https://www.kernel.org/doc/html/latest/spi/spi-summary.html)）。 该驱动程序具有以下特点：

- 支持任何 PXA2xx 和兼容的 SSP。
- SSP PIO 和 SSP DMA 数据传输。
- 外部和内部 (SSPFRM) 芯片选择。
- 每个外围设备（芯片）配置。
- 完全暂停、冻结、恢复支持。

该驱动程序围绕由内核线程提供服务的 &struct spi_message FIFO 构建。 内核线程 spi_pump_messages() 驱动消息 FIFO，并负责对 SPI 事务进行排队以及设置和启动 DMA 或中断驱动的传输。


# 2. Declaring PXA2xx host controllers

通常，对于旧平台，SPI 主机控制器在 arch/.../mach-/board-.c 中定义为“平台设备”。 主机控制器配置通过 include/linux/spi/pxa2xx_spi.h 中的表传递给驱动程序：

```c
struct pxa2xx_spi_controller {
      u16 num_chipselect;
      u8 enable_dma;
      ...
};
```

“pxa2xx_spi_controller.num_chipselect”字段用于确定连接到该 SPI 主控制器的外围设备（芯片）的数量。

“pxa2xx_spi_controller.enable_dma”字段通知驱动程序应使用 SSP DMA。 这导致驱动程序获取两个 DMA 通道：Rx 通道和 Tx 通道。 Rx 通道比 Tx 通道具有更高的 DMA 服务优先级。 请参阅“PXA2xx 开发人员手册”中的“DMA 控制器”部分。

对于新平台，控制器和外围设备的描述来自设备树或 ACPI。


# 3. NSSP HOST SAMPLE

以下是针对旧平台使用 PXA255 NSSP 的示例配置：

```c
static struct resource pxa_spi_nssp_resources[] = {
      [0] = {
              .start  = __PREG(SSCR0_P(2)), /* Start address of NSSP */
              .end    = __PREG(SSCR0_P(2)) + 0x2c, /* Range of registers */
              .flags  = IORESOURCE_MEM,
      },
      [1] = {
              .start  = IRQ_NSSP, /* NSSP IRQ */
              .end    = IRQ_NSSP,
              .flags  = IORESOURCE_IRQ,
      },
};

static struct pxa2xx_spi_controller pxa_nssp_controller_info = {
      .num_chipselect = 1, /* Matches the number of chips attached to NSSP */
      .enable_dma = 1, /* Enables NSSP DMA */
};

static struct platform_device pxa_spi_nssp = {
      .name = "pxa2xx-spi", /* MUST BE THIS VALUE, so device match driver */
      .id = 2, /* Bus number, MUST MATCH SSP number 1..n */
      .resource = pxa_spi_nssp_resources,
      .num_resources = ARRAY_SIZE(pxa_spi_nssp_resources),
      .dev = {
              .platform_data = &pxa_nssp_controller_info, /* Passed to driver */
      },
};

static struct platform_device *devices[] __initdata = {
      &pxa_spi_nssp,
};

static void __init board_init(void)
{
      (void)platform_add_device(devices, ARRAY_SIZE(devices));
}
```


# 4. Declaring peripheral devices

通常，对于传统平台，每个 SPI 外围设备（芯片）都使用“linux/spi/spi.h”中的“spi_board_info”结构在 arch/.../mach-/board-.c 中定义。 有关更多信息，请参阅“Linux 内核 SPI 支持概述”。

连接到 PXA2xx 的每个外围设备（芯片）必须通过“include/linux/spi/pxa2xx_spi.h”中的结构“pxa2xx_spi_chip”提供特定的芯片配置信息。 每当驱动程序与外围设备通信时，PXA2xx 主机控制器驱动程序都会使用该配置。 所有字段都是可选的。

```c
struct pxa2xx_spi_chip {
      u8 tx_threshold;
      u8 rx_threshold;
      u8 dma_burst_size;
      u32 timeout;
};
```

“pxa2xx_spi_chip.tx_threshold”和“pxa2xx_spi_chip.rx_threshold”字段用于配置SSP硬件FIFO。 这些字段对于 pxa2xx_spi 驱动程序的性能至关重要，配置错误将导致 rx FIFO 溢出（尤其是在 PIO 模式传输中）。 好的默认值是：

```c
.tx_threshold = 8,
.rx_threshold = 8,
```

范围是 1 到 16，其中零表示“使用默认值”。

“pxa2xx_spi_chip.dma_burst_size”字段用于配置 PXA2xx DMA 引擎，并与“spi_device.bits_per_word”字段相关。 阅读并理解 PXA2xx“开发人员手册”中有关 DMA 控制器和 SSP 控制器的部分，以确定正确的值。 配置为字节宽传输的 SSP 将使用值 8。如果 dma_burst_size == 0，驱动程序将确定合理的默认值。

“pxa2xx_spi_chip.timeout”字段用于有效处理 SSP 接收器 FIFO 中的尾随字节。 该字段的正确值取决于 SPI 总线速度（“spi_board_info.max_speed_hz”）和特定的外围设备。 请注意，PXA2xx SSP 1 不支持尾随字节超时，并且必须忙等待任何尾随字节。

注意：如果使用 SSPFRM，SPI 驱动程序无法控制片选，因此每次 spi_transfer 后片选都会被丢弃。 大多数设备需要在完整消息周围断言芯片选择。 使用 SSPFRM 作为 GPIO（通过描述符）来容纳这些芯片。


# 5. NSSP PERIPHERAL SAMPLE

对于旧平台或在某些其他情况下，pxa2xx_spi_chip 结构将传递到“spi_board_info.controller_data”字段中的 pxa2xx_spi 驱动程序。 以下是使用 PXA255 NSSP 的示例配置。

```c
static struct pxa2xx_spi_chip cs8415a_chip_info = {
      .tx_threshold = 8, /* SSP hardware FIFO threshold */
      .rx_threshold = 8, /* SSP hardware FIFO threshold */
      .dma_burst_size = 8, /* Byte wide transfers used so 8 byte bursts */
      .timeout = 235, /* See Intel documentation */
};

static struct pxa2xx_spi_chip cs8405a_chip_info = {
      .tx_threshold = 8, /* SSP hardware FIFO threshold */
      .rx_threshold = 8, /* SSP hardware FIFO threshold */
      .dma_burst_size = 8, /* Byte wide transfers used so 8 byte bursts */
      .timeout = 235, /* See Intel documentation */
};

static struct spi_board_info streetracer_spi_board_info[] __initdata = {
      {
              .modalias = "cs8415a", /* Name of spi_driver for this device */
              .max_speed_hz = 3686400, /* Run SSP as fast a possible */
              .bus_num = 2, /* Framework bus number */
              .chip_select = 0, /* Framework chip select */
              .platform_data = NULL; /* No spi_driver specific config */
              .controller_data = &cs8415a_chip_info, /* Host controller config */
              .irq = STREETRACER_APCI_IRQ, /* Peripheral device interrupt */
      },
      {
              .modalias = "cs8405a", /* Name of spi_driver for this device */
              .max_speed_hz = 3686400, /* Run SSP as fast a possible */
              .bus_num = 2, /* Framework bus number */
              .chip_select = 1, /* Framework chip select */
              .controller_data = &cs8405a_chip_info, /* Host controller config */
              .irq = STREETRACER_APCI_IRQ, /* Peripheral device interrupt */
      },
};

static void __init streetracer_init(void)
{
      spi_register_board_info(streetracer_spi_board_info,
                              ARRAY_SIZE(streetracer_spi_board_info));
}
```


# 6. DMA and PIO I/O Support

pxa2xx_spi 驱动程序支持 DMA 和中断驱动的 PIO 消息传输。 驱动程序默认为 PIO 模式，并且必须通过设置“pxa2xx_spi_controller”结构中的“enable_dma”标志来启用 DMA 传输。 对于已知支持 DMA 的较新平台，驱动程序将自动启用它并首先尝试它，并可能回退到 PIO。 DMA 模式支持相干和基于流的 DMA 映射。

以下逻辑用于确定每个“spi_transfer”要使用的 I/O 类型：

```c
if spi_message.len > 65536 then
      if spi_message.is_dma_mapped or rx_dma_buf != 0 or tx_dma_buf != 0 then
              reject premapped transfers

      print "rate limited" warning
      use PIO transfers

if enable_dma and the size is in the range [DMA burst size..65536] then
      use streaming DMA mode

otherwise
      use PIO transfer
```


# 7. THANKS TO

David Brownell 和其他人指导了该驱动程序的开发。
