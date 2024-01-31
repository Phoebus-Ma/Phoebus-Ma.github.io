
# How to port a serial driver to driver model

# 1. Introduction

这是将串行驱动程序转换为驱动程序模型的建议方法。 请随时根据您的想法和建议更新此文件。

- #ifdef 输出所有您自己的串行驱动程序代码 (#ifndef CONFIG_DM_SERIAL)
- 为您的主板、供应商或架构定义 CONFIG_DM_SERIAL
- 如果主板尚未使用驱动程序模型，则还需要 CONFIG_DM
- 然后您的板应该构建，但不会启动，因为没有串行驱动程序
- 在末尾添加 U_BOOT_DRIVER 部分（例如复制serial_s5p.c）
- 为驱动程序数据添加私有结构 - 避免使用静态变量
- 实现每个驱动程序方法，也许通过调用旧方法
- 您可能需要调整函数参数，以便新旧实现可以共享大部分现有代码
- 如果转换驱动程序的所有现有用户，请删除预驱动程序模型代码

就补丁而言，转换系列通常具有以下补丁： - 清理/准备转换驱动程序 - 添加驱动程序模型代码 - 转换至少一个现有板以使用驱动程序模型序列 - （如果没有剩余板不使用驱动程序 模型）删除旧代码。

这可能也是让您的主板也使用设备树的好时机。 主要涉及以下步骤：

- 定义 CONFIG_OF_CONTROL 和 CONFIG_OF_SEPARATE
- 将设备树文件添加到 arch/<arch>/dts
- 在那里更新 Makefile
- 将 stdout-path 添加到 /chosen 设备树节点（如果尚不存在）
- 构建并获取 u-boot-dtb.bin 以便您可以测试它
- 您的驱动程序现在可以使用设备树
- 对于SPL中的设备树，定义CONFIG_SPL_OF_CONTROL


# 2. Converting boards to CONFIG_DM_SERIAL

如果您的 SoC 有一个使用驱动程序模型的串行驱动程序（其中有 U_BOOT_DRIVER()），那么您可能仍然会发现您的板尚未转换。 要转换您的主板，请启用该选项并查看是否可以使其工作。

首先，如果您有一种调试电路板的方法（例如 JTAG 连接），您将会取得更大的成功。 如果调试 UART 失败，那么它是有用的，尽管由于您正在尝试让 UART 驱动程序运行，但它最终会干扰您的工作。

其次，虽然 UART 是一个相对简单的外设，但它可能需要相当多的部件才能启动并运行才能工作，例如正确的引脚复用、时钟、电源域，如果使用外部收发器，甚至可能需要 GPIO 。 查看使用相同 SoC 的其他板，以获取有关所需内容的线索。

第三，添加标签后，将它们放入 xxx-u-boot.dtsi 文件中，其中 xxx 是您的板名称或 SoC 名称。 您的 SoC 可能已经有一个文件，其中包含您需要的内容。 U-Boot 自动包含这些文件：请参阅为 U-Boot 添加调整。

以下是您可能需要考虑的一些事项：

1. 串行驱动程序本身需要在重定位之前存在，以便出现 U-Boot 横幅。 确保它在设备树中具有 bootph-all 标记，以便在 U-Boot 启动时绑定串行驱动程序。

例如，在 iMX8 上：

```c
lpuart3: serial@5a090000 {
    compatible = "fsl,imx8qm-lpuart";
    ...
};
```

将其放入您的 xxx-u-boot.dtsi 文件中：

```c
&lpuart3 {
    bootph-some-ram;
};
```

2. 如果您的串行端口需要特定的 pinmux 配置，您可能需要 pinctrl 驱动程序。 这还需要有一个 bootph-all 标签。 如果需要使正确的 pinctrl 可用，请注意任何子节点都具有相同的标记。

例如，在 RK3288 上，UART2 使用 uart2_xfer：

```c
uart2: serial@ff690000 {
    ...
    pinctrl-0 = <&uart2_xfer>;
};
```

其定义如下：

```c
pinctrl: pinctrl {
    compatible = "rockchip,rk3228-pinctrl";

    uart2: uart2 {
        uart2_xfer: uart2-xfer {
            rockchip,pins = <1 RK_PC2 RK_FUNC_2 &pcfg_pull_up>,
                  <1 RK_PC3 RK_FUNC_2 &pcfg_pull_none>;
    };
    ...
};
```

这意味着您必须使 uart2-xfer 节点及其所有父节点可用，因此请将其放入 xxx-u-boot.dtsi 文件中：

```c
&pinctrl {
    bootph-all;
};

&uart2 {
    bootph-all;
};

&uart2_xfer {
    bootph-all;
};
```

3. 这同样适用于电源域。 例如，如果必须启用特定的电源域才能使串行端口工作，则需要在搬迁之前确保其可用：

例如，在 iMX8 上，将其放入 xxx-u-boot.dtsi 文件中：

```c
&pd_dma {
    bootph-some-ram;
};

&pd_dma_lpuart3 {
    bootph-some-ram;
};
```

这同样适用于时钟。 确保当您的驱动程序请求时钟（通常使用 clk_get_by_index()）时，时钟可用。

一般来说，找不到所需设备会导致错误，如果调试 UART 正常工作，您可以捕获该错误。 U-Boot 将串行数据输出到调试 UART，直到真正的串行驱动程序接管为止。 该点由设置了 GD_FLG_SERIAL_READY 标志的 gd->flags 标记。 此更改发生在serial-uclass.c 中的serial_init() 中，因此在此之前将使用调试UART。 您可以在 putc() 中看到相关代码，例如：

```c
/* if we don't have a console yet, use the debug UART */
if (IS_ENABLED(CONFIG_DEBUG_UART) && !(gd->flags & GD_FLG_SERIAL_READY)) {
   printch(c);
   return;
}
... carries on to use the console / serial driver
```

请注意，在 device_probe() 中，如果 pinctrl 驱动程序失败，则对 pinctrl_select_state() 的调用会默默失败。 如果需要，您可以在那里添加临时支票。

为什么我们有所有这些标签？ 问题是，在重定位之前我们不想绑定所有驱动程序，因为内存有限并且CPU可能运行速度很慢。 如果没有这种优化，许多主板将无法启动，或者可能需要很长时间才能启动（例如数百毫秒）。 这些标签告诉 U-Boot 要绑定哪些驱动程序。

好消息是，这个问题通常可以由 SoC 解决，因此任何使用它的主板都可以正常工作。 但在某些情况下，有多个 UART 或多个 pinmux 选项，这意味着每个板可能需要进行一些定制。


# 3. Serial in SPL

SPL 中需要类似的过程，但在这种情况下使用 bootph-pre-ram 或 bootph-pre-sram 标签。 以与上述相同的方式添加这些节点，以确保 SPL 设备树包含所需的节点（有关其实际包含的内容，请参阅 spl/u-boot-spl.dtb）。


# 4. Removing old code

在某些情况下，使用驱动程序模型时可能不再需要初始化代码，例如设置引脚复用或启用时钟。 请务必将其删除。


# 5. Example patch

iMX7的看这个 [serial_patch](https://patchwork.ozlabs.org/project/uboot/patch/20220314232406.1945308-1-festevam@gmail.com/)。
