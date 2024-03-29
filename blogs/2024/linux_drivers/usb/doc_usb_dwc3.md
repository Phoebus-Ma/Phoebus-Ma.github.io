
# DWC3 driver

# TODO

请在阅读时选择一些内容：

- 将中断处理程序转换为 per-ep-thread-irq

    事实证明，一些 DWC3 命令大约需要 1 毫秒才能完成。 目前我们一直旋转直到命令完成，这很糟糕。

    实现思路：

    - dwc 内核为每个端点的中断实现了一个多路分离 irq 芯片。 中断号在探测期间分配并属于设备。 如果 MSI 提供每个端点中断，则该虚拟中断芯片可以替换为“真实”中断。

    - 中断在 usb_ep_enable() 上请求/分配，并在 usb_ep_disable() 上删除。 最坏的情况是 32 个中断，ep0/1 的下限是 2 个。

    - dwc3_send_gadget_ep_cmd() 将在 wait_for_completion_timeout() 中休眠，直到命令完成。

    - 中断处理程序分为以下几部分：

        - 设备的主处理程序会遍历每个事件并针对该事件调用 generic_handle_irq()。 从 generic_handle_irq() 返回时，确认事件计数器，因此中断消失（最终）。

        - 设备的线程处理程序无。

        - EP 中断的主处理程序读取事件并尝试处理它。 一切需要睡眠的事情都交给了线程。 该事件保存在每个端点的数据结构中。 我们可能必须注意，一旦将某些内容交给线程，就不要处理事件，因此我们不会在 X > Y 的情况下处理事件 X prio Y。

        - EP 中断的线程处理程序处理剩余的 EP 工作，这些工作可能会休眠，例如等待命令完成。

    早期:

    延迟不应增加，因为中断线程具有高优先级，并且将在用户空间中的平均任务之前运行（除非用户更改了优先级）。
