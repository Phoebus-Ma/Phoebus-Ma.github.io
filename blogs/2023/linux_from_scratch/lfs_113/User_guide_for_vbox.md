
## User guide

### 1. Download
--------

[Ubuntu 22.04](https://mirrors.ustc.edu.cn/ubuntu-releases/22.04/ubuntu-22.04.2-desktop-amd64.iso)

[Virtialbox 7.0](https://mirrors.tuna.tsinghua.edu.cn/virtualbox/7.0.8/VirtualBox-7.0.8-156879-Win.exe)

[lfs packages 11.3](https://mirrors.ustc.edu.cn/lfs/lfs-packages/lfs-packages-11.3.tar)

整个操作过程，是在Windows下，使用虚拟机中的Ubuntu完成的，并且顺利启动并进入LFS系统。
当然，除了Ubuntu之外，你可以选择其它Linux发行版，比如Gentoo等，下面的大多数步骤都是通用的。


### 2. Preparing the Host System
--------

#### 2.1 Create VM

1. 配置：

    - Type      : Linux
    - Version   : Ubuntu (64 bit)

    - CPU       : 6    cores
    - MEM       : 8192 MiB
    - Disk      : 35   GiB   (如果想在32G的U盘中部署，那么就不要超过25G)

    - Storage   : Optical Drive --> ubuntu-22.04.2-desktop-amd64.iso
    - Network   : Brigded Adapter

2. 开启虚拟化：

```bash
$ VBoxManage.exe list vms
$ VBoxManage.exe modifyvm "XXX" --nested-hw-virt on
```

其中"XXX"代表创建的虚拟机名称。

#### 2.2 Install Packages

启动虚拟机后，选择`Try Ubuntu`，我们只使用Ubuntu live功能，而不安装Ubuntu。

打开设置，Settings --> Power --> Screen Blank --> 设置为Never.

打开Terminal，做如下配置：

1. 修改Ubuntu源：

```bash
$ sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
$ sudo nano /etc/apt/sources.list
```

填入如下内容：

```bash
# 默认注释了源码仓库，如有需要可自行取消注释
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-security main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-security main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
```

2. 安装必要的软件：

```bash
$ sudo apt update
$ sudo apt install build-essential vim bc flex bison gawk m4 texinfo
$ 
$ cd /bin
$ sudo rm sh
$ sudo ln -s bash sh
$ cd ~
```

#### 2.3 Create Disk Partition

```bash
# 修改root密码
$ sudo passwd root

# 切换到root用户
$ su - root

# 查看磁盘
$ fdisk -l
```

这里假设磁盘是/dev/sda（如果严格按照上述步骤，这里一般就是/dev/sda），对磁盘进行分区：

| No.| Partition | Type | capacity        |
| -- | --------- | ---- | --------------- |
| 1  | /dev/sda1 | boot | 256M            |
| 2  | /dev/sda2 | swap | 2G              |
| 3  | /dev/sda3 |  /   | 35G - 256M - 2G |

```bash
$ fdisk /dev/sda

Welcome to fdisk (util-linux 2.37.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xd9475dd5.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-73400319, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-73400319, default 73400319): +256M

Created a new partition 1 of type 'Linux' and of size 256 MiB.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2):
First sector (526336-73400319, default 526336):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (526336-73400319, default 73400319): +2048M

Created a new partition 2 of type 'Linux' and of size 2 GiB.

Command (m for help): n
Partition type
   p   primary (2 primary, 0 extended, 2 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (3,4, default 3):
First sector (4720640-73400319, default 4720640):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (4720640-73400319, default 73400319):

Created a new partition 3 of type 'Linux' and of size 32.7 GiB.

Command (m for help): t
Partition number (1-3, default 3): 2
Hex code or alias (type L to list all): L

00 Empty            24 NEC DOS          81 Minix / old Lin  bf Solaris
01 FAT12            27 Hidden NTFS Win  82 Linux swap / So  c1 DRDOS/sec (FAT-
02 XENIX root       39 Plan 9           83 Linux            c4 DRDOS/sec (FAT-
03 XENIX usr        3c PartitionMagic   84 OS/2 hidden or   c6 DRDOS/sec (FAT-
04 FAT16 <32M       40 Venix 80286      85 Linux extended   c7 Syrinx
05 Extended         41 PPC PReP Boot    86 NTFS volume set  da Non-FS data
06 FAT16            42 SFS              87 NTFS volume set  db CP/M / CTOS / .
07 HPFS/NTFS/exFAT  4d QNX4.x           88 Linux plaintext  de Dell Utility
08 AIX              4e QNX4.x 2nd part  8e Linux LVM        df BootIt
09 AIX bootable     4f QNX4.x 3rd part  93 Amoeba           e1 DOS access
0a OS/2 Boot Manag  50 OnTrack DM       94 Amoeba BBT       e3 DOS R/O
0b W95 FAT32        51 OnTrack DM6 Aux  9f BSD/OS           e4 SpeedStor
0c W95 FAT32 (LBA)  52 CP/M             a0 IBM Thinkpad hi  ea Linux extended
0e W95 FAT16 (LBA)  53 OnTrack DM6 Aux  a5 FreeBSD          eb BeOS fs
0f W95 Ext'd (LBA)  54 OnTrackDM6       a6 OpenBSD          ee GPT
10 OPUS             55 EZ-Drive         a7 NeXTSTEP         ef EFI (FAT-12/16/
11 Hidden FAT12     56 Golden Bow       a8 Darwin UFS       f0 Linux/PA-RISC b
12 Compaq diagnost  5c Priam Edisk      a9 NetBSD           f1 SpeedStor
14 Hidden FAT16 <3  61 SpeedStor        ab Darwin boot      f4 SpeedStor
16 Hidden FAT16     63 GNU HURD or Sys  af HFS / HFS+       f2 DOS secondary
17 Hidden HPFS/NTF  64 Novell Netware   b7 BSDI fs          fb VMware VMFS
18 AST SmartSleep   65 Novell Netware   b8 BSDI swap        fc VMware VMKCORE
1b Hidden W95 FAT3  70 DiskSecure Mult  bb Boot Wizard hid  fd Linux raid auto
1c Hidden W95 FAT3  75 PC/IX            bc Acronis FAT32 L  fe LANstep
1e Hidden W95 FAT1  80 Old Minix        be Solaris boot     ff BBT

Aliases:
   linux          - 83
   swap           - 82
   extended       - 05
   uefi           - EF
   raid           - FD
   lvm            - 8E
   linuxex        - 85
Hex code or alias (type L to list all): 82

Changed type of partition 'Linux' to 'Linux swap / Solaris'.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

$ 
$  ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sda3
```

格式化分区：

```bash
$ mkfs -v -t ext4 /dev/sda1
$ mkswap /dev/sda2
$ mkfs -v -t ext4 /dev/sda3
```

挂载磁盘：

```bash
$ export LFS=/mnt/lfs
$ mkdir -pv $LFS
$ mount -v -t ext4 /dev/sda3 $LFS
$ mkdir -pv $LFS/boot
$ mount -v -t ext4 /dev/sda1 $LFS/boot
$ /sbin/swapon -v /dev/sda2
```


### 3. Packages and Patches
--------

```bash
$ mkdir -pv $LFS/sources
$ chmod -v a+wt $LFS/sources
$ cd $LFS/sources
$ wget https://mirrors.ustc.edu.cn/lfs/lfs-packages/lfs-packages-11.3.tar
$ tar -xf lfs-packages-11.3.tar
```

使用tar解包之后，会得到一个11.3的文件夹，我们把文件夹的源码移动到`$LFS/sources`下：

```bash
$ pwd
/mnt/lfs/sources
$ 
$ mv 11.3/* .
$ rm lfs-packages-11.3.tar
```

验证md5文件：

```bash
$ pushd $LFS/sources
  md5sum -c md5sums
popd
```

如果不是在root用户下下载的软件包，就执行如下命令，把文件所有者改为root：

```bash
$ chown root:root $LFS/sources/*
```

Note：为了防止关机重启系统导致之前执行的状态消失，创建快照。

`执行到此步，点击关闭按钮，选择"Save Machine"，保存为状态1.`

### 4. Final Preparations
--------

首先在root用户执行：

```bash
[ ! -e /etc/bash.bashrc ] || mv -v /etc/bash.bashrc /etc/bash.bashrc.NOUSE
```

其次执行：

```bash
export MAKEFLAGS='-j4'
```

其它按照文档执行，[4. Final Preparations](4.Final_preparations.md)。

`执行到此步，点击关闭按钮，选择"Save Machine"，保存为状态2.`


### 5. Compiling a Cross-Toolchain
--------

按照文档执行，[5. Compiling a Cross-Toolchain](5.Compiling_a_cross_toolchain.md)。

`执行到此步，点击关闭按钮，选择"Save Machine"，保存为状态3.`


### 6. Cross Compiling Temporary Tools
--------

按照文档执行，[6. Cross Compiling Temporary Tools](6.Cross_compiling_temporary_tools.md)。

`执行到此步，点击关闭按钮，选择"Save Machine"，保存为状态4.`


### 7. Entering Chroot and Building Additional Temporary Tools
--------

按照文档执行，[7. Entering Chroot and Building Additional Temporary Tools](7.Entering_chroot_and_building_additional_temporary_tools.md)。

`执行到此步，点击关闭按钮，选择"Save Machine"，保存为状态5.`


### 8. Installing Basic System Software
--------

在`8.78 Stripping`之前，重新执行：`8.23 Acl-2.3.1`、`8.35 Libtool-2.4.7`，之后执行8.78之后的内容。
其它按照文档执行，[8. Installing Basic System Software](8.Installing_basic_system_software.md)。

`执行到此步，点击关闭按钮，选择"Save Machine"，保存为状态6.`


### 9. System Configuration
--------

跳过9.3、9.4.2、9.4.3。

- 9.5.1：

```bash
$ ls /sys/class/net
enp0s3  lo
```

填写如下内容：

```bash
cd /etc/sysconfig/
cat > ifconfig.enp0s3 << "EOF"
ONBOOT=yes
IFACE=enp0s3
SERVICE=ipv4-static
IP=192.168.1.15
GATEWAY=192.168.1.1
PREFIX=24
BROADCAST=192.168.1.255
EOF
```

- 9.5.2：

```bash
cat > /etc/resolv.conf << "EOF"
# Begin /etc/resolv.conf

domain mytest.org
nameserver 180.76.76.76
nameserver 8.8.8.8

# End /etc/resolv.conf
EOF
```

- 9.5.3：

```bash
echo "lfs113test" > /etc/hostname
```

- 9.5.4

```bash
cat > /etc/hosts << "EOF"
# Begin /etc/hosts

127.0.0.1 localhost.localdomain localhost
192.168.1.15 lfs113test.mytest.org lfs113test

# End /etc/hosts
EOF
```

- 9.6.5:

```bash
cat > /etc/sysconfig/console << "EOF"
# Begin /etc/sysconfig/console

LOGLEVEL="3"

# End /etc/sysconfig/console
EOF
```

跳过9.6.7、9.6.8。

- 9.7：

```bash
$ LC_ALL=en_US.utf8 locale charmap
```

```bash
cat > /etc/profile << "EOF"
# Begin /etc/profile

export LANG=en_US.UTF-8

# End /etc/profile
EOF
```

其它按照文档执行，[9. System Configuration](9.System_configuration.md)。


### 10. Making the LFS System Bootable
--------

- 10.2：

```bash
cat > /etc/fstab << "EOF"
# Begin /etc/fstab

# file system  mount-point  type     options             dump  fsck
#                                                              order

/dev/sda3      /            ext4     defaults            1     1
/dev/sda1      /boot        ext4     noauto              1     2
/dev/sda2      swap         swap     pri=1               0     0
proc           /proc        proc     nosuid,noexec,nodev 0     0
sysfs          /sys         sysfs    nosuid,noexec,nodev 0     0
devpts         /dev/pts     devpts   gid=5,mode=620      0     0
tmpfs          /run         tmpfs    defaults            0     0
devtmpfs       /dev         devtmpfs mode=0755,nosuid    0     0
tmpfs          /dev/shm     tmpfs    nosuid,nodev        0     0

# End /etc/fstab
EOF
```

- 10.3.1:

在`make menuconfig`之前：

```bash
$ make defconfig
```

最后一步：

```bash
$ chmod -R 0:0 /sources/linux-6.1.11
```

跳过10.3.2、10.4.1、10.4.2。

- 10.4.4：

```bash
cat > /boot/grub/grub.cfg << "EOF"
# Begin /boot/grub/grub.cfg
set default=0
set timeout=5

insmod ext2
set root=(hd0,1)

menuentry "GNU/Linux, Linux 6.1.11-lfs-11.3" {
        linux   /vmlinuz-6.1.11-lfs-11.3 root=/dev/sda3 ro
}
EOF
```

其它按照文档执行，[10. Making the LFS System Bootable](10.Making_the_lfs_system_bootable.md)。

`执行到此步，点击关闭按钮，选择"Save Machine"，保存为状态7.`


### 11. The End
--------

按照文档执行，[11. The End](11.The_end.md)。

`执行到此步，点击关闭按钮，选择"Save Machine"，保存为状态8.` 重启系统，顺利的话，就可以正常进入lfs了。
