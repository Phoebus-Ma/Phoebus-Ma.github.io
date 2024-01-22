
# Live Device Tree

# 1. Introduction

传统上，U-Boot 使用“扁平”设备树。 这意味着它直接从设备树二进制结构中读取。 它被称为扁平设备树，因为节点被一个接一个地列出，并且层次结构由格式中的标签检测到。

本文档描述了 U-Boot 对“实时”设备树的支持，这意味着该树被加载到 U-Boot 内的分层数据结构中。


# 2. Motivation

扁平设备树有几个优点：

- 它是设备树编译器生成的格式，因此不需要翻译
- 它相当紧凑（例如不需要指针）
- 它由 libfdt 库访问，该库经过充分测试且稳定

然而，扁平设备树确实有一些限制。 添加新属性可能需要复制大量数据以腾出空间。 整个树具有固定的最大尺寸，因此有时必须在新位置重建树以创建更多空间。 即使不添加新的属性或节点，扫描树也可能很慢。 例如，查找节点的父节点是一个缓慢的过程。 从节点读取涉及少量解析，这需要一些时间。

驱动程序模型在启动时按顺序扫描整个设备树，这避免了扁平树最严重的限制。 但如果要在运行时修改树，则活树要快得多。 即使不需要修改，解析一次树并从那时起使用活动树似乎可以节省一点时间。


# 3. Implementation

在 U-Boot 中，当前仅在重定位后才支持实时设备树（‘livetree’）。 因此，我们需要一种机制来指定设备树节点，无论它是在 Flat Tree 还是 LiveTree 中。

“ofnode”类型提供了这一点。 ofnode 可以指向平面树节点（当尚未设置活动树节点时）或活动树节点。 ofnode 函数的调用者不需要担心这些细节。

设备树中信息的主要用户是驱动程序。 它们有一个附加到设备树节点的“struct udevice *”。 因此，能够使用“struct udevice *”读取设备树属性是有意义的，而不必首先获取 ofnode。

‘dev_read_…()’接口提供了这一点。 它允许仅使用设备指针轻松地从设备树中读取属性。 它在底层使用 ofnode，因此它可以与平面设备树和实时设备树一起使用。


# 4. Enabling livetree

CONFIG_OF_LIVE 启用 livetree。 启用此选项后，扁平树将在 SPL 中以及在 U-Boot 中重定位之前使用。 就在重定位之前，会构建一个 livetree，这将在重定位之后用于 U-Boot。

大多数 livetree 检查都使用 CONFIG_IS_ENABLED(OF_LIVE)。 这意味着对于 SPL，会检查 CONFIG_SPL_OF_LIVE 选项。 目前这还不存在，因为 SPL 不支持 livetree。


# 5. Porting drivers

许多现有驱动程序使用 fdtdec 接口来读取设备树属性。 这只适用于平面设备树。 应将驱动程序转换为使用 dev_read_() 接口。

例如，旧的代码可能是这样的：

```c
struct udevice *bus;
const void *blob = gd->fdt_blob;
int node = dev_of_offset(bus);

i2c_bus->regs = (struct i2c_ctlr *)devfdt_get_addr(dev);
plat->frequency = fdtdec_get_int(blob, node, "spi-max-frequency", 500000);
```

新代码是：

```c
struct udevice *bus;

i2c_bus->regs = dev_read_addr_ptr(dev);
plat->frequency = dev_read_u32_default(bus, "spi-max-frequency", 500000);
```

dev_read_…() 接口更方便，并且适用于平面设备树和实时设备树。 有关函数列表，请参阅 include/dm/read.h。

如果必须从子节点或其他节点读取属性，则必须回退到使用 ofnode。 例如，对于像这样的旧代码：

```c
const void *blob = gd->fdt_blob;
int subnode;

fdt_for_each_subnode(subnode, blob, dev_of_offset(dev)) {
    freq = fdtdec_get_int(blob, node, "spi-max-frequency", 500000);
    ...
}
```

你应该使用：

```c
ofnode subnode;

ofnode_for_each_subnode(subnode, dev_ofnode(dev)) {
    freq = ofnode_read_u32(node, "spi-max-frequency", 500000);
    ...
}
```


# 6. Useful ofnode functions

livetree的内部数据结构定义在include/dm/of.h中：

**struct device_node**

&emsp;保存有关设备树节点的信息

**struct property**

&emsp;保存有关节点内属性的信息

节点有指向它们的第一个属性、父节点、第一个子节点和兄弟节点的指针。 这允许节点在分层树中链接在一起。

属性具有指向下一个属性的指针。 这允许节点的所有属性在一条链中链接在一起。

在正常代码中没有必要使用这些数据结构。 特别是，您应该避免使用直接访问 livetree 的函数，例如 of_read_u32()。 请改用 ofnode 函数，以允许您的代码也可以使用平面树。

一些转换函数在内部使用。 一般来说，驱动程序代码不需要这些。 请注意，如果在错误的上下文中调用它们将不起作用。 例如，当使用扁平树时，调用 ofnode_to_no() 是无效的。 同样，不可能在 livetree 节点上调用 ofnode_to_offset() 。

**ofnode_to_np():**

&emsp;将 ofnode 转换为 struct device_node *

**ofnode_to_offset():**

&emsp;将 ofnode 转换为 offset

**no_to_ofnode():**

&emsp;将节点指针转换为 ofnode

**offset_to_ofnode():**

&emsp;将 offset 转换为 ofnode

其他有用的功能：

**of_live_active():**

&emsp;如果 livetree 正在使用则返回 true，如果扁平树则返回 false

**ofnode_valid():**

&emsp;如果给定节点有效则返回 true

**ofnode_is_np():**

&emsp;如果给定节点是 livetree 节点，则返回 true

**ofnode_equal():**

&emsp;比较两个 ofnode

**ofnode_null():**

&emsp;返回 null ofnode（ofnode_valid() 返回 false）


# 7. Phandles

对活树有完整的 phandle 支持。 所有函数都使用 struct ofnode_phandle_args，其中包含一个 ofnode。 这透明地支持 livetree 和 flat 树。 请参阅node_parse_phandle_with_args() 的示例。


# 8. Reading addresses

您应该使用 dev_read_addr() 和朋友从设备树节点读取地址。


# 9. fdtdec

现有的 fdtdec 接口最终将被淘汰。 请尽量避免在新代码中使用它。


# 10. Modifying the livetree

这是通过 ofnode_write_prop() 和相关函数以有限的方式支持的。

反扁平化算法会导致为整个树分配单个内存块。 当写入新属性时，这些属性会在该块之外分配新的内存。 当块被释放时，分配的属性仍然存在。 这可能会导致内存泄漏。

此泄漏的解决方案是为属性（以及节点，当支持添加这些属性时）添加一个标志，指示它们应该被释放。 然后，在释放内存块之前，可以扫描树以查找这些“单独分配”的节点和属性。

ofnode_write_…() 函数还支持写入平面树。 但是应该小心，因为这可能会改变平面树中节点名称和属性的位置，从而影响活动树。 一般来说，这并不重要，因为当我们启动活树时，我们就不会再使用扁平树了。 但在测试的情况下，这可能会导致问题。

沙箱测试通常在启用 OF_LIVE 的情况下运行，但实际的活动树要么存在，要么不存在。 这是为了确保即使启用了 OF_LIVE，扁平树功能也能正常工作。 但是，如果测试修改了平面设备树，则活动树可能会变得无效。 该点之后运行的任何实时树测试都将使用损坏的树，例如 属性名称不正确或更糟。 为了解决这个问题，我们获取设备树的副本，并在修改它的任何测试后恢复它。 请注意，此副本不是在其他板上制作的，只是在沙箱上制作的。


# 11. Multiple livetrees

livetree 实现最初设计用于与控制 FDT 一起使用。 这意味着 FDT 修复（ft_board_setup() 等）必须使用平面树。

使用 livetree 进行修复会很有帮助，因为添加大量节点和属性将涉及更少的内存复制并且效率更高。 作为实现这一目标的一步，引入了 oftree 类型。 它通常设置为 oftree_default()，但可以使用 oftree_from_fdt() 设置为其他值。 只要 OF_LIVE 被禁用，就可以使用 ofnode 接口进行修复。 OF_LIVE 支持需要在最后添加展平步骤。

请参阅 dm_test_ofnode_root() 了解一些示例。 oftree_from_fdt() 函数导致平面设备树被“注册”，以便可以被 ofnode 接口使用。


# 12. Internal implementation

dev_read_…() 函数有两种实现。 当 CONFIG_DM_DEV_READ_INLINE 启用时，这些函数只需直接调用 ofnode 函数即可。 当 livetree 未启用时这很有用。 ofnode 函数调用 ofnode_is_np(node) ，如果 livetree 被禁用，它将始终返回 false，只是回退到平面树代码。

这种优化意味着在没有启用 livetree 的情况下，dev_read_…() 和 ofnode 接口不会明显增加代码大小。

当 livetree 禁用时，CONFIG_DM_DEV_READ_INLINE 选项默认启用。

大多数livetree代码直接来自Linux，并尽可能少地修改。 这是故意的，因为这段代码相当稳定并且可以满足我们的要求。 不支持某些功能（例如获取/放置）。 内部宏负责默默地删除这些功能。

在 of_access.c 文件中，有指向别名节点、所选节点和 stdout-path 别名的指针。


# 13. Errors

对于平面设备树，会返回 libfdt 错误（例如 -FDT_ERR_NOTFOUND）。 对于 livetree，会返回正常的“errno”错误（例如 -ENOTFOUND）。 目前 ofnode 和 dev_read_…() 函数返回一种或其他类型的错误。 这显然是不可取的。 一旦为所有功能添加了测试，就可以进行整理。


# 14. Adding new access functions

添加设备树访问的新函数涉及以下步骤：

- 添加两个 dev_read() 函数：
    - read.h头文件中的内联版本，调用了一个ofnode函数；
    - read.c文件（或者可能是另一个文件）中的标准版本，它也调用了ofnode函数；

    &emsp;这些函数的实现可以是相同的。 内联版本的目的纯粹是为了减少代码大小的影响。

- 添加ofnode函数。 这应该调用 ofnode_is_np() 来确定是使用 livetree 还是 flat 树。 对于 livetree，它应该调用 of_…() 函数。 对于扁平树，它应该调用 fdt_…() 函数。 如果未启用 livetree，则 livetree 版本将在编译时进行优化。

- 为 livetree 实现添加一个 of_…() 函数。 如果Linux中有类似的功能，则应从那里获取实现并尽可能少地修改（通常根本不修改）。


# 15. Future work

U-Boot 2017.07 中引入了 Live 树支持。 一些可能的增强功能包括：

- 支持 SPL 中和搬迁之前的 livetree（如果需要）

- 释放因向 livetree 写入新节点/属性值而导致的泄漏内存 (ofnode_write_prop())
