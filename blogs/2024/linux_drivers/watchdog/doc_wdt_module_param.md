
# WatchDog Module Parameters

此文件提供有关许多 Linux 看门狗驱动程序的模块参数的信息。 除非驱动程序有自己的特定于驱动程序的信息文件，否则看门狗驱动程序参数规范应在此处列出。

有关为内置驱动程序与可加载模块提供内核参数的信息，请参阅[内核的命令行参数](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html)。

----------------
watchdog core:

open_timeout:

&emsp;在用户空间打开相应的 /dev/watchdogN 设备之前，看门狗框架将负责 ping 正在运行的硬件看门狗的最长时间（以秒为单位）。 值 0 表示无限超时。 将其设置为非零值对于确保用户空间正常启动或主板重置并允许引导加载程序中的回退逻辑尝试其他操作非常有用。

----------------
acquirewdt:

wdt_stop:

&emsp;获取WDT“停止”io端口（默认0x43）

wdt_start:

&emsp;获取 WDT 'start' io 端口（默认 0x443）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
advantechwdt:

wdt_stop:

&emsp;研华 WDT“停止”io 端口（默认 0x443）

wdt_start:

&emsp;研华 WDT“启动”io 端口（默认 0x443）

timeout:

&emsp;看门狗超时（以秒为单位）。 1<=超时<=63，默认=60。

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
alim1535_wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 （0 < 超时 < 18000，默认=60）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
alim7101_wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 (1<=超时<=3600，默认=30)

use_gpio:

&emsp;使用 GPIO 看门狗（旧的钴板需要）。 默认=0/关闭/否

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
ar7_wdt:

margin:

&emsp;看门狗余量（以秒为单位）（默认=60）

nowayout:

&emsp;关闭时禁用看门狗关闭（默认=内核配置参数）

----------------
armada_37xx_wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 （默认=120）

nowayout:

&emsp;关闭时禁用看门狗关闭（默认=内核配置参数）

----------------
at91rm9200_wdt:

wdt_time:

&emsp;看门狗时间（以秒为单位）。 （默认=5）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
at91sam9_wdt:

heartbeat:

&emsp;看门狗心跳数秒。 （默认 = 15）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
bcm47xx_wdt:

wdt_time:

&emsp;看门狗时间（以秒为单位）。 （默认=30）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
coh901327_wdt:

margin:

&emsp;看门狗余量（以秒为单位）（默认 60 秒）

----------------
cpu5wdt:

port:

&emsp;看门狗卡基地址，默认为0x91

verbose:

&emsp;详细，默认为 0（否）

ticks:

&emsp;倒计时刻度，默认为 10000

----------------
cpwd:

wd0_timeout:

&emsp;默认 watchdog0 超时（1/10 秒）

wd1_timeout:

&emsp;默认 watchdog1 超时（1/10 秒）

wd2_timeout:

&emsp;默认 watchdog2 超时（1/10 秒）

----------------
da9052wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 2<=超时<=131，默认=2.048s

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
davinci_wdt:

heartbeat:

&emsp;看门狗心跳周期（以秒为单位）从 1 到 600，默认 60

----------------
ebc-c384_wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 (1<=超时<=15300，默认=60)

nowayout:

&emsp;看门狗一旦启动就无法停止

----------------
ep93xx_wdt:

nowayout:

&emsp;看门狗一旦启动就无法停止

timeout:

&emsp;看门狗超时（以秒为单位）。 （1<=超时<=3600，默认=TBD）

----------------
eurotechwdt:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

io:

&emsp;Eurotech WDT io 端口（默认=0x3f0）

irq:

&emsp;Eurotech WDT 中断（默认=10）

ev:

&emsp;Eurotech WDT 事件类型（默认为 int）

----------------
gef_wdt:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
geodewdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 1<=超时<=131，默认=60。

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
i6300esb:

heartbeat:

&emsp;看门狗心跳以秒为单位。 （1 < 心跳 < 2046，默认=30）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
iTCO_wdt:

heartbeat:

&emsp;看门狗心跳以秒为单位。 （2 < 心跳 < 39 (TCO v1) 或 613 (TCO v2)，默认=30）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
iTCO_vendor_support:

vendorsupport:

&emsp;iTCO 供应商特定支持模式，默认=0（无），1=SuperMicro Pent3，2=SuperMicro Pent4+，911=损坏的 SMI BIOS

----------------
ib700wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 0<=超时<=30，默认=30。

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
ibmasr:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
imx2_wdt:

timeout:

&emsp;看门狗超时（以秒为单位）（默认 60 秒）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
indydog:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
iop_wdt:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
it8712f_wdt:

margin:

&emsp;看门狗余量（以秒为单位）（默认 60）

nowayout:

&emsp;关闭时禁用看门狗关闭（默认=内核配置参数）

----------------
it87_wdt:

nogameport:

&emsp;禁止激活游戏端口，默认=0

nocir:

&emsp;禁止使用 CIR（某些有问题的设置的解决方法）； 如果系统重置（尽管看门狗守护进程正在运行），则设置为 1，默认值 = 0

exclusive:

&emsp;看门狗独占设备打开，默认=1

timeout:

&emsp;看门狗超时（以秒为单位），默认=60

testmode:

&emsp;看门狗测试模式（1 = 不重启），默认=0

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
ixp4xx_wdt:

heartbeat:

&emsp;看门狗心跳以秒为单位（默认 60 秒）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
machzwd:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

action:

&emsp;看门狗复位后，生成： 0 = RESET(*) 1 = SMI 2 = NMI 3 = SCI

----------------
max63xx_wdt:

heartbeat:

&emsp;看门狗心跳周期（以秒为单位）从 1 到 60，默认 60

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

nodelay:

&emsp;强制选择没有初始延迟的超时设置（仅限 max6373/74，默认=0）

----------------
mixcomwd:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
mpc8xxx_wdt:

timeout:

&emsp;看门狗超时（以刻度为单位）。 （0 < 超时 < 65536，默认=65535）

reset:

&emsp;看门狗中断/复位模式。 0 = 中断，1 = 复位

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
mv64x60_wdt:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
ni903x_wdt:

timeout:

&emsp;初始看门狗超时（以秒为单位）（0 < 超时 < 516，默认=60）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
nic7018_wdt:

timeout:

&emsp;初始看门狗超时（以秒为单位）（0 < 超时 < 464，默认=80）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
omap_wdt:

timer_margin:

&emsp;初始看门狗超时（以秒为单位）

early_enable:

&emsp;看门狗在模块插入时启动（默认=0）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
orion_wdt:

heartbeat:

&emsp;初始看门狗心跳（以秒为单位）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
pc87413_wdt:

io:

&emsp;pc87413 WDT I/O 端口（默认：io）。

timeout:

&emsp;看门狗超时（以分钟为单位）（默认=超时）。

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
pika_wdt:

heartbeat:

&emsp;看门狗心跳数秒。 （默认 = 15）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
pnx4008_wdt:

heartbeat:

&emsp;看门狗心跳周期（以秒为单位）从 1 到 60，默认 19

nowayout:

&emsp;设置为 1 以在设备释放后保持看门狗运行

----------------
pnx833x_wdt:

timeout:

&emsp;看门狗超时（Mhz）。 （68Mhz 时钟），默认=2040000000（30 秒）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

start_enabled:

&emsp;看门狗在模块插入时启动（默认=1）

----------------
pseries-wdt:

action:

&emsp;看门狗到期时采取的操作：0（关闭电源）、1（重新启动）、2（转储并重新启动）。 （默认=1）

timeout:

&emsp;初始看门狗超时（以秒为单位）。 （默认=60）

nowayout:

&emsp;看门狗一旦启动就无法停止. (default=kernel config parameter)

----------------
rc32434_wdt:

timeout:

&emsp;看门狗超时值，以秒为单位（默认=20）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
riowd:

riowd_timeout:

&emsp;看门狗超时（以分钟为单位）（默认=1）

----------------
s3c2410_wdt:

tmr_margin:

&emsp;看门狗 tmr_margin 以秒为单位。 （默认=15）

tmr_atboot:

&emsp;如果设置为 1，则看门狗在引导时启动，默认 = 0

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

soft_noboot:

&emsp;看门狗操作，设置为 1 忽略重新启动，设置为 0 重新启动

debug:

&emsp;看门狗调试，设置为 >1 进行调试，（默认 0）

----------------
sa1100_wdt:

margin:

&emsp;看门狗余量（以秒为单位）（默认 60 秒）

----------------
sb_wdog:

timeout:

&emsp;看门狗超时（以微秒为单位）（最大/默认 8388607 或 8.3 秒）

----------------
sbc60xxwdt:

wdt_stop:

&emsp;SBC60xx WDT“停止”io 端口（默认 0x45）

wdt_start:

&emsp;SBC60xx WDT“启动”io 端口（默认 0x443）

timeout:

&emsp;看门狗超时（以秒为单位）。 (1 <= 超时 <= 3600，默认=30)

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
sbc7240_wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 （1 <= 超时 <= 255，默认=30）

nowayout:

&emsp;关闭设备文件时禁用看门狗

----------------
sbc8360:

timeout:

&emsp;超时表索引 (0-63)（默认=27 (60s)）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
sbc_epx_c3:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
sbc_fitpc2_wdt:

margin:

&emsp;看门狗余量（以秒为单位）（默认 60 秒）

nowayout:

&emsp;看门狗一旦启动就无法停止

----------------
sbsa_gwdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 （默认10秒）

action:

&emsp;第一阶段超时时的看门狗动作，设置为0表示忽略，1表示恐慌。 （默认=0）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
sc1200wdt:

isapnp:

&emsp;当设置为 0 时，驱动程序 ISA PnP 支持将被禁用（默认=1）

io:

&emsp;io 端口

timeout:

&emsp;范围是 0-255 分钟，默认为 1

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
sc520_wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 （1 <= 超时 <= 3600，默认=30）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
sch311x_wdt:

force_id:

&emsp;覆盖检测到的设备 ID

therm_trip:

&emsp;如果 ThermTrip 触发复位发生器

timeout:

&emsp;看门狗超时（以秒为单位）。 1<=超时<=15300，默认=60

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
scx200_wdt:

margin:

&emsp;看门狗余量（以秒为单位）

nowayout:

&emsp;关闭时禁用看门狗关闭

----------------
shwdt:

clock_division_ratio:

&emsp;时钟分频比。 有效范围为 0x5 (1.31ms) 到 0x7 (5.25ms)。 （默认=7）

heartbeat:

&emsp;看门狗心跳以秒为单位。 （1 <= 心跳 <= 3600，默认=30

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
smsc37b787_wdt:

timeout:

&emsp;范围是 1-255 单位，默认为 60

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
softdog:

soft_margin:

&emsp;看门狗 soft_margin 以秒为单位。 （0 < soft_margin < 65536，默认=60）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

soft_noboot:

&emsp;Softdog 操作，设置为 1 忽略重新启动，设置为 0 重新启动（默认=0）

----------------
stmp3xxx_wdt:

heartbeat:

&emsp;看门狗心跳周期（以秒为单位）从 1 到 4194304，默认 19

----------------
tegra_wdt:

heartbeat:

&emsp;看门狗心跳数秒。 （默认 = 120）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
ts72xx_wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 （1 <= 超时 <= 8，默认=8）

nowayout:

&emsp;关闭时禁用看门狗关闭

----------------
twl4030_wdt:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
txx9wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 （0 < 超时 < N，默认=60）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
uniphier_wdt:

timeout:

&emsp;看门狗超时以两秒为单位。 （1 <= 超时 <= 128，默认=64）

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
w83627hf_wdt:

wdt_io:

&emsp;w83627hf/thf WDT io 端口（默认 0x2E）

timeout:

&emsp;看门狗超时（以秒为单位）。 1 <= 超时 <= 255，默认=60。

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
w83877f_wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 (1<=超时<=3600，默认=30)

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
w83977f_wdt:

timeout:

&emsp;看门狗超时（以秒为单位）(15..7635)，默认=45

testmode:

&emsp;看门狗测试模式（1 = 不重新启动），默认=0

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
wafer5823wdt:

timeout:

&emsp;看门狗超时（以秒为单位）。 1 <= 超时 <= 255，默认=60。

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
wdt285:

soft_margin:

&emsp;看门狗超时（以秒为单位）（默认 = 60）

----------------
wdt977:

timeout:

&emsp;看门狗超时（以秒为单位）（60..15300，默认=60）

testmode:

&emsp;看门狗测试模式（1 = 不重新启动），默认=0

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
wm831x_wdt:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
wm8350_wdt:

nowayout:

&emsp;看门狗一旦启动就无法停止（默认=内核配置参数）

----------------
sun4v_wdt:

timeout_ms:

&emsp;看门狗超时（以毫秒为单位）1..180000，默认=60000

nowayout:

&emsp;看门狗一旦启动就无法停止
