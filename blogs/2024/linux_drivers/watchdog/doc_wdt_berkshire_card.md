
# Berkshire Products PC Watchdog Card

最后评论：2007/5/10

# 1. Support for ISA Cards Revision A and C

文档和驱动程序作者：Ken Hollis <kenji@bitgate.com>

PC 看门狗卡提供与 WDT 卡相同类型的功能，只是它不需要 IRQ 来运行。 此外，修订版 C 卡允许您监控任何 IO 端口以自动触发卡重置。 通过这种方式，您可以使卡监视硬盘驱动器状态或您需要的任何其他内容。

看门狗驱动程序有一个基本作用：与卡对话并向其发送信号，这样它就不会重置您的计算机……至少在正常操作期间。

看门狗驱动程序将自动找到您的看门狗卡，并附加一个正在运行的驱动程序以与该卡一起使用。 看门狗驱动程序初始化后，您可以使用 PC 看门狗程序与卡通信。

我建议在 fsck 开始之前放置“watchdog -d”，并在 fsck 结束后立即放置“watchdog -e -t 1”。 （记得运行程序时要加上“&”才能在后台运行！）

如果你想编写一个与PC Watchdog驱动程序兼容的程序，只需修改看门狗测试程序即可：tools/testing/selftests/watchdog/watchdog-test.c

其他 IOCTL 功能包括：

- **WDIOC_GETSUPPORT**

    这会返回卡本身的支持。 这会返回结构“PCWDS”，该结构返回：

    - **options = WDIOS_TEMPPANIC**

    (该卡支持温度)

    - **firmware_version = xxxx**

    (卡的固件版本)

- **WDIOC_GETSTATUS**

这将返回卡的状态，并将 AUDIO* 的位按位与放入值中。 （注释在include/uapi/linux/watchdog.h中）

- **WDIOC_GETBOOTSTATUS**

这将返回启动时报告的卡的状态。

- **WDIOC_GETTEMP**

这将返回卡的温度。 （您还可以读取 /dev/watchdog，它每秒更新一次温度。）

- **WDIOC_SETOPTIONS**

这允许您设置卡的选项。 您可以通过这种方式启用或禁用该卡。

- **WDIOC_KEEPALIVE**

这会对卡执行 ping 操作，告诉它不要重置您的计算机。

—Ken Hollis (kenji@bitgate.com)
