
# Virtualbox源码编译

# 1. 下载源代码

环境 : Ubuntu 22.04 for x64

[Virtualbox-7.0.10.tar.bz2](https://download.virtualbox.org/virtualbox/7.0.10/VirtualBox-7.0.10.tar.bz2).


# 2. 配置环境

1. 安装dev-base

```bash
$ sudo apt install build-essential flex bc bison gawk texinfo file ssh git unzip
$ sudo apt install gcc-12 g++-12 lib32gcc-12-dev gcc-12-multilib g++-12-multilib
```

2. 重定向gcc

```bash
$ sudo ln -s /bin/gcc-12 /bin/gcc
$ sudo ln -s /bin/g++-12 /bin/g++
```

3. 安装依赖

```bash
$ sudo apt install acpica-tools chrpath doxygen libasound2-dev libcap-dev lib32stdc++6  \
        libcurl4-openssl-dev libdevmapper-dev libidl-dev libopus-dev libpam0g-dev       \
        libpulse-dev libqt5opengl5-dev libqt5x11extras5-dev qttools5-dev libsdl1.2-dev  \
        libsdl-ttf2.0-dev libssl-dev libvpx-dev libxcursor-dev libxinerama-dev          \
        libxml2-dev libxml2-utils libxmu-dev libxrandr-dev make nasm python3-dev        \
        qttools5-dev-tools texlive texlive-fonts-extra texlive-latex-extra unzip        \
        xsltproc default-jdk libstdc++5 libxslt1-dev linux-kernel-headers makeself      \
        mesa-common-dev subversion yasm zlib1g-dev glslang-tools libc6-dev-i386
```

4. 链接库

```bash
ln -s libX11.so.6    /usr/lib32/libX11.so 
ln -s libXTrap.so.6  /usr/lib32/libXTrap.so 
ln -s libXt.so.6     /usr/lib32/libXt.so 
ln -s libXtst.so.6   /usr/lib32/libXtst.so
ln -s libXmu.so.6    /usr/lib32/libXmu.so
ln -s libXext.so.6   /usr/lib32/libXext.so
```


# 3. 编译

1. 配置

```bash
$ ./configure --disable-hardening
```

2. 设置环境

```bash
$ source ./env.sh
```

3. 编译

Release:

```bash
$ kmk all
```

Debug:

```bash
$ kmk BUILD_TYPE=debug
```

如果编译不过, 则可以使用如下编译方法:

```bash
$ kmk BUILD_TYPE=debug -i
```

运行编译生成的可执行文件:

```bash
$ cd out/linux.amd64/debug/bin/
$ ./VirtualBox
```
