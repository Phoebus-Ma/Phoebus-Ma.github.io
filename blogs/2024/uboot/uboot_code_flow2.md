

# Uboot启动流程：

```bash
_start  <arch/arm/lib/vectors.S>
    |--->reset  <arch/arm/cpu/armv7/start.S>
    |       |--->save_boot_params   <arch/arm/cpu/armv7/start.S>
    |                      |--->save_boot_params_ret    <arch/arm/cpu/armv7/start.S>
    |                                   |               // 禁止中断，设置cpu的模式为SVC
    |                                   |               // 清楚SCTLR的bit13，允许向量重定位，同时
                                        |               // 重定位向量表，把CP15的c12（VBAR）寄存器设置为
                                        |               // 0x87800000(uboot的起始地址，也是向量表的起始地址)
                                        |
                                        |--->cpu_init_cp15      <arch/arm/cpu/armv7/start.S>
                                        |                       // 设置cp15相关内容，比如关闭mmu，cache
                                        |
                                        |--->cpu_init_crit      <arch/arm/cpu/armv7/start.S>
                                        |           |
                                        |           |--->lowlevel_init  <arch/arm/cpu/armv7/lowlevel_init.S>
                                        |           |                   // 设置栈指针sp = 0x0091FF00,属于MX6ULL
                                        |           |                   // 的内部ram，同时(sp - GD_SIZE(248))-->sp
                                        |           |                   // 留出global_data数据结构的位置sp = 0x0091FE08
                                        |           |                   // 设置sp-->r9, sp==r9
                                        |           |
                                        |           |--->s_init     <arch/arm/cpu/armv7/mx6/soc.c>
                                        |           |               // 空函数，直接返回
                                        |
                                        |--->_main      <arch/arm/lib/crt0.S>
                                        |       // 设置sp为0x0091ff00,调用函数
                                        |       // board_init_f_alloc_reserve(arg:0x0091FF00)后，把sp设为
                                        |       // 此函数的返回值：0x0091FA00, r9(gd)设为0x0091FA00;
                                        |       // 调用board_init_f_init_reserve(arg:0x0091FA00)后
                                        |       // 把gb的成员malloc_base设为0x0091FB00(early_malloc的起始地址)
                                        |       // 调用board_init_f函数：会初始化gd，返回之后重新设置环境（sp和gd）
                                        |       // 把gd的成员start_addr(0x9EF44E90)赋值给sp, 此时sp == 0x9EF44E90
                                        |       // 是外部DDR的地址，gd->bd赋给r9(gd),新的gd结构在bd结构下面，
                                        |       // 重新设置gd = r9 - sizeof(*gd); lr = here. gd指向新的区域(DDR内)时
                                        |       // lr = here + 68,这是为什么？uboot的拷贝目的地址：0x9FF47000
                                        |--->board_init_f_alloc_reserve(arg:0x0091FF)    <common/init/board_init.c>
                                        |                       // 在包含此函数的文件中有：DECLARE_GLOBAL_DATA_PTR;
                                        |                       // 是个宏定义：#define  DECLARE_GLOBAL_DATA_PTR \_
                                        |                       //                 register volatile gd_t *gd asm("r9")
                                        |                       // 此函数设置留出早期malloc和global_data内存区域，
                                        |                       // 返回值：0x0091FA00
                                        |
                                        |--->board_init_f_init_reserve(arg:0x0091FA00)     <common/init/board_init.c>
                                        |                       // 此函数用于初始化gd所指向的结构（清零处理）
                                        |                       // 设置gd的成员malloc_base为0x91FB00
                                        |                       // 就是early_malloc的起始地
                                        |
                                        |--->board_init_f       <common/board_f.c>
                                        |                       // 主要做两个工作：初始化一系列外设（串口、定时器等）
                                        |                       // 初始化gd的各个成员变量(此时gd还保存在内部ocram中)。上面的工作都是通过在函数内运行
                                        |                       // initcall_sequence_f函数表中的一些函数来实现的，此函数表与
                                        |                       // board_init_f函数定义在相同的文件，是static属性的静态表
                                        |                       // 表中的函数执行完后会把gd->mon_len设为0xA8E74(__bss_end-_start),
                                        |                       // 也就是代码长度。gd->malloc_init设为0x400(malloc内存池的大小)
                                        |                       // gd->ram_size:0x20000000  gd->ram_top:0xA0000000  gd->relocaddr:0x9FF47000
                                        |                       // gd->arch.tlb_size:0x4000  gd->arch.tlb_addr:0x9FFF0000
                                        |
                                        |--->relocate_code(arg:0x9FF47000)      <arch/arm/lib/relocate.S>
                                        |                       // 代码拷贝。0x9FF47000是uboot拷贝目标首地址，offset=0x9FF47000-0x8780000,offset:0x18747000
                                        |                       // 拷贝源地址：__image_copy_start=0x87800000,结束地址：__image_copy_end =0x8785dd54
                                        |                       // 裸机程序运行需要链接地址与运行地址相同，uboot解决拷贝后的重定位问题是采用ld链接器
                                        |                       // 链接时使用选项'-pie'生成位置无关的可执行文件，使用此选项时会生成一个.rel.dyn段，
                                        |                       // uboot就是靠这个.rel.dyn来解决重定位问题的（.rel.dyn 段是存放.text 段中需要重定位地址的集合）
                                        |                       // 修改.rel.dyn中的label来重定位
                                        |
                                        |--->relocate_vectors       <arch/arm/lib/relocate.S>
                                        |                       // 重定位向量表，将CP15的VBAR寄存器的值设为0x9FF47000，uboot拷贝后的目标首地址
                                        |
                                        |--->c_runtime_cpu_setup      <arch/arm/cpu/armv7/start.S>
                                        |
                                        |--->board_init_r       <common/board_r.c>
                                        |           |           // 初始化一些在board_init_f函数中未初始化的一些外设，做些后续工作。
                                        |           |           // 是通过运行init_sequence_r函数集合中的函数来实现的，init_sequence_r与board_init_f函数
                                        |           |           // 在同一个文件。在函数集合中initr_reloc_global_data函数初始化重定位后的gd的一些成员变量
                                        |           |           // 集合中的其他函数：初始化了malloc、串口、电源芯片、emmc、环境变量、LCD、初始化跳转表、中断，使能中断
                                        |           |           // 初始化网络地址（获取MAC地址，通过读取环境变量ethaddr的值，环境变量保存在emmc中）、
                                        |           |           // 初始化网络设备，最后执行run_main_loop函数，主循环（处理命令）
                                        |           |
                                        |           |--->run_main_loop      <common/board_r.c>
                                                            |            // uboot启动以后会进入3秒倒计时，如果在3秒倒计时结束之前按下按下回车键，那么就
                                                            |            // 会进入uboot的命令模式，如果倒计时结束以后都没有按下回车键，那么就会自动启动Linux内
                                                            |            // 核，这个功能就是由run_main_loop函数来完成的.
                                                            |
                                                            |--->main_loop(void)    <common/main.c>
                                                            |           |        // 如果如果倒计时结束之前按下按键，那么就会执行cli_loop函数，这个就是
                                                            |           |        // 命令处理函数，负责接收好处理输入的命令。
                                                            |           |
                                                            |           |--->bootstage_mark_name
                                                            |           |                   // 打印处启动进度
                                                                        |
                                                                        |--->autoboot_command
                                                                        |                   // 此函数就是检查倒计时是否结束？倒计时结束之前有没有被打断？
                                                                        |
                                                                        |--->cli_loop       <common/cli.c>
                                                                        |       |     // cli_loop函数是uboot的命令行处理函数，我们在uboot中输入
                                                                        |       |     // 各种命令，进行各种操作就是由cli_loop来处理的
                                                                        |       |
                                                                        |       |--->parse_file_outer
                                                                                            |
                                                                                            |--->setup_file_in_str
                                                                                            |
                                                                                            |--->parse_stream_outer
                                                                                            |                   // 这个函数就是 hush shell 的命令解释器，
                                                                                            |                   // 负责接收命令行输入，然后解析并执行相应的命令
```
