
# Kernel driver i2c-mux-gpio

作者: Peter Korsgaard <peter.korsgaard@barco.com>


## 1. Description

i2c-mux-gpio 是一个 i2c 多路复用器驱动程序，提供从主 I2C 总线和通过 GPIO 引脚控制的硬件多路复用器对 I2C 总线段的访问。

例如:

```bash
 ----------              ----------  Bus segment 1   - - - - -
|          | SCL/SDA    |          |-------------- |           |
|          |------------|          |
|          |            |          | Bus segment 2 |           |
|  Linux   | GPIO 1..N  |   MUX    |---------------   Devices
|          |------------|          |               |           |
|          |            |          | Bus segment M
|          |            |          |---------------|           |
 ----------              ----------                  - - - - -
```

根据 GPIO 引脚 1..N 的设置，主 I2C 总线的 SCL/SDA 复用至总线段 1..M。


## 2. Usage

i2c-mux-gpio 使用平台总线，因此您需要提供一个 struct platform_device，其中 platform_data 指向 struct i2c_mux_gpio_platform_data，其中包含主总线的 I2C 适配器编号、要创建的总线段数以及用于控制的 GPIO 引脚 它。 有关详细信息，请参阅 include/linux/platform_data/i2c-mux-gpio.h。

例如：对于提供通过 3 个 GPIO 引脚控制的 4 个总线段的 MUX，类似如下：

```c
#include <linux/platform_data/i2c-mux-gpio.h>
#include <linux/platform_device.h>

static const unsigned myboard_gpiomux_gpios[] = {
      AT91_PIN_PC26, AT91_PIN_PC25, AT91_PIN_PC24
};

static const unsigned myboard_gpiomux_values[] = {
      0, 1, 2, 3
};

static struct i2c_mux_gpio_platform_data myboard_i2cmux_data = {
      .parent         = 1,
      .base_nr        = 2, /* optional */
      .values         = myboard_gpiomux_values,
      .n_values       = ARRAY_SIZE(myboard_gpiomux_values),
      .gpios          = myboard_gpiomux_gpios,
      .n_gpios        = ARRAY_SIZE(myboard_gpiomux_gpios),
      .idle           = 4, /* optional */
};

static struct platform_device myboard_i2cmux = {
      .name           = "i2c-mux-gpio",
      .id             = 0,
      .dev            = {
              .platform_data  = &myboard_i2cmux_data,
      },
};
```

如果您在注册时不知道绝对 GPIO 引脚编号，则可以提供芯片名称 (.chip_name) 和相对 GPIO 引脚编号，i2c-mux-gpio 驱动程序将为您完成工作，包括延迟探测 如果 GPIO 芯片不能立即可用。


# 3. Device Registration

注册 i2c-mux-gpio 设备时，您应该传递它用作设备 ID 的任何 GPIO 引脚的编号。 这保证了每个实例都有不同的 ID。

或者，如果您不需要稳定的设备名称，您可以简单地传递 PLATFORM_DEVID_AUTO 作为设备 ID，平台核心将为您的设备分配一个动态 ID。 如果您在注册时不知道绝对 GPIO 引脚编号，这甚至是唯一的选择。
