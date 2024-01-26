
# Converting old watchdog drivers to the watchdog framework

by Wolfram Sang <wsa@kernel.org>

在看门狗框架进入内核之前，每个驱动程序都必须自己实现 API。 现在，随着框架分解出通用组件，可以减轻这些驱动程序的负担，使其成为框架的用户。 本文档将指导您完成此任务。 描述了必要的步骤以及需要注意的事项。


# 1. Remove the file_operations struct

旧驱动程序为 open()、write() 等操作定义了自己的 file_operations...这些操作现在由框架处理，只需在需要时调用驱动程序即可。 因此，一般来说，“file_operations”结构和各种函数都可以运行。 只有很少的特定于驱动程序的细节需要转移到其他功能。 以下是功能和可能需要的操作的概述：

- open: 与资源管理相关的一切（文件打开检查、魔术关闭准备）都可以简单地进行。 设备特定的内容需要转到驱动程序特定的启动函数。 请注意，对于某些驱动程序，启动功能也充当 ping 功能。 如果是这种情况，并且您需要平衡启动/停止（时钟！），那么最好重构一个单独的启动函数。

- close: 与打开应用的提示相同。

- write: 可以简单地走，所有定义的行为都由框架处理，即写入时的 ping 和魔术字符（“V”）处理。

- ioctl: 虽然允许驱动程序对 IOCTL 接口进行扩展，但最常见的扩展由框架处理，并由驱动程序的一些帮助提供支持：

**WDIOC_GETSUPPORT:**

&emsp;从驱动程序返回强制的 watchdog_info 结构

**WDIOC_GETSTATUS:**

&emsp;需要定义状态回调，否则返回 0

**WDIOC_GETBOOTSTATUS:**

&emsp;需要正确设置 bootstatus 成员。 如果您没有进一步的支持，请确保它为 0！

**WDIOC_SETOPTIONS:**

&emsp;无需任何准备

**WDIOC_KEEPALIVE:**

&emsp;如果需要，watchdog_info 中的选项需要设置 WDIOF_KEEPALIVEPING

**WDIOC_SETTIMEOUT:**

&emsp;watchdog_info 中的选项需要设置 WDIOF_SETTIMEOUT 并且必须定义 set_timeout-callback。 如果设置了看门狗设备中的 min_timeout 和 max_timeout，则内核还将执行限制检查。 一切都是可选的。

**WDIOC_GETTIMEOUT:**

&emsp;无需任何准备

**WDIOC_GETTIMELEFT:**

&emsp;它需要定义 get_timeleft() 回调。 否则将返回EOPNOTSUPP

其他 IOCTL 可以使用 ioctl 回调来提供服务。 请注意，这主要是为了移植旧驱动程序； 新驱动程序不应该发明私有 IOCTL。 首先处理私有 IOCTL。 当回调返回 -ENOIOCTLCMD 时，框架的 IOCTL 也会被尝试。 任何其他错误都会直接提供给用户。

转换示例：

```c
-static const struct file_operations s3c2410wdt_fops = {
-       .owner          = THIS_MODULE,
-       .llseek         = no_llseek,
-       .write          = s3c2410wdt_write,
-       .unlocked_ioctl = s3c2410wdt_ioctl,
-       .open           = s3c2410wdt_open,
-       .release        = s3c2410wdt_release,
-};
```

检查特定于设备的功能，并将其保留以供以后重构。 剩下的就可以走了。


# 2. Remove the miscdevice

由于 file_operations 现在已经消失，您还可以删除“struct Miscdevice”。 框架将在 watchdog_register_device() 调用的 watchdog_dev_register() 上创建它：

```c
-static struct miscdevice s3c2410wdt_miscdev = {
-       .minor          = WATCHDOG_MINOR,
-       .name           = "watchdog",
-       .fops           = &s3c2410wdt_fops,
-};
```


# 3. Remove obsolete includes and defines

由于简化，一些定义现在可能未使用。 删除它们。 包含也可以删除。 例如：

```c
- #include <linux/fs.h>
- #include <linux/miscdevice.h> (if MODULE_ALIAS_MISCDEV is not used)
- #include <linux/uaccess.h> (if no custom IOCTLs are used)
```


# 4. Add the watchdog operations

所有可能的回调都在“struct watchdog_ops”中定义。 您可以在该目录中的“Linux WatchDog Timer Driver Core kernel API”中找到它的解释。 start() 和owner 必须设置，其余的都是可选的。 您可以在旧驱动程序中轻松找到相应的功能。 请注意，您现在将获得一个指向 watchdog_device 的指针作为这些函数的参数，因此您可能必须更改函数头。 其他更改很可能不需要，因为这里只发生直接硬件访问。 如果您在上述步骤中留下了特定于设备的代码，则应将其重构到这些回调中。

这是一个简单的例子：

```c
+static struct watchdog_ops s3c2410wdt_ops = {
+       .owner = THIS_MODULE,
+       .start = s3c2410wdt_start,
+       .stop = s3c2410wdt_stop,
+       .ping = s3c2410wdt_keepalive,
+       .set_timeout = s3c2410wdt_set_heartbeat,
+};
```

典型的函数头更改如下所示：

```c
-static void s3c2410wdt_keepalive(void)
+static int s3c2410wdt_keepalive(struct watchdog_device *wdd)
 {
...
+
+       return 0;
 }

...

-       s3c2410wdt_keepalive();
+       s3c2410wdt_keepalive(&s3c2410_wdd);
```


# 5. Add the watchdog device

现在我们需要创建一个“struct watchdog_device”并用框架的必要信息填充它。 该目录中的“Linux WatchDog Timer Driver Core kernel API”中也详细解释了该结构。 我们向它传递强制的 watchdog_info 结构和新创建的 watchdog_ops。 通常，旧驱动程序有自己的记录，例如使用静态变量的启动状态和超时。 必须将它们转换为使用 watchdog_device 中的成员。 请注意，超时值是无符号整数。 有些驱动程序使用signed int，因此也必须进行转换。

这是看门狗设备的一个简单示例：

```c
+static struct watchdog_device s3c2410_wdd = {
+       .info = &s3c2410_wdt_ident,
+       .ops = &s3c2410wdt_ops,
+};
```


# 6. Handle the 'nowayout' feature

一些驱动程序静态使用 nowayout，即没有模块参数，只有 CONFIG_WATCHDOG_NOWAYOUT 确定是否要使用该功能。 这需要通过初始化 watchdog_device 的状态变量来转换，如下所示：

```c
.status = WATCHDOG_NOWAYOUT_INIT_STATUS,
```

然而，大多数驱动程序还允许运行时配置 nowayout，通常是通过添加模块参数来实现。 其转换类似于：

```c
watchdog_set_nowayout(&s3c2410_wdd, nowayout);
```

模块参数本身需要保留，但与 nowayout 相关的其他所有内容都可以保留。 这可能是 open()、close() 或 write() 中的一些代码。


# 7. Register the watchdog device

将misc_register(&miscdev) 替换为watchdog_register_device(&watchdog_dev)。 确保检查返回值并且错误消息（如果存在）仍然适用。 同时转换取消注册的情况：

```c
-       ret = misc_register(&s3c2410wdt_miscdev);
+       ret = watchdog_register_device(&s3c2410_wdd);

...

-       misc_deregister(&s3c2410wdt_miscdev);
+       watchdog_unregister_device(&s3c2410_wdd);
```


# 8. Update the Kconfig-entry

驱动程序的条目现在需要选择 WATCHDOG_CORE：

- select WATCHDOG_CORE


# 9. Create a patch and send it to upstream

确保您理解 [提交补丁：将代码放入内核的基本指南](https://www.kernel.org/doc/html/latest/process/submitting-patches.html) 并将补丁发送到 <linux-watchdog@vger.kernel.org>。 我们很期待。
