
# How to instantiate I2C devices

与 PCI 或 USB 设备不同，I2C 设备不会在硬件级别进行枚举。 相反，软件必须知道每个 I2C 总线段上连接了哪些设备，以及这些设备正在使用什么地址。 因此，内核代码必须显式实例化 I2C 设备。 根据上下文和要求，有多种方法可以实现此目的。


# 1. Method 1: Declare the I2C devices statically

当 I2C 总线是系统总线时（就像许多嵌入式系统的情况一样），此方法适用。 在此类系统上，每个 I2C 总线都有一个预先已知的编号。 因此可以预先声明该总线上的 I2C 设备。

在不同的体系结构上，此信息以不同的方式提供给内核：设备树、ACPI 或板文件。

当注册相关的 I2C 总线时，i2c-core 将自动实例化 I2C 设备。 当它们所在的 I2C 总线消失时（如果有的话），这些设备将自动解除绑定并销毁。


# 2. Declare the I2C devices via devicetree

在使用 devicetree 的平台上，I2C 设备的声明是在主控制器的子节点中完成的。

示例:

```c
i2c1: i2c@400a0000 {
        /* ... master properties skipped ... */
        clock-frequency = <100000>;

        flash@50 {
                compatible = "atmel,24c256";
                reg = <0x50>;
        };

        pca9532: gpio@60 {
                compatible = "nxp,pca9532";
                gpio-controller;
                #gpio-cells = <2>;
                reg = <0x60>;
        };
};
```

这里，两个设备以 100kHz 的速度连接到总线。 有关设置设备可能需要的其他属性，请参阅 Documentation/devicetree/bindings/ 中的 devicetree 文档。


## 2.1 Declare the I2C devices via ACPI

ACPI 还可以描述 I2C 设备。 目前有这方面的特殊文档位于 [ACPI Based Device Enumeration](https://www.kernel.org/doc/html/latest/firmware-guide/acpi/enumeration.html).


## 2.2 Declare the I2C devices in board files

在许多嵌入式架构中，devicetree已经取代了基于板文件的旧硬件描述，但后者仍然在旧代码中使用。 通过板文件实例化 I2C 设备是通过调用 i2c_register_board_info() 注册的 struct i2c_board_info 数组完成的。

示例 (来自 omap2 h4):

```c
static struct i2c_board_info h4_i2c_board_info[] __initdata = {
      {
              I2C_BOARD_INFO("isp1301_omap", 0x2d),
              .irq            = OMAP_GPIO_IRQ(125),
      },
      {       /* EEPROM on mainboard */
              I2C_BOARD_INFO("24c01", 0x52),
              .platform_data  = &m24c01,
      },
      {       /* EEPROM on cpu card */
              I2C_BOARD_INFO("24c01", 0x57),
              .platform_data  = &m24c01,
      },
};

static void __init omap_h4_init(void)
{
      (...)
      i2c_register_board_info(1, h4_i2c_board_info,
                      ARRAY_SIZE(h4_i2c_board_info));
      (...)
}
```

上面的代码在 I2C 总线 1 上声明了 3 个设备，包括它们各自的地址和驱动程序所需的自定义数据。


# 3. Method 2: Instantiate the devices explicitly

当较大的设备使用 I2C 总线进行内部通信时，此方法适用。 一个典型的例子是电视适配器。 这些可以具有调谐器、视频解码器、音频解码器等，通常通过I2C总线连接到主芯片。 您事先不会知道I2C总线的编号，因此无法使用上述方法1。 相反，您可以显式实例化您的 I2C 设备。 这是通过填充 struct i2c_board_info 并调用 i2c_new_client_device() 来完成的。

示例 (来自 sfe4001 网络驱动程序):

```c
static struct i2c_board_info sfe4001_hwmon_info = {
      I2C_BOARD_INFO("max6647", 0x4e),
};

int sfe4001_init(struct efx_nic *efx)
{
      (...)
      efx->board_info.hwmon_client =
              i2c_new_client_device(&efx->i2c_adap, &sfe4001_hwmon_info);

      (...)
}
```

上面的代码在 I2C 总线上实例化 1 个 I2C 设备，该设备位于相关网络适配器上。

这种情况的一个变体是当您不确定 I2C 设备是否存在时（例如，对于便宜的主板版本中不存在的可选功能，但您无法区分它们），或者 一块板到另一块板的地址可能不同（制造商更改其设计，恕不另行通知）。 在这种情况下，您可以调用 i2c_new_scanned_device() 而不是 i2c_new_client_device()。

示例 (来自 nxp OHCI 驱动程序):

```c
static const unsigned short normal_i2c[] = { 0x2c, 0x2d, I2C_CLIENT_END };

static int usb_hcd_nxp_probe(struct platform_device *pdev)
{
      (...)
      struct i2c_adapter *i2c_adap;
      struct i2c_board_info i2c_info;

      (...)
      i2c_adap = i2c_get_adapter(2);
      memset(&i2c_info, 0, sizeof(struct i2c_board_info));
      strscpy(i2c_info.type, "isp1301_nxp", sizeof(i2c_info.type));
      isp1301_i2c_client = i2c_new_scanned_device(i2c_adap, &i2c_info,
                                                  normal_i2c, NULL);
      i2c_put_adapter(i2c_adap);
      (...)
}
```

上述代码在 I2C 总线上实例化最多 1 个 I2C 设备，该设备位于相关 OHCI 适配器上。 它首先尝试地址 0x2c，如果没有找到任何东西，它会尝试地址 0x2d，如果仍然没有找到，它就会放弃。

实例化 I2C 设备的驱动程序负责在清理时销毁它。 这是通过在 i2c_new_client_device() 或 i2c_new_scanned_device() 先前返回的指针上调用 i2c_unregister_device() 来完成的。


# 4. Method 3: Probe an I2C bus for certain devices

有时您没有足够的有关 I2C 设备的信息，甚至无法调用 i2c_new_scanned_device()。 典型的案例是PC主板上的硬件监控芯片。 有几十个模型，可以驻留在 25 个不同的地址。 鉴于主板数量巨大，几乎不可能建立所使用的硬件监控芯片的详尽列表。 幸运的是，大多数这些芯片都有制造商和设备ID寄存器，因此可以通过探测来识别它们。

在这种情况下，I2C 设备既不会显式声明，也不会显式实例化。 相反，i2c-core 将在加载驱动程序后立即探测此类设备，如果发现任何设备，将自动实例化 I2C 设备。 为了防止该机制出现任何不当行为，适用以下限制：

- I2C 设备驱动程序必须实现 detector() 方法，该方法通过读取任意寄存器来识别受支持的设备。

- 只有可能具有受支持的设备并同意被探测的总线才会被探测。 例如，这可以避免探测电视适配器上的硬件监控芯片。

示例: 请参阅 drivers/hwmon/lm90.c 中的 lm90_driver 和 lm90_detect()

当检测到它们的驱动程序被删除时，或者当底层 I2C 总线本身被破坏时（以先发生者为准），因此类成功探测而实例化的 I2C 设备将自动被破坏。

熟悉 2.4 内核和早期 2.6 内核 I2C 子系统的人会发现，方法 3 本质上与那里所做的类似。 两个显着差异是：

- 探测现在只是实例化 I2C 设备的一种方法，而在当时它是唯一的方法。 如果可能，应首选方法 1 和 2。 仅当没有其他方法时才应使用方法 3，因为它可能会产生不良副作用。

- I2C 总线现在必须明确说明哪些 I2C 驱动程序类可以探测它们（通过类位字段），而当时默认情况下会探测所有 I2C 总线。 默认是一个空类，这意味着不会发生探测。 类位域的目的是限制上述不良副作用。

再次强调，应尽可能避免使用方法 3。 显式设备实例化（方法 1 和 2）更受青睐，因为它更安全、更快速。


# 5. Method 4: Instantiate from user-space

一般来说，内核应该知道连接了哪些 I2C 设备以及它们所在的地址。 但是，在某些情况下，它不会，因此添加了 sysfs 接口来让用户提供信息。 该接口由在每个 I2C 总线目录中创建的 2 个属性文件组成：new_device 和 delete_device。 这两个文件都是只写的，您必须向它们写入正确的参数才能正确实例化（分别删除）I2C 设备。

文件new_device有2个参数：I2C设备的名称（字符串）和I2C设备的地址（数字，通常以0x开头的十六进制表示，但也可以以十进制表示。）

文件delete_device 采用单个参数：I2C 设备的地址。 由于在给定 I2C 段上没有两个设备可以位于同一地址，因此该地址足以唯一标识要删除的设备。

示例:

```bash
# echo eeprom 0x50 > /sys/bus/i2c/devices/i2c-3/new_device
```

虽然此接口仅应在无法完成内核内设备声明时使用，但在多种情况下它会很有帮助：

- I2C 驱动程序通常会检测设备（上面的方法 3），但设备所在的总线段没有设置正确的类位，因此不会触发检测。

- I2C 驱动程序通常会检测设备，但您的设备位于意外的地址。

- I2C驱动程序通常会检测设备，但你的设备没有被检测到，要么是因为检测例程太严格，要么是因为你的设备尚未得到官方支持，但你知道它是兼容的。

- 您正在测试板上开发驱动程序，您自己在测试板上焊接了 I2C 设备。

此接口替代了某些 I2C 驱动程序实现的 force_* 模块参数。 在 i2c 核心中实现，而不是在每个设备驱动程序中单独实现，它的效率要高得多，并且还有一个优点，即您不必重新加载驱动程序来更改设置。 您还可以在驱动程序加载甚至可用之前实例化设备，并且不需要知道设备需要什么驱动程序。
