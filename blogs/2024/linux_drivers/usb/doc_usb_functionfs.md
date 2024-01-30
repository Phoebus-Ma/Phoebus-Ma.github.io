
# How FunctionFS works

从内核的角度来看，它只是一个具有一些独特行为的复合函数。 仅当用户空间驱动程序通过写入描述符和字符串进行注册后，才可以将其添加到 USB 配置（用户空间程序必须提供与内核级复合函数添加到配置时提供的信息相同的信息）。

这尤其意味着复合初始化函数可能不在 init 部分中（即，可能不使用 __init 标记）。

从用户空间的角度来看，它是一个文件系统，在安装时提供“ep0”文件。 用户空间驱动程序需要将描述符和字符串写入该文件。 它不需要担心端点、接口或字符串编号，而只需提供描述符，例如该函数是否是唯一的（端点和字符串编号从 1 开始，接口编号从 0 开始）。 FunctionFS 根据需要更改它们，并处理不同配置中数字不同时的情况。

当写入描述符和字符串时，会出现“ep#”文件（每个声明的端点一个），用于处理单个端点上的通信。 同样，FunctionFS 负责实数和配置更改（这意味着“ep1”文件可能真正映射到（例如）端点 3（以及当配置更改为（例如）端点 2 时））。 “ep0”用于接收事件和处理设置请求。

当所有文件都关闭时，该功能将自行禁用。

我还想提一下的是，FunctionFS 的设计方式使得可以多次挂载它，因此最终一个小工具可以使用多个 FunctionFS 函数。 这个想法是每个 FunctionFS 实例都由安装时使用的设备名称来标识。

人们可以想象一个具有以太网、MTP 和 HID 接口的小工具，其中后两个接口是通过 FunctionFS 实现的。 在用户空间级别，它看起来像这样：

```bash
$ insmod g_ffs.ko idVendor=<ID> iSerialNumber=<string> functions=mtp,hid
$ mkdir /dev/ffs-mtp && mount -t functionfs mtp /dev/ffs-mtp
$ ( cd /dev/ffs-mtp && mtp-daemon ) &
$ mkdir /dev/ffs-hid && mount -t functionfs hid /dev/ffs-hid
$ ( cd /dev/ffs-hid && hid-daemon ) &
```

在内核级别，小工具检查 ffs_data->dev_name 以确定其 FunctionFS 是为 MTP（“mtp”）还是 HID（“hid”）设计的。

如果未提供“functions”模块参数，则驱动程序仅接受一个具有任何名称的函数。

当提供“functions”模块参数时，仅接受具有列出名称的函数。 特别是，如果“functions”参数的值只是一个单元素列表，则行为与根本没有“functions”时类似； 但是，只接受具有指定名称的函数。

仅当所有声明的函数文件系统都已挂载且所有函数的 USB 描述符已写入其 ep0 后，该小工具才会注册。

相反，在第一个 USB 功能关闭其端点后，该小工具将被取消注册。
