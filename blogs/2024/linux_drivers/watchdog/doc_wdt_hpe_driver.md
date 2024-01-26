
# HPE iLO NMI Watchdog Driver

# 1. for iLO based ProLiant Servers

最后评论：2018/8/20

HPE iLO NMI Watchdog 驱动程序是一个内核模块，为 iLO“生成 NMI 到系统”虚拟按钮提供基本的看门狗功能和处理程序。

本文档中所有对 iLO 的引用都意味着它也适用于 iLO2 及所有后续版本。

像任何其他常见的看门狗驱动程序一样启用看门狗功能。 也就是说，需要启动一个应用程序来启动看门狗定时器。 一个基本的应用程序存在于tools/testing/selftests/watchdog/中，名为watchdog-test.c。 只需编译 C 文件并启动即可。 如果系统进入不良状态并挂起，HPE ProLiant iLO 计时器寄存器将无法及时更新，并且会发生硬件系统重置（也称为自动服务器恢复 (ASR)）事件。

hpwdt 驱动程序还具有以下模块参数：

|              ||
| ------------ | - |
| soft_margin  | 允许用户设置看门狗定时器值。 默认值为 30 秒。 |
| timeout      | soft_margin 的别名。 |
| pretimeout   | 允许用户设置看门狗预超时值。 这是 NMI 传送到系统时超时之前的秒数。 将该值设置为零会禁用预超时 NMI。 默认值为 9 秒。 |
| nowayout     | 基本看门狗参数，不允许重新启动定时器或转义即将发生的 ASR。 默认值是在编译内核时设置的。 如果将其设置为“Y”，则看门狗一旦启动就无法禁用。 |
| kdumptimeout | 在调用恐慌之前收到 NMI 时应用的最短超时（以秒为单位）。 (-1) 禁用看门狗。 当值 > 0 时，定时器将使用该值或当前超时值中的较大者重新编程。 |

NOTE:

有关看门狗驱动程序的更多信息，包括 /dev/watchdog 的 ioctl 接口，可以在 [Linux 看门狗驱动程序 API](https://www.kernel.org/doc/html/latest/watchdog/watchdog-api.html) 和 [Linux IPMI 驱动程序](https://www.kernel.org/doc/html/latest/driver-api/ipmi.html)。

由于 iLO 硬件的限制，NMI 预超时（如果启用）只能设置为 9 秒。 尝试将预超时设置为其他非零值将被四舍五入，可能为零。 用户在尝试设置预超时或超时后应验证预超时值。

从 iLO 收到 NMI 后，hpwdt 驱动程序将引发紧急情况。 这是为了允许收集故障转储。 用户有责任为 kdump 正确配置系统。

Linux 内核在出现紧急情况时的默认行为是打印内核逻辑删除并永远循环。 这通常不是看门狗用户想要的。

想要了解更多信息的人请参阅：

- [Kdump 文档 - 基于 kexec 的故障转储解决方案](https://www.kernel.org/doc/html/latest/admin-guide/kdump/kdump.html)
- [内核的命令行参数](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html) (panic=)
- 您的 Linux 发行版特定文档。

如果 hpwdt 未收到与到期计时器关联的 NMI，并且计时器尚未更新，则 iLO 将在超时时继续重置系统。

HPE iLO NMI Watchdog 驱动程序和文档最初由 Tom Mingarelli 开发。
