
# Tables

- [1. Analyzing crash dumps](#1-analyzing-crash-dumps)
- [2. Tracing in u-boot](#2-tracing-in-u-boot)


# 1. Analyzing crash dumps

当CPU检测到一条它无法执行的指令时，它会引发一个中断。 然后 U-Boot 写入故障转储。 本章介绍如何分析此类转储。


## 1.1 Creating a crash dump voluntarily

为了描述故障转储的分析，我们需要一个示例。 U-Boot 附带了一个命令 [exception](https://docs.u-boot.org/en/latest/usage/cmd/exception.html)，在这里可以派上用场。 该命令通过以下方式启用：

```bash
CONFIG_CMD_EXCEPTION=y
```

下面的示例输出是在 QEMU 上运行 qemu_arm64_defconfig 时记录的：

```bash
=> exception undefined
"Synchronous Abort" handler, esr 0x02000000
elr: 00000000000101fc lr : 00000000000214ec (reloc)
elr: 000000007ff291fc lr : 000000007ff3a4ec
x0 : 000000007ffbd7f8 x1 : 0000000000000000
x2 : 0000000000000001 x3 : 000000007eedce18
x4 : 000000007ff291fc x5 : 000000007eedce50
x6 : 0000000000000064 x7 : 000000007eedce10
x8 : 0000000000000000 x9 : 0000000000000004
x10: 6db6db6db6db6db7 x11: 000000000000000d
x12: 0000000000000006 x13: 000000000001869f
x14: 000000007edd7dc0 x15: 0000000000000002
x16: 000000007ff291fc x17: 0000000000000000
x18: 000000007eed8dc0 x19: 0000000000000000
x20: 000000007ffbd7f8 x21: 0000000000000000
x22: 000000007eedce10 x23: 0000000000000002
x24: 000000007ffd4c80 x25: 0000000000000000
x26: 0000000000000000 x27: 0000000000000000
x28: 000000007eedce70 x29: 000000007edd7b40

Code: b00003c0 912ad000 940029d6 17ffff52 (e7f7defb)
Resetting CPU ...

resetting ...
```

第一行为我们提供了发生的中断类型。 在 ARMv8 上，同步中止是命中未分配指令时引发的异常。 异常综合症寄存器ESR寄存器包含描述异常原因的信息。 此处设置的位 25 表示 32 位指令导致了异常。

第二行提供了 elr 和 lr 寄存器减去重定位偏移量后的内容。 - U-Boot 加载后会自行重新定位。 - 重定位偏移量也可以使用 bdinfo 命令显示。

在寄存器的内容之后，我们得到一行指示崩溃之前指令的机器代码，并在括号中指示导致转储的指令。


## 1.2 Analyzing the code location

我们可以使用 objdump 命令将以“Code:”开头的行中的指令转换为助记符。 为了使事情变得更容易，提供了脚本/解码：

```bash
$echo 'Code: b00003c0 912ad000 940029d6 17ffff52 (e7f7defb)' | \
  CROSS_COMPILE=aarch64-linux-gnu- ARCH=arm64 scripts/decodecode
Code: b00003c0 912ad000 940029d6 17ffff52 (e7f7defb)
All code
========
   0:   b00003c0     adrp   x0, 0x79000
   4:   912ad000     add    x0, x0, #0xab4
   8:   940029d6     bl     0xa760
   c:   17ffff52     b      0xfffffffffffffd54
  10:*  e7f7defb     .inst  0xe7f7defb ; undefined <-- trapping instruction

Code starting with the faulting instruction
===========================================
   0:   e7f7defb     .inst  0xe7f7defb ; undefined
```

现在，让我们在减去重定位偏移量后使用 elr 和 lr 寄存器提供的位置来找出代码中发生崩溃的位置以及调用它的位置。

文件 u-boot.map 包含 U-Boot 二进制文件的内存布局。 在这里我们找到这些行：

```bash
.text.do_undefined
               0x00000000000101fc        0xc cmd/built-in.o
.text.exception_complete
               0x0000000000010208       0x90 cmd/built-in.o
...
.text.cmd_process
               0x00000000000213b8      0x164 common/built-in.o
               0x00000000000213b8                cmd_process
.text.cmd_process_error
               0x000000000002151c       0x40 common/built-in.o
               0x000000000002151c                cmd_process_error
```

因此，错误发生在函数 do_undefined() 的开头，并且该函数是从函数 cmd_process() 内部的某个位置调用的。

如果我们想更深入地研究，我们可以反汇编 U-Boot 二进制文件：

```bash
$ aarch64-linux-gnu-objdump -S -D u-boot | less

00000000000101fc <do_undefined>:
{
        /*
         * 0xe7f...f.   is undefined in ARM mode
         * 0xde..       is undefined in Thumb mode
        */
        asm volatile (".word 0xe7f7defb\n");
   101fc:       e7f7defb        .inst   0xe7f7defb ; undefined
        return CMD_RET_FAILURE;
}
10200:       52800020        mov     w0, #0x1        // #1
10204:       d65f03c0        ret
```

此示例基于 ARMv8 架构，但相同的过程也可用于其他架构。


## 1.3 Crashs in UEFI binaries

如果发生崩溃时加载 UEFI 映像，则会显示其加载地址。 如果进程计数器指向加载的 UEFI 二进制文件中的地址，则会指示相对进程计数器位置。 以下是在 U-Boot 沙箱上执行的示例：

```bash
=> load host 0:1 $kernel_addr_r buggy.efi
5632 bytes read in 0 ms
=> bootefi $kernel_addr_r
Booting /buggy.efi
Buggy world!

Segmentation violation
pc = 0x19fc264c, pc_reloc = 0xffffaa4688b1664c

UEFI image [0x0000000019fc0000:0x0000000019fc6137] pc=0x264c '/buggy.efi'
```

崩溃发生在 UEFI 二进制 buggy.efi 的相对位置 0x264c 处。 反汇编可用于查找实际的源代码位置：

```bash
$ x86_64-linux-gnu-objdump -S -D buggy_efi.so

0000000000002640 <memset>:
    2640:       f3 0f 1e fa             endbr64
    2644:       48 89 f8                mov    %rdi,%rax
    2647:       48 89 f9                mov    %rdi,%rcx
    264a:       eb 0b                   jmp    2657 <memset+0x17>
    264c:       40 88 31                mov    %sil,(%rcx)
```


## 1.4 Architecture specific details

**ARMv8**

在 ARM 64 位架构上，CONFIG_ARMV8_SPL_EXCEPTION_VECTORS 控制是否在辅助程序加载器 (SPL) 中设置异常向量表。 如果没有初始化表，则无法显示故障转储。 大多数主板上默认禁用该功能，以减少 SPL 的大小。

**RISC-V**

在 RISC-V 架构上，必须指定 CONFIG_SHOW_REGS=y 才能显示故障转储中的所有寄存器。

**Sandbox**

必须使用参数 -S 调用沙箱 U-Boot 二进制文件才能显示故障转储：

```bash
./u-boot -S -T
```

仅当 CONFIG_SANDBOX_CRASH_RESET=y 时，沙箱才会在崩溃后重新启动。


# 2. Tracing in U-Boot

U-Boot 支持简单的跟踪功能，该功能允许收集执行记录并将其发送到主机进行分析。 目前，其主要用途是分析启动时间。


## 2.1 Overview

跟踪功能使用 GCC 的仪器函数功能来跟踪所有函数入口/出口点。 然后将它们记录在内存缓冲区中。 可以使用 tftpput 通过网络链接将内存缓冲区保存到主机，或者写入附加的存储设备（例如 MMC）。

在主机上，文件首先使用名为“proftool”的工具进行转换，该工具从中提取有用的信息。 生成的跟踪输出类似于 Linux 的 ftrace 功能发出的输出，因此可以通过 kernelshark 直观地显示 (查看 [kernelshark](https://kernelshark.org/)) 并与“trace-cmd report”一起使用 (see [trace_cmd](https://www.trace-cmd.org/)).

还可以生成与 Flamegraph.pl 一起使用的火焰图 (查看 [flamegraph_pl](https://github.com/brendangregg/FlameGraph/blob/master/flamegraph.pl)).


## 2.2 Quick-start using Sandbox

Sandbox 是 U-Boot 的一个版本，可以在 Linux 下运行，因此在实际开发板上使用它之前，这是一种尝试跟踪的便捷方法。 为此，请按照下列步骤操作：

将以下内容添加到 config/sandbox_defconfig 中：

```bash
CONFIG_TRACE=y
```

构建启用跟踪的沙箱 U-Boot：

```bash
make FTRACE=1 O=sandbox sandbox_config
make FTRACE=1 O=sandbox
```

运行沙箱，等待一些跟踪信息出现，然后捕获跟踪：

```bash
./sandbox/u-boot

U-Boot 2013.04-rc2-00100-ga72fcef (Apr 17 2013 - 19:25:24)

DRAM:  128 MiB
trace: enabled
Using default environment

In:    serial
Out:   serial
Err:   serial
=>trace stats
    671,406 function sites
     69,712 function calls
          0 untracked function calls
     73,373 traced function calls
         16 maximum observed call depth
         15 call depth limit
     66,491 calls not traced due to depth
=>trace stats
    671,406 function sites
  1,279,450 function calls
          0 untracked function calls
    950,490 traced function calls (333217 dropped due to overflow)
         16 maximum observed call depth
         15 call depth limit
      1,275,767 calls not traced due to depth
=>trace calls 1000000 e00000
Call list dumped to 00000000, size 0xae0a40
=>print
baudrate=115200
profbase=0
profoffset=ae0a40
profsize=e00000
stderr=serial
stdin=serial
stdout=serial

Environment size: 117/8188 bytes
=>host save hostfs - 1000000 trace ${profoffset}
11405888 bytes written in 10 ms (1.1 GiB/s)
=>reset
```

然后运行proftool将trace信息转换为ftrace格式：

```bash
./sandbox/tools/proftool -m sandbox/System.map -t trace dump-ftrace -o trace.dat
```

最后运行 kernelshark 来显示它（注意它仅适用于 .dat 文件！）：

```bash
kernelshark trace.dat
```

使用此工具，您可以查看跟踪记录并查看每个函数的时间戳。

[!Kernelshark 显示函数跟踪记录]()

要在控制台上查看记录，请使用trace-cmd：

```bash
trace-cmd report trace.dat | less
cpus=1
      u-boot-1     [000]     3.116364: function:             initf_malloc
      u-boot-1     [000]     3.116375: function:             initf_malloc
      u-boot-1     [000]     3.116386: function:             initf_bootstage
      u-boot-1     [000]     3.116396: function:                bootstage_init
      u-boot-1     [000]     3.116408: function:                   malloc
      u-boot-1     [000]     3.116418: function:                      malloc_simple
      u-boot-1     [000]     3.116429: function:                         alloc_simple
      u-boot-1     [000]     3.116441: function:                         alloc_simple
      u-boot-1     [000]     3.116449: function:                      malloc_simple
      u-boot-1     [000]     3.116457: function:                   malloc
```

请注意，pytimechart 已过时，因此无法再使用。

有一个 -f 选项可用于选择函数图：

```bash
./sandbox/tools/proftool -m sandbox/System.map -t trace -f funcgraph dump-ftrace -o trace.dat
```

同样，您可以使用 kernelshark 或trace-cmd 来查看输出。 在这种情况下，您将看到每个函数所花费的时间与其退出记录相对应。

[!Kernelshark 显示函数图记录]()

```bash
trace-cmd report trace.dat | less
cpus=1
          u-boot-1     [000]     3.116364: funcgraph_entry:        0.011 us   |    initf_malloc();
          u-boot-1     [000]     3.116386: funcgraph_entry:                   |    initf_bootstage() {
          u-boot-1     [000]     3.116396: funcgraph_entry:                   |      bootstage_init() {
          u-boot-1     [000]     3.116408: funcgraph_entry:                   |        malloc() {
          u-boot-1     [000]     3.116418: funcgraph_entry:                   |          malloc_simple() {
          u-boot-1     [000]     3.116429: funcgraph_entry:        0.012 us   |            alloc_simple();
          u-boot-1     [000]     3.116449: funcgraph_exit:         0.031 us   |            }
          u-boot-1     [000]     3.116457: funcgraph_exit:         0.049 us   |          }
          u-boot-1     [000]     3.116466: funcgraph_entry:        0.063 us   |        memset();
          u-boot-1     [000]     3.116539: funcgraph_exit:         0.143 us   |        }
```


## 2.3 Flame graph

使用 dump-flamegraph 命令还可以使用一些简单的火焰图选项：

```bash
./sandbox/tools/proftool -m sandbox/System.map -t trace dump-flamegraph -o trace.fg
flamegraph.pl trace.fg >trace.svg
```

您可以将 .svg 文件加载到查看器中。 如果您使用 Chrome（以及其他一些程序），您可以单击周围并放大和缩小。

[!Chrome 显示了 Flamegraph.pl 输出 Chrome 显示了 Flamegraph.pl 输出的放大情况]()

还可以使用计时变体，它可以了解每个调用堆栈花费了多少时间：

```bash
./sandbox/tools/proftool -m sandbox/System.map -t trace dump-flamegraph -f timing -o trace.fg
flamegraph.pl trace.fg >trace.svg
```

请注意，跟踪收集确实会减慢执行速度，因此时间会被夸大。 它们应该用于指导优化。 为了获得准确的启动时间，请使用 bootstage。

[!Chrome 显示 Flamegraph.pl 输出和计时]()


## 2.4 CONFIG Options

**CONFIG_TRACE**

启用 U-Boot 中的跟踪功能。

**CONFIG_CMD_TRACE**

启用跟踪命令。

**CONFIG_TRACE_BUFFER_SIZE**

为 U-Boot 分配的跟踪缓冲区的大小。 该缓冲区在重定位后使用，作为放置函数跟踪信息的地方。 缓冲区的地址由重定位代码确定。

**CONFIG_TRACE_EARLY**

将此定义为在重定位之前尽早开始跟踪。

**CONFIG_TRACE_EARLY_SIZE**

“早期”跟踪缓冲区的大小。 在 U-Boot 重新定位之前，它没有适当的跟踪缓冲区。 在许多板上，您可以定义用于跟踪缓冲区的内存区域，直到重定位后“真实”跟踪缓冲区可用为止。 然后将该缓冲区的内容复制到实际缓冲区。

**CONFIG_TRACE_EARLY_ADDR**

早期跟踪缓冲区的地址。

**CONFIG_TRACE_CALL_DEPTH_LIMIT**

设置跟踪调用深度的限制。 对于广阔的视野，10 通常就足够了。 将其设置得太大会产生巨大的痕迹并严重扭曲整体时序。


## 2.5 Building U-Boot with Tracing Enabled

将“FTRACE=1”传递给 U-Boot Makefile 以实际检测代码。 这是作为一个单独的选项保留的，以便可以轻松地从命令行启用/禁用检测，而不必更改板配置文件。


## 2.6 Board requirements

跟踪数据收集依赖于微秒计时器，通过timer_get_us() 访问。 因此，您应该做的第一件事是确保这为您的董事会带来合理的结果。 该定时器的合适源包括高分辨率定时器、PWM 或配置文件定时器（如果可用）。 大多数现代 SOC 都为此配备了合适的计时器。

请参阅 add_ftrace() 了解调用 timer_get_us() 的位置。 notrace 属性必须在timer_get_us() 调用的每个函数上使用，因为递归调用add_ftrace() 会导致错误：

```bash
trace: recursion detected, disabling
```

您不能使用驱动程序模型来获取微秒计时器，因为在设置驱动程序模型之前可能会启用跟踪。 相反，提供一个访问计时器的低级函数，并在需要时进行设置。


## 2.7 Collecting Trace Data

当您在主板上运行 U-Boot 时，它将收集跟踪数据，直至达到您指定的跟踪缓冲区大小的限制。 一旦耗尽，将不再收集任何数据。

收集跟踪数据会影响执行时间和性能。 您会注意到这一点，特别是对于琐碎的函数 - 记录其执行的开销甚至可能超过其正常的执行时间。 实际上，只要您意识到效果，这并不重要。 完成优化后，请在使用 bootstage 进行端到端计时之前关闭跟踪。

开始跟踪的最佳时间是在 U-Boot 开始时。 停止追踪的最佳时间是在最后。 在实践中，这些理想很难实现。

当启用 TRACE_EARLY 时，此实现可以在 board_init_r() 或 board_init_f() 中尽早进行跟踪。 这意味着它捕获了大部分主板初始化过程，仅缺少早期特定于架构的初始化。 然而，如果有的话，它也会错过整个 SPL 阶段。 目前 SPL 不支持跟踪。

U-Boot 通常以加载并运行操作系统的“bootm”命令结束。 在该 bootm 命令的执行过程中存在有用的跟踪数据。 因此，此实现提供了一种在 bootm 完成处理之后、跳转到操作系统之前收集跟踪数据的方法。 实际上，U-Boot 此时运行“fakegocmd”环境变量。 该变量应该有一个简短的脚本，用于收集跟踪数据并将其写入某处。


## 2.8 Controlling the trace

U-Boot 为跟踪系统提供了一个命令行接口，用于控制跟踪和访问跟踪数据。查看 [trace 命令](https://docs.u-boot.org/en/latest/usage/cmd/trace.html).


## 2.9 Environment Variables

使用以下内容：

**profbase**

跟踪输出缓冲区的基地址。

**profoffset**

跟踪输出缓冲区中第一个未写入字节的偏移量。

**profsize**

跟踪输出缓冲区的大小。

所有这些都是由“tracecalls”命令设置的。

这些变量跟踪通过“trace”命令写入跟踪输出缓冲区的数据量。 将数据写入输出缓冲区的跟踪命令可以使用这些命令来指定要写入的缓冲区，并每次更新 profoffset。 这允许连续的命令将数据附加到同一缓冲区，例如：

```bash
=> trace funclist 10000 e00000
=> trace calls
```

（后一个命令将更多数据附加到缓冲区）。

**fakegocmd**

指定在启动操作系统之前运行的命令。 这是将跟踪数据写入主机进行处理的有用时间。


## 2.10 Writing Out Trace Data

一旦跟踪数据进入内存中的输出缓冲区，就有多种方法将其传输到主机。 值得注意的是，您可以使用 tftput 通过网络链接发送数据：

```bash
fakegocmd=trace pause; usb start; set autoload n; bootp;
trace calls 10000000 1000000;
tftpput ${profbase} ${profoffset} 192.168.1.4:/tftpboot/calls
```

这将启动 USB（与连接的 USB 以太网加密狗通信），将跟踪日志写入地址 10000000 并使用 TFTP 将其发送到主机。 此后，U-Boot 将正常启动操作系统，尽管会稍晚一些。

对于文件系统，您可以执行以下操作：

```bash
trace calls 10000000 1000000;
save mmc 1:1 10000000 /trace ${profoffset}
```

跟踪缓冲区格式是跟踪系统的内部格式。 它由标头、每个函数站点的调用计数组成，后面是跟踪记录列表，每个函数调用一次。


## 2.11 Converting Trace Output Data (proftool)

跟踪输出数据以二进制格式保存，此处未记录。 如果您有兴趣，请参阅trace.h 头文件。 要将其转换为有用的东西，您可以使用 proftool。

必须向该工具提供 U-Boot 映射文件以及运行该 U-Boot 时收到的跟踪数据。 它产生一个二进制输出文件。

还可以提供一个配置文件来指示在转换期间应包含或删除哪些函数。 该文件由以下行组成：

```bash
include-func <regex>
exclude-func <regex>
```

其中 <regex> 是与函数名称匹配的正则表达式。 它允许在生成 ftrace 记录时从跟踪中删除某些函数。

选项:

**-c <config_file>**

指定可选配置文件，以控制输出中包含哪些函数。

**-f <format>**

指定要使用的格式（见下文）。

**-m <map_file>**

指定 U-Boot 映射文件 (System.map)。

**-o <output file>**

指定输出文件名。

**-t <trace_file>**

指定跟踪文件，从U-Boot保存的数据。

**-v <0-4>**

指定详细程度，其中 0 是最小级别，4 用于调试。

命令:

**dump-ftrace:**

以 Linux ftrace 格式编写文件的二进制转储。 有两种选择：

- function

写入函数调用记录（调用者/被调用者）

- funcgraph

写入函数进入/退出记录（图表）

此格式可与 kernelshark 和trace_cmd 一起使用。

**dump-flamegraph**

编写对生成火焰图有用的堆栈记录列表。 有两种选择：

- calls

创建堆栈帧的火焰图。

- timing

为每个堆栈帧创建微秒的火焰图。

此格式可与 Flamegraph_pl 一起使用。


## 2.12 Viewing the Trace Data

您可以将 kernelshark 用于 GUI，但请注意 2.0.x 版本已损坏。 如果您有该版本，您可以尝试从源代码构建它。

该文件必须具有 .dat 扩展名，否则将被忽略。 该程序具有简洁的用户界面，但查看 U-Boot 配置文件信息非常方便。

还可以使用提供命令行界面的trace_cmd。


## 2.13 Workflow Suggestions

如果您尝试缩短启动时间，以下建议可能会有所帮助：

1. 启用 CONFIG_BOOTSTAGE 和 CONFIG_BOOTSTAGE_REPORT。 这将为您提供有用的启动时间的整体快照。
2. 通过跟踪构建 U-Boot 并运行它。 请注意启动时间的差异（跟踪通常会增加 10% 的时间）。
3. 如上所述收集跟踪信息。 用它来查找所有时间都花在哪里了。
4. 查看该代码，看看是否可以对其进行优化。 也许可以加快设备的初始化速度，或删除未使用的功能。
5. 再次重建、运行并收集。 比较你的结果。
6. 继续前进，直到你耗尽动力，或者你的启动速度足够快。


## 2.14 Configuring Trace

您可能需要考虑代码中的一些参数。 有一个函数调用深度限制（默认设置为 15）。 当堆栈深度超过此值时，将不会记录任何跟踪信息。 达到的最大深度由“trace stats”命令记录和显示。 虽然将深度限制设置得相当高可能很诱人，但这会显着增加跟踪输出的大小以及执行时间。


## 2.15 Future Work

跟踪在某些方面可能会更整洁一些，例如为跟踪提供运行时配置选项。

其他一些可能有用的功能：

- 跟踪过滤器以选择记录哪些函数；
- 使用定时器中断进行基于样本的分析；
- 更好地控制走线深度；
- 跟踪信息的压缩。
