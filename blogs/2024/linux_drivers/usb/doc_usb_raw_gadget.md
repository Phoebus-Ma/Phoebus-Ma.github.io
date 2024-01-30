
# USB Raw Gadget

USB Raw Gadget 是一个小工具驱动程序，可让用户空间对小工具的通信过程进行低级控制。

与任何其他小工具驱动程序一样，Raw Gadget 通过 USB 小工具 API 实现 USB 设备。 与大多数小工具驱动程序不同，Raw Gadget 本身不实现任何具体的 USB 功能，但需要用户空间来执行此操作。

Raw Gadget 目前是一个严格的调试功能，不应在生产中使用。 请改用 GadgetFS。

通过 CONFIG_USB_RAW_GADGET 启用。


# 1. Comparison to GadgetFS

Raw Gadget 与 GadgetFS 类似，但为用户空间提供了对 USB gadget 层的更直接的访问。 主要区别是：

1. Raw Gadget 将每个 USB 请求传递到用户空间以获得响应，而 GadgetFS 根据提供的描述符在内部响应某些 USB 请求。 请注意，UDC 驱动程序可能会自行响应某些请求，并且从不将它们转发到小工具层。
2. Raw Gadget 允许提供任意数据作为对 USB 请求的响应，而 GadgetFS 对提供的 USB 描述符执行健全性检查。 这使得 Raw Gadget 适合通过提供格式错误的数据作为对 USB 请求的响应来进行模糊测试。
3. Raw Gadget 提供了一种选择要绑定的 UDC 设备/驱动程序的方法，而 GadgetFS 当前绑定到第一个可用的 UDC。 这允许将多个原始小工具实例绑定到不同的 UDC。
4. 原始小工具显式公开有关端点地址和功能的信息。 这允许用户编写与 UDC 无关的小工具。
5. Raw Gadget 有一个基于 ioctl 的接口，而不是基于文件系统的接口。


# 2. Userspace interface

用户可以通过打开 /dev/raw-gadget 并发出 ioctl 调用来与 Raw Gadget 进行交互； 有关详细信息，请参阅 include/uapi/linux/usb/raw_gadget.h 中的注释。 可以同时使用多个Raw Gadget实例（绑定到不同的UDC）。

Raw Gadget的典型使用场景：

1. 通过打开 /dev/raw-gadget 创建一个 Raw Gadget 实例。
2. 通过 USB_RAW_IOCTL_INIT 初始化实例。
3. 使用 USB_RAW_IOCTL_RUN 启动实例。
4. 在循环中发出 USB_RAW_IOCTL_EVENT_FETCH 以接收来自原始小工具的事件，并根据必须实现哪种 USB 小工具对这些事件做出反应。

请注意，某些 UDC 驱动程序具有分配给端点的固定地址，因此不能在描述符中使用任意端点地址。 尽管如此，Raw Gadget 提供了一种与 UDC 无关的方式来编写 USB 小工具。 通过 USB_RAW_IOCTL_EVENT_FETCH 接收到 USB_RAW_EVENT_CONNECT 后，USB_RAW_IOCTL_EPS_INFO 可用于查找有关 UDC 驱动程序具有的端点的信息。 基于此，用户空间必须为小工具选择 UDC 端点，并相应地在端点描述符中分配地址。

原始小工具使用示例和测试套件：

<https://github.com/xairy/raw-gadget>


# 3. Internal details

每个原始小工具端点读/写 ioctl 都会提交 USB 请求并等待其完成。 这样做是有意为之，通过让单个系统调用完全处理单个 USB 请求来协助覆盖引导的模糊测试。 在实现中必须保留此功能。


# 4. Potential future improvements

支持 O_NONBLOCK I/O。 这将是另一种操作模式，其中 Raw Gadget 不会等到每个 USB 请求完成。

支持 USB 3 功能（启用端点时接受 SS 端点伴随描述符；允许为批量传输提供stream_id）。

支持 ISO 传输功能（公开已完成请求的帧号）。
