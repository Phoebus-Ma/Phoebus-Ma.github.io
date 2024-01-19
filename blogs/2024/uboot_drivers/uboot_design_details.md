# Design Details

# 1. Introduction

本自述文件包含有关驱动程序模型的高级信息，这是在 U-Boot 中声明和访问驱动程序的统一方法。 最初的工作是由以下人员完成的：

- Marek Vasut <marex@denx.de>
- Pavel Herrmann <morpheus.ibis@gmail.com>
- Viktor Křivák <viktor.krivak@gmail.com>
- Tomas Hlavacek <tmshlvck@gmail.com>

这已通过以下方式简化并扩展到当前的实施中：

- Simon Glass <sjg@chromium.org>

# 2. Terminology

**Uclass**

一组以相同方式运行的设备。 uclass 提供了一种访问组内各个设备的方法，但始终使用相同的接口。 例如，GPIO uclass 提供获取/设置值的操作。 一个 I2C uclass 可能有 10 个 I2C 端口，其中 4 个与一个驱动程序相关，6 个与另一个驱动程序相关。

**Driver**

一些与外围设备对话并为其提供更高级别接口的代码。

**Device**

绑定到特定端口或外设的驱动程序实例。

# 3. How to try it

构建 U-Boot 沙箱并运行它：

```bash
make sandbox_defconfig
make
./u-boot -d u-boot.dtb

(type 'reset' to exit U-Boot)
```

有一个名为“demo”的 uclass。 这个 uclass 处理打招呼并报告其状态。 这个uclass中有两个驱动程序：

- simple: 仅打印一条消息打招呼，不实现状态
- shape: 打印形状并报告作为状态打印的字符数

演示类非常简单，但并不简单。 目的是它可以用于测试，因此它将实现所有驱动程序模型功能并提供良好的代码覆盖率。 它确实有多个驱动程序，它处理参数数据和平台（告诉驱动程序如何在特定平台上操作的数据），并且它使用私有驱动程序数据。

要尝试它，请参阅下面的示例会话：

```bash
=>demo hello 1
Hello '@' from 07981110: red 4
=>demo status 2
Status: 0
=>demo hello 2
g
r@
e@@
e@@@
n@@@@
g@@@@@
=>demo status 2
Status: 21
=>demo hello 4 ^
  y^^^
 e^^^^^
l^^^^^^^
l^^^^^^^
 o^^^^^
  w^^^
=>demo status 4
Status: 36
=>
```

# 4. Running the tests

驱动模型的目的是核心部分在沙箱中具有 100% 的测试覆盖率，并且每个 uclass 都有自己的测试。 为了实现这一目标，test/dm 中提供了测试。 要运行它们，请尝试：

```bash
./test/py/test.py --bd sandbox --build -k ut_dm -v
```

你应该看到这样的东西：

```bash
(venv)$ ./test/py/test.py --bd sandbox --build -k ut_dm -v
+make O=/root/u-boot/build-sandbox -s sandbox_defconfig
+make O=/root/u-boot/build-sandbox -s -j8
============================= test session starts ==============================
platform linux2 -- Python 2.7.5, pytest-2.9.0, py-1.4.31, pluggy-0.3.1 -- /root/u-boot/venv/bin/python
cachedir: .cache
rootdir: /root/u-boot, inifile:
collected 199 items

test/py/tests/test_ut.py::test_ut_dm_init PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_adc_bind] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_adc_multi_channel_conversion] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_adc_multi_channel_shot] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_adc_single_channel_conversion] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_adc_single_channel_shot] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_adc_supply] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_adc_wrong_channel_selection] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_autobind] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_autobind_uclass_pdata_alloc] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_autobind_uclass_pdata_valid] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_autoprobe] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_bus_child_post_bind] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_bus_child_post_bind_uclass] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_bus_child_pre_probe_uclass] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_bus_children] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_bus_children_funcs] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_bus_children_iterators] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_bus_parent_data] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_bus_parent_data_uclass] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_bus_parent_ops] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_bus_parent_platdata] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_bus_parent_platdata_uclass] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_children] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_clk_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_clk_periph] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_device_get_uclass_id] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_eth] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_eth_act] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_eth_alias] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_eth_prime] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_eth_rotate] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_fdt] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_fdt_offset] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_fdt_pre_reloc] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_fdt_uclass_seq] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_gpio] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_gpio_anon] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_gpio_copy] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_gpio_leak] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_gpio_phandles] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_gpio_requestf] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_i2c_bytewise] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_i2c_find] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_i2c_offset] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_i2c_offset_len] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_i2c_probe_empty] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_i2c_read_write] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_i2c_speed] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_leak] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_led_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_led_gpio] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_led_label] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_lifecycle] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_mmc_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_net_retry] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_operations] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_ordering] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_pci_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_pci_busnum] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_pci_swapcase] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_platdata] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_power_pmic_get] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_power_pmic_io] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_power_regulator_autoset] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_power_regulator_autoset_list] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_power_regulator_get] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_power_regulator_set_get_current] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_power_regulator_set_get_enable] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_power_regulator_set_get_mode] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_power_regulator_set_get_voltage] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_pre_reloc] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_ram_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_regmap_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_regmap_syscon] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_remoteproc_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_remove] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_reset_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_reset_walk] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_rtc_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_rtc_dual] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_rtc_reset] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_rtc_set_get] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_spi_find] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_spi_flash] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_spi_xfer] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_syscon_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_syscon_by_driver_data] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_timer_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_uclass] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_uclass_before_ready] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_uclass_devices_find] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_uclass_devices_find_by_name] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_uclass_devices_get] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_uclass_devices_get_by_name] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_usb_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_usb_flash] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_usb_keyb] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_usb_multi] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_usb_remove] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_usb_tree] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_usb_tree_remove] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_usb_tree_reorder] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_base] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_bmp] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_bmp_comp] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_chars] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_context] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_rotation1] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_rotation2] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_rotation3] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_text] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_truetype] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_truetype_bs] PASSED
test/py/tests/test_ut.py::test_ut[ut_dm_video_truetype_scroll] PASSED

======================= 84 tests deselected by '-kut_dm' =======================
================== 115 passed, 84 deselected in 3.77 seconds ===================
```

# 5. What is going on?

让我们从顶部开始。 演示命令位于 cmd/demo.c 中。 它执行通常的命令处理，然后：

```c
struct udevice *demo_dev;

ret = uclass_get_device(UCLASS_DEMO, devnum, &demo_dev);
```

UCLASS_DEMO 表示实现“演示”的设备类别。 其他类可能是 MMC、GPIO、散列或串行。 这个想法是该类中的设备都共享特定的工作方式。 该类向 U-Boot 提供所有这些设备的统一视图。

此函数查找演示 uclass 的设备。 给定设备号，我们可以找到该设备，因为所有设备都已在 UCLASS_DEMO uclass 中注册。

该设备会自动激活以供 uclass_get_device() 使用。

现在我们有了设备，我们可以执行以下操作：

```c
return demo_hello(demo_dev, ch);
```

这个函数在demo uclass 中。 它负责调用相关驱动程序的“hello”方法。 请记住，有两个驱动程序，该特定设备可能会使用其中一个或另一个。

demo_hello() 的代码位于 drivers/demo/demo-uclass.c 中：

```c
int demo_hello(struct udevice *dev, int ch)
{
        const struct demo_ops *ops = device_get_ops(dev);

        if (!ops->hello)
                return -ENOSYS;

        return ops->hello(dev, ch);
}
```

正如您所看到的，它只是调用相关的驱动程序方法。 其中之一位于 drivers/demo/demo-simple.c 中：

```c
static int simple_hello(struct udevice *dev, int ch)
{
        const struct dm_demo_pdata *pdata = dev_get_plat(dev);

        printf("Hello from %08x: %s %d\n", map_to_sysmem(dev),
               pdata->colour, pdata->sides);

        return 0;
}
```

因此，这是从顶部（命令执行）到底部（驱动程序操作）的旅程，但它留下了很多主题需要解决。

# 6. Declaring Drivers

驱动程序声明如下所示（请参阅 drivers/demo/demo-shape.c）：

```c
static const struct demo_ops shape_ops = {
        .hello = shape_hello,
        .status = shape_status,
};

U_BOOT_DRIVER(demo_shape_drv) = {
        .name   = "demo_shape_drv",
        .id     = UCLASS_DEMO,
        .ops    = &shape_ops,
        .priv_data_size = sizeof(struct shape_data),
};
```

该驱动程序有两个方法（hello 和 status），并且需要一些私有数据（一旦探测到驱动程序，就可以通过 dev_get_priv(dev) 访问）。 它是 UCLASS_DEMO 的成员，因此将在那里注册自己。

在 U_BOOT_DRIVER 中，还可以指定绑定和取消绑定的特殊方法，并在适当的时间调用这些方法。 对于许多驱动来说，希望只需要“探测”和“删除”。

U_BOOT_DRIVER 宏创建一个可从 C 访问的数据结构，因此驱动程序模型可以找到可用的驱动程序。

设备可以提供的方法记录在 device.h 标头中。 简而言之，它们是：

- bind - 使驱动程序模型了解设备（将其绑定到其驱动程序）
- unbind - 让驱动模型忘记设备
- of_to_plat - 将设备树数据转换为平台数据 - 见下文
- probe - 使设备可供使用
- remove - 删除设备，使其在再次探测之前无法使用

让设备工作的顺序是bind、of_to_plat（如果使用设备树）和probe。

# 7. Platform Data

注意：平台数据是旧的做事方式。 它基本上是一个 C 结构，传递给驱动程序以告诉它们特定于平台的设置，如寄存器地址、总线速度等。设备树现在是处理此问题的首选方式。 除非您有充分的理由不使用设备树（主要是您需要 SPL 中的串行支持，并且没有足够的 SRAM 用于缩减设备树和 libfdt 库），否则您应该远离平台数据。

如果您熟悉的话，平台数据就像 Linux 平台数据。 它提供用于启动设备的板特定信息。

为什么这些信息不只存储在设备驱动程序本身中？ 这个想法是设备驱动程序是通用的，原则上可以在任何具有该类型设备的板上运行。 例如，对于现代高度复杂的 SoC，IP 通常来自 IP 供应商，因此（例如）来自不同供应商的芯片上的 MMC 控制器可能是相同的。 当每个供应商的 SoC 上的 MMC 控制器几乎都相同时，为它们编写独立的驱动程序是没有意义的。 类似地，我们在一个 SoC 中可能有 6 个 UART，它们大多相同，但位于地址空间中的不同地址。

使用 UART 示例，我们有一个驱动程序，通过提供 6 批平台数据来实例化 6 次。 每批平台数据都会提供驱动程序名称和指向包含有关该实例的信息的结构的指针 - 例如 寄存器空间的地址。 其中一个 UARTS 可能支持 RS-485 操作 - 这可以作为一个标志添加到平台数据中，该标志针对该一个端口进行设置，并针对其余端口进行清除。

将您的驱动程序视为一段通用代码，它知道如何与设备通信，但需要知道它在哪里、任何变体/选项信息等等。 平台数据提供了通用代码与其绑定到特定板上的特定方式之间的链接。

平台数据的示例包括：

- IP块寄存器空间的基地址

- 配置选项，例如：
  
  - SPI 控制器的 SPI 极性和最大速度
  - 用于 I2C 设备的 I2C 速度
  - GPIO 设备中可用的 GPIO 数量

平台数据从哪里来？ 它要么保存在编译到 U-Boot 中的结构中，要么可以从设备树中解析（请参阅下面的“设备树”）。

有关如何编译它的示例，请参阅 demo-pdata.c，它设置了驱动程序名称及其关联平台数据的表。 驱动程序可以按照他们喜欢的方式解释数据 - 它基本上是板特定代码和通用驱动程序之间的通信方案，旨在在任何板上工作。

驱动程序可以通过 dev->info->plat 访问其数据。 这是平台数据的声明，通常出现在板文件中。

```c
static const struct dm_demo_pdata red_square = {
        .colour = "red",
        .sides = 4.
};

static const struct driver_info info[] = {
        {
                .name = "demo_shape_drv",
                .plat = &red_square,
        },
};

demo1 = driver_bind(root, &info[0]);
```

# 7. Device Tree

虽然 plat 很有用，但提供设备数据的更灵活的方法是使用设备树。 在 U-Boot 中，您应该尽可能使用它。 除非绝对必要，否则请避免发送使用 U_BOOT_DRVINFO() 宏的补丁。

对于设备树，我们将上面的代码替换为以下设备树片段：

```c
red-square {
        compatible = "demo-shape";
        colour = "red";
        sides = <4>;
};
```

这意味着我们不会在板文件中包含大量 U_BOOT_DRVINFO() 声明，而是将它们放在设备树中。 这种方法具有更多的通用性，因为只需使用不同的设备树，相同的板文件就可以支持多种类型的板（例如，具有相同的 SoC）。 另一个好处是可以使用 Linux 设备树，从而进一步简化 U-Boot 或 Linux 开发人员的主板启动任务（无论谁先到达主板！）。

实现此目的的最简单方法是向驱动程序添加一些成员：

```c
.plat_auto = sizeof(struct dm_test_pdata),
.of_to_plat = testfdt_of_to_plat,
```

“auto”功能允许在调用驱动程序的 of_to_plat() 方法之前为 plat 分配空间并归零。 驱动程序编写的 of_to_plat() 方法应该解析该设备的设备树节点并将其放置在 dev->plat 中。 因此，当稍后调用探测方法（以设置设备以供使用）时，平台数据将出现。

请注意，这两种方法都是可选的。 如果您提供 of_to_plat 方法，那么它将首先被调用（在激活期间）。 如果您提供探测方法，接下来将调用它。 有关更多详细信息，请参阅下面的驱动程序生命周期。

如果您不想自动分配 plat，则可以省略 plat_auto。 在这种情况下，您可以在 of_to_plat （或probe）方法中使用 malloc 来分配所需的内存，并且应该在remove 方法中释放它。

驱动程序模型树旨在反映设备树的模型树。 根驱动程序位于设备树偏移量 0（根节点“/”），其子级是根节点的子级。

为了使设备树有效，内容必须相对于设备树规范 (<https://www.devicetree.org/specifications/>) 或 doc/ 中找到的设备树绑定正确。 设备树绑定目录。 当不是 U-Boot 特定时，此目录中的绑定往往来自 Linux 内核。 因此，我们可能已经就如何描述和绑定特定设备做出了某些设计决策。 在大多数情况下，我们希望保留兼容性，而不对设备树源文件进行额外的更改。

# 8. Declaring Uclasses

演示 uclass 声明如下：

```c
UCLASS_DRIVER(demo) = {
        .id             = UCLASS_DEMO,
};
```

还可以为探针等指定特殊方法。uclass编号来自include/dm/uclass-id.h。 要添加新的 uclass，请添加到枚举的末尾，然后按上述方式声明您的 uclass。

# 9. Device Sequence Numbers

U-Boot 在许多情况下都从 0 开始对设备进行编号，例如在 I2C 和 SPI 总线的命令行中，以及串行端口的设备名称（serial0、serial1...）。 驱动程序模型支持这种编号并允许设备通过其“序列”进行定位。 此编号唯一标识其 uclass 中的设备，因此特定 uclass 中的两个设备不能具有相同的序列号。

序列号从 0 开始，但允许有间隙。 例如，主板可能有 I2C 总线 1、4、5，但没有 0、2 或 3。设备编号方式的选择取决于特定主板，并且在某些情况下可能由 SoC 设置。 虽然在序列中存在间隙的情况下自动对设备重新编号可能很诱人，但这可能会导致混乱，并且不是 U-Boot 的工作方式。

设备获取其序列号的位置由 DM_SEQ_ALIAS Kconfig 选项控制，该选项在 U-Boot 本身和 SPL 中可以具有不同的值。 如果未设置此选项，则忽略别名。

即使启用了 CONFIG_DM_SEQ_ALIAS，uclass 仍必须设置 DM_UC_FLAG_SEQ_ALIAS 标志，以便其设备按别名排序。

设置这些选项后，具有别名（例如“serial2”）的设备将获得该序列号（例如 2）。 其他设备将在所有别名和所有现有号码之后获取下一个可用号码。 这意味着，如果只有一个别名“serial2”，则未别名的串行设备将被分配 3 个或更多，其中 0 和 1 未使用。

如果未设置 CONFIG_DM_SEQ_ALIAS 或 DM_UC_FLAG_SEQ_ALIAS，则所有设备都将以从 0 开始的简单顺序获取序列号。为了查找下一个要分配的编号，驱动程序模型会扫描以查找最大的现有编号，然后使用下一个。 它并不试图填补空白。

```c
aliases {
        serial2 = "/serial@22230000";
};
```

这表明在名为“serial”的uclass中，命名节点（“/serial@22230000”）将被赋予序列号2。任何请求串行设备2的命令或驱动程序都将获得该设备。

更常见的是，您可以使用节点引用，它将扩展为完整路径：

```c
aliases {
        serial2 = &serial_2;
};
...
serial_2: serial@22230000 {
...
};
```

在本例中，别名解析为相同的字符串，但此版本更易于阅读。

设备序列号在设备绑定时解析，并且该编号在设备的生命周期内不会更改。

在某些情况下，uclass 必须分配序列号，因为严格的递增序列（首先绑定设备树节点）是不合适的。 PCI 总线就是一个例子。 在这种情况下，您可以设置 uclass DM_UC_FLAG_NO_AUTO_SEQ 标志。 设置此标志后，只有具有别名的设备才会按驱动程序模型分配一个编号。 剩下的就留给 uclass 来解决，例如 枚举总线时。

请注意，不允许更改设备的序列号（例如在驱动程序中）。 如果认为有必要，请在邮件列表上询问。

# 10. Bus Drivers

驱动程序模型的常见用途是实现总线，即提供对其他设备的访问的设备。 总线的示例包括 SPI 和 I2C。 通常，总线提供某种传输或转换，使得与总线上的设备对话成为可能。

驱动程序模型提供了一些有用的功能来帮助实现总线。 首先，总线可以请求其子节点存储一些“父数据”，这些数据可用于跟踪子节点状态。 其次，总线可以定义当子进程被探测或删除时调用的方法。 这与 uclass 驱动程序提供的方法类似。 第三，可以提供每个孩子的平台数据来指定孩子在总线上的地址等信息。 这在子probe()/remove()周期中持续存在。

为了一致性和易于实现，总线 uclass 可以指定每个子级的平台数据，以便该 uclass 中总线的所有子级都可以相同。 还有一些 uclass 方法可以在子级被绑定和探测时调用。

这里解释一下总线如何与 uclass 相适应可能会很有用。 考虑一个连接有多个设备的 USB 总线，每个设备都来自不同的（组成的）uclass：

```c
xhci_usb (UCLASS_USB)
   eth (UCLASS_ETH)
   camera (UCLASS_CAMERA)
   flash (UCLASS_FLASH_STORAGE)
```

每个设备都连接到 USB 总线上的不同地址。 总线设备想要存储该地址和一些其他信息，例如每个设备的总线速度。

为了实现这一点，总线设备可以在其三个子设备中使用 dev->parent_plat 。 如果总线驱动程序（或总线 uclass）的 per_child_plat_auto 具有非零值，则可以自动分配该值。 如果没有，则总线设备或 uclass 可以在探测子设备之前自行分配空间。

此外，总线驱动程序可以定义 child_pre_probe() 和 child_post_remove() 方法，以允许在子进程激活之前或停用之后进行一些处理。

类似地，总线 uclass 可以定义 child_post_bind() 方法来从设备树中获取每个子平台的数据并为子设备进行设置。 总线 uclass 还可以提供 child_pre_probe() 方法。 通常是总线 uclass 控制这些功能，因为它避免了每个驱动程序必须执行相同的处理。 当然，驾驶员仍然可以调整和覆盖这些活动。

请注意，控制此行为的信息位于公交车司机中，而不是孩子的信息中。 事实上，孩子可能不知道自己已连接到总线。 相同的子设备甚至可以在两种不同的总线类型上使用。 举个例子。 上面显示的“闪存”设备也可以连接在 SATA 总线上，也可以在没有总线的情况下独立连接：

```c
xhci_usb (UCLASS_USB)
   flash (UCLASS_FLASH_STORAGE)  - parent data/methods defined by USB bus

sata (UCLASS_AHCI)
   flash (UCLASS_FLASH_STORAGE)  - parent data/methods defined by SATA bus

flash (UCLASS_FLASH_STORAGE)  - no parent data/methods (not on a bus)
```

在上面您可以看到 xhci_usb/sata 的驱动程序控制着子进程的总线方法。 在第三个示例中，设备不在总线上，因此根本没有这些方法。 考虑闪存设备定义子方法的情况。 这些将用于其子级，并且与闪存设备所连接的总线的驱动程序定义的方法完全分开。 将设备附加到作为总线的父设备的行为会导致该设备开始表现得像总线设备，而不管它自己对此事的看法如何。

设备的 uclass 还可以包含该 uclass 私有的数据。 但请注意，总线上的每个设备可能是不同 uclass 的成员，并且此数据与总线上每个子设备的子数据无关。 总线的 uclass 控制着总线的子进程。

# 11. Driver Lifecycle

以下是设备在驱动程序模型中经历的阶段。 请注意，此处提到的所有方法都是可选的 - 例如 如果设备没有probe()方法，那么它不会被调用。 一个简单的设备可能实际定义的方法很少。

## 11.1 Bind stage

U-Boot 使用以下两种方法之一发现设备：

- 扫描 U_BOOT_DRVINFO() 定义。 U-Boot 查找每个指定的名称，以找到适当的 U_BOOT_DRIVER() 定义。 在这种情况下，没有可以提供driver_data的路径，但是U_BOOT_DRVINFO()可以提供plat。

- 扫描设备树定义。 U-Boot 查看设备树中的顶级节点。 它查看每个节点中的兼容字符串，并使用 U_BOOT_DRIVER() 结构的 of_match 表来查找每个节点的正确驱动程序。 在这种情况下，of_match表可能会提供driver_data值，但plat要稍后才能提供。

对于发现的每个设备，U-Boot 然后调用 device_bind() 创建一个新设备，初始化设备对象的各个核心字段，例如名称、uclass 和驱动程序，初始化设备对象的任何适用的可选字段，例如 of_offset、driver_data & plat，最后调用驱动程序的bind()方法（如果定义了）。

此时，所有设备都是已知的，并且绑定到它们的驱动程序。 为所有设备分配了一个“struct udevice”。 但是，没有任何内容被激活（根设备除外）。 从 U_BOOT_DRVINFO() 声明创建的每个绑定设备都将保存该声明中指定的 plat 指针。 对于从设备树创建的绑定设备，plat 将为 NULL，但 of_offset 将是导致设备被创建的设备树节点的偏移量。 设备的 uclass 已正确设置。

如果没有特别请求，设备的序列号将被分配，要么是请求的序列号，要么是下一个可用的序列号（在处理所有别名之后）。

设备的bind()方法允许执行简单的操作，但不应该扫描设备树节点，不初始化硬件，也不应该设置结构或分配内存。 所有这些任务都应该留给probe()方法。

请注意，与 Linux 相比，U-Boot 的驱动程序模型具有独立于绑定/取消绑定的探测/删除步骤。 部分原因是在 U-Boot 中探测设备可能会很昂贵，并且我们不想在需要它们之前或搬迁之后才这样做。

## 11.2 Reading ofdata

大多数设备在设备树中都有数据，它们可以读取这些数据来找出硬件寄存器的基地址和与驱动程序操作相关的参数。 这称为“ofdata”（开放固件数据）。

设备的of_to_plat()实现plat的分配和读取。 父级的 ofdata 总是在子级之前读取。

步骤是：

1. 如果 priv_auto 非零，则为设备分配设备私有空间并清零。 它将可以通过 dev->priv 进行访问。 驱动程序可以在其中放置任何它喜欢的内容，但应该将其用于运行时信息，而不是平台数据（平台数据应该是静态的并且在探测设备之前已知）。

2. 如果 plat_auto 非零，则分配平台数据空间。 这仅对设备树操作有用，否则您必须在 U_BOOT_DRVINFO() 声明中指定平台数据。 该空间已分配给设备并清零。 它将可以作为 dev->plat 进行访问。

3. 如果设备的 uclass 指定非零的 per_device_auto，则该空间也会被分配并清零。 它是为设备分配并存储在设备中的，但它是uclass数据。 由 uclass 驱动程序拥有。 设备可以访问它。

4. 如果设备的直接父设备指定了 per_child_auto，则分配该空间。 这是供父设备使用来跟踪与子设备相关的事情的。 例如，连接到 USB 主控制器的 USB 闪存棒可能会使用此空间。 控制器可以保存有关其每个子级的 USB 状态的信息。

5. 如果驱动程序提供了 of_to_plat() 方法，则调用该方法将设备树数据转换为平台数据。 这应该执行各种调用，例如 dev_read_u32(dev, …) 来访问节点并将结果信息存储到 dev->plat 中。 此后，无论设备是使用设备树节点还是 U_BOOT_DRVINFO() 结构绑定，设备都会以相同的方式工作。 无论哪种情况，平台数据现在都存储在平台结构中。 通常，您将使用 plat_auto 功能来指定平台数据结构的大小，U-Boot 将在进入 of_to_plat() 之前自动为您分配并将其归零。 但如果没有，您可以在 of_to_plat() 中自行分配。 请注意，最好在 of_to_plat() 中而不是在probe() 中进行所有设备树解码。 （除了混合配置和运行时数据的丑陋之外，有一天 U-Boot 可能会缓存定期停用/激活的设备的平台数据）。

6. 该设备被标记为“平台有效”。

请注意，ofdata 读取始终在探测开始之前完成（对于子级及其所有父级）。 因此，设备在探测时会经历两种不同的状态：读取平台数据和实际接触硬件以启动设备。

将探测与 ofdata 读取分开有助于处理 of-platdata，其中probe() 方法对于 DT/of-platdata 操作是通用的，但 of_to_plat() 方法的实现方式不同。

另一种情况是这种分离很有用。 ACPI 表的生成使用 of-platdata，但不想探测设备。 探测会导致 U-Boot 违反其设计原则之一，即它应该只探测所使用的设备。 对于 ACPI，我们希望为每个设备生成一个表，即使 U-Boot 不使用它。 事实上，甚至可能无法探测该设备 - 例如 不存在的 SD 卡将导致探测错误，但我们仍然必须告诉 Linux 有关 SD 卡连接器的信息，以防在 Linux 运行时使用它。

重要的是 of_to_plat() 方法实际上并不探测设备本身。 然而，在某些情况下，必须在 of_to_plat() 方法中探测其他设备。 一个例子是设备需要 GPIO 才能运行。 要选择 GPIO 显然需要探测 GPIO 设备。 当用于常见的核心设备（例如 GPIO、时钟、中断、复位等）时，这是可以的。

如果您的设备依赖其父设备设置合适的地址空间，以便 dev_read_addr() 正常工作，请确保父设备在 of_to_plat() 中有其设置代码。 如果它在probe方法中有它，那么你不能从子设备的of_to_plat()方法调用dev_read_addr()。 将其移至probe()。 像 PCI 这样的总线可能会违反这条规则。

## 11.3 Activation/probe

为了节省资源，U-Boot 中的设备会被延迟探测。 U-Boot 是引导加载程序，而不是操作系统。 许多设备在 U-Boot 运行期间从未使用过，探测它们需要时间、需要内存、可能会增加主循环的延迟等。

应通过 uclass 代码或通用设备代码（例如 device_find_global_by_ofnode()）探测设备。 Uclass 有所不同，但可以看到两个常见用例：

1. uclass 被要求查找特定设备，例如 SPI 总线 0，第一个片选 - 在这种情况下，应激活返回的设备。

2. uclass 被要求在任何支持它的设备上执行特定的功能，例如。 使用任何可以找到的 sysreset 重置主板 - 对于这种情况，核心 uclass 代码提供了迭代器，在返回每个设备之前激活每个设备，并且 uclass 通常实现一个遍历函数，该函数迭代 uclass 的所有设备并尝试执行所请求的操作 依次对每个函数进行操作，直至成功。

要激活设备 U-Boot，首先按上述方式读取数据，然后执行以下步骤（请参阅 device_probe()）：

1. 所有父设备都会被探测。 除非激活设备的前任设备（一直到根设备），否则无法激活设备。 这意味着（例如）I2C 驱动程序将要求激活其总线。

2. 调用设备的probe()方法。 这应该执行设备运行所需的任何操作。 这可能包括检查硬件是否确实存在、为硬件设置时钟以及将硬件寄存器设置为初始值。 probe()中的代码可以访问：
   
   - dev->plat 中的平台数据（用于配置）
   
   - dev->priv 中的私有数据（用于运行时状态）
   
   - dev->uclass_priv 中的 uclass 数据（用于 uclass 存储有关该设备的信息）
     
     &emsp;注意：如果你不使用 priv_auto 那么你需要自己在这里分配 priv 空间。 这同样适用于 plat_auto。 请记住在remove()方法中释放它们。

3. 该设备被标记为“已激活”

4. 调用 uclass 的 post_probe() 方法（如果存在）。 这可能会导致 uclass 执行一些内务处理，以将设备记录为 uclass 激活和“已知”的设备。

## 11.4 Running stage

该设备现已激活并可以使用。 从现在开始直到它被删除为止，所有上述结构都是可以访问的。 该设备出现在 uclass 的设备列表中（因此，如果该设备位于 UCLASS_GPIO 中，它将显示为 GPIO uclass 中的设备）。 这是设备的“运行”状态。

## 11.5 Removal stage

当不再需要该设备时，可以调用 device_remove() 将其删除。 这将执行相反的探测步骤：

1. 调用 uclass 的 pre_remove() 方法（如果存在）。 这可能会导致 uclass 执行一些内务处理，将设备记录为已停用且不再被 uclass“已知”。

2. 该设备的所有子设备都将被删除。 不允许将活动的子设备与非活动的父设备一起使用。 这意味着此时会递归地为所有子级调用 device_remove() 。

3. 调用设备的remove()方法。 在此阶段，没有任何内容被释放，因此平台数据、私有数据和 uclass 数据仍然存在。 这是可以关闭硬件的地方。 此时设备应完全处于非活动状态，为了确保 U-Boot 没有硬件正在运行，删除所有设备应该就足够了。

4. 设备内存被释放（平台数据、私有数据、uclass 数据、父数据）。

注意：由于 U_BOOT_DRVINFO() 的平台数据是使用静态指针定义的，因此在使用 remove() 方法期间不会取消分配。 对于使用设备树数据实例化的设备，平台数据将被动态分配，因此需要在remove()方法期间释放：

- 如果 plat_auto 非零，则释放会在解除绑定阶段的驱动程序模型核心内自动发生； 或者

- 当 plat_auto 为 0 时，分配（在probe() 中或最好是of_to_plat() 中）和remove() 中的释放都是驱动程序作者的责任。
5. 该设备被标记为不活动。 请注意，它仍然是绑定的，因此设备结构本身此时尚未释放。 如果设备再次激活，则循环从上述步骤 2 重新开始。

## 11.6 Unbind stage

设备未绑定。 这是真正破坏设备的步骤。 如果父母有孩子，这些孩子将首先被销毁。 此后该设备不存在并且其内存已被释放。

# 12. Special cases for removal

有些设备需要在调用操作系统之前进行清理。 例如，USB 驱动程序可能想要停止总线。 这可以在remove()方法中完成。 一些特殊标志用于确定是否删除设备：

DM_FLAG_OS_PREPARE - 指示设备需要为操作系统启动做好准备。 该设备将在操作系统启动之前被删除。

DM_REMOVE_ACTIVE_DMA - 表明该设备使用 DMA。 这实际上与 DM_FLAG_OS_PREPARE 相同，因此设备在操作系统启动之前被删除。

DM_FLAG_VITAL - 表示该设备对于其他设备的操作“至关重要”。 删除所有常规设备后，可以删除此设备。 这很有用，例如 对于时钟，需要在设备移除阶段处于活动状态。

dm_remove_devices_flags() 函数可用于根据驱动程序标志删除设备。

# 13. Error codes

驱动程序模型尝试以一致的方式使用错误代码，如下所示：

**-EAGAIN**

稍后再尝试，例如 依赖项未准备好

**-EINVAL**

参数无效，例如 dev_read_…() 失败或任何其他与设备树相关的访问。 当驱动程序方法传递了它认为无效或不支持的参数时也使用。

**-EIO**

执行 I/O 操作失败。 当本地设备（即 SOC 的一部分）未按预期工作时使用此功能。 如果无法与单独的设备通信，请使用 -EREMOTEIO，例如 通过 I2C 或 SPI 通道。

**-ENODEV**

请勿绑定设备。 这不应该用于指示探测设备时发生错误或用于任何其他目的，以免驱动程序模型混淆。 在驱动程序方法中使用 -ENODEV 没有任何意义，因为显然存在一个设备。

**-ENOENT**

未找到条目或对象。 当无法找到设备、文件或目录时（例如，按名称查找时）使用此选项，它还可以指示丢失的设备树子节点。

**-ENOMEM**

内存不足

**-ENOSPC**

空间不足（例如在缓冲区或有限大小的数组中）

**-ENOSYS**

功能未实现。 这是由驱动程序未实现特定方法的 uclass 返回的。 当未实现特定子方法时，驱动程序也可以返回它。 这在更广泛的代码库中进行了广泛检查，其中某个功能可能会也可能不会编译到 U-Boot 中。 它表明该功能不可用，但这通常只是正常操作。 请不要使用-ENOSUPP。 如果向方法提供了不正确或未知的参数（例如未知的时钟 ID），则返回 -EINVAL。

**-ENXIO**

找不到设备/地址。 当设备或地址无法获取或无效时使用。 如果 -ENOENT 已用于驱动程序中的其他内容，则它通常用于指示不同类型的问题。

**-EPERM**

这是 -1，因此一些较旧的代码可能会将其用作一般错误。 这表明不允许进行操作，例如 安全违规或策略限制。 如果设备未标记为预重定位使用，则在重定位之前绑定设备时会在内部返回该值。

**-EPFNOSUPPORT**

缺少uclass。 这是故意设置的一个不常见的错误代码，以便可以轻松区分。 如果您在 U-Boot 中很早就看到这一点，则意味着存在具有特定 uclass 的设备，但该 uclass 不存在（很可能是因为它未编译）。 在uclass.c 或lists.c 中启用DEBUG 以查看哪个uclass ID 或驱动程序导致了问题。

**-EREMOTEIO**

这表示通过通信链路（例如 I2C 或 SPI）与外设通信时出现错误。 它可能表明设备不存在或未按预期响应。

**-ETIMEDOUT**

硬件访问或某些其他操作已超时。 当有预期的响应时间并且超出了足够的余量以致可能出现问题时使用此方法。

不太常见的：

**-ECOMM**

使用不广泛，但与-EREMOTEIO 类似。 可用作辅助错误，以区分问题与 -EREMOTEIO。

**-EKEYREJECTED**

尝试删除与删除标志不匹配的设备。 请参阅 device_remove()。

**-EILSEQ**

Devicetree 读取失败，特别是尝试读取 string-listg 属性中不存在的字符串索引

**-ENOEXEC**

尝试在不在该 uclass 中的设备上使用该 uclass 方法。 目前很少检查这一点，因为它通常是编程错误并且浪费代码空间。 仅调试检查在这里很有用。

**-ENODATA**

Devicetree 读取错误，属性存在但没有与其关联的数据

**-EOVERFLOW**

Devicetree 读取错误，属性比预期长

**-EPROBE_DEFER**

当移除标记指示仅应移除重要设备时，尝试移除非重要设备

**-ERANGE**

当参数超出范围时由 regmap 函数返回。 这对于区分寄存器映射错误和探测设备时获得的其他错误非常有用。

驱动程序应使用相同的约定，以便事情按预期运行。 特别是，如果驱动程序无法探测，或者 uclass 操作失败，则错误代码是指示实际发生情况的主要方式。

由于代码大小膨胀，并且可能导致在正常操作中出现消息，因此不鼓励在驱动程序中打印错误消息。 例如，如果某个命令尝试两种不同的设备并正确使用其中一种探测，我们不希望显示错误消息，即使该命令本身可能显示警告或信息性消息。 理想情况下，驱动程序中的消息应仅在调试时显示，例如 通过使用 log_debug()，尽管在极端情况下可以使用 log_warning() 或 log_error()。

可以使用 log_msg_ret() 记录错误消息，以便启用 CONFIG_LOG 和 CONFIG_LOG_ERROR_RETURN 显示通过调用堆栈返回的错误代码的跟踪。 这可以是快速找出错误发生位置的便捷方法。 养成使用 return log_msg_ret(“here”, ret) 返回错误的习惯，而不仅仅是 return ret。 该字符串只需足够长即可在单个函数中找到，因为日志记录存储（并且可以使用 CONFIG_LOGF_FUNC 打印）生成它的函数。

# 14. Data Structures

驱动模型采用双向链表作为基本数据结构。 有些节点有多个列表贯穿其中。 一旦我们了解了瓶颈是什么，在极少数情况下创建更高效的数据结构可能是值得的。

# 15. Tag Support

有时，子系统将其自己的私有数据（或对象）关联到 DM 设备（即 struct udevice）以支持附加功能是有用的。

驱动程序模型中的标签支持将使我们能够动态地执行此操作，而不是修改“udevice”数据结构。 在初始版本中，我们将支持两种类型的属性：

- 带有 dm_tag_set_ptr() 的指针，以及
- dm_tag_set_val() 的 unsigned long

例如，UEFI 子系统利用该功能根据链接的 udevice 的生命周期来维护 efi_disk 对象。

虽然当前的实现非常简单，但随着该功能在 U-Boot 子系统中得到更广泛的使用，它将会得到发展。

# 16. Changes since v1

根据记录，此实现使用与原始补丁非常相似的方法，但至少进行了以下更改：

- 尝试积极删除样板文件，以便对于大多数驱动程序来说，几乎不需要或不需要编写“驱动程序模型”代码。
- 将一些数据从代码移动到数据结构中 - 例如 在驱动程序中存储指向驱动程序操作结构的指针，而不是将其传递给驱动程序绑定函数。
- 重命名一些结构，使它们更类似于 Linux（struct udevice 而不是 struct instance、struct plat 等）。
- 将名称“core”更改为“uclass”，意思是 U-Boot 类。 似乎这个概念与一类驱动程序（或子系统）有关。 我们不应该使用“class”，因为它是 C++ 保留字，因此 U-Boot 类（uclass）似乎比“core”更好。
- 删除“struct driver_instance”并仅使用单个“struct udevice”。 这消除了看似不必要的间接级别。
- 内置设备树支持，避免需要平台数据。
- 删除了驱动程序重定位的概念，只是让新的驱动程序（重定位后创建的）可以访问旧驱动程序的数据。 我觉得搬迁是一个非常特殊的情况，只适用于少数驱动程序，其中许多驱动程序无论如何都可以/将重新初始化。 因此处理这个问题的开销可能不值得。
- 实现了 GPIO 系统，力图保持简单。

# 17. Pre-Relocation Support

对于预重定位，我们只需调用驱动程序模型 init 函数即可。 只有标有 DM_FLAG_PRE_RELOC 或设备树“bootph-all”属性的驱动程序才会在重定位之前初始化。 这有助于减少驱动程序模型开销。 如果启用了设备树 (CONFIG_OF_CONTROL)，则该标志也适用于 SPL 和 TPL。

请注意，启用设备树后，设备树“bootph-all”属性可以提供更好的控制粒度，以在重定位之前绑定哪个设备。 当使用驱动程序的 DM_FLAG_PRE_RELOC 标志时，所有具有相同驱动程序的设备都会被绑定，这需要分配大量的内存。 当不使用设备树时，DM_FLAG_PRE_RELOC 是在重定位之前通过 U_BOOT_DRVINFO() 绑定静态声明设备的唯一方法。

通过在设备树节点中使用更专门的“bootph-pre-ram”和“bootph-pre-sram”标志，可以将其限制为特定的重定位步骤。 对于 U-Boot 本身，您可以使用“bootph-some-ram”，这意味着它将在重定位之前在 U-Boot 本身中进行处理（和驱动程序绑定），但在 SPL 或 TPL 中不可用。

为了减小 SPL 和 TPL 的大小，只有具有预重定位属性（“bootph-all”、“bootph-pre-ram”或“bootph-pre-sram”）的节点才会保留在其设备树中（请参阅 README.h）。 SPL 详细信息）； 其余节点始终处于绑定状态。

然后在搬迁后，我们将其丢弃并再次重新初始化驱动程序模型。 对于需要重定位前和重定位后设备之间某种连续性的驱动程序，我们可以提供对重定位前设备指针的访问，但这目前尚未实现（根设备指针已保存，但无法通过驱动程序模型使用） 应用程序编程接口）。

# 18. SPL Support

驱动程序模型可以在 SPL 下运行。 其高效的实现和较小的代码大小提供了较小的开销，除了最受限的系统之外，这对于所有系统来说都是可以接受的。

要在 SPL 中启用驱动程序模型，请定义 CONFIG_SPL_DM。 您可能还想考虑以下选项。 有关更多详细信息，请参阅主要自述文件。

- CONFIG_SPL_SYS_MALLOC_SIMPLE
- CONFIG_DM_WARN
- CONFIG_DM_DEVICE_REMOVE
- CONFIG_DM_STDIO

# 19. Enabling Driver Model

驱动模型正在逐步被引入U-Boot中。 当每个子系统获得支持时，就会创建一个 uclass 和一个 CONFIG，以启用该子系统的驱动程序模型。

例如 CONFIG_DM_SERIAL 启用串行驱动程序模型。 定义后，旧的串行支持将不会启用，并且您的串行驱动程序必须符合驱动程序模型。 如果未定义，则启用旧的串行支持，并且驱动程序模型不可用于串行。 这意味着当您转换驱动程序时，您必须转换其所有板，或者提供带驱动程序模型和不带驱动程序模型的驱动程序编译（通常这不是很难）。

有关可用驱动程序模型 CONFIG 选项的完整详细信息，请参阅主自述文件。

# 20. Things to punt for later

Uclass 在编译时静态编号。 可以将其更改为动态编号，但随后我们需要某种查找服务，可能是按名称搜索。 这效率稍低，因此暂时被排除在外。 动态编号的一个小优点可能是 uclass-id.h 中的合并冲突更少。
