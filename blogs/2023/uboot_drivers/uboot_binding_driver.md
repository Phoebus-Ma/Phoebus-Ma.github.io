
# Binding/unbinding a driver

本文档旨在描述绑定和取消绑定命令。

出于调试目的，从 U-Boot 命令行绑定或取消绑定驱动程序应该很有用。

unbind 命令调用删除设备驱动程序回调并取消设备与其驱动程序的绑定。

bind 命令将设备绑定到其驱动程序。

在某些情况下，能够从命令行将设备绑定到驱动程序可能会很有用。 最明显的例子是 USB 小工具等多功能设备。 另一个用例是设备在启动时尚未准备好，并且在绑定驱动程序之前需要进行一些设置（例如：从大容量存储或以太网获取位流的 FPGA）

用法:

```bash
bind <node path> <driver>
bind <class> <index> <driver>
unbind <node path>
unbind <class> <index>
unbind <class> <index> <driver>
```

定义:

- <node path> 是节点的设备树路径。
- <class> 是“dm uclass”命令或“dm tree”命令第一列给出的列表中可用的类之一。
- <index> 是父节点的索引（“dm 树”输出的第二列）。
- <driver> 是由“dm drivers”命令或“dm tree”输出的第四列给出的要绑定的驱动程序名称。

例子:

```bash
bind usb_dev_generic 0 usb_ether
```

```bash
unbind usb_dev_generic 0 usb_ether
```

或

```bash
unbind eth 1
```

```bash
bind /ocp/omap_dwc3@48380000/usb@48390000 usb_ether
unbind /ocp/omap_dwc3@48380000/usb@48390000
```
