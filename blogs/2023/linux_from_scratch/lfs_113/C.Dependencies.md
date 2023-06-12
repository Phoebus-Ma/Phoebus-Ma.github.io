
## C.Dependencies
--------

LFS 中构建的每个软件包都依赖于一个或多个其他软件包，才能正确地构建和安装。某些软件包甚至存在循环依赖，即第一个软件包依赖于第二个软件包，而第二个软件包反过来又依赖第一个。由于这些依赖关系的存在，在 LFS 中构建软件包的顺序非常关键。本页面的目的就是记录 LFS 中每个软件包构建时的依赖关系。

对于我们构建的每个软件包，我们都列出了三到五种依赖关系。第一种列出了编译和安装该软件包需要的其他软件包。第二种列出了在运行该软件包提供的程序或库时必要的软件包。第三种列出了不属于第一种情况，但在运行该软件包测试套件时需要的软件包。第四种列出了在构建和安装前，需要该软件包已经构建并安装到最终位置的其他软件包。多数情况下，这是因为它们会在脚本中硬编码指向二进制程序的路径。如果不按照特定顺序构建，则最终的系统中某个脚本可能包含路径 /tools/bin/[二进制程序]，这显然是我们不希望的。

最后一种依赖关系是 LFS 中没有提到的可选软件包，但它们对用户可能很有用。这些软件包本身可能还有必要或可选的依赖关系。对于这些依赖关系，推荐的方法是在完成 LFS 手册后，安装可选依赖项，再重新构建相关的 LFS 软件包。BLFS 提到了几个软件包的重新安装方法。


Acl
--------
- 安装依赖于:                 Attr, Bash, Binutils, Coreutils, GCC, Gettext, Grep, M4, Make, Perl, Sed, 以及 Texinfo
- 运行时依赖于:               Attr 和 Glibc
- 测试依赖于:                 Automake, Diffutils, Findutils, 以及 Libtool
- 必须在下列软件包之前安装:    Coreutils, Sed, Tar, 以及 Vim
- 可选依赖项:                 无


Attr
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Gettext, Glibc, Grep, M4, Make, Perl, Sed, 以及 Texinfo
- 运行时依赖于:               Glibc
- 测试依赖于:                 Automake, Diffutils, Findutils, 以及 Libtool
- 必须在下列软件包之前安装:    Acl 和 Libcap
- 可选依赖项:                 无


Autoconf
--------
- 安装依赖于:                 Bash, Coreutils, Grep, M4, Make, Perl, Sed, 以及 Texinfo
- 运行时依赖于:               Bash, Coreutils, Grep, M4, Make, Sed, 以及 Texinfo
- 测试依赖于:                 Automake, Diffutils, Findutils, GCC, 以及 Libtool
- 必须在下列软件包之前安装:    Automake
- 可选依赖项:                 Emacs


Automake
--------
- 安装依赖于:                 Autoconf, Bash, Coreutils, Gettext, Grep, M4, Make, Perl, Sed, 以及 Texinfo
- 运行时依赖于:               Bash, Coreutils, Grep, M4, Sed, 以及 Texinfo
- 测试依赖于:                 Binutils, Bison, Bzip2, DejaGNU, Diffutils, Expect, Findutils, Flex, GCC, Gettext, Gzip, Libtool, 以及 Tar
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Bash
--------
- 安装依赖于:                 Bash, Binutils, Bison, Coreutils, Diffutils, Gawk, GCC, Glibc, Grep, Make, Ncurses, Patch, Readline, Sed, 以及 Texinfo
- 运行时依赖于:               Glibc, Ncurses, 以及 Readline
- 测试依赖于:                 Expect 和 Shadow
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 Xorg


Bc
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Glibc, Grep, Make, 以及 Readline
- 运行时依赖于:               Glibc, Ncurses, 以及 Readline
- 测试依赖于:                 Gawk
- 必须在下列软件包之前安装:    Linux
- 可选依赖项:                 无


Binutils
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, File, Flex, Gawk, GCC, Glibc, Grep, Make, Perl, Sed, Texinfo, 以及 Zlib
- 运行时依赖于:               Glibc 和 Zlib
- 测试依赖于:                 DejaGNU 和 Expect
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 Elfutils 和 Jansson


Bison
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Gettext, Glibc, Grep, M4, Make, Perl, 以及 Sed
- 运行时依赖于:               Glibc
- 测试依赖于:                 Diffutils, Findutils, 以及 Flex
- 必须在下列软件包之前安装:    Kbd 和 Tar
- 可选依赖项:                 Doxygen


Bzip2
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, GCC, Glibc, Make, 以及 Patch
- 运行时依赖于:               Glibc
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    File
- 可选依赖项:                 无


Check
--------
- 安装依赖于:                 Gawk, GCC, Grep, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Bash 和 Gawk
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Coreutils
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Gettext, Glibc, GMP, Grep, Libcap, Make, OpenSSL, Patch, Perl, Sed, 以及 Texinfo
- 运行时依赖于:               Glibc
- 测试依赖于:                 Diffutils, E2fsprogs, Findutils, Shadow, 以及 Util-linux
- 必须在下列软件包之前安装:    Bash, Diffutils, Eudev, Findutils, 以及 Man-DB
- 可选依赖项:                 Expect.pm 和 IO::Tty


DejaGNU
--------
- 安装依赖于:                 Bash, Coreutils, Diffutils, Expect, GCC, Grep, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Expect 和 Bash
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Diffutils
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Gawk, GCC, Gettext, Glibc, Grep, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Glibc
- 测试依赖于:                 Perl
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


E2fsprogs
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, Gawk, GCC, Glibc, Grep, Gzip, Make, Sed, Texinfo, 以及 Util-linux
- 运行时依赖于:               Glibc 和 Util-linux
- 测试依赖于:                 Procps-ng 和 Psmisc
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Eudev
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Gawk, GCC, Glibc, Grep, Gperf, Make, Sed, 以及 Util-linux
- 运行时依赖于:               Glibc, Kmod, Xz, Util-linux, 以及 Zlib
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Expat
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Gawk, GCC, Glibc, Grep, Make, 以及 Sed
- 运行时依赖于:               Glibc
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    Python 和 XML::Parser
- 可选依赖项:                 无


Expect
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, GCC, Glibc, Grep, Make, Patch, Sed, 以及 Tcl
- 运行时依赖于:               Glibc 和 Tcl
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 Tk


File
--------
- 安装依赖于:                 Bash, Binutils, Bzip2, Coreutils, Diffutils, Gawk, GCC, Glibc, Grep, Make, Sed, Xz, 以及 Zlib
- 运行时依赖于:               Glibc, Bzip2, Xz, 以及 Zlib
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 libseccomp


Findutils
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Gettext, Glibc, Grep, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Bash 和 Glibc
- 测试依赖于:                 DejaGNU, Diffutils, 以及 Expect
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Flex
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Gettext, Glibc, Grep, M4, Make, Patch, Sed, 以及 Texinfo
- 运行时依赖于:               Bash, Glibc, 以及 M4
- 测试依赖于:                 Bison 和 Gawk
- 必须在下列软件包之前安装:    Binutils, IProute2, Kbd, Kmod, 以及 Man-DB
- 可选依赖项:                 无


Gawk
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Gettext, Glibc, GMP, Grep, Make, MPFR, Patch, Readline, Sed, 以及 Texinfo
- 运行时依赖于:               Bash, Glibc, 以及 Mpfr
- 测试依赖于:                 Diffutils
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 libsigsegv


GCC
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, Findutils, Gawk, GCC, Gettext, Glibc, GMP, Grep, M4, Make, MPC, MPFR, Patch, Perl, Sed, Tar, Texinfo, 以及 Zstd
- 运行时依赖于:               Bash, Binutils, Glibc, Mpc, 以及 Python
- 测试依赖于:                 DejaGNU, Expect, 以及 Shadow
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 GNAT 和 ISL


GDBM
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, GCC, Grep, Make, 以及 Sed
- 运行时依赖于:               Bash, Glibc, 以及 Readline
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Gettext
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Gawk, GCC, Glibc, Grep, Make, Ncurses, Sed, 以及 Texinfo
- 运行时依赖于:               Acl, Bash, Gcc, 以及 Glibc
- 测试依赖于:                 Diffutils, Perl, 以及 Tcl
- 必须在下列软件包之前安装:    Automake 和 Bison
- 可选依赖项:                 无


Glibc
--------
- 安装依赖于:                 Bash, Binutils, Bison, Coreutils, Diffutils, Gawk, GCC, Gettext, Grep, Gzip, Linux API Headers, Make, Perl, Python, Sed, 以及 Texinfo
- 运行时依赖于:               无
- 测试依赖于:                 File
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


GMP
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, Gawk, GCC, Glibc, Grep, M4, Make, Sed, 以及 Texinfo
- 运行时依赖于:               GCC 和 Glibc
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    MPFR 和 GCC
- 可选依赖项:                 无


Gperf
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Glibc, 以及 Make
- 运行时依赖于:               GCC 和 Glibc
- 测试依赖于:                 Diffutils 和 Expect
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Grep
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, GCC, Gettext, Glibc, Grep, Make, Patch, Sed, 以及 Texinfo
- 运行时依赖于:               Glibc
- 测试依赖于:                 Gawk
- 必须在下列软件包之前安装:    Man-DB
- 可选依赖项:                 PCRE2 和 libsigsegv


Groff
--------
- 安装依赖于:                 Bash, Binutils, Bison, Coreutils, Gawk, GCC, Glibc, Grep, Make, Patch, Sed, 以及 Texinfo
- 运行时依赖于:               GCC, Glibc, 以及 Perl
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    Man-DB 和 Perl
- 可选依赖项:                 ghostscript 和 Uchardet


GRUB
--------
- 安装依赖于:                 Bash, Binutils, Bison, Coreutils, Diffutils, GCC, Gettext, Glibc, Grep, Make, Ncurses, Sed, Texinfo, 以及 Xz
- 运行时依赖于:               Bash, GCC, Gettext, Glibc, Xz, 以及 Sed.
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Gzip
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Glibc, Grep, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Bash 和 Glibc
- 测试依赖于:                 Diffutils 和 Less
- 必须在下列软件包之前安装:    Man-DB
- 可选依赖项:                 无


Iana-Etc
--------
- 安装依赖于:                 Coreutils
- 运行时依赖于:               无
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    Perl
- 可选依赖项:                 无


Inetutils
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Glibc, Grep, Make, Ncurses, Patch, Sed, Texinfo, 以及 Zlib
- 运行时依赖于:               GCC, Glibc, Ncurses, 以及 Readline
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    Tar
- 可选依赖项:                 无


Intltool
--------
- 安装依赖于:                 Bash, Gawk, Glibc, Make, Perl, Sed, 以及 XML::Parser
- 运行时依赖于:               Autoconf, Automake, Bash, Glibc, Grep, Perl, 以及 Sed
- 测试依赖于:                 Perl
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


IProute2
--------
- 安装依赖于:                 Bash, Bison, Coreutils, Flex, GCC, Glibc, Make, Libcap, Libelf, Linux API 头文件, 以及 Zlib
- 运行时依赖于:               Bash, Coreutils, Glibc, Libcap, Libelf, 以及 Zlib
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 Berkeley DB 和 iptables


Kbd
--------
- 安装依赖于:                 Bash, Binutils, Bison, Check, Coreutils, Flex, GCC, Gettext, Glibc, Gzip, Make, Patch, 以及 Sed
- 运行时依赖于:               Bash, Coreutils, 以及 Glibc
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Kmod
--------
- 安装依赖于:                 Bash, Binutils, Bison, Coreutils, Flex, GCC, Gettext, Glibc, Gzip, Make, OpenSSL, Pkg-config, Sed, Xz, 以及 Zlib
- 运行时依赖于:               Glibc, Xz, 以及 Zlib
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    Eudev
- 可选依赖项:                 无


Less
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, GCC, Glibc, Grep, Make, Ncurses, 以及 Sed
- 运行时依赖于:               Glibc 和 Ncurses
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    Gzip
- 可选依赖项:                 PCRE2 或 PCRE


Libcap
--------
- 安装依赖于:                 Attr, Bash, Binutils, Coreutils, GCC, Glibc, Perl, Make, 以及 Sed
- 运行时依赖于:               Glibc
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    IProute2 和 Shadow
- 可选依赖项:                 Linux-PAM


Libelf
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Glibc, 以及 Make
- 运行时依赖于:               Glibc 和 Zlib
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    IProute2 和 Linux
- 可选依赖项:                 无


Libffi
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Glibc, Make, 以及 Sed
- 运行时依赖于:               Glibc
- 测试依赖于:                 DejaGnu
- 必须在下列软件包之前安装:    Python
- 可选依赖项:                 无


Libpipeline
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, Gawk, GCC, Glibc, Grep, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Glibc
- 测试依赖于:                 Check
- 必须在下列软件包之前安装:    Man-DB
- 可选依赖项:                 无


Libtool
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, Gawk, GCC, Glibc, Grep, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Bash, Binutils, Coreutils, GCC, Glibc, Grep, Make, 以及 SedAutoconf, Automake, Bash, Binutils, Coreutils, File, GCC, Glibc, Grep, Make, 以及 Sed
- 测试依赖于:                 Autoconf, Automake, 以及 Findutils
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Linux
--------
- 安装依赖于:                 Bash, Bc, Binutils, Coreutils, Diffutils, Findutils, GCC, Glibc, Grep, Gzip, Kmod, Libelf, Make, Ncurses, OpenSSL, Perl, 以及 Sed
- 运行时依赖于:               无
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 cpio 和 LLVM (包括 Clang)


Linux API 头文件
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Findutils, GCC, Glibc, Grep, Gzip, Make, Perl, 以及 Sed
- 运行时依赖于:               无
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


M4
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Glibc, Grep, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Bash 和 Glibc
- 测试依赖于:                 Diffutils
- 必须在下列软件包之前安装:    Autoconf 和 Bison
- 可选依赖项:                 libsigsegv


Make
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Gettext, Glibc, Grep, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Glibc
- 测试依赖于:                 Perl 和 Procps-ng
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 Guile


Man-DB
--------
- 安装依赖于:                 Bash, Binutils, Bzip2, Coreutils, Flex, GCC, GDBM, Gettext, Glibc, Grep, Groff, Gzip, Less, Libpipeline, Make, Sed, 以及 Xz
- 运行时依赖于:               Bash, GDBM, Groff, Glibc, Gzip, Less, Libpipeline, 以及 Zlib
- 测试依赖于:                 Util-linux
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 libseccomp 和 po4a


Man-Pages
--------
- 安装依赖于:                 Bash, Coreutils, 以及 Make
- 运行时依赖于:               无
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Meson
--------
- 安装依赖于:                 Ninja 和 Python
- 运行时依赖于:               Python
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


MPC
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, Gawk, GCC, Glibc, Grep, GMP, Make, MPFR, Sed, 以及 Texinfo
- 运行时依赖于:               Glibc, GMP, 以及 MPFR
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    GCC
- 可选依赖项:                 无


MPFR
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, Gawk, GCC, Glibc, Grep, GMP, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Glibc 和 GMP
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    Gawk 和 GCC
- 可选依赖项:                 无


Ncurses
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, Gawk, GCC, Glibc, Grep, Make, Patch, 以及 Sed
- 运行时依赖于:               Glibc
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    Bash, GRUB, Inetutils, Less, Procps-ng, Psmisc, Readline, Texinfo, Util-linux, 以及 Vim
- 可选依赖项:                 无


Ninja
--------
- 安装依赖于:                 Binutils, Coreutils, GCC, 以及 Python
- 运行时依赖于:               GCC 和 Glibc
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    Meson
- 可选依赖项:                 Asciidoc, Doxygen, Emacs, 以及 re2c


OpenSSL
--------
- 安装依赖于:                 Binutils, Coreutils, Gcc, Make, 以及 Perl
- 运行时依赖于:               Glibc 和 Perl
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    Coreutils, Kmod, 以及 Linux
- 可选依赖项:                 无


Patch
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Glibc, Grep, Make, 以及 Sed
- 运行时依赖于:               Glibc
- 测试依赖于:                 Diffutils
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 Ed


Perl
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Gawk, GCC, GDBM, Glibc, Grep, Groff, Make, Sed, 以及 Zlib
- 运行时依赖于:               GDBM 和 Glibc
- 测试依赖于:                 Iana-Etc, Less, 以及 Procps-ng
- 必须在下列软件包之前安装:    Autoconf
- 可选依赖项:                 Berkeley DB


Pkg-config
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Gawk, GCC, Glibc, Grep, Make, 以及 Sed
- 运行时依赖于:               Glibc
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    Kmod
- 可选依赖项:                 Glib2


Procps-ng
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Glibc, Make, 以及 Ncurses
- 运行时依赖于:               Glibc
- 测试依赖于:                 DejaGNU
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Psmisc
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Gettext, Glibc, Grep, Make, Ncurses, 以及 Sed
- 运行时依赖于:               Glibc 和 Ncurses
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Python
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Expat, GCC, Gdbm, Gettext, Glibc, Grep, Libffi, Make, Ncurses, OpenSSL, Sed, 以及 Util-linux
- 运行时依赖于:               Bzip2, Expat, Gdbm, Glibc, Libffi, Ncurses, OpenSSL, 以及 Zlib
- 测试依赖于:                 GDB 和 Valgrind
- 必须在下列软件包之前安装:    Ninja
- 可选依赖项:                 Berkeley DB, libnsl, SQLite, 以及 Tk


Readline
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Gawk, GCC, Glibc, Grep, Make, Ncurses, Patch, Sed, 以及 Texinfo
- 运行时依赖于:               Glibc 和 Ncurses
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    Bash, Bc, 以及 Gawk
- 可选依赖项:                 无


Sed
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Gettext, Glibc, Grep, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Acl, Attr, 以及 Glibc
- 测试依赖于:                 Diffutils 和 Gawk
- 必须在下列软件包之前安装:    E2fsprogs, File, Libtool, 以及 Shadow
- 可选依赖项:                 无


Shadow
--------
- 安装依赖于:                 Acl, Attr, Bash, Binutils, Coreutils, Diffutils, Findutils, Gawk, GCC, Gettext, Glibc, Grep, Libcap, Make, 以及 Sed
- 运行时依赖于:               Glibc
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    Coreutils
- 可选依赖项:                 CrackLib 和 Linux-PAM


Sysklogd
--------
- 安装依赖于:                 Binutils, Coreutils, GCC, Glibc, Make, 以及 Patch
- 运行时依赖于:               Glibc
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Sysvinit
--------
- 安装依赖于:                 Binutils, Coreutils, GCC, Glibc, Make, 以及 Sed
- 运行时依赖于:               Glibc
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Tar
--------
- 安装依赖于:                 Acl, Attr, Bash, Binutils, Bison, Coreutils, GCC, Gettext, Glibc, Grep, Inetutils, Make, Sed, 以及 Texinfo
- 运行时依赖于:               Acl, Attr, Bzip2, Glibc, Gzip, 以及 Xz
- 测试依赖于:                 Autoconf, Diffutils, Findutils, Gawk, 以及 Gzip
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Tcl
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, GCC, Glibc, Grep, Make, 以及 Sed
- 运行时依赖于:               Glibc 和 Zlib
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Texinfo
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Gettext, Glibc, Grep, Make, Ncurses, Patch, 以及 Sed
- 运行时依赖于:               Glibc 和 Ncurses
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


Util-linux
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, Eudev, Findutils, Gawk, GCC, Gettext, Glibc, Grep, Make, Ncurses, Sed, 以及 Zlib
- 运行时依赖于:               Glibc, Ncurses, Readline, 以及 Zlib
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 Libcap-NG, Linux-PAM, 以及 smartmontools


Vim
--------
- 安装依赖于:                 Acl, Attr, Bash, Binutils, Coreutils, Diffutils, GCC, Glibc, Grep, Make, Ncurses, 以及 Sed
- 运行时依赖于:               Acl, Attr, Glibc, Python, Ncurses, 以及 Tcl
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 Xorg, GTK+2, LessTif, Ruby, 以及 GPM


wheel
--------
- 安装依赖于:                 Python
- 运行时依赖于:               Python
- 测试依赖于:                 没有可用的测试套件
- 必须在下列软件包之前安装:    无
- 可选依赖项:                 无


XML::Parser
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Expat, GCC, Glibc, Make, 以及 Perl
- 运行时依赖于:               Expat, Glibc, 以及 Perl
- 测试依赖于:                 Perl
- 必须在下列软件包之前安装:    Intltool
- 可选依赖项:                 无


Xz
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, Diffutils, GCC, Glibc, 以及 Make.
- 运行时依赖于:               Glibc
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    Eudev, File, GRUB, Kmod, 以及 Man-DB
- 可选依赖项:                 无


Zlib
--------
- 安装依赖于:                 Bash, Binutils, Coreutils, GCC, Glibc, Make, 以及 Sed
- 运行时依赖于:               Glibc
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    File, Kmod, Perl, 以及 Util-linux
- 可选依赖项:                 无


Zstd
--------
- 安装依赖于:                 Binutils, Coreutils, GCC, Glibc, Gzip, Make, 以及 Xz
- 运行时依赖于:               Glibc
- 测试依赖于:                 无
- 必须在下列软件包之前安装:    GCC
- 可选依赖项:                 LZ4
