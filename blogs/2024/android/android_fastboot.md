
# Android Fastboot

# 1. Android 分区

| 分区名    | 设备          | 说明                         |
| -------- | ------------- | ---------------------------- |
| splash1  | <none>        | 启动画面图像。                |
| misc     | /dev/mtd/mtd0 | 是否有一些标志用于控制设备模式 |
| recovery | /dev/mtd/mtd1 | 恢复模式                     |
| boot     | /dev/mtd/mtd2 | 内核启动分区                  |
| system   | /dev/mtd/mtd3 | Android系统部分              |
| cache    | /dev/mtd/mtd4 | 缓存文件夹                   |
| userdata | /dev/mtd/mtd5 | 用户安装的软件以及各种数据     |

安卓系统一般把rom芯片分成7个区，如果再加上内置sd卡这个分区，就是8个：

- hboot分区----------负责启动。

- radio分区----------负责驱动。

- recovery分区-------负责恢复。

- boot分区-----------系统内核。

- system分区---------系统文件。

- cache分区----------系统缓存。

- userdata分区-------用户数据。

就是第7个区userdate区的大小，这个区主要装一些软件，是用户可以使用的分区。这个分区的大小直接影响到安装软件的数量。也就是说userdate区就是我们常说的“可用rom”在安卓里，虽然软件可以安装到卡中，但是一些系统软件最好还是安装到userdata分区，否则会出现加载的一些小问题，比如使用的桌面软件和一些在桌面上显示插件的软件。并且就算你把软件都装到卡里，userdata分区还是会不断减小。就像在电脑里，就算你把软件装到D盘，仍然会在c盘生成一些系统文件，占掉c盘空间一样。


# 2. BootLoader

简单地说，BootLoader就是在操作系统内核运行之前运行的一段小程序。通过这段小程序，我们可以初始化硬件设备、建立内存空间映射图，从而将系统的软硬件环境带到一个合适状态，以便为最终调用操作系统内核准备好正确的环境。在嵌入式系统中，通常并没有像BIOS那样的固件程序（注，有的嵌入式CPU也会内嵌一段短小的启动程序），因此整个系统的加载启动任务就完全由BootLoader来完成。比如在一个基于ARM7TDMI core的嵌入式系统中，系统在上电或复位时通常都从地址0x00000000处开始执行，而在这个地址处安排的通常就是系统的BootLoader程序。


# 3. BootLoader 和 FastBoot

从字面意思来讲，Fastboot是一个英文单词，翻译成中文的意思是『快速启动』。Fastboot 可以说是一个通信协议，电脑可以通过这个通信协议，直接向手机系统不同分区中写入文件（.img 文件）。

fastboot 主要是用来与bootloader的USB通讯的PC命令行工具。他一般主要也用来向bootloader传送刷机文件进行文件分区重烧。 因此在使用时，必须有一个PC机并且USB线要始终联着。所以这种方式称为线刷。 用fastboot需要bootloader 支持，所以不是每一家公司产品都支的这个功能的

通常大家所讨论的Fastboot，通常都是说安卓手机的Fastboot模式。在安卓手机中fastboot是一种比recovery更底层的刷机模式。简单来说，就是使用USB数据线，连接电脑的一种刷机模式。这种刷机方式，我们又通常称为“线刷”刷机。另外安卓手机还有一种刷机方式，叫做“卡刷”，两者区别如下。

线刷： 直接想手机硬盘写入*.img 文件，我个人觉得这种方法比较快捷，而且省事。但是必须借助电脑和数据线。
卡刷：就是利用recovery的从SD卡中更新系统的这个功能，如果你想刷第三方Rom，必须刷入个第三方recovery，只有fastboot模式才能刷recovery.img。卡刷有个限制，必须要把想要更新的ROM（Android系统）拷贝到SD卡上。如果手机已经是砖了。那只能用线刷了。


# 4. 命令

```bash
$ fastboot -h
usage: fastboot [ <option> ] <command>

commands:
    update <filename>                        reflash device from update.zip
    flashall                                 flash boot + recovery + system
    flash <partition> [ <filename> ]         write a file to a flash partition
    erase <partition>                        erase a flash partition
    getvar <variable>                        display a bootloader variable
    boot <kernel> [ <ramdisk> ]              download and boot kernel
    flash:raw boot <kernel> [ <ramdisk> ]    create bootimage and flash it
    devices                                  list all connected devices
    reboot                                   reboot device normally
    reboot-bootloader                        reboot device into bootloader
 
options:
    -w                                       erase userdata and cache
    -s <serial number>                       specify device serial number
    -p <product>                             specify product name
    -c <cmdline>                             override kernel commandline
    -i <vendor id>                           specify a custom USB vendor id
```

fastboot(bootloader)模式怎么进入？

1. 大多数安卓手机，都可以在关机状态下，然后同时按住【电源键】+【音量+】键，大约2-3s后，就可以进入Fastboot模式。

2. 作为开发者在开机状态下可以用下面的方式进入：

```bash
$ adb reboot bootloader
```

然后就可以执行下面的fastboot命名了:

```bash
$ fastboot flashing unlock    #6.0以上设备 设备必须解锁，开始刷机（这个不同的手机厂商不同）
$ fastboot erase {partition}  # 擦除分区
$ fastboot  erase  frp    # 擦除 frp 分区，frp 即 Factory Reset Protection，用于防止用户信息在手机丢失后外泄
$ fastboot  flash  boot  boot.img    # 刷入 boot 分区
$ fastboot  flash  system  system.img    # 刷入 system 分区
$ fastboot  flash  recovery  recovery.img    # 刷入 recovery 分区
$ fastboot flashall    #烧写所有分区，注意：此命令会在当前目录中查找所有img文件，将这些img文件烧写到所有对应的分区中，并重新启动手机。
$ fastboot  format  data    # 格式化 data 分区
$ fastboot  flashing lock    # 设备上锁，刷机完毕
$ fastboot  continue    # 自动重启设备
$ fastboot reboot# 重启手机
$ fastboot reboot-bootloader# 重启到bootloader 刷机用
$ fastboot devices  ## 发现手机，显示当前哪些手机通过fastboot连接了
```

一次烧写boot，system，recovery分区

1. 创建包含boot.img，system.img，recovery.img文件的zip包。

2. 执行：fastboot update {*.zip}

注：华为手机解锁命令: fastboot oem unlock 解锁码


# 5. Recovery 模式的简介

Recovery 更类似于一个小型的管理系统。只不过功能简单，所做的管理有限。在recovery模式下，会加载了部分文件系统，所以才可以读sdcard中的update.zip进行刷机，当然，也可以清除cache和用户数据。

该模式可根据用户的需要进行修改，因此有官方recovery模式以及第三方recovery模式。第三方recovery模式可以识别第三方rom包，因此可以用来刷机。而官方recovery一般不能识别第三方zip文件。好用的第三方RE:TWRP 和 CWM

Recovery刷机包是称为Google Update 格式。在用Recovery恢复时，刷机包通常放在SD卡里，所以这里刷机一般称为卡刷。


# 6. 刷入RE:

首先进入 bootloader（fastboot）模式:

```bash
$ fastboot flash recovery recovery.img
```
