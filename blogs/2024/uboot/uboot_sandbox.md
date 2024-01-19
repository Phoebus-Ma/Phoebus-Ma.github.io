
# 1. 开发环境

Ubuntu 22.04 for x86_64.

```bash
$ sudo apt update
$ sudo apt install build-essential flex bison gawk texinfo libsdl2-dev python3-setuptools python3-dev swig libgnutls28-dev
```

下载 u-boot 源码:

```bash
$ wget https://ftp.denx.de/pub/u-boot/u-boot-2023.10.tar.bz2
```


# 2. U-Boot 的本机执行

“沙盒”架构旨在允许 U-Boot 在几乎任何硬件上的 Linux 下运行。 为了实现这一点，它（尽可能）将 U-Boot 构建为带有 main() 和普通 C 库的普通 C 应用程序。

因此，所有 U-Boot 的特定于体系结构的代码都不能构建为沙箱 U-Boot 的一部分。 在Linux下运行U-Boot的目的是测试所有通用代码，而不是特定于任何一种架构。 这个想法是创建单元测试，我们可以运行它来测试这个上层代码。

沙盒允许以传统方式开发多种类型的新功能，而不需要在真实硬件上测试每次迭代。 许多 U-Boot 功能都是在沙箱上开发的，包括核心驱动程序模型、大多数 uclass、验证启动、bloblist、日志记录等。 沙盒还实现了许多大规模的代码重构。

CONFIG_SANDBOX 在构建本机板时定义。

板名称为“sandbox”，但供应商名称未设置，因此板/沙箱中有一个板。

在大端机器上运行时应定义 CONFIG_SANDBOX_BIG_ENDIAN。

沙箱有两种版本：一种使用 32 位宽整数，一种使用 64 位宽整数。 通过选择或取消选择 CONFIG_SANDBOX_32BIT，可以在 32 位或 64 位主机上构建和运行 32 位版本； 默认情况下，它为 32 位主机构建的沙箱。 使用 64 位宽整数的沙箱只能在 64 位主机上构建。

请注意，目前不提供独立/API 支持。


# 3. 基础操作

查看 sandbox 配置文件:

```bash
$ tar -xf u-boot-2023.10.tar.bz2
$ cd u-boot-2023.10/
$ ls configs/ | grep sandbox
sandbox64_defconfig
sandbox_defconfig
sandbox_flattree_defconfig
sandbox_noinst_defconfig
sandbox_spl_defconfig
sandbox_vpl_defconfig
```

要运行沙箱 U-Boot，请使用以下命令：

```bash
$ make sandbox_defconfig all -j4
$ ./u-boot
```

注意：如果您收到有关“sdl-config：未找到命令”的错误，您可能需要安装 libsdl2.0-dev 或类似版本才能获得 SDL 支持。 或者，您可以通过禁用 .config 文件中的 CONFIG_SANDBOX_SDL 来构建没有 SDL 的沙箱（即没有显示/键盘支持）。

U-Boot 将在您的计算机上启动，显示串行控制台的沙箱模拟：

```bash
U-Boot 2014.04 (Mar 20 2014 - 19:06:00)

DRAM:  128 MiB
Using default environment

In:    serial
Out:   lcd
Err:   lcd
=>
```

您可以像平常一样发出命令。 如果不支持您想要的命令，您可以将其添加到 include/configs/sandbox.h。

要退出，请输入“poweroff”或按 Ctrl-C。


# 4. Console / LCD 支持

假设构建时启用了 CONFIG_SANDBOX_SDL，您可以使用 LCD 和键盘模拟来运行沙箱，使用如下命令：

```bash
./u-boot -d u-boot.dtb -l
```

这将启动 U-Boot，并显示一个显示 LCD 内容的窗口。 如果该窗口具有焦点，那么您将能够像在控制台上一样键入命令。 您可以在设备树文件中调整显示设置 - 请参阅 arch/sandbox/dts/sandbox.dts。


# 5. 命令行选项

有多种选项可供选择，主要用于测试目的。 使用 -h 查看可用选项。 其中一些描述如下：

**-t, --terminal <arg>**

终端通常处于所谓的“raw-with-sigs”模式。 这意味着您可以使用箭头键进行命令编辑和历史记录，但如果您按 Ctrl-C，U-Boot 将退出，而不是将其视为按键。 其他选项是“raw”（因此 Ctrl-C 在 U-Boot 中处理）和“cooked”（终端处于“cooked”模式并且光标键不起作用，Ctrl-C 将退出）。

**-l**

显示 LCD 仿真窗口。

**-d <device_tree>**

可以使用 -d 提供设备树二进制文件。 如果您编辑源代码（它存储在 arch/sandbox/dts/sandbox.dts 中），则必须重建 U-Boot 以重新创建二进制文件。

**-D**

要使用默认设备树，请使用 -D。

**-T**

要使用测试设备树，请使用 -T。

**-c [<cmd>;]<cmd>**

要直接执行命令，请使用 -c 选项。 您可以指定单个命令，也可以指定用分号分隔的多个命令，这在 U-Boot 中很常见。 请小心引用，因为 shell 通常会处理并吞掉引号。 使用 -c 时，U-Boot 在命令完成后退出，但您可以使用 -i 强制它进入交互模式。

**-i**

执行-c指定的命令后进入交互模式。


# 6. 环境变量

**UBOOT_SB_TIME_OFFSET**

该环境变量存储模拟实时时钟与主机实时时钟的偏移量（以秒为单位）。 偏移量默认为零。


# 7. 内存模拟

支持内存模拟，大小由CONFIG_SANDBOX_RAM_SIZE_MB设置。 -m 选项可用于在启动时从文件读取内存并在关闭时写入。 这允许在测试运行期间保留内存内容。 您可以使用 –rm_memory 选项告诉 U-Boot 在读取（启动时）内存文件后删除该文件。

要在代码中访问 U-Boot 的模拟内存，请使用 map_sysmem()。 该函数在整个 U-Boot 中使用，以确保使用模拟内存而不是 U-Boot 应用程序内存。 这提供了从 0 开始并扩展到仿真大小的内存。


# 8. 存储状态

使用沙箱，您可以编写模拟真实设备上驱动程序操作的驱动程序。 其中一些驱动程序可能想要记录在 U-Boot 运行期间保留的状态。 这对于测试特别有用。 例如，SPI闪存芯片的内容不应该仅仅因为U-Boot退出而消失。

状态以特定于驱动程序的简单格式存储在设备树文件中。 然后使用 -s 选项指定状态文件。 使用 -r 使 U-Boot 在启动时读取状态（否则它开始为空），使用 -w 在退出时写入状态（否则存储的状态保持不变，并且 U-Boot 所做的任何更改都将丢失）。 您还可以使用 -n 告诉 U-Boot 忽略任何丢失状态的问题。 这在第一次运行时很有用，因为状态文件将为空。

设备树文件为每个驱动程序都有一个节点 - 驱动程序可以在其中存储它喜欢的任何属性。 有关如何让驱动程序读取和写入其状态的更多详细信息，请参阅下面的“编写沙箱驱动程序”。


# 9. 运行和引导

由于没有机器架构，沙箱 U-Boot 无法实际启动内核，但它支持 bootm 命令。 支持文件系统、内存命令、散列、FIT 映像、验证启动和许多其他功能。

当“bootm”运行内核时，沙箱将退出，就像 U-Boot 在真机上所做的那样。 当然，在这种情况下，不会运行任何内核。

还可以使用 -j 选项告诉 U-Boot 它已从临时的先前 U-Boot 二进制文件跳转。 该二进制文件会被获取 -j 选项的 U-Boot 自动删除。 这允许您编写模拟链式加载 U-Boot 操作的测试，通常用于板上存储了第二个“可更新”U-Boot 的情况。 覆盖或升级主板上唯一的 U-Boot 是非常危险的，因为电源或其他故障会导致主板变砖，如果是消费类设备，则需要返回制造商。


# 10. 支持的驱动程序

U-Boot 沙箱支持这些模拟：

- Arm FF-A
- 块设备
- Chrome OS EC
- GPIO
- 主机文件系统（从 U-Boot 内访问主机上的文件）
- I2C
- 键盘 (Chrome OS)
- LCD
- 网络
- Serial (for console only)
- 声音（不完整 - 有关详细信息，请参阅 sandbox_sdl_sound_init()）
- SPI
- SPI flash
- TPM (可信平台模块)

执行了多种命令。 支持使用块设备的文件系统。

沙箱还支持驱动程序模型（CONFIG_DM）和相关命令。


# 11. 沙盒变体

不幸的是，目前有很多变体：

**sandbox:**

应该用于大多数测试

**sandbox64:**

强制使用 64 位主机的特殊构建

**sandbox_flattree:**

使用定义为内联的 dev_read_…() 函数进行构建。 我们需要这个构建，以便我们可以测试这些内联函数，并且我们不能同时使用内联函数和非内联函数进行构建，因为它们的名称相同。

**sandbox_spl:**

构建支持 SPL 的沙箱，因此您可以运行 spl/u-boot-spl，它将启动并加载 ./u-boot。 也可以直接运行./u-boot。

其中 sandbox_spl 可能可以删除，因为它是沙箱的超集。

这些变体之间的大多数配置选项应该是相同的。


# 12. Linux RAW 网络桥

sandbox_eth_raw 驱动程序在网络堆栈底部和 Linux 中的 RAW 套接字 API 之间桥接流量。 这允许在沙箱中针对真实网络流量测试大部分 U-Boot 网络功能。

对于以太网网络适配器，桥接器使用 RAW AF_PACKET API。 这是访问 Linux 中网络堆栈最低层所必需的。 这意味着所有以太网帧都被包括在内。 这使得U-Boot网络堆栈能够得到充分利用。 换句话说，Linux 网络堆栈的任何内容都不参与形成最终在线上的数据包。 要接收对从 U-Boot 发送的数据包的响应，网络接口必须设置为混杂模式，以便网卡不会过滤掉不是发往其配置的（在 Linux 上）MAC 地址的数据包。

RAW 套接字以太网 API 需要 Linux 中提升的权限。 您可以以 root 身份运行，也可以添加所需的功能，如下所示：

```bash
sudo /sbin/setcap "CAP_NET_RAW+ep" /path/to/u-boot
```

沙箱的默认设备树包括沙箱主机上的 eth0 条目，其别名为“eth1”。 以下是在 eth0 接口上测试网络操作的几个示例。

```bash
sudo /path/to/u-boot -D

DHCP
....

setenv autoload no
setenv ethrotate no
setenv ethact eth1
dhcp

PING
....

setenv autoload no
setenv ethrotate no
setenv ethact eth1
dhcp
ping $gatewayip

TFTP
....

setenv autoload no
setenv ethrotate no
setenv ethact eth1
dhcp
setenv serverip WWW.XXX.YYY.ZZZ
tftpboot u-boot.bin
```

该桥还支持（在较小程度上）本地主机接口“lo”。

“lo”接口无法使用 RAW AF_PACKET API，因为 lo 接口不支持以太网级流量。 它是一个更高级别的接口，预计仅在 API 的 AF_INET 级别使用。 因此，我们可以在该接口上获得的最原始的是 UDP 上的 RAW AF_INET API。 这允许我们设置 IP_HDRINCL 选项以在我们发送和接收的数据包中包含除以太网标头之外的所有内容。

由于仅支持 UDP，因此 ICMP 流量将不起作用，因此预计 ping 命令会超时。

沙箱的默认设备树包括别名为“eth5”的沙箱主机上的 lo 条目。 以下是在 lo 接口上测试网络操作的示例。

```bash
TFTP
....

setenv ethrotate no
setenv ethact eth5
tftpboot u-boot.bin
```


# 13. SPI 仿真

沙盒支持 SPI 和 SPI 闪存仿真。

可以通过设备树启用设备，例如：

```bash
spi@0 {
        #address-cells = <1>;
        #size-cells = <0>;
        reg = <0 1>;
        compatible = "sandbox,spi";
        cs-gpios = <0>, <&gpio_a 0>;
        spi.bin@0 {
                reg = <0>;
                compatible = "spansion,m25p16", "jedec,spi-nor";
                spi-max-frequency = <40000000>;
                sandbox,filename = "spi.bin";
        };
};
```

该文件必须提前创建：

```bash
$ dd if=/dev/zero of=spi.bin bs=1M count=2
$ u-boot -T
```

在这里，您可以使用“-T”或“-D”选项分别指定test.dtb或u-boot.dtb，或者使用“-d <file>”指定您自己的dtb。

通过此设置，您可以正常发出 SPI 闪存命令：

```bash
=>sf probe
SF: Detected M25P16 with page size 64 KiB, total 2 MiB
=>sf read 0 0 10000
SF: 65536 bytes @ 0x0 Read: OK
```

由于这是一个完整的 SPI 仿真（而不仅仅是闪存），因此您还可以使用低级 SPI 命令：

```bash
=>sspi 0:0 32 9f
FF202015
```

这是发出 READ_ID 命令并返回 20 (ST Micro) 部分 0x2015 (M25P16)。


# 14. 块设备仿真

U-Boot 可以使用原始磁盘映像进行块设备模拟。 例如 列出镜像“disk.raw”第二部分根目录的内容，可以使用以下命令：

```bash
=>host bind 0 ./disk.raw
=>ls host 0:2
```

可以使用“host bind -r”将设备标记为可移除。

可以使用以下命令创建磁盘映像：

```bash
$> truncate -s 1200M ./disk.raw
$> /usr/sbin/sgdisk --new=1:0:+64M --typecode=1:EF00 --new=2:0:0 --typecode=2:8300 disk.raw
$> lodev=`sudo losetup -P -f --show ./disk.raw`
$> sudo mkfs.vfat -n EFI -v ${lodev}p1
$> sudo mkfs.ext4 -L ROOT -v ${lodev}p2
```

或利用 test/py/make_test_disk.py 中描述的设备：

```bash
#!/usr/bin/python
import make_test_disk
make_test_disk.makeDisk()
```

有关更多技术细节，请参阅[沙箱块设备（实现）](https://docs.u-boot.org/en/latest/arch/sandbox/block_impl.html)。


# 15. 编写沙箱驱动程序

通常，您应该将驱动程序放在包含单词“sandbox”的文件中，并将其与其他同类驱动程序放在同一目录中。 然后，您可以实现与其他驱动程序相同的挂钩。

要访问 U-Boot 的模拟内存，请使用上面提到的 map_sysmem()。

如果您的驱动程序需要存储配置或状态（例如 SPI 闪存内容或模拟芯片寄存器），您可以使用如上所述的设备树。 使用 SANDBOX_STATE_IO 宏为此定义处理程序。 请参阅 arch/sandbox/include/asm/state.h 以获取文档。 简而言之，您提供节点名称、兼容的字符串和用于读取和写入状态的函数。 由于写入状态可以扩展设备树，因此您可能需要使用 state_setprop() 它会自动执行此操作并避免空间不足。 请参阅现有代码以获取示例。


# 16. VPL (验证程序加载器)

Sandbox 提供了一个名为 sandbox_vpl 的 vpl 示例构建。 构建它：

```bash
make sandbox_vpl_defconfig all
```

可以使用以下命令运行：

```bash
./tpl/u-boot-tpl -d u-boot.dtb
```

它启动 TPL（第一阶段 init），然后是 VPL，然后运行 SPL，最后运行正确的 U-Boot，遵循验证启动的正常流程。 目前尚未实际实施验证。

这是一个跟踪示例：

```bash
U-Boot TPL 2024.01-rc2-00129 (Nov 19 2023 - 08:10:12 -0700)
Trying to boot from sandbox_image
Trying to boot from sandbox_file

U-Boot VPL 2024.01-rc2-00129 (Nov 19 2023 - 08:10:12 -0700)
Trying to boot from vbe_simple
Trying to boot from sandbox_image
Trying to boot from sandbox_file

U-Boot SPL 2024.01-rc2-00129 (Nov 19 2023 - 08:10:12 -0700)
Trying to boot from vbe_simple
Trying to boot from sandbox_image
Trying to boot from sandbox_file


U-Boot 2024.01-rc2-00129 (Nov 19 2023 - 08:10:12 -0700)

Reset Status: COLD
Model: sandbox
DRAM:  256 MiB
using memory 0x1b576000-0x1f578000 for malloc()

Warning: host_lo MAC addresses don't match:
Address in ROM is            96:cd:ef:82:78:51
Address in environment is    02:00:11:22:33:44
Core:  103 devices, 51 uclasses, devicetree: board
MMC:
Loading Environment from nowhere... OK
In:    serial,cros-ec-keyb,usbkbd
Out:   serial,vidconsole
Err:   serial,vidconsole
Model: sandbox
Net:   eth0: host_lo, eth1: host_enp14s0, eth2: host_eth6, eth3: host_wlp15s0, eth4: host_virbr0, eth5: host_docker0, eth6: eth@10002000
Hit any key to stop autoboot:  1
```


# 17. 调试初始化序列

如果 initcall 序列失败，如下所示：

```bash
initcall sequence 0000560775957c80 failed at call 0000000000048134 (err=-96)
```

然后您可以使用 grep 来查看哪个 init 调用失败，例如：

```bash
$ grep 0000000000048134 u-boot.map
stdio_add_devices
```

当然，另一种选择是使用 gdb 等调试器运行它：

```bash
$ gdb u-boot
...
(gdb) br initcall.h:41
Breakpoint 1 at 0x4db9d: initcall.h:41. (2 locations)
```

请注意，报告了两个位置，因为该函数在 board_init_f() 和 board_init_r() 中都使用。

```bash
(gdb) r
Starting program: /tmp/b/sandbox/u-boot
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

U-Boot 2018.09-00264-ge0c2ba9814-dirty (Sep 22 2018 - 12:21:46 -0600)

DRAM:  128 MiB
MMC:

Breakpoint 1, initcall_run_list (init_sequence=0x5555559619e0 <init_sequence_f>)
    at /scratch/sglass/cosarm/src/third_party/u-boot/files/include/initcall.h:41
41                              printf("initcall sequence %p failed at call %p (err=%d)\n",
(gdb) print *init_fnc_ptr
$1 = (const init_fnc_t) 0x55555559c114 <stdio_add_devices>
(gdb)
```

这种方法可以在普通板和沙箱上使用。

对于使用 GDB 或 LLDB 进行调试，最好降低编译器优化级别 (CONFIG_CC_OPTIMIZE_FOR_DEBUG=y) 并禁用链接时间优化 (CONFIG_LTO=n)。


# 18. SDL_CONFIG

如果 sdl-config 与默认路径不同，请在构建 U-Boot 之前将 SDL_CONFIG 环境变量设置为正确的路径名。


# 19. 使用 valgrind / memcheck

可以在 valgrind 下运行 U-Boot 来检查内存分配：

```bash
valgrind ./u-boot
```

然而，这并没有给出非常有用的结果。 沙箱通过mmap()分配内存池。 U-Boot 的内部 malloc() 和 free() 在此内存池上工作。 默认情况下，自定义分配器和解除分配器对 valgrind 是不可见的。 要将 U-Boot 的 malloc() 和 free() 公开给 valgrind，请启用 CONFIG_VALGRIND。 启用此选项将注入 valgrind 解释的占位符汇编代码。 这用于将内存部分注释为安全或不安全，并通知 valgrind 有关 malloc() 和 free() 的信息。 目前 RISC-V 没有标准的占位符组装序列，因此无法在该架构上启用此选项。

Malloc的记账信息默认被标记为不安全。 然而，当 malloc 本身访问此信息时，这会产生许多误报。 可以通过以下方式抑制这些警告：

```bash
valgrind --suppressions=scripts/u-boot.supp ./u-boot
```

此外，如果 U-Boot 使用比主机架构更小的指针大小，您可能会遇到误报。 这是因为 U-Boot 使用的指针仅包含 32 位寻址信息。 当解释为 64 位指针时，valgrind 会认为它们没有正确初始化。 要解决此问题，请在 64 位主机上运行时启用 CONFIG_SANDBOX64（例如通过 sandbox64_defconfig）。


## 19.1 其他选项

除了上面的示例之外，以下 valgrind 选项也很有用：

**--trace-childen=yes**

告诉 valgrind 继续跟踪子进程，例如当 U-Boot 从 TPL 跳转到 SPL，或从 SPL 跳转到 U-Boot 时。

**--track-origins=yes**

将（以少量开销）告诉 valgrind 跟踪谁分配了一些麻烦的内存。

**--error-limit**

将在单个会话中打印超过 1000 个错误。

**--vgdb=yes --vgdb-error=0**

会让你使用 GDB 来附加，例如：

```bash
gdb -ex "target remote | vgdb" u-boot
```

这对于出现错误时检查程序状态非常有帮助。

以下 U-Boot 选项也很有帮助：

**-Tc 'ut all'**

让 U-Boot 自动运行单元测试。 请注意，并非所有单元测试都会在默认配置中成功。

**-t cooked**

如果您提前终止控制台（而不是必须运行 tset），将使控制台保持在正常状态。


## 19.2 未来的工作

当前方法的最大限制是抑制不会“取消污染”未初始化的内存访问。 目前，dlmalloc 的记账信息被标记为“红色区域”。 这意味着对该区域的所有读取都被 valgrind 标记为非法。 这对于常规代码来说很好，但是 dlmalloc 确实需要访问该区域，因此我们抑制了它的违规。 但是，如果 dlmalloc 随后传递根据“受污染”访问计算出的结果，则该结果仍然受污染。 所以第一个访问者会发出警告。 这意味着每个构造都像：

```bash
foo = malloc(sizeof(*foo));
if (!foo)
    return -ENOMEM;
```

当我们检查 malloc 的结果时会发出警告。 Whoops。

至少有四种可能的方法来解决这个问题：

- 不要将 dlmalloc 簿记信息标记为红色区域。 这是最简单的解决方案，但极大地降低了 valgrind 的功能，因为我们无法再确定（例如）超出数组末尾的访问是未定义的。

- 正确实施红色区域。 这将涉及将每个分配增加固定数量（16 字节左右），然后将额外的空间用于常规代码和 dlmalloc 都不需要访问的真正的红色区域。 不幸的是，这可能需要对 dlmalloc 进行一些相当密集的操作，以适当地添加/删除偏移量。

- 在 dlmalloc 中使用记账信息之前将其标记为有效，然后在返回之前将其标记为无效。 这将是最正确的，但实现起来会非常棘手，因为需要标记的代码路径太多。 我认为这将是这里三个选项中最努力的一个。

- 使用主机 malloc 和 free 而不是 U-Boot 的自定义分配器。 这将消除注释 dlmalloc 的需要。 但是，对沙箱使用不同的分配器意味着 dlmalloc 中的错误仅在读取（或模拟）硬件上运行时才会被测试。

在实施上述选项之一之前，筛选大量虚假警告仍然很困难。


# 20. 测试

U-Boot 沙箱可用于运行各种测试，主要在 test/ 目录中。

查看 [Sandbox tests](https://docs.u-boot.org/en/latest/develop/tests_sandbox.html) 了解更多信息和查看 [Introduction to testing](https://docs.u-boot.org/en/latest/develop/testing.html) 有关一般测试的信息。


# 21. 内存映射

沙箱有自己的模拟内存，从 0 开始。以下是映射到该内存的一些内容：

| Addr   | Config                   | Usage                                 |
| ------ | ------------------------ | ------------------------------------- |
| 100    | CONFIG_SYS_FDT_LOAD_ADDR | Device tree                           |
| b000   | CONFIG_BLOBLIST_ADDR     | Blob list                             |
| 10000  | CFG_MALLOC_F_ADDR        | 早期内存分配                           |
| f0000  | CONFIG_PRE_CON_BUF_ADDR  | Pre-console buffer                    |
| 100000 | CONFIG_TRACE_EARLY_ADDR  | 早期跟踪缓冲区（如果启用）。 也用作 spl_test_load() 中的 SPL 加载缓冲区 |
| 200000 | CONFIG_TEXT_BASE         | U-Boot 的加载缓冲区（仅限 sandbox_spl） |
