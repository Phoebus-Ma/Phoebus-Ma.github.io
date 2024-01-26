
# The Linux Watchdog driver API

最后评论：2007/10/5

Copyright 2002 Christer Weingel <wingel@nano-system.com>

本文档的某些部分是从 sbc60xxwdt 驱动程序逐字复制的，该驱动程序 (c) 版权所有 2000 Jakob Oestergaard <jakob@ostenfeld.dk>

本文档描述了 Linux 2.4.18 内核的状态。


# 1. Introduction

看门狗定时器（WDT）是一种硬件电路，可以在软件故障时重置计算机系统。 你可能已经知道了。

通常，用户空间守护程序会定期通过 /dev/watchdog 特殊设备文件通知内核看门狗驱动程序用户空间仍然处于活动状态。 当这样的通知发生时，驱动程序通常会告诉硬件看门狗一切正常，并且看门狗应该再等待一段时间来重置系统。 如果用户空间出现故障（RAM 错误、内核错误等），通知将停止发生，并且硬件看门狗将在超时后重置系统（导致重新启动）。

Linux 看门狗 API 是一个相当特殊的结构，不同的驱动程序实现不同的、有时甚至是不兼容的部分。 该文件试图记录现有的用法并允许未来的驱动程序编写者将其用作参考。


# 2. The simplest API

所有驱动程序都支持基本操作模式，一旦 /dev/watchdog 打开，看门狗就会激活，并且除非在一定时间内 ping 通看门狗，否则看门狗将重新启动，该时间称为超时或裕度。 ping 看门狗的最简单方法是将一些数据写入设备。 因此，一个非常简单的看门狗守护进程将类似于以下源文件：请参阅样本/watchdog/watchdog-simple.c

例如，更高级的驱动程序可以在执行写入调用以 ping 看门狗之前检查 HTTP 服务器是否仍在响应。

当设备关闭时，看门狗被禁用，除非支持“Magic Close”功能（见下文）。 这并不总是一个好主意，因为如果看门狗守护程序中存在错误并且崩溃，系统将不会重新启动。 因此，某些驱动程序支持配置选项“关闭时禁用看门狗关闭”CONFIG_WATCHDOG_NOWAYOUT。 如果在编译内核时将其设置为Y，则一旦启动就无法禁用看门狗。 因此，如果看门狗守护进程崩溃，系统将在超时后重新启动。 看门狗设备通常还支持 nowayout 模块参数，以便可以在运行时控制该选项。


# 3. Magic Close feature

如果驱动程序支持“Magic Close”，则驱动程序不会禁用看门狗，除非在关闭文件之前将特定的魔术字符“V”发送到 /dev/watchdog。 如果用户空间守护进程关闭文件而不发送此特殊字符，则驱动程序将假定守护进程（以及一般用户空间）已死亡，并且将停止对看门狗执行 ping 操作，而不先禁用它。 如果看门狗没有在足够的时间内重新打开，这将导致重新启动。


# 4. The ioctl API

所有符合标准的驱动程序还支持 ioctl API。

使用 ioctl 对看门狗执行 ping 操作：

所有具有 ioctl 接口的驱动程序都至少支持一个 ioctl，即 KEEPALIVE。 此 ioctl 执行的操作与写入看门狗设备完全相同，因此上述程序中的主循环可以替换为：

```c
while (1) {
        ioctl(fd, WDIOC_KEEPALIVE, 0);
        sleep(10);
}
```

ioctl 的参数被忽略。


# 5. Setting and getting the timeout

对于某些驱动程序，可以使用 SETTIMEOUT ioctl 动态修改看门狗超时，这些驱动程序在其选项字段中设置了 WDIOF_SETTIMEOUT 标志。 参数是一个整数，表示超时（以秒为单位）。 驱动程序返回同一变量中使用的实际超时，由于硬件的限制，该超时可能与请求的超时不同：

```c
int timeout = 45;
ioctl(fd, WDIOC_SETTIMEOUT, &timeout);
printf("The timeout was set to %d seconds\n", timeout);
```

如果设备的超时粒度为分钟，则此示例实际上可能会打印“超时设置为 60 秒”。

从 Linux 2.4.18 内核开始，可以使用 GETTIMEOUT ioctl 查询当前超时：

```c
ioctl(fd, WDIOC_GETTIMEOUT, &timeout);
printf("The timeout was is %d seconds\n", timeout);
```


# 6. Pretimeouts

某些看门狗定时器可以设置为在实际重置系统之前触发触发器。 这可以通过 NMI、中断或其他机制来完成。 这允许 Linux 在重置之前记录有用的信息（例如恐慌信息和内核核心转储）：

```c
pretimeout = 10;
ioctl(fd, WDIOC_SETPRETIMEOUT, &pretimeout);
```

请注意，预超时是超时发生之前的秒数。 它不是距离预超时的秒数。 因此，例如，如果将超时设置为 60 秒，将预超时设置为 10 秒，则预超时将在 50 秒后结束。 将预超时设置为零会禁用它。

还有一个 get 函数用于获取超时时间：

```c
ioctl(fd, WDIOC_GETPRETIMEOUT, &timeout);
printf("The pretimeout was is %d seconds\n", timeout);
```

并非所有看门狗驱动程序都支持预超时。


# 7. Get the number of seconds before reboot

一些看门狗驱动程序能够报告系统重新启动之前的剩余时间。 WDIOC_GETTIMELEFT 是返回重启前秒数的 ioctl：

```c
ioctl(fd, WDIOC_GETTIMELEFT, &timeleft);
printf("The timeout was is %d seconds\n", timeleft);
```


# 8. Environmental monitoring

所有看门狗驱动程序都需要返回有关系统的更多信息，有些驱动程序进行温度、风扇和功率级别监控，有些驱动程序可以告诉您系统上次重新启动的原因。 GETSUPPORT ioctl 可用于询问设备可以做什么：

```c
struct watchdog_info ident;
ioctl(fd, WDIOC_GETSUPPORT, &ident);
```

ident 结构中返回的字段是：

|                  |                          |
| ---------------- | ------------------------ |
| identity         | 标识看门狗驱动程序的字符串 |
| firmware_version | 卡的固件版本（如果有）     |
| options          | 描述设备支持的标志         |

options 字段可以设置以下位，并描述 GET_STATUS 和 GET_BOOT_STATUS ioctl 可以返回什么类型的信息。

|                |            |
| -------------- | ---------- |
| WDIOF_OVERHEAT | CPU过热复位 |

由于超出热限制，看门狗上次重新启动了机器：

|                |            |
| -------------- | ---------- |
| WDIOF_FANFAULT | Fan failed |

看门狗卡监控的系统风扇故障

|               |                  |
| ------------- | ---------------- |
| WDIOF_EXTERN1 | External relay 1 |

外部监控继电器/源 1 已触发。 用于现实世界应用的控制器包括将触发重置的外部监控引脚。

|               |                  |
| ------------- | ---------------- |
| WDIOF_EXTERN2 | External relay 2 |

外部监控继电器/源 2 已触发

|                  |                       |
| ---------------- | --------------------- |
| WDIOF_POWERUNDER | Power bad/power fault |

机器显示欠压状态

|                 |                |
| --------------- | -------------- |
| WDIOF_CARDRESET | 卡之前重置了CPU |

上次重启是看门狗卡引起的

|                 |                    |
| --------------- | ------------------ |
| WDIOF_POWEROVER | Power over voltage |

机器显示过压状态。 请注意，如果一个级别低于且高于两个位都将被设置 - 这可能看起来很奇怪，但很有意义。

|                     |                       |
| ------------------- | --------------------- |
| WDIOF_KEEPALIVEPING | Keep alive ping reply |

自上次查询以来，看门狗看到了一个 keepalive ping。

|                  |                         |
| ---------------- | ----------------------- |
| WDIOF_SETTIMEOUT | Can set/get the timeout |

看门狗可以进行预超时。

|                  |                            |
| ---------------- | -------------------------- |
| WDIOF_PRETIMEOUT | 超时（以秒为单位），获取/设置 |

对于那些返回选项字段中设置的任何位的驱动程序，可以使用 GETSTATUS 和 GETBOOTSTATUS ioctl 分别询问当前状态和上次重新启动时的状态：

```c
int flags;
ioctl(fd, WDIOC_GETSTATUS, &flags);

or

ioctl(fd, WDIOC_GETBOOTSTATUS, &flags);
```

请注意，并非所有设备都支持这两个调用，有些设备仅支持 GETBOOTSTATUS 调用。

某些驱动程序可以使用 GETTEMP ioctl 测量温度。 返回值是以华氏度为单位的温度：

```c
int temperature;
ioctl(fd, WDIOC_GETTEMP, &temperature);
```

最后，SETOPTIONS ioctl 可用于控制卡操作的某些方面：

```c
int options = 0;
ioctl(fd, WDIOC_SETOPTIONS, &options);
```

可以使用以下选项：

|                   |                  |
| ----------------- | ---------------- |
| WDIOS_DISABLECARD | 关闭看门狗定时器   |
| WDIOS_ENABLECARD  | 打开看门狗定时器   |
| WDIOS_TEMPPANIC   | 温度跳变时内核恐慌 |

[FIXME -- better explanations]
