
# How USB works with driver model

# 1. Introduction

驱动程序模型 USB 支持利用现有功能，但改变了驱动程序的查找方式。 本文档提供了一些信息，旨在帮助您了解启用驱动程序模型时 U-Boot 中 USB 的工作方式。


# 2. Enabling driver model for USB

提供了新的 CONFIG_DM_USB 选项来启用 USB 驱动程序模型。 这会导致包含 USB uclass，并删除 usb.c 中的等效代码。 特别是 usb_init() 函数由 uclass 实现。


# 3. Support for EHCI and XHCI

目前不支持 OHCI。 EHCI 和 XHCI 驱动程序都应在 USB uclass 中声明为驱动程序。 例如：

```c
static const struct udevice_id ehci_usb_ids[] = {
        { .compatible = "nvidia,tegra20-ehci", .data = USB_CTLR_T20 },
        { .compatible = "nvidia,tegra30-ehci", .data = USB_CTLR_T30 },
        { .compatible = "nvidia,tegra114-ehci", .data = USB_CTLR_T114 },
        { }
};

U_BOOT_DRIVER(usb_ehci) = {
        .name   = "ehci_tegra",
        .id     = UCLASS_USB,
        .of_match = ehci_usb_ids,
        .of_to_plat = ehci_usb_of_to_plat,
        .probe = tegra_ehci_usb_probe,
        .remove = tegra_ehci_usb_remove,
        .ops    = &ehci_usb_ops,
        .plat_auto = sizeof(struct usb_plat),
        .priv_auto = sizeof(struct fdt_usb),
        .flags  = DM_FLAG_ALLOC_PRIV_DMA,
};
```

这里ehci_usb_ids用于列出驱动程序支持的控制器。 每个都有自己的数据价值。 控制器必须位于 UCLASS_USB uclass 中。

of_to_plat() 方法允许控制器驱动程序从设备树中获取任何必要的设置。

这里的ops是ehci_usb_ops。 在大多数情况下，所有 EHCI 驱动程序都将使用这些相同的操作，因为它们都是 EHCI 兼容的。 对于 EHCI，还有一些特殊操作可以在调用 ehci_register() 时被覆盖。

驱动程序可以使用 priv_auto 设置其私有数据的大小。 这可以保存驱动程序操作所需的运行时信息。 它在设备被探测时存在（而不是在绑定时），并在驱动程序被删除时被删除。

请注意，usb_plat 目前仅用于处理 USB 设备模式下的总线设置（OTG 操作）。 如果不支持，可以省略。

驱动程序的probe()方法应该执行基本的控制器初始化，然后调用ehci_register()将自身注册为EHCI设备。 它应该在remove()方法中调用ehci_deregister()。 注册新的 EHCI 设备本身不会导致总线被扫描。

旧的 ehci_hcd_init() 函数不再使用。 也无需通过板初始化代码设置 USB 控制器。 当使用“usb start”时，将探测每个控制器并扫描其总线。

XHCI 的工作方式类似。


# 4. Data structures

使用以下主要数据结构：

**struct usb_device:**

&emsp;它保存有关总线上设备的信息。 所有设备都具有这种结构，甚至是根集线器。 控制器本身不具有这种结构。 您可以使用 dev_get_parent_priv(dev) 为设备“dev”访问它。 它与旧结构匹配，只是不存在父级和子级信息（因为驱动程序模型处理该信息）。 一旦设备设置完毕，您可以在此结构中找到设备描述符和当前配置描述符。

**struct usb_plat:**

&emsp;它保存控制器的平台数据。 到目前为止，这仅用作在 OTG 模式下充当 USB 设备的控制器的解决方法，因为小工具框架不使用驱动程序模型。

**struct usb_dev_plat:**

&emsp;它保存设备的平台数据。 您可以使用 dev_get_parent_plat(dev) 访问设备“dev”。 它保存设备地址和速度——在设备驱动程序实际设置之前可以确定的任何内容。 当探测总线时，该结构用于向设备驱动程序提供基本信息。

**struct usb_bus_priv:**

&emsp;这是每个控制器的私有信息，由控制器 uclass 维护。 它主要用于跟踪下一个要使用的设备地址。

其中，在驱动程序模型之前仅使用了 struct usb_device。


# 5. USB buses

给定一个控制器，您就知道总线 - 它是连接到控制器的总线。 每个控制器只处理一条总线。 每个控制器都附有一个根集线器。 该集线器本身就是一个 USB 设备，可以提供一个或多个可连接其他设备的“端口”。 可以启动集线器并找出其哪些端口连接了设备。

设备的地址从 1 开始。根集线器的地址始终为 1，从这里开始，设备按顺序编号。 USB uclass 在枚举期间自动处理此编号。

USB 设备是通过在特定集线器上查找设备并将其地址设置为下一个可用地址来枚举的。 USB 总线以树形结构延伸，可能有多个集线器，每个集线器有多个端口，也许还有其他集线器。 有些集线器有自己的电源，否则控制器提供的 5V 500mA 电源将不足以运行很多设备。

U-Boot 中的枚举需要很长时间，因为一次探测一个设备，并且每个设备都有足够的时间来唤醒并宣布自己。 超时是为最慢的设备设置的。

每条总线上最多可连接 127 个设备。 USB 有四种总线速度：低速 (1.5Mbps)、全速 (12Mbps)、高速 (480Mbps)（仅适用于 USB2 及更高版本 (EHCI)）和超级 (5Gbps)（仅适用于 USB3 及更高版本 (XHCI)）。 如果将超高速设备连接到高速集线器，您将只能获得高速。


# 6. USB operations

与之前的驱动程序模型一样，可以使用submit_bulk_msg()等发送消息。 这些现在由 USB uclass 实现并通过控制器驱动程序进行路由。 请注意，消息不会发送到设备本身的驱动程序 - 即它们不会将堆栈向下传递到控制器。 U-Boot 只是找到设备所连接的控制器，并向那里发送带有适当“管道”值的消息，以便可以正确寻址。 话虽如此，应该接收消息的 USB 设备被传递到驱动程序方法中，以供沙箱使用。 此设计决策可供审查，并且更改它对代码的影响很小，因为这些方法通常由 EHCI 和 XHCI 堆栈实现。

控制器驱动程序（在 UCLASS_USB 中）本身提供了发送每种消息类型的方法。 对于 XHCI，提供了一个额外的 alloc_device() 方法，因为 XHCI 需要在读取设备描述符之前分配设备上下文。

这些方法使用“管道”，它是用于描述消息类型、传输方向和预期接收者（设备编号）的位字段的集合。


# 7. USB Devices

USB 设备使用一种简单的算法来查找，该算法通过深度优先搜索中的可用集线器进行工作。 设备可以位于任何 uclass 中，但附加到父集线器（或在根集线器的情况下为控制器），因此附加了父数据（这是 struct usb_device）。

当设备的probe()方法被调用时，它已被枚举并准备好与主机通信。

枚举过程需要确定将哪个驱动程序附加到每个 USB 设备。 它通过检查设备类、接口类、供应商 ID、产品 ID 等来完成此操作。有关驱动程序如何与 USB 设备匹配的信息，请参阅 struct usb_driver_entry - 您可以使用 USB_DEVICE() 宏来声明 USB 驱动程序。 例如，usb_storage.c定义了一个USB_DEVICE()来处理存储设备，它将用于所有匹配的USB设备。


# 8. Technical details on enumeration flow

准确理解 USB 总线是如何枚举的，可以避免在处理 USB 设备时出现混乱。

设备初始化大致如下：

- 在某个时刻，“usb start”命令会运行；
- 这会调用 usb_init()，它依次通过每个控制器工作；
- 控制器被探测()。 这没有枚举；
- 然后调用usb_scan_bus()。 这会调用 usb_scan_device() 来扫描连接到控制器的（唯一）设备 - 根集线器；
- usb_scan_device() 设置一个假的 struct usb_device 并调用 usb_setup_device()，传递要扫描的端口号，在本例中为端口 0；
- usb_setup_device()首先调用usb_prepare_device()设置设备地址，然后usb_select_config()选择第一个配置；
- 此时设备已被枚举，但我们没有真正的 struct udevice 。 但我们在 struct usb_device 中有描述符，因此我们可以使用它来确定要使用的驱动程序；
- 回到 usb_scan_device() 中，我们调用 usb_find_child() 来尝试查找与我们刚刚在总线上找到的设备相匹配的现有设备。 如果设备在设备树中被提及，或者如果我们之前扫描了总线并且之前创建了该设备，则可能会发生这种情况；
- 如果 usb_find_child() 没有找到现有设备，我们调用 usb_find_and_bind_driver() 尝试绑定一个设备；
- usb_find_and_bind_driver() 搜索所有可用的 USB 驱动程序（用 USB_DEVICE() 声明）。 如果找到匹配项，则会绑定该驱动程序以创建新设备；
- 如果没有，它会绑定一个通用驱动程序。 通用驱动程序足以允许访问设备（向其发送数据包等），但所有功能都需要在驱动程序模型之外实现；
- 无论如何，当 usb_find_child() 和/或 usb_find_and_bind_driver() 完成后，我们就有了一个具有正确 uclass 的设备。 此时我们要探测该设备；
- 首先，我们将新设备的基本信息（地址、端口、速度）存储在其父平台数据中。 我们无法存储其私有数据，因为在探测设备之前这些数据不会存在；
- 然后我们调用 device_probe() 来探测设备；
- 第一个探测步骤实际上是 USB 控制器（或 USB 集线器）的 child_pre_probe() 方法。 它在其他任何事情之前被调用，旨在设置一个准备与其父总线一起使用的子设备。 对于USB，这会调用usb_child_pre_probe()，它会获取存储在父平台数据中的信息并将其存储在父私有数据中（这是struct usb_device，这次是一个真实的数据）。 然后它再次调用 usb_select_config() 以确保有关设备的所有内容均已设置；
- 请注意，我们已调用 usb_select_config() 两次。 这是低效的，但替代方案是在平台数据中存储附加信息。 这种方式花费的时间最少，也更简单；
- 此时，就 USB 子系统而言，设备已设置完毕并可供使用；
- 然后调用设备的probe()方法。 它可以发送消息并做任何它想让设备工作的事情；

请注意，第一个设备始终是根集线器，必须对其进行扫描才能找到任何设备。 上述步骤将创建一个集线器 (UCLASS_USB_HUB)，为其指定地址 1 并设置配置。

对于集线器，集线器 uclass 有一个 post_probe() 方法。 这意味着在探测任何集线器之后，uclass 会进行一些处理。 在这种情况下，调用 usb_hub_post_probe()，并执行以下步骤：

- usb_hub_post_probe() 调用 usb_hub_scan() 扫描集线器，集线器又调用 usb_hub_configure()；
- 集线器电源已启用；
- 我们循环访问集线器上的每个端口，对每个端口执行相同的步骤；
- 首先，检查是否存在设备。 这发生在 usb_hub_port_connect_change() 中。 如果是，则调用 usb_scan_device() 来扫描设备，并传递适当的端口号；
- 您将从上述步骤中识别出 usb_scan_device() 。 它将设备设置为可供使用。 如果它是一个集线器，它将在继续这里之前扫描该集线器（递归，深度优先）；
- 一旦以这种方式扫描所有集线器端口，集线器及其所有下游设备就可以使用了；
- 以同样的方式扫描其他控制器；

上面的方法有一些很好的属性：

- 总线枚举是通过驱动程序模型的自然设备流进行的；
- 大多数逻辑位于 USB 控制器和集线器 uclass 中； 实际的设备驱动程序不需要知道它们位于 USB 总线上，至少就枚举而言；
- 集线器扫描在探测到集线器后自动进行；


# 9. Hubs

USB 集线器的扫描如上一节所述。 虽然集线器有自己的 uclass，但它们与控制器共享一些共同元素：

- 它们都将私有数据附加到它们的子级（struct usb_device，子级可通过 dev_get_parent_priv(child) 访问）；
- 他们都使用 usb_child_pre_probe() 将他们的孩子设置为正确的 USB 设备；


# 10. Example - Mass Storage

作为 USB 设备驱动程序的示例，请参阅 usb_storage.c。 它使用自己的 uclass 并将其自身声明如下：

```c
U_BOOT_DRIVER(usb_mass_storage) = {
        .name   = "usb_mass_storage",
        .id     = UCLASS_MASS_STORAGE,
        .of_match = usb_mass_storage_ids,
        .probe = usb_mass_storage_probe,
};

static const struct usb_device_id mass_storage_id_table[] = {
        { .match_flags = USB_DEVICE_ID_MATCH_INT_CLASS,
          .bInterfaceClass = USB_CLASS_MASS_STORAGE},
        { }     /* Terminating entry */
};

USB_DEVICE(usb_mass_storage, mass_storage_id_table);
```

USB_DEVICE() 宏将给定的匹配信息表附加到给定的驱动程序。 请注意，驱动程序在 U_BOOT_DRIVER() 中声明为“usb_mass_storage”，并且它必须与 USB_DEVICE 的第一个参数匹配。

当在 bInterfaceClass 值为 USB_CLASS_MASS_STORAGE 的 USB 设备上调用 usb_find_and_bind_driver() 时，它将自动查找该驱动程序并使用它。


# 11. Counter-example: USB Ethernet

作为旧方法的示例，请参阅 usb_ether.c。 当扫描总线时，所有以太网设备将被创建为通用 USB 设备（在 uclass UCLASS_USB_DEV_GENERIC 中）。 然后，当扫描完成时，将调用 usb_host_eth_scan()。 它会查看每条总线上的所有设备，并以以前的方式手动找出哪些是以太网设备。

事实上，usb_ether 应该移至驱动程序模型。 每个 USB 以太网驱动程序（例如 drivers/usb/eth/asix.c）应包含 USB_DEVICE() 声明，以便将其作为正常 USB 枚举的一部分找到。 然后，将使用真正的（驱动程序模型感知）驱动程序，而不是通用 USB 驱动程序。 由于以太网现在支持驱动程序模型，这应该很容易实现，然后 usb_ether.c 和 usb_host_eth_scan() 将消失。


# 12. Sandbox

所有驱动程序模型 uclass 都必须经过测试，USB 也不例外。 为了实现这一点，提供了沙箱 USB 控制器。 这可以利用伪装成 USB 设备的仿真驱动程序。 为集线器和闪存棒提供了仿真。 这些足以创建一个可以扫描和使用的假装 USB 总线（由沙箱设备树 sandbox.dts 定义）。

test/dm/usb.c 中的测试利用了此功能。 它允许测试大部分 USB 堆栈，而无需真正的硬件。

这是一个设备树片段示例：

```c
usb@1 {
        compatible = "sandbox,usb";
        hub {
                compatible = "usb-hub";
                usb,device-class = <USB_CLASS_HUB>;
                hub-emul {
                        compatible = "sandbox,usb-hub";
                        #address-cells = <1>;
                        #size-cells = <0>;
                        flash-stick {
                                reg = <0>;
                                compatible = "sandbox,usb-flash";
                                sandbox,filepath = "flash.bin";
                        };
                };
        };
};
```

这定义了一个控制器，包含一个根集线器（这是必需的）。 集线器由集线器模拟器模拟，并且模拟集线器有一个闪存棒可以在其端口之一上进行模拟。

当使用“usb start”时，将提供以下“dm tree”输出：

```bash
usb         [ + ]    `-- usb@1
usb_hub     [ + ]        `-- hub
usb_emul    [ + ]            |-- hub-emul
usb_emul    [ + ]            |   `-- flash-stick
usb_mass_st [ + ]            `-- usb_mass_storage
```

这可能看起来令人困惑。 其中大部分镜像设备树，但“usb_mass_storage”设备不在设备树中。 这是由 usb_find_and_bind_driver() 基于 usb_storage.c 中的 USB_DRIVER 创建的。 虽然“flash-stick”是模拟设备，但“usb_mass_storage”是与之通信的真正的 U-Boot USB 设备驱动程序。


# 13. Future work

在嵌入式系统上拥有带有大量集线器的大型 USB 总线是相当罕见的。 事实上，除了根集线器之外的任何东西都不常见。 尽管如此，仍然可以通过两种方式加速枚举：

- 广度优先搜索将允许设备在某种程度上并行重置和探测；
- 枚举可能是懒惰的，因为我们可以首先枚举根集线器，然后仅在使用我们找不到的设备时才进入下一个“级别”。 如果设备在设备树中静态声明（这对于每条总线上都有相同、已知的事物的生产板来说是可以接受的），那么这可能会变得更容易。

但在一般情况下，当前的算法就足够了。

其他需要做的事情： - 如上所述将 usb_ether 转换为使用驱动程序模型 - 测试键盘是否工作（并转换为驱动程序模型） - 将 USB 小工具框架移至驱动程序模型 - 在驱动程序模型中实现 OHCI - 在驱动程序模型中实现 USB PHY - 找出一种巧妙的方法来为 USB 设备提供惰性初始化。
