
# 1. U-boot Logging

## 1.1 Log level

以 u-boot-2023.10/configs/sandbox_defconfig 为例：

```bash
CONFIG_LOG=y
CONFIG_LOG_MAX_LEVEL=9
CONFIG_LOG_DEFAULT_LEVEL=6
```

从其配置信息中可以看出，启用了log功能，log等级最大9级，只输出6级以上的log。它们作用于以下封装宏：

u-boot/include/log.h

```c
#define log_emer(_fmt...)	log(LOG_CATEGORY, LOGL_EMERG, ##_fmt)
#define log_alert(_fmt...)	log(LOG_CATEGORY, LOGL_ALERT, ##_fmt)
#define log_crit(_fmt...)	log(LOG_CATEGORY, LOGL_CRIT, ##_fmt)
#define log_err(_fmt...)	log(LOG_CATEGORY, LOGL_ERR, ##_fmt)
#define log_warning(_fmt...)	log(LOG_CATEGORY, LOGL_WARNING, ##_fmt)
#define log_notice(_fmt...)	log(LOG_CATEGORY, LOGL_NOTICE, ##_fmt)
#define log_info(_fmt...)	log(LOG_CATEGORY, LOGL_INFO, ##_fmt)
#define log_debug(_fmt...)	log(LOG_CATEGORY, LOGL_DEBUG, ##_fmt)
#define log_content(_fmt...)	log(LOG_CATEGORY, LOGL_DEBUG_CONTENT, ##_fmt)
#define log_io(_fmt...)		log(LOG_CATEGORY, LOGL_DEBUG_IO, ##_fmt)
#define log_cont(_fmt...)	log(LOGC_CONT, LOGL_CONT, ##_fmt)
```

从 enum log_level_t 中可知，LOGL_INFO = 6，LOGL_DEBUG = 7，所以默认的 sandbox_defconfig 只输出 debug 级别以上的log。

从 enum log_category_t 中可知，log 数据可以来自不同的类别，在 uboot 中可以使用过滤器对消息来源进行过滤。


## 1.2 Debug

DEBUG 宏定义在 u-boot/include/log.h 中：

```c
#ifdef DEBUG
#define _DEBUG	1
#else
#define _DEBUG	0
#endif
```

- 如果想进行全局的数据 debug，可以在 u-boot/include/log.h 的 #ifdef DEBUG之前，做如下声明：

```c
#define DEBUG
```

- 如果想单独对某个文件进行debug，则在文件的开始(头文件之前)进行声明：

```c
#define DEBUG
```

一旦做了 DEBUG 声明，无论 CONFIG_LOG_DEFAULT_LEVEL 是什么值，都会打印 debug 信息。

主要的日志记录功能是：log()，此外还有 debug() 和 error()。

它们(1.1 和 1.2 节均适用)使用 LOG_CATEGORY 作为类别，因此，在调试某些文件时，可以在文件的开始定义你想获取的类别，来缩小调试范围：

```c
#define LOG_CATEGORY LOGC_ALLOC
```

或者：

```c
#define LOG_CATEGORY UCLASS_SPI
```


## 1.3 Log way

U-Boot 提供了几种可能的日志信息确定方式，所有这些都可以独立启用或禁用：

* console - 转到标准输出
* syslog - 在 UDP 端口 514 上向系统日志服务器广播 RFC 3164 消息

syslog 驱动程序将环境变量 'log_hostname' 的值作为 HOSTNAME 发送（如果可用）。


## 1.4 Filters

过滤器附加到日志驱动程序以控制这些驱动程序发出的内容。 过滤器在匹配日志消息时可以允许或拒绝日志消息。 只有过滤器允许的记录才会到达驱动程序。

过滤器可以基于几个标准：

* 最小或最大日志级别
* 在一组类别中
* 在一组文件中

如果没有过滤器附加到驱动程序，则使用默认过滤器，该过滤器将输出限制为级别低于 CONFIG_MAX_LOG_LEVEL 的记录。

使用示例：

要在运行时添加新过滤器，请使用“log filter-add”命令。 例如，要抑制来自 SPI 和 MMC 子系统的消息，请运行：

```bash
log filter-add -D -c spi -c mmc
```

您还需要添加另一个过滤器以允许其他消息（因为默认过滤器不再适用）：

```bash
log filter-add -A -l info
```

日志级别可以是符号名称（如上）或数字。 例如，要禁用来自`drivers/core/lists.c`和`drivers/core/ofnode.c`的所有调试及以上（日志级别 7）消息，请运行：

```bash
log filter-add -D -f drivers/core/lists.c,drivers/core/ofnode.c -L 7
```

要查看活动过滤器，请使用'log filter-list'命令。 一些示例输出是：

```bash
=> log filter-list
num policy level            categories files
    2   deny >= DEBUG               drivers/core/lists.c,drivers/core/ofnode.c
    0   deny <= IO                  spi
                                    mmc
    1  allow <= INFO
```

请注意，过滤器是按从上到下的顺序处理的，而不是按照过滤器编号的顺序。 如果过滤器在匹配时拒绝，则添加到列表顶部；如果在匹配时允许，则添加到底部。 有关更多信息，请通过运行“help log”来查阅“log”命令的用法。
