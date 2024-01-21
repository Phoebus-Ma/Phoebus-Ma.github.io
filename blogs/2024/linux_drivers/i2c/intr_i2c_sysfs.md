
# Linux I2C Sysfs

# 1. Overview

由于 I2C MUX（I2C 多路复用器）的存在，I2C 拓扑可能会很复杂。 Linux 内核将 MUX 通道抽象为逻辑 I2C 总线编号。 然而，从 I2C 总线物理编号和 MUX 拓扑到逻辑 I2C 总线编号的映射存在知识差距。 本文档旨在填补这一空白，因此观众（例如硬件工程师和新软件开发人员）可以通过了解物理 I2C 拓扑并浏览 Linux shell 中的 I2C sysfs 来了解内核中逻辑 I2C 总线的概念 。 这些知识对于使用 i2c 工具进行开发和调试非常有用且必不可少。


## 1.1 Target audience

需要使用 Linux shell 与运行 Linux 的系统上的 I2C 子系统进行交互的人员。


## 1.2 Prerequisites

1. 了解一般 Linux shell 文件系统命令和操作。
2. I2C、I2C MUX 和 I2C 拓扑的一般知识。


# 2. Location of I2C Sysfs

通常，Linux Sysfs 文件系统挂载在 /sys 目录下，因此您可以在 /sys/bus/i2c/devices 下找到 I2C Sysfs，您可以直接 cd 到它。 该目录下有一个符号链接列表。 以 i2c- 开头的链路是 I2C 总线，可以是物理总线，也可以是逻辑总线。 其他以数字开头和结尾的链接是I2C设备，其中第一个数字是I2C总线号，第二个数字是I2C地址。

以 Google Pixel 3 手机为例：

```bash
blueline:/sys/bus/i2c/devices $ ls
0-0008  0-0061  1-0028  3-0043  4-0036  4-0041  i2c-1  i2c-3
0-000c  0-0066  2-0049  4-000b  4-0040  i2c-0   i2c-2  i2c-4
```

i2c-2是编号为2的I2C总线，2-0049是与内核驱动程序绑定的总线2地址0x49上的I2C设备。


# 3. Terminology

首先，让我们定义一些术语以避免在后面的部分中混淆。


## 3.1 (Physical) I2C Bus Controller

运行Linux内核的硬件系统可能有多个物理I2C总线控制器。 控制器是硬件和物理的，并且系统可以在存储器空间中定义多个寄存器来操作控制器。 Linux 内核在源目录`drivers/i2c/busses`下有 I2C 总线驱动程序，用于将内核 I2C API 转换为不同系统的寄存器操作。 该术语不仅限于 Linux 内核。


## 3.2 I2C Bus Physical Number

对于每个物理 I2C 总线控制器，系统供应商可以为每个控制器分配一个物理编号。 例如，具有最低寄存器地址的第一个I2C总线控制器可以被称为`I2C-0`。


## 3.3 Logical I2C Bus

您在 Linux I2C Sysfs 中看到的每个 I2C 总线编号都是分配有编号的逻辑 I2C 总线。 这类似于软件代码通常写入虚拟内存空间而不是物理内存空间的事实。

每个逻辑 I2C 总线可以是物理 I2C 总线控制器的抽象，也可以是 I2C MUX 后面通道的抽象。 如果它是 MUX 通道的抽象，每当我们通过这样的逻辑总线访问 I2C 设备时，内核都会将 I2C MUX 切换到正确的通道，作为抽象的一部分。


## 3.4 Physical I2C Bus

如果逻辑 I2C 总线是物理 I2C 总线控制器的直接抽象，我们称其为物理 I2C 总线。


## 3.5 Caveat

对于只了解主板物理 I2C 设计的人来说，这可能是一个令人困惑的部分。 实际上可以将 I2C 总线物理编号重命名为设备树源 (DTS) 部分别名下逻辑 I2C 总线级别中的不同编号。 有关 DTS 文件的示例，请参阅`arch/arm/boot/dts/nuvoton-npcm730-gsj.dts`。

最佳实践：（致内核软件开发人员）最好保持I2C总线物理编号与其对应的逻辑I2C总线编号相同，而不是重命名或映射它们，这样可能不会让其他用户感到困惑。 这些物理 I2C 总线可以作为 I2C MUX 扇出的良好起点。 对于以下示例，我们假设物理 I2C 总线的编号与其 I2C 总线物理编号相同。


# 4. Walk through Logical I2C Bus

在接下来的内容中，我们将以更复杂的I2C拓扑为例。 这是 I2C 拓扑的简要图。 如果您乍一看不理解此图，请不要害怕继续阅读此文档，并在阅读完后进行复习。

```bash
i2c-7 (physical I2C bus controller 7)
`-- 7-0071 (4-channel I2C MUX at 0x71)
    |-- i2c-60 (channel-0)
    |-- i2c-73 (channel-1)
    |   |-- 73-0040 (I2C sensor device with hwmon directory)
    |   |-- 73-0070 (I2C MUX at 0x70, exists in DTS, but failed to probe)
    |   `-- 73-0072 (8-channel I2C MUX at 0x72)
    |       |-- i2c-78 (channel-0)
    |       |-- ... (channel-1...6, i2c-79...i2c-84)
    |       `-- i2c-85 (channel-7)
    |-- i2c-86 (channel-2)
    `-- i2c-203 (channel-3)
```


## 4.1 Distinguish Physical and Logical I2C Bus

区分物理 I2C 总线和逻辑 I2C 总线的一种简单方法是使用命令 ls -l 或 readlink 读取 I2C 总线目录下的符号链接设备。

要检查的替代符号链接是 mux_device。 此链接仅存在于从另一条 I2C 总线扇出的逻辑 I2C 总线目录中。 阅读此链接还将告诉您哪个 I2C MUX 设备创建了此逻辑 I2C 总线。

如果符号链接指向以.i2c结尾的目录，那么它应该是物理I2C总线，直接抽象出一个物理I2C总线控制器。 例如：

```bash
$ readlink /sys/bus/i2c/devices/i2c-7/device
../../f0087000.i2c
$ ls /sys/bus/i2c/devices/i2c-7/mux_device
ls: /sys/bus/i2c/devices/i2c-7/mux_device: No such file or directory
```

在这种情况下，i2c-7是物理I2C总线，因此它的目录下没有符号链接mux_device。 并且，如果内核软件开发者遵循通常的做法，不重命名物理I2C总线，则这也应该指的是系统的物理I2C总线控制器7。

另一方面，如果符号链接指向另一条 I2C 总线，则当前目录呈现的 I2C 总线必须是逻辑总线。 链接指向的 I2C 总线是父总线，它可以是物理 I2C 总线，也可以是逻辑总线。 在这种情况下，当前目录呈现的I2C总线在父总线下抽象出一个I2C MUX通道。

比如:

```bash
$ readlink /sys/bus/i2c/devices/i2c-73/device
../../i2c-7
$ readlink /sys/bus/i2c/devices/i2c-73/mux_device
../7-0071
```

i2c-73 是 i2c-7 下 I2C MUX 的逻辑总线扇出，其 I2C 地址为 0x71。 每当我们访问总线 73 的 I2C 设备时，作为抽象的一部分，内核始终会将地址为 0x71 的 I2C MUX 切换到正确的通道。


## 4.2 Finding out Logical I2C Bus Number

在本节中，我们将描述如何基于物理硬件 I2C 拓扑的知识找出代表某些 I2C MUX 通道的逻辑 I2C 总线号。

在此示例中，我们有一个具有物理 I2C 总线 7 且未在 DTS 中重命名的系统。 该总线上的地址 0x71 处有一个 4 通道 MUX。 在 0x71 MUX 的通道 1 后面，地址 0x72 处还有另一个 8 通道 MUX。 让我们浏览 Sysfs 并找出 0x72 MUX 通道 3 的逻辑 I2C 总线号。

首先我们进入i2c-7的目录：

```bash
~$ cd /sys/bus/i2c/devices/i2c-7
/sys/bus/i2c/devices/i2c-7$ ls
7-0071         i2c-60         name           subsystem
delete_device  i2c-73         new_device     uevent
device         i2c-86         of_node
i2c-203        i2c-dev        power
```

在那里，我们看到 0x71 MUX 为 7-0071。 进入其中：

```bash
/sys/bus/i2c/devices/i2c-7$ cd 7-0071/
/sys/bus/i2c/devices/i2c-7/7-0071$ ls -l
channel-0   channel-3   modalias    power
channel-1   driver      name        subsystem
channel-2   idle_state  of_node     uevent
```

使用 readlink 或 ls -l 读取链接通道 1：

```bash
/sys/bus/i2c/devices/i2c-7/7-0071$ readlink channel-1
../i2c-73
```

我们发现 i2c-7 上 0x71 MUX 的通道 1 分配了逻辑 I2C 总线编号 73。让我们继续以任一方式访问目录 i2c-73：

```bash
# cd to i2c-73 under I2C Sysfs root
/sys/bus/i2c/devices/i2c-7/7-0071$ cd /sys/bus/i2c/devices/i2c-73
/sys/bus/i2c/devices/i2c-73$

# cd the channel symbolic link
/sys/bus/i2c/devices/i2c-7/7-0071$ cd channel-1
/sys/bus/i2c/devices/i2c-7/7-0071/channel-1$

# cd the link content
/sys/bus/i2c/devices/i2c-7/7-0071$ cd ../i2c-73
/sys/bus/i2c/devices/i2c-7/i2c-73$
```

无论哪种方式，您最终都会进入 i2c-73 目录。 与上面类似，我们现在可以找到 0x72 MUX 以及为其通道分配的逻辑 I2C 总线编号：

```bash
/sys/bus/i2c/devices/i2c-73$ ls
73-0040        device         i2c-83         new_device
73-004e        i2c-78         i2c-84         of_node
73-0050        i2c-79         i2c-85         power
73-0070        i2c-80         i2c-dev        subsystem
73-0072        i2c-81         mux_device     uevent
delete_device  i2c-82         name
/sys/bus/i2c/devices/i2c-73$ cd 73-0072
/sys/bus/i2c/devices/i2c-73/73-0072$ ls
channel-0   channel-4   driver      of_node
channel-1   channel-5   idle_state  power
channel-2   channel-6   modalias    subsystem
channel-3   channel-7   name        uevent
/sys/bus/i2c/devices/i2c-73/73-0072$ readlink channel-3
../i2c-81
```

在那里，我们发现0x72 MUX的通道3的逻辑I2C总线编号是81。稍后我们可以使用这个编号切换到自己的I2C Sysfs目录或发出i2c-tools命令。

提示：一旦您了解了 MUX 的 I2C 拓扑，I2C 工具中的命令 i2cdetect -l 就可以轻松地为您提供 I2C 拓扑的概述（如果您的系统可用）。 例如：

```bash
$ i2cdetect -l | grep -e '\-73' -e _7 | sort -V
i2c-7   i2c             npcm_i2c_7                              I2C adapter
i2c-73  i2c             i2c-7-mux (chan_id 1)                   I2C adapter
i2c-78  i2c             i2c-73-mux (chan_id 0)                  I2C adapter
i2c-79  i2c             i2c-73-mux (chan_id 1)                  I2C adapter
i2c-80  i2c             i2c-73-mux (chan_id 2)                  I2C adapter
i2c-81  i2c             i2c-73-mux (chan_id 3)                  I2C adapter
i2c-82  i2c             i2c-73-mux (chan_id 4)                  I2C adapter
i2c-83  i2c             i2c-73-mux (chan_id 5)                  I2C adapter
i2c-84  i2c             i2c-73-mux (chan_id 6)                  I2C adapter
i2c-85  i2c             i2c-73-mux (chan_id 7)                  I2C adapter
```


## 4.3 Pinned Logical I2C Bus Number

如果 DTS 中未指定，则当应用 I2C MUX 驱动程序并成功探测到 MUX 设备时，内核将根据当前最大逻辑总线编号递增地为 MUX 通道分配逻辑总线编号。 例如，如果系统以 i2c-15 作为最高逻辑总线编号，并且成功应用了 4 通道 MUX，则我们将为 MUX 通道 0 使用 i2c-16，并一直为 i2c-19 作为 MUX 频道 3.

内核软件开发人员能够将扇出 MUX 通道固定到 DTS 中的静态逻辑 I2C 总线编号。 本文档不会详细介绍如何在 DTS 中实现这一点，但我们可以在以下位置看到一个示例：`arch/arm/boot/dts/aspeed-bmc-facebook-wedge400.dts`

在上面的例子中，在物理I2C总线2上地址0x70处有一个8通道I2C MUX。MUX的通道2在DTS中定义为imux18，并以`i2c18 = &imux18`线路固定到逻辑I2C总线18上;在节别名中。

更进一步，可以设计一个易于人类记忆或算术计算的逻辑 I2C 总线编号模式。 例如，我们可以将总线 3 上的 MUX 的扇出通道固定为从 30 开始。因此 30 将是总线 3 上的 MUX 的通道 0 的逻辑总线编号，37 将是该通道的逻辑总线编号 总线 3 上的 MUX 7。


# 5. I2C Devices

在前面的章节中，我们主要介绍了 I2C 总线。 在本节中，让我们看看从链接名称为${bus}-${addr}格式的I2C设备目录中我们可以了解到什么。 名称中的${bus}部分是逻辑I2C总线十进制数，而${addr}部分是每个设备的I2C地址的十六进制数。


## 5.1 I2C Device Directory Content

在每个 I2C 设备目录中，都有一个名为 name.txt 的文件。 该文件告诉内核驱动程序使用什么设备名称来探测该设备。 使用命令 cat 来读取其内容。 例如：

```bash
/sys/bus/i2c/devices/i2c-73$ cat 73-0040/name
ina230
/sys/bus/i2c/devices/i2c-73$ cat 73-0070/name
pca9546
/sys/bus/i2c/devices/i2c-73$ cat 73-0072/name
pca9547
```

有一个名为 driver 的符号链接，用于告知使用哪个 Linux 内核驱动程序来探测该设备：

```bash
/sys/bus/i2c/devices/i2c-73$ readlink -f 73-0040/driver
/sys/bus/i2c/drivers/ina2xx
/sys/bus/i2c/devices/i2c-73$ readlink -f 73-0072/driver
/sys/bus/i2c/drivers/pca954x
```

但是，如果链接驱动程序一开始就不存在，则可能意味着内核驱动程序由于某些错误而未能探测到该设备。 在dmesg中可能会发现错误：

```bash
/sys/bus/i2c/devices/i2c-73$ ls 73-0070/driver
ls: 73-0070/driver: No such file or directory
/sys/bus/i2c/devices/i2c-73$ dmesg | grep 73-0070
pca954x 73-0070: probe failed
pca954x 73-0070: probe failed
```

根据 I2C 设备是什么以及用于探测设备的内核驱动程序，设备目录中可能有不同的内容。


## 5.2 I2C MUX Device

虽然您可能在前面的部分中已经意识到这一点，但 I2C MUX 设备在其设备目录中将具有符号链接通道-*。 这些符号链接指向其逻辑 I2C 总线目录：

```bash
/sys/bus/i2c/devices/i2c-73$ ls -l 73-0072/channel-*
lrwxrwxrwx ... 73-0072/channel-0 -> ../i2c-78
lrwxrwxrwx ... 73-0072/channel-1 -> ../i2c-79
lrwxrwxrwx ... 73-0072/channel-2 -> ../i2c-80
lrwxrwxrwx ... 73-0072/channel-3 -> ../i2c-81
lrwxrwxrwx ... 73-0072/channel-4 -> ../i2c-82
lrwxrwxrwx ... 73-0072/channel-5 -> ../i2c-83
lrwxrwxrwx ... 73-0072/channel-6 -> ../i2c-84
lrwxrwxrwx ... 73-0072/channel-7 -> ../i2c-85
```


## 5.3 I2C Sensor Device / Hwmon

I2C 传感器设备也很常见。 如果它们被内核 hwmon（硬件监控）驱动程序成功绑定，您将在 I2C 设备目录中看到 hwmon 目录。 继续深入下去，你会发现 I2C 传感器设备的 Hwmon Sysfs：

```bash
/sys/bus/i2c/devices/i2c-73/73-0040/hwmon/hwmon17$ ls
curr1_input        in0_lcrit_alarm    name               subsystem
device             in1_crit           power              uevent
in0_crit           in1_crit_alarm     power1_crit        update_interval
in0_crit_alarm     in1_input          power1_crit_alarm
in0_input          in1_lcrit          power1_input
in0_lcrit          in1_lcrit_alarm    shunt_resistor
```

有关 Hwmon Sysfs 的更多信息，请参阅文档：

[sysfs 文件的命名和数据格式标准](https://www.kernel.org/doc/html/latest/hwmon/sysfs-interface.html)


## 5.4 Instantiate I2C Devices in I2C Sysfs

参考 [如何实例化I2C设备](https://www.kernel.org/doc/html/latest/i2c/instantiating-devices.html) 的“方法4：从用户空间实例化”一节。
