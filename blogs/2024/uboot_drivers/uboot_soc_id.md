
# SOC ID Framework

# 1. Introduction

驱动程序模型 SOC ID 框架能够提供有关运行时使用的特定 SoC 的标识信息，并且还提供来自阵列的一组标识信息的匹配。 这对于启用 SoC 变体之间存在的驱动程序中的小怪癖非常有用，而使用设备树标志来实现这些小怪癖是不切实际的。 它基于UCLASS_SOC。

**UCLASS_SOC:**

- drivers/soc/soc-uclass.c
- include/soc.h

**Configuration:**

- CONFIG_SOC_DEVICE 由驱动程序根据需要选择。


# 2. Implementing a UCLASS_SOC provider

该框架的目的是允许 UCLASS_SOC 提供程序驱动程序提供有关运行时使用的 SoC 的标识信息。 该框架允许驱动程序定义返回标识字符串的 soc_ops。 所有 soc_ops 不需要定义，可以保留为 NULL，在这种情况下，框架将返回 -ENOSYS 并且在执行 soc_device_match 时不考虑该值。

由驱动程序实现者决定如何确定返回的信息，但一般来说，同一 SOC 应始终返回同一组标识信息。 返回的信息必须采用 NULL 终止字符串的形式。

有关可用 soc_ops 的文档以及可返回值的预期含义，请参阅 include/soc.h。 有关 UCLASS_SOC 提供程序驱动程序示例，请参阅 drivers/soc/soc_sandbox.c。


# 3. Using a UCLASS_SOC driver

该框架提供了直接检索和使用标识字符串的能力。 它还能够使用 soc_device_match 从不同的 SoC 数据集列表中返回匹配项。

可以定义“struct soc_attr”数组，每个数组包含特定 SoC 的 ID 信息，当传递给 soc_device_match 时，列表中每个条目的标识符值将与正在使用的 UCLASS_SOC 驱动程序提供的值进行比较 。 soc_device_match 将返回列表中与所有非空值匹配的第一个条目。

该框架的各种用途的示例可以在 test/dm/soc.c 中找到。


# 4. Describing the device using device tree

```c
chipid: chipid {
     compatible = "sandbox,soc";
};
```

DT 节点中所需要的只是与相应的 UCLASS_SOC 驱动程序兼容。
