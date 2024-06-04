
# Create cross toolchain using crosstool-ng

# 1. Enviorments

Platform: Ubuntu 22.04

```bash
$ sudo apt update
$ sudo apt install build-essential flex bison gawk texinfo bc file git ssh \
                zip unzip libncurses-dev libssl-dev u-boot-tools net-tools \
                liblz4-tool libtool libtool-bin help2man python-dev-is-python3
$ sudo apt install qemu-user-static qemu-system-arm
```


# 2. Build ct-ng

```bash
$ mkdir $HOME/crosstools
$ cd $HOME/crosstools/
$ git clone https://github.com/crosstool-ng/crosstool-ng.git
$ mkdir dl out _install
$ cd crosstool-ng/
$ ./bootstrap
$ ./configure --prefix=$HOME/crosstools/out
$ make
$ make install
$ export PATH=$PATH:$HOME/crosstools/out/bin
$ ct-ng help
$ git branch -vv
* master 06fad54c [origin/master] gcc: Add GCC 13.3.0
```


# 3. Build cross toolchain

```bash
$ ct-ng list-samples
...
[L...]   aarch64-rpi4-linux-gnu
[L...]   aarch64-unknown-linux-gnu
[L...]   aarch64-unknown-linux-musl
[L...]   aarch64-unknown-linux-uclibc
...
[L...]   arm-unknown-linux-gnueabi
...
[L...]   i686-w64-mingw32
...
[L..X]   loongarch64-unknown-linux-gnu
...
[L...]   mipsel-multilib-linux-gnu
[L...]   mips-unknown-linux-gnu
...
[L...]   powerpc64-unknown-linux-gnu
[L...]   powerpc-e300c3-linux-gnu
...
[L..X]   riscv32-unknown-elf
[L..X]   riscv64-unknown-linux-gnu
...
[L...]   x86_64-unknown-linux-gnu
[L...]   x86_64-w64-mingw32,x86_64-pc-linux-gnu
...
```

Using aarch64:

```bash
$ ct-ng aarch64-unknown-linux-gnu
$ ct-ng menuconfig
```

The menuconfig configure:

```bash
Paths and misc options  --->
    ($HOME/crosstools/dl) Local tarballs directory
    ($HOME/crosstools/out/build) Working directory
    ($HOME/crosstools/_install) Prefix directory

Operating System  --->
    Target OS (linux)  --->
    Version of linux (5.4.276)  --->

C compiler  --->
    Compiler (gcc)  --->
    Version of gcc (12.3.0)  --->

Debug facilities  --->
    [*] gdb  --->
        Version of gdb (12.1)  --->
```

build (include build.log):

```bash
$ ct-ng build
```

It will download src file and build, install. The download file list:

```bash
$ ls $HOME/crosstools/dl
binutils-2.42.tar.xz
expat-2.5.0.tar.xz
gcc-12.3.0.tar.xz
gdb-12.1.tar.xz
gettext-0.22.5.tar.xz
glibc-2.39.tar.xz
gmp-6.2.1.tar.xz
isl-0.26.tar.xz
libiconv-1.16.tar.gz
linux-5.4.276.tar.xz
mpc-1.3.1.tar.gz
mpfr-4.2.1.tar.xz
ncurses-6.4.tar.gz
zlib-1.3.tar.xz
zstd-1.5.6.tar.gz
```

The build file list:

```bash
$ cd $HOME/crosstools/
$ tree ./out/build/ -L 2
./out/build/
├── aarch64-unknown-linux-gnu
│   ├── build
│   ├── buildtools
│   └── src
├── src
│   ├── binutils-2.42
│   ├── expat-2.5.0
│   ├── gcc-12.3.0
│   ├── gdb-12.1
│   ├── gettext-0.22.5
│   ├── glibc-2.39
│   ├── gmp-6.2.1
│   ├── isl-0.26
│   ├── libiconv-1.16
│   ├── linux-5.4.276
│   ├── mpc-1.3.1
│   ├── mpfr-4.2.1
│   ├── ncurses-6.4
│   ├── zlib-1.3
│   └── zstd-1.5.6
├── tarballs
│   ├── binutils-2.42.tar.xz -> $HOME/crosstools/dl/binutils-2.42.tar.xz
│   ├── expat-2.5.0.tar.xz -> $HOME/crosstools/dl/expat-2.5.0.tar.xz
│   ├── gcc-12.3.0.tar.xz -> $HOME/crosstools/dl/gcc-12.3.0.tar.xz
│   ├── gdb-12.1.tar.xz -> $HOME/crosstools/dl/gdb-12.1.tar.xz
│   ├── gettext-0.22.5.tar.xz -> $HOME/crosstools/dl/gettext-0.22.5.tar.xz
│   ├── glibc-2.39.tar.xz -> $HOME/crosstools/dl/glibc-2.39.tar.xz
│   ├── gmp-6.2.1.tar.xz -> $HOME/crosstools/dl/gmp-6.2.1.tar.xz
│   ├── isl-0.26.tar.xz -> $HOME/crosstools/dl/isl-0.26.tar.xz
│   ├── libiconv-1.16.tar.gz -> $HOME/crosstools/dl/libiconv-1.16.tar.gz
│   ├── linux-5.4.276.tar.xz -> $HOME/crosstools/dl/linux-5.4.276.tar.xz
│   ├── mpc-1.3.1.tar.gz -> $HOME/crosstools/dl/mpc-1.3.1.tar.gz
│   ├── mpfr-4.2.1.tar.xz -> $HOME/crosstools/dl/mpfr-4.2.1.tar.xz
│   ├── ncurses-6.4.tar.gz -> $HOME/crosstools/dl/ncurses-6.4.tar.gz
│   ├── zlib-1.3.tar.xz -> $HOME/crosstools/dl/zlib-1.3.tar.xz
│   └── zstd-1.5.6.tar.gz -> $HOME/crosstools/dl/zstd-1.5.6.tar.gz
└── tools
    └── bin

23 directories, 15 files
```

The install file list:

```bash
$ cd $HOME/crosstools/
$ tree _install/ -L 2
_install/
├── aarch64-unknown-linux-gnu
│   ├── bin
│   ├── debug-root
│   ├── include
│   ├── lib
│   ├── lib64
│   └── sysroot
├── bin
│   ├── aarch64-unknown-linux-gnu-addr2line
│   ├── aarch64-unknown-linux-gnu-ar
│   ├── aarch64-unknown-linux-gnu-as
│   ├── aarch64-unknown-linux-gnu-c++
│   ├── aarch64-unknown-linux-gnu-cc -> aarch64-unknown-linux-gnu-gcc
│   ├── aarch64-unknown-linux-gnu-c++filt
│   ├── aarch64-unknown-linux-gnu-cpp
│   ├── aarch64-unknown-linux-gnu-ct-ng.config
│   ├── aarch64-unknown-linux-gnu-dwp
│   ├── aarch64-unknown-linux-gnu-elfedit
│   ├── aarch64-unknown-linux-gnu-g++
│   ├── aarch64-unknown-linux-gnu-gcc
│   ├── aarch64-unknown-linux-gnu-gcc-12.3.0
│   ├── aarch64-unknown-linux-gnu-gcc-ar
│   ├── aarch64-unknown-linux-gnu-gcc-nm
│   ├── aarch64-unknown-linux-gnu-gcc-ranlib
│   ├── aarch64-unknown-linux-gnu-gcov
│   ├── aarch64-unknown-linux-gnu-gcov-dump
│   ├── aarch64-unknown-linux-gnu-gcov-tool
│   ├── aarch64-unknown-linux-gnu-gdb
│   ├── aarch64-unknown-linux-gnu-gdb-add-index
│   ├── aarch64-unknown-linux-gnu-gp-archive
│   ├── aarch64-unknown-linux-gnu-gp-collect-app
│   ├── aarch64-unknown-linux-gnu-gp-display-html
│   ├── aarch64-unknown-linux-gnu-gp-display-src
│   ├── aarch64-unknown-linux-gnu-gp-display-text
│   ├── aarch64-unknown-linux-gnu-gprof
│   ├── aarch64-unknown-linux-gnu-gprofng
│   ├── aarch64-unknown-linux-gnu-ld
│   ├── aarch64-unknown-linux-gnu-ld.bfd
│   ├── aarch64-unknown-linux-gnu-ldd
│   ├── aarch64-unknown-linux-gnu-ld.gold
│   ├── aarch64-unknown-linux-gnu-lto-dump
│   ├── aarch64-unknown-linux-gnu-nm
│   ├── aarch64-unknown-linux-gnu-objcopy
│   ├── aarch64-unknown-linux-gnu-objdump
│   ├── aarch64-unknown-linux-gnu-populate
│   ├── aarch64-unknown-linux-gnu-ranlib
│   ├── aarch64-unknown-linux-gnu-readelf
│   ├── aarch64-unknown-linux-gnu-size
│   ├── aarch64-unknown-linux-gnu-strings
│   └── aarch64-unknown-linux-gnu-strip
├── build.log.bz2
├── etc
│   └── gprofng.rc
├── include
│   ├── collectorAPI.h
│   ├── libcollector.h
│   └── libfcollector.h
├── lib
│   ├── bfd-plugins
│   ├── gcc
│   ├── gprofng
│   ├── libcc1.so -> libcc1.so.0.0.0
│   ├── libcc1.so.0 -> libcc1.so.0.0.0
│   ├── libcc1.so.0.0.0
│   └── libgprofng.a
├── libexec
│   └── gcc
└── share
    ├── gcc-12.3.0
    ├── gdb
    └── licenses

20 directories, 51 files
```

The origin crosstool-ng:

```bash
$ cd $HOME/crosstools/
$ ls crosstools-ng/
bash-completion  ct-ng.in           licenses.d   README.md
bootstrap        debian             m4           samples
config           docs               maintainer   scripts
configure.ac     issue_template.md  Makefile.am  testing
contrib          kconfig            packages     TODO
COPYING          LICENSE            paths.sh.in
```

The build completed crosstool-ng:

```bash
$ cd $HOME/crosstools/
$ ls crosstools-ng/
aclocal.m4       configure          kconfig      paths.sh.in
autom4te.cache   configure.ac       LICENSE      README.md
bash-completion  contrib            licenses.d   samples
bootstrap        COPYING            m4           scripts
build.log        ct-ng              maintainer   stamp-h1
config           ct-ng.in           Makefile     testing
config.h         debian             Makefile.am  TODO
config.h.in      docs               Makefile.in  verbatim-data.mk
config.log       include            packages
config.status    issue_template.md  paths.sh
```


# 4. Validation

Check version:

```bash
$ cd $HOME/crosstools/_install/bin/
$ ./aarch64-unknown-linux-gnu-gcc -v
Using built-in specs.
COLLECT_GCC=./aarch64-unknown-linux-gnu-gcc
COLLECT_LTO_WRAPPER=$HOME/crosstools/_install/libexec/gcc/aarch64-unknown-linux-gnu/12.3.0/lto-wrapper
Target: aarch64-unknown-linux-gnu
Configured with: ...
Thread model: posix
Supported LTO compression algorithms: zlib zstd
gcc version 12.3.0 (crosstool-NG 1.26.0.85_06fad54)
```

Generate hello.c:

```bash
#include <stdio.h>

int main(void)
{
    printf("hello world.\n");

    return 0;
}
```

Build hello.c

```bash
$ aarch64-unknown-linux-gnu-gcc hello.c -o hello --static

$ du -sh hello
2.7M    hello

$ file hello
hello: ELF 64-bit LSB executable, ARM aarch64, version 1 (GNU/Linux), statically linked, for GNU/Linux 5.4.255, with debug_info, not stripped

$ aarch64-unknown-linux-gnu-strip hello
$ du -sh hello
520K    hello
```

Running:

```bash
$ qemu-aarch64-static hello
hello world.
```
