
# Debugging driver model

# 1. 简介

本文档旨在当您无法找出驱动程序模型未按预期执行的原因时提供帮助。


# 2. 一般有用的技术

以下是一些有用的调试功能。

- 如果您正在编写新功能，请考虑在沙箱中而不是在主板上进行。 沙箱没有限制，允许轻松调试（例如 gdb），并且您可以为大多数常见设备编写模拟器。
- 将“#define DEBUG”放在文件顶部，以激活该文件中的所有 debug() 和 log_debug() 语句。
- 在使用日志记录的情况下，更改日志记录级别，例如 在 SPL 中，CONFIG_SPL_LOG_MAX_LEVEL=7（即 LOGL_DEBUG）且 CONFIG_LOG_DEFAULT_LEVEL=7。
- 如果使用 log_msg_ret() 实现返回值的记录，请设置 CONFIG_LOG_ERROR_RETURN=y 以准确查看错误发生的位置。
- 确保您启用了调试 UART - 请参阅 CONFIG_DEBUG_UART。 这样，您可以在串行驱动程序运行之前获得串行输出（printf()等）。
- 使用 JTAG 仿真器设置断点并单步执行代码。

并不是说大多数这些在启用时会稍微增加代码/数据大小。


# 3. 无法定位设备

假设你有 uclass_first_device_err() 并且它没有找到任何东西。

如果它返回错误，那么这会给你一个线索。 查找 linux/errno.h 以查看错误。 常见的有：

- -ENOMEM 表示内存不足。 如果发生在 SPL 中或在 U-Boot 中重定位之前，请检查 CONFIG_SPL_SYS_MALLOC_F_LEN 和 CONFIG_SYS_MALLOC_F_LEN，因为它们可能需要更大。 在 malloc_simple.c 的最顶部添加“#define DEBUG”以了解内存的去向。
- -EINVAL 通常表示设备树节点中存在某些内容丢失或错误。 检查一切是否正确并查看驱动程序中的 of_to_plat() 方法。

如果没有错误，您应该检查设备是否真正绑定。 在找到设备之前调用 dm_dump_tree() 以确保其存在。

如果不存在，请检查设备树兼容字符串是否与驱动程序期望的字符串（在 struct udevice_id 数组中）匹配。

如果您使用 of-platdata（例如 CONFIG_SPL_OF_PLATDATA），请检查驱动程序名称是否与设备树中的第一个兼容字符串相同（无效变量字符转换为下划线）。

如果你真的陷入困境，将“#define LOG_DEBUG”放在 drivers/core/lists.c 的顶部应该会告诉你发生了什么。
