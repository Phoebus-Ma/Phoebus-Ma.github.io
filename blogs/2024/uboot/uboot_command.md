
# 1. 添加命令

代码位置：u-boot/cmd (以u-boot-2023.10为例)。


## 1.1 现有命令修改

修改 u-boot/cmd/time.c

```diff
--- a/u-boot/cmd/time.c
+++ b/u-boot/cmd/time.c
@@ -22,6 +22,19 @@ static void report_time(ulong cycles)
        if (minutes)
                printf(" %lu minutes,", minutes);
        printf(" %lu.%03lu seconds\n", seconds, milliseconds);
+
+       printf("test 1.\n");
+       log_emer("test 2.\n");
+       log_alert("test 3.\n");
+       log_crit("test 4.\n");
+       log_err("test 5.\n");
+       log_warning("test 6.\n");
+       log_notice("test 7.\n");
+       log_info("test 8.\n");
+       log_debug("test 9.\n");
+       log_content("test 10.\n");
+       log_io("test 11.\n");
+       log_cont("test 12.\n");
+       debug("test 13.\n");
 }

 static int do_time(struct cmd_tbl *cmdtp, int flag, int argc,
```

修改完成后，u-boot根目录执行编译：

```bash
$ make -j4
```

测试如下：

```bash
$ ./u-boot
U-Boot 2023.10 (Jan 04 2024 - 11:10:55 +0800)

DRAM:  256 MiB
Core:  27 devices, 14 uclasses, devicetree: board
MMC:   
Loading Environment from nowhere... OK
Warning: device tree node '/config/environment' not found
In:    serial,cros-ec-keyb,usbkbd
Out:   serial,vidconsole
Err:   serial,vidconsole
Net:   No ethernet found.
Hit any key to stop autoboot:  0 
=> 
=> 
=> 
=> 
=> time true

time: 0.000 seconds
test 1.
test 2.
test 3.
test 4.
test 5.
test 6.
test 7.
test 8.
=>
```

从上面的测试结果看出，log_debug及以下等级，不会打印出来，原因是loglevel等级的限制：

```bash
CONFIG_LOG_DEFAULT_LEVEL=6
```

查看loglevel定义的话是在：

```bash
log_level_t @ u-boot-2023.10/include/log.h
```

另外，debug()中的内容，也没有打印，原因是没有开启debug，开启debug的方法是：

```bash
#ifdef DEBUG @ u-boot-2023.10/include/log.h
```

这个宏定义之前定义：

```c
#define DEBUG
```


## 1.2 添加新命令

首先是在 u-boot/cmd 目录下新建文件：

```bash
$ cd u-boot/cmd/
$ touch hello-test.c
```

在 u-boot/cmd/Makefile 中添加如下内容：

```bash
obj-$(CONFIG_CMD_HELLO1) += hello-test.o
```

或者：

```bash
obj-y += hello-test.o
```

使用后者其它步骤就可以忽略，直接跳转到代码编写环节，这里使用前者，所以还需要做一些额外的工作：

在 u-boot/cmd/Kconfig 中添加如下内容：

```bash
config CMD_HELLO1
	bool "hello1"
	help
	  Run commands and display hello world to stdout.
```

这里采用 sandbox 仿真 u-boot，所以在 u-boot/configs/sandbox_defconfig 添加如下内容：

```bash
CONFIG_CMD_HELLO1=y
```

然后，进入编码环节：

在 include/command.h 中，包含了命令声明宏：

```c
#define U_BOOT_CMD(_name, _maxargs, _rep, _cmd, _usage, _help)		\
	U_BOOT_CMD_COMPLETE(_name, _maxargs, _rep, _cmd, _usage, _help, NULL)

#define U_BOOT_CMD_WITH_SUBCMDS(_name, _usage, _help, ...)		\
	U_BOOT_SUBCMDS(_name, __VA_ARGS__)				\
	U_BOOT_CMDREP_COMPLETE(_name, CONFIG_SYS_MAXARGS, do_##_name,	\
			       _usage, _help, complete_##_name)
```

U_BOOT_CMD 和 U_BOOT_CMD_WITH_SUBCMDS 的区别就是，一次声明命令的数量，前者一次申请一个命令，后者一次同时申请多个命令(比如acpi有acpi list、acpi items等多个命令)。

这里使用 U_BOOT_CMD，它的参数含义如下：

```bash
_name：     添加的命令的名字
_maxargs：  添加的命令最多有几个参数(注意，假如你设置的参数个数是3，而实际的参数个数是4，那么执行命令会输出帮助信息的)
_rep：      是否重复(1重复，0不重复)（即按下Enter键的时候，自动执行上次的命令）
_cmd：      执行函数，即运行了命令具体做啥会在这个函数中体现出来
_usage：    帮助信息（short）
_help：     帮助信息（long）
```

编辑 hello-test.c：

```c
/**
 * hello-test.c
 * 	command test example.
 *
 * License - MIT.
 */

#include <common.h>
#include <command.h>

static int do_hello(struct cmd_tbl *cmdtp, int flag, int argc,
                   char *const argv[])
{
    printf("hello world.\n");

    return 0;
}

U_BOOT_CMD(hello1, CONFIG_SYS_MAXARGS, 0, do_hello,
           "run commands and display hello world to stdout",
           "command\n");

```

编译：

```bash
$ make sandbox_defconfig all -j4
```

运行：

```bash
$ ./u-boot
U-Boot 2023.10 (Jan 04 2024 - 12:13:55 +0800)

DRAM:  256 MiB
Core:  27 devices, 14 uclasses, devicetree: board
MMC:   
Loading Environment from nowhere... OK
Warning: device tree node '/config/environment' not found
In:    serial,cros-ec-keyb,usbkbd
Out:   serial,vidconsole
Err:   serial,vidconsole
Net:   No ethernet found.
Hit any key to stop autoboot:  0 
=> 
=> 
=> 
=> 
=> hello1
hello world.
=>
```


# 2. U-boot原生命令

```bash
2048      - 2048游戏
?         - 'help'的别名
ab_select - 选择用于启动的插槽并注册启动尝试。
abootimg  - 操纵 Android 启动映像
acpi      - ACPI表
addrmap   - 列出 32 位 CPU 的非标识虚拟物理内存映射
aes       - AES 128/192/256 CBC 加密
armffa    - Arm FF-A 测试命令
askenv    - 从 stdin 获取环境变量
avb       - 提供用于测试 Android Verified Boot 2.0 功能的命令
axi       - AXI 子系统
base      - 打印或设置地址偏移量
bdinfo    - 打印板信息结构
bind      - 将设备绑定到驱动程序
blkcache  - 块缓存诊断和控制
blkmap    - 可组合的虚拟块设备
bloblist  - 块列表
bmp       - 操作 BMP 图像数据
boot      - 启动默认值，即运行“bootcmd”
bootcount - bootcount
bootd     - 启动默认值，即运行“bootcmd”
bootdev   - 启动设备
bootefi   - 从内存启动 EFI 负载
bootflow  - 启动流程
bootm     - 从内存启动应用程序映像
bootmenu  - ANSI 终端启动菜单
bootmeth  - 启动方式
bootp     - 使用 BOOTP/TFTP 协议通过网络启动映像
bootstage - 启动阶段命令
bootz     - 从内存启动 Linux zImage 映像
btrsubvol - 列出 BTRFS 文件系统的子卷
button    - 管理按钮
cat       - 将文件打印到标准输出
cbfsinfo  - 打印有关文件系统的信息
cbfsinit  - 初始化cbfs驱动
cbfsload  - 从 cbfs 文件系统加载二进制文件
cbfsls    - 列出文件
cdp       - 执行 CDP 网络配置
cedit     - 配置编辑器
chpart    - 更改 MTD 设备的活动分区
clone     - 简单存储克隆
cls       - 清除屏幕
cmp       - 内存比较
config    - 打印 .config
coninfo   - 打印控制台设备和信息
cp        - 内存复制
cpu       - 显示有关 CPU 的信息
cramfsload- 从文件系统映像加载二进制文件
cramfsls  - 列出目录中的文件 (default /)
crc32     - 校验和计算
crosec    - CROS-EC实用程序命令
cyclic    - 循环
date      - 获取/设置/重置日期和时间
demo      - 驱动模型（dm）演示操作
dhcp      - 使用 DHCP/TFTP 协议通过网络启动映像
dhcp6     - 使用 DHCPv6/TFTP 协议通过网络启动映像。 使用带有 [] 括号的 IPv6 hostIPaddr
dhry      - [迭代] - 运行 dhrystone 基准测试
diskboot  - 从 IDE 设备启动
dm        - 驱动程序模型低级访问
dns       - 查找主机名的IP
echo      - 将参数回显到控制台
editenv   - 编辑环境变量
eficonfig - 提供菜单驱动的UEFI变量维护界面
efidebug  - 配置UEFI环境
env       - 环境处理命令
erase     - 擦除闪存
eraseenv  - 从持久存储中删除环境变量
erofsload - 从 EROFS 文件系统加载二进制文件
erofsls   - 列出目录中的文件。 默认值：根 (/)。
ethsw     - 以太网二层交换机命令
event     - 事件
exception - 强制发生异常
exit      - 退出脚本
ext2load  - 从 Ext2 文件系统加载二进制文件
ext2ls    - 列出目录中的文件（默认 /）
ext4load  - 从 Ext4 文件系统加载二进制文件
ext4ls    - 列出目录中的文件（默认 /）
ext4size  - 确定文件的大小
ext4write - 在根目录下创建一个文件
extension - 扩展板管理子系统
false     - 什么也不做，不成功
fastboot  - 作为 fastboot USB 或 udp 设备运行
fatinfo   - 打印有关文件系统的信息
fatload   - 从 dos 文件系统加载二进制文件
fatls     - 列出目录中的文件（默认 /）
fatmkdir  - 创建一个目录
fatrm     - 删除一个文件
fatsize   - 确定文件的大小
fatwrite  - 将文件写入dos文件系统
fdt       - 扁平化设备树实用程序命令
flinfo    - 打印FLASH存储器信息
font      - 字体
fstype    - 查找文件系统类型
fstypes   - 列出支持的文件系统类型
gettime   - 获取计时器 val 已过时间
go        - 在地址“addr”处启动应用程序
gpio      - 查询和控制GPIO引脚
gpt       - GUID分区表
grepenv   - 搜索环境变量
gzwrite   - 解压缩并将内存写入块设备
hash      - 计算哈希消息摘要
help      - 打印命令说明/用法
host      - 各种主机命令
i2c       - I2C 子系统
ide       - IDE 子系统
iminfo    - 打印应用程序图像的标题信息
imxtract  - 提取多图像的一部分
iod       - IO空间显示
iotrace   - iotrace 实用程序命令
iow       - IO空间修改
itest     - 整数比较时返回 true/false
lcdputs   - 在视频帧缓冲区上打印字符串
led       - 管理 LED
license   - 打印 GPL 许可证文本
linklocal - 使用链路本地协议获取网络IP地址
ln        - 创建符号链接
load      - 从文件系统加载二进制文件
loadb     - 通过串行线加载二进制文件（kermit 模式）
loadm     - 将二进制 blob 从源地址加载到目标地址
loads     - 通过串行线加载 S-Record 文件
loadx     - 通过串行线路加载二进制文件（xmodem 模式）
loady     - 通过串行线路加载二进制文件（ymodem 模式）
loop      - 地址范围上的无限循环
loopw     - 地址范围上的无限写循环
ls        - 列出目录中的文件（默认 /）
lsblk     - 列出块驱动程序和设备
lzmadec   - lzma 解压缩内存区域
mbr       - MBR (Master Boot Record，主引导记录)
md        - 内存显示
md5sum    - 计算MD5消息摘要
mdc       - 内存循环显示
mdio      - MDIO 实用程序命令
meminfo   - 显示内存信息
mii       - MII 实用程序命令
mm        - 内存修改（自动递增地址）
mmc       - MMC 子系统
mmcinfo   - 显示MMC信息
ms        - 记忆搜索
mtdparts  - 定义flash/nand分区
mtest     - 简单的RAM读/写测试
mux       - 列出、选择和取消选择多路复用器
mw        - 内存写入（填充）
mwc       - 内存写循环
net       - NET 子系统
nm        - 内存修改（常量地址）
nvme      - NVM Express 子系统
osd       - OSD 子系统
panic     - 带有可选消息的panic
part      - 磁盘分区相关命令
pause     - 延迟直到用户输入
pcap      - pcap
pci       - 列出并访问 PCI 配置空间
pci_mps   - 配置 PCI Express MPS
ping      - 发送 ICMP ECHO_REQUEST 到网络主机
ping6     - 发送 ICMPv6 ECHO_REQUEST 到网络主机
pinmux    - 显示引脚控制器复用
pmc       - 电源管理控制器信息
pmic      - PMIC子系统
poweroff  - 执行设备断电
printenv  - 打印环境变量
protect   - 启用或禁用 FLASH 写保护
pstore    - 管理 Linux 持久存储
pwm       - 控制 PWM 通道
pxe       - 从 pxe 文件获取和引导的命令要使用 IPv6 添加 -ipv6 参数
qfw       - QEMU 固件接口
random    - 用随机模式填充内存
rarpboot  - 使用 RARP/TFTP 协议通过网络启动映像
read      - 从分区加载二进制数据
regulator - uclass操作
reset     - 执行 CPU 的 RESET
rng       - 从硬件随机数生成器打印字节
rproc     - 控制 SoC 中远程处理器的操作
rtc       - RTC 子系统
run       - 在环境变量中运行命令
save      - 将文件保存到文件系统
saveenv   - 将环境变量保存到持久存储
sb        - 沙箱状态命令
scp03     - 安全通道协议03控制
scsi      - SCSI子系统
scsiboot  - 从 SCSI 设备启动
setcurs   - 设置屏幕内的光标位置
setenv    - 设置环境变量
setexpr   - 将环境变量设置为 eval 表达式的结果
sf        - SPI闪存子系统
showvar   - 打印本地 hushshell 变量
size      - 确定文件的大小
sleep     - 延迟执行一段时间
sntp      - 通过网络同步RTC
sound     - 声音子系统
source    - 从内存中运行脚本
sqfsload  - 从 SquashFS 文件系统加载二进制文件
sqfsls    - 列出目录中的文件。 默认值：根 (/)
sspi      - SPI 实用命令
stackprot_test- 测试堆栈保护器失败
sysboot   - 从 syslinux 文件获取并启动的命令
temperature- 热传感器温度
test      - 最小测试，如 /bin/sh
test_fdtdec- test_fdtdec
tftpboot  - 使用 TFTP 协议通过网络启动映像 要使用 IPv6，请添加 -ipv6 参数或使用用 [] 括号括起来的 IPv6 hostIPaddr
tftpput   - TFTP put命令，用于将文件上传到服务器
tftpsrv   - 充当 TFTP 服务器并启动第一个接收到的文件
time      - 运行命令并总结执行时间
timer     - 访问系统定时器
tpm       - 发出 TPMv1.x 命令
tpm2      - 发出 TPMv2.x 命令
tpmtest   - TPM 测试
true      - 什么都不做，成功
unbind    - 解除设备与驱动程序的绑定
unzip     - 解压缩内存区域
usb       - USB子系统
usbboot   - 从 USB 设备启动
ut        - 单元测试
ut_cmd    - 命令解析器的非常基本的测试
vbe       - 嵌入式验证启动
version   - 打印监视器、编译器和链接器版本
virtio    - virtio 块设备子系统
w1        - onewire 接口实用程序命令
wdt       - Watchdog 子系统
write     - 将二进制数据存储到分区
xxd       - 将文件作为十六进制转储打印到标准输出
```
