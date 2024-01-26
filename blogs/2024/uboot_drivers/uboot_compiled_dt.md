
# Compiled-in Device Tree / Platform Data

# 1. Introduction

设备树是U-Boot中的标准配置方法。 它用于定义系统中有哪些设备并向这些设备提供配置信息。

添加对 U-Boot 的 devicetree 访问的开销相当适中，在 Thumb 2 上大约为 3KB（加上 DT 本身的大小）。 这意味着大多数情况下最好使用devicetree进行配置。

然而，U-Boot 需要在一些非常受限的环境中工作。 其中包括具有严重内存限制的 SPL。 例如，某些 SoC 需要 16KB SPL 映像，其中必须包含完整的 MMC 堆栈。 在这种情况下，设备树访问的开销可能太大。

可以通过为其定义 C 结构来手动创建平台数据，并在 U_BOOT_DRVINFO() 声明中引用该数据。 这完全绕过了 devicetree 的使用，有效地创建了并行配置机制。 但它是 SPL 的一个可用选项。

作为替代方案，提供了“of-platdata”功能。 这会将 devicetree 内容转换为可编译为 SPL 二进制文件的 C 代码。 由于数据存储效率更高，这可以节省 3KB 的代码开销，或许还可以节省几百个字节。


# 2. How it works

该功能由 CONFIG OF_PLATDATA 启用。 这仅在 SPL/TPL 中可用，并且应使用以下命令进行测试：

```c
#if CONFIG_IS_ENABLED(OF_PLATDATA)
```

名为“dtoc”的工具将 devicetree 文件转换为一组结构声明（每个兼容节点一个），以及一组 U_BOOT_DRVINFO() 声明以及每个设备的实际平台数据。 作为示例，请考虑以下 MMC 节点：

```c
sdmmc: dwmmc@ff0c0000 {
        compatible = "rockchip,rk3288-dw-mshc";
        clock-freq-min-max = <400000 150000000>;
        clocks = <&cru HCLK_SDMMC>, <&cru SCLK_SDMMC>,
                 <&cru SCLK_SDMMC_DRV>, <&cru SCLK_SDMMC_SAMPLE>;
        clock-names = "biu", "ciu", "ciu_drv", "ciu_sample";
        fifo-depth = <0x100>;
        interrupts = <GIC_SPI 32 IRQ_TYPE_LEVEL_HIGH>;
        reg = <0xff0c0000 0x4000>;
        bus-width = <4>;
        cap-mmc-highspeed;
        cap-sd-highspeed;
        card-detect-delay = <200>;
        disable-wp;
        num-slots = <1>;
        pinctrl-names = "default";
        pinctrl-0 = <&sdmmc_clk>, <&sdmmc_cmd>, <&sdmmc_cd>, <&sdmmc_bus4>;
            vmmc-supply = <&vcc_sd>;
            status = "okay";
            bootph-all;
    };
```

其中一些属性由 U-Boot 在 CONFIG_OF_SPL_REMOVE_PROPS 选项的控制下删除。 其余的都处理好了。 这将产生以下 C 结构声明：

```c
struct dtd_rockchip_rk3288_dw_mshc {
        fdt32_t         bus_width;
        bool            cap_mmc_highspeed;
        bool            cap_sd_highspeed;
        fdt32_t         card_detect_delay;
        fdt32_t         clock_freq_min_max[2];
        struct phandle_1_arg clocks[4];
        bool            disable_wp;
        fdt32_t         fifo_depth;
        fdt32_t         interrupts[3];
        fdt32_t         num_slots;
        fdt32_t         reg[2];
        fdt32_t         vmmc_supply;
};
```

以及以下设备声明：

```c
/* Node /clock-controller@ff760000 index 0 */
...

/* Node /dwmmc@ff0c0000 index 2 */
static struct dtd_rockchip_rk3288_dw_mshc dtv_dwmmc_at_ff0c0000 = {
        .fifo_depth             = 0x100,
        .cap_sd_highspeed       = true,
        .interrupts             = {0x0, 0x20, 0x4},
        .clock_freq_min_max     = {0x61a80, 0x8f0d180},
        .vmmc_supply            = 0xb,
        .num_slots              = 0x1,
        .clocks                 = {{0, 456},
                                   {0, 68},
                                   {0, 114},
                                   {0, 118}},
        .cap_mmc_highspeed      = true,
        .disable_wp             = true,
        .bus_width              = 0x4,
        .u_boot_dm_pre_reloc    = true,
        .reg                    = {0xff0c0000, 0x4000},
        .card_detect_delay      = 0xc8,
};

U_BOOT_DRVINFO(dwmmc_at_ff0c0000) = {
        .name           = "rockchip_rk3288_dw_mshc",
        .plat       = &dtv_dwmmc_at_ff0c0000,
        .plat_size  = sizeof(dtv_dwmmc_at_ff0c0000),
        .parent_idx     = -1,
};
```

然后，该设备在运行时实例化，并且可以使用以下方式访问平台数据：

```c
struct udevice *dev;
struct dtd_rockchip_rk3288_dw_mshc *plat = dev_get_plat(dev);
```

这避免了在驱动程序中将设备树数据转换为平台数据的代码开销。 因此，of_to_plat() 方法在此类驱动程序中不应执行任何操作。

请注意，对于要与驱动程序匹配的平台数据，U_BOOT_DRVINFO() 声明的“name”属性必须与通过 U_BOOT_DRIVER() 声明的驱动程序匹配。 这实际上意味着需要一个 U_BOOT_DRIVER()，其“名称”对应于设备树“兼容”字符串（将其转换为 C 的有效名称后），因此每个“兼容”字符串都需要一个专用驱动程序。

为了使其更加灵活，可以使用 DM_DRIVER_ALIAS() 宏来声明驱动程序名称的别名，通常是“兼容”字符串。 该宏不生成代码，但由 dtoc 工具使用。 它必须与其关联的驱动程序位于同一文件中，最好位于其之后。

Parent_idx 是父 driver_info 结构在其链接器列表中的索引（由 U_BOOT_DRVINFO() 宏实例化）。 这用于支持 dev_get_parent()。

在构建过程中，dtoc 解析 U_BOOT_DRIVER() 和 DM_DRIVER_ALIAS() 以构建有效驱动程序名称和驱动程序别名的列表。 如果用于设备的“兼容”字符串与有效的驱动程序名称不匹配，则会根据驱动程序别名列表进行检查，以获得要使用的正确驱动程序名称。 如果在此步骤中未找到匹配项，则会发出警告以避免运行时故障。

如果节点具有多个兼容字符串，dtoc 会生成 #define 以使它们等效，例如：

```c
#define dtd_rockchip_rk3299_dw_mshc dtd_rockchip_rk3288_dw_mshc
```


# 3. Converting of-platdata to a useful form

当然，只要需要配置信息，就可以直接在驱动程序中使用 of-platdata。 但是，这意味着驱动程序将无法支持 devicetree，因为使用 devicetree 时，of-platdata 结构不可用。 如果 devicetree 可用，那么使用此结构就没有意义，因为该结构具有下面注意事项中提到的所有限制。

因此，建议仅在驱动程序的probe()方法中使用of-platdata结构。 它不能在 of_to_plat() 方法中使用，因为当平台数据已经存在时不会调用该方法。


# 4. How to structure your driver

驱动程序应始终支持 devicetree 作为选项。 of-platdata 功能旨在作为现有驱动程序的附加组件。

您的驱动程序应该在其probe()方法中转换plat结构。 现有的 devicetree 解码逻辑应保留在 of_to_plat() 方法中并用 #if 包装。

比如:

```c
#include <dt-structs.h>

struct mmc_plat {
#if CONFIG_IS_ENABLED(OF_PLATDATA)
        /* Put this first since driver model will copy the data here */
        struct dtd_mmc dtplat;
#endif
        /*
         * Other fields can go here, to be filled in by decoding from
         * the devicetree (or the C structures when of-platdata is used).
         */
        int fifo_depth;
};

static int mmc_of_to_plat(struct udevice *dev)
{
    if (CONFIG_IS_ENABLED(OF_REAL)) {
        /* Decode the devicetree data */
        struct mmc_plat *plat = dev_get_plat(dev);
        const void *blob = gd->fdt_blob;
        int node = dev_of_offset(dev);

        plat->fifo_depth = fdtdec_get_int(blob, node, "fifo-depth", 0);
    }

    return 0;
}

static int mmc_probe(struct udevice *dev)
{
        struct mmc_plat *plat = dev_get_plat(dev);

#if CONFIG_IS_ENABLED(OF_PLATDATA)
        /* Decode the of-platdata from the C structures */
        struct dtd_mmc *dtplat = &plat->dtplat;

        plat->fifo_depth = dtplat->fifo_depth;
#endif
        /* Set up the device from the plat data */
        writel(plat->fifo_depth, ...)
}

static const struct udevice_id mmc_ids[] = {
        { .compatible = "vendor,mmc" },
        { }
};

U_BOOT_DRIVER(mmc_drv) = {
        .name           = "mmc_drv",
        .id             = UCLASS_MMC,
        .of_match       = mmc_ids,
        .of_to_plat = mmc_of_to_plat,
        .probe          = mmc_probe,
        .priv_auto = sizeof(struct mmc_priv),
        .plat_auto = sizeof(struct mmc_plat),
};

DM_DRIVER_ALIAS(mmc_drv, vendor_mmc) /* matches compatible string */
```

请注意，struct mmc_plat 是在 C 文件中定义的，而不是在标头中。 这是为了避免需要在头文件中包含 dt-structs.h。 这个想法是将每个 of-platdata 结构的使用保持在尽可能小的代码区域。 每个结构只有一个驱动程序 C 文件，可以从 of-platdata 结构转换为驱动程序使用的标准结构。

在启用SPL_OF_PLATDATA的情况下，仍然使用plat_auto为平台数据分配空间。 这与正常行为不同，是通过使用 of-platdata 触发的（严格来说，它是触发此行为的非零 plat_size ）。

of-platdata 结构内容从 C 结构数据复制到新分配区域的开头。 在使用devicetree的情况下，平台数据被分配，并开始清零。 在这种情况下， of_to_plat() 方法仍应设置平台数据（并且 of-platdata 结构将不存在）。

SPL 必须使用 of-platdata 或 devicetree。 驱动程序不能同时使用两者，但它们必须支持 devicetree。 支持 of-platdata 是可选的。

当启用 CONFIG_SPL_OF_PLATDATA 时，设备树将变得不可访问，因为设备树访问代码未编译进去。推论是，如果板使用的所有驱动程序都支持它，则板只能转向使用 of-platdata。 让某些驱动程序需要设备树数据没有什么意义，因为这些驱动程序仍然需要 libfdt，并且不会有代码大小的好处。


# 5. Build-time instantiation

即使使用 of-platdata，驱动程序模型中也需要相当数量的代码。 可以让 U-Boot 在构建时处理设备的实例化，从而避免需要 device_bind() 代码和 device_probe() 的某些部分。

该功能由 CONFIG_OF_PLATDATA_INST 启用。

下面是一个示例设备，由 dtoc 生成：

```c
/*
 * Node /serial index 6
 * driver sandbox_serial parent root_driver
*/

#include <asm/serial.h>
struct sandbox_serial_plat __attribute__ ((section (".priv_data")))
   _sandbox_serial_plat_serial = {
   .dtplat = {
      .sandbox_text_colour   = "cyan",
   },
};
#include <asm/serial.h>
u8 _sandbox_serial_priv_serial[sizeof(struct sandbox_serial_priv)]
   __attribute__ ((section (".priv_data")));
#include <serial.h>
u8 _sandbox_serial_uc_priv_serial[sizeof(struct serial_dev_priv)]
   __attribute__ ((section (".priv_data")));

DM_DEVICE_INST(serial) = {
   .driver     = DM_DRIVER_REF(sandbox_serial),
   .name       = "sandbox_serial",
   .plat_      = &_sandbox_serial_plat_serial,
   .priv_      = _sandbox_serial_priv_serial,
   .uclass     = DM_UCLASS_REF(serial),
   .uclass_priv_ = _sandbox_serial_uc_priv_serial,
   .uclass_node   = {
      .prev = &DM_UCLASS_REF(serial)->dev_head,
      .next = &DM_UCLASS_REF(serial)->dev_head,
   },
   .child_head   = {
      .prev = &DM_DEVICE_REF(serial)->child_head,
      .next = &DM_DEVICE_REF(serial)->child_head,
   },
   .sibling_node   = {
      .prev = &DM_DEVICE_REF(i2c_at_0)->sibling_node,
      .next = &DM_DEVICE_REF(spl_test)->sibling_node,
   },
   .seq_ = 0,
};
```

这是驱动程序的一部分，供参考：

```c
static const struct udevice_id sandbox_serial_ids[] = {
   { .compatible = "sandbox,serial" },
   { }
};

U_BOOT_DRIVER(sandbox_serial) = {
   .name   = "sandbox_serial",
   .id   = UCLASS_SERIAL,
   .of_match    = sandbox_serial_ids,
   .of_to_plat  = sandbox_serial_of_to_plat,
   .plat_auto   = sizeof(struct sandbox_serial_plat),
   .priv_auto   = sizeof(struct sandbox_serial_priv),
   .probe = sandbox_serial_probe,
   .remove = sandbox_serial_remove,
   .ops   = &sandbox_serial_ops,
   .flags = DM_FLAG_PRE_RELOC,
};
```

DM_DEVICE_INST() 宏声明了一个 struct udevice，因此您可以看到成员来自该结构。 私有数据在上面声明为 _sandbox_serial_priv_serial，因此不需要运行时内存分配。 #include 行也会生成，因为 dtoc 在 U-Boot 源代码中搜索 struct sandbox_serial_priv 的定义并添加相关标头，以便代码编译时不会出现错误。

plat_ 成员设置为紧邻设备上方声明的 dtv 数据。 这与没有 of-platdata-inst 的情况类似，但节点内部的 dtplat 成员是更广泛的 _sandbox_serial_plat_serial 结构的一部分。 这是因为驱动程序声明了自己的平台数据，而dtoc生成的部分只能是其中的一部分。 dtplat 部分始终位于结构中的第一个部分。 如果设备没有 .plat_auto 字段，则可以使用简单的 dtv 结构，如下例所示：

```c
static struct dtd_sandbox_clk dtv_clk_sbox = {
   .assigned_clock_rates   = 0x141,
   .assigned_clocks   = {0x7, 0x3},
};

#include <asm/clk.h>
u8 _sandbox_clk_priv_clk_sbox[sizeof(struct sandbox_clk_priv)]
   __attribute__ ((section (".priv_data")));

DM_DEVICE_INST(clk_sbox) = {
   .driver    = DM_DRIVER_REF(sandbox_clk),
   .name      = "sandbox_clk",
   .plat_     = &dtv_clk_sbox,
```

这是驱动程序的一部分，供参考：

```c
static const struct udevice_id sandbox_clk_ids[] = {
   { .compatible = "sandbox,clk" },
   { }
};

U_BOOT_DRIVER(sandbox_clk) = {
   .name       = "sandbox_clk",
   .id         = UCLASS_CLK,
   .of_match   = sandbox_clk_ids,
   .ops        = &sandbox_clk_ops,
   .probe      = sandbox_clk_probe,
   .priv_auto  = sizeof(struct sandbox_clk_priv),
};
```

您可以看到 dtv_clk_sbox 仅具有 devicetree 内容，并且不需要 dtplat 分离，因为除了 devicetree 提供的数据（即没有 .plat_auto 字段）之外，驱动程序没有自己的平台数据。

双向链表是通过显式声明每个节点的值来处理的，如上例中的 .prev 和 .next 值所示。 由于 dtoc 知道设备的顺序，因此它可以将它们正确链接到适当的列表中。

驱动程序模型的功能之一是 uclass 能够为该 uclass 中的每个设备拥有少量私有数据。 这用于提供 uclass 可用于所有设备的通用数据结构，从而允许在通用代码中实现通用功能。 一个例子是 I2C，它存储总线速度。

类似地，父设备可以具有与其每个子设备相关联的数据。 这用于提供特定总线的所有子节点通用的信息。 对于 I2C 总线，这用于存储总线上每个子级的 I2C 地址。

这一切都由 dtoc 自动处理：

```c
#include <asm/i2c.h>
u8 _sandbox_i2c_priv_i2c_at_0[sizeof(struct sandbox_i2c_priv)]
   __attribute__ ((section (".priv_data")));
#include <i2c.h>
u8 _sandbox_i2c_uc_priv_i2c_at_0[sizeof(struct dm_i2c_bus)]
   __attribute__ ((section (".priv_data")));

DM_DEVICE_INST(i2c_at_0) = {
   .driver      = DM_DRIVER_REF(sandbox_i2c),
   .name      = "sandbox_i2c",
   .plat_   = &dtv_i2c_at_0,
   .priv_      = _sandbox_i2c_priv_i2c_at_0,
   .uclass   = DM_UCLASS_REF(i2c),
   .uclass_priv_ = _sandbox_i2c_uc_priv_i2c_at_0,
  ...
```

部分驱动，供参考：

```c
static const struct udevice_id sandbox_i2c_ids[] = {
   { .compatible = "sandbox,i2c" },
   { }
};

U_BOOT_DRIVER(sandbox_i2c) = {
   .name   = "sandbox_i2c",
   .id   = UCLASS_I2C,
   .of_match = sandbox_i2c_ids,
   .ops   = &sandbox_i2c_ops,
   .priv_auto   = sizeof(struct sandbox_i2c_priv),
};
```

I2C uclass的部分内容，供参考：

```c
UCLASS_DRIVER(i2c) = {
   .id         = UCLASS_I2C,
   .name       = "i2c",
   .flags      = DM_UC_FLAG_SEQ_ALIAS,
   .post_bind  = i2c_post_bind,
   .pre_probe  = i2c_pre_probe,
   .post_probe = i2c_post_probe,
   .per_device_auto   = sizeof(struct dm_i2c_bus),
   .per_child_plat_auto   = sizeof(struct dm_i2c_chip),
   .child_post_bind = i2c_child_post_bind,
};
```

这里，_sandbox_i2c_uc_priv_i2c_at_0 是 uclass 所必需的，但根据驱动程序模型的要求在设备中声明。 包含所需的头文件，以便代码编译时不会出现错误。 类似的机制用于子设备，但本示例未显示。

避免绑定设备并没有那么有用，但仍然需要在运行时分配 uclass。 因此 dtoc 也会生成 uclass 实例：

```c
struct list_head uclass_head = {
   .prev = &DM_UCLASS_REF(serial)->sibling_node,
   .next = &DM_UCLASS_REF(clk)->sibling_node,
};

DM_UCLASS_INST(clk) = {
   .uc_drv      = DM_UCLASS_DRIVER_REF(clk),
   .sibling_node   = {
      .prev = &uclass_head,
      .next = &DM_UCLASS_REF(i2c)->sibling_node,
   },
   .dev_head   = {
      .prev = &DM_DEVICE_REF(clk_sbox)->uclass_node,
      .next = &DM_DEVICE_REF(clk_fixed)->uclass_node,
   },
};
```

顶部是列表头。 驱动程序模型在启动时使用它，而不是创建自己的。

下面是一组 DM_UCLASS_INST() 宏，每个宏都声明一个 struct uclass。 双向链表适用于设备。

所有私有数据都放置在 .priv_data 部分中，以便它在生成的输出二进制文件中是连续的。


# 6. Indexes

U-Boot 在 linker_list 结构中存储驱动程序、设备和许多其他内容。 它们按名称排序，因此 dtoc 知道链接器运行时它们出现的顺序。 每个 driver_info / udevice 都由其在 linker_list 数组中的索引引用，在代码中称为“idx”。

当CONFIG_OF_PLATDATA_INST启用时，idx是udevice索引，否则是driver_info索引。 无论哪种情况，索引都用于使用 device_get_by_ofplat_idx() 来引用设备。 这使得手柄能够按预期工作。


# 7. Phases

U-Boot 分几个阶段运行，通常是 TPL、SPL 和 U-Boot 本身。 后者不使用 dtoc。

在极少数情况下，两个阶段使用不同的驱动程序。 例如，在 TPL 中可能不需要使用完整的 PCI 子系统，因此可以使用简单的驱动程序。

这在构建系统中只需编译一个驱动程序或另一个驱动程序（例如，用于 SPL 的 PCI 驱动程序 + uclass；用于 TPL 的 simple_bus）即可实现。 但是 dtoc 无法知道哪些代码是在哪个阶段编译的，因为它不检查 Makefile 或依赖关系图。

因此，为了使 dtoc 发挥作用，我们需要能够显式地标记驱动程序的阶段。 这是通过向驱动程序添加宏来完成的：

```c
/* code in tpl.c only compiled into TPL */
U_BOOT_DRIVER(pci_x86) = {
   .name   = "pci_x86",
   .id   = UCLASS_SIMPLE_BUS,
   .of_match = of_match_ptr(tpl_fake_pci_ids),
   DM_PHASE(tpl)
};


/* code in pci_x86.c compiled into SPL and U-Boot proper */
U_BOOT_DRIVER(pci_x86) = {
   .name   = "pci_x86",
   .id   = UCLASS_PCI,
   .of_match = pci_x86_ids,
   .ops   = &pci_x86_ops,
};
```

请注意，第二个驱动程序具有相同的名称，但没有 DM_PHASE()，因此它将用于 SPL 和 U-Boot。

另请注意，这仅影响 dtoc 生成的代码。 您仍然需要确保每个阶段中仅构建所需的驱动程序。


# 8. Header files

对于 OF_PLATDATA_INST，dtoc 必须在所使用的任何结构的生成代码中包含正确的头文件，以便代码能够编译。 例如，如果使用struct ns16550_plat，则代码必须包含ns16550.h头文件。

通常，dtoc 可以通过查找驱动程序使用的结构来检测驱动程序所需的头文件。 例如，如果驱动程序作为使用 struct ns16550_plat 的 .priv_auto，则 dtoc 可以搜索头文件以查找该结构的定义并使用该文件。

在某些情况下，枚举在驱动程序中使用，通常与 struct udevice_id 的 .data 字段一起使用。 由于 dtoc 不支持搜索这些标头，因此您必须使用 DM_HDR() 宏来告诉 dtoc 要使用哪个标头。 这作为包含在驱动程序定义中的宏起作用：

```c
static const struct udevice_id apl_syscon_ids[] = {
   { .compatible = "intel,apl-punit", .data = X86_SYSCON_PUNIT },
   { }
};

U_BOOT_DRIVER(intel_apl_punit) = {
   .name       = "intel_apl_punit",
   .id         = UCLASS_SYSCON,
   .of_match   = apl_syscon_ids,
   .probe      = apl_punit_probe,
   DM_HEADER(<asm/cpu.h>)    /* for X86_SYSCON_PUNIT */
};
```


# 9. Problems

本节介绍一些常见问题以及如何解决这些问题。


## 9.1 Driver not found

在某些情况下，您会看到类似这样的内容：

```bash
WARNING: the driver rockchip_rk3188_grf was not found in the driver list
```

驱动程序列表是驱动程序的列表，每个驱动程序都有一个名称。 该名称位于 U_BOOT_DRIVER() 声明中，重复两次，一次在括号中，一次作为 .name 成员。 例如，在以下声明中，驱动程序名称为rockchip_rk3188_grf：

```c
U_BOOT_DRIVER(rockchip_rk3188_grf) = {
     .name = "rockchip_rk3188_grf",
     .id = UCLASS_SYSCON,
     .of_match = rk3188_syscon_ids + 1,
     .bind = rk3188_syscon_bind_of_plat,
};
```

第一个名称 U_BOOT_DRIVER(xx) 用于创建链接器符号，以便可以在构建时访问驱动程序而无需任何开销。 第二个 (.name = “xx”) 在运行时当需要打印出驱动程序名称时使用。

dtoc 工具期望能够为设备树中的每个兼容字符串找到驱动程序。 例如，如果设备树具有：

```c
grf: grf@20008000 {
   compatible = "rockchip,rk3188-grf", "syscon";
   reg = <0x20008000 0x200>;
   bootph-pre-ram;
};
```

然后 dtoc 查看第一个兼容字符串 (“rockchip,rk3188-grf”)，将其转换为 C 标识符 (rockchip_rk3188_grf)，然后查找它。


# 9.2 Missing .compatible or Missing .id

有多种情况可能导致 dtoc 无法找到驱动程序，并且它会尝试对这些情况发出警告。 例如：

```bash
rockchip_rk3188_uart: Missing .compatible in drivers/serial/serial_rockchip.c
                 : WARNING: the driver rockchip_rk3188_uart was not found in the driver list
```

如果没有兼容的字符串，则 dtoc 无法使用驱动程序，即使运行时实际上并不需要兼容的字符串。

如果问题只是存在多个兼容字符串，则可以使用 DM_DRIVER_ALIAS() 宏来告诉 dtoc 这一点并避免出现问题。

还进行检查以确认引用的驱动程序具有 .compatible 成员和 .id 成员。 第一个提供兼容字符串数组，第二个提供 uclass ID。


# 9.3 Missing parent

使用设备时，其父设备也必须在场。 如果您看到类似以下的错误：

```bash
Node '/i2c@0/emul/emul0' requires parent node '/i2c@0/emul' but it is not in
   the valid list
```

它表明您使用的节点的父节点不存在于设备树中。 在此示例中，如果您查看设备树输出（例如构建目录中的 fdtdump tpl/u-boot-tpl.dtb），您可能会看到如下内容：

```c
emul {
    emul0 {
        compatible = "sandbox,i2c-rtc-emul";
        #emul-cells = <0x00000000>;
        phandle = <0x00000003>;
    };
};
```

在此示例中，“emul0”存在，但其父级“emul”没有属性。 为了减少设备树的大小，fdtgrep 已删除这些内容。 这表明两个节点具有不同的相位设置。 查看源.dts：

```c
i2c_emul: emul {
   bootph-pre-ram;
   reg = <0xff>;
   compatible = "sandbox,i2c-emul-parent";
   emul0: emul0 {
      bootph-all;
      compatible = "sandbox,i2c-rtc-emul";
      #emul-cells = <0>;
   };
};
```

您可以看到子节点“emul0”使用“bootph-all”，表示该节点存在于所有 SPL 版本中，但其父节点使用“bootph-pre-ram”表示它仅存在于 SPL 中，而不存在于 TPL 中。 对于 TPL 构建，这将失败并显示上述消息。 解决方法是更改“emul0”以使用相同的“bootph-pre-ram”条件，以便它不会像其父级一样出现在 TPL 中。


# 9.4 Link errors / undefined reference

有时 dtoc 没有为您找到问题，但出现问题并且您会收到链接错误，例如：

```bash
:(__u_boot_list_2_udevice_2_spl_test5+0x0): undefined reference to
   `_u_boot_list_2_driver_2_sandbox_spl_test'
/usr/bin/ld: dts/dt-uclass.o:(__u_boot_list_2_uclass_2_misc+0x8):
     undefined reference to `_u_boot_list_2_uclass_driver_2_misc'
```

第一个指示设备找不到其驱动程序。 这意味着有一个驱动程序“sandbox_spl_test”，但它没有编译到构建中。 检查您的 Kconfig 设置以确保它是。 如果您不希望在构建中出现这种情况，请调整您的阶段设置，例如 通过在节点中使用“bootph-pre-ram”将其从 TPL 构建中排除：

```c
spl-test5 {
        bootph-pre-sram;
        compatible = "sandbox,spl-test";
        stringarray = "tpl";
};
```

我们可以删除“bootph-pre-sram”行，这样该节点就不会出现在 TPL 设备树中，从而不需要驱动程序。

上面的第二个错误表明驱动程序需要 MISC uclass（因为它位于 MISC uclass 中），但 uclass 未在构建中编译。 上面的修复也可以修复这个错误。 但如果您确实希望在构建中使用此 uclass，请检查您的 Kconfig 设置以确保正在构建 uclass（在本例中为 CONFIG_MISC）。

另一个可能出现的错误是这样的：

```bash
spl/dts/dt-device.c:257:38: error: invalid application of ‘sizeof’ to
      incomplete type ‘struct sandbox_irq_priv’
   257 | u8 _sandbox_irq_priv_irq_sbox[sizeof(struct sandbox_irq_priv)]
       |                                      ^~~~~~
```

这表明 struct sandbox_irq_priv 没有在任何地方定义。 解决方案是添加 DM_HEADER() 行，如下所示，因此它包含在 dt-device.c 文件中：

```c
U_BOOT_DRIVER(sandbox_irq) = {
   .name             = "sandbox_irq",
   .id               = UCLASS_IRQ,
   .of_match = sandbox_irq_ids,
   .ops              = &sandbox_irq_ops,
   .priv_auto        = sizeof(struct sandbox_irq_priv),
   DM_HEADER(<asm/irq.h>)
};
```

请注意，上面没有进行依赖性检查，因此当您更新源文件（此处为 irq_sandbox.c）时，U-Boot 不会重新生成 dt-device.c 文件。 您需要先运行 make mrproper 才能获得新的构建。

另一个可能出现的错误是这样的：

```bash
spl/dts/dt-device.c:257:38: error: invalid application of ‘sizeof’ to
      incomplete type ‘struct sandbox_irq_priv’
   257 | u8 _sandbox_irq_priv_irq_sbox[sizeof(struct sandbox_irq_priv)]
       |                                      ^~~~~~
```

这表明 struct sandbox_irq_priv 没有在任何地方定义。 解决方案是添加 DM_HEADER() 行，如下所示，因此它包含在 dt-device.c 文件中：

```c
U_BOOT_DRIVER(sandbox_irq) = {
   .name             = "sandbox_irq",
   .id               = UCLASS_IRQ,
   .of_match = sandbox_irq_ids,
   .ops              = &sandbox_irq_ops,
   .priv_auto        = sizeof(struct sandbox_irq_priv),
   DM_HEADER(<asm/irq.h>)
};
```

请注意，上面没有进行依赖性检查，因此当您更新源文件（此处为 irq_sandbox.c）时，U-Boot 不会重新生成 dt-device.c 文件。 您需要先运行 make mrproper 才能获得新的构建。


# 10. Caveats

此功能存在各种复杂性，这意味着仅应在绝对必要时使用它，即在内存有限的 SPL 中。 值得注意的警告包括：

- 设备树不描述数据类型。 但 C 代码必须为每个属性定义一个类型。 这些是使用启发式猜测的，但在一些相当常见的情况下这是错误的。 例如，8 字节值被视为 2 项整数数组，并且是字节交换的。 不存在的布尔值意味着“假”，但不能包含在结构中，因为在 devicetree 文件中通常没有提及它。

- 节点和属性的命名是自动的。 这意味着它们遵循设备树中的命名，这可能会导致 C 标识符看起来有点奇怪。

- 给定属性名称是不可能找到值的。 代码必须直接在代码中使用关联的 C 成员变量。 这使得代码在面对设备树更改时不太健壮。 为了避免出现第二个具有相似成员和名称的结构，您需要使用 DM_DRIVER_ALIAS() 显式将其声明为别名。

- 平台数据以 C 结构形式提供给驱动程序。 驱动程序必须使用相同的结构来访问数据。 由于驱动程序通常也支持 devicetree，因此必须使用 #ifdef 来分离出此代码，因为这些结构仅在 SPL 中可用。 通过使结构在 SPL 之外可用，可以相当容易地解决此问题，以便可以使用 IS_ENABLED()。

- 使用 CONFIG_OF_PLATDATA_INST，所有绑定都在构建时发生，这意味着（默认情况下）不可能从 C 代码调用 device_bind()。 这意味着所有设备都必须具有关联的设备树节点和兼容字符串。 例如，如果 GPIO 设备当前在其 bind() 方法中创建子设备，则它将无法与 CONFIG_OF_PLATDATA_INST 一起使用。 无论如何，这可以说是不好的做法，应该更新 devicetree 绑定以声明子设备的兼容字符串。 可以禁用 OF_PLATDATA_NO_BIND 但不建议这样做，因为它会增加代码大小。


# 11. Internals

## 11.1 Generated files

启用后，dtoc 会生成以下五个文件：

**include/generated/dt-decl.h (OF_PLATDATA_INST only)**

&emsp;包含所有驱动程序、设备和 uclass 的声明。 这允许通过名称来定位任何 struct udevice、struct driver 或 struct uclass。

**include/generated/dt-structs-gen.h**

&emsp;包含所使用的设备树节点的结构定义。 这和没有 OF_PLATDATA_INST 是一样的。

**spl/dts/dt-plat.c (only with !OF_PLATDATA_INST)**

&emsp;包含 U-Boot 在启动时用于绑定设备的 U_BOOT_DRVINFO() 声明。 参见上面的例子。

**spl/dts/dt-device.c (only with OF_PLATDATA_INST)**

&emsp;包含可在运行时使用的每个设备的 DM_DEVICE_INST() 声明。 这些与它们使用的任何私有/平台数据一起在文件中声明。 每个设备都有一个 idx，如上所述。 由于每个设备都必须是双链表的一部分，因此节点也在代码中声明。

**spl/dts/dt-uclass.c (only with OF_PLATDATA_INST)**

&emsp;包含每个可在运行时使用的 uclass 的 DM_UCLASS_INST() 声明。 这些与 uclass 本身（.priv_auto 成员）关联的任何私有数据一起在文件中声明。 由于每个 uclass 必须是双链表的一部分，因此节点也在代码中声明。

如果启用 CONFIG_SPL_OF_PLATDATA，则 dt-structs.h 文件包含生成的文件 (include/ generated/dt-structs.h)。 否则（例如在 U-Boot 中）这些结构不可用。 这可以防止它们被无意中使用。 所有用法都必须用 #if CONFIG_IS_ENABLED(OF_PLATDATA) 括起来。

dt-plat.c 文件包含设备声明，并且是在 spl/dt-plat.c 中构建的。


## 11.2 CONFIG options

多个 CONFIG 选项用于控制 of-platdata 的行为，所有选项均适用于 SPL 和 TPL：

**OF_PLATDATA**

&emsp;这是启用 of-platdata 功能的主要选项。

**OF_PLATDATA_PARENT**

&emsp;这允许 device_get_parent() 工作。 如果没有这个，所有设备都作为根节点的直接子节点存在。 对于 I2C 等总线来说，此选项非常理想（即使不是绝对必要）。

**OF_PLATDATA_INST**

&emsp;这控制构建时设备的实例化。 禁用它后，仅创建 U_BOOT_DRVINFO() 记录，U-Boot 在启动时处理 device_bind() 中的绑定。 启用后，仅创建 DM_DEVICE_INST() 和 DM_UCLASS_INST() 记录，运行时不需要 device_bind()。

**OF_PLATDATA_NO_BIND**

&emsp;这控制是否支持 device_bind()。 默认情况下，它是通过 OF_PLATDATA_INST 启用的，因为减少代码大小实际上是该功能的要点。 如果需要，可以将其禁用，但从长远来看不太可能得到支持。

**OF_PLATDATA_DRIVER_RT**

&emsp;这控制 struct driver_rt 记录是否被 U-Boot 使用。 通常，当设备绑定时，U-Boot 将设备指针存储在这些记录之一中。 系统中的每个 struct driver_info 都有一个，即与这些记录绑定的每个设备都有一个。 它提供了一种在代码中定位设备的方法，并由 device_get_by_ofplat_idx() 使用。 该选项始终与 of-platdata 一起启用，前提是 OF_PLATDATA_INST 未启用。 在这种情况下，这些记录是无用的，因为我们没有任何 struct driver_info 记录。

**OF_PLATDATA_RT**

&emsp;这控制 struct udevice_rt 记录是否被 U-Boot 使用。 它将可更新字段从 struct udevice（当前只有标志）移动到一个单独的结构中，从而允许将记录保存在只读内存中。 如果 OF_PLATDATA_INST 启用，则通常会启用它。 此选项还控制私有数据是在原位使用，还是首先复制到分配的区域中。 同样，这是为了允许 dtoc 生成的代码声明的私有数据位于只读存储器中。 请注意，对私有数据的访问必须通过访问器函数（例如 dev_get_priv()）完成，以便处理重定位。

**READ_ONLY**

&emsp;这表明不应修改 dtoc 生成的数据。 实际上，U-Boot 中只有少数字段发生了更改，例如设备标志。 此选项导致它们移动到分配的空间中（请参阅 OF_PLATDATA_RT）。 另外，由于当某些节点无法更新时，通常不可能更新双向链表，因此启用 OF_PLATDATA_NO_BIND。


## 11.3 Data structures

of-platdata 使用了一些额外的数据结构：

**struct udevice_rt**

&emsp;设备的运行时信息。 当 OF_PLATDATA_RT 启用时，它保存每个设备的标志，因此 struct udevice 可以通过 U-Boot 保持不变，并且可能驻留在只读存储器中。 然后通过 dev_get_flags() 和 dev_or_flags() 等函数访问标志。 该数据结构在启动时分配，其中私有数据也被复制。 所有标志值都从 0 开始，任何更改都由 dev_or_flags() 和 dev_bic_flags() 处理。 将标志设置为 DM_FLAG_BOUND 或 DM_FLAG_BOUND | 会更正确。 DM_FLAG_ALLOC_PDATA，但由于没有绑定/取消绑定设备的代码，也没有分配/释放私有数据/平台数据的代码，所以没关系。

**struct driver_rt**

&emsp;struct driver_info 记录的运行时信息。 当 OF_PLATDATA_DRIVER_RT 启用时，它保存指向每个记录创建的设备的指针。 这是必需的，以便可以从 C 代码定位设备。 具体来说，代码可以使用 DM_DRVINFO_GET(name) 来获取对特定结构 driver_info 的引用，其中 name 是 devicetree 节点的名称。 这非常方便。 它也很快，因为不需要搜索或字符串比较。 该数据结构在启动时分配，由 device_bind() 填充并由 device_get_by_ofplat_idx() 使用。


## 11.4 Other changes

of-platdata 还进行了一些其他更改：

**Accessor functions**

&emsp;一直鼓励通过 dev_get_priv() 等函数访问私有/平台数据。 对于 OF_PLATDATA_RT，这是至关重要的，因为 priv_ 和 plat_ （等）值指向 dtoc 生成的数据，而不是有时在启动时进行的读写副本。 更改私有/平台数据指针始终是不鼓励的（API 被标记为内部），但对于 OF_PLATDATA_RT 目前一般不支持，因为它假设所有此类指针都指向重新定位的数据。 另请注意，将结构体成员重命名为尾随下划线的部分原因是为了让人们意识到不应直接访问它们。

**gd->uclass_root_s**

&emsp;通常U-Boot在这里设置uclass列表的头部，并使gd->uclass_root指向它。 使用 OF_PLATDATA_INST，dtoc 在 dt-uclass.c 中生成 uclass_head 声明，因为它需要将头节点链接到列表中。 在这种情况下，不使用 gd->uclass_root_s，U-Boot 只是使 gd->uclass_root 指向 uclass_head。

**gd->dm_driver_rt**

&emsp;它保存一个指向 struct driver_rt 记录列表的指针，每个 struct driver_info 记录一个。 该列表按 U_BOOT_DRVINFO(name) 中使用的名称按字母顺序排列，并按 idx 索引，第一个记录的索引为 0。仅在未启用 OF_PLATDATA_INST 时使用。 这是通过宏访问的，以便可以在 IS_ENABLED() 内部使用它，而不是在 C 代码中不存在 #ifdef 时要求它。

**gd->dm_udevice_rt**

&emsp;它保存一个指向 struct udevice_rt 记录列表的指针，每个 struct udevice 记录一个。 该列表按 DM_DEVICE_INST(name)（设备树节点的 C 版本）中使用的名称按字母顺序排列，并按 idx 索引，第一个记录的索引为 0。仅在启用 OF_PLATDATA_INST 时使用。 这是通过宏访问的，以便可以在 IS_ENABLED() 内部使用它，而不是在 C 代码中不存在 #ifdef 时要求它。

**gd->dm_priv_base**

&emsp;当 OF_PLATDATA_RT 启用时，每个设备的私有/平台数据将在启动时由 U-Boot 复制到分配的区域中。 这指向该区域。 然后，对访问器函数（例如 dev_get_priv()）的所有调用都会从调用者提供的指针（假设位于 __priv_data_start 和 __priv_data_end 之间）转换到新分配的区域。 该成员通过宏访问，以便可以在 IS_ENABLED() 内部使用，而不是在 C 代码中需要 #ifdef（当它不存在时）。

**struct udevice->flags_**

&emsp;当 OF_PLATDATA_RT 启用时，设备标志不再是 struct udevice 的一部分，而是保存在 struct udevice_rt 中，如上所述。 标志是通过函数访问的，例如 dev_get_flags() 和 dev_or_flags()。

**struct udevice->node_**

&emsp;当OF_PLATDATA启用时，运行时没有devicetree，因此不需要该字段。 删除它只是为了节省空间。

**DM_PHASE**

&emsp;该宏用于指示驱动程序适用于 U-Boot 的哪个阶段。 详情请参阅上文。

**DM_HDR**

&emsp;该宏用于指示 dtoc 应使用哪个头文件来允许驱动程序声明正确编译。 详情请参阅上文。

**device_get_by_ofplat_idx()**

&emsp;曾经有一个名为 device_get_by_driver_info() 的函数，它查找 struct driver_info 指针并返回从中创建的 struct udevice 。 它只能与 of-platdata 一起使用。 这已被删除，以支持 device_get_by_ofplat_idx()，它使用 idx，即 linker_list 中 struct driver_info 或 struct udevice 的索引。 同样，struct phandle_0_arg（等）结构已更新为使用此索引，而不是指向 struct driver_info 的指针。

**DM_DRVINFO_GET**

&emsp;由于我们现在使用索引从 struct phandle_0_arg 等获取驱动程序，因此该内容已被删除。

**Two-pass binding**

&emsp;原始的 of-platdata 尝试在生成的文件中对 U_BOOT_DRVINFO() 进行排序，以便在子级之前声明父级。 这很方便，因为它避免了 U-Boot 中的任何特殊代码。 对于 OF_PLATDATA_INST 这不起作用，因为 idx 值依赖于对所有内容使用字母顺序，因此 dtoc 和 U-Boot 的 linker_lists 就 idx 值达成一致。 然后设备按照 idx 的顺序进行绑定，而不考虑父/子关系。 因此，设备绑定现在分多次进行，父母先于孩子绑定。 这很重要，以便孩子们可以在需要时在 bind() 方法中找到他们的父母。

**Root device**

&emsp;根设备通常由 U-Boot 绑定，但对于 OF_PLATDATA_INST 则不能，因为绑定需要在构建时完成。 因此，在本例中，dtoc 使用 dt-device.c 中的 DM_DEVICE_INST() 设置根设备，并且 U-Boot 使用它。 当 OF_PLATDATA_INST 未启用时，U-Boot 通常会忽略根节点，并且不会为其创建 U_BOOT_DRVINFO() 记录。 这意味着 struct driver_info 使用的 idx 编号（当 OF_PLATDATA_INST 禁用时）和 struct udevice 使用的 idx 编号（当启用 OF_PLATDATA_INST 时）不同，因为一个有根节点，而另一个没有。 这实际上并不重要，因为实际上只有其中一个用于任何特定的构建，但如果比较索引值并打开和关闭 OF_PLATDATA_INST，则值得记住。

**__priv_data_start and __priv_data_end**

&emsp;dtoc 声明的私有/平台数据全部收集在链接器部分中，这些符号标记了它的开始和结束。 这允许 U-Boot 在需要时将该区域重新定位到新位置（使用 OF_PLATDATA_RT）

**dm_priv_to_rw()**

&emsp;该函数将 dtoc 生成的私有或平台数据指针值转换为可由 U-Boot 使用的指针值。 除非启用了 OF_PLATDATA_RT，否则它是一个 NOP，在这种情况下，它将地址转换为重定位区域。 请参阅上文了解更多信息。

以前需要的 dm_populate_phandle_data() 函数现在已被删除，因为 dtoc 可以直接从 dt-plat.c 寻址驱动程序，并且不需要在运行时修复问题。

pylibfdt Python 模块用于访问设备树。


# 12. Credits

这是 Tom Rini <trini@konsulko.com> 想法的实现。


# 13. Future work

考虑以编程方式读取绑定文件而不是设备树内容。

允许在 C 代码中使用 IS_ENABLED() 而不是 #if。
