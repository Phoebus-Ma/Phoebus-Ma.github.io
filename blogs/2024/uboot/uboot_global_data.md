
# 1. Global data

全局必填字段保存在全局数据结构中。 指向该结构的指针可用作符号 gd。 该符号由宏 %DECLARE_GLOBAL_DATA_PTR 提供。


# 2. Register pointing to global data

在大多数架构上，全局数据指针存储在寄存器中。

| ARC        | r25      |
| ---------- | -------- |
| ARM 32bit  | r9       |
| ARM 64bit  | x18      |
| M68000     | d7       |
| MicroBlaze | r31      |
| Nios II    | gp       |
| PowerPC    | r2       |
| RISC-V     | gp (x3)  |
| SuperH     | r13      |
| x86 32bit  | fs       |

沙箱、x86_64 和 Xtensa 是值得注意的例外。

当前的实现使用 GD 指针的寄存器，因为这会导致代码更小。 然而，使用简单的全局数据作为 GD 指针也是可能的（而且更简单，因为它不需要为其保留特定的寄存器），但生成的代码更大。

Clang for ARM 不支持分配全局寄存器。 使用 Clang 时，gd 被定义为使用汇编代码的内联函数。 这会增加代码大小几个字节。

U-Boot 调用的二进制文件不知道寄存器的使用情况，也不会保存 gd。 UEFI 二进制文件调用 U-Boot 提供的 API，并可能返回到 U-Boot。 每次离开 U-Boot 时都必须保存 gd 的值，并在重新进入 U-Boot 时恢复 gd 的值。 这也与函数跟踪的实现相关。 为了设置 gd 的值，可以使用 set_gd() 函数。


# 3. Global data structure

--------
*struct global_data*

    全局数据结构体


## 3.1 Definition

```c
struct global_data {
    struct bd_info *bd;
    unsigned long flags;
    unsigned int baudrate;
    unsigned long cpu_clk;
    unsigned long bus_clk;
    unsigned long pci_clk;
    unsigned long mem_clk;
#if CONFIG_IS_ENABLED(VIDEO);
    unsigned long fb_base;
#endif;
#if defined(CONFIG_POST);
    unsigned long post_log_word;
    unsigned long post_log_res;
    unsigned long post_init_f_time;
#endif;
#ifdef CONFIG_BOARD_TYPES;
    unsigned long board_type;
#endif;
    unsigned long have_console;
#if CONFIG_IS_ENABLED(PRE_CONSOLE_BUFFER);
    long precon_buf_idx;
#endif;
    unsigned long env_addr;
    unsigned long env_valid;
    unsigned long env_has_init;
    int env_load_prio;
    unsigned long ram_base;
    phys_addr_t ram_top;
    unsigned long relocaddr;
    phys_size_t ram_size;
    unsigned long mon_len;
    unsigned long irq_sp;
    unsigned long start_addr_sp;
    unsigned long reloc_off;
    struct global_data *new_gd;
#ifdef CONFIG_DM;
    struct udevice *dm_root;
    struct udevice *dm_root_f;
    struct list_head uclass_root_s;
    struct list_head *uclass_root;
# if CONFIG_IS_ENABLED(OF_PLATDATA_DRIVER_RT);
    struct driver_rt *dm_driver_rt;
# endif;
#if CONFIG_IS_ENABLED(OF_PLATDATA_RT);
    struct udevice_rt *dm_udevice_rt;
    void *dm_priv_base;
# endif;
#endif;
#ifdef CONFIG_TIMER;
    struct udevice *timer;
#endif;
    const void *fdt_blob;
    void *new_fdt;
    unsigned long fdt_size;
    enum fdt_source_t fdt_src;
#if CONFIG_IS_ENABLED(OF_LIVE);
    struct device_node *of_root;
#endif;
#if CONFIG_IS_ENABLED(MULTI_DTB_FIT);
    const void *multi_dtb_fit;
#endif;
    struct jt_funcs *jt;
    char env_buf[32];
#ifdef CONFIG_TRACE;
    void *trace_buff;
#endif;
#if CONFIG_IS_ENABLED(SYS_I2C_LEGACY);
    int cur_i2c_bus;
#endif;
    unsigned int timebase_h;
    unsigned int timebase_l;
#if CONFIG_IS_ENABLED(CMD_BDINFO_EXTRA);
    unsigned long malloc_start;
#endif;
#if CONFIG_IS_ENABLED(SYS_MALLOC_F);
    unsigned long malloc_base;
    unsigned long malloc_limit;
    unsigned long malloc_ptr;
#endif;
#ifdef CONFIG_PCI;
    struct pci_controller *hose;
    phys_addr_t pci_ram_top;
#endif;
#ifdef CONFIG_PCI_BOOTDELAY;
    int pcidelay_done;
#endif;
    struct udevice *cur_serial_dev;
    struct arch_global_data arch;
#ifdef CONFIG_CONSOLE_RECORD;
    struct membuff console_out;
    struct membuff console_in;
#endif;
#if CONFIG_IS_ENABLED(VIDEO);
    ulong video_top;
    ulong video_bottom;
#endif;
#ifdef CONFIG_BOOTSTAGE;
    struct bootstage_data *bootstage;
    struct bootstage_data *new_bootstage;
#endif;
#ifdef CONFIG_LOG;
    int log_drop_count;
    int default_log_level;
    struct list_head log_head;
    int log_fmt;
    bool processing_msg;
    int logc_prev;
    int logl_prev;
    bool log_cont;
#endif;
#if CONFIG_IS_ENABLED(BLOBLIST);
    struct bloblist_hdr *bloblist;
    struct bloblist_hdr *new_bloblist;
#endif;
#if CONFIG_IS_ENABLED(HANDOFF);
    struct spl_handoff *spl_handoff;
#endif;
#if defined(CONFIG_TRANSLATION_OFFSET);
    fdt_addr_t translation_offset;
#endif;
#ifdef CONFIG_ACPI;
    struct acpi_ctx *acpi_ctx;
    ulong acpi_start;
#endif;
#if CONFIG_IS_ENABLED(GENERATE_SMBIOS_TABLE);
    char *smbios_version;
#endif;
#if CONFIG_IS_ENABLED(EVENT);
    struct event_state event_state;
#endif;
#ifdef CONFIG_CYCLIC;
    struct hlist_head cyclic_list;
#endif;
    struct list_head dmtag_list;
};
```


## 3.2 Members

**bd**

板级信息

**flags**

全局数据标志

查看 enum gd_flags

**baudrate**

串行接口的波特率

**cpu_clk**

CPU 时钟频率（以 Hz 为单位）

**bus_clk**

平台时钟频率（Hz）

**pci_clk**

PCI 时钟频率（以 Hz 为单位）

**mem_clk**

内存时钟频率（Hz）

**fb_base**

帧缓冲存储器的基地址

**post_log_word**

主动 POST 测试

post_log_word 是一个位掩码，定义记录哪些 POST 测试（请参阅常量 POST_*）。

**post_log_res**

POST 结果

post_log_res 是包含 POST 结果的位掩码。 值为 1 的位表示执行成功。

**post_init_f_time**

post_init_f() 启动的时间（以毫秒为单位）

**board_type**

板子类型

如果U-Boot配置支持多种板类型，则实际的板类型可以存储在该字段中。

**have_console**

控制台可用

值 1 表示调用了 serial_init() 并且控制台可用。 值 0 表示不应调用控制台输入和输出驱动程序。

**precon_buf_idx**

前控制台缓冲区索引

precon_buf_idx 指示在控制台可用之前用于收集输出的缓冲区的当前位置。 如果为负，则临时禁用预控制台缓冲区（在写出预控制台缓冲区时使用，以防止将其内容添加到自身）。

**env_addr**

环境结构体地址

env_addr 包含保存环境变量的结构的地址。

**env_valid**

环境有效

查看 enum env_valid

**env_has_init**

指示环境位置的位掩码

enum env_location 定义哪个位与哪个位置相关

**env_load_prio**

加载环境的优先级

**ram_base**

U-Boot 使用的 RAM 基地址

**ram_top**

U-Boot 使用的 RAM 顶部地址

**relocaddr**

U-Boot在RAM中的起始地址

重定位后，该字段指示 U-Boot 已重定位到的地址。 可以使用 bdinfo 命令显示它。 使用“add-symbol-file u-boot <relocaddr>”命令使用 GDB 进行调试时，需要使用它的值来显示源代码。

**ram_size**

RAM 大小（以字节为单位）

**mon_len**

监视器长度（以字节为单位）

**irq_sp**

IRQ堆栈指针

**start_addr_sp**

初始堆栈指针地址

**reloc_off**

重定向偏移量

**new_gd**

指向重定位全局数据的指针

**dm_root**

驱动程序模型的根实例

**dm_root_f**

预重定位根实例

**uclass_root_s**

当 uclass 不在只读内存中时，核心树的头部。

当uclass在只读内存中时，不使用uclass_root_s，uclass_root指向dtoc生成的根节点。

**uclass_root**

指向核心树头的指针，如果 uclass 位于只读内存中并且无法调整为使用 uclass_root 作为列表头。

当不在只读存储器中时，uclass_root_s用于保存uclass根，uclass_root指向uclass_root_s的地址。

**dm_driver_rt**

有关驾驶员的动态信息

**dm_udevice_rt**

有关 udevice 的动态信息

**dm_priv_base**

当 udevice 和 uclass 位于只读内存中时使用的 priv/plat 区域的基地址。 如果不使用则为 NULL

**timer**

驱动程序模型的计时器实例

**fdt_blob**

U-Boot自己的设备树，如果没有则为NULL

**new_fdt**

重新定位的设备树

**fdt_size**

为重新定位的设备空间保留的空间

**fdt_src**

FDT的源码

**of_root**

活树的根节点

**multi_dtb_fit**

指向未压缩的多 dtb FIT 图像的指针

**jt**

跳转表

跳转表包含指向导出函数的指针。 指向跳转表的指针被传递给独立应用程序。

**env_buf**

重新定位之前 env_get() 的缓冲区

**trace_buff**

跟踪缓冲区

当在 U-Boot 中跟踪函数时，该字段指向记录函数调用的缓冲区。

**cur_i2c_bus**

目前使用的I2C总线

**timebase_h**

定时器的高32位

**timebase_l**

定时器低32位

**malloc_start**

malloc() 区域的开始

**malloc_base**

早期 malloc() 的基地址

**malloc_limit**

早期malloc()的限制地址

**malloc_ptr**

早期 malloc() 的当前地址

**hose**

PCI 软管供早期使用

**pci_ram_top**

PCI 可访问区域的顶部

**pcidelay_done**

PIC 软管扫描到期前的延迟时间

如果CONFIG_PCI_BOOTDELAY=y，pci_hose_scan()在扫描之前等待环境变量pcidelay定义的毫秒数。 一旦此延迟到期，标志 pcidelay_done 将设置为 1。

**cur_serial_dev**

当前串行设备

**arch**

特定于架构的数据

**console_out**

用于控制台录制的输出缓冲区

该缓冲区用于收集控制台录制期间的输出。

**console_in**

用于控制台录制的输入缓冲区

如果激活控制台记录，则该缓冲区可用于模拟输入。

**video_top**

视频帧缓冲区顶部

**video_bottom**

视频帧缓冲区底部

**bootstage**

启动阶段信息

**new_bootstage**

重新定位启动阶段信息

**log_drop_count**

丢弃的日志消息数

对于无法处理的每条日志消息，此计数器都会递增，因为日志记录尚不可用，如标志中的标志 GD_FLG_LOG_READY 所指示的那样。

**default_log_level**

默认日志记录级别

对于没有过滤器的日志记录设备，default_log_level 定义日志记录级别，参见。 枚举 log_level_t。

**log_head**

记录设备列表

**log_fmt**

记录格式的位掩码

log_fmt 位掩码选择要在日志消息中显示的字段。 枚举 log_fmt 定义位掩码的位。

**processing_msg**

正在处理日志消息

该标志用于在处理另一条消息时抑制创建附加消息。

**logc_prev**

上一条消息的日志记录类别

该值用作连续消息的日志记录类别。

**logl_prev**

上一条消息的日志记录级别

该值用作连续消息的日志记录级别。

**log_cont**

上一个日志行未以 n 结束

这允许在同一行上链接日志消息

**bloblist**

blob 列表信息

**new_bloblist**

重新定位的 blob 列表信息

**spl_handoff**

SPL 交接信息

**translation_offset**

可选的平移偏移

请参阅 CONFIG_TRANSLATION_OFFSET。

**acpi_ctx**

ACPI 上下文指针

**acpi_start**

ACPI 表的起始地址

**smbios_version**

指向 SMBIOS 类型 0 版本

**event_state**

指向事件的当前状态

**cyclic_list**

已注册循环函数列表

**dmtag_list**

DM 标签列表

--------
*gd_board_type*

gd_board_type ()

检索板类型

**Parameters**

**Return**

全局板类型

--------
*enum gd_flags*

    全局数据标志


## 3.3 Constants

**GD_FLG_RELOC**

代码被重新定位到RAM

**GD_FLG_DEVINIT**

设备已初始化

**GD_FLG_SILENT**

静音模式

**GD_FLG_POSTFAIL**

关键 POST 测试失败

**GD_FLG_POSTSTOP**

POST 序列已中止

**GD_FLG_LOGINIT**

日志缓冲区已初始化

**GD_FLG_DISABLE_CONSOLE**

禁用控制台（输入和输出）

**GD_FLG_ENV_READY**

环境导入到哈希表中

**GD_FLG_SERIAL_READY**

预搬迁串行控制台就绪

**GD_FLG_FULL_MALLOC_INIT**

完整的 malloc() 已准备就绪

**GD_FLG_SPL_INIT**

spl_init() 已被调用

**GD_FLG_SKIP_RELOC**

不要重定向

**GD_FLG_RECORD**

记录控制台

**GD_FLG_RECORD_OVF**

记录控制台溢出

**GD_FLG_ENV_DEFAULT**

默认变量标志

**GD_FLG_SPL_EARLY_INIT**

早期 SPL 初始化已完成

**GD_FLG_LOG_READY**

日志系统可供使用

**GD_FLG_CYCLIC_RUNNING**

循环运行正在进行中

**GD_FLG_SKIP_LL_INIT**

不执行低级初始化

**GD_FLG_SMP_READY**

SMP初始化完成

**GD_FLG_FDT_CHANGED**

测试检测到设备树发生变化

**GD_FLG_OF_TAG_MIGRATE**

设备树有旧的 u-boot、dm- 标签

**GD_FLG_DM_DEAD**

驱动程序模型不可访问。 当用于保存其表的内存已被映射出来时，可以设置此值。

**GD_FLG_BLOBLIST_READY**

bloblist 已准备好使用

**GD_FLG_HUSH_OLD_PARSER**

使用 hush 旧解析器.

**GD_FLG_HUSH_MODERN_PARSER**

使用 hush 2021 解析器。


## 3.4 Description

查看字段标志 [struct global_data](https://docs.u-boot.org/en/latest/develop/global_data.html#c.global_data).
