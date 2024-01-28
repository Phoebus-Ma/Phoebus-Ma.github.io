
# PCI with Driver Model

# 1. How busses are scanned

任何配置读取都将在 pci_read_config() 处结束。 这使用 uclass_get_device_by_seq() 来获取特定总线号的 PCI 总线。 需要首先请求总线号 0，设备树文件中的别名将指向正确的设备：

```c
aliases {
        pci0 = &pcic;
};

pcic: pci@0 {
        compatible = "sandbox,pci";
        ...
};
```

如果没有别名，设备将在设备树中按顺序编号。

对 uclass_get_device() 的调用将导致 PCI 总线被探测。 这会扫描总线以查找可用设备。 这些设备绑定到适当的驱动程序（如果可用）。 如果没有驱动程序，那么它们将绑定到不执行任何操作的通用 PCI 驱动程序。

探测总线后，可用设备将出现在该总线下的设备树中。

请注意，这一切都是根据需要在懒惰的基础上完成的，因此直到 PCI 上发生某些事情（例如：调用 pci_find_devices()）为止，它将不会被探测到。

PCI 设备可以出现在扁平化设备树中。 如果他们这样做，他们的节点通常包含无法从设备的 PCI ID 或 PCI 类导出的额外信息。 每个 PCI 设备节点必须有一个 <reg> 属性，如 IEEE Std 1275-1994 PCI 总线绑定文档 v2.1 所定义。 兼容字符串列表是可选的，通常不需要，因为 PCI 是可发现的总线，尽管有合理的例外。 如果存在兼容字符串，则对其进行匹配优先于 PCI ID 和 PCI 类。

注意，我们必须使用与硬件相同的总线层次结构来描述 PCI 设备，否则驱动程序模型在 PCI 总线枚举期间无法检测到正确的父/子关系，因此 PCI 设备将不会相应地绑定到其驱动程序。 一个工作示例如下：

```c
pci {
        #address-cells = <3>;
        #size-cells = <2>;
        compatible = "pci-x86";
        bootph-all;
        ranges = <0x02000000 0x0 0x40000000 0x40000000 0 0x80000000
                  0x42000000 0x0 0xc0000000 0xc0000000 0 0x20000000
                  0x01000000 0x0 0x2000 0x2000 0 0xe000>;

        pcie@17,0 {
                #address-cells = <3>;
                #size-cells = <2>;
                compatible = "pci-bridge";
                bootph-all;
                reg = <0x0000b800 0x0 0x0 0x0 0x0>;

                topcliff@0,0 {
                        #address-cells = <3>;
                        #size-cells = <2>;
                        compatible = "pci-bridge";
                        bootph-all;
                        reg = <0x00010000 0x0 0x0 0x0 0x0>;

                        pciuart0: uart@a,1 {
                                compatible = "pci8086,8811.00",
                                                "pci8086,8811",
                                                "pciclass,070002",
                                                "pciclass,0700",
                                                "x86-uart";
                                bootph-all;
                                reg = <0x00025100 0x0 0x0 0x0 0x0
                                       0x01025110 0x0 0x0 0x0 0x0>;
                                ......
                        };

                        ......
                };
        };

        ......
};
```

在此示例中，根 PCI 总线节点是与“pci-x86”驱动程序匹配的“/pci”。 它有一个子节点“pcie@17,0”和驱动程序“pci-bridge”。 “pcie@17,0”还有子节点“topcliff@0,0”，它也是一个“pci-bridge”。 在该桥下，描述了 PCI UART 设备“uart@a,1”。 这准确地反映了硬件总线层次结构：在根 PCI 总线上，有一个 PCIe 根端口连接到下游设备 Topcliff 芯片组。 Topcliff芯片组内部有一个PCIe-to PCI桥，所有芯片组集成设备（例如PCI UART设备）都位于PCI总线上。 与设备树中的其他设备一样，如果我们想在重定位之前绑定 PCI 设备，则必须在每个节点中声明“bootph-all”。

如果 PCI 设备未在设备树中列出，则可以使用 U_BOOT_PCI_DEVICE 指定用于该设备的驱动程序。 设备树优先于 U_BOOT_PCI_DEVICE。 请注意，对于 U_BOOT_PCI_DEVICE，只有具有 DM_FLAG_PRE_RELOC 的驱动程序才会在重定位之前绑定。 如果设备树和 U_BOOT_PCI_DEVICE 均未提供，则将使用内置驱动程序（pci_bridge_drv 或 pci_generic_drv）。


# 2. Sandbox

对于沙箱，我们需要为总线上的每个设备提供一个设备模拟器，因为没有真正的 PCI 总线。 这是通过在设备树节点中查找模拟器驱动程序来实现的。 例如：

```c
pci@1f,0 {
        compatible = "pci-generic";
        reg = <0xf800 0 0 0 0>;
        sandbox,emul = <&emul_1f>;
};
pci-emul {
        compatible = "sandbox,pci-emul-parent";
        emul_1f: emul@1f,0 {
                compatible = "sandbox,swap-case";
                #emul-cells = <0>;
        };
};
```

这意味着该总线位置有一个“沙箱、交换箱”驱动程序。 请注意，“reg”值中的第一个单元格是总线/设备/功能。 请参阅 PCI_BDF() 了解编码（它也在 IEEE Std 1275-1994 PCI 总线绑定文档 v2.1 中指定）。

pci-emul 节点应该位于 pci 总线节点之外，否则它将被作为 PCI 设备进行扫描，从而导致混乱。

当扫描该总线时，我们最终会得到如下结果：

```bash
`- * pci@0 @ 05c660c8, 0
 `-   pci@1f,0 @ 05c661c8, 63488
`-   emul@1f,0 @ 05c662c8
```

当访问 pci@1f,0 设备时，它们将被转发到其模拟器。

沙箱 PCI 驱动程序还支持动态驱动程序绑定，允许设备驱动程序通过 U_BOOT_PCI_DEVICE() 声明驱动程序绑定信息，从而无需在主机控制器节点下提供任何设备树节点。 为了使此功能正常工作，主机控制器节点中必须提供“sandbox,dev-info”属性。

```c
pci1: pci@1 {
        compatible = "sandbox,pci";
        ...
        sandbox,dev-info = <0x08 0x00 0x1234 0x5678
                            0x0c 0x00 0x1234 0x5678>;
};
```

“sandbox,dev-info”属性指定该总线上的所有动态 PCI 设备。 每个动态 PCI 设备被编码为 4 个单元一组。 第一和第二单元分别是PCI设备号和功能号。 第三和第四单元分别是 PCI 供应商 ID 和设备 ID。

当扫描该总线时，我们最终会得到如下结果：

```bash
pci        [ + ]   pci_sandbo  |-- pci1
pci_emul   [   ]   sandbox_sw  |   |-- sandbox_swap_case_emul
pci_emul   [   ]   sandbox_sw  |   `-- sandbox_swap_case_emul
```
