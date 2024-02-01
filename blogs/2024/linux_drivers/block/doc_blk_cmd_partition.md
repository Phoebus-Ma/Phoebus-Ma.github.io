
# Embedded device command line partition parsing

“blkdevparts”命令行选项添加了对从内核命令行读取块设备分区表的支持。

它通常用于固定块 (eMMC) 嵌入式设备。 它没有MBR，因此节省了存储空间。 通过块设备上数据的绝对地址可以轻松访问引导加载程序。 用户可以轻松更改分区。

命令行的格式就像 mtdparts 一样：

```bash
blkdevparts=<blkdev-def>[;<blkdev-def>]
    <blkdev-def> := <blkdev-id>:<partdef>[,<partdef>]
        <partdef> := <size>[@<offset>](part-name)
```

**<blkdev-id>**

&emsp;块设备磁盘名称。 嵌入式设备使用固定块设备。 其磁盘名称也是固定的，如：mmcblk0、mmcblk1、mmcblk0boot0。

**<size>**

&emsp;分区大小，以字节为单位，如：512、1m、1G。 size 可能包含可选后缀（大写或小写）：

&emsp;K, M, G, T, P, E.

&emsp;“-”用于表示所有剩余空间。

**<offset>**

&emsp;分区起始地址，以字节为单位。 offset 可以包含可选后缀（大写或小写）：

&emsp;K, M, G, T, P, E.

**(part-name)**

&emsp;分区名称。 内核发送带有“PARTNAME”的 uevent。 应用程序可以创建一个指向名为“PARTNAME”的块设备分区的链接。 用户空间应用程序可以通过分区名称访问分区。

示例：

eMMC 磁盘名称为“mmcblk0”和“mmcblk0boot0”。

bootargs:

```bash
'blkdevparts=mmcblk0:1G(data0),1G(data1),-;mmcblk0boot0:1m(boot),-(kernel)'
```

dmesg:

```bash
mmcblk0: p1(data0) p2(data1) p3()
mmcblk0boot0: p1(boot) p2(kernel)
```
