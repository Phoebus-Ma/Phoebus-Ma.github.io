
Buildroot用户手册
=========================

**目录**

[I. 开始](#part-i-getting-started)

[1. 关于Buildroot](#chapter-1-about-buildroot)

[2. 系统需求](#chapter-2-system-requirements)

[2.1. 必要的包](#21-mandatory-packages)

[2.2. 可选的包](#22-optional-packages)

[3. 获取Buildroot](#chapter-3-getting-buildroot)

[4. Buildroot快速开始](#chapter-4-buildroot-quick-start)

[5. 社区资源](#chapter-5-community-resources)

[II. 用户指南](#part-ii-user-guide)

[6. Buildroot配置](#chapter-6-buildroot-configuration)

[6.1. 交叉编译工具链](#61-cross-compilation-toolchain)

[6.2. /dev管理](#62-dev-management)

[6.3. 初始化系统](#63-init-system)

[7. 其他组件的配置](#chapter-7-configuration-of-other-components)

[8. 一般Buildroot用法](#chapter-8-general-buildroot-usage)

[8.1. make tips](#81-make-tips)

[8.2. 理解什么时候需要完全重建](#82-understanding-when-a-full-rebuild-is-necessary)

[8.3. 理解如何重建包](#83-understanding-how-to-rebuild-packages)

[8.4. 离线构建](#84-offline-builds)

[8.5. 树外构建](#85-building-out-of-tree)

[8.6. 环境变量](#86-environment-variables)

[8.7. 高效地处理文件系统映像](#87-dealing-efficiently-with-filesystem-images)

[8.8. 关于包的详细信息](#88-details-about-packages)

[8.9. 绘制包之间的依赖关系图](#89-graphing-the-dependencies-between-packages)

[8.10. 绘制构建持续时间](#810-graphing-the-build-duration)

[8.11. 绘制包对文件系统存储的占用](#811-graphing-the-filesystem-size-contribution-of-packages)

[8.12. 顶层并行构建](#812-top-level-parallel-build)

[8.13. 高级用法](#813-advanced-usage)

[9. 具体项目的定制](#chapter-9-project-specific-customization)

[9.1. 推荐的目录结构](#91-recommended-directory-structure)

[9.2. 在Buildroot之外保持自定义](#92-keeping-customizations-outside-of-buildroot)

[9.3. 存储Buildroot配置](#93-storing-the-buildroot-configuration)

[9.4. 存储其他组件的配置信息](#94-storing-the-configuration-of-other-components)

[9.5. 自定义生成的目标文件系统](#95-customizing-the-generated-target-filesystem)

[9.6. 添加自定义用户账户](#96-adding-custom-user-accounts)

[9.7. 创建映像后的自定义](#97-customization-after-the-images-have-been-created)

[9.8. 添加特定于项目的补丁](#98-adding-project-specific-patches)

[9.9. 添加特定于项目的包](#99-adding-project-specific-packages)

[9.10. 快速指南存储您的项目特定的自定义](#910-quick-guide-to-storing-your-project-specific-customizations)

[10. 集成的话题](#chapter-10-integration-topics)

[10.1. Systemd](#101-systemd)

[10.2. 在Buildroot中使用SELinux](#102-using-selinux-in-buildroot)

[11. 常见问题及故障处理](#chapter-11-frequently-asked-questions--troubleshooting)

[11.1. 启动网络后boot挂起…](#111-the-boot-hangs-after-starting-network)

[11.2. 为什么目标上没有编译器?](#112-why-is-there-no-compiler-on-the-target)

[11.3. 为什么target上没有开发文件?](#113-why-are-there-no-development-files-on-the-target)

[11.4. 为什么没有目标的资料?](#114-why-is-there-no-documentation-on-the-target)

[11.5. 为什么有些包在Buildroot配置菜单中不可见?](#115-why-are-some-packages-not-visible-in-the-buildroot-config-menu)

[11.6. 为什么不使用目标目录作为chroot目录呢?](#116-why-not-use-the-target-directory-as-a-chroot-directory)

[11.7. 为什么Buildroot不能生成二进制包(.deb， .ipkg…)](#117-why-doesnt-buildroot-generate-binary-packages-deb-ipkg)

[11.8. 如何加快构建过程?](#118-how-to-speed-up-the-build-process)

[12. 已知的问题](#chapter-12-known-issues)

[13. 法律公告及licensing](#chapter-13-legal-notice-and-licensing)

[13.1. 遵循开源许可协议](#131-complying-with-open-source-licenses)

[13.2. 符合Buildroot许可证](#132-complying-with-the-buildroot-license)

[14. Beyond Buildroot](#chapter-14-beyond-buildroot)

[14.1. 引导生成的映像](#141-boot-the-generated-images)

[14.2. Chroot](#142-chroot)

[III. 开发人员指南](#part-iii-developer-guide)

[15. Buildroot是如何工作的](#chapter-15-how-buildroot-works)

[16. 编码风格](#chapter-16-coding-style)

[16.1. `Config.in`文件](#161-configin-file)

[16.2. `.mk`文件](#162-the-mk-file)

[16.3. `genimage.cfg`文件](#163-the-genimagecfg-file)

[16.4. 文档](#164-the-documentation)

[16.5. 支持脚本](#165-support-scripts)

[17. 添加对特定开发板的支持](#chapter-17-adding-support-for-a-particular-board)

[18. 向Buildroot添加新包](#chapter-18-adding-new-packages-to-buildroot)

[18.1. 包目录](#181-package-directory)

[18.2. 配置文件](#182-config-files)

[18.3. `.mk`文件](#183-the-mk-file)

[18.4. `.hash`文件](#184-the-hash-file)

[18.5. `SNNfoo`启动脚本](#185-the-snnfoo-start-script)

[18.6. 具有特定构建系统的包的基础架构](#186-infrastructure-for-packages-with-specific-build-systems)

[18.7. 基于autotools的包的基础架构](#187-infrastructure-for-autotools-based-packages)

[18.8. 基于cmake的包的基础架构](#188-infrastructure-for-cmake-based-packages)

[18.9. Python包的基础架构](#189-infrastructure-for-python-packages)

[18.10. 基于LuaRocks的软件包的基础架构](#1810-infrastructure-for-luarocks-based-packages)

[18.11. Perl/CPAN包的基础架构](#1811-infrastructure-for-perlcpan-packages)

[18.12. 虚拟包的基础架构](#1812-infrastructure-for-virtual-packages)

[18.13. 包的基础架构，使用kconfig配置文件](#1813-infrastructure-for-packages-using-kconfig-for-configuration-files)

[18.14. 基于rebar的包的基础架构](#1814-infrastructure-for-rebar-based-packages)

[18.15. 基于Waf的包的基础架构](#1815-infrastructure-for-waf-based-packages)

[18.16. 基于Meson的包的基础架构](#1816-infrastructure-for-meson-based-packages)

[18.17. 基于Cargo的包的基础架构](#1817-infrastructure-for-cargo-based-packages)

[18.18. Go包的基础架构](#1818-infrastructure-for-go-packages)

[18.19. 基于QMake的包的基础架构](#1819-infrastructure-for-qmake-based-packages)

[18.20. 构建内核模块的包的基础架构](#1820-infrastructure-for-packages-building-kernel-modules)

[18.21. asciidoc文档的基础架构](#1821-infrastructure-for-asciidoc-documents)

[18.22. 特定于Linux内核包的基础架构](#1822-infrastructure-specific-to-the-linux-kernel-package)

[18.23. 在不同的构建步骤中可用的钩子](#1823-hooks-available-in-the-various-build-steps)

[18.24. 与包的Gettext集成和交互](#1824-gettext-integration-and-interaction-with-packages)

[18.25. 提示和技巧](#1825-tips-and-tricks)

[18.26. 结论](#1826-conclusion)

[19. 为软件包打补丁](#chapter-19-patching-a-package)

[19.1. 提供补丁](#191-providing-patches)

[19.2. 如何应用补丁](#192-how-patches-are-applied)

[19.3. 软件包补丁的格式和许可](#193-format-and-licensing-of-the-package-patches)

[19.4. 额外的补丁文档](#194-additional-patch-documentation)

[20. 下载基础架构](#chapter-20-download-infrastructure)

[21. 调试Buildroot](#chapter-21-debugging-buildroot)

[22. 为Buildroot贡献](#chapter-22-contributing-to-buildroot)

[22.1. 重现、分析和修复bug](#221-reproducing-analyzing-and-fixing-bugs)

[22.2. 分析和修复自动构建失败](#222-analyzing-and-fixing-autobuild-failures)

[22.3. 检查和测试补丁](#223-reviewing-and-testing-patches)

[22.4. 处理待办事项列表中的待办事项](#224-work-on-items-from-the-todo-list)

[22.5. 提交补丁](#225-submitting-patches)

[22.6. 报告问题/bug或寻求帮助](#226-reporting-issuesbugs-or-getting-help)

[22.7. 使用运行时测试框架](#227-using-the-runtime-tests-framework)

[23. 开发者文件和获取开发者](#chapter-23-developers-file-and-get-developers)

[24. Release Engineering](#chapter-24-release-engineering)

[24.1. Releases](#241-releases)

[24.2. Development](#242-development)

[IV. 附录](#part-iv-appendix)

[25. Makedev语法文档](#chapter-25-makedev-syntax-documentation)

[26. Makeusers语法文档](#chapter-26-makeusers-syntax-documentation)

[26.1. 自动UID和GID的警告](#261-caveat-with-automatic-uids-and-gids)

[27. 从较旧的Buildroot版本迁移](#chapter-27-migrating-from-older-buildroot-versions)

[27.1. 一般方法](#271-general-approach)

[27.2. 迁移到2016.11](#272-migrating-to-201611)

[27.3. 迁移到2017.08](#273-migrating-to-201708)

**List of Examples**

18.1. [配置脚本: divine package](#idm3325)

18.2. [配置脚本: imagemagick package:](#idm3332)

Buildroot 2023.05.1手动生成于2023-07-17 15:07:36 UTC，由git版本7814dbce15生成

Buildroot手册是由Buildroot开发人员编写的。它在GNU通用公共许可证(version 2)下授权。请参阅Buildroot源代码中的[COPYING](http://git.buildroot.org/buildroot/tree/COPYING?id=7814dbce1580c51f62255105ae572da2c42f2eb4)文件以获得本许可证的全文。

Copyright © The Buildroot developers <buildroot@buildroot.org>



Part I. 开始
=======================

# Chapter 1. About Buildroot
--------------------------

Buildroot是一个工具，它使用交叉编译简化并自动化为嵌入式系统构建完整Linux系统的过程。

为了实现这一点，Buildroot能够为您的目标生成交叉编译工具链、根文件系统、Linux内核映像和引导加载程序。Buildroot可以独立地用于这些选项的任何组合(例如，您可以使用现有的交叉编译工具链，并仅使用Buildroot构建根文件系统)。

Buildroot主要用于处理嵌入式系统。嵌入式系统通常使用的处理器不是大家习惯于在自己的PC上使用的常规x86处理器。它们可以是PowerPC处理器、MIPS处理器、ARM处理器等。

Buildroot支持许多处理器及其变体。它还提供了几种现成的主板的默认配置。除此之外，许多第三方项目都是基于或在Buildroot之上开发他们的BSP[1]或SDK[2]。

[1] BSP: Board Support Package

[2] SDK: Software Development Kit



# Chapter 2. System requirements
------------------------------

Buildroot设计用于在Linux系统上运行。

虽然Buildroot本身将构建编译所需的大多数主机包，但预计已经在主机系统上安装了某些标准Linux实用程序。下面是必选包和可选包的概述(注意，包的名称可能因发行版而异)。

## 2.1. Mandatory packages
-----------------------

*   构建工具:
    
    *   `which`
    *   `sed`
    *   `make` (3.81版本或更高版本)
    *   `binutils`
    *   `build-essential` (仅限于基于Debian的系统)
    *   `diffutils`
    *   `gcc` (4.8或更高版本)
    *   `g++` (4.8或更高版本)
    *   `bash`
    *   `patch`
    *   `gzip`
    *   `bzip2`
    *   `perl` (版本5.8.7或更高版本)
    *   `tar`
    *   `cpio`
    *   `unzip`
    *   `rsync`
    *   `file` (一定在`/usr/bin/file`)
    *   `bc`
    *   `findutils`
    
*   源码获取工具:
    
    *   `wget`
    

## 2.2. Optional packages
----------------------

*   推荐的依赖关系:
    
    Buildroot中的一些功能或实用程序，如legal-info或图形生成工具，具有额外的依赖关系。尽管对于简单的构建来说它们不是强制的，但仍然强烈推荐使用它们:
    
    *   `python` (2.7或更高版本)
    
*   配置接口依赖:
    
    对于这些库，您需要安装运行时和开发数据，在许多发行版中，它们是单独打包的。开发包通常带有-dev或-devel后缀。
    
    *   `ncurses5` 来使用menuconfig接口
    *   `qt5` 来使用xconfig接口
    *   `glib2`, `gtk2` 和 `glade2` 用来使用gconfig接口
    
*   源码获取工具:
    
    在官方树中，大多数包源都是使用`wget`从ftp、http或https位置检索的。有些包只能通过版本控制系统使用。此外，Buildroot还可以通过其他工具下载源代码，例如`rsync`或`scp`(更多细节请参阅[第20章下载基础架构](#chapter-20-download-infrastructure))。如果你使用上述任何一种方法启用包，则需要在主机系统上安装相应的工具:
    
    *   `bazaar`
    *   `cvs`
    *   `git`
    *   `mercurial`
    *   `rsync`
    *   `scp`
    *   `sftp`
    *   `subversion`
    
*   如果需要为目标系统构建Java类路径，可以使用与Java相关的包:
    
    *   `javac`编译器
    *   `jar`工具
    
*   文档生成工具:
    
    *   `asciidoc`, 8.6.3或更高版本
    *   `w3m`
    *   `python`带有`argparse`模块 (自动出现在2.7+和3.2+版本中)
    *   `dblatex` (仅PDF手册需要)
    
*   图形生成工具:
    
    *   `graphviz` 用于使用graph-depends和<pkg>-graph-depends
    *   `python-matplotlib` 用于使用graph-build
    


# Chapter 3. Getting Buildroot
----------------------------

Buildroot每3个月发布一次，分别在2月、5月、8月和11月。发行版本号的格式为YYYY.MM，比如2013.02,2014.08。

发布压缩包可在 [http://buildroot.org/downloads/](http://buildroot.org/downloads/).

为了方便您，在Buildroot源代码树的`support/misc/Vagrantfile`中有一个[Vagrantfile](https://www.vagrantup.com/)，可以快速设置一个虚拟机，并使用所需的依赖项开始运行。

如果你想在Linux或Mac Os X上设置一个隔离的buildroot环境，将这行代码粘贴到你的终端:

```bash
curl -O https://buildroot.org/downloads/Vagrantfile; vagrant up
```

如果你使用的是Windows系统，将以下代码粘贴到powershell中:

```bash
(new-object System.Net.WebClient).DownloadFile(
"https://buildroot.org/downloads/Vagrantfile","Vagrantfile");
vagrant up
```

如果你想继续开发，可以使用每日快照或克隆Git存储库。有关更多详细信息，请参阅Buildroot网站的[下载页](http://buildroot.org/download)。



# Chapter 4. Buildroot quick start
--------------------------------

**重要**: 你可以并且应该**以一个普通用户的身份构建一切**。不需要拥有root权限就可以配置和使用Buildroot。通过以普通用户身份运行所有命令，可以保护系统免受包在编译和安装期间行为不佳的影响。

使用Buildroot的第一步是创建配置。Buildroot有一个很好的配置工具，类似于您可以在[Linux kernel](http://www.kernel.org/)或[BusyBox](http://www.busybox.net/)中找到的配置工具。

从buildroot目录运行

```bash
$ make menuconfig
```

对于原始的基于curses的配置器，或

```bash
$ make nconfig
```

对于新的基于curses的配置器，或者

```bash
$ make xconfig
```

对于基于qt的配置器，或者

```bash
$ make gconfig
```

对于基于GTK的配置器。

所有这些“make”命令都需要构建一个配置实用工具(包括接口)，所以你可能需要为配置实用工具使用的相关库安装“development”包。参考[第2章，系统需求](#chapter-2-system-requirements)了解更多细节，特别是[可选的要求](#22-optional-packages)来获取你最喜欢的界面的依赖项。

对于配置工具中的每个菜单项，您可以找到描述该项目的相关帮助。请参阅[Chapter 6, Buildroot configuration](#chapter-6-buildroot-configuration)了解一些具体配置的细节。

一旦所有配置完成，配置工具会生成一个包含整个配置的`.config`文件。这个文件将由顶层的Makefile读取。

要启动构建过程，只需运行:

```bash
$ make
```

默认情况下，Buildroot不支持顶层并行构建，因此无需运行`make -jN`。不过，也有对顶层并行构建的实验性支持，参见[8.12节，“顶层并行构建”](#812-top-level-parallel-build)。

`make`命令通常会执行以下步骤:

*   下载源文件(如有需要);
*   配置、构建和安装交叉编译工具链，或者简单地导入外部工具链;
*   配置、构建和安装选定的目标包;
*   如果选中，则构建一个内核映像。
*   构建一个引导加载映像(如果选中);
*   以选定的格式创建一个根文件系统。

Buildroot的输出存储在单个目录`output/`中。这个目录包含以下几个子目录:

*   `images/`，存储所有映像(内核映像、引导装载器和根文件系统映像)。这些是需要放在目标系统上的文件。
*   `build/`是构建所有组件的地方(包括Buildroot在主机上所需的工具和为目标编译的包)。这个目录包含每个组件的一个子目录。
*   `host/`包含为主机构建的工具和目标工具链的sysroot。前者是为主机编译的工具的安装，这些工具是正确执行Buildroot所必需的，包括交叉编译工具链。后者类似于根文件系统的层次结构。它包含所有用户空间包的头和库，这些包提供和安装其他包使用的库。但该目录并不是目标文件系统的根文件系统:它包含了很多开发文件、未删除的二进制文件和库，对于嵌入式系统来说，这个目录太大了。这些开发文件用于为目标编译依赖于其他库的库和应用程序。
*   `staging/`是指向`host/`中的目标工具链sysroot的符号链接，它的存在是为了向后兼容。
*   `target/`几乎包含了目标的完整根文件系统:除了`/dev/`中的设备文件之外，所有需要的文件都已经存在了(Buildroot不能创建它们，因为Buildroot不是以root身份运行，也不想以root身份运行)。此外，它没有正确的权限(例如busybox二进制文件的setuid)。因此，这个目录**不应该在你的目标上使用**。相反，你应该使用在`images/`目录下构建的一个图像。如果你需要根文件系统的解压镜像来通过NFS引导，那么使用在`images/`中生成的压缩镜像并将其解压为根文件。与`staging/`相比，`target/`只包含运行所选目标应用程序所需的文件和库:开发文件(头文件等)不存在，二进制文件被剥离(strip)。

这些命令，`make menuconfig|nconfig|gconfig|xconfig`和`make`，是最基本的命令，允许轻松快速地生成符合您需求的图像，具有您启用的所有功能和应用程序。

关于“make”命令用法的更多细节见[8.1节, “make tips”](#81-make-tips)。



# Chapter 5. Community resources
------------------------------

与任何开源项目一样，Buildroot在社区内外都有不同的共享信息的方式。

如果您正在寻求帮助，想了解Buildroot或为项目做出贡献，这些方法都可能使您感兴趣。

**邮件列表**

Buildroot有一个邮件列表供讨论和开发。它是Buildroot用户和开发人员交互的主要方法。

只有Buildroot邮件列表的订阅者才能向此列表发送邮件。您可以通过[邮件列表信息页](http://lists.buildroot.org/mailman/listinfo/buildroot)订阅。

发送到邮件列表的邮件也可以在邮件列表存档中找到，可以通过[Mailman](http://lists.buildroot.org/pipermail/buildroot)或[lore.kernel.org](https://lore.kernel.org/buildroot/)获得。

**IRC**

Buildroot IRC频道[#buildroot](irc://irc.oftc.net/#buildroot)托管在[OFTC](https://www.oftc.net/WebChat/)上。这是一个很有用的地方，可以快速提出问题或讨论某些主题。

在IRC上寻求帮助时，请使用代码分享网站分享相关的日志或代码片段，例如[https://paste.ack.tf/](https://paste.ack.tf/)。

请注意，对于某些问题，发送到邮件列表可能更好，因为它将接触到更多的人，包括开发人员和用户。

**Bug tracker**

Buildroot中的bug可以通过邮件列表报告，或者通过[Buildroot bugtracker](https://bugs.buildroot.org/buglist.cgi?product=buildroot)报告。在创建bug报告之前，请参阅[第22.6节，“报告issue/bug或获得帮助”](#226-reporting-issuesbugs-or-getting-help)。

**Wiki**

[Buildroot wiki页面](http://elinux.org/Buildroot)托管在[eLinux](http://elinux.org) wiki上。它包含一些有用的链接，过去和即将发生的事件的概述，以及待办事项列表。

**Patchwork**

Patchwork是一个基于web的补丁跟踪系统，旨在促进对开源项目的贡献和管理。已发送到邮件列表的补丁会被系统“捕获”，并出现在网页上。任何引用补丁的评论也会添加到补丁页面中。有关Patchwork的更多信息，请参见[http://jk.ozlabs.org/projects/patchwork/](http://jk.ozlabs.org/projects/patchwork/)。

Buildroot的Patchwork网站主要供Buildroot的维护者使用，以确保不会遗漏补丁。Buildroot补丁审阅者也使用它(参见[第22.3.1节，“从Patchwork中应用补丁”](#2231-applying-patches-from-patchwork))。然而，由于该网站在一个干净简洁的web界面中暴露了补丁及其相应的评审注释，因此它对所有Buildroot开发人员都很有用。

Buildroot补丁管理界面可以在[https://patchwork.ozlabs.org/project/buildroot/list/](https://patchwork.ozlabs.org/project/buildroot/list/)上找到.



Part II. 用户指南
===================

# Chapter 6. Buildroot configuration
----------------------------------

`make *config`中的所有配置选项都有一个帮助文本，提供了有关该选项的详细信息。

`make *config`命令也提供了一个搜索工具。阅读不同前端菜单中的帮助信息以了解如何使用它:

*   在menuconfig中，按`/`调用搜索工具;
*   在xconfig中，按下`Ctrl` + `f`来调用搜索工具。

搜索结果显示匹配项的帮助信息。在menuconfig中，左侧列中的数字提供了对应条目的快捷方式。只需键入此数字即可直接跳转到条目，或者在由于缺少依赖项而无法选择条目时跳转到包含菜单。

尽管菜单结构和条目的帮助文本应该足够一目了然，但许多主题需要额外的解释，这些解释在帮助文本中不容易涉及，因此将在以下几节中讨论。


## 6.1. Cross-compilation toolchain
--------------------------------

编译工具链是允许您为系统编译代码的一组工具。它由一个编译器(在我们的例子中，`gcc`)，二进制utils，如汇编器和链接器(在我们的例子中，`binutils`)和一个C标准库(例如[GNU Libc](http://www.gnu.org/software/libc/libc.html), [uClibc-ng](http://www.uclibc-ng.org/))组成。

安装在开发平台上的系统当然已经有了编译工具链，您可以使用它来编译在系统上运行的应用程序。如果你使用的是PC，你的编译工具链在x86处理器上运行，并为x86处理器生成代码。在大多数Linux系统下，编译工具链使用GNU libc (glibc)作为C标准库。这个编译工具链被称为“宿主编译工具链”。运行它和您工作的机器称为“主机系统”[3]。

编译工具链是由你的发行版提供的，Buildroot和它没有任何关系(只是用它来构建交叉编译工具链和在开发主机上运行的其他工具)。

如上所述，系统附带的编译工具链在宿主系统上运行并为处理器生成代码。由于您的嵌入式系统具有不同的处理器，您需要一个交叉编译工具链——一个在主机系统上运行但为目标系统(和目标处理器)生成代码的编译工具链。例如，如果您的主机系统使用x86，而目标系统使用ARM，则主机上的常规编译工具链在x86上运行并为x86生成代码，而交叉编译工具链在x86上运行并为ARM生成代码。

Buildroot为交叉编译工具链提供了两种解决方案:

*   **internal toolchain backend**，在配置界面中称为`Buildroot toolchain`。
*   **external toolchain backend**，在配置界面中称为“External toolchain”。

这两种解决方案之间的选择是通过`Toolchain`菜单中的`Toolchain Type`选项来完成的。一旦选择了一个解决方案，就会出现许多配置选项，下面各节将详细介绍。

### 6.1.1. Internal toolchain backend

内部工具链后端是Buildroot在为目标嵌入式系统构建用户空间应用程序和库之前自行构建交叉编译工具链的后端。

这个后端支持几个C库: [uClibc-ng](http://www.uclibc-ng.org), [glibc](http://www.gnu.org/software/libc/libc.html) 和 [musl](http://www.musl-libc.org).

一旦选择了这个后端，就会出现许多选项。其中最重要的几个允许:

*   更改用于构建工具链的Linux内核头文件的版本。这个项目值得做一些解释。在构建交叉编译工具链的过程中，正在构建C库。该库提供了用户空间应用程序和Linux内核之间的接口。为了知道如何与Linux内核“对话”，C库需要访问Linux内核头文件(即来自内核的`.h`文件)，它定义了用户空间和内核之间的接口(系统调用、数据结构等)。由于此接口是向后兼容的，用于构建工具链的Linux内核头文件的版本不需要与您打算在嵌入式系统上运行的Linux内核的版本完全匹配。它们只需要与您要运行的Linux内核版本相同或更旧的版本即可。如果您使用的内核头文件比您在嵌入式系统上运行的Linux内核更新，那么C库可能使用了Linux内核不提供的接口。
*   更改GCC编译器、binutils和C库的版本。
*   选择一些工具链选项(仅uClibc):工具链是否应该具有RPC支持(主要用于NFS)、宽字符支持、区域设置支持(用于国际化)、C++支持或线程支持。根据您选择的选项，Buildroot菜单中可见的用户空间应用程序和库的数量将发生变化:许多应用程序和库需要启用某些工具链选项。当需要某个工具链选项来启用这些包时，大多数包都会显示注释。如果需要，你可以通过运行`make uclibc-menuconfig`来进一步细化uClibc配置。但是请注意，Buildroot中的所有包都是根据Buildroot中捆绑的默认uClibc配置进行测试的:如果您通过从uClibc中删除功能而偏离此配置，则某些包可能不再构建。

值得注意的是，每当修改其中一个选项时，都必须重新构建整个工具链和系统。请参阅[8.2节，“了解何时需要完全重建”](#82-understanding-when-a-full-rebuild-is-necessary)。

这个后端的优点:

*   与Buildroot集成良好
*   快速，只构建必要的东西

这个后端的缺点:

*   在执行`make clean`时需要重建工具链，这需要时间。如果你想减少构建时间，可以考虑使用外部工具链后端。

### 6.1.2. External toolchain backend

外部工具链后端允许使用现有的预构建的交叉编译工具链。Buildroot知道许多著名的交叉编译工具链(来自ARM的[Linaro](http://www.linaro.org)、[Sourcery CodeBench](http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/editions/lite-edition/) 用于ARM、x86-64、PowerPC和MIPS，并能够自动下载它们，或者可以指向自定义工具链，可以下载或在本地安装。

然后，你有三种使用外部工具链的解决方案:

*   使用预定义的外部工具链配置文件，并让Buildroot下载、提取和安装工具链。Buildroot已经知道一些CodeSourcery和Linaro工具链。只需在`Toolchain`中选择可用的工具链配置文件。这绝对是最简单的解决方案。
*   使用预定义的外部工具链配置文件，但是不是让Buildroot下载并提取工具链，而是可以告诉Buildroot您的工具链已经安装在系统的哪里。只需通过可用的工具链选择`Toolchain`中的工具链配置文件，取消选择`Download toolchain automatically`，并使用交叉编译工具链的路径填充`Toolchain path`文本条目。
*   使用完全自定义的外部工具链。这对于使用crosstool-NG或Buildroot本身生成的工具链特别有用。为此，在`Toolchain`列表中选择`Custom toolchain`解决方案。你需要填充`Toolchain path`, `Toolchain prefix` 和 `External toolchain C library`选项。然后，你必须告诉Buildroot你的外部工具链支持什么。如果你的外部工具链使用glibc库，你只需要知道你的工具链是否支持C++以及它是否内置RPC支持。如果您的外部工具链使用uClibc库，那么您必须告诉Buildroot它是否支持RPC、宽字符、区域设置、程序调用、线程和C++。在执行开始时，Buildroot会告诉您所选选项是否与工具链配置不匹配。

我们的外部工具链支持已经使用CodeSourcery和Linaro的工具链、[crosstool-NG](http://crosstool-ng.org)生成的工具链以及Buildroot本身生成的工具链进行了测试。一般来说，所有支持sysroot特性的工具链都应该可以工作。如果没有，请毫不犹豫地联系开发人员。

我们不支持由OpenEmbedded或Yocto生成的工具链或SDK，因为这些工具链不是纯粹的工具链(即仅仅是编译器、binutils、C和C++库)。相反，这些工具链附带了一组非常大的预编译库和程序。因此，Buildroot不能导入工具链的sysroot，因为它会包含通常由Buildroot构建的数百兆预编译库。

我们也不支持使用发行版工具链(即由发行版安装的gcc/binutils/C库)作为为目标构建软件的工具链。这是因为你的发行版工具链不是一个“纯”的工具链(即仅与C/C++库)，所以我们不能正确地将它导入Buildroot构建环境。因此，即使您正在为x86或x86_64目标构建系统，也必须使用Buildroot或crosstool-NG生成交叉编译工具链。

如果您想为您的项目生成自定义工具链，它可以作为Buildroot中的外部工具链使用，我们的建议是使用Buildroot本身构建它(参见[6.1.3节，“使用Buildroot构建外部工具链”](#613-build-an-external-toolchain-with-buildroot))或使用[crosstool-NG](http://crosstool-ng.org)。

这个后端的优点:

*   允许使用著名的和经过良好测试的交叉编译工具链。
*   避免了交叉编译工具链的构建时间，这通常在嵌入式Linux系统的整体构建时间中非常重要。

这个后端的缺点:

*   如果你预构建的外部工具链有bug，可能很难从工具链供应商那里得到修复，除非你自己使用Buildroot或Crosstool-NG构建外部工具链。

### 6.1.3. Build an external toolchain with Buildroot

Buildroot内部工具链选项可用于创建外部工具链。以下是构建内部工具链的一系列步骤，并将其打包以供Buildroot本身(或其他项目)重用。

创建一个新的Buildroot配置，详细信息如下:

*   为你的目标CPU架构选择合适的**Target options**
*   在**Toolchain**菜单中，为**Toolchain type**保留**Buildroot Toolchain**的默认值，并根据需要配置你的工具链
*   在**System configuration**菜单中，选择**None**作为**Init system**，选择**None**作为**/bin/sh**
*   在**Target packages**菜单中，禁用**BusyBox**
*   在**Filesystem images**菜单中，禁用**tar the root Filesystem**

然后，我们可以触发构建，并让Buildroot生成一个SDK。这将方便地为我们生成一个包含我们的工具链的压缩包:

```bash
make sdk
```

这将在`$(O)/images`中生成SDK压缩包，其名称类似于`arm-buildroot-linux-uclibcgnueabi_sdk-buildroot.tar.gz`。保存这个tar包，因为它现在是工具链，您可以在其他Buildroot项目中作为外部工具链重用。

在其他Buildroot项目中，在**Toolchain**菜单中:

*   将 **Toolchain type** 设置为 **External toolchain**
*   将 **Toolchain** 设置为 **Custom toolchain**
*   将 **Toolchain origin** 设置为 **Toolchain to be downloaded and installed**
*   将 **Toolchain URL** 设置为 `file:///path/to/your/sdk/tarball.tar.gz`

#### 外部工具链包装器

当使用外部工具链时，Buildroot生成一个包装器程序，它透明地将适当的选项(根据配置)传递给外部工具链程序。如果你需要调试这个包装器来检查到底传递了什么参数，你可以将环境变量`BR2_DEBUG_WRAPPER`设置为以下任意一个:

*   `0`, 空或不设置:no debug
*   `1`: 在一行中跟踪所有参数
*   `2`: 每行跟踪一个参数


## 6.2. /dev management
--------------------

在Linux系统中，`/dev`目录包含一些特殊的文件，称为设备文件，允许用户空间应用程序访问由Linux内核管理的硬件设备。如果没有这些设备文件，用户空间应用程序将无法使用硬件设备，即使Linux内核正确识别了这些设备。

在`System configuration`, 和`/dev management`目录下，Buildroot提供了四种不同的解决方案来处理`/dev`目录:

*   第一个解决方案是**静态使用设备表**。这是Linux中处理设备文件的古老经典方法。使用这种方法，设备文件持久化存储在根文件系统中(即，它们在重新启动时保持不变)，并且当从系统中添加或删除硬件设备时，没有任何东西会自动创建和删除这些设备文件。因此，Buildroot使用设备表创建一组标准的设备文件，默认设备表存储在Buildroot源代码中的`system/device_table_dev.txt`中。当Buildroot生成最终的根文件系统映像时将清理该文件，因此设备文件在`output/target`目录中是不可见的。`BR2_ROOTFS_STATIC_DEVICE_TABLE`选项允许更改Buildroot使用的默认设备表，或者添加额外的设备表，以便Buildroot在构建期间创建额外的设备文件。因此，如果您使用此方法，并且您的系统中缺少一个设备文件，例如，您可以创建一个包含附加设备文件描述的`board/<yourcompany>/<yourproject>/device_table_dev.txt`文件，然后您可以将`BR2_ROOTFS_STATIC_DEVICE_TABLE`设置为`system/device_table_dev.txt board/<yourcompany>/<yourproject>/device_table_dev.txt`。有关设备表文件格式的更多细节，请参阅[第25章，Makedev语法文档](#chapter-25-makedev-syntax-documentation)。
*   第二种解决方案是**仅动态使用devtmpfs**。devtmpfs是Linux内核中的一个虚拟文件系统，在内核2.6.32中引入(如果您使用的是旧的内核，则不能使用该选项)。当挂载到`/dev`时，这个虚拟文件系统将自动使设备文件随着硬件设备的添加和从系统中删除而显示和消失。该文件系统在重启期间不是持久的，它是由内核动态填充的。使用devtmpfs需要启用以下内核配置选项:`CONFIG_DEVTMPFS` 和 `CONFIG_DEVTMPFS_MOUNT`。当Buildroot负责为嵌入式设备构建Linux内核时，它会确保启用这两个选项。但是，如果您在Buildroot之外构建Linux内核，那么启用这两个选项是您的责任(如果您没有这样做，您的Buildroot系统将无法启动)。
*   第三种解决方案是**动态使用devtmpfs + mdev**。此方法也依赖于上面详细介绍的devtmpfs虚拟文件系统(因此在内核配置中启用`CONFIG_DEVTMPFS` 和 `CONFIG_DEVTMPFS_MOUNT`的需求仍然适用)，但在其之上添加了`mdev`用户空间实用程序。`mdev`是BusyBox的一个程序部分，每次添加或删除设备时内核都会调用它。由于有了`/etc/mdev.conf`配置文件，`mdev`可以被配置为，例如，设置设备文件的特定权限或所有权，在设备出现或消失时调用脚本或应用程序等。基本上，它允许用户空间对设备添加和删除事件做出反应。例如，当设备出现在系统上时，`mdev`可以用于自动加载内核模块。如果你的设备需要固件，`mdev`也很重要，因为它将负责将固件内容推送到内核。`mdev`是`udev`的轻量级实现(具有较少的功能)。有关`mdev`及其配置文件语法的更多细节，请参见[http://git.busybox.net/busybox/tree/docs/mdev.txt](http://git.busybox.net/busybox/tree/docs/mdev.txt)。
*   第四个解决方案是**动态使用devtmpfs + eudev**。该方法也依赖于上面详细介绍的devtmpfs虚拟文件系统，但在其之上添加了`eudev`用户空间守护进程。`eudev`是一个在后台运行的守护进程，当系统添加或删除设备时，内核会调用它。它是一个比`mdev`更重量级的解决方案，但提供了更高的灵活性。`eudev`是`udev`的独立版本，`udev`是最初在大多数桌面Linux发行版中使用的用户空间守护进程，现在是Systemd的一部分。更多详情，请参阅[http://en.wikipedia.org/wiki/Udev](https://en.wikipedia.org/wiki/Udev)。

Buildroot开发者的建议是，从**Dynamic using devtmpfs only**的解决方案开始，直到你需要在设备添加/删除时通知用户空间，或者如果需要固件，在这种情况下，**Dynamic using devtmpfs + mdev**通常是一个很好的解决方案。

注意，如果选择`systemd`作为init系统，/dev管理将由`systemd`提供的`udev`程序执行。


## 6.3. init system
----------------

init程序是内核启动的第一个用户空间程序(PID为1)，负责启动用户空间服务和程序(例如:web服务器、图形应用程序、其他网络服务器，等等)。

Buildroot允许使用三种不同类型的init系统，可以从`System configuration`、`Init System`中选择:

*   第一个解决方案是**BusyBox**。在众多程序中，BusyBox实现了一个基本的`init`程序，这对于大多数嵌入式系统来说已经足够了。启用`BR2_INIT_BUSYBOX`将确保BusyBox将构建并安装它的`init`程序。这是Buildroot的默认解决方案。BusyBox的`init`程序会在启动时读取`/etc/inittab`文件来知道要做什么。该文件的语法可以在[http://git.busybox.net/busybox/tree/examples/inittab](http://git.busybox.net/busybox/tree/examples/inittab)中找到(注意BusyBox `inittab`的语法是特殊的:不要使用互联网上随机的`inittab`文档来了解BusyBox `inittab`)。Buildroot中的默认`inittab`存储在`system/skeleton/etc/inittab`中。除了挂载一些重要的文件系统，默认的inittab所做的主要工作是启动`/etc/init.d/rcS` shell脚本，并启动`getty`程序(它提供了一个登录提示符)。
*   第二种解决方案是**systemV**。这个解决方案使用了旧的传统sysvinit程序，打包在`package/sysvinit`的Buildroot中。这是在大多数桌面Linux发行版中使用的解决方案，直到他们切换到更新的替代方案，如Upstart或Systemd。`sysvinit`也可以使用`inittab`文件(它与BusyBox的语法略有不同)。这个初始化解决方案默认安装的`inittab`位于`package/sysvinit/inittab`。
*   第三种解决方案是**systemd**。`systemd`是Linux的新一代init系统。它比传统的init程序做得更多:积极的并行化能力，使用套接字和D-Bus激活来启动服务，提供按需启动守护进程，使用Linux控制组跟踪进程，支持系统状态的快照和恢复等。`systemd`在相对复杂的嵌入式系统上很有用，例如需要D-Bus和服务相互通信的系统。值得注意的是，`systemd`带来了相当多的大型依赖:`dbus`， `udev`等。有关`systemd`的更多详细信息，请参见[http://www.freedesktop.org/wiki/Software/systemd](http://www.freedesktop.org/wiki/Software/systemd)。

Buildroot开发者推荐的解决方案是使用**BusyBox init**，因为它对于大多数嵌入式系统来说已经足够了。**systemd**可以用于更复杂的情况。


* * *

[3] 这个术语与GNU configure所使用的不同，在GNU configure中，主机是运行应用程序的机器(通常与target相同)。



# Chapter 7. Configuration of other components
--------------------------------------------

在尝试修改下面的任何组件之前，请确保您已经配置了Buildroot本身，并启用了相应的包。

**BusyBox**

如果你已经有一个BusyBox配置文件，你可以直接使用`BR2_PACKAGE_BUSYBOX_CONFIG`在Buildroot配置中指定这个文件。否则，Buildroot将从默认的BusyBox配置文件启动。

要对配置进行后续更改，请使用`make busybox-menuconfig`打开BusyBox配置编辑器。

也可以通过环境变量指定BusyBox配置文件，但不推荐这样做。请参阅[第8.6节，环境变量](#86-environment-variables)了解更多细节。

**uClibc**

uClibc的配置与BusyBox的配置相同。用于指定现有配置文件的configuration变量是`BR2_UCLIBC_CONFIG`。进行后续更改的命令是`make uclibc-menuconfig`。

**Linux kernel**

如果你已经有了一个内核配置文件，你可以使用`BR2_LINUX_KERNEL_USE_CUSTOM_CONFIG`直接在Buildroot配置中指定这个文件。

如果你还没有内核配置文件，你可以使用`BR2_LINUX_KERNEL_USE_DEFCONFIG`在Buildroot配置中指定defconfig，或者使用`BR2_LINUX_KERNEL_USE_CUSTOM_CONFIG`创建一个空文件并指定它作为自定义配置文件。

要对配置进行后续更改，请使用`make linux-menuconfig`打开Linux配置编辑器。

**Barebox**

Barebox的配置与Linux内核的配置相同。对应的配置变量是`BR2_TARGET_BAREBOX_USE_CUSTOM_CONFIG`和`BR2_TARGET_BAREBOX_USE_DEFCONFIG`。要打开配置编辑器，使用`make barebox-menuconfig`。

**U-Boot**

U-Boot(2015.04或更新版本)的配置方式与Linux内核相同。对应的配置变量是`BR2_TARGET_UBOOT_USE_CUSTOM_CONFIG`和`BR2_TARGET_UBOOT_USE_DEFCONFIG`。要打开配置编辑器，使用`make uboot-menuconfig`。



# Chapter 8. General Buildroot usage
----------------------------------

## 8.1. make tips
----------------

这是一组可以帮助你充分利用Buildroot的技巧。

**显示make执行的所有命令:** 

```bash
$ make V=1 <target>
```

**使用defconfig显示板的列表:** 

```bash
$ make list-defconfigs
```

**显示所有可用的目标:** 

```bash
$ make help
```

并不是所有的targets都是可用的，`.config`文件中的一些设置可能会隐藏一些targets:

*   `busybox-menuconfig`仅在启用`busybox`时有效;
*   `linux-menuconfig`和`linux-savedefconfig`仅在`linux`启用时有效;
*   `uclibc-menuconfig`仅当在内部工具链后端选择uClibc C库时可用;
*   `barebox-menuconfig` 和 `barebox-savedefconfig`仅在启用`barebox`引导加载程序时有效。
*   `uboot-menuconfig` 和 `uboot-savedefconfig`仅在`U-Boot`引导加载程序启用且`uboot`构建系统设置为`Kconfig`时有效。

**清理:** 当任何体系结构或工具链配置选项发生更改时，都需要显式清理。

删除所有构建产品(包括构建目录、宿主、过渡树和目标树、图像和工具链):

```bash
$ make clean
```

**生成手册:** 目前的手册来源位于docs/manual目录中。生成手册:

```bash
$ make manual-clean
$ make manual
```

手动输出将在output/docs/manual中生成。

**注意**

*   构建文档需要一些工具(参见:[第2.2节，“可选包”](#22-optional-packages))。

**为新target重置Buildroot:**以删除所有构建产品以及配置:

```bash
$ make distclean
```

**注意.** 如果启用了`ccache`，运行`make clean`或`distclean`不会清空Buildroot使用的编译器缓存。要删除它，请参阅[8.13.3节，“在Buildroot中使用`ccache`”](#8133-using-ccache-in-buildroot)。

**转储内部的make变量:**可以转储已知要创建的变量及其值:

```bash
$ make -s printvars VARS='VARIABLE1 VARIABLE2'
VARIABLE1=value_of_variable
VARIABLE2=value_of_variable
```

可以使用一些变量调整输出:

*   `VARS`会将列表限制为名称与指定的make-patterns匹配的变量——这个必须设置，否则不会打印
*   如果设置为`YES`， `QUOTED_VARS`将单引号引用这个值
*   如果设置为`YES`， `RAW_VARS`将打印未展开的值

例如:

```bash
$ make -s printvars VARS=BUSYBOX_%DEPENDENCIES
BUSYBOX_DEPENDENCIES=skeleton toolchain
BUSYBOX_FINAL_ALL_DEPENDENCIES=skeleton toolchain
BUSYBOX_FINAL_DEPENDENCIES=skeleton toolchain
BUSYBOX_FINAL_PATCH_DEPENDENCIES=
BUSYBOX_RDEPENDENCIES=ncurses util-linux
```

```bash
$ make -s printvars VARS=BUSYBOX_%DEPENDENCIES QUOTED_VARS=YES
BUSYBOX_DEPENDENCIES='skeleton toolchain'
BUSYBOX_FINAL_ALL_DEPENDENCIES='skeleton toolchain'
BUSYBOX_FINAL_DEPENDENCIES='skeleton toolchain'
BUSYBOX_FINAL_PATCH_DEPENDENCIES=''
BUSYBOX_RDEPENDENCIES='ncurses util-linux'
```

```bash
$ make -s printvars VARS=BUSYBOX_%DEPENDENCIES RAW_VARS=YES
BUSYBOX_DEPENDENCIES=skeleton toolchain
BUSYBOX_FINAL_ALL_DEPENDENCIES=$(sort $(BUSYBOX_FINAL_DEPENDENCIES) $(BUSYBOX_FINAL_PATCH_DEPENDENCIES))
BUSYBOX_FINAL_DEPENDENCIES=$(sort $(BUSYBOX_DEPENDENCIES))
BUSYBOX_FINAL_PATCH_DEPENDENCIES=$(sort $(BUSYBOX_PATCH_DEPENDENCIES))
BUSYBOX_RDEPENDENCIES=ncurses util-linux
```

加引号的变量的输出可以在shell脚本中重用，例如:

```bash
$ eval $(make -s printvars VARS=BUSYBOX_DEPENDENCIES QUOTED_VARS=YES)
$ echo $BUSYBOX_DEPENDENCIES
skeleton toolchain
```


## 8.2. Understanding when a full rebuild is necessary
---------------------------------------------------

当通过`make menuconfig`、`make xconfig`或其他配置工具更改系统配置时，Buildroot不会尝试检测应该重建系统的哪些部分。在某些情况下，Buildroot应该重建整个系统，在某些情况下，只重建特定的包子集。但是以完全可靠的方式检测它是非常困难的，因此Buildroot的开发人员决定不尝试这样做。

相反，用户有责任知道何时需要完全重建。作为提示，这里有一些经验法则可以帮助你理解如何使用Buildroot:

*   当目标体系结构配置更改时，需要完全重建。例如，改变体系结构变体、二进制格式或浮点策略会对整个系统产生影响。
*   更改工具链配置时，通常需要完全重新构建。更改工具链配置通常涉及更改编译器版本、C库或其配置的类型，或其他一些基本配置项，这些更改会对整个系统产生影响。
*   当向配置中添加额外的包时，不需要完全重新构建。Buildroot将检测到这个包从未被构建过，并将构建它。但是，如果这个包是一个库，可以被已经构建的包使用，那么Buildroot将不会自动重建这些包。你要么知道哪些包应该重新构建，然后手动重新构建，要么应该完全重新构建。例如，假设你已经使用`ctorrent`包构建了一个系统，但是没有使用`openssl`。你的系统正常工作了，但是你意识到你希望在`ctorrent`中有SSL支持，所以你在Buildroot配置中启用了`openssl`包并重新启动构建。Buildroot会检测到应该构建`openssl`并且会构建它，但是它不会检测到`ctorrent`应该重新构建以受益于`openssl`来添加openssl支持。你要么完全重建，要么重建`ctorrent`本身。
*   当从配置中删除包时，Buildroot不会做任何特殊的事情。它不会从目标根文件系统或工具链sysroot中删除此包安装的文件。需要完全重建才能摆脱这个包。不过，一般来说，你并不需要马上删除这个包，可以等到下一个午休时间再从头开始构建。
*   当包的子选项更改时，包不会自动重新生成。在进行此类更改后，仅重建此软件包通常就足够了，除非启用package子选项向软件包添加一些功能，这些功能对已经构建的另一个软件包有用。同样，Buildroot不会跟踪包何时应该重新构建:一旦包被构建，除非明确告知，否则永远不会重新构建。
*   在修改根文件系统的框架时，需要完全重建。然而，当根文件系统覆盖更改时，进行post-build脚本或post-image脚本时，不需要完全重建:简单的`make`调用将考虑更改。
*   当在`FOO_DEPENDENCIES`中列出的包被重建或删除时，包`foo`不会自动重建。例如，如果一个包`bar`以`FOO_DEPENDENCIES = bar`列在`FOO_DEPENDENCIES`中，并且`bar`包的配置被更改，配置更改不会导致包`foo`的自动重建。在这种情况下，你可能需要重新构建任何在`DEPENDENCIES`中引用`bar`的包，或者执行完全重建以确保任何`bar`依赖包是最新的。

一般来说，当遇到构建错误，并且不确定所做配置更改的潜在后果时，请完全重新构建。如果你遇到了相同的构建错误，那么你可以肯定这个错误与包的部分重构无关，如果这个错误发生在来自官方Buildroot的包上，不要犹豫，报告这个问题!随着你使用Buildroot的经验的发展，你将逐渐了解何时真正需要完全重建，并将节省越来越多的时间。

作为参考，完整的重建可以通过运行:

```bash
$ make clean all
```

## 8.3. Understanding how to rebuild packages
------------------------------------------

Buildroot用户最常见的问题之一是如何重建给定的包或如何删除包而不从头开始重建所有内容。

Buildroot不支持删除包而不从头开始重建。这是因为Buildroot不会跟踪哪个包安装了`output/staging` 和 `output/target`目录中的哪些文件，或者哪个包会根据另一个包的可用性进行不同的编译。

从头开始重建单个包的最简单方法是在`output/build`中删除它的构建目录。然后，Buildroot将从头重新提取、重新配置、重新编译和重新安装此包。你可以使用`make <package>-dirclean`命令让buildroot执行此操作。

另一方面，如果你只想从编译步骤重新启动包的构建过程，你可以运行`make <package>-rebuild`。它会重新启动包的编译和安装，但不是从零开始:它基本上重新执行包中的`make`和`make install`，因此它只会重建更改的文件。

如果你想从配置步骤重新启动包的构建过程，你可以运行`make <package>-reconfigure`。它将重新启动配置、编译和安装包。

虽然`<package>-rebuild`意味着`<package>-reinstall`和`<package>-reconfigure`意味着`<package>-rebuild`，但这些目标和`<package>`都只作用于上述包，而不会触发重新创建根文件系统映像。如果需要重新创建根文件系统，另外应该运行`make`或`make all`。

在内部，Buildroot创建所谓的stamp文件来跟踪每个包的构建步骤已经完成。它们存储在包构建目录`output/build/<package>-<version>/`中，并被命名为`.stamp_<step-name>`。上面详细描述的命令只是操作这些stamp文件，以强制Buildroot重新启动包构建过程的特定步骤集。

关于包专用make目标的更多细节将在[8.13.5节，“特定于包的生成目标”](#8135-package-specific-make-targets)中解释。


## 8.4. Offline builds
-------------------

如果你想进行离线构建，只是想下载之前在配置器中选择的所有源代码(menuconfig、nconfig、xconfig或gconfig)，然后执行:

```bash
$ make source
```

你现在可以断开连接或将`dl`目录的内容复制到build-host。


## 8.5. Building out-of-tree
-------------------------

默认情况下，Buildroot构建的所有内容都存储在Buildroot树的`output`目录中。

Buildroot还支持使用类似于Linux内核的语法从树中构建。要使用它，在make命令行中添加`O=<directory>`:

```bash
$ make O=/tmp/build menuconfig
```

或:

```bash
$ cd /tmp/build; make O=$PWD -C path/to/buildroot menuconfig
```

所有输出文件都位于`/tmp/build`目录下。如果`O`路径不存在，Buildroot会创建它。

**注意:** `O`路径可以是绝对路径也可以是相对路径，但如果它作为相对路径传递，重要的是要注意，它是相对于主Buildroot源目录进行解释的，**不是**当前工作目录。

当使用树外构建时，Buildroot的`.config`和临时文件也存储在输出目录中。这意味着，只要使用唯一的输出目录，您就可以使用相同的源代码树并行运行多个构建。

为了方便使用，Buildroot在输出目录中生成一个Makefile包装器——因此在第一次运行后，你不再需要传递`O=<…>` 和 `-C <…>`，只需运行(在输出目录中):

```bash
$ make <target>
```


## 8.6. Environment variables
--------------------------

当将某些环境变量传递给`make`或在环境中设置时，Buildroot 还支持一些环境变量：

*   `HOSTCXX`, 宿主C++编译器使用
*   `HOSTCC`, 宿主C编译器要使用
*   `UCLIBC_CONFIG_FILE=<path/to/.config>`, 如果正在构建内部工具链，则用于编译uClibc的uClibc配置文件的路径。注意，uClibc配置文件也可以从配置界面进行设置，因此可以通过Buildroot的`.config`文件进行设置;这是推荐的设置方式。
*   `BUSYBOX_CONFIG_FILE=<path/to/.config>`, BusyBox配置文件的路径。请注意，BusyBox配置文件也可以从配置界面设置，因此可以通过Buildroot的`.config`文件进行设置;这是推荐的设置方式。
*   `BR2_CCACHE_DIR` 在使用ccache时覆盖Buildroot存储缓存文件的目录。
*   `BR2_DL_DIR` 覆盖Buildroot存储/检索下载文件的目录。请注意，Buildroot下载目录也可以从配置界面设置，因此可以通过Buildroot的`.config`文件设置。有关如何设置下载目录，请参阅[8.13.4节，“下载包的位置”](#8134-location-of-downloaded-packages)。
*   `BR2_GRAPH_ALT`, 如果设置且非空，则在构建时图中使用另一种颜色方案
*   `BR2_GRAPH_OUT` 要设置生成图形的文件类型，可以使用`pdf`(默认)或`png`。
*   `BR2_GRAPH_DEPS_OPTS` 向依赖关系图传递额外的选项;请参阅[第8.9节，“绘制包之间的依赖关系”](#89-graphing-the-dependencies-between-packages)获取可接受的选项
*   `BR2_GRAPH_DOT_OPTS` 作为选项一字不差地传递给`dot`工具来绘制依赖关系图。
*   `BR2_GRAPH_SIZE_OPTS`用于将额外的选项传递给大小图;请参阅[8.11节，“绘制包对文件系统存储的占用”](#811-graphing-the-filesystem-size-contribution-of-packages)获取可接受的选项

一个使用位于顶层目录和$HOME中的配置文件的示例:

```bash
$ make UCLIBC_CONFIG_FILE=uClibc.config BUSYBOX_CONFIG_FILE=$HOME/bb.config
```

如果你想使用默认的`gcc`或`g++`以外的编译器在你的主机上构建辅助二进制文件，那么就去做吧

```bash
$ make HOSTCXX=g++-4.3-HEAD HOSTCC=gcc-4.3-HEAD
```


## 8.7. Dealing efficiently with filesystem images
-----------------------------------------------

文件系统映像可能非常大，这取决于你选择的文件系统、包的数量、是否配置了空闲空间……然而，文件系统映像中的某些位置可能是空的(例如，长时间运行的0);这种文件称为稀疏文件(sparse file)。

大多数工具都可以高效地处理sparse文件，并且只存储或写入sparse文件中非空的部分。

例如:

*   `tar`接受`-S`选项来告诉它只存储非零块的sparse文件:
    
    *   `tar cf archive.tar -S [files…]`将有效地将稀疏文件存储为tar包
    *   `tar xf archive.tar -S`将有效地存储从压缩包中提取的稀疏文件
    
*   `cp`接受`--sparse=WHEN`选项(`WHEN`是`auto`， `never`或`always`中的一个):
    
    *   `cp --sparse=always source.file dest.file`将使`dest.file`成为一个稀疏文件，如果`source.file`有长串的0
    

其他工具可能有类似的选项。请查阅各自的手册页。

如果你需要存储文件系统映像(例如从一台机器传输到另一台机器)，或者需要发送它们(例如发送到Q&A团队)，你可以使用稀疏文件。

但是请注意，当使用稀疏模式`dd`时，将文件系统映像刷新到设备可能会导致文件系统损坏(例如，ext2文件系统的块位图可能损坏;或者，如果文件系统中有稀疏文件，这些部分在回读时可能不是全0)。你应该只在构建机器上处理文件时使用sparse文件，而不是将它们传输到将在目标上使用的实际设备时。


## 8.8. Details about packages
---------------------------

Buildroot可以生成一个JSON简介，描述当前配置中启用的一组包，以及它们的依赖项、许可证和其他元数据。这个JSON简介是使用`show-info` make目标生成的:

```bash
make show-info
```

Buildroot还可以使用`pkg-stats` make目标生成关于包的详细信息，以HTML和JSON输出。这些细节包括已知的cve(安全漏洞)是否会影响当前配置中的包。它还会显示这些包是否有更新的上游版本。

```bash
make pkg-stats
```


## 8.9. Graphing the dependencies between packages
-----------------------------------------------

Buildroot的工作之一是知道包之间的依赖关系，并确保它们按照正确的顺序构建。这些依赖项有时相当复杂，对于给定的系统，通常不容易理解为什么Buildroot将这样或那样的包引入构建中。

为了帮助理解依赖关系，从而更好地理解嵌入式Linux系统中不同组件的角色，Buildroot能够生成依赖关系图。

要生成你编译的整个系统的依赖关系图，只需运行:

```bash
make graph-depends
```

你可以在`output/graphs/graph-depends.pdf`中找到生成的图形。

如果你的系统非常大，依赖关系图可能会过于复杂和难以阅读。因此，可以仅为给定的包生成依赖图:

```bash
make <pkg>-graph-depends
```

你可以在`output/graph/<pkg>-graph-depends.pdf`中找到生成的图形。

请注意，依赖图是使用Graphviz项目中的`dot`工具生成的，要使用此功能，您必须在系统上安装该工具。在大多数发行版中，它都以`graphviz`包的形式提供。

默认情况下，依赖关系图以PDF格式生成。然而，通过传递`BR2_GRAPH_OUT`环境变量，你可以切换到其他输出格式，例如PNG、PostScript或SVG。支持`dot`工具的`-T`选项支持的所有格式。

```bash
BR2_GRAPH_OUT=svg make graph-depends
```

`graph-depends`的行为可以通过在`BR2_GRAPH_DEPS_OPTS`环境变量中设置选项来控制。可接受的选项有:

*   `--depth N`, `-d N`, 将依赖深度限制为`N`级。默认值`0`表示没有限制。
*   `--stop-on PKG`, `-s PKG`, 停止包装上的图形`PKG`。`PKG`可以是一个实际的包名、一个通义词、关键字virtual(在虚拟包上停止)或关键字host(在主机包上停止)。包仍然在图上，但它的依赖项不在图上。
*   `--exclude PKG`, `-x PKG`, 类似于`--stop-on`，但也从图中省略了`PKG`。
*   `--transitive`, `--no-transitive`, 绘制(或不绘制)传递依赖关系。默认情况下不绘制传递依赖。
*   `--colors R,T,H`, 用逗号分隔的颜色列表，用于绘制根包(`R`)、目标包(`T`)和主包(`H`)。默认值:`lightblue,grey,gainsboro`

```bash
BR2_GRAPH_DEPS_OPTS='-d 3 --no-transitive --colors=red,green,blue' make graph-depends
```


## 8.10. Graphing the build duration
---------------------------------

当系统的构建需要很长时间时，了解哪些包构建时间最长，看看是否可以采取措施加快构建速度，这有时是有用的。为了帮助这种构建时间分析，Buildroot收集每个包的每个步骤的构建时间，并允许从这些数据生成图形。

要在构建后生成构建时间图，请运行:

```bash
make graph-build
```

这将在`output/graphs`中生成一组文件:

*   `build.hist-build.pdf`, 每个包的构建时间直方图，按构建顺序排序。
*   `build.hist-duration.pdf`, 每个包的构建时间直方图，按持续时间排序(最长的优先)
*   `build.hist-name.pdf`, 每个包的构建时间直方图，按包名排序。
*   `build.pie-packages.pdf`, 每个包的构建时间的饼图
*   `build.pie-steps.pdf`, 包构建过程中每个步骤花费的全局时间的饼图。

这个`graph-build`目标需要安装Python Matplotlib和Numpy库(在大多数发行版上是`python-matplotlib` 和 `python-numpy`)，如果你使用的Python版本高于2.7(在大多数发行版上是` python-argparse`)，还需要安装`argparse`模块。

默认情况下，图形的输出格式是PDF，但可以使用`BR2_GRAPH_OUT`环境变量选择不同的格式。唯一支持的其他格式是PNG:

```bash
BR2_GRAPH_OUT=png make graph-build
```


## 8.11. Graphing the filesystem size contribution of packages
-----------------------------------------------------------

当你的目标系统增长时，有时候了解每个Buildroot包对整个根文件系统存储的占用是很有用的。为了帮助进行这样的分析，Buildroot收集关于每个包安装的文件的数据，并使用这些数据，生成一个图表和CSV文件，详细说明不同包的存储占用。

要在构建后生成这些数据，请运行:

```bash
make graph-size
```

这将生成:

*   `output/graphs/graph-size.pdf`, 每个包占根文件系统总存储的饼图
*   `output/graphs/package-size-stats.csv`, 一个CSV文件，给出了每个包对根文件系统总存储的占用
*   `output/graphs/file-size-stats.csv`, 一个CSV文件，给出了每个安装文件对其所属包的存储大小，以及整个文件系统的存储大小。

这个`graph-size`目标需要安装Python Matplotlib库(在大多数发行版上是`python-matplotlib`)，如果你使用的Python版本高于2.7(在大多数发行版上是`python-argparse`)，还需要安装`argparse`模块。

就像持续时间图一样，支持使用`BR2_GRAPH_OUT`环境变量来调整输出文件格式。请参阅[第8.9节，“绘制包之间的依赖关系”](#89-graphing-the-dependencies-between-packages)以了解这个环境变量的详细信息。

此外，可以设置环境变量`BR2_GRAPH_SIZE_OPTS`来进一步控制生成的图。可接受的选项有:

*   `--size-limit X`, `-l X`, 将自身占用低于`X`百分比的所有包分组到图中标记为其他的单个条目中。默认情况下，`X=0.01`，这意味着每个占用小于1%的包被分组在其他包之下。可接受的值在`[0.0..1.0]`的范围内。
*   `--iec`, `--binary`, `--si`, `--decimal`, 使用IEC(二进制，1024的幂)或SI(十进制，1000的幂;默认)前缀。
*   `--biggest-first`, 按大小递减的顺序而不是大小递增的顺序对包进行排序。

**注意.** 收集到的文件系统大小的数据只有在完全重建后才有意义。在使用`make graph-size`之前，请确保运行`make clean all`。

要比较两个不同Buildroot编译的根文件系统大小，例如在调整配置后或切换到另一个Buildroot版本时，使用`size-stats-compare`脚本。它以两个`file-size-stats.csv`文件(由`make graph-size`生成)作为输入。有关更多细节，请参阅此脚本的帮助文本:

```bash
utils/size-stats-compare -h
```


## 8.12. Top-level parallel build
------------------------------

**注意.** 本节讨论的是一个实验性的特性，在某些正常情况下可以达到收支平衡。使用风险自负。

Buildroot始终能够在每个包的基础上使用并行构建:每个包都是由Buildroot使用`make -jN`(或非基于make的构建系统的等效调用)构建的。并行度默认为cpu数量+ 1，但可以使用`BR2_JLEVEL`配置选项进行调整。

然而，直到2020.02,Buildroot都在以串行方式构建包:每个包都是一个接一个地构建，而不是在包之间并行构建。截至2020.02,Buildroot实验性地支持了**顶层并行构建**，通过并行构建没有依赖关系的包，可以显著节省构建时间。然而，该功能被标记为试验性的，已知在某些情况下不起作用。

为了使用顶层并行构建，必须:

1.  在Buildroot配置中启用`BR2_PER_PACKAGE_DIRECTORIES`选项
2.  在启动Buildroot构建时使用`make -jN`

在内部，`BR2_PER_PACKAGE_DIRECTORIES`将启用一种名为**per-package directories**的机制，它将产生以下效果:

*   将使用每个包的目标目录和主机目录，而不是所有包通用的全局目标目录和全局主机目录，分别在`$(O)/per-package/<pkg>/target/` 和 `$(O)/per-package/<pkg>/host/`中使用。这些文件夹将从`<pkg>`构建开始时的包依赖的对应文件夹中填充。因此，编译器和所有其他工具只能查看和访问由`<pkg>`显式列出的依赖项安装的文件。
*   在构建结束时，全局的target和host目录将被填充，分别位于`$(O)/target` 和 `$(O)/host`中。这意味着在构建过程中，这些文件夹将是空的，只有在构建的最后阶段才会填充它们。


## 8.13. Advanced usage
--------------------

### 8.13.1. Using the generated toolchain outside Buildroot

你可能想为你的目标编译你自己的程序或其他没有在Buildroot中打包的软件。为了做到这一点，您可以使用Buildroot生成的工具链。

Buildroot生成的工具链默认位于`output/host/`。使用它最简单的方法是将`output/host/bin/`添加到你的PATH环境变量中，然后使用`ARCH-linux-gcc`, `ARCH-linux-objdump`, `ARCH-linux-ld`等。

或者，Buildroot也可以通过运行`make sdk`命令将所有选定包的工具链和开发文件导出为SDK。这将生成主机目录`output/host/`内容的压缩包，命名为`<TARGET-TUPLE>_sdk-buildroot.tar.gz`(可以通过设置环境变量`BR2_SDK_PREFIX`来覆盖)，并位于输出目录`output/images/`中。

然后，当应用程序开发人员希望开发尚未打包为Buildroot包的应用程序时，可以将此压缩包分发给应用程序开发人员。

在提取SDK压缩包后，用户必须运行脚本`relocate-sdk.sh`(位于SDK的顶部目录)，以确保所有路径都更新为新的位置。

或者，如果你只是想准备SDK而不生成压缩包(例如，因为你只是移动`host`目录，或者将自己生成压缩包)，Buildroot也允许你只使用`make prepare-sdk`来准备SDK而不实际生成压缩包。

为了方便你，通过选择`BR2_PACKAGE_HOST_ENVIRONMENT_SETUP`选项，你可以在`output/host/`中安装一个`environment-setup`脚本，也就是在你的SDK中。这个脚本可以通过`. your/sdk/path/environment-setup`来导出一些环境变量，这些变量将有助于使用Buildroot sdk交叉编译你的项目:`PATH`将包含sdk二进制文件，标准的autotools变量将使用适当的值定义，`CONFIGURE_FLAGS`将包含basic `./configure`选项来交叉编译autotools项目。它还提供了一些有用的命令。但是请注意，一旦这个脚本执行，环境就只能用于交叉编译，而不再用于本地编译。

### 8.13.2. Using `gdb` in Buildroot

Buildroot允许进行交叉调试，调试器在构建机上运行，并与目标机上的`gdbserver`通信以控制程序的执行。

要实现这一点:

*   如果你使用的是内部工具链(由Buildroot构建)，你必须启用`BR2_PACKAGE_HOST_GDB`, `BR2_PACKAGE_GDB` 和 `BR2_PACKAGE_GDB_SERVER`。这可以确保交叉gdb和gdbserver都被构建，并且gdbserver被安装到你的目标上。
*   如果你使用了外部工具链，你应该启用`BR2_TOOLCHAIN_EXTERNAL_GDB_SERVER_COPY`，它会将外部工具链中包含的gdbserver复制到目标。如果您的外部工具链没有交叉gdb或gdbserver，也可以通过启用与内部工具链后端相同的选项，让Buildroot构建它们。

现在，要开始调试一个名为`foo`的程序，你应该在target上运行:

```bash
gdbserver :2345 foo
```

这将导致`gdbserver`在TCP端口2345上监听来自交叉gdb的连接。

然后，在主机上使用下面的命令行启动cross gdb:

```bash
<buildroot>/output/host/bin/<tuple>-gdb -ix <buildroot>/output/staging/usr/share/buildroot/gdbinit foo
```

当然，`foo`必须在当前目录中可用，并使用调试符号构建。通常，你从构建`foo`的目录启动这个命令(而不是从`output/target/`启动，因为该目录中的二进制文件被剥离(strip)了)。

`<buildroot>/output/staging/usr/share/buildroot/gdbinit`文件将告诉交叉gdb在哪里找到目标的库。

最后，从cross gdb连接到目标:

```bash
(gdb) target remote <target ip address>:2345
```

### 8.13.3. Using `ccache` in Buildroot

[ccache](https://ccache.samba.org)是一个编译器缓存。它存储了每次编译过程产生的目标文件，并且能够通过使用预先存在的目标文件跳过将来对同一个源文件(具有相同的编译器和相同的参数)的编译。当多次从头开始构建几乎相同的版本时，它可以很好地加快构建过程。

Buildroot中集成了对`ccache`的支持。你只需要在`Build options`中启用`Enable compiler cache`。这将自动构建`ccache`并将其用于每个主机和目标编译。

缓存位于由`BR2_CCACHE_DIR`配置选项定义的目录中，默认为`$HOME/.buildroot-ccache`。这个默认位置在Buildroot输出目录之外，因此它可以被不同的Buildroot构建共享。如果您想要删除缓存，只需删除此目录。

你可以通过运行`make ccache-stats`来获取缓存的统计信息(大小、命中次数、未命中次数等)。

make目标`ccache-options`和`CCACHE_OPTIONS`变量提供了对ccache更通用的访问。例如

```bash
# set cache limit size
make CCACHE_OPTIONS="--max-size=5G" ccache-options

# zero statistics counters
make CCACHE_OPTIONS="--zero-stats" ccache-options
```

`ccache`对源文件和编译器选项进行哈希。如果编译器选项不同，则不会使用缓存的对象文件。不过，很多编译器选项都包含过渡目录的绝对路径。因此，构建不同的输出目录将导致许多缓存未命中。

为了避免这个问题，buildroot有`Use relative paths`选项(`BR2_CCACHE_USE_BASEDIR`)。这将重写所有指向输出目录内的绝对路径为相对路径。因此，更改输出目录不再导致缓存缺失。

相对路径的一个缺点是，它们最终也是目标文件中的相对路径。因此，例如，调试器将不再找到文件，除非你先cd到输出目录。

有关重写绝对路径的更多细节，请参阅[ccache手册的“在不同目录中编译”部分](https://ccache.samba.org/manual.html#_compiling_in_different_directories)。

当在Buildroot中使用`BR2_CCACHE=y`选项启用`ccache`时:

*   `ccache`在Buildroot构建过程中使用
*   在Buildroot之外构建时，例如直接调用交叉编译器或使用SDK时，不会使用`ccache`

你可以使用`BR2_USE_CCACHE`环境变量来覆盖此行为:当设置为`1`时，ccache的使用是启用的(Buildroot构建期间的默认值)，当未设置或设置为与`1`不同的值时，ccache的使用是禁用的。

### 8.13.4. Location of downloaded packages

Buildroot下载的各种压缩包都存储在`BR2_DL_DIR`中，默认情况下是`dl`目录。如果您想要保留一个完整的Buildroot版本，该版本可以使用相关的压缩包，您可以创建此目录的副本。这将允许您使用完全相同的版本重新生成工具链和目标文件系统。

如果你维护多个Buildroot树，最好有一个共享的下载位置。这可以通过将环境变量`BR2_DL_DIR`指向一个目录来实现。如果设置了这个值，那么Buildroot配置中的`BR2_DL_DIR`的值会被覆盖。下面这行代码应该添加到`<~/.bashrc>`中。

```bash
export BR2_DL_DIR=<shared download location>
```

下载地址也可以通过`.config`文件中的`BR2_DL_DIR`选项设置。与.config文件中的大多数选项不同，这个值会被`BR2_DL_DIR`环境变量覆盖。

### 8.13.5. Package-specific make targets

运行`make <package>`将构建并安装特定的包及其依赖项。

对于依赖于Buildroot基础架构的包，有许多特殊的make目标可以独立调用，如下所示:

```bash
make <package>-<target>
```

包构建目标如下(按执行顺序排列):

| 命令/<target>     | 描述                                                                            |
| ----------------- | ------------------------------------------------------------------------------ |
| `source`          | 获取源代码(下载压缩包、克隆源代码存储库等)                                         |
| `depends`         | 构建并安装构建包所需的所有依赖项                                                  |
| `extract`         | 将源代码放在包构建目录中(解压、复制源代码等)                                       |
| `patch`           | 应用补丁(如果有的话)                                                             |
| `configure`       | 如果有，请执行configure命令                                                      |
| `build`           | 运行编译命令                                                                    |
| `install-staging` | **target package:** 如果需要，在过渡目录中运行包的安装                            |
| `install-target`  | **target package:** 如果需要，在目标目录中运行包的安装                            |
| `install`         | **target package:** 运行前面的2条安装命令;**host package:** 在主机目录下运行安装包 |

此外，还有一些其他有用的make目标。

| 命令/<target>             | 描述                                                  |
| ------------------------- | ----------------------------------------------------- |
| `show-depends`            | 显示构建包所需的一阶依赖项                              |
| `show-recursive-depends`  | 递归地显示构建包所需的依赖项                            |
| `show-rdepends`           | 显示包的一阶反向依赖关系(即直接依赖它的包)               |
| `show-recursive-rdepends` | 递归地显示包的反向依赖关系(即直接或间接依赖它的包)        |
| `graph-depends`           | 在当前Buildroot配置的上下文中生成包的依赖关系图。有关依赖图的更多细节，请参阅[本节](#89-graphing-the-dependencies-between-packages)。|
| `graph-rdepends`          | 生成此包反向依赖关系的图(即直接或间接依赖于它的包)        |
| `dirclean`                | 删除整个包构建目录                                     |
| `reinstall`               | 重新运行安装命令                                       |
| `rebuild`                 | 重新运行编译命令——只有在使用`OVERRIDE_SRCDIR`功能或直接在构建目录中修改文件时，这才有意义  |
| `reconfigure`             | 重新运行配置命令，然后重新构建——只有在使用`OVERRIDE_SRCDIR`功能或直接在构建目录中修改文件时，这才有意义  |

### 8.13.6. Using Buildroot during development

Buildroot的正常操作是下载一个压缩包，提取它，配置，编译和安装在这个压缩包中找到的软件组件。源代码在`output/build/<package>-<version>`中提取，这是一个临时目录:每当使用`make clean`时，此目录将被完全删除，并在下一次`make`调用时重新创建。即使使用Git或Subversion存储库作为包源代码的输入，Buildroot也会从中创建一个tarball，然后像往常一样使用tarball。

当Buildroot主要用作集成工具来构建和集成嵌入式Linux系统的所有组件时，这种行为非常适合。然而，如果在系统的某些组件开发期间使用Buildroot，这种行为就不是很方便:相反，人们希望对某个包的源代码进行小更改，并能够使用Buildroot快速重建系统。

直接在`output/build/<package>-<version>`中进行更改不是一个合适的解决方案，因为该目录在`make clean`中被删除了。

因此，Buildroot为这个用例提供了一种特定的机制:`<pkg>_OVERRIDE_SRCDIR`机制。Buildroot读取一个覆盖文件，这允许用户告诉Buildroot某些包的源代码位置。

覆盖文件的默认位置是`$(CONFIG_DIR)/local.mk`，由`BR2_PACKAGE_OVERRIDE_FILE`配置选项定义。`$(CONFIG_DIR)`是Buildroot `.config`文件的位置，所以默认情况下`local.mk`与`.config`文件共存，这意味着:

*   在顶层的Buildroot源目录中进行树内构建(即，当不使用`O=`时)
*   在树外目录下进行构建(即使用`O=`时)

如果需要不同于这些默认值的位置，可以通过`BR2_PACKAGE_OVERRIDE_FILE`配置选项指定。

在这个覆盖文件中，Buildroot希望找到表单中的行:

```bash
<pkg1>_OVERRIDE_SRCDIR = /path/to/pkg1/sources
<pkg2>_OVERRIDE_SRCDIR = /path/to/pkg2/sources
```

例如:

```bash
LINUX_OVERRIDE_SRCDIR = /home/bob/linux/
BUSYBOX_OVERRIDE_SRCDIR = /home/bob/busybox/
```

当Buildroot发现对于给定的包，已经定义了`<pkg>_OVERRIDE_SRCDIR`时，它将不再尝试下载、提取和修补该包。相反，它将直接使用指定目录中的源代码，并且`make clean`不会涉及该目录。这允许将Buildroot指向你自己的目录，这些目录可以由Git、Subversion或任何其他版本控制系统管理。为了实现这一点，Buildroot将使用rsync将组件的源代码从指定的`<pkg>_OVERRIDE_SRCDIR` 复制到 `output/build/<package>-custom/`。

这种机制最好与`make <pkg>-rebuild` 和 `make <pkg>-reconfigure`目标一起使用。`make <pkg>-rebuild all`序列会将源代码从`<pkg>_OVERRIDE_SRCDIR` 到 `output/build/<package>-custom`(多亏了rsync，只复制了修改过的文件)，并重新启动这个包的构建过程。

在上面`linux`包的例子中，开发人员可以更改`/home/bob/linux`中的源代码，然后运行:

```bash
make linux-rebuild all
```

并且在几秒钟内就可以在`output/images`中获得更新的Linux内核映像。类似地，可以对`/home/bob/busybox`中的BusyBox源代码做如下修改:

```bash
make busybox-rebuild all
```

`output/images`中的根文件系统映像包含更新后的BusyBox。

大型项目的源代码树通常包含数百或数千个不需要用于构建的文件，但会减慢使用rsync复制源代码的过程。另外，还可以定义`<pkg>_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS`来跳过从源树同步某些文件。例如，当使用`webkitgtk`包时，以下代码将从本地WebKit源代码树中排除测试和树内构建:

```bash
WEBKITGTK_OVERRIDE_SRCDIR = /home/bob/WebKit
WEBKITGTK_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS = \
        --exclude JSTests --exclude ManualTests --exclude PerformanceTests \
        --exclude WebDriverTests --exclude WebKitBuild --exclude WebKitLibraries \
        --exclude WebKit.xcworkspace --exclude Websites --exclude Examples
```

默认情况下，Buildroot会跳过VCS工件的同步(例如，**.git**和**.svn**目录)。有些包喜欢在构建过程中让这些VCS目录可用，例如，用于自动确定版本信息的精确提交引用。要以较慢的速度撤销此内置过滤，请将以下目录添加回:

```bash
LINUX_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS = --include .git
```



# Chapter 9. Project-specific customization
-----------------------------------------

对于一个给定的项目，你可能需要执行的典型操作有:

*   配置Buildroot(包括构建选项和工具链、引导装载器、内核、包和文件系统映像类型选择)
*   配置其他组件，如Linux内核和BusyBox
*   自定义生成的目标文件系统
    
    *   添加或覆盖目标文件系统上的文件(使用`BR2_ROOTFS_OVERLAY`)
    *   修改或删除目标文件系统上的文件(使用`BR2_ROOTFS_POST_BUILD_SCRIPT`)
    *   在生成文件系统映像之前运行任意命令(使用`BR2_ROOTFS_POST_BUILD_SCRIPT`)
    *   设置文件权限和所有权(使用`BR2_ROOTFS_DEVICE_TABLE`)
    *   添加自定义设备节点(使用`BR2_ROOTFS_STATIC_DEVICE_TABLE`)
    
*   添加自定义用户账户(使用`BR2_ROOTFS_USERS_TABLES`)
*   在生成文件系统映像后运行任意命令(使用`BR2_ROOTFS_POST_IMAGE_SCRIPT`)
*   为某些包添加特定项目的补丁(使用`BR2_GLOBAL_PATCH_DIR`)
*   添加特定于项目的包

关于此类特定于项目的自定义的重要注意事项:请仔细考虑哪些更改确实是特定于项目的，哪些更改对项目之外的开发人员也有用。Buildroot社区高度推荐并鼓励向官方Buildroot项目上传改进、软件包和董事会支持。当然，有时不可能或不希望上游，因为更改是高度特定或专有的。

本章将介绍如何在Buildroot中针对项目进行定制，以及如何存储这些定制，以便在运行make clean命令后以可重复的方式构建相同的镜像。通过遵循推荐的策略，你甚至可以使用相同的Buildroot树来构建多个不同的项目!


## 9.1. Recommended directory structure
------------------------------------

为项目定制Buildroot时，需要创建一个或多个特定于项目的文件，这些文件需要存储在某个地方。虽然这些文件中的大多数可以放置在任何位置，因为它们的路径要在Buildroot配置中指定，但Buildroot开发人员建议使用本节中描述的特定目录结构。

与这个目录结构正交的是，你可以选择将这个结构本身放置在哪里:在Buildroot树内部，或者在它外部使用br2-external树。两种选择都是有效的，选择取决于你。

```bash
+-- board/
|   +-- <company>/
|       +-- <boardname>/
|           +-- linux.config
|           +-- busybox.config
|           +-- <other configuration files>
|           +-- post_build.sh
|           +-- post_image.sh
|           +-- rootfs_overlay/
|           |   +-- etc/
|           |   +-- <some files>
|           +-- patches/
|               +-- foo/
|               |   +-- <some patches>
|               +-- libbar/
|                   +-- <some other patches>
|
+-- configs/
|   +-- <boardname>_defconfig
|
+-- package/
|   +-- <company>/
|       +-- Config.in (if not using a br2-external tree)
|       +-- <company>.mk (if not using a br2-external tree)
|       +-- package1/
|       |    +-- Config.in
|       |    +-- package1.mk
|       +-- package2/
|           +-- Config.in
|           +-- package2.mk
|
+-- Config.in (if using a br2-external tree)
+-- external.mk (if using a br2-external tree)
+-- external.desc (if using a br2-external tree)
```

本章将进一步给出上述文件的详细信息。

注意:如果您选择将此结构放置在Buildroot树之外，但放置在br2-external树中，则 `company` 和可能的 `boardname` 组件可能是多余的，可以省略。

### 9.1.1. Implementing layered customizations

对于一个用户来说，有几个相关的项目在一定程度上需要相同的自定义是很常见的。建议使用分层定制方法，而不是为每个项目重复这些定制，如本节所述。

几乎Buildroot中所有可用的自定义方法，如后构建脚本和根文件系统覆盖，都接受一个以空格分隔的项目列表。指定的项总是按照从左到右的顺序处理。通过创建多个这样的项，一个用于常见的定制，另一个用于真正特定于项目的定制，您可以避免不必要的重复。每一层通常由`board/<company>/`中的单独目录体现。根据你的项目，你甚至可以引入两个以上的层。

一个用户有两个自定义层common和fooboard的目录结构示例如下:

```bash
+-- board/
    +-- <company>/
        +-- common/
        |   +-- post_build.sh
        |   +-- rootfs_overlay/
        |   |   +-- ...
        |   +-- patches/
        |       +-- ...
        |
        +-- fooboard/
            +-- linux.config
            +-- busybox.config
            +-- <other configuration files>
            +-- post_build.sh
            +-- rootfs_overlay/
            |   +-- ...
            +-- patches/
                +-- ...
```

例如，如果用户将`BR2_GLOBAL_PATCH_DIR`配置选项设置为:

```bash
BR2_GLOBAL_PATCH_DIR="board/<company>/common/patches board/<company>/fooboard/patches"
```

然后首先应用common层的补丁，然后应用fooboard层的补丁。


## 9.2. Keeping customizations outside of Buildroot
------------------------------------------------

正如在[第9.1节，“推荐的目录结构”](#91-recommended-directory-structure)中简要提到的那样，您可以将特定于项目的自定义设置放在两个位置:

*   直接在Buildroot树中，通常使用版本控制系统中的分支来维护它们，以便轻松升级到新的Buildroot版本。
*   在Buildroot树之外，使用br2-external机制。这种机制允许将包配置、板级支持和配置文件保存在Buildroot树之外，同时仍然将它们很好地集成到构建逻辑中。我们将这个位置称为br2-external树。本节解释如何使用br2-external机制，以及在br2-external树中提供什么。

通过将`BR2_EXTERNAL` make变量设置为要使用的br2-external树的路径，可以告诉Buildroot使用一棵或多棵br2-external树。它可以传递给任何Buildroot的`make`调用。它会自动保存在输出目录中隐藏的`.br2-external.mk`文件中。正因为如此，我们不需要在每次调用`make`时都传递`BR2_EXTERNAL`。不过，只要传入一个新值，就可以随时修改它，也可以传入一个空值来删除它。

**注意.** br2-external树的路径可以是绝对路径，也可以是相对路径。如果传递的是相对路径，请注意，它是相对于主要的Buildroot源目录进行解释的，**而不是** Buildroot输出目录。

**注意:** 如果在Buildroot 2016.11之前使用br2-external树，则需要在Buildroot 2016.11之后使用它之前将其转换。请参阅[第27.2节，“迁移到2016.11”](#272-migrating-to-201611)以获取帮助。

一些例子:

```bash
buildroot/ $ make BR2_EXTERNAL=/path/to/foo menuconfig
```

从现在开始，从`/path/to/foo` br2-external树中的定义将被使用:

```bash
buildroot/ $ make
buildroot/ $ make legal-info
```

我们可以随时切换到另一个br2-external树:

```bash
buildroot/ $ make BR2_EXTERNAL=/where/we/have/bar xconfig
```

我们也可以使用多个br2-external树:

```bash
buildroot/ $ make BR2_EXTERNAL=/path/to/foo:/where/we/have/bar menuconfig
```

或者禁用任何br2-external树:

```bash
buildroot/ $ make BR2_EXTERNAL= xconfig
```

### 9.2.1. Layout of a br2-external tree

一个br2-external树必须至少包含这3个文件，将在以下章节描述。

*   `external.desc`
*   `external.mk`
*   `Config.in`

除了这些强制文件，br2-external树中可能还存在其他可选内容，如`configs/` 或 `provides/`目录。它们也将在接下来的章节中进行描述。

稍后还将描述br2-external树布局的完整示例。

#### `external.desc`文件

该文件描述了br2-external树: 该br2-external树的名称和描述。

该文件的格式是以行为单位的，每行以一个关键字开头，然后是一个冒号和一个或多个空格，最后是分配给该关键字的值。目前可以识别的关键字有两个:

*   `name`, 它定义了br2-external树的名称。这个名字只能使用ASCII字符，并且只能包含`[A-Za-z0-9_]`;禁止使用任何其他字符。Buildroot将变量`BR2_EXTERNAL_$(NAME)_PATH`设置为br2-external树的绝对路径，这样你就可以使用它来引用你的br2-external树。这个变量在Kconfig和Makefile中都是可用的，因此你可以使用它来获取Kconfig文件的源(见下文)，也可以使用它来包含其他Makefile(见下文)或引用br2-external树中的其他文件(如数据文件)。
    
    **注意:** 因为可以一次使用多个br2-external树，Buildroot使用这个名称为每个树生成变量。这个名字是用来标识你的br2-external树的，所以尽量想出一个真正描述你的br2-external树的名字，以便它相对唯一，这样它就不会与另一个br2-external树的另一个名字冲突，特别是如果你打算以某种方式与第三方共享你的br2-external树或使用第三方的br2-external树。
    
*   `desc`, 可选，提供了br2-external树的简短描述。它应该在一行内，主要是自由格式(见下文)，并且在显示br2-external树的信息时使用(例如，在defconfig文件列表的上方，或作为menuconfig中的提示);因此，它应该相对简短(40个字符可能是一个不错的上限)。描述可以在`BR2_EXTERNAL_$(NAME)_DESC`变量中找到。

名称和相应的`BR2_EXTERNAL_$(NAME)_PATH`变量的例子:

*   `FOO` → `BR2_EXTERNAL_FOO_PATH`
*   `BAR_42` → `BR2_EXTERNAL_BAR_42_PATH`

在下面的示例中，假定名称设置为`BAR_42`。

**注意:** `BR2_EXTERNAL_$(NAME)_PATH` 和 `BR2_EXTERNAL_$(NAME)_DESC`都可以在Kconfig文件和makefile中使用。它们也会在环境中导出，因此可以在post-build、post-image和in-fakeroot脚本中使用。

#### `Config.in` 和 `external.mk`文件

这些文件(每个文件可能是空的)可用于定义包配方(即:`foo/Config.in` 和 `foo/foo.mk`用于打包在Buildroot本身中的包)或其他自定义配置选项或逻辑。

Buildroot自动包含每个br2-external树中的`Config.in`，使其出现在顶层配置菜单中，并包含每个br2-external树中的`external.mk`和其余的makefile逻辑。

它的主要用途是存储包配方。推荐的方法是写一个`Config.in`文件，看起来像这样:

```bash
source "$BR2_EXTERNAL_BAR_42_PATH/package/package1/Config.in"
source "$BR2_EXTERNAL_BAR_42_PATH/package/package2/Config.in"
```

然后，创建一个如下所示的`external.mk`文件:

```bash
include $(sort $(wildcard $(BR2_EXTERNAL_BAR_42_PATH)/package/*/*.mk))
```

然后在`$(BR2_EXTERNAL_BAR_42_PATH)/package/package1` 和 `$(BR2_EXTERNAL_BAR_42_PATH)/package/package2`中创建正常的Buildroot包，如[第18章，向Buildroot添加新包](#chapter-18-adding-new-packages-to-buildroot)中所述。如果你愿意，也可以将包分组在名为<boardname>并相应地调整上述路径。

你也可以在`Config.in`中定义自定义配置选项，在`external.mk`中定义自定义make逻辑。

#### `configs/`目录

你可以将Buildroot的defconfig存储在br2-external树的`configs`子目录中。Buildroot会自动将它们显示在`make list-defconfigs`的输出中，并允许它们使用普通的`make <name>_defconfig`命令加载。它们将在make list-defconfigs输出中可见，位于包含定义它们的br2-external树名称的`External configs`标签下方。

**注意:** 如果一个defconfig文件存在于多个br2-external树中，那么将使用最后一个br2-external树中的那个。因此，可以覆盖Buildroot或另一个br2-external树中捆绑的defconfig。

#### `provides/`目录

对于某些包，Buildroot提供了在 API 兼容的此类包的两个（或多个）实现之间进行选择。例如，可以选择 libjpeg 或 jpeg-turbo；openssl 和 libressl 之间有一个；可以选择已知的预配置工具链之一……

br2-external可以通过提供一组定义这些选项的文件来扩展这些选项:

*   `provides/toolchains.in` 定义预先配置的工具链，然后将在工具链选择中列出;
*   `provides/jpeg.in` 定义了可选的libjpeg实现;
*   `provides/openssl.in` 定义了可选的openssl实现;
*   `provides/skeleton.in` 定义可选的骨架实现;
*   `provides/init.in` 定义了可选的init系统实现，这可以用来为init选择一个默认的框架。

#### 自由格式的内容

你可以将所有特定于板的配置文件存储在那里，例如内核配置、根文件系统覆盖层或任何其他Buildroot允许设置位置的配置文件(通过使用`BR2_EXTERNAL_$(NAME)_PATH`变量)。例如，您可以将路径设置为全局补丁目录、rootfs覆盖层和内核配置文件，如下所示(例如，通过运行`make menuconfig`并填写这些选项):

```bash
BR2_GLOBAL_PATCH_DIR=$(BR2_EXTERNAL_BAR_42_PATH)/patches/
BR2_ROOTFS_OVERLAY=$(BR2_EXTERNAL_BAR_42_PATH)/board/<boardname>/overlay/
BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE=$(BR2_EXTERNAL_BAR_42_PATH)/board/<boardname>/kernel.config
```

#### 额外的Linux内核扩展

额外的Linux内核扩展(参见[第18.22.2节，“linux-内核-扩展”](#18222-linux-kernel-extensions))可以通过将它们存储在br2-external树的根目录下的`linux/`目录中来添加。

#### 示例布局

下面是一个使用br2-external所有特性的示例布局(示例内容显示在上面的文件中，与解释br2-external树相关;当然，这完全是为了说明而编造的):

```bash
/path/to/br2-ext-tree/
  |- external.desc
  |     |name: BAR_42
  |     |desc: Example br2-external tree
  |     `----
  |
  |- Config.in
  |     |source "$BR2_EXTERNAL_BAR_42_PATH/toolchain/toolchain-external-mine/Config.in.options"
  |     |source "$BR2_EXTERNAL_BAR_42_PATH/package/pkg-1/Config.in"
  |     |source "$BR2_EXTERNAL_BAR_42_PATH/package/pkg-2/Config.in"
  |     |source "$BR2_EXTERNAL_BAR_42_PATH/package/my-jpeg/Config.in"
  |     |
  |     |config BAR_42_FLASH_ADDR
  |     |    hex "my-board flash address"
  |     |    default 0x10AD
  |     `----
  |
  |- external.mk
  |     |include $(sort $(wildcard $(BR2_EXTERNAL_BAR_42_PATH)/package/*/*.mk))
  |     |include $(sort $(wildcard $(BR2_EXTERNAL_BAR_42_PATH)/toolchain/*/*.mk))
  |     |
  |     |flash-my-board:
  |     |    $(BR2_EXTERNAL_BAR_42_PATH)/board/my-board/flash-image \
  |     |        --image $(BINARIES_DIR)/image.bin \
  |     |        --address $(BAR_42_FLASH_ADDR)
  |     `----
  |
  |- package/pkg-1/Config.in
  |     |config BR2_PACKAGE_PKG_1
  |     |    bool "pkg-1"
  |     |    help
  |     |      Some help about pkg-1
  |     `----
  |- package/pkg-1/pkg-1.hash
  |- package/pkg-1/pkg-1.mk
  |     |PKG_1_VERSION = 1.2.3
  |     |PKG_1_SITE = /some/where/to/get/pkg-1
  |     |PKG_1_LICENSE = blabla
  |     |
  |     |define PKG_1_INSTALL_INIT_SYSV
  |     |    $(INSTALL) -D -m 0755 $(PKG_1_PKGDIR)/S99my-daemon \
  |     |                          $(TARGET_DIR)/etc/init.d/S99my-daemon
  |     |endef
  |     |
  |     |$(eval $(autotools-package))
  |     `----
  |- package/pkg-1/S99my-daemon
  |
  |- package/pkg-2/Config.in
  |- package/pkg-2/pkg-2.hash
  |- package/pkg-2/pkg-2.mk
  |
  |- provides/jpeg.in
  |     |config BR2_PACKAGE_MY_JPEG
  |     |    bool "my-jpeg"
  |     `----
  |- package/my-jpeg/Config.in
  |     |config BR2_PACKAGE_PROVIDES_JPEG
  |     |    default "my-jpeg" if BR2_PACKAGE_MY_JPEG
  |     `----
  |- package/my-jpeg/my-jpeg.mk
  |     |# This is a normal package .mk file
  |     |MY_JPEG_VERSION = 1.2.3
  |     |MY_JPEG_SITE = https://example.net/some/place
  |     |MY_JPEG_PROVIDES = jpeg
  |     |$(eval $(autotools-package))
  |     `----
  |
  |- provides/init.in
  |     |config BR2_INIT_MINE
  |     |    bool "my custom init"
  |     |    select BR2_PACKAGE_MY_INIT
  |     |    select BR2_PACKAGE_SKELETON_INIT_MINE if BR2_ROOTFS_SKELETON_DEFAULT
  |     `----
  |
  |- provides/skeleton.in
  |     |config BR2_ROOTFS_SKELETON_MINE
  |     |    bool "my custom skeleton"
  |     |    select BR2_PACKAGE_SKELETON_MINE
  |     `----
  |- package/skeleton-mine/Config.in
  |     |config BR2_PACKAGE_SKELETON_MINE
  |     |    bool
  |     |    select BR2_PACKAGE_HAS_SKELETON
  |     |
  |     |config BR2_PACKAGE_PROVIDES_SKELETON
  |     |    default "skeleton-mine" if BR2_PACKAGE_SKELETON_MINE
  |     `----
  |- package/skeleton-mine/skeleton-mine.mk
  |     |SKELETON_MINE_ADD_TOOLCHAIN_DEPENDENCY = NO
  |     |SKELETON_MINE_ADD_SKELETON_DEPENDENCY = NO
  |     |SKELETON_MINE_PROVIDES = skeleton
  |     |SKELETON_MINE_INSTALL_STAGING = YES
  |     |$(eval $(generic-package))
  |     `----
  |
  |- provides/toolchains.in
  |     |config BR2_TOOLCHAIN_EXTERNAL_MINE
  |     |    bool "my custom toolchain"
  |     |    depends on BR2_some_arch
  |     |    select BR2_INSTALL_LIBSTDCPP
  |     `----
  |- toolchain/toolchain-external-mine/Config.in.options
  |     |if BR2_TOOLCHAIN_EXTERNAL_MINE
  |     |config BR2_TOOLCHAIN_EXTERNAL_PREFIX
  |     |    default "arch-mine-linux-gnu"
  |     |config BR2_PACKAGE_PROVIDES_TOOLCHAIN_EXTERNAL
  |     |    default "toolchain-external-mine"
  |     |endif
  |     `----
  |- toolchain/toolchain-external-mine/toolchain-external-mine.mk
  |     |TOOLCHAIN_EXTERNAL_MINE_SITE = https://example.net/some/place
  |     |TOOLCHAIN_EXTERNAL_MINE_SOURCE = my-toolchain.tar.gz
  |     |$(eval $(toolchain-external-package))
  |     `----
  |
  |- linux/Config.ext.in
  |     |config BR2_LINUX_KERNEL_EXT_EXAMPLE_DRIVER
  |     |    bool "example-external-driver"
  |     |    help
  |     |      Example external driver
  |     |---
  |- linux/linux-ext-example-driver.mk
  |
  |- configs/my-board_defconfig
  |     |BR2_GLOBAL_PATCH_DIR="$(BR2_EXTERNAL_BAR_42_PATH)/patches/"
  |     |BR2_ROOTFS_OVERLAY="$(BR2_EXTERNAL_BAR_42_PATH)/board/my-board/overlay/"
  |     |BR2_ROOTFS_POST_IMAGE_SCRIPT="$(BR2_EXTERNAL_BAR_42_PATH)/board/my-board/post-image.sh"
  |     |BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE="$(BR2_EXTERNAL_BAR_42_PATH)/board/my-board/kernel.config"
  |     `----
  |
  |- patches/linux/0001-some-change.patch
  |- patches/linux/0002-some-other-change.patch
  |- patches/busybox/0001-fix-something.patch
  |
  |- board/my-board/kernel.config
  |- board/my-board/overlay/var/www/index.html
  |- board/my-board/overlay/var/www/my.css
  |- board/my-board/flash-image
  `- board/my-board/post-image.sh
        |#!/bin/sh
        |generate-my-binary-image \
        |    --root ${BINARIES_DIR}/rootfs.tar \
        |    --kernel ${BINARIES_DIR}/zImage \
        |    --dtb ${BINARIES_DIR}/my-board.dtb \
        |    --output ${BINARIES_DIR}/image.bin
        `----
```

br2-external树将在menuconfig中可见(扩展布局):

```bash
External options  --->
    *** Example br2-external tree (in /path/to/br2-ext-tree/)
    [ ] pkg-1
    [ ] pkg-2
    (0x10AD) my-board flash address
```

如果您使用不止一棵 br2-external 树，它看起来像（布局展开，第二棵名为`FOO_27`，但`external.desc`中没有`desc:`字段）：

```bash
External options  --->
    Example br2-external tree  --->
        *** Example br2-external tree (in /path/to/br2-ext-tree)
        [ ] pkg-1
        [ ] pkg-2
        (0x10AD) my-board flash address
    FOO_27  --->
        *** FOO_27 (in /path/to/another-br2-ext)
        [ ] foo
        [ ] bar
```

此外，jpeg提供程序将在jpeg选项中可见:

```bash
Target packages  --->
    Libraries  --->
        Graphics  --->
            [*] jpeg support
                jpeg variant ()  --->
                    ( ) jpeg
                    ( ) jpeg-turbo
                        *** jpeg from: Example br2-external tree ***
                    (X) my-jpeg
                        *** jpeg from: FOO_27 ***
                    ( ) another-jpeg
```

对于工具链也是类似的:

```bash
Toolchain  --->
    Toolchain ()  --->
        ( ) Custom toolchain
            *** Toolchains from: Example br2-external tree ***
        (X) my custom toolchain
```

**注意.** `toolchain/toolchain-external-mine/Config.in.options`中的工具链选项将不会出现在`Toolchain`菜单中。它们必须在br2-external的顶层`Config.in`中显式包含，因此将出现在`External options`菜单中。


## 9.3. Storing the Buildroot configuration
----------------------------------------

Buildroot配置可以使用命令`make savedefconfig`存储。

这将通过删除具有默认值的配置选项来剥离Buildroot配置。结果存储在名为`defconfig`的文件中。如果你想将其保存在另一个地方，请更改Buildroot配置本身中的`BR2_DEFCONFIG`选项，或者使用`make savedefconfig BR2_DEFCONFIG=<path-to-defconfig>`调用make。

推荐存储这个defconfig的地方是`configs/<boardname>_defconfig`。如果你遵循这个建议，配置将列在`make list-defconfigs`中，并且可以通过运行`make <boardname>_defconfig`再次进行设置。

或者，你可以将文件复制到任何其他位置，并使用`make defconfig BR2_DEFCONFIG=<path-to-defconfig-file>`重新构建。


## 9.4. Storing the configuration of other components
--------------------------------------------------

BusyBox、Linux内核、Barebox、U-Boot和uClibc的配置文件也应该被保存。对于这些组件中的每一个，都有一个Buildroot配置选项指向一个输入配置文件，例如:`BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE`。要存储它们的配置，请将这些配置选项设置为要保存配置文件的路径，然后使用下面描述的辅助目标来实际存储配置。

如[第9.1节，“推荐的目录结构”](#91-recommended-directory-structure)所述，存储这些配置文件的推荐路径是`board/<company>/<boardname>/foo.config`。

确保您在更改`BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE`等选项之前创建一个配置文件。否则，Buildroot将尝试访问这个还不存在的配置文件，并将失败。你可以通过运行`make linux-menuconfig`等命令创建配置文件。

Buildroot提供了一些辅助目标，使配置文件的保存更容易。

*   `make linux-update-defconfig`将linux配置保存到`BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE`指定的路径下。它通过删除默认值来简化配置文件。但这仅适用于内核版本2.6.33之后的版本。对于早期的内核，请使用`make linux-update-config`。
*   `make busybox-update-config`将busybox配置保存到`BR2_PACKAGE_BUSYBOX_CONFIG`指定的路径下。
*   `make uclibc-update-config`将uClibc配置保存到`BR2_UCLIBC_CONFIG`指定的路径中。
*   `make barebox-update-defconfig`将barebox配置保存到`BR2_TARGET_BAREBOX_CUSTOM_CONFIG_FILE`指定的路径下。
*   `make uboot-update-defconfig`将U-Boot配置保存到`BR2_TARGET_UBOOT_CUSTOM_CONFIG_FILE`指定的路径中。
*   对于at91bootstrap3来说，不存在辅助工具，所以你必须手动将配置文件复制到`BR2_TARGET_AT91BOOTSTRAP3_CUSTOM_CONFIG_FILE`。


## 9.5. Customizing the generated target filesystem
------------------------------------------------

除了通过`make *config`更改配置之外，还有一些其他方法可以自定义生成的目标文件系统。

推荐的两种方法是根文件系统覆盖和post构建脚本，这两种方法可以共存。

根文件系统覆盖(`BR2_ROOTFS_OVERLAY`)

文件系统覆盖层(filesystem overlay)是一个文件树，在目标文件系统构建后直接复制到目标文件系统上。要启用此功能，请将配置选项`BR2_ROOTFS_OVERLAY`(在`System configuration`菜单中)设置为覆盖的根目录。你甚至可以指定多个叠加层，以空格分隔。如果你指定了一个相对路径，它将是相对于Buildroot树的根。版本控制系统的隐藏目录，如`.git`、`.svn`、`.hg`等名为`.empty`的文件和以`~`结尾的文件不会被复制。

当启用`BR2_ROOTFS_MERGED_USR`时，覆盖网不能包含/bin、/lib或/sbin目录，因为Buildroot会将它们创建为指向/usr中相关文件夹的符号链接。在这种情况下，如果覆盖层上有任何程序或库，它们应该放在/usr/bin、/usr/sbin和/usr/lib中

如[第9.1节，“推荐的目录结构”](#91-recommended-directory-structure)所示，这个覆盖层的推荐路径是`board/<company>/<boardname>/rootfs-overlay`。

Post-build脚本(`BR2_ROOTFS_POST_BUILD_SCRIPT`)

Post-build脚本是在Buildroot构建所有选定软件之后，但在组装rootfs映像之前调用的shell脚本。要启用此功能，请在配置选项`BR2_ROOTFS_POST_BUILD_SCRIPT`(在`System configuration`菜单中)中指定以空格分隔的post-build脚本列表。如果你指定了一个相对路径，它将是相对于Buildroot树的根。

使用post-build脚本，你可以删除或修改目标文件系统中的任何文件。然而，您应该谨慎使用此功能。每当你发现某个包生成了错误或不需要的文件时，你应该修复该包，而不是使用一些post-build清理脚本来解决它。

如[第9.1节，“推荐的目录结构”](#91-recommended-directory-structure)所示，这个脚本的推荐路径是`board/<company>/<boardname>/post_build.sh`。

构建后的脚本以主Buildroot树作为当前工作目录运行。到目标文件系统的路径作为每个脚本的第一个参数传递。如果配置选项`BR2_ROOTFS_POST_SCRIPT_ARGS`不为空，这些参数也会传递给脚本。所有脚本都将被传递完全相同的参数集，不可能给每个脚本传递不同的参数集。

此外，你也可以使用这些环境变量:

*   `BR2_CONFIG`: Buildroot .config文件的路径
*   `CONFIG_DIR`: 包含.config文件的目录，以及要使用的顶层Buildroot Makefile(对于树内构建和树外构建都是正确的)
*   `HOST_DIR`, `STAGING_DIR`, `TARGET_DIR`: 参见 [第18.6.2节，“`generic-package`引用”](#1862-generic-package-reference)
*   `BUILD_DIR`: 解压和构建包的目录
*   `BINARIES_DIR`: 存储所有二进制文件(即图像)的地方
*   `BASE_DIR`: 基本输出目录

下面将描述另外三种定制目标文件系统的方法，但不推荐使用。

**直接修改目标文件系统**

对于临时修改，可以直接修改目标文件系统并重新构建映像。目标文件系统位于`output/target/`目录下。做出更改后，运行`make`来重建目标文件系统镜像。

这个方法允许你对目标文件系统做任何操作，但是如果你需要使用`make clean`清理你的Buildroot树，这些更改将会丢失。这种清理在某些情况下是必要的，请参阅[8.2节，“了解何时需要完全重建”](#82-understanding-when-a-full-rebuild-is-necessary)以了解详情。因此，此解决方案仅适用于快速测试:更改不会在`make clean`命令中生效。一旦你验证了你的更改，你应该使用根文件系统覆盖层或post-build脚本，确保它们在`make clean`之后仍然有效。

**自定义目标骨架 (`BR2_ROOTFS_SKELETON_CUSTOM`)**

根文件系统映像是根据目标框架创建的，所有包都会在其上安装它们的文件。在构建和安装任何包之前，框架被复制到目标目录`output/target`。默认的目标框架提供了标准的Unix文件系统布局以及一些基本的init脚本和配置文件。

如果默认的骨架(在`system/skeleton`下提供)不符合你的需求，你通常会使用根文件系统覆盖或post-build脚本来适应它。然而，如果默认的骨架与您所需要的完全不同，那么使用自定义的骨架可能更合适。

要启用此功能，请启用配置选项`BR2_ROOTFS_SKELETON_CUSTOM`，并将`BR2_ROOTFS_SKELETON_CUSTOM_PATH`设置为自定义骨架的路径。这两个选项都可以在`System configuration`菜单中找到。如果你指定了一个相对路径，它将是相对于Buildroot树的根。

自定义的框架不需要包含/bin、/lib或/sbin目录，因为它们是在构建过程中自动创建的。当启用了`BR2_ROOTFS_MERGED_USR`时，自定义框架不能包含/bin、/lib或/sbin目录，因为Buildroot会将它们创建为指向/usr中相关文件夹的符号链接。在这种情况下，如果框架中有任何程序或库，它们应该放置在/usr/bin、/usr/sbin和/usr/lib中

不推荐使用这种方法，因为它重复了整个框架，这将导致无法利用在以后的Buildroot版本中对默认框架进行的修复或改进。

**Post-fakeroot脚本(`BR2_ROOTFS_POST_FAKEROOT_SCRIPT`)**

当聚合最终图像时，过程的某些部分需要root权限:在`/dev`中创建设备节点，设置文件和目录的权限或所有权……为了避免需要实际的root权限，Buildroot使用`fakeroot`来模拟root权限。这并不是真正成为root的完全替代，但足以满足Buildroot的需求。

Post-fakeroot脚本是在fakeroot阶段结束时调用的shell脚本，就在调用文件系统映像生成器之前。因此，它们在fakeroot上下文中被调用。

Post-fakeroot脚本在需要调整文件系统以进行通常只有root用户才能使用的修改时非常有用。

**注意:** 建议使用现有的机制来设置文件权限或在`/dev`中创建条目(参见[9.5.1节，“设置文件权限和所有权并添加自定义设备节点”](#951-setting-file-permissions-and-ownership-and-adding-custom-devices-nodes))或创建用户(参见[9.6节，“添加自定义用户账户”](#96-adding-custom-user-accounts))

**注意:** post-build脚本(上面)和fakeroot脚本之间的区别在于，post-build脚本不会在fakeroot上下文中调用。

**注意:** 使用`fakeroot`并不能绝对替代真正的root。`fakeroot`只会伪造文件访问权限和类型(普通、块设备或字符设备……)以及uid/gid;这些是在内存中模拟的。

### 9.5.1. Setting file permissions and ownership and adding custom devices nodes

有时需要对文件或设备节点设置特定的权限或所有权。例如，某些文件可能需要由root拥有。因为post-build脚本不是以root身份运行的，所以除非在post-build脚本中显式使用fakeroot，否则无法从root进行更改。

相反，Buildroot提供了对所谓权限表的支持。要使用此功能，请将配置选项`BR2_ROOTFS_DEVICE_TABLE`设置为空格分隔的权限表列表，即遵循[makedev语法](#chapter-25-makedev-syntax-documentation)的普通文本文件。

如果你使用的是静态设备表(即不使用`devtmpfs`、`mdev`或`(e)udev`)，那么你可以使用相同的语法在所谓的设备表中添加设备节点。要使用此功能，请将配置选项`BR2_ROOTFS_STATIC_DEVICE_TABLE`设置为一个由空格分隔的设备表列表。

如[第9.1节，“推荐的目录结构”](#91-recommended-directory-structure)所示，此类文件的推荐位置是`board/<company>/<boardname>/`。

需要注意的是，如果特定的权限或设备节点与特定的应用程序相关，则应该在包的`.mk`文件中设置变量`FOO_PERMISSIONS` 和 `FOO_DEVICES`(参见[第18.6.2节，“`generic-package`引用”](#1862-generic-package-reference))。


## 9.6. Adding custom user accounts
--------------------------------

有时需要在目标系统中添加特定用户。为了满足这个需求，Buildroot提供了对所谓用户表的支持。要使用此功能，请将配置选项`BR2_ROOTFS_USERS_TABLES`设置为空格分隔的用户表列表，遵循[makeusers语法](#chapter-26-makeusers-syntax-documentation)的普通文本文件。

如[第9.1节，“推荐的目录结构”](#91-recommended-directory-structure)所示，此类文件的推荐位置是`board/<company>/<boardname>/`。

需要注意的是，如果自定义用户与特定应用程序相关，则应该在包的`.mk`文件中设置变量`FOO_USERS`(参见[第18.6.2节，“`generic-package`引用”](#1862-generic-package-reference))。


## 9.7. Customization after the images have been created
-------------------------------------------------------

虽然在构建文件系统映像、内核和引导装载器之前运行post-build脚本([9.5节，“自定义生成的目标文件系统”](#95-customizing-the-generated-target-filesystem))，但在创建所有映像之后，可以使用**post-image scripts**执行一些特定的操作。

例如，Post-image脚本可以用于在NFS服务器导出的位置自动提取根文件系统的压缩包，或者创建一个捆绑根文件系统和内核映像的特殊固件映像，或者用于项目所需的任何其他自定义操作。

要启用此功能，请在配置选项`BR2_ROOTFS_POST_IMAGE_SCRIPT`(在`System configuration`菜单中)中指定空格分隔的post-image脚本列表。如果你指定了一个相对路径，它将是相对于Buildroot树的根。

就像post-build脚本一样，post-image脚本也以主Buildroot树作为当前工作目录运行。`images`输出目录的路径作为第一个参数传递给每个脚本。如果配置选项`BR2_ROOTFS_POST_SCRIPT_ARGS`不为空，这些参数也会传递给脚本。所有脚本都将被传递完全相同的参数集，不可能给每个脚本传递不同的参数集。

同样，就像构建后的脚本一样，这些脚本可以访问环境变量`BR2_CONFIG`, `HOST_DIR`, `STAGING_DIR`, `TARGET_DIR`, `BUILD_DIR`, `BINARIES_DIR`, `CONFIG_DIR` 和 `BASE_DIR`。

post-image脚本将作为执行Buildroot的用户执行，该用户通常不应该是root用户。因此，在这些脚本中任何需要root权限的操作都需要特殊处理(使用fakeroot或sudo)，这留给脚本开发人员。


## 9.8. Adding project-specific patches
------------------------------------

有时，在Buildroot中提供的包之上，为包应用额外的补丁是有用的。例如，这可能用于支持项目中的自定义功能，或者在开发新架构时。

`BR2_GLOBAL_PATCH_DIR`配置选项可用于指定由空格分隔的一个或多个包含包补丁的目录列表。

对于特定包`<packageversion>`的特定版本`<packagename>`，补丁从`BR2_GLOBAL_PATCH_DIR`应用如下:

1.  对于`BR2_GLOBAL_PATCH_DIR`中存在的每个目录`<global-patch-dir>`， `<package-patch-dir>`将被确定如下:
    
    *   `<global-patch-dir>/<packagename>/<packageversion>/` 如果该目录存在。
    *   否则, `<global-patch-dir>/<packagename>` 如果该目录存在。
    
2.  然后，补丁将从`<package-patch-dir>`中应用，如下所示:
    
    *   如果包目录中存在`series`文件，则根据`series`文件应用补丁;
    *   否则，补丁文件匹配`*.patch`按字母顺序应用。因此，为了确保它们按照正确的顺序应用，强烈建议将补丁文件命名为:`<number>-<description>.patch`，其中`<number>`为应用顺序。
    

有关如何为包应用补丁的信息，请参阅[19.2节，“如何应用补丁”](#192-how-patches-are-applied)

`BR2_GLOBAL_PATCH_DIR`选项是为包指定自定义补丁目录的首选方法。它可以用来为buildroot中的任何包指定补丁目录。它还应该用来代替U-Boot和Barebox等包的自定义补丁目录选项。通过这样做，它将允许用户从一个顶层目录管理他们的补丁。

`BR2_GLOBAL_PATCH_DIR`是指定自定义补丁的首选方法，但例外情况是`BR2_LINUX_KERNEL_PATCH`。应该使用`BR2_LINUX_KERNEL_PATCH`来指定URL中可用的内核补丁。**注意:** `BR2_LINUX_KERNEL_PATCH`指定在`BR2_GLOBAL_PATCH_DIR`中可用补丁之后应用的内核补丁，因为它是通过Linux包的后补丁钩子完成的。


## 9.9. Adding project-specific packages
-------------------------------------

一般来说，任何新的包都应该直接添加到`package`目录中，并提交到Buildroot上游项目。如何向Buildroot中添加包在[第18章，向Buildroot添加新包](#chapter-18-adding-new-packages-to-buildroot)中有详细说明，这里不再赘述。然而，您的项目可能需要一些无法上传到的专有包。本节将解释如何将此类特定于项目的包保存在特定于项目的目录中。

如[9.1节“推荐的目录结构”](#91-recommended-directory-structure)所示，项目特定包的推荐位置是`package/<company>/`。如果你正在使用br2-external tree特性(参见[9.2节，在Buildroot之外保留自定义项](#92-keeping-customizations-outside-of-buildroot))，建议将它们放在br2-external tree中名为`package/`的子目录中。

但是，除非我们执行一些额外的步骤，否则Buildroot不会知道这个位置中的包。正如在[第18章，向Buildroot添加新包](#chapter-18-adding-new-packages-to-buildroot)中所解释的那样，Buildroot中的包基本上由两个文件组成:一个`.mk`文件(描述如何构建包)和一个`Config.in`文件(描述此包的配置选项)。

Buildroot将自动将`mk`文件包含在`package`目录的一级子目录中(使用`package/*/*.mk`模式)。如果我们想让Buildroot包含更深层次的子目录中的`.mk`文件(比如`package/<company>/package1/`)，那么我们只需要在包含这些额外的`.mk`文件的一级子目录中添加一个`.mk`文件。因此，创建一个文件`package/<company>/<company>.mk`，包含以下内容(假设在`package/<company>/`下面只有一个额外的目录层):

```bash
include $(sort $(wildcard package/<company>/*/*.mk))
```

对于`Config.in`文件，创建一个文件`package/<company>/Config.in`，其中包含你所有包的`Config.in`文件。由于kconfig的source命令不支持通配符，因此必须提供一个详尽的列表。例如:

```bash
source "package/<company>/package1/Config.in"
source "package/<company>/package2/Config.in"
```

从`package/Config.in`中包含这个新文件`package/<company>/Config.in`，最好是在公司特定的菜单中，以便更容易与未来的Buildroot版本合并。

如果使用br2-external树，请参考[9.2节，“在Buildroot之外保留自定义”](#92-keeping-customizations-outside-of-buildroot)来了解如何填充这些文件。


## 9.10. Quick guide to storing your project-specific customizations
-----------------------------------------------------------------

本章前面描述了针对项目进行定制的不同方法。本节将通过分步说明如何存储特定于项目的自定义来总结所有这些内容。显然，与项目无关的步骤可以跳过。

1.  `make menuconfig`来配置工具链、包和内核。
2.  `make linux-menuconfig`来更新内核配置，类似于busybox、uclibc…
3.  `mkdir -p board/<manufacturer>/<boardname>`
4.  将以下选项设置为`board/<manufacturer>/<boardname>/<package>.config`(只要它们是相关的):
    
    *   `BR2_LINUX_KERNEL_CUSTOM_CONFIG_FILE`
    *   `BR2_PACKAGE_BUSYBOX_CONFIG`
    *   `BR2_UCLIBC_CONFIG`
    *   `BR2_TARGET_AT91BOOTSTRAP3_CUSTOM_CONFIG_FILE`
    *   `BR2_TARGET_BAREBOX_CUSTOM_CONFIG_FILE`
    *   `BR2_TARGET_UBOOT_CUSTOM_CONFIG_FILE`
    
5.  写入配置文件:
    
    *   `make linux-update-defconfig`
    *   `make busybox-update-config`
    *   `make uclibc-update-config`
    *   `cp <output>/build/at91bootstrap3-*/.config board/<manufacturer>/<boardname>/at91bootstrap3.config`
    *   `make barebox-update-defconfig`
    *   `make uboot-update-defconfig`
    
6.  创建`board/<manufacturer>/<boardname>/rootfs-overlay/`，并用你需要的其他文件填充它。`board/<manufacturer>/<boardname>/rootfs-overlay/etc/inittab`。设置`BR2_ROOTFS_OVERLAY`为`board/<manufacturer>/<boardname>/rootfs-overlay`。
7.  创建一个post-build脚本`board/<manufacturer>/<boardname>/post_build.sh`。设置`BR2_ROOTFS_POST_BUILD_SCRIPT`为`board/<manufacturer>/<boardname>/post_build.sh`
8.  如果需要设置额外的setuid权限或创建设备节点，请创建`board/<manufacturer>/<boardname>/device_table.txt`，并将该路径添加到`BR2_ROOTFS_DEVICE_TABLE`。
9.  如果必须创建其他用户帐户，请创建`board/<manufacturer>/<boardname>/users_table.txt`，并将该路径添加到`BR2_ROOTFS_USERS_TABLES`。
10.  要向某些包添加自定义补丁，请将`BR2_GLOBAL_PATCH_DIR`设置为`board/<manufacturer>/<boardname>/patches/`，并在以包命名的子目录中为每个包添加补丁。每个补丁应该被命名为`<packagename>-<num>-<description>.patch`。
11.  特别是对于Linux内核，还存在`BR2_LINUX_KERNEL_PATCH`选项，其主要优点是它也可以从URL下载补丁。如果你不需要它，`BR2_GLOBAL_PATCH_DIR`是首选。U-Boot、Barebox、at91bootstrap和at91bootstrap3也有单独的选项，但它们并没有比`BR2_GLOBAL_PATCH_DIR`提供任何优势，未来可能会被删除。
12.  如果您需要添加项目特定的包，请创建`package/<manufacturer>/`并将您的包放在该目录中。创建一个完整的`<manufacturer>.mk`文件，其中包含你所有包的`.mk`文件。创建一个完整的`Config.in`文件，它是所有包的`Config.in`文件的源文件。从Buildroot的`package/Config.in`文件中包含这个`Config.in`文件。
13.  `make savedefconfig` 来保存buildroot配置。
14.  `cp defconfig configs/<boardname>_defconfig`



# Chapter 10. Integration topics
------------------------------

本章将讨论各种事物如何在系统级进行集成。Buildroot是高度可配置的，这里讨论的几乎所有内容都可以通过[rootfs覆盖或自定义骨架](#95-customizing-the-generated-target-filesystem)配置更改或覆盖。


## 10.1. Systemd
-------------

本章描述Buildroot集成systemd时所做的决策，以及如何实现各种用例。

### 10.1.1. DBus daemon

Systemd需要一个DBus守护进程。它有两种选择:传统的dbus (`BR2_PACKAGE_DBUS`)和bus1 dbus-broker (`BR2_PACKAGE_DBUS_BROKER`)。必须至少选择其中一个。如果两者都包含在配置中，则dbus-broker将用作系统总线，但是传统的dbus-daemon仍然被安装，并且可以用作会话总线。还有它的工具(例如:`dbus-send`)可以使用(systemd本身有`busctl`作为替代)。此外，传统的dbus包是唯一提供`libdbus`的包，它被许多包用作dbus集成库。

在dbus和dbus-broker情况下，守护进程都作为用户`dbus`运行。两者的DBus配置文件也是相同的。

为了确保只有两个守护进程中的一个作为系统总线启动，当选择dbus-broker时，dbus包的systemd激活文件(`dbus.socket`和`multi-user.target.wants`中的`dbus.service`符号链接)被删除。


## 10.2. Using SELinux in Buildroot
--------------------------------

[SELinux](https://selinuxproject.org)是一个强制访问控制策略的Linux内核安全模块。除了传统的文件权限和访问控制列表之外，`SELinux`还允许为用户或进程编写规则，以访问特定的资源(文件、套接字等)。

SELinux有三种操作模式。

*   Disabled: 策略不应用。
*   Permissive: 策略被应用，未授权的操作被简单记录。该模式通常用于解决SELinux问题。
*   Enforcing: 该策略被应用，未授权的操作被拒绝。

在Buildroot中，操作模式由`BR2_PACKAGE_REFPOLICY_POLICY_STATE_*`配置选项控制。Linux内核也有各种影响`SELinux`如何启用的配置选项(请参阅Linux内核源代码中的`security/selinux/Kconfig`)。

在Buildroot中，默认情况下，`SELinux`策略由上游[refpolicy](https://github.com/SELinuxProject/refpolicy)项目提供，通过`BR2_PACKAGE_REFPOLICY`启用。

### 10.2.1. Enabling SELinux support

要在Buildroot生成的系统中正确支持`SELinux`，必须启用以下配置选项:

*   `BR2_PACKAGE_LIBSELINUX`
*   `BR2_PACKAGE_REFPOLICY`

此外，文件系统映像格式必须支持扩展属性。

### 10.2.2. SELinux policy tweaking

`SELinux refpolicy`包含可以在构建时启用或禁用的模块。每个模块都提供了许多`SELinux`规则。在Buildroot中，非基础模块默认是禁用的，并且提供了几种启用这些模块的方法:

*   包可以使用`<packagename>_SELINUX_MODULES`变量在`refpolicy`中启用`SELinux`模块列表。
*   通过将包(.fc, .if 和 .te文件)放在`package/<packagename>/selinux/`中，包可以提供额外的`SELinux`模块。
*   额外的`SELinux`模块可以添加到由`BR2_REFPOLICY_EXTRA_MODULES_DIRS`配置选项指定的目录中。
*   如果在`BR2_REFPOLICY_EXTRA_MODULES_DEPENDENCIES`配置选项中列出，则可以启用`refpolicy`中的其他模块。

Buildroot还允许完全覆盖`refpolicy`。这允许提供专门为给定系统设计的完全自定义策略。这样做时，上述所有机制都被禁用:没有额外的`SElinux`模块添加到策略中，自定义策略中所有可用的模块都被启用并内置到最终的二进制策略中。自定义策略必须是官方[refpolicy](https://github.com/SELinuxProject/refpolicy)的分支。

为了完全覆盖`refpolicy`，必须设置以下配置变量:

*   `BR2_PACKAGE_REFPOLICY_CUSTOM_GIT`
*   `BR2_PACKAGE_REFPOLICY_CUSTOM_REPO_URL`
*   `BR2_PACKAGE_REFPOLICY_CUSTOM_REPO_VERSION`



# Chapter 11. Frequently Asked Questions & Troubleshooting
--------------------------------------------------------


## 11.1. The boot hangs after Starting network…
----------------------------------------------

如果启动过程在以下消息之后挂起(消息不一定完全相似，取决于选择的包列表):

```bash
Freeing init memory: 3972K
Initializing random number generator... done.
Starting network...
Starting dropbear sshd: generating rsa key... generating dsa key... OK
```

这意味着系统正在运行，但没有在串行控制台中启动shell。为了让系统在串行控制台启动shell，你必须进入Buildroot配置，在`System configuration`中，修改`Run a getty (login prompt) after boot`，并在`getty options`子菜单中设置适当的端口和波特率。这将自动调整生成的系统的`/etc/inittab`文件，以便shell在正确的串口上启动。


## 11.2. Why is there no compiler on the target?
---------------------------------------------

已经决定从Buildroot-2012.11版本开始停止对目标上原生编译器的支持，原因如下:

*   这个功能既没有维护，也没有测试，而且经常被破坏;
*   此功能仅用于Buildroot工具链;
*   Buildroot主要针对板载资源有限(CPU、ram、大容量存储)的小型或非常小的目标硬件，对于这些硬件，在目标上进行编译没有多大意义;
*   Buildroot旨在简化交叉编译，使目标上的本地编译变得不必要。

如果无论如何你都需要在目标上安装编译器，那么Buildroot不适合你。在这种情况下，你需要一个真正的分布，你应该选择这样的分布:

*   [openembedded](http://www.openembedded.org)
*   [yocto](https://www.yoctoproject.org)
*   [Debian](https://www.debian.org/ports/)
*   [Fedora](https://fedoraproject.org/wiki/Architectures)
*   [openSUSE ARM](http://en.opensuse.org/Portal:ARM)
*   [Arch Linux ARM](http://archlinuxarm.org)
*   …


## 11.3. Why are there no development files on the target?
-------------------------------------------------------

由于目标上没有可用的编译器(参见[11.2节，“为什么目标上没有编译器?”](#112-why-is-there-no-compiler-on-the-target))，因此浪费头文件或静态库的空间是没有意义的。

因此，自Buildroot-2012.11发布以来，这些文件总是从target中删除。


## 11.4. Why is there no documentation on the target?
--------------------------------------------------

因为Buildroot主要针对机载资源有限(CPU、ram、大容量存储)的小型或非常小的目标硬件，所以浪费文档数据的空间是没有意义的。

如果无论如何你都需要目标上的文档数据，那么Buildroot并不适合你，你应该寻找一个真正的发行版(参见:[11.2节，“为什么目标上没有编译器?”](#112-why-is-there-no-compiler-on-the-target))。


## 11.5. Why are some packages not visible in the Buildroot config menu?
---------------------------------------------------------------------

如果一个包存在于Buildroot树中，但没有出现在config菜单中，这很可能意味着包的某些依赖项没有得到满足。

要了解有关包依赖项的更多信息，请在配置菜单中搜索包符号(参见[8.1节, “make tips”](#81-make-tips))。

然后，您可能必须递归地启用几个选项(对应于未满足的依赖项)，以最终能够选择包。

如果由于一些未满足的工具链选项导致包不可见，那么您当然应该运行完全重建(请参阅[8.1节, “make tips”](#81-make-tips)了解更多解释)。


## 11.6. Why not use the target directory as a chroot directory?
-------------------------------------------------------------

有很多理由**不**使用目标目录而不是chroot目录，其中包括:

*   目标目录下的文件所有权、模式和权限设置不正确;
*   目标目录下未创建设备节点。

由于这些原因，使用目标目录作为新的根目录运行chroot的命令很可能会失败。

如果你想在chroot目录下运行目标文件系统，或者作为NFS的根目录，那么使用在`images/`目录下生成的压缩镜像并将其提取为根目录。


## 11.7. Why doesn’t Buildroot generate binary packages (.deb, .ipkg…)?
--------------------------------------------------------------------

在Buildroot列表中经常讨论的一个特性是“包管理”。总而言之，我们的想法是添加一些跟踪哪个Buildroot包安装了什么文件，目标是:

*   当从menuconfig中取消选择该包时，能够删除该包安装的文件;
*   能够生成可安装在目标上的二进制包(ipk或其他格式)，而无需重新生成新的根文件系统映像。

一般来说，大多数人认为这很容易做到:只需跟踪哪个软件包安装了什么，并在软件包未选中时删除它。然而，实际情况要复杂得多:

*   它不仅是关于`target/`目录，还包括`host/<tuple>/sysroot`中的sysroot和`host/`目录本身。必须跟踪各种包安装在这些目录中的所有文件。
*   当一个包从配置中被取消选择时，仅仅删除它安装的文件是不够的。还必须删除其所有的反向依赖项(即依赖于它的包)并重新构建所有这些包。例如，包A可选依赖于OpenSSL库。这两个都被选中，并且构建了Buildroot。包A是使用OpenSSL构建的加密支持。稍后，OpenSSL从配置中被取消，但包A仍然存在(因为OpenSSL是可选的依赖，所以这是可能的)。如果只移除OpenSSL文件，那么包A安装的文件就会被破坏:它们使用了目标上不再存在的库。虽然这在技术上是可行的，但它给Buildroot增加了很多复杂性，这与我们坚持的简单性相悖。
*   除了前面的问题之外，还有一种情况，Buildroot甚至不知道optional依赖。例如，包A在1.0版本中从未使用过OpenSSL，但在2.0版本中，如果可用，它会自动使用OpenSSL。如果Buildroot .mk文件没有更新以考虑到这一点，那么包A就不会成为OpenSSL的反向依赖的一部分，也不会在OpenSSL被移除时被移除并重新构建。当然，包A的.mk文件应该被修复以提及此可选依赖项，但同时，您可能会有不可重现的行为。
*   该请求还允许将menuconfig中的更改应用于输出目录，而不必从头开始重新构建所有内容。然而，这很难以可靠的方式实现:当包的子选项更改时会发生什么(我们必须检测此情况，并从头开始重建包及其所有反向依赖项)，如果工具链选项更改会发生什么，等等。目前，Buildroot所做的是清晰和简单的，因此它的行为非常可靠，很容易支持用户。如果在menuconfig中做的配置更改是在下一次make之后应用的，那么它必须在所有情况下都正确和正确地工作，并且不能出现一些奇怪的边缘情况。风险是得到bug报告，比如“我启用了包A、B和C，然后运行make，然后禁用包C并启用包D和运行make，然后重新启用包C并启用包E，然后出现构建失败”。或者更糟糕的情况是“我做了一些配置，然后构建，然后进行一些更改，构建，一些更改，构建，一些更改，构建，现在它失败了，但我不记得我做过的所有更改以及更改的顺序”。这是不可能支持的。

基于所有这些原因，结论是，添加已安装文件的跟踪以在包未选中时删除它们，或生成二进制包的存储库，是一件非常难以可靠实现的事情，并且会增加很多复杂性。

关于这个问题，Buildroot的开发者发表了如下立场声明:

*   Buildroot努力使生成根文件系统变得容易(顺便说一下，根文件系统因此而得名)。这就是我们想让Buildroot擅长的:构建根文件系统。
*   Buildroot并不是一个发行版(或者更确切地说，是一个发行版生成器)。大多数Buildroot开发者的观点是，这不是我们应该追求的目标。我们相信有其他工具比Buildroot更适合生成发行版。例如，[Open Embedded](http://openembedded.org/), 或 [openWRT](https://openwrt.org/)就是这样的工具。
*   我们倾向于将Buildroot推向一个更容易(甚至更容易)生成完整的根文件系统的方向。这就是Buildroot脱颖而出的原因(当然还有其他原因!)
*   我们相信对于大多数嵌入式Linux系统来说，二进制包不是必需的，而且可能是有害的。当使用二进制包时，这意味着系统可以部分升级，这将创建大量可能的包版本组合，在对嵌入式设备进行升级之前，应该对这些包版本进行测试。另一方面，通过一次性升级整个根文件系统映像来进行完整的系统升级，可以保证部署到嵌入式系统的映像确实是经过测试和验证的映像。


## 11.8. How to speed-up the build process?
----------------------------------------

由于Buildroot通常需要对整个系统进行完整的重构，这可能会很长时间，因此我们提供了以下一些提示来帮助减少构建时间:

*   使用预构建的外部工具链，而不是默认的Buildroot内部工具链。通过使用预构建的Linaro工具链(在ARM上)或sourcerycodebench工具链(对于ARM、x86、x86-64、MIPS等)，您将在每次完全重建时节省工具链的构建时间，大约15到20分钟。请注意，暂时使用外部工具链并不会阻止您在系统的其余部分正常工作后切换回内部工具链(可能提供更高级别的自定义);
*   使用`ccache`编译器缓存(参见:[8.13.3节，“在Buildroot中使用`ccache`”](#8133-using-ccache-in-buildroot));
*   学习只重建你真正关心的少数包(请参阅[8.3节，“理解如何重建包”](#83-understanding-how-to-rebuild-packages))，但要注意，有时完全重建是必要的(请参阅[8.2节，“了解何时需要完全重建”](#82-understanding-when-a-full-rebuild-is-necessary));
*   确保您没有使用用于运行Buildroot的Linux系统的虚拟机。众所周知，大多数虚拟机技术都会对I/O造成显著的性能影响，而I/O对于构建源代码非常重要;
*   请确保只使用本地文件:不要尝试通过NFS进行构建，这会显著降低构建速度。在本地提供Buildroot下载文件夹也会有所帮助。
*   买新的硬件。SSD和大量的RAM是加速构建的关键。
*   尝试使用顶层并行构建，参见[8.12节，“顶层并行构建”](#812-top-level-parallel-build)。



# Chapter 12. Known issues
------------------------

*   如果额外的链接器选项包含`$`符号，则不可能通过`BR2_TARGET_LDFLAGS`传递。例如，下面的代码是无法正常运行的: `BR2_TARGET_LDFLAGS="-Wl,-rpath='$ORIGIN/../lib'"`
*   SuperH 2和ARMv7-M架构不支持`libffi`包。
*   使用Sourcery CodeBench, 2012.09版的SuperH 4编译器时，`prboom`包会触发编译器失败。



# Chapter 13. Legal notice and licensing
--------------------------------------

## 13.1. Complying with open source licenses
-----------------------------------------

Buildroot的所有最终产品(工具链、根文件系统、内核、引导装载器)都包含在各种许可证下发布的开源软件。

使用开源软件使您可以自由地构建丰富的嵌入式系统，从广泛的软件包中进行选择，但也强加了一些您必须了解和尊重的义务。有些许可要求您在产品文档中发布许可文本。另一些则要求你将软件的源代码重新分发给那些接收你的产品的人。

每个许可证的确切要求都记录在每个软件包中，遵守这些要求是你的责任(或你的法律办公室的责任)。为了让你更容易，Buildroot可以为你收集一些你可能需要的材料。要生成这个材料，在你用`make menuconfig`, `make xconfig` 或 `make gconfig`配置好Buildroot后，运行:

```bash
make legal-info
```

Buildroot将在输出目录的`legal-info/`子目录下收集与法律相关的材料。你会发现:

*   一个`README`文件，它总结了生成的材料，并包含有关Buildroot无法生成的材料的警告。
*   `buildroot.config`:这是通常使用`make menuconfig`生成的Buildroot配置文件，它是重现构建所必需的。
*   所有包的源代码;它分别保存在目标包和主机包的`sources/`和`host-sources/`子目录中。设置为`<PKG>_REDISTRIBUTE = NO`的包的源代码将不会被保存。应用的补丁也会被保存，同时保存的还有一个名为`series`的文件，该文件按应用的顺序列出了补丁。补丁与它们所修改的文件处于相同的许可证之下。注意:Buildroot对基于autotools包的Libtool脚本应用额外的补丁。这些补丁可以在Buildroot源代码的`support/libtool`下找到，并且由于技术限制，没有与软件包源代码一起保存。您可能需要手动收集它们。
*   一个清单文件(一个用于主机包，一个用于目标包)列出了配置的包、它们的版本、许可证和相关信息。其中一些信息可能没有在Buildroot中定义;这些物品被标记为“未知”。
*   所有包的许可证文本，分别位于目标包和主机包的`licenses/`和`host-licenses/`子目录中。如果在Buildroot中没有定义许可证文件，则不会生成该文件，并且在`README`中会出现警告。

请注意，Buildroot的`legal-info`功能的目的是生成与软件包许可的法律合规性有关的所有材料。Buildroot不会尝试生产你必须以某种方式公开的确切材料。当然，生产的材料比严格遵守法律所需要的要多。例如，它为在类似bsd许可证下发布的包生成源代码，您不需要以源代码形式重新发布。

此外，由于技术上的限制，Buildroot不会生成您将要或可能需要的一些材料，例如一些外部工具链的工具链源代码和Buildroot源代码本身。当你运行`make legal-info`时，Buildroot会在`README`文件中产生警告，通知你相关材料无法保存。

最后，请记住，`make legal-info`的输出是基于每个包秘诀中的声明式语句。Buildroot的开发人员会尽他们所能保持这些声明式语句尽可能准确。然而，很有可能这些声明性语句并不都是完全准确或详尽的。你(或你的法律部门)必须在使用`make legal-info`作为自己的合规交付之前检查它的输出。参见Buildroot发行版根目录的`COPYING`文件中的NO WARRANTY条款(第11条和第12条)。


## 13.2. Complying with the Buildroot license
------------------------------------------

Buildroot本身是一个开源软件，在[GNU通用公共许可证，版本2](http://www.gnu.org/licenses/old-licenses/gpl-2.0.html)或(根据您的选择)任何更新版本下发布，但下面详细介绍的包补丁除外。然而，作为一个构建系统，它通常不是最终产品的一部分:如果您为一个设备开发了根文件系统、内核、引导装载程序或工具链，那么Buildroot的代码只存在于开发机器上，而不在设备存储中。

尽管如此，Buildroot开发人员的普遍看法是，在发布包含gpl许可软件的产品时，应该将Buildroot源代码连同其他包的源代码一起发布。这是因为[GNU GPL](http://www.gnu.org/licenses/old-licenses/gpl-2.0.html)将可执行作品的“完整源代码”定义为“它包含的所有模块的所有源代码，加上任何相关的接口定义文件，加上用于控制可执行作品的编译和安装的脚本”。Buildroot是用于控制可执行文件的编译和安装的脚本的一部分，因此它被认为是必须重新发布的材料的一部分。

请记住，这只是Buildroot开发人员的意见，如果有任何疑问，请咨询法律部门或律师。

### 13.2.1. Patches to packages

Buildroot还打包了补丁文件，这些文件应用于各种包的源代码。这些补丁没有被Buildroot的许可证覆盖。相反，它们被应用补丁的软件的许可证所覆盖。当上述软件在多个许可证下可用时，Buildroot补丁只在公开可访问许可证下提供。

请参阅[第19章为包打补丁](#chapter-19-patching-a-package)以了解技术细节。



# Chapter 14. Beyond Buildroot
----------------------------

## 14.1. Boot the generated images
-------------------------------

### 14.1.1. NFS boot

要实现NFS-boot，请在文件系统映像菜单中启用tar根文件系统。

在完成构建后，只需运行以下命令设置NFS-root目录:

```bash
sudo tar -xavf /path/to/output_dir/rootfs.tar -C /path/to/nfs_root_dir
```

记得把这个路径添加到`/etc/exports`中。

然后，您可以从您的目标执行NFS-boot。

### 14.1.2. Live CD

要构建一个实时CD镜像，请启用文件系统镜像菜单中的iso镜像选项。注意，这个选项只在x86和x86-64架构上可用，并且如果您使用Buildroot构建内核的话。

您可以使用IsoLinux、Grub或Grub 2构建一个活动CD映像作为引导程序，但只有IsoLinux支持使该映像既可用作活动CD，也可用作活动USB(通过构建混合映像选项)。

你可以使用QEMU测试你的live CD镜像:

```bash
qemu-system-i386 -cdrom output/images/rootfs.iso9660
```

如果它是一个混合ISO，也可以将其用作硬盘镜像:

```bash
qemu-system-i386 -hda output/images/rootfs.iso9660
```

可以使用`dd`轻松地将其闪存到USB驱动器:

```bash
dd if=output/images/rootfs.iso9660 of=/dev/sdb
```

## 14.2. Chroot
------------

如果你想在生成的图像中使用chroot，那么有几件事你应该知道:

*   您应该从tar根文件系统映像设置新的根目录;
*   选择的目标架构与你的主机兼容，或者你应该使用一些`qemu-*`二进制文件，并在`binfmt`属性中正确设置它，以便能够在你的主机上运行为目标构建的二进制文件;
*   Buildroot目前没有为这种使用正确地构建和设置`host-qemu`和`binfmt`。



Part III. Developer guide
=========================

# Chapter 15. How Buildroot works
-------------------------------

如上所述，Buildroot基本上是一组makefile，使用正确的选项下载、配置和编译软件。它还包括各种软件包的补丁——主要涉及交叉编译工具链(`gcc`、`binutils`和`uClibc`)。

基本上每个软件包都有一个Makefile，它们以`.mk`扩展名命名。makefile分为许多不同的部分。

*   `toolchain/`目录包含与交叉编译工具链相关的所有软件的makefile和相关文件:`binutils`、`gcc`、`gdb`、`kernel-headers`和`uClibc`。
*   `arch/`目录包含Buildroot支持的所有处理器架构的定义。
*   `package/`目录包含所有用户空间工具和库的makefile和相关文件，Buildroot可以编译并添加到目标根文件系统。每个包都有一个子目录。
*   `linux/`目录包含了linux内核的makefile和相关文件。
*   `boot/`目录包含Buildroot支持的引导加载器的makefile和相关文件。
*   `system/`目录包含对系统集成的支持，例如目标文件系统框架和init系统的选择。
*   `fs/`目录包含与生成目标根文件系统映像相关的makefile和相关文件。

每个目录至少包含2个文件:

*   `something.mk`是用于下载、配置、编译和安装`something`包的Makefile。
*   `Config.in`是配置工具描述文件的一部分。它描述了与包相关的选项。

主Makefile执行以下步骤(在配置完成后):

*   在输出目录中创建所有输出目录:`staging`、`target`、`build`等(默认为`output/`，可以使用`O=`指定另一个值)
*   生成工具链目标。当使用内部工具链时，这意味着生成交叉编译工具链。当使用外部工具链时，这意味着检查外部工具链的特性并将其导入Buildroot环境。
*   生成`TARGETS`变量中列出的所有目标。该变量由所有独立组件的makefile填充。生成这些目标将触发用户空间包(库、程序)、内核、引导装载程序的编译，以及根文件系统映像的生成，具体取决于配置。



# Chapter 16. Coding style
------------------------

总的来说，这些编码样式规则是为了帮助您在Buildroot中添加新文件或重构现有文件。

如果你稍微修改了一些现有的文件，重要的是要保持整个文件的一致性，这样你就可以:

*   要么遵循此文件中使用的可能已弃用的编码风格，
*   或者完全重写它以使其符合这些规则。

## 16.1. `Config.in` file
----------------------

`Config.in`文件包含了几乎所有Buildroot中可配置的条目。

条目具有以下模式：

```bash
config BR2_PACKAGE_LIBFOO
        bool "libfoo"
        depends on BR2_PACKAGE_LIBBAZ
        select BR2_PACKAGE_LIBBAR
        help
          This is a comment that explains what libfoo is. The help text
          should be wrapped.

          http://foosoftware.org/libfoo/
```

*   `bool`，`depends on`，`select`和`help`行用一个tab缩进。
*   帮助文本本身应该用一个制表符和两个空格缩进。
*   帮助文本应该换行以容纳72列，其中tab为8列，因此文本本身包含62个字符。

`Config.in`文件是Buildroot中使用的配置工具的输入，它是常规的Kconfig。有关Kconfig语言的更多细节，请参阅[http://kernel.org/doc/Documentation/kbuild/kconfig-language.txt](http://kernel.org/doc/Documentation/kbuild/kconfig-language.txt)。

## 16.2. The `.mk` file
--------------------

*   头文件:文件以头文件开始。它包含模块名称，最好是小写，用80个散列组成的分隔符括起来。标题后面必须有一个空行:
    
```bash
################################################################################
#
# libfoo
#
################################################################################
```

*   赋值:使用`=`前后加一个空格:

```bash
LIBFOO_VERSION = 1.0
LIBFOO_CONF_OPTS += --without-python-support
```

不要对齐`=`符号。

*   缩进:只使用tab:

```bash
define LIBFOO_REMOVE_DOC
        $(RM) -r $(TARGET_DIR)/usr/share/libfoo/doc \
                $(TARGET_DIR)/usr/share/man/man3/libfoo*
endef
```

    请注意，`define`块中的命令总是以tab开头，因此make将它们识别为命令。
    
*   可选依赖:
    
    *   更喜欢多行语法。

YES:

```bash
ifeq ($(BR2_PACKAGE_PYTHON3),y)
LIBFOO_CONF_OPTS += --with-python-support
LIBFOO_DEPENDENCIES += python3
else
LIBFOO_CONF_OPTS += --without-python-support
endif
```

NO:

```bash
LIBFOO_CONF_OPTS += --with$(if $(BR2_PACKAGE_PYTHON3),,out)-python-support
LIBFOO_DEPENDENCIES += $(if $(BR2_PACKAGE_PYTHON3),python3,)
```

    *   保持配置选项和依赖项接近。
    
*   可选钩子:将钩子定义和赋值放在一个if块中。

YES:

```bash
ifneq ($(BR2_LIBFOO_INSTALL_DATA),y)
define LIBFOO_REMOVE_DATA
        $(RM) -r $(TARGET_DIR)/usr/share/libfoo/data
endef
LIBFOO_POST_INSTALL_TARGET_HOOKS += LIBFOO_REMOVE_DATA
endif
```

NO:

```bash
define LIBFOO_REMOVE_DATA
        $(RM) -r $(TARGET_DIR)/usr/share/libfoo/data
endef

ifneq ($(BR2_LIBFOO_INSTALL_DATA),y)
LIBFOO_POST_INSTALL_TARGET_HOOKS += LIBFOO_REMOVE_DATA
endif
```


## 16.3. The `genimage.cfg` file
-----------------------------

`genimage.cfg`文件包含了输出图像布局，genimage工具用来创建最终的.img文件。

下面是一个例子:

```bash
image efi-part.vfat {
        vfat {
                file EFI {
                        image = "efi-part/EFI"
                }

                file Image {
                        image = "Image"
                }
        }

        size = 32M
}

image sdimage.img {
        hdimage {
        }

        partition u-boot {
                image = "efi-part.vfat"
                offset = 8K
        }

        partition root {
                image = "rootfs.ext2"
                size = 512M
        }
}
```

*   每一个`section`(即: hdimage, vfat等)，`partition`必须用一个tab缩进。
*   每个`file`或其他`subnode`都必须使用两个tab来缩进。
*   每个节点(`section`、`partition`、`file`、`subnode`)必须在节点名称的同一行上有一个左大括号，而结束的一个必须在换行符上，除最后一个节点外，在换行符后面必须添加一个换行符。选项也是如此，例如选项`size` `=`。
*   每一个`option`(即: `image`， `offset`， `size`)必须具有`=`赋值的值，并且与指定的值相距一个空格。
*   文件名必须至少以genimage前缀开始，并具有.cfg扩展名以便识别。
*   允许的`offset`和`size`选项符号是:`G`， `M`， `K`(不是`k`)。如果上面的表示法无法表示精确的字节计数，那么请使用十六进制的`0x`前缀，或者作为最后的机会，使用字节计数。在注释中使用`GB`， `MB`， `KB`(不是`Kb`)来代替`G`， `M`， `K`。
*   对于 GPT 分区，EFI 系统分区的`partition-type-uuid`值必须是`U`(通过genimage扩展为`c12a7328-f81f-11d2-ba4b-00a0c93ec93b`)，FAT 分区的`F` (通过genimage扩展为`ebd0a0a2-b9e5-4433-87c0-68b6b72699c7`)，或者对于根文件系统或其他文件系统的`L`(通过genimage扩展为`0fc63daf-8483-4772-8e79-3d69d8477de4`)。尽管`L`是genimage的默认值，但我们更希望在`genimage.cfg`文件中明确指定它。最后，使用这些快捷方式时不要使用双引号，例如`partition-type-uuid = U`。如果明确指定了GUID，则应该使用小写字母。

`genimage.cfg`文件是 Buildroot 中使用的 genimage 工具的输入，用于生成最终映像文件（即 sdcard.img）。有关genimage语言的更多详细信息，请参阅[https://github.com/pengutronix/genimage/blob/master/README.rst](https://github.com/pengutronix/genimage/blob/master/README.rst)。

## 16.4. The documentation
-----------------------

文档使用[asciidoc](https://asciidoc-py.github.io/)格式。

有关asciidoc语法的更多细节，请参阅 [https://asciidoc-py.github.io/userguide.html](https://asciidoc-py.github.io/userguide.html).

## 16.5. Support scripts
---------------------

`support/`和`utils/`目录中的一些脚本是用Python编写的，并且应该遵循[PEP8 Python代码风格指南](https://www.python.org/dev/peps/pep-0008/)。



# Chapter 17. Adding support for a particular board
-------------------------------------------------

Buildroot包含几个公开可用的硬件板的基本配置，以便使用这种板的用户可以轻松构建已知可以工作的系统。您也欢迎添加对其他板的支持到Buildroot。

为此，您需要创建一个普通的Buildroot配置，为硬件构建一个基本的系统:(内部)工具链、内核、引导装载程序、文件系统和一个简单的仅busybox的用户空间。不应该选择任何特定的包:配置应该尽可能少，并且只应该为目标平台构建一个可工作的基本BusyBox系统。当然，你可以在内部项目中使用更复杂的配置，但Buildroot项目只会集成基本的电路板配置。这是因为包的选择是高度特定于应用程序的。

一旦你有了一个可以运行的配置，运行`make savedefconfig`。这将在Buildroot源码树的根目录中生成一个最小的`defconfig`文件。将这个文件移动到`configs/`目录，并将其重命名为`<boardname>_defconfig`。如果配置有点复杂，最好手动重新格式化并将其分成多个部分，在每个部分之前添加注释。典型的部分是体系结构、工具链选项(通常只是linux头文件版本)、固件、引导装载程序、内核和文件系统。

始终使用固定版本或提交不同组件的哈希值，而不是“最新”版本。例如，设置`BR2_LINUX_KERNEL_CUSTOM_VERSION=y` 和 `BR2_LINUX_KERNEL_CUSTOM_VERSION_VALUE`为你测试的内核版本。

建议使用尽可能多的上游版本的Linux内核和引导装载程序，并使用尽可能多的默认内核和引导装载程序配置。如果它们对你的板来说是不正确的，或者不存在默认值，我们鼓励你将修复发送到相应的上游项目。

然而，与此同时，您可能希望存储特定于目标平台的内核或引导装载程序配置或补丁。为此，创建一个目录`board/<manufacturer>`和一个子目录`board/<manufacturer>/<boardname>`。然后，您可以将补丁和配置存储在这些目录中，并从主Buildroot配置中引用它们。更多细节请参考[第9章，项目定制](#chapter-9-project-specific-customization)。

在为新开发板提交补丁之前，建议使用最新的gitlab-CI docker容器进行构建，以测试它。要做到这一点，请使用`utils/docker-run`脚本，并在其中执行以下命令:

```bash
$ make <boardname>_defconfig
$ make
```

默认情况下，Buildroot开发人员使用托管在[gitlab.com registry](https://gitlab.com/buildroot.org/buildroot/container_registry/2395076)上的官方镜像，它应该对大多数使用都很方便。如果你仍然想构建自己的docker镜像，可以在官方镜像的基础上使用自己的Dockerfile的`FROM`指令构建:

```bash
FROM registry.gitlab.com/buildroot.org/buildroot/base:YYYYMMDD.HHMM
RUN ...
COPY ...
```

当前版本号YYYYMMDD.HHMM可以在Buildroot源代码树顶部的`.gitlab-ci.yml`文件中找到;所有过去的版本也都列在上述注册表中。



# Chapter 18. Adding new packages to Buildroot
--------------------------------------------

本节介绍如何将新的包(用户空间库或应用程序)集成到Buildroot中。它还展示了如何集成现有的包，这是修复问题或调整配置所需的。

当你添加一个新包时，请确保在各种条件下测试它(参见[18.25.3节，“如何测试你的包”](#18253-how-to-test-your-package))，并检查它的编码风格(参见[18.25.2节，“如何检查编码风格”](#18252-how-to-check-the-coding-style))。


## 18.1. Package directory
-----------------------

首先，在`package`目录下为你的软件创建一个目录，例如`libfoo`。

有些包按主题分组在子目录中:`x11r7`， `qt5`和`gstreamer`。如果您的包适合这些类别之一，则在这些类别中创建您的包目录。但是，不鼓励创建新的子目录。


## 18.2. Config files
------------------

要在配置工具中显示包，您需要在包目录中创建配置文件。有两种类型:`Config.in`和`Config.in.host`。

### 18.2.1. `Config.in` file

对于在目标上使用的包，创建一个名为`Config.in`的文件。该文件将包含与我们的`libfoo`软件相关的选项描述，它们将被使用并显示在配置工具中。它基本上应该包含:

```bash
config BR2_PACKAGE_LIBFOO
        bool "libfoo"
        help
          This is a comment that explains what libfoo is. The help text
          should be wrapped.

          http://foosoftware.org/libfoo/
```

`bool`行、`help`行和其他关于配置选项的元数据信息必须用一个tab缩进。帮助文本本身应该用一个制表符和两个空格缩进，每行应该换行以容纳72列，其中制表符为8，因此文本本身有62个字符。帮助文本必须在空行之后提到项目的上游URL。

作为Buildroot特有的约定，属性的顺序如下:

1.  选项的类型:`bool`，`string` ...
2.  如果需要，`default`值
3.  在`depends on`表单中，任何依赖于目标的内容
4.  任何依赖`depends on`形式的工具链
5.  任何依赖于`depends on`形式的其他包的依赖
6.  `select`表单的任何依赖项
7.  帮助关键字和帮助文本。

你可以在`if BR2_PACKAGE_LIBFOO…endif`语句中添加其他子选项来配置软件中的特定内容。您可以在其他包中查看示例。`Config.in`文件的语法与内核Kconfig文件的语法相同。这种语法的文档可以在[http://kernel.org/doc/Documentation/kbuild/kconfig-language.txt](http://kernel.org/doc/Documentation/kbuild/kconfig-language.txt)找到。

最后，你必须将新的`libfoo/Config.in`添加到`package/Config.in`中(或者如果你决定将包放在现有类别中的某个类别子目录中)。其中包含的文件按每个类别的字母顺序排序，不应该包含除包的裸名之外的任何内容。

```bash
source "package/libfoo/Config.in"
```

### 18.2.2. `Config.in.host` file

还需要为主机系统构建一些包。这里有两种选择。

*   宿主包只需要满足一个或多个目标包的构建时依赖。在这种情况下，将`host-foo`添加到目标包的`BAR_DEPENDENCIES`变量中。不应该创建`Config.in.host`文件。
*   主机包应该由用户从配置菜单中明确选择。在这种情况下，为主机包创建一个`Config.in.host`文件:
    
```bash
config BR2_PACKAGE_HOST_FOO
        bool "host foo"
        help
          This is a comment that explains what foo for the host is.

          http://foosoftware.org/foo/
```
    
与`Config.in`文件相同的编码风格和选项是有效的。
    
最后，你必须将新的`libfoo/Config.in.host`添加到`package/Config.in.host`中。其中包含的文件按字母顺序排序，除了包的裸名之外不应该包含任何内容。
    
```bash
source "package/foo/Config.in.host"
```
    
然后可以从`Host utilities`菜单中获取host包。
    

### 18.2.3. Choosing `depends on` or `select`

包的`Config.in`文件也必须确保启用了依赖项。通常，Buildroot使用以下规则。

*   对于依赖库的依赖，使用`select`类型的依赖。这些依赖关系通常不明显，因此由kconfig系统确保选择依赖关系是有意义的。例如，libgtk2包使用`select BR2_PACKAGE_LIBGLIB2`来确保该库也被启用。`select`关键字使用反向语义表达依赖关系。
*   当用户真的需要知道依赖关系时，使用`depends on`类型的依赖关系。通常，Buildroot将这种类型的依赖用于对目标架构的依赖、MMU支持和工具链选项(参见[第18.2.4节，“目标和工具链选项的依赖”](#1824-dependencies-on-target-and-toolchain-options))，或者用于对“大”事物的依赖，例如X.org系统。`depends on`关键字以前向语义表达依赖关系。

**注意.** kconfig语言当前的问题是，这两个依赖关系语义在内部没有链接。因此，可以选择一个包，它的一个依赖项/需求没有得到满足。

一个例子说明了`select`和`depends on`的用法。

```bash
config BR2_PACKAGE_RRDTOOL
        bool "rrdtool"
        depends on BR2_USE_WCHAR
        select BR2_PACKAGE_FREETYPE
        select BR2_PACKAGE_LIBART
        select BR2_PACKAGE_LIBPNG
        select BR2_PACKAGE_ZLIB
        help
          RRDtool is the OpenSource industry standard, high performance
          data logging and graphing system for time series data.

          http://oss.oetiker.ch/rrdtool/

comment "rrdtool needs a toolchain w/ wchar"
        depends on !BR2_USE_WCHAR
```

请注意，这两种依赖关系仅在同类依赖关系下才具有传递性。

这意味着，在下面的例子中:

```bash
config BR2_PACKAGE_A
        bool "Package A"

config BR2_PACKAGE_B
        bool "Package B"
        depends on BR2_PACKAGE_A

config BR2_PACKAGE_C
        bool "Package C"
        depends on BR2_PACKAGE_B

config BR2_PACKAGE_D
        bool "Package D"
        select BR2_PACKAGE_B

config BR2_PACKAGE_E
        bool "Package E"
        select BR2_PACKAGE_D
```

*   如果选择了`Package B`，则选择`Package C`将可见，而`Package B`仅在选择了`Package A`时可见。
*   选择`Package E`将选择`Package D`， `Package D`将选择`Package B`，它不会检查`Package B`的依赖关系，因此它不会选择`Package A`。
*   由于`Package B`被选中了，但`Package A`没有被选中，这违反了`Package B`对`Package A`的依赖。因此，在这种情况下，必须显式添加传递依赖:

```bash
config BR2_PACKAGE_D
        bool "Package D"
        depends on BR2_PACKAGE_A
        select BR2_PACKAGE_B

config BR2_PACKAGE_E
        bool "Package E"
        depends on BR2_PACKAGE_A
        select BR2_PACKAGE_D
```

总的来说，对于包库依赖项，`select`应该是首选。

请注意，这样的依赖项将确保依赖项选项也启用，但不一定在包之前构建。为此，依赖关系还需要在包的`.mk`文件中表达。

进一步的格式化细节:参见[编码风格](#161-configin-file)。

### 18.2.4. Dependencies on target and toolchain options

许多包依赖于工具链的某些选项:C库的选择、C++支持、线程支持、RPC支持、wchar支持或动态库支持。有些包只能在某些目标体系结构上构建，或者在处理器中有MMU可用的情况下构建。

这些依赖关系必须在Config.in文件中使用适当的depends on语句来表示。此外，对于工具链选项的依赖项，当选项未启用时，应显示一个`comment`，以便用户知道为什么该包不可用。不应该在注释中显示对目标体系结构或MMU支持的依赖关系:因为用户不太可能自由选择另一个目标，所以显式显示这些依赖关系没有什么意义。

只有在满足工具链选项依赖关系时，`config`选项本身可见时，`comment`才应该可见。这意味着该包的所有其他依赖项(包括对目标架构和MMU支持的依赖项)必须在`comment`定义上重复。为了清楚起见，这些非工具链选项的`depends on`语句应该与工具链选项的`depends on`语句保持分离。如果在同一个文件(通常是主包)中有配置项依赖，最好使用全局的`if…endif`结构，而不是在注释和其他配置项上重复`depends on`语句。

foo包的依赖`comment`的一般格式是:

```bash
foo needs a toolchain w/ featA, featB, featC
```

例如:

```bash
mpd needs a toolchain w/ C++, threads, wchar
```

或

```bash
crda needs a toolchain w/ threads
```

请注意，这段文本故意保持简短，以适应80个字符的终端。

本节的其余部分将列举不同的target和工具链选项、要依赖的相应配置符号以及注释中要使用的文本。

*   目标架构
    
    *   依赖符号:`BR2_powerpc`， `BR2_mips`，…(参见`arch/Config.in`)
    *   注释字符串:不添加注释
    
*   MMU支持
    
    *   依赖符号: `BR2_USE_MMU`
    *   注释字符串:不添加注释
    
*   Gcc `_sync*`内置函数用于原子操作。它们有1字节、2字节、4字节和8字节的变体。由于不同的体系结构支持不同长度上的原子操作，每种长度对应一个依赖符号:
    
    *   依赖符号: `BR2_TOOLCHAIN_HAS_SYNC_1`表示1字节，`BR2_TOOLCHAIN_HAS_SYNC_2`表示2字节，`BR2_TOOLCHAIN_HAS_SYNC_4`表示4字节，`BR2_TOOLCHAIN_HAS_SYNC_8`表示8字节。
    *   注释字符串:不添加注释
    
*   Gcc `_atomic*`内置函数用于原子操作。
    
    *   依赖符号: `BR2_TOOLCHAIN_HAS_ATOMIC`.
    *   注释字符串:不添加注释
    
*   Kernel headers
    
    *   依赖符号: `BR2_TOOLCHAIN_HEADERS_AT_LEAST_X_Y`, (将`X_Y`替换为正确的版本，参见`toolchain/Config.in`)
    *   注释字符串: `headers >= X.Y` 和/或 `headers <= X.Y` (将`X.Y`替换为正确的版本)
    
*   GCC版本
    
    *   依赖符号: `BR2_TOOLCHAIN_GCC_AT_LEAST_X_Y`, (将`X_Y`替换为正确的版本，参见`toolchain/Config.in`)
    *   注释字符串: `gcc >= X.Y` 和/或 `gcc <= X.Y` (将`X.Y`替换为正确的版本)
    
*   Host GCC版本
    
    *   依赖符号: `BR2_HOST_GCC_AT_LEAST_X_Y`, (将`X_Y`替换为正确的版本，参见`Config.in`)
    *   注释字符串:不添加注释
    *   请注意，通常包本身并没有GCC的最低宿主版本，而是它所依赖的宿主包。
    
*   C库
    
    *   依赖符号: `BR2_TOOLCHAIN_USES_GLIBC`, `BR2_TOOLCHAIN_USES_MUSL`, `BR2_TOOLCHAIN_USES_UCLIBC`
    *   注释字符串: 对于C库，使用了一种稍微不同的注释文本: `foo needs a glibc toolchain`, 或 `foo needs a glibc toolchain w/ C++`
    
*   C++支持
    
    *   依赖符号: `BR2_INSTALL_LIBSTDCPP`
    *   注释字符串: `C++`
    
*   D支持
    
    *   依赖符号: `BR2_TOOLCHAIN_HAS_DLANG`
    *   注释字符串: `Dlang`
    
*   Fortran支持
    
    *   依赖符号: `BR2_TOOLCHAIN_HAS_FORTRAN`
    *   注释字符串: `fortran`
    
*   thread支持
    
    *   依赖符号: `BR2_TOOLCHAIN_HAS_THREADS`
    *   注释字符串: `threads` (除非还需要`BR2_TOOLCHAIN_HAS_THREADS_NPTL`，在这种情况下，只指定`NPTL`就足够了)
    
*   NPTL thread支持
    
    *   依赖符号: `BR2_TOOLCHAIN_HAS_THREADS_NPTL`
    *   注释字符串: `NPTL`
    
*   RPC支持
    
    *   依赖符号: `BR2_TOOLCHAIN_HAS_NATIVE_RPC`
    *   注释字符串: `RPC`
    
*   wchar支持
    
    *   依赖符号: `BR2_USE_WCHAR`
    *   注释字符串: `wchar`
    
*   动态库
    
    *   依赖符号: `!BR2_STATIC_LIBS`
    *   注释字符串: `dynamic library`
    

### 18.2.5. Dependencies on a Linux kernel built by buildroot

有些包需要由buildroot构建Linux内核。这些通常是内核模块或固件。应该在配置文件中添加注释。在文件中表达这种依赖关系，类似于工具链选项上的依赖关系。一般格式如下:

```bash
foo needs a Linux kernel to be built
```

如果同时依赖工具链选项和Linux内核，请使用以下格式:

```bash
foo needs a toolchain w/ featA, featB, featC and a Linux kernel to be built
```

### 18.2.6. Dependencies on udev /dev management

如果一个包需要udev /dev管理，它应该依赖符号`BR2_PACKAGE_HAS_UDEV`，并且应该添加以下注释:

```bash
foo needs udev /dev management
```

如果对工具链选项和udev /dev管理都有依赖，请使用以下格式:

```bash
foo needs udev /dev management and a toolchain w/ featA, featB, featC
```

### 18.2.7. Dependencies on features provided by virtual packages

有些功能可以由多个包提供，例如openGL库。

请参阅[第18.12节，“虚拟包的基础架构”](#1812-infrastructure-for-virtual-packages)了解更多关于虚拟包的信息。


## 18.3. The `.mk` file
--------------------

最后，这是最难的部分。创建名为`libfoo.mk`的文件。它描述了包应该如何下载、配置、构建、安装等。

根据包的类型，`.mk`文件必须以不同的方式编写，使用不同的基础架构:

*   **Makefiles for generic packages** (不使用autotools或CMake): 它们基于类似于基于autotools的包所使用的基础架构，但需要开发人员多做一些工作。它们指定了包的配置、编译和安装。所有不使用autotools作为构建系统的包都必须使用此基础架构。将来，可能会为其他构建系统编写其他专门的基础结构。我们会在一个[教程](#1861-generic-package-tutorial)和一个[引用](#1862-generic-package-reference)中全面介绍它们。
*   **Makefiles for autotools-based software** (autoconf, automake, 等): 我们为这些包提供了专用的基础架构，因为autotools是一个非常常见的构建系统。对于依赖autotools作为构建系统的新包，必须使用此基础架构。我们通过[教程](#1871-autotools-package-tutorial) 和 [引用](#1872-autotools-package-reference)来介绍它们。
*   **Makefiles for cmake-based software**: 我们为这些包提供了一个专用的基础架构，因为CMake是一个越来越常用的构建系统，并且具有标准化的行为。对于依赖于CMake的新包，必须使用此基础架构。我们通过[教程](#1881-cmake-package-tutorial) 和 [引用](#1882-cmake-package-reference)来介绍它们。
*   **Makefiles for Python modules**: 我们有一个专门的Python模块基础架构，使用`distutils`, `flit`, `pep517` 或 `setuptools`机制。我们会通过一个[教程](#1891-python-package-tutorial)和一个[引用](#1892-python-package-reference)来介绍它们。
*   **Makefiles for Lua modules**: 我们有一个专门的Lua模块的基础架构，可以通过LuaRocks网站获得。我们通过一个[教程](#18101-luarocks-package-tutorial)和一个[引用](#18102-luarocks-package-reference)来介绍它们。

进一步的格式化细节:参见 [写作规则](#162-the-mk-file).


## 18.4. The `.hash` file
----------------------

如果可能的话，你必须添加一个名为`libfoo.hash`的第三个文件，它包含了`libfoo`包下载文件的哈希值。不添加`.hash`文件的唯一原因是由于包的下载方式而无法进行hash检查。

当一个包有版本选择选项时，哈希文件可以存储在以版本命名的子目录中，例如:`package/libfoo/1.2.3/libfoo.hash`。如果不同版本具有不同的许可条款，但它们存储在同一个文件中，那么这一点尤其重要。否则，散列文件应该留在包的目录中。

存储在该文件中的哈希用于验证下载的文件和许可证文件的完整性。

这个文件的格式是，每个要检查散列值的文件一行，每一行由以下三个字段用两个空格分隔:

*   hash的类型，为其中之一:
    
    *   `md5`, `sha1`, `sha224`, `sha256`, `sha384`, `sha512`
    
*   文件的哈希值:
    
    *   对于`md5`, 32个十六进制字符
    *   对于`sha1`, 40个十六进制字符
    *   对于`sha224`, 56个十六进制字符
    *   对于`sha256`, 64个十六进制字符
    *   对于`sha384`, 96个十六进制字符
    *   对于`sha512`, 128个十六进制字符
    
*   文件名:
    
    *   对于源存档:文件的基本名，没有任何目录组件，
    *   对于许可证文件:它出现在`FOO_LICENSE_FILES`中的路径。
    

以`#`开头的行被认为是注释，会被忽略。空行被忽略。

一个文件可以有多个散列，每个散列在单独的一行上。在这种情况下，所有的散列值必须匹配。

**注意.** 理想情况下，存储在此文件中的哈希值应该与upstream发布的哈希值匹配，例如，在他们的网站上，在电子邮件公告中……(`sha1`和`sha512`)，然后最好将所有的哈希值添加到`.hash`文件中。如果upstream没有提供任何哈希值，或者只提供了`md5`哈希值，那么你自己至少计算一个强哈希值(最好是`sha256`，而不是`md5`)，并在哈希值上方的注释行中提到这一点。

**注意.** license文件的哈希用于在包版本发生变化时检测license变化。这些散列值会在make legal-info目标运行时进行检查。对于具有多个版本的包(如Qt5)，在该包的子目录`<packageversion>`中创建哈希文件(参见[第19.2节，“如何应用补丁”](#192-how-patches-are-applied))。

下面的例子定义了upstream发布的主要`libfoo-1.2.3.tar.bz2` tarball的`sha1`和`sha256`，来自upstream的`md5`和本地计算的`sha256`哈希值用于二进制blob， `sha256`用于下载的补丁，以及一个没有哈希值的归档文件:

```bash
# Hashes from: http://www.foosoftware.org/download/libfoo-1.2.3.tar.bz2.{sha1,sha256}:
sha1  486fb55c3efa71148fe07895fd713ea3a5ae343a  libfoo-1.2.3.tar.bz2
sha256  efc8103cc3bcb06bda6a781532d12701eb081ad83e8f90004b39ab81b65d4369  libfoo-1.2.3.tar.bz2

# md5 from: http://www.foosoftware.org/download/libfoo-1.2.3.tar.bz2.md5, sha256 locally computed:
md5  2d608f3c318c6b7557d551a5a09314f03452f1a1  libfoo-data.bin
sha256  01ba4719c80b6fe911b091a7c05124b64eeece964e09c058ef8f9805daca546b  libfoo-data.bin

# Locally computed:
sha256  ff52101fb90bbfc3fe9475e425688c660f46216d7e751c4bbdb1dc85cdccacb9  libfoo-fix-blabla.patch

# Hash for license files:
sha256  a45a845012742796534f7e91fe623262ccfb99460a2bd04015bd28d66fba95b8  COPYING
sha256  01b1f9f2c8ee648a7a596a1abe8aa4ed7899b1c9e5551bda06da6e422b04aa55  doc/COPYING.LGPL
```

如果`.hash`文件存在，并且它包含一个或多个下载文件的哈希值，Buildroot(下载后)计算的哈希值必须与存储在`.hash`文件中的哈希值匹配。如果一个或多个散列不匹配，Buildroot会认为这是一个错误，删除下载的文件并终止。

如果`.hash`文件存在，但它不包含下载文件的哈希值，Buildroot会认为这是一个错误并终止。然而，下载的文件被留在下载目录中，因为这通常表明`.hash`文件是错误的，但下载的文件可能是好的。

哈希值目前用于检查从http/ftp服务器获取的文件、Git仓库、使用scp复制的文件和本地文件。没有检查其他版本控制系统(如Subversion、CVS等)的哈希值，因为Buildroot目前在从这些版本控制系统获取源代码时不会生成可重复的压缩包。

哈希值只应该添加到`.hash`文件中，以保证文件的稳定性。例如，Github自动生成的补丁不能保证是稳定的，因此它们的哈希可能会随着时间的推移而变化。这样的补丁不应该被下载，而是被添加到本地的包文件夹中。

如果`.hash`文件缺失，则不会进行任何检查。


## 18.5. The `SNNfoo` start script
-------------------------------

提供系统守护进程的包通常需要在引导时以某种方式启动。Buildroot支持几个init系统，其中一些被认为是第一级(参见[第6.3节，“init系统”](#63-init-system))，而其他也可用，但没有相同的集成级别。理想情况下，所有提供系统守护进程的包都应该提供BusyBox/SysV init的启动脚本和systemd单元文件。

为了一致性，启动脚本必须遵循参考文件中所示的样式和组成:`package/busybox/S01syslogd`。这个样式的注释示例如下所示。systemd单元文件没有特定的编码风格，但是如果一个包带有它自己的单元文件，如果它与buildroot兼容，则优先于buildroot特定的。

启动脚本的名称由`SNN`和守护进程的名称组成。`NN`是起始订单号，需要仔细选择。例如，一个需要联网的程序不应该在`S40network`之前启动。这些脚本是按照字母顺序启动的，所以`S01syslogd`会在`S01watchdogd`之前启动，而`S02sysctl`则在`S01syslogd`之后启动。

```bash
01: #!/bin/sh
02:
03: DAEMON="syslogd"
04: PIDFILE="/var/run/$DAEMON.pid"
05:
06: SYSLOGD_ARGS=""
07:
08: # shellcheck source=/dev/null
09: [ -r "/etc/default/$DAEMON" ] && . "/etc/default/$DAEMON"
10:
11: # BusyBox' syslogd does not create a pidfile, so pass "-n" in the command line
12: # and use "-m" to instruct start-stop-daemon to create one.
13: start() {
14:     printf 'Starting %s: ' "$DAEMON"
15:     # shellcheck disable=SC2086 # we need the word splitting
16:     start-stop-daemon -b -m -S -q -p "$PIDFILE" -x "/sbin/$DAEMON" \
17:             -- -n $SYSLOGD_ARGS
18:     status=$?
19:     if [ "$status" -eq 0 ]; then
20:             echo "OK"
21:     else
22:             echo "FAIL"
23:     fi
24:     return "$status"
25: }
26:
27: stop() {
28:     printf 'Stopping %s: ' "$DAEMON"
29:     start-stop-daemon -K -q -p "$PIDFILE"
30:     status=$?
31:     if [ "$status" -eq 0 ]; then
32:             rm -f "$PIDFILE"
33:             echo "OK"
34:     else
35:             echo "FAIL"
36:     fi
37:     return "$status"
38: }
39:
40: restart() {
41:     stop
42:     sleep 1
43:     start
44: }
45:
46: case "$1" in
47:     start|stop|restart)
48:             "$1";;
49:     reload)
50:             # Restart, since there is no true "reload" feature.
51:             restart;;
52:     *)
53:             echo "Usage: $0 {start|stop|restart|reload}"
54:             exit 1
55: esac
```

**注意:** 支持以某种方式重新加载配置的程序(`SIGHUP`)应该提供一个类似于`stop()`的`reload()`函数。`start-stop-daemon`支持`-K -s HUP`。建议在所有的`start-stop-daemon`命令后都加上`-x "/sbin/$DAEMON"`，以确保信号被设置为与`$DAEMON`匹配的PID。

启动脚本和单元文件都可以从`/etc/default/foo`中获取命令行参数，一般来说，如果这样的文件不存在，它不应该阻止守护进程的启动，除非有一些站点特定的命令行参数，守护进程需要启动。对于启动脚本，`FOO_ARGS="-s -o -m -e -args"`可以被定义为默认值，用户可以在`/etc/default/foo`中覆盖它。


## 18.6. Infrastructure for packages with specific build systems
-------------------------------------------------------------

所谓特定构建系统的包，是指构建系统不是标准构建系统的所有包，如autotools或CMake。这通常包括构建系统基于手写makefile或shell脚本的包。

### 18.6.1. `generic-package` tutorial

```bash
01: ################################################################################
02: #
03: # libfoo
04: #
05: ################################################################################
06:
07: LIBFOO_VERSION = 1.0
08: LIBFOO_SOURCE = libfoo-$(LIBFOO_VERSION).tar.gz
09: LIBFOO_SITE = http://www.foosoftware.org/download
10: LIBFOO_LICENSE = GPL-3.0+
11: LIBFOO_LICENSE_FILES = COPYING
12: LIBFOO_INSTALL_STAGING = YES
13: LIBFOO_CONFIG_SCRIPTS = libfoo-config
14: LIBFOO_DEPENDENCIES = host-libaaa libbbb
15:
16: define LIBFOO_BUILD_CMDS
17:     $(MAKE) $(TARGET_CONFIGURE_OPTS) -C $(@D) all
18: endef
19:
20: define LIBFOO_INSTALL_STAGING_CMDS
21:     $(INSTALL) -D -m 0755 $(@D)/libfoo.a $(STAGING_DIR)/usr/lib/libfoo.a
22:     $(INSTALL) -D -m 0644 $(@D)/foo.h $(STAGING_DIR)/usr/include/foo.h
23:     $(INSTALL) -D -m 0755 $(@D)/libfoo.so* $(STAGING_DIR)/usr/lib
24: endef
25:
26: define LIBFOO_INSTALL_TARGET_CMDS
27:     $(INSTALL) -D -m 0755 $(@D)/libfoo.so* $(TARGET_DIR)/usr/lib
28:     $(INSTALL) -d -m 0755 $(TARGET_DIR)/etc/foo.d
29: endef
30:
31: define LIBFOO_USERS
32:     foo -1 libfoo -1 * - - - LibFoo daemon
33: endef
34:
35: define LIBFOO_DEVICES
36:     /dev/foo c 666 0 0 42 0 - - -
37: endef
38:
39: define LIBFOO_PERMISSIONS
40:     /bin/foo f 4755 foo libfoo - - - - -
41: endef
42:
43: $(eval $(generic-package))
```

Makefile从第7行到第11行开始包含元数据信息:包的版本(`LIBFOO_VERSION`)、包含该包的tar包的名称(`LIBFOO_SOURCE`) (xz-ed tarball推荐)、可以从互联网上下载tar包的位置(`LIBFOO_SITE`)、许可证(`LIBFOO_LICENSE`)和带有许可证文本的文件(`LIBFOO_LICENSE_FILES`)。所有变量必须以相同的前缀开头，本例中为`LIBFOO_`。这个前缀始终是包名的大写版本(请参阅下面以了解包名在何处定义)。

在第12行，我们指定这个包要在过渡空间中安装一些东西。对于库来说，这是经常需要的，因为它们必须在过渡空间中安装头文件和其他开发文件。这将确保执行`LIBFOO_INSTALL_STAGING_CMDS`变量中列出的命令。

在第13行，我们指定要对在`LIBFOO_INSTALL_STAGING_CMDS`阶段安装的一些libfoo-config文件进行一些修复。这些*-config文件是位于$(STAGING_DIR)/usr/bin目录下的可执行shell脚本文件，由其他第三方包执行，以查找此特定包的位置和链接标志。

问题是所有这些*-config文件默认都给出了错误的主机系统链接标志，不适合交叉编译。

例如:将-I/usr/include替换为-I$(STAGING_DIR)/usr/include或将-L$(STAGING_DIR)/usr/lib替换为-L$(STAGING_DIR)/usr/lib

因此，对这些脚本执行了一些sed魔法，使它们给出正确的标志。传给`LIBFOO_CONFIG_SCRIPTS`的参数是需要修复的shell脚本的文件名。所有这些名称都是相对于$(STAGING_DIR)/usr/bin的，如果需要，可以提供多个名称。

此外，列出在`LIBFOO_CONFIG_SCRIPTS`中的脚本将从`$(TARGET_DIR)/usr/bin`中删除，因为它们在目标上不需要。

**Example 18.1. Config script: divine package**

安装shell脚本$(STAGING_DIR)/usr/bin/divine-config。

所以它的修正如下:

```bash
DIVINE_CONFIG_SCRIPTS = divine-config
```

**Example 18.2. Config script: imagemagick package:**

imagemagick包安装以下脚本: $(STAGING_DIR)/usr/bin/{Magick,Magick++,MagickCore,MagickWand,Wand}-config

所以它的修正将是:

```bash
IMAGEMAGICK_CONFIG_SCRIPTS = \
   Magick-config Magick++-config \
   MagickCore-config MagickWand-config Wand-config
```

在第14行，我们指定了这个包依赖的依赖项列表。这些依赖项以小写包名的形式列出，可以是目标包(没有`host-`前缀)或主机包(有`host-`前缀)。Buildroot将确保在当前包开始配置之前构建并安装所有这些包。

Makefile的其余部分，第16行..29、定义了在包配置、编译和安装的不同步骤应该做什么。`LIBFOO_BUILD_CMDS`告诉我们应该执行哪些步骤来构建包。`LIBFOO_INSTALL_STAGING_CMDS`告诉我们应该执行哪些步骤将包安装到过渡空间中。`LIBFOO_INSTALL_TARGET_CMDS`告诉我们应该执行哪些步骤将包安装到目标空间中。

所有这些步骤都依赖于`$(@D)`变量，它包含了包的源代码被提取的目录。

第31..33行、我们定义一个此包使用的用户(例如，以非根用户身份运行守护进程)(`LIBFOO_USERS`)。

第35…37行、我们定义了这个包使用的设备节点文件(`LIBFOO_DEVICES`)。

第39..41行、我们定义权限来设置这个包(`LIBFOO_PERMISSIONS`)安装的特定文件。

最后，在第43行，我们调用`generic-package`函数，它根据前面定义的变量生成所有使包工作所需的Makefile代码。

### 18.6.2. `generic-package` reference

通用目标有两种变体。`generic-package`宏用于为目标交叉编译包。`host-generic-package`宏用于主机包，为主机原生编译。可以在一个`.mk`文件中调用它们:一次创建规则以生成目标包，一次创建规则以生成主机包:

```bash
$(eval $(generic-package))
$(eval $(host-generic-package))
```

如果目标包的编译需要在主机上安装一些工具，这可能很有用。如果包名是`libfoo`，那么目标的包名也是`libfoo`，而主机的包名是`host-libfoo`。如果其他包依赖于`libfoo`或`host-libfoo`，则应该在它们的依赖变量中使用这些名称。

对`generic-package`和/或`host-generic-package`宏的调用**必须**在`.mk`文件的末尾，在所有变量定义之后。调用`host-generic-package` **必须**在调用`generic-package`之后(如果有的话)。

对于目标包，`generic-package`使用.mk文件定义的变量，并以大写的包名`LIBFOO_*`作为前缀。`host-generic-package`使用`HOST_LIBFOO_*`变量。对于某些变量，如果前缀为`HOST_LIBFOO_`的变量不存在，则包基础架构会使用前缀为`LIBFOO_`的对应变量。如果变量在target包和host包中具有相同的值，则执行此操作。详情见下文。

可以在`.mk`文件中设置以提供元数据信息的变量列表如下(假设包名为`libfoo`):

*   `LIBFOO_VERSION`, 强制，必须包含包的版本。请注意，如果`HOST_LIBFOO_VERSION`不存在，则假定它与`LIBFOO_VERSION`相同。它也可以是一个版本号或直接从版本控制系统获取的包的标签。例子:
    
    *   tarball的发布版本: `LIBFOO_VERSION = 0.1.2`
    *   git树的sha1算法: `LIBFOO_VERSION = cb9d6aa9429e838f0e54faa3d455bcbab5eef057`
    *   git树的标签 `LIBFOO_VERSION = v0.1.2`
        
        **注意:** 使用分支名称作为`FOO_VERSION`是不支持的，因为它不能像人们期望的那样工作:
        
        1.  由于本地缓存的原因，Buildroot不会重新获取存储库，所以那些希望能够跟踪远程存储库的人会非常惊讶和失望;
        2.  因为两个构建永远不可能完美地同时进行，也因为远程仓库可能随时会在分支上有新的提交，所以两个使用相同Buildroot树并构建相同配置的用户可能会得到不同的源，从而导致构建不可重现，人们会非常惊讶和失望。
        
    
*   `LIBFOO_SOURCE`可能包含包的压缩包名称，Buildroot将使用该名称从`LIBFOO_SITE`下载压缩包。如果未指定`HOST_LIBFOO_SOURCE`，则默认为`LIBFOO_SOURCE`。如果没有指定，则默认为`libfoo-$(LIBFOO_VERSION).tar.gz`。例如:`LIBFOO_SOURCE = foobar-$(LIBFOO_VERSION).tar.bz2`
*   `LIBFOO_PATCH`可能包含一个以空格分隔的补丁文件名列表，Buildroot将下载这些文件名并将其应用于包的源代码。如果一个条目包含`://`，那么Buildroot将假设它是一个完整的URL并从这个位置下载补丁。否则，Buildroot将假设补丁应该从`LIBFOO_SITE`下载。如果未指定`HOST_LIBFOO_PATCH`，则默认为`LIBFOO_PATCH`。请注意，Buildroot本身中包含的补丁使用不同的机制:Buildroot中的包目录中所有以`*.patch`形式存在的文件将在提取后应用于包(请参阅[修补包](#chapter-19-patching-a-package))。最后，`LIBFOO_PATCH`变量中列出的补丁会在Buildroot包目录中存储的补丁之前应用。
*   `LIBFOO_SITE`提供了包的位置，可以是URL或本地文件系统路径。HTTP、FTP和SCP是支持的获取压缩包的URL类型。在这种情况下，不要包含结尾的斜杠:它将由Buildroot在适当的情况下添加到目录和文件名之间。Git、Subversion、Mercurial和Bazaar都支持直接从源代码管理系统检索包的URL类型。有一个辅助函数可以更容易地从GitHub下载源代码压缩包(请参阅[第18.25.4节，“如何从GitHub添加包”](#18254-how-to-add-a-package-from-github)了解详情)。文件系统路径可以用来指定一个压缩包或者一个包含包源代码的目录。请参阅下面的`LIBFOO_SITE_METHOD`了解更多关于检索工作的细节。注意，SCP URLs的形式应该是`scp://[user@]host:filepath`，而filepath是相对于用户的主目录而言的，所以你可能想在路径前加上一个斜杠表示绝对路径:`scp://[user@]host:/absolutepath`。SFTP URLs也是如此。如果未指定`HOST_LIBFOO_SITE`，则默认为`LIBFOO_SITE`。 例子: `LIBFOO_SITE=http://www.libfoosoftware.org/libfoo` `LIBFOO_SITE=http://svn.xiph.org/trunk/Tremor` `LIBFOO_SITE=/opt/software/libfoo.tar.gz` `LIBFOO_SITE=$(TOPDIR)/../src/libfoo`
*   `LIBFOO_DL_OPTS`是一个以空格分隔的附加选项列表，用于传递给下载器。用于检索服务器端检查用户登录名和密码的文档，或使用代理。支持所有对`LIBFOO_SITE_METHOD`有效的下载方法;有效的选项取决于下载方法(有关下载实用程序，请参阅手册页)。
*   `LIBFOO_EXTRA_DOWNLOADS`是一个由空格分隔的附加文件列表，Buildroot应该下载这些文件。如果条目包含`://`，那么Buildroot将假设它是一个完整的URL，并将使用此URL下载文件。否则，Buildroot将假定要下载的文件位于`LIBFOO_SITE`。Buildroot不会对这些额外的文件做任何处理，只会下载它们:从`$(LIBFOO_DL_DIR)`使用它们取决于包的配方。
*   `LIBFOO_SITE_METHOD`确定了用于获取或复制包源代码的方法。在很多情况下，Buildroot会根据`LIBFOO_SITE`的内容来猜测这个方法，并没有必要设置`LIBFOO_SITE_METHOD`。当未指定`HOST_LIBFOO_SITE_METHOD`时，默认为`LIBFOO_SITE_METHOD`的值。`LIBFOO_SITE_METHOD`可能的值有:
    
    *   `wget`用于普通的FTP/HTTP压缩包下载。当`LIBFOO_SITE`以`http://`, `https://` 或 `ftp://`开头时，默认使用。
    *   `scp`用于使用scp通过SSH下载压缩包。当`LIBFOO_SITE`以`scp://`开头时，默认使用。
    *   `sftp`用于通过sftp通过SSH下载压缩包。当`LIBFOO_SITE`以`sftp://`开头时，默认使用。
    *   `svn`用于从Subversion仓库中检索源代码。当`LIBFOO_SITE`以`svn://`开头时，默认使用。当在`LIBFOO_SITE`中指定`http://` Subversion存储库URL时，必须指定`LIBFOO_SITE_METHOD=svn`。Buildroot执行签出操作，并以压缩包的形式保存在下载缓存中;后续的构建使用tar压缩包，而不是执行另一个签出。
    *   `cvs`用于从cvs存储库中检索源代码。当`LIBFOO_SITE`以`cvs://`开头时，默认使用。下载的源代码会像`svn`方法一样被缓存。假定匿名pserver模式是在`LIBFOO_SITE`中明确定义的。`LIBFOO_SITE=cvs://libfoo.net:/cvsroot/libfoo` 和 `LIBFOO_SITE=cvs://:ext:libfoo.net:/cvsroot/libfoo`都是被接受的，假设是之前的匿名pserver访问模式。`LIBFOO_SITE`必须包含源URL以及远程仓库目录。模块就是包的名称。`LIBFOO_VERSION`是强制性的，必须是一个标签、分支或日期(例如:“2014-10-20”、“2014-10-20 13:45”、“2014-10-20 13:45+01”详见“man cvs”)。
    *   `git`用于从git仓库中检索源代码。当`LIBFOO_SITE`以`git://`开头时，默认使用。下载的源代码会像`svn`方法一样被缓存。
    *   `hg`用于从Mercurial存储库中检索源代码。当`LIBFOO_SITE`包含Mercurial仓库的URL时，必须指定`LIBFOO_SITE_METHOD=hg`。下载的源代码会像`svn`方法一样被缓存。
    *   `bzr`用于从Bazaar存储库检索源代码。当`LIBFOO_SITE`以`bzr://`开头时，默认使用。下载的源代码会像`svn`方法一样被缓存。
    *   `file`用于本地压缩文件。当`LIBFOO_SITE`指定包的压缩包作为本地文件名时，应该使用它。对于未公开或未在版本控制中提供的软件很有用。
    *   `local`表示本地源代码目录。当`LIBFOO_SITE`指定包含包源代码的本地目录路径时，应该使用它。Buildroot将源目录的内容复制到包的构建目录中。请注意，对于`local`包，没有应用补丁。如果你仍然需要为源代码打补丁，请使用`LIBFOO_POST_RSYNC_HOOKS`，参见[18.23.1节，“使用`POST_RSYNC`钩子”](#18231-using-the-post_rsync-hook)。
    
*   可以将`LIBFOO_GIT_SUBMODULES`设置为`YES`来在仓库中创建一个git子模块的归档。这仅适用于通过git下载的包(即`LIBFOO_SITE_METHOD=git`)。注意，当git子模块包含打包的库时，我们尽量不使用这些git子模块，在这种情况下，我们更喜欢使用它们自己的包中的库。
*   如果Git仓库使用Git LFS在带外存储大文件，则`LIBFOO_GIT_LFS`应该设置为`YES`。这仅适用于通过git下载的包(即`LIBFOO_SITE_METHOD=git`)。
*   `LIBFOO_STRIP_COMPONENTS`是tar解压时必须从文件名中剥离(strip)的主要组件(目录)的数量。大多数包的压缩包都有一个名为"<pkg-name>-<pkg-version>"的主组件，因此Buildroot将--strip-components=1传递给tar以删除它。对于没有这个组件的非标准包，或者有多个要剥离(strip)的前导组件，用要传递给tar的值设置这个变量。默认值:1。
*   `LIBFOO_EXCLUDES`是一个以空格分隔的模式列表，在提取归档时需要排除。列表中的每一项都作为tar的`--exclude`选项传递。默认为空。
*   `LIBFOO_DEPENDENCIES`列出了当前目标包编译所需的依赖项(根据包名)。这些依赖项保证在当前包的配置开始之前被编译和安装。但是，对这些依赖项配置的修改不会强制重新构建当前包。类似地，`HOST_LIBFOO_DEPENDENCIES`列出了当前主机包的依赖项。
*   `LIBFOO_EXTRACT_DEPENDENCIES`根据包名列出了当前目标包提取所需的依赖项。这些依赖项保证在当前包的提取步骤开始之前被编译和安装。这仅由包基础架构内部使用，通常不应由包直接使用。
*   `LIBFOO_PATCH_DEPENDENCIES`列出了当前包需要打补丁的依赖项(根据包名)。在为当前包打补丁之前，这些依赖项可以保证被提取并打补丁(但不一定是构建)。类似地，`HOST_LIBFOO_PATCH_DEPENDENCIES`列出了当前主机包的依赖项。这很少使用;通常，`LIBFOO_DEPENDENCIES`才是你真正想要使用的。
*   `LIBFOO_PROVIDES`列出了`libfoo`实现的所有虚拟包。参见[章节18.12，“虚拟包的基础架构”](#1812-infrastructure-for-virtual-packages)。
*   `LIBFOO_INSTALL_STAGING`可以设置为`YES`或`NO`(默认值)。如果设置为“YES”，那么将执行`LIBFOO_INSTALL_STAGING_CMDS`变量中的命令，将包安装到过渡目录中。
*   `LIBFOO_INSTALL_TARGET`可以设置为`YES`(默认值)或`NO`。如果设置为`YES`，那么将执行`LIBFOO_INSTALL_TARGET_CMDS`变量中的命令，将包安装到目标目录中。
*   `LIBFOO_INSTALL_IMAGES`可以设置为`YES`或`NO`(默认值)。如果设置为`YES`，那么将执行`LIBFOO_INSTALL_IMAGES_CMDS`变量中的命令，将包安装到images目录中。
*   `LIBFOO_CONFIG_SCRIPTS`列出了$(STAGING_DIR)/usr/bin中需要一些特殊修复的文件的名称，以使它们对交叉编译友好。可以给出多个以空格分隔的文件名，并且都是相对于$(STAGING_DIR)/usr/bin而言的。列出在`LIBFOO_CONFIG_SCRIPTS`中的文件也会从`$(TARGET_DIR)/usr/bin`中删除，因为目标上不需要它们。
*   `LIBFOO_DEVICES`列出了Buildroot在使用静态设备表时创建的设备文件。要使用的语法是makedevs。你可以在[第25章Makedev语法文档](#chapter-25-makedev-syntax-documentation)中找到有关这种语法的文档。该变量是可选的。
*   `LIBFOO_PERMISSIONS`列出了在构建过程结束时要完成的权限更改。语法仍然是makedevs。你可以在[第25章Makedev语法文档](#chapter-25-makedev-syntax-documentation)中找到有关这种语法的文档。该变量是可选的。
*   `LIBFOO_USERS`列出了要为这个包创建的用户，如果它安装了一个你想以特定用户身份运行的程序(例如，作为守护进程，或作为cron-job)。它的语法在精神上和makedevs类似，可以在[第26章Makeusers语法文档](#chapter-26-makeusers-syntax-documentation)中描述。该变量是可选的。
*   `LIBFOO_LICENSE`定义了这个包发布的许可证。这个名字会出现在`make legal-info`生成的清单文件中。如果license出现在[SPDX license列表](https://spdx.org/licenses/)中，请使用SPDX短标识符使manifest文件保持一致。否则，以精确和简洁的方式描述许可证，避免使用诸如`BSD`这样模棱两可的名称，因为它们实际上命名了一系列许可证。该变量是可选的。如果未定义，`unknown`将出现在此包的清单文件的`license`字段中。这个变量的预期格式必须符合以下规则:
    
    *   如果包的不同部分在不同的许可证下发布，则使用`逗号`分开的许可证(例如:`` `LIBFOO_LICENSE = GPL-2.0+, LGPL-2.1+` ``)。如果哪个组件在哪个许可证下获得许可有明确的区别，那么在许可证上加上该组件的注释(例如: `` `LIBFOO_LICENSE = GPL-2.0+ (programs), LGPL-2.1+ (libraries)` ``)。
    *   如果某些许可是以启用子选项为条件的，在条件许可后面加上逗号(例如:`FOO_LICENSE += , GPL-2.0+ (programs)`);基础架构会在内部删除逗号之前的空格。
    *   如果包是双重许可的，则使用`or`关键字进行单独的许可(例如:`` `LIBFOO_LICENSE = AFL-2.1 或 GPL-2.0+` ``)。
    
*   `LIBFOO_LICENSE_FILES`是压缩包中以空格分隔的文件列表，其中包含了该包发布时所使用的许可证。`make legal-info`复制`legal-info`目录下的所有文件。更多信息请参阅[第13章，法律通知和许可](#chapter-13-legal-notice-and-licensing)。该变量是可选的。如果它没有定义，将会产生一个警告让你知道，并且`not saved`将出现在此包的清单文件的`license files`字段中。
*   `LIBFOO_ACTUAL_SOURCE_TARBALL`只适用于`LIBFOO_SITE` / `LIBFOO_SOURCE`对指向的归档文件实际上不包含源代码，而是二进制代码的包。这是一个非常不常见的情况，只适用于已经编译的外部工具链，尽管理论上它可能适用于其他包。在这种情况下，实际的源代码通常附带一个单独的tarball。将`LIBFOO_ACTUAL_SOURCE_TARBALL`设置为实际源代码存档的名称，Buildroot将下载它，并在你运行`make legal-info`来收集法律相关的材料时使用它。注意此文件不会在常规构建期间下载，也不会通过`make source`下载。
*   `LIBFOO_ACTUAL_SOURCE_SITE`提供了实际源代码压缩包的位置。默认值是`LIBFOO_SITE`，所以如果二进制归档文件和源归档文件在同一个目录下，你不需要设置这个变量。如果没有设置`LIBFOO_ACTUAL_SOURCE_TARBALL`，那么定义`LIBFOO_ACTUAL_SOURCE_SITE`是没有意义的。
*   `LIBFOO_REDISTRIBUTE`可以设置为`YES`(默认值)或`NO`，以表示包的源代码是否允许被重新分发。对于非开源包，将其设置为`NO`: Buildroot在收集`legal-info`时将不会保存此包的源代码。
*   `LIBFOO_FLAT_STACKSIZE`以扁平二进制格式定义了应用程序的堆栈大小。NOMMU体系结构处理器上的应用程序堆栈大小在运行时不能扩大。普通二进制格式的默认堆栈大小只有4k字节。如果应用程序消耗更多的堆栈，则在此处追加所需的数字。
*   `LIBFOO_BIN_ARCH_EXCLUDE`是一个以空格分隔的路径列表(相对于目标目录)，在检查包是否正确安装交叉编译的二进制文件时需要忽略。你很少需要设置这个变量，除非包安装了默认位置(`/lib/firmware`, `/usr/lib/firmware`, `/lib/modules`, `/usr/lib/modules`, 和 `/usr/share`)之外的二进制数据块，这些位置会被自动排除。
*   `LIBFOO_IGNORE_CVES`是一个由空格分隔的CVE列表，它告诉Buildroot CVE跟踪工具此包应忽略哪些CVE。当CVE通过包中的补丁修复时，或者由于某种原因CVE没有影响Buildroot包时，通常使用此选项。在向该变量添加CVE之前必须始终使用Makefile注释。例子:

```bash
# 0001-fix-cve-2020-12345.patch
LIBFOO_IGNORE_CVES += CVE-2020-12345
# only when built with libbaz, which Buildroot doesn't support
LIBFOO_IGNORE_CVES += CVE-2020-54321
```

*   `LIBFOO_CPE_ID_*` 变量是一组变量，允许包定义它的[CPE标识符](https://nvd.nist.gov/products/cpe)。可用的变量有:
    
    *   `LIBFOO_CPE_ID_PREFIX`, 指定CPE标识符的前缀，即前三个字段。当未定义时，默认值为`cpe:2.3:a`。
    *   `LIBFOO_CPE_ID_VENDOR`, 指定CPE标识符的供应商部分。当未定义时，默认值为`<pkgname>_project`。
    *   `LIBFOO_CPE_ID_PRODUCT`, 指定CPE标识符的产品部分。当未定义时，默认值是`<pkgname>`。
    *   `LIBFOO_CPE_ID_VERSION`, 指定CPE标识符的版本部分。当未定义时，默认值是`$(LIBFOO_VERSION)`。
    *   `LIBFOO_CPE_ID_UPDATE` 指定CPE标识符的更新部分。当未定义时，默认值为`*`。
    
    如果定义了这些变量中的任何一个，那么通用包基础架构将假定包提供了有效的CPE信息。在这种情况下，通用的包基础架构将定义`LIBFOO_CPE_ID`。
    
    对于主包来说，如果它的`LIBFOO_CPE_ID_*`变量没有定义，它就会从相应的目标包中继承这些变量的值。
    

推荐使用下面的语法来定义这些变量:

```bash
LIBFOO_VERSION = 2.32
```

现在是定义在构建过程的不同步骤中应该执行什么操作的变量。

*   `LIBFOO_EXTRACT_CMDS`列出了提取包需要执行的操作。这通常是不需要的，因为tar包由Buildroot自动处理。但是，如果包使用非标准的归档格式，例如ZIP或RAR文件，或者具有非标准组织的压缩包，则此变量允许覆盖包基础架构的默认行为。
*   `LIBFOO_CONFIGURE_CMDS`列出了在编译包之前需要执行的配置操作。
*   `LIBFOO_BUILD_CMDS`列出了编译包时需要执行的操作。
*   当包是主机包时，`HOST_LIBFOO_INSTALL_CMDS`列出了安装包需要执行的操作。该包必须将其文件安装到`$(HOST_DIR)`指定的目录中。应该安装所有文件，包括开发文件(如头文件)，因为其他包可能在此包之上编译。
*   当包是目标包时，`LIBFOO_INSTALL_TARGET_CMDS`列出了将包安装到目标目录所需执行的操作。该包必须将其文件安装到`$(TARGET_DIR)`指定的目录中。只需要安装执行包所需的文件。当目标文件系统最终完成时，头文件、静态库和文档将再次被删除。
*   当包是目标包时，`LIBFOO_INSTALL_STAGING_CMDS`列出了将包安装到过渡目录所需执行的操作。这个包必须将它的文件安装到`$(STAGING_DIR)`指定的目录中。应该安装所有的开发文件，因为编译其他包可能需要它们。
*   当包是目标包时，`LIBFOO_INSTALL_IMAGES_CMDS`列出了将包安装到images目录所需执行的操作。该包必须将其文件安装到`$(BINARIES_DIR)`指定的目录中。只有不属于`TARGET_DIR`但对于引导板是必要的二进制图像(又名图像)文件应该放在这里。例如，如果一个包有类似于内核映像、引导装载程序或根文件系统映像的二进制文件，那么它应该利用这一步。
*   `LIBFOO_INSTALL_INIT_SYSV`, `LIBFOO_INSTALL_INIT_OPENRC` 和 `LIBFOO_INSTALL_INIT_SYSTEMD`列出了为类似systemv的init系统(busybox、sysvinit等)、openrc或systemd单元安装初始化脚本的操作。这些命令只有在安装了相关的init系统时才会运行(即，如果在配置中选择systemd作为init系统，则只会运行`LIBFOO_INSTALL_INIT_SYSTEMD`)。唯一的例外是当openrc被选为init系统并且没有设置`LIBFOO_INSTALL_INIT_OPENRC`时，这种情况下`LIBFOO_INSTALL_INIT_SYSV`将被调用，因为openrc支持sysv初始化脚本。当systemd用作init系统时，buildroot将在镜像构建的最后阶段使用`systemctl preset-all`命令自动启用所有服务。您可以添加预设文件来防止某个特定单元被buildroot自动启用。
*   `LIBFOO_HELP_CMDS`列出了打印包帮助的操作，它包含在主`make help`输出中。这些命令可以以任何格式打印任何内容。这很少使用，因为包很少有自定义规则。**不要使用这个变量**，除非你真的知道需要打印帮助信息。
*   `LIBFOO_LINUX_CONFIG_FIXUPS`列出了构建和使用此软件包所需的Linux内核配置选项，没有这些配置，软件包将从根本上被破坏。这是对kconfig调整选项之一的调用: `KCONFIG_ENABLE_OPT`, `KCONFIG_DISABLE_OPT`, or `KCONFIG_SET_OPT`. 这很少使用，因为package对内核选项通常没有严格的要求。

定义这些变量的首选方式是:

```bash
define LIBFOO_CONFIGURE_CMDS
        action 1
        action 2
        action 3
endef
```

在操作定义中，你可以使用以下变量:

*   `$(LIBFOO_PKGDIR)` 包含`libfoo.mk`和`Config.in`文件的目录路径。当需要安装捆绑在Buildroot中的文件时，这个变量很有用，比如运行时配置文件、splashscreen镜像……
*   `$(@D)`, 其中包含软件包源代码未压缩的目录。
*   `$(LIBFOO_DL_DIR)` 包含Buildroot为`libfoo`下载的所有文件所在的目录路径。
*   `$(TARGET_CC)`, `$(TARGET_LD)`, 等，以获得目标交叉编译实用程序。
*   `$(TARGET_CROSS)` 获取交叉编译工具链前缀
*   当然，需要使用`$(HOST_DIR)`, `$(STAGING_DIR)` 和 `$(TARGET_DIR)`变量来正确安装包。这些变量指向全局的主机、暂存和目标目录，除非使用了每个包目录支持，在这种情况下，它们指向当前包主机、暂存和目标目录。在这两种情况下，从包的角度来看没有任何区别:它应该简单地使用`HOST_DIR`, `STAGING_DIR` 和 `TARGET_DIR`。请参阅[第8.12节，“顶层并行构建”](#812-top-level-parallel-build)了解关于每个包目录支持的更多细节。

最后，你也可以使用hooks。参见 [18.23节“不同构建步骤中可用的钩子”](#1823-hooks-available-in-the-various-build-steps) 获取更多信息。


## 18.7. Infrastructure for autotools-based packages
-------------------------------------------------

### 18.7.1. `autotools-package` tutorial

首先，让我们看看如何为基于autotools的包写一个`.mk`文件，举个例子:

```bash
01: ################################################################################
02: #
03: # libfoo
04: #
05: ################################################################################
06:
07: LIBFOO_VERSION = 1.0
08: LIBFOO_SOURCE = libfoo-$(LIBFOO_VERSION).tar.gz
09: LIBFOO_SITE = http://www.foosoftware.org/download
10: LIBFOO_INSTALL_STAGING = YES
11: LIBFOO_INSTALL_TARGET = NO
12: LIBFOO_CONF_OPTS = --disable-shared
13: LIBFOO_DEPENDENCIES = libglib2 host-pkgconf
14:
15: $(eval $(autotools-package))
```

第7行声明了包的版本号。

在第8行和第9行，我们声明了压缩包的名称(建议使用xz-ed压缩包)以及压缩包在Web上的位置。Buildroot会自动从这个位置下载压缩包。

第10行告诉Buildroot把这个包安装到过渡目录中。staging目录位于`output/staging/`中，是所有包的安装目录，包括它们的开发文件等。默认情况下，包不会安装到过渡目录，因为通常只需要将库安装到过渡目录中:它们的开发文件需要用于编译依赖它们的其他库或应用程序。另外，默认情况下，当启用临时安装时，包会使用`make install`命令安装在此位置。

在第11行，我们告诉Buildroot不要把这个包安装到目标目录。该目录将成为在目标上运行的根文件系统。对于纯粹的静态库，没有必要将它们安装在目标目录中，因为它们不会在运行时使用。默认情况下，目标安装是启用的;几乎不需要将这个变量设置为NO。默认情况下，包也使用`make install`命令安装在此位置。

在第12行，我们告诉Buildroot传递一个自定义配置选项，它将被传递给`./configure`脚本，然后配置和构建包。

在第13行，我们声明了依赖，以便在包的构建过程开始之前构建它们。

最后，在第15行，我们调用`autotools-package`宏，该宏生成所有实际允许构建包的Makefile规则。

### 18.7.2. `autotools-package` reference

autotools包基础架构的主要宏是`autotools-package`。它类似于`generic-package`宏。也可以使用`host-autotools-package`宏来获得目标包和主机包。

就像通用基础结构一样，autotools基础结构通过在调用`autotools-package`宏之前定义许多变量来工作。

首先，在通用基础架构中存在的所有包元数据信息变量也存在于autotools基础架构中: `LIBFOO_VERSION`, `LIBFOO_SOURCE`, `LIBFOO_PATCH`, `LIBFOO_SITE`, `LIBFOO_SUBDIR`, `LIBFOO_DEPENDENCIES`, `LIBFOO_INSTALL_STAGING`, `LIBFOO_INSTALL_TARGET`.

还可以定义一些特定于autotools基础架构的额外变量。它们中的许多只在非常特定的情况下有用，因此典型的包只会使用其中的一些。

*   `LIBFOO_SUBDIR`可以包含配置脚本所在包中的子目录名称。例如，如果主配置脚本不在由压缩包提取的树的根中，那么这是有用的。如果未指定`HOST_LIBFOO_SUBDIR`，则默认为`LIBFOO_SUBDIR`。
*   `LIBFOO_CONF_ENV`, 指定传递给配置脚本的附加环境变量。默认为空。
*   `LIBFOO_CONF_OPTS`, 指定传递给配置脚本的其他配置选项。默认为空。
*   `LIBFOO_MAKE`, 指定另一个`make`命令。当在配置中启用了并行make(使用`BR2_JLEVEL`)，但由于某种原因，应该对给定的包禁用此功能时，这通常很有用。默认设置为`$(MAKE)`。如果该包不支持并行构建，则应将其设置为`LIBFOO_MAKE=$(MAKE1)`。
*   `LIBFOO_MAKE_ENV`, 指定在构建步骤中要传递的附加环境变量。这些参数在`make`命令之前传递。默认为空。
*   `LIBFOO_MAKE_OPTS`, 指定在构建步骤中要传递的附加变量。这些参数在`make`命令之后传递。默认为空。
*   `LIBFOO_AUTORECONF`, 告诉包是否应该被自动重新配置(即配置脚本和Makefile。在文件中应该通过重新运行autoconf, automake, libtool等重新生成)。有效值是`YES`和`NO`。默认值为`NO`。
*   `LIBFOO_AUTORECONF_ENV`, 如果`LIBFOO_AUTORECONF=YES`，则指定额外的环境变量传递给autoreconf程序。这些参数是在autoreconf命令的环境中传递的。默认为空。
*   `LIBFOO_AUTORECONF_OPTS` 如果`LIBFOO_AUTORECONF=YES`，则指定传递给autoreconf程序的其他选项。默认为空。
*   `LIBFOO_AUTOPOINT`, 告诉包是否应该被自动指向(即包是否需要复制I18N基础架构)。仅当`LIBFOO_AUTORECONF=YES`时有效。有效值是`YES`和`NO`。默认值是`NO`。
*   `LIBFOO_LIBTOOL_PATCH` 说明是否应该应用用于修复libtool交叉编译问题的Buildroot补丁。有效值是`YES`和`NO`。默认值为`YES`。
*   `LIBFOO_INSTALL_STAGING_OPTS` 包含用于将包安装到过渡目录的make选项。默认值是`DESTDIR=$(STAGING_DIR) install`，这对于大多数autotools包都是正确的。仍然可以覆盖它。
*   `LIBFOO_INSTALL_TARGET_OPTS` 包含用于将包安装到目标目录的make选项。默认值为`DESTDIR=$(TARGET_DIR) install`。大多数autotools包的默认值都是正确的，但如果需要，仍然可以覆盖它。

使用autotools基础架构，已经定义了构建和安装包所需的所有步骤，并且它们通常适用于大多数基于autotools的包。但是，在需要时，仍然可以自定义任何特定步骤的操作:

*   通过添加一个操作后钩子(在提取、打补丁、配置、构建或安装之后)。请参阅[18.23节“不同构建步骤中可用的钩子”](#1823-hooks-available-in-the-various-build-steps)了解详情。
*   通过重写其中一个步骤。例如，即使使用了autotools基础架构，如果包中的`.mk`文件定义了自己的`LIBFOO_CONFIGURE_CMDS`变量，它将被使用，而不是默认的autotools变量。但是，应该限制在非常特殊的情况下使用这种方法。一般情况下不要使用它。


## 18.8. Infrastructure for CMake-based packages
---------------------------------------------

### 18.8.1. `cmake-package` tutorial

首先，让我们看看如何为基于cmake的包编写`.mk`文件，下面是一个例子:

```bash
01: ################################################################################
02: #
03: # libfoo
04: #
05: ################################################################################
06:
07: LIBFOO_VERSION = 1.0
08: LIBFOO_SOURCE = libfoo-$(LIBFOO_VERSION).tar.gz
09: LIBFOO_SITE = http://www.foosoftware.org/download
10: LIBFOO_INSTALL_STAGING = YES
11: LIBFOO_INSTALL_TARGET = NO
12: LIBFOO_CONF_OPTS = -DBUILD_DEMOS=ON
13: LIBFOO_DEPENDENCIES = libglib2 host-pkgconf
14:
15: $(eval $(cmake-package))
```

第7行声明了包的版本号。

在第8行和第9行，我们声明了压缩包的名称(建议使用xz-ed压缩包)以及压缩包在Web上的位置。Buildroot会自动从这个位置下载压缩包。

第10行告诉Buildroot把这个包安装到过渡目录中。staging目录位于`output/staging/`中，是所有包的安装目录，包括它们的开发文件等。默认情况下，包不会安装到过渡目录，因为通常只需要将库安装到过渡目录中:它们的开发文件需要用于编译依赖它们的其他库或应用程序。另外，默认情况下，当启用临时安装时，包会使用`make install`命令安装在此位置。

在第11行，我们告诉Buildroot不要把这个包安装到目标目录。该目录将成为在目标上运行的根文件系统。对于纯粹的静态库，没有必要将它们安装在目标目录中，因为它们不会在运行时使用。默认情况下，目标安装是启用的;几乎不需要将这个变量设置为NO。默认情况下，包也使用`make install`命令安装在此位置。

在第12行，我们告诉Buildroot在配置包时向CMake传递自定义选项。

在第13行，我们声明了依赖，以便在包的构建过程开始之前构建它们。

最后，在第15行，我们调用`cmake-package`宏，该宏生成所有实际允许构建包的Makefile规则。

### 18.8.2. `cmake-package` reference

CMake包基础架构的主要宏是`cmake-package`。它类似于`generic-package`宏。也可以使用`host-cmake-package`宏来获取目标包和主机包。

就像通用基础架构一样，CMake基础架构通过在调用`cmake-package`宏之前定义许多变量来工作。

首先，存在于通用基础架构中的所有包元数据信息变量也存在于CMake基础架构中: `LIBFOO_VERSION`, `LIBFOO_SOURCE`, `LIBFOO_PATCH`, `LIBFOO_SITE`, `LIBFOO_SUBDIR`, `LIBFOO_DEPENDENCIES`, `LIBFOO_INSTALL_STAGING`, `LIBFOO_INSTALL_TARGET`.

还可以定义一些特定于CMake基础架构的额外变量。它们中的许多只在非常特定的情况下有用，因此典型的包只会使用其中的一些。

*   `LIBFOO_SUBDIR`可能包含包中包含主CMakeLists.txt文件的子目录的名称。例如，如果主CMakeLists.txt文件不在压缩包解压出来的树的根目录中，这就很有用。如果未指定`HOST_LIBFOO_SUBDIR`，则默认为`LIBFOO_SUBDIR`。
*   `LIBFOO_CONF_ENV`, 指定传递给CMake的附加环境变量。默认为空。
*   `LIBFOO_CONF_OPTS`, 指定传递给CMake的附加配置选项。默认为空。许多常见的CMake选项由`cmake-package`基础架构设置;所以通常不需要在包的`*.mk`文件中设置它们，除非你想覆盖它们:
    
    *   `CMAKE_BUILD_TYPE`由`BR2_ENABLE_RUNTIME_DEBUG`驱动;
    *   `CMAKE_INSTALL_PREFIX`;
    *   `BUILD_SHARED_LIBS`有`BR2_STATIC_LIBS`驱动;
    *   `BUILD_DOC`, `BUILD_DOCS` 被禁用;
    *   `BUILD_EXAMPLE`, `BUILD_EXAMPLES` 被禁用;
    *   `BUILD_TEST`, `BUILD_TESTS`, `BUILD_TESTING` 是禁用的。
    
*   当包不能在源代码树中构建而需要一个单独的构建目录时，应该设置`LIBFOO_SUPPORTS_IN_SOURCE_BUILD = NO`。
*   `LIBFOO_MAKE`，指定另一个`make`命令。当在配置中启用了parallel make(使用`BR2_JLEVEL`)，但由于某种原因，应该对给定的包禁用此功能时，这通常很有用。默认设置为`$(MAKE)`。如果该包不支持并行构建，则应将其设置为`LIBFOO_MAKE=$(MAKE1)`。
*   `LIBFOO_MAKE_ENV`, 指定在构建步骤中要传递的附加环境变量。这些参数在`make`命令之前传递。默认为空。
*   `LIBFOO_MAKE_OPTS`, 指定在构建步骤中要传递的附加变量。这些参数在`make`命令之后传递。默认为空。
*   `LIBFOO_INSTALL_OPTS` 包含用于将包安装到主机目录的make选项。默认值是`install`，这对于大多数CMake包都是正确的。仍然可以覆盖它。
*   `LIBFOO_INSTALL_STAGING_OPTS` 包含用于将包安装到过渡目录的make选项。默认值是`DESTDIR=$(STAGING_DIR) install/fast`，这对于大多数CMake包都是正确的。仍然可以覆盖它。
*   `LIBFOO_INSTALL_TARGET_OPTS` 包含用于将包安装到目标目录的make选项。默认值为`DESTDIR=$(TARGET_DIR) install/fast`。对于大多数CMake包来说，默认值是正确的，但如果需要，仍然可以覆盖它。

有了CMake基础架构，构建和安装包所需的所有步骤都已经定义好了，它们通常适用于大多数基于CMake的包。但是，在需要时，仍然可以自定义任何特定步骤的操作:

*   通过添加一个操作后钩子(在提取、打补丁、配置、构建或安装之后)。请参阅[18.23节“不同构建步骤中可用的钩子”](#1823-hooks-available-in-the-various-build-steps)了解详情。
*   通过重写其中一个步骤。例如，即使使用了CMake基础架构，如果包中的`.mk`文件定义了自己的`LIBFOO_CONFIGURE_CMDS`变量，它将被使用，而不是默认的CMake变量。但是，应该限制在非常特殊的情况下使用这种方法。一般情况下不要使用它。


## 18.9. Infrastructure for Python packages
----------------------------------------

此基础架构适用于使用标准Python setuptools或pep517机制作为构建系统的Python包，通常可以通过使用`setup.py`脚本或`pyproject.toml`文件来识别。

### 18.9.1. `python-package` tutorial

首先，让我们看看如何为Python包编写`.mk`文件，举个例子:

```bash
01: ################################################################################
02: #
03: # python-foo
04: #
05: ################################################################################
06:
07: PYTHON_FOO_VERSION = 1.0
08: PYTHON_FOO_SOURCE = python-foo-$(PYTHON_FOO_VERSION).tar.xz
09: PYTHON_FOO_SITE = http://www.foosoftware.org/download
10: PYTHON_FOO_LICENSE = BSD-3-Clause
11: PYTHON_FOO_LICENSE_FILES = LICENSE
12: PYTHON_FOO_ENV = SOME_VAR=1
13: PYTHON_FOO_DEPENDENCIES = libmad
14: PYTHON_FOO_SETUP_TYPE = distutils
15:
16: $(eval $(python-package))
```

第7行声明了包的版本号。

在第8行和第9行，我们声明了压缩包的名称(建议使用xz-ed压缩包)以及压缩包在Web上的位置。Buildroot会自动从这个位置下载压缩包。

第10行和第11行给出了这个包的许可细节(第10行给出了它的许可，第11行给出了包含许可文本的文件)。

在第12行，我们告诉Buildroot在配置包时将自定义选项传递给Python的`setup.py`脚本。

在第13行，我们声明了依赖，以便在包的构建过程开始之前构建它们。

第14行声明了要使用的Python构建系统。在这种情况下，使用`distutils` Python构建系统。支持的四个工具是`distutils`, `flit`, `pep517` 和 `setuptools`。

最后，在第16行，我们调用`python-package`宏来生成所有实际允许构建包的Makefile规则。

### 18.9.2. `python-package` reference

作为一个策略，只提供Python模块的包在Buildroot中都应该命名为`python-<something>`。其他使用Python构建系统但不是Python模块的包可以自由选择它们的名称(Buildroot中现有的示例是`scons`和`supervisor`)。

Python包基础架构的主要宏是`python-package`。它类似于`generic-package`宏。也可以使用`host-python-package`宏创建Python主机包。

就像通用基础架构一样，Python基础架构的工作原理是在调用`python-package`或`host-python-package`宏之前定义许多变量。

[通用包基础架构](#1862-generic-package-reference)中存在的所有包元数据信息变量也存在于Python基础架构中: `PYTHON_FOO_VERSION`, `PYTHON_FOO_SOURCE`, `PYTHON_FOO_PATCH`, `PYTHON_FOO_SITE`, `PYTHON_FOO_SUBDIR`, `PYTHON_FOO_DEPENDENCIES`, `PYTHON_FOO_LICENSE`, `PYTHON_FOO_LICENSE_FILES`, `PYTHON_FOO_INSTALL_STAGING`, 等.

注意:

*   没有必要在包的`PYTHON_FOO_DEPENDENCIES`变量中添加`python`或`host-python`，因为这些基本依赖项会根据python包基础架构的需要自动添加。
*   类似地，对于基于setuptools的包，不需要将`host-setuptools`添加到`PYTHON_FOO_DEPENDENCIES`，因为它会由Python基础架构根据需要自动添加。

有一个特定于Python基础架构的变量是必须的:

*   `PYTHON_FOO_SETUP_TYPE`, 来定义包使用的Python构建系统。支持的四个值是`distutils`, `flit`, `pep517` 和 `setuptools`。如果你不知道你的包中使用了哪个，请查看包源代码中的`setup.py`或`pyproject.toml`文件，并查看它是否从`distutils`、`flit`模块或`setuptools`模块中导入内容。如果包正在使用`pyproject.toml`。如果使用本地树内后端路径，则应该使用`pep517`。

根据包的需求，可以选择性地定义一些特定于Python基础架构的额外变量。它们中的许多只在非常特定的情况下有用，因此典型的包将只使用其中的一些，或完全不使用。

*   `PYTHON_FOO_SUBDIR`可能包含包中子目录的名称，该包包含主文件`setup.py`或`pyproject.toml`。例如，如果主文件`setup.py`或`pyproject.toml`不在压缩包解压后的树的根目录中，这就很有用。如果未指定`HOST_PYTHON_FOO_SUBDIR`，则默认为`PYTHON_FOO_SUBDIR`。
*   `PYTHON_FOO_ENV`, 指定额外的环境变量，将其传递给Python的`setup.py`脚本(用于distutils/setuptools包)或`support/scripts/pyinstaller.py`脚本(用于flit/pep517包)，用于构建和安装步骤。请注意，基础架构会自动传递几个标准变量，定义在`PKG_PYTHON_DISTUTILS_ENV`(用于distutils目标包)、`PKG_PYTHON_SETUPTOOLS_ENV`(用于setuptools目标包)、`HOST_PKG_PYTHON_SETUPTOOLS_ENV`(用于setuptools目标包)、`PKG_PYTHON_PEP517_ENV`(用于flit/pep517目标包)和`HOST_PKG_PYTHON_PEP517_ENV`(用于flit/pep517主机包)中。
*   `PYTHON_FOO_BUILD_OPTS`, 要指定在构建步骤中传递给Python `setup.py`脚本的其他选项，通常只对基于distutils/setuptools的包有意义，因为基于flit/pep517的包不会将这些选项传递给`setup.py`脚本，而是将它们传递给`support/scripts/pyinstaller.py`。对于目标distutils包，基础架构已经自动传递了`PKG_PYTHON_DISTUTILS_BUILD_OPTS`选项。
*   `PYTHON_FOO_INSTALL_TARGET_OPTS`, `PYTHON_FOO_INSTALL_STAGING_OPTS`, `HOST_PYTHON_FOO_INSTALL_OPTS` 要指定在目标安装步骤、临时安装步骤或主机安装期间传递给Python `setup.py`脚本(用于distutils/setuptools包)或`support/scripts/pyinstaller.py`(用于flit/pep517包)的其他选项。请注意，基础架构自动传递一些选项，定义在`PKG_PYTHON_DISTUTILS_INSTALL_TARGET_OPTS`或`PKG_PYTHON_DISTUTILS_INSTALL_STAGING_OPTS`(针对目标distutils包)，`HOST_PKG_PYTHON_DISTUTILS_INSTALL_OPTS`(针对主机distutils包)，`PKG_PYTHON_SETUPTOOLS_INSTALL_TARGET_OPTS`或`PKG_PYTHON_SETUPTOOLS_INSTALL_STAGING_OPTS`(针对目标setuptools包)，`HOST_PKG_PYTHON_SETUPTOOLS_INSTALL_OPTS`(用于主机setuptools包)和`PKG_PYTHON_PEP517_INSTALL_TARGET_OPTS`或`PKG_PYTHON_PEP517_INSTALL_STAGING_OPTS`(用于目标flit/pep517包)。

有了Python基础架构，构建和安装包所需的所有步骤都已经定义好了，它们通常适用于大多数基于Python的包。但是，在需要时，仍然可以自定义任何特定步骤的操作:

*   通过添加一个操作后钩子(在提取、打补丁、配置、构建或安装之后)。请参阅[第18.23节，“在各种构建步骤中可用的钩子”](#1823-hooks-available-in-the-various-build-steps)了解详情。
*   通过重写其中一个步骤。例如，即使使用了Python基础架构，如果包`.mk`文件定义了自己的`PYTHON_FOO_BUILD_CMDS`变量，它将被使用，而不是默认的Python变量。但是，应该限制在非常特殊的情况下使用这种方法。一般情况下不要使用它。

### 18.9.3. Generating a `python-package` from a PyPI repository

如果你想创建一个Buildroot包的Python包在PyPI上可用，你可能想使用位于`utils/`中的`scanpypi`工具来自动化这个过程。

您可以在[这里](https://pypi.python.org)找到现有PyPI包的列表。

`scanpypi`需要在主机上安装Python的`setuptools`包。

当在你的buildroot目录的根目录下执行以下命令:

```bash
utils/scanpypi foo bar -o package
```

这将在包文件夹中生成包`python-foo`和`python-bar`，如果它们存在于[https://pypi.python.org](https://pypi.python.org)上。

找到`external python modules`菜单并将你的包插入其中。记住，菜单中的项目应该按照字母顺序排列。

请记住，您很可能必须手动检查软件包是否有任何错误，因为有些东西是生成器猜不到的(例如，依赖于任何python核心模块，如BR2_PACKAGE_PYTHON_ZLIB)。另外，请注意，许可证和许可证文件是猜测的，必须进行检查。你还需要手动将包添加到`package/Config.in`文件中。

如果你的Buildroot包不在官方的Buildroot树中，而是在br2-external树中，请像下面这样使用-o参数:

```bash
utils/scanpypi foo bar -o other_package_dir
```

这将在`other_package_directory`中生成`python-foo` 和 `python-bar`包，而不是`package`包。

选项`-h`将列出可用的选项:

```bash
utils/scanpypi -h
```

### 18.9.4. `python-package` CFFI backend

CFFI (C Foreign Function Interface for Python)提供了一种方便可靠的方法，可以使用用C语言编写的接口声明从Python中调用编译后的C代码。Python包依赖于这个后端，可以通过其`setup.py`文件的`install_requires`字段中的`cffi`依赖项来识别。

这样一套方案应:

*   添加`python-cffi`作为运行时依赖，以便在目标上安装编译后的C库包装器。这可以通过将`select BR2_PACKAGE_PYTHON_CFFI`添加到`Config.in`包中来实现。

```bash
config BR2_PACKAGE_PYTHON_FOO
        bool "python-foo"
        select BR2_PACKAGE_PYTHON_CFFI # runtime
```

*   添加`host-python-cffi`作为构建时依赖，以便交叉编译C包装器。这是通过将`host-python-cffi`添加到`PYTHON_FOO_DEPENDENCIES`变量来实现的。

```bash
################################################################################
#
# python-foo
#
################################################################################

...

PYTHON_FOO_DEPENDENCIES = host-python-cffi

$(eval $(python-package))
```


## 18.10. Infrastructure for LuaRocks-based packages
-------------------------------------------------

### 18.10.1. `luarocks-package` tutorial

首先，让我们看看如何为基于LuaRocks的包编写`.mk`文件，以下是一个例子:

```bash
01: ################################################################################
02: #
03: # lua-foo
04: #
05: ################################################################################
06:
07: LUA_FOO_VERSION = 1.0.2-1
08: LUA_FOO_NAME_UPSTREAM = foo
09: LUA_FOO_DEPENDENCIES = bar
10:
11: LUA_FOO_BUILD_OPTS += BAR_INCDIR=$(STAGING_DIR)/usr/include
12: LUA_FOO_BUILD_OPTS += BAR_LIBDIR=$(STAGING_DIR)/usr/lib
13: LUA_FOO_LICENSE = luaFoo license
14: LUA_FOO_LICENSE_FILES = $(LUA_FOO_SUBDIR)/COPYING
15:
16: $(eval $(luarocks-package))
```

在第7行，我们声明了包的版本(与rockspec中相同，由上游版本和rockspec修订版本连接而成，用连字符 - 分隔)。

在第8行，我们在luarock中声明包名为"foo"。在Buildroot中，我们给lua相关的包起一个以“lua”开头的名字，因此Buildroot的名称与上游名称不同。`LUA_FOO_NAME_UPSTREAM`建立了两个名称之间的联系。

在第9行，我们声明了本机库的依赖项，以便在我们的包的构建过程开始之前构建它们。

在第11-12行，我们告诉Buildroot在构建包时将自定义选项传递给LuaRocks。

在第13-14行，我们指定了包的许可条款。

最后，在第16行，我们调用`luarocks-package`宏，该宏生成所有实际允许构建包的Makefile规则。

大多数细节都可以从`rock`和`rockspec`中获取。这个文件和Config。可以通过在buildroot目录下运行命令`luarocks buildroot foo lua-foo`来生成。这个命令会运行一个特定的`luarocks`的Buildroot插件，它会自动生成一个Buildroot包。结果仍然必须手动检查，并可能进行修改。

*   `package/Config.in`文件必须手动更新以包含生成的Config.in文件。

### 18.10.2. `luarocks-package` reference

LuaRocks是一个Lua模块的部署和管理系统，支持各种`build.type`: `builtin`, `make` 和 `cmake`。在Buildroot的上下文中，`luarocks-package`基础架构只支持`builtin`模式。使用`make`或`cmake`构建机制的luarock包应该分别使用Buildroot中的`generic-package` 和 `cmake-package`基础架构进行打包。

LuaRocks包基础架构的主要宏是`luarocks-package`:像`generic-package`一样，它通过定义一些提供包元数据信息的变量来工作，然后调用`luarocks-package`。

就像通用基础架构一样，LuaRocks基础架构通过在调用`luarocks-package`宏之前定义许多变量来工作。

首先，存在于通用基础架构中的所有包元数据信息变量也存在于LuaRocks基础架构中: `LUA_FOO_VERSION`, `LUA_FOO_SOURCE`, `LUA_FOO_SITE`, `LUA_FOO_DEPENDENCIES`, `LUA_FOO_LICENSE`, `LUA_FOO_LICENSE_FILES`.

其中两个由LuaRocks基础架构填充(用于“下载”步骤)。如果你的包没有托管在LuaRocks镜像`$(BR2_LUAROCKS_MIRROR)`上，你可以覆盖它们:

*   `LUA_FOO_SITE`, 默认为 `$(BR2_LUAROCKS_MIRROR)`
*   `LUA_FOO_SOURCE`, 默认为 `$(lowercase LUA_FOO_NAME_UPSTREAM)-$(LUA_FOO_VERSION).src.rock`

A few additional variables, specific to the LuaRocks infrastructure, are also defined. They can be overridden in specific cases.

*   `LUA_FOO_NAME_UPSTREAM`, 默认为 `lua-foo`, 即Buildroot包的名称
*   `LUA_FOO_ROCKSPEC`, 默认为 `$(lowercase LUA_FOO_NAME_UPSTREAM)-$(LUA_FOO_VERSION).rockspec`
*   `LUA_FOO_SUBDIR`, 默认为 `$(LUA_FOO_NAME_UPSTREAM)-$(LUA_FOO_VERSION_WITHOUT_ROCKSPEC_REVISION)`
*   `LUA_FOO_BUILD_OPTS` 包含用于`luarocks build`调用的其他构建选项。


## 18.11. Infrastructure for Perl/CPAN packages
--------------------------------------------

### 18.11.1. `perl-package` tutorial

首先，让我们看看如何为Perl/CPAN包编写`.mk`文件，举个例子:

```bash
01: ################################################################################
02: #
03: # perl-foo-bar
04: #
05: ################################################################################
06:
07: PERL_FOO_BAR_VERSION = 0.02
08: PERL_FOO_BAR_SOURCE = Foo-Bar-$(PERL_FOO_BAR_VERSION).tar.gz
09: PERL_FOO_BAR_SITE = $(BR2_CPAN_MIRROR)/authors/id/M/MO/MONGER
10: PERL_FOO_BAR_DEPENDENCIES = perl-strictures
11: PERL_FOO_BAR_LICENSE = Artistic or GPL-1.0+
12: PERL_FOO_BAR_LICENSE_FILES = LICENSE
13: PERL_FOO_BAR_DISTNAME = Foo-Bar
14:
15: $(eval $(perl-package))
```

第7行声明了包的版本号。

在第8行和第9行，我们声明了压缩包的名称以及压缩包在CPAN服务器上的位置。Buildroot会自动从这个位置下载压缩包。

在第10行，我们声明了依赖项，以便在包的构建过程开始之前构建它们。

第11行和第12行给出了这个包的许可细节(第11行给出了它的许可，第12行给出了包含许可文本的文件)。

第13行是脚本`utils/scancpan`所需的发行版名称(以便重新生成/升级这些包文件)。

最后，在第15行，我们调用`perl-package`宏，该宏生成所有实际允许构建包的Makefile规则。

大多数这些数据可以从[https://metacpan.org/](https://metacpan.org/)检索。因此，这个文件和配置文件可以通过在Buildroot目录(或br2-external树)中运行`utils/scancpan Foo-Bar`脚本来生成。此脚本为所请求的包创建Config.in文件和foo-bar.mk文件，并为CPAN指定的所有依赖项创建递归。你仍然应该手动编辑结果。特别应检查以下事项。

*   如果perl模块链接了由另一个(非perl)包提供的共享库，则不会自动添加此依赖项。它必须手动添加到`PERL_FOO_BAR_DEPENDENCIES`中。
*   必须手动更新`package/Config.in`文件以包含生成的配置。在文件中。作为提示，`scancpan`脚本打印出所需的`source "…"`语句，并按字母顺序排序。

### 18.11.2. `perl-package` reference

作为一个策略，提供Perl/CPAN模块的包在Buildroot中都应该被命名为`perl-<something>`。

这个基础架构处理各种Perl构建系统:`ExtUtils-MakeMaker`(EUMM)、`Module-Build` (MB)和`Module-Build-Tiny`。当包提供了`Makefile.PL`和`Build.PL`时，默认优先使用`Build.PL`。

Perl/CPAN包基础架构的主要宏是`perl-package`。它类似于`generic-package`宏。也可以使用`host-perl-package`宏来获得目标包和主机包。

就像一般的基础结构一样，Perl/CPAN基础结构通过在调用`perl-package`宏之前定义许多变量来工作。

首先，存在于通用基础架构中的所有包元数据信息变量也存在于Perl/CPAN基础架构中: `PERL_FOO_VERSION`, `PERL_FOO_SOURCE`, `PERL_FOO_PATCH`, `PERL_FOO_SITE`, `PERL_FOO_SUBDIR`, `PERL_FOO_DEPENDENCIES`, `PERL_FOO_INSTALL_TARGET`.

请注意，除非定义了`PERL_FOO_INSTALL_STAGING_CMDS`变量，否则将`PERL_FOO_INSTALL_STAGING`设置为`YES`是无效的。perl基础架构没有定义这些命令，因为perl模块通常不需要安装到`staging`目录。

还可以定义一些特定于Perl/CPAN基础结构的额外变量。它们中的许多只在非常特定的情况下有用，因此典型的包只会使用其中的一些。

*   `PERL_FOO_PREFER_INSTALLER`/`HOST_PERL_FOO_PREFER_INSTALLER`, 指定首选的安装方法。可能的值是`EUMM`(用于使用`ExtUtils-MakeMaker`进行基于`Makefile.PL`的安装)和`MB`(用于使用`Module-Build`进行基于`Build.PL`的安装)。仅当包提供了两种安装方法时，才使用此变量。
*   `PERL_FOO_CONF_ENV`/`HOST_PERL_FOO_CONF_ENV`, 指定传递给`perl Makefile.PL`或`perl Build.PL`的附加环境变量。默认为空。
*   `PERL_FOO_CONF_OPTS`/`HOST_PERL_FOO_CONF_OPTS`, 指定传递给`perl Makefile.PL`或`perl Build.PL`的额外配置选项。默认为空。
*   `PERL_FOO_BUILD_OPTS`/`HOST_PERL_FOO_BUILD_OPTS`, 在构建步骤中指定传递给`make pure_all`或`perl Build build`的附加选项。默认为空。
*   `PERL_FOO_INSTALL_TARGET_OPTS`, 在安装步骤中指定传递给`make pure_install`或`perl Build install`的附加选项。默认为空。
*   `HOST_PERL_FOO_INSTALL_OPTS`, 在安装步骤中指定传递给`make pure_install`或`perl Build install`的附加选项。默认为空。


## 18.12. Infrastructure for virtual packages
------------------------------------------

在Buildroot中，虚拟包是由一个或多个包(称为提供者)提供功能的包。虚拟包管理是一种可扩展的机制，允许用户选择在rootfs中使用的提供者。

例如，OpenGL ES是嵌入式系统上用于2D和3D图形的API。对于Allwinner Tech Sunxi和Texas Instruments的OMAP35xx平台，此API的实现是不同的。所以`libgles`是一个虚拟包，`sunxi-mali-utgard`和`ti-gfx`是它的提供者。

### 18.12.1. `virtual-package` tutorial

在下面的例子中，我们将解释如何添加一个新的虚拟包(something-virtual)和它的提供者(some-provider)。

首先，让我们创建虚拟包。

### 18.12.2. Virtual package’s `Config.in` file

虚拟包something-virtual的`Config.in`文件应该包含:

```bash
01: config BR2_PACKAGE_HAS_SOMETHING_VIRTUAL
02:     bool
03:
04: config BR2_PACKAGE_PROVIDES_SOMETHING_VIRTUAL
05:     depends on BR2_PACKAGE_HAS_SOMETHING_VIRTUAL
06:     string
```

在这个文件中，我们声明了两个选项，`BR2_PACKAGE_HAS_SOMETHING_VIRTUAL` 和 `BR2_PACKAGE_PROVIDES_SOMETHING_VIRTUAL`，它们的值将被providers使用。

### 18.12.3. Virtual package’s `.mk` file

虚拟包的`.mk`应该只计算`virtual-package`宏:

```bash
01: ################################################################################
02: #
03: # something-virtual
04: #
05: ################################################################################
06:
07: $(eval $(virtual-package))
```

也可以使用`host-virtual-package`宏来获得目标包和主机包。

### 18.12.4. Provider’s `Config.in` file

当添加一个包作为provider时，只有`Config.in`文件需要一些修改。

some-provider包的`Config.in`文件提供了something-virtual的功能，它应该包含:

```bash
01: config BR2_PACKAGE_SOME_PROVIDER
02:     bool "some-provider"
03:     select BR2_PACKAGE_HAS_SOMETHING_VIRTUAL
04:     help
05:       This is a comment that explains what some-provider is.
06:
07:       http://foosoftware.org/some-provider/
08:
09: if BR2_PACKAGE_SOME_PROVIDER
10: config BR2_PACKAGE_PROVIDES_SOMETHING_VIRTUAL
11:     default "some-provider"
12: endif
```

在第3行，我们选择了`BR2_PACKAGE_HAS_SOMETHING_VIRTUAL`，在第11行，我们将`BR2_PACKAGE_PROVIDES_SOMETHING_VIRTUAL`的值设置为提供者的名称，但仅当它被选中时才设置。

### 18.12.5. Provider’s `.mk` file

`.mk`文件还应该声明一个额外的变量`SOME_PROVIDER_PROVIDES`来包含它所实现的所有虚拟包的名称:

```bash
01: SOME_PROVIDER_PROVIDES = something-virtual
```

当然，不要忘记为这个包添加适当的构建和运行时依赖项!

### 18.12.6. Notes on depending on a virtual package

当添加一个需要由虚拟包提供的特定功能的包时，你必须使用`depends on BR2_PACKAGE_HAS_FEATURE`，如下所示:

```bash
config BR2_PACKAGE_HAS_FEATURE
    bool

config BR2_PACKAGE_FOO
    bool "foo"
    depends on BR2_PACKAGE_HAS_FEATURE
```

### 18.12.7. Notes on depending on a specific provider

如果你的包确实需要一个特定的provider，那么你必须让你的包`depends on`这个provider;你不能`select`一个provider。

让我们以一个有两个providers的`FEATURE`为例:

```bash
config BR2_PACKAGE_HAS_FEATURE
    bool

config BR2_PACKAGE_FOO
    bool "foo"
    select BR2_PACKAGE_HAS_FEATURE

config BR2_PACKAGE_BAR
    bool "bar"
    select BR2_PACKAGE_HAS_FEATURE
```

你正在添加一个包，它需要`foo`提供的`FEATURE`，但不需要`bar`提供的`FEATURE`。

如果你使用了`select BR2_PACKAGE_FOO`，那么用户仍然可以在菜单配置中选择`BR2_PACKAGE_BAR`。这将导致配置不一致，即两个具有相同`FEATURE`的提供者将同时启用，一个由用户显式设置，另一个由您的`select`隐式设置。

相反，你必须使用`depends on BR2_PACKAGE_FOO`，这可以避免任何隐式配置的不一致。


## 18.13. Infrastructure for packages using kconfig for configuration files
------------------------------------------------------------------------

软件包处理用户指定配置的一种流行方式是`kconfig`。其中，它由Linux内核、Busybox和Buildroot本身使用。出现.config文件和`menuconfig`目标是使用kconfig时的两个众所周知的症状。

Buildroot为使用kconfig进行配置的包提供了一个基础架构。这个基础架构提供了必要的逻辑，以便在Buildroot中将包的`menuconfig`目标暴露为`foo-menuconfig`，并以正确的方式处理配置文件的复制。

`kconfig-package`基础架构基于`generic-package`基础架构。`generic-package`支持的所有变量也可以在`kconfig-package`中使用。请参阅[第18.6.2节，“`generic-package`引用”](#1862-generic-package-reference)了解更多细节。

为了在Buildroot包中使用`kconfig-package`基础架构，除了`generic-package`基础架构所需的变量之外，`.mk`文件中最少需要的行包括:

```bash
FOO_KCONFIG_FILE = reference-to-source-configuration-file

$(eval $(kconfig-package))
```

这段代码创建了以下make目标:

*   `foo-menuconfig`, 哪个会调用包的`menuconfig`目标
*   `foo-update-config`, 它将配置复制回源配置文件。当设置片段文件时，不能使用此目标。
*   `foo-update-defconfig`, 它将配置复制回源配置文件。配置文件将只列出与默认值不同的选项。当设置片段文件时，不能使用此目标。
*   `foo-diff-config`, 它输出当前配置与这个kconfig包的Buildroot配置中定义的配置之间的差异。例如，输出对于识别可能必须传播到配置片段的配置更改非常有用。

并确保在正确的时刻将源配置文件复制到构建目录。

有两个选项可以指定要使用的配置文件，`FOO_KCONFIG_FILE`(如上例所示)或`FOO_KCONFIG_DEFCONFIG`。必须提供其中一个，但不能同时提供两个:

*   `FOO_KCONFIG_FILE` 指定用于配置包的defconfig或full-config文件的路径。
*   `FOO_KCONFIG_DEFCONFIG` 指定要调用的defconfig生成规则来配置包。

除了这些最低要求的行之外，还可以设置几个可选变量，以适应所考虑的包的需求:

*   `FOO_KCONFIG_EDITORS`: 要支持的kconfig编辑器列表，以空格分隔，例如menuconfig xconfig。默认情况下，menuconfig。
*   `FOO_KCONFIG_FRAGMENT_FILES`: 由空格分隔的配置片段文件列表，这些文件合并到主配置文件中。片段文件通常用于希望与上游(def)config文件保持同步的情况，并进行一些小的修改。
*   `FOO_KCONFIG_OPTS`: 调用kconfig编辑器时要传递的额外选项。例如，这可能需要包括$(FOO_MAKE_OPTS)。默认为空。
*   `FOO_KCONFIG_FIXUP_CMDS`: 在复制配置文件或运行kconfig编辑器后修复配置文件所需的shell命令列表。例如，可能需要这样的命令来确保配置与Buildroot的其他配置一致。默认为空。
*   `FOO_KCONFIG_DOTCONFIG`: 相对于包源树，`.config`文件的路径(带文件名)。默认值`.config`应该非常适合所有使用继承自Linux内核的标准kconfig基础架构的包;有些包使用kconfig的派生，使用不同的位置。
*   `FOO_KCONFIG_DEPENDENCIES`: 在解释此包的kconfig之前需要构建的包列表(很可能是主机包)。很少使用。默认为空。
*   `FOO_KCONFIG_SUPPORTS_DEFCONFIG`: 包的kconfig系统是否支持使用defconfig文件;很少有包不是这样。默认情况下: YES。


## 18.14. Infrastructure for rebar-based packages
----------------------------------------------

### 18.14.1. `rebar-package` tutorial

首先，让我们看看如何为基于rebar的包编写`.mk`文件，并提供一个示例:

```bash
01: ################################################################################
02: #
03: # erlang-foobar
04: #
05: ################################################################################
06:
07: ERLANG_FOOBAR_VERSION = 1.0
08: ERLANG_FOOBAR_SOURCE = erlang-foobar-$(ERLANG_FOOBAR_VERSION).tar.xz
09: ERLANG_FOOBAR_SITE = http://www.foosoftware.org/download
10: ERLANG_FOOBAR_DEPENDENCIES = host-libaaa libbbb
11:
12: $(eval $(rebar-package))
```

第7行声明了包的版本号。

在第8行和第9行，我们声明了压缩包的名称(建议使用xz-ed压缩包)以及压缩包在Web上的位置。Buildroot会自动从这个位置下载压缩包。

在第10行，我们声明了依赖项，以便在包的构建过程开始之前构建它们。

最后，在第12行，我们调用`rebar-package`宏，它生成所有实际允许构建包的Makefile规则。

### 18.14.2. `rebar-package` reference

`rebar`包基础架构的主要宏是`rebar-package`。它类似于`generic-package`宏。也可以使用`host-rebar-package`宏来获得主机包。

就像通用基础结构一样，`rebar`基础结构通过在调用`rebar-package`宏之前定义许多变量来工作。

首先，存在于通用基础架构中的所有包元数据信息变量也存在于`rebar`基础架构中: `ERLANG_FOOBAR_VERSION`, `ERLANG_FOOBAR_SOURCE`, `ERLANG_FOOBAR_PATCH`, `ERLANG_FOOBAR_SITE`, `ERLANG_FOOBAR_SUBDIR`, `ERLANG_FOOBAR_DEPENDENCIES`, `ERLANG_FOOBAR_INSTALL_STAGING`, `ERLANG_FOOBAR_INSTALL_TARGET`, `ERLANG_FOOBAR_LICENSE` 和 `ERLANG_FOOBAR_LICENSE_FILES`.

还可以定义一些特定于`rebar`基础架构的额外变量。它们中的许多只在非常特定的情况下有用，因此典型的包只会使用其中的一些。

*   `ERLANG_FOOBAR_USE_AUTOCONF`, 指定包在配置步骤中使用autoconf。当一个包将此变量设置为`YES`时，将使用`autotools`基础架构。
    
    **注意.** 你也可以使用`autotools`基础架构中的一些变量: `ERLANG_FOOBAR_CONF_ENV`, `ERLANG_FOOBAR_CONF_OPTS`, `ERLANG_FOOBAR_AUTORECONF`, `ERLANG_FOOBAR_AUTORECONF_ENV` 和 `ERLANG_FOOBAR_AUTORECONF_OPTS`.
    
*   `ERLANG_FOOBAR_USE_BUNDLED_REBAR`, 指定包有一个捆绑版本的rebar **和**它是应使用。有效值是`YES`或`NO`(默认值)。
    
    **注意.** 如果该包打包了一个rebar实用程序，但可以使用Buildroot提供的通用工具，只需说`NO`(即不指定此变量)。仅当必须使用此包中捆绑的rebar实用程序时设置。
    
*   `ERLANG_FOOBAR_REBAR_ENV`, 指定传递给rebar实用程序的附加环境变量。
*   `ERLANG_FOOBAR_KEEP_DEPENDENCIES`, 以保持rebar.config文件中描述的依赖项。有效值是`YES`或`NO`(默认值)。除非此变量设置为`YES`，否则rebar基础架构将在补丁后钩子中删除此类依赖项，以确保rebar不会下载或编译它们。

使用rebar基础架构，已经定义了构建和安装包所需的所有步骤，它们通常适用于大多数基于rebar的包。但是，在需要时，仍然可以自定义任何特定步骤的操作:

*   通过添加一个操作后钩子(在提取、打补丁、配置、构建或安装之后)。参见 [18.23节，“不同构建步骤中可用的钩子”](#1823-hooks-available-in-the-various-build-steps) 获取更多信息。
*   通过重写其中一个步骤。例如，即使使用了钢筋基础架构，如果包`.mk`文件定义了自己的`ERLANG_FOOBAR_BUILD_CMDS`变量，它将被使用，而不是默认的rebar变量。但是，应该限制在非常特殊的情况下使用这种方法。一般情况下不要使用它。


## 18.15. Infrastructure for Waf-based packages
--------------------------------------------

### 18.15.1. `waf-package` tutorial

首先，让我们看看如何为基于Waf的包编写`.mk`文件，并给出一个示例:

```bash
01: ################################################################################
02: #
03: # libfoo
04: #
05: ################################################################################
06:
07: LIBFOO_VERSION = 1.0
08: LIBFOO_SOURCE = libfoo-$(LIBFOO_VERSION).tar.gz
09: LIBFOO_SITE = http://www.foosoftware.org/download
10: LIBFOO_CONF_OPTS = --enable-bar --disable-baz
11: LIBFOO_DEPENDENCIES = bar
12:
13: $(eval $(waf-package))
```

第7行声明了包的版本号。

在第8行和第9行，我们声明了压缩包的名称(建议使用xz-ed压缩包)以及压缩包在Web上的位置。Buildroot会自动从这个位置下载压缩包。

第10行告诉Buildroot要为libfoo启用哪些选项。

在第11行，我们告诉Buildroot libfoo的依赖。

最后，在第13行，我们调用`waf-package`宏，该宏生成所有实际允许构建包的Makefile规则。

### 18.15.2. `waf-package` reference

Waf包基础架构的主要宏是`waf-package`。它类似于`generic-package`宏。

就像通用基础架构一样，Waf基础架构通过在调用`waf-package`宏之前定义许多变量来工作。

首先，存在于通用基础结构中的所有包元数据信息变量也存在于Waf基础结构中: `LIBFOO_VERSION`, `LIBFOO_SOURCE`, `LIBFOO_PATCH`, `LIBFOO_SITE`, `LIBFOO_SUBDIR`, `LIBFOO_DEPENDENCIES`, `LIBFOO_INSTALL_STAGING`, `LIBFOO_INSTALL_TARGET`.

还可以定义一个特定于Waf基础结构的附加变量。

*   `LIBFOO_SUBDIR`可能包含主wscript文件所在包中的子目录名称。例如，如果主wscript文件不在由压缩包提取的树的根中，那么这是有用的。如果未指定`HOST_LIBFOO_SUBDIR`，则默认为`LIBFOO_SUBDIR`。
*   `LIBFOO_NEEDS_EXTERNAL_WAF`可以设置为`YES`或`NO`来告诉Buildroot使用打包的`waf`可执行文件。如果设置为`NO`(默认值)，那么Buildroot将使用包源树中提供的waf可执行文件;如果设置为`YES`，那么Buildroot将下载、安装waf作为主机工具并使用它来构建包。
*   `LIBFOO_WAF_OPTS`，用于指定在包构建过程的每个步骤(配置、构建和安装)传递给`waf`脚本的额外选项。默认为空。
*   `LIBFOO_CONF_OPTS`, 为配置步骤指定传递给`waf`脚本的其他选项。默认为空。
*   `LIBFOO_BUILD_OPTS`, 指定在构建阶段传递给`waf`脚本的其他选项。默认为空。
*   `LIBFOO_INSTALL_STAGING_OPTS`, 指定在临时安装阶段传递给`waf`脚本的其他选项。默认为空。
*   `LIBFOO_INSTALL_TARGET_OPTS`, 指定在目标安装步骤中传递给`waf`脚本的其他选项。默认为空。


## 18.16. Infrastructure for Meson-based packages
----------------------------------------------

### 18.16.1. `meson-package` tutorial

[Meson](http://mesonbuild.com)是一个开源构建系统，其速度非常快，更重要的是，对用户尽可能友好。它使用[Ninja](https://ninja-build.org)作为辅助工具来执行实际的构建操作。

让我们看看如何为基于介子的包编写`.mk`文件，举个例子:

```bash
01: ################################################################################
02: #
03: # foo
04: #
05: ################################################################################
06:
07: FOO_VERSION = 1.0
08: FOO_SOURCE = foo-$(FOO_VERSION).tar.gz
09: FOO_SITE = http://www.foosoftware.org/download
10: FOO_LICENSE = GPL-3.0+
11: FOO_LICENSE_FILES = COPYING
12: FOO_INSTALL_STAGING = YES
13:
14: FOO_DEPENDENCIES = host-pkgconf bar
15:
16: ifeq ($(BR2_PACKAGE_BAZ),y)
17: FOO_CONF_OPTS += -Dbaz=true
18: FOO_DEPENDENCIES += baz
19: else
20: FOO_CONF_OPTS += -Dbaz=false
21: endif
22:
23: $(eval $(meson-package))
```

Makefile从包声明的标准变量的定义开始(第7行到第11行)。

在第23行，我们调用`meson-package`宏，该宏生成所有实际允许构建包的Makefile规则。

在这个例子中，`host-pkgconf`和`bar`在第14行`FOO_DEPENDENCIES`中被声明为依赖项，因为`foo`的Meson构建文件使用`pkg-config`来确定`bar`包的编译标志和库。

请注意，没有必要在包的`FOO_DEPENDENCIES`变量中添加`host-meson`，因为这个基本依赖项会根据Meson包基础架构的需要自动添加。

如果选中了“baz”包，那么通过在第17行向`FOO_CONF_OPTS`添加`-Dbaz=true`来激活“foo”中对“baz”特性的支持，如“foo”源代码树中的`meson_options.txt`文件中指定的那样。“baz”包也被添加到`FOO_DEPENDENCIES`中。请注意，如果没有选择包，对`baz`的支持在第20行显式禁用。

总而言之，要添加一个新的基于介子的包，可以逐字复制Makefile示例，然后编辑它，将所有出现的`FOO`替换为新包的大写名称，并更新标准变量的值。

### 18.16.2. `meson-package` reference

介子包基础结构的主要宏是`meson-package`。它类似于`generic-package`宏。通过`host-meson-package`宏，也可以获得目标包和主机包。

就像通用基础结构一样，Meson基础结构通过在调用`meson-package`宏之前定义许多变量来工作。

首先，存在于通用基础结构中的所有包元数据信息变量也存在于介子基础结构中: `FOO_VERSION`, `FOO_SOURCE`, `FOO_PATCH`, `FOO_SITE`, `FOO_SUBDIR`, `FOO_DEPENDENCIES`, `FOO_INSTALL_STAGING`, `FOO_INSTALL_TARGET`.

还可以定义一些特定于Meson基础结构的额外变量。它们中的许多只在非常特定的情况下有用，因此典型的包只会使用其中的一些。

*   `FOO_SUBDIR`可以包含包含主介子的包中的子目录的名称。构建文件。这很有用，例如，主介子。构建文件不在被压缩包解压的树的根目录中。如果未指定`HOST_FOO_SUBDIR`，则默认为`FOO_SUBDIR`。
*   `FOO_CONF_ENV`，用于指定额外的环境变量，在配置步骤中传递给`meson`。默认为空。
*   `FOO_CONF_OPTS`，用于指定其他要传递给`meson`的配置步骤的选项。默认为空。
*   `FOO_CFLAGS`，用于指定编译器参数，添加到特定于包的`cross-compile.conf`文件的`c_args`属性中。默认情况下，`TARGET_CFLAGS`的值。
*   `FOO_CXXFLAGS`，用于指定添加到特定于包的`cross-compile.conf`文件的`cpp_args`属性中的编译器参数。默认情况下，`TARGET_CXXFLAGS`的值。
*   `FOO_LDFLAGS`，用于指定添加到特定于包的`cross-compile.conf`文件`c_link_args`和`cpp_link_args`属性中的编译器参数。默认情况下，`TARGET_LDFLAGS`的值。
*   `FOO_MESON_EXTRA_BINARIES`，指定一个以空格分隔的程序列表，要添加到meson `cross-compilation.conf`配置文件的`[binaries]`部分。格式为`program-name='/path/to/program'`， `=`符号周围没有空格，单引号中包含程序的路径。默认为空。请注意，Buildroot已经为`c`、`cpp`、`ar`、`strip`和`pkgconfig`设置了正确的值。
*   `FOO_MESON_EXTRA_PROPERTIES`，指定一个以空格分隔的属性列表，要添加到meson `cross-compilation.conf`配置文件的`[properties]`部分。格式是`property-name=<value>`; `=`符号周围没有空格，字符串值周围使用单引号。默认为空。请注意，Buildroot已经设置了`needs_exe_wrapper`、`c_args`、`c_link_args`、`cpp_args`、`cpp_link_args`、`sys_root`和`pkg_config_libdir`的值。
*   `FOO_NINJA_ENV`, 指定额外的环境变量传递给`ninja`，meson companion工具负责构建操作。默认为空。
*   `FOO_NINJA_OPTS`, 指定以空格分隔的要构建的目标列表。默认情况下，为空，以构建默认目标。


## 18.17. Infrastructure for Cargo-based packages
----------------------------------------------

Cargo是Rust编程语言的包管理器。它允许用户构建用Rust编写的程序或库，但它也下载和管理它们的依赖项，以确保可重复构建。货物包裹被称为“crates”。

### 18.17.1. `cargo-package` tutorial

基于Cargo的foo包的`Config.in`文件应该包含:

```bash
01: config BR2_PACKAGE_FOO
02:     bool "foo"
03:     depends on BR2_PACKAGE_HOST_RUSTC_TARGET_ARCH_SUPPORTS
04:     select BR2_PACKAGE_HOST_RUSTC
05:     help
06:       This is a comment that explains what foo is.
07:
08:       http://foosoftware.org/foo/
```

这个包的`.mk`文件应该包含:

```bash
01: ################################################################################
02: #
03: # foo
04: #
05: ################################################################################
06:
07: FOO_VERSION = 1.0
08: FOO_SOURCE = foo-$(FOO_VERSION).tar.gz
09: FOO_SITE = http://www.foosoftware.org/download
10: FOO_LICENSE = GPL-3.0+
11: FOO_LICENSE_FILES = COPYING
12:
13: $(eval $(cargo-package))
```

Makefile从包声明的标准变量的定义开始(第7行到第11行)。

如第13行所示，它基于`cargo-package`基础架构。Cargo将由此基础架构自动调用，以构建和安装包。

仍然可以定义自定义的构建命令或安装命令(即FOO_BUILD_CMDS和FOO_INSTALL_TARGET_CMDS)。这些将取代来自货运基础架构的命令。

### 18.17.2. `cargo-package` reference

Cargo包基础架构的主要宏是目标包的`cargo-package`和主包的`host-cargo-package`。

就像通用基础结构一样，Cargo基础结构通过在调用`cargo-package` 或 `host-cargo-package`宏之前定义一系列变量来工作。

首先，存在于通用基础架构中的所有包元数据信息变量也存在于Cargo基础架构中: `FOO_VERSION`, `FOO_SOURCE`, `FOO_PATCH`, `FOO_SITE`, `FOO_DEPENDENCIES`, `FOO_LICENSE`, `FOO_LICENSE_FILES`, 等.

还可以定义一些特定于Cargo基础架构的额外变量。它们中的许多只在非常特定的情况下有用，因此典型的包只会使用其中的一些。

*   `FOO_SUBDIR`可能包含包含Cargo.toml文件的包中的子目录的名称。例如，如果它不在由压缩包提取的树的根目录中，那么这是有用的。如果未指定`HOST_FOO_SUBDIR`，则默认为`FOO_SUBDIR`。
*   `FOO_CARGO_ENV`可用于在`cargo`调用环境中传递额外的变量。它在构建和安装时都使用。
*   `FOO_CARGO_BUILD_OPTS`可用于在构建时将其他选项传递给`cargo`。
*   `FOO_CARGO_INSTALL_OPTS`可以在安装时将额外的选项传递给`cargo`。

一个crate可以依赖于crates中的其他库。在它的`Cargo.toml`文件中列出。Buildroot会自动下载这些依赖项，作为使用`cargo-package`基础架构的包下载步骤的一部分。然后，这些依赖项与Buildroot的`DL_DIR`中缓存的包源代码一起保存在tarball中，因此包的tarball的哈希值就包含了这些依赖项。

这种机制可以确保检测到依赖项中的任何更改，并允许完全脱机执行构建。


## 18.18. Infrastructure for Go packages
-------------------------------------

此基础架构适用于使用标准构建系统并使用打包依赖项的Go软件包。

### 18.18.1. `golang-package` tutorial

首先，让我们看看如何为go包编写一个`.mk`文件，举个例子:

```bash
01: ################################################################################
02: #
03: # foo
04: #
05: ################################################################################
06:
07: FOO_VERSION = 1.0
08: FOO_SITE = $(call github,bar,foo,$(FOO_VERSION))
09: FOO_LICENSE = BSD-3-Clause
10: FOO_LICENSE_FILES = LICENSE
11:
12: $(eval $(golang-package))
```

第7行声明了包的版本号。

在第8行，我们声明了包的上游位置，这里是从Github获取的，因为大量的Go包托管在Github上。

第9行和第10行给出了这个包的许可细节。

最后，在第12行，我们调用`golang-package`宏，该宏生成所有实际允许构建包的Makefile规则。

### 18.18.2. `golang-package` reference

在他们的`Config.in`文件中，使用`golang-package`基础架构的包应该依赖于`BR2_PACKAGE_HOST_GO_TARGET_ARCH_SUPPORTS`，因为Buildroot会自动为这些包添加对`host-go`的依赖。如果你需要在你的包中支持CGO，你必须添加一个依赖`BR2_PACKAGE_HOST_GO_TARGET_CGO_LINKING_SUPPORTS`。

Go包基础架构的主要宏是`golang-package`。它类似于`generic-package`宏。也可以使用`host-golang-package`宏构建主机包。由`host-golang-package`宏构建的主机包应该依赖于BR2_PACKAGE_HOST_GO_HOST_ARCH_SUPPORTS。

就像通用基础架构一样，Go基础架构的工作方式是在调用`golang-package`之前定义许多变量。

[通用包基础架构](#1862-generic-package-reference)中存在的所有包元数据信息变量也存在于Go基础架构中: `FOO_VERSION`, `FOO_SOURCE`, `FOO_PATCH`, `FOO_SITE`, `FOO_SUBDIR`, `FOO_DEPENDENCIES`, `FOO_LICENSE`, `FOO_LICENSE_FILES`, `FOO_INSTALL_STAGING`, 等。

请注意，没有必要在包的`FOO_DEPENDENCIES`变量中添加`host-go`，因为这个基本依赖项会根据Go包基础架构的需要自动添加。

根据包的需求，可以选择性地定义一些特定于Go基础架构的额外变量。它们中的许多只在非常特定的情况下有用，因此典型的包将只使用其中的一些，或完全不使用。

*   包必须在`FOO_GOMOD`变量中指定它的Go模块名称。如果没有指定，它默认为`URL-domain/1st-part-of-URL/2nd-part-of-URL`，例如对于指定`FOO_SITE = $(call github,bar,foo,$(FOO_VERSION))`的包，`FOO_GOMOD`的值为`github.com/bar/foo`。如果`go.mod`文件不存在，Go包基础架构将自动在包源树中生成一个最小的`go.mod`文件。
*   `FOO_LDFLAGS`和`FOO_TAGS`可以分别用于将`LDFLAGS`或`TAGS`传递给`go`构建命令。
*   `FOO_BUILD_TARGETS`可以用来传递要构建的目标列表。如果未指定`FOO_BUILD_TARGETS`，则默认为`.`。我们有两种情况:
    
    *   `FOO_BUILD_TARGETS`是`.`。在这种情况下，我们假设只会生成一个二进制文件，并且默认情况下我们以包名命名它。如果不合适，可以使用`FOO_BIN_NAME`覆盖生成的二进制文件的名称。
    *   `FOO_BUILD_TARGETS`不是`.`。在这种情况下，我们遍历这些值来构建每个目标，并为每个目标生成一个二进制文件，该二进制文件是目标的非目录部分。例如，如果`FOO_BUILD_TARGETS = cmd/docker cmd/dockerd`，则生成的二进制文件是`docker`和`dockerd`。
    
*   `FOO_INSTALL_BINS`可以用来传递应该安装在目标`/usr/bin`中的二进制文件列表。如果未指定`FOO_INSTALL_BINS`，则默认为包的小写名称。

使用Go基础架构，构建和安装包所需的所有步骤都已定义，并且它们通常适用于大多数基于Go的包。但是，在需要时，仍然可以自定义任何特定步骤的操作:

*   通过添加一个操作后钩子(在提取、打补丁、配置、构建或安装之后)。请参阅[第18.23节，“在各种构建步骤中可用的钩子”](#1823-hooks-available-in-the-various-build-steps)了解详情。
*   通过重写其中一个步骤。例如，即使使用了Go基础架构，如果包`.mk`文件定义自己的`FOO_BUILD_CMDS`变量，它将被使用，而不是默认的Go变量。但是，应该限制在非常特殊的情况下使用这种方法。一般情况下不要使用它。

Go包可以依赖于其`go.mod`文件中列出的其他Go模块。Buildroot会自动下载这些依赖项，作为使用`golang-package`基础架构的包下载步骤的一部分。然后，这些依赖项与Buildroot的`DL_DIR`中缓存的包源代码一起保存在tarball中，因此包的tarball的哈希值就包含了这些依赖项。

这种机制可以确保检测到依赖项中的任何更改，并允许完全脱机执行构建。


## 18.19. Infrastructure for QMake-based packages
----------------------------------------------

### 18.19.1. `qmake-package` tutorial

首先，让我们看看如何为基于QMake的包写一个`.mk`文件，举个例子:

```bash
01: ################################################################################
02: #
03: # libfoo
04: #
05: ################################################################################
06:
07: LIBFOO_VERSION = 1.0
08: LIBFOO_SOURCE = libfoo-$(LIBFOO_VERSION).tar.gz
09: LIBFOO_SITE = http://www.foosoftware.org/download
10: LIBFOO_CONF_OPTS = QT_CONFIG+=bar QT_CONFIG-=baz
11: LIBFOO_DEPENDENCIES = bar
12:
13: $(eval $(qmake-package))
```

第7行声明了包的版本号。

在第8行和第9行，我们声明了压缩包的名称(建议使用xz-ed压缩包)以及压缩包在Web上的位置。Buildroot会自动从这个位置下载压缩包。

第10行告诉Buildroot要为libfoo启用哪些选项。

在第11行，我们告诉Buildroot libfoo的依赖。

最后，在第13行，我们调用`qmake-package`宏，该宏生成所有实际允许构建包的Makefile规则。

### 18.19.2. `qmake-package` reference

QMake包基础架构的主要宏是`qmake-package`。它类似于`generic-package`宏。

就像通用基础架构一样，QMake基础架构通过在调用`qmake-package`宏之前定义许多变量来工作。

首先，存在于通用基础架构中的所有包元数据信息变量也存在于QMake基础架构中: `LIBFOO_VERSION`, `LIBFOO_SOURCE`, `LIBFOO_PATCH`, `LIBFOO_SITE`, `LIBFOO_SUBDIR`, `LIBFOO_DEPENDENCIES`, `LIBFOO_INSTALL_STAGING`, `LIBFOO_INSTALL_TARGET`.

还可以定义一个特定于QMake基础架构的额外变量。

*   `LIBFOO_CONF_ENV`, 为配置步骤指定传递给`qmake`脚本的额外环境变量。默认为空。
*   `LIBFOO_CONF_OPTS`, 为配置步骤指定传递给`qmake`脚本的其他选项。默认为空。
*   `LIBFOO_MAKE_ENV`, 在构建和安装步骤中，在`make`命令中指定额外的环境变量。默认为空。
*   `LIBFOO_MAKE_OPTS`, 在构建步骤中指定要传递给`make`命令的额外目标。默认为空。
*   `LIBFOO_INSTALL_STAGING_OPTS`, 指定额外的目标，在过渡安装阶段传递给`make`命令。默认情况下是`install`。
*   `LIBFOO_INSTALL_TARGET_OPTS`, 在目标安装步骤中指定要传递给`make`命令的其他目标。默认情况下是`install`。
*   `LIBFOO_SYNC_QT_HEADERS`, 在qmake之前运行syncqt.pl。有些包需要在运行构建之前有一个正确填充的包含目录。


## 18.20. Infrastructure for packages building kernel modules
----------------------------------------------------------

Buildroot提供了一个辅助基础架构，使编写构建和安装Linux内核模块的包变得容易。有些包只包含一个内核模块，其他包除了内核模块之外还包含程序和库。Buildroot的helper基础架构支持这两种情况。

### 18.20.1. `kernel-module` tutorial

让我们从一个示例开始，说明如何准备一个只构建内核模块而不构建其他组件的简单包:

```bash
01: ################################################################################
02: #
03: # foo
04: #
05: ################################################################################
06:
07: FOO_VERSION = 1.2.3
08: FOO_SOURCE = foo-$(FOO_VERSION).tar.xz
09: FOO_SITE = http://www.foosoftware.org/download
10: FOO_LICENSE = GPL-2.0
11: FOO_LICENSE_FILES = COPYING
12:
13: $(eval $(kernel-module))
14: $(eval $(generic-package))
```

第7-11行定义了常用的元数据，用于指定版本、存档名称、在何处查找包源的远程URI、许可信息。

在第13行，我们调用`kernel-module`辅助基础架构，它生成所有适当的Makefile规则和变量来构建该内核模块。

最后，在第14行，我们调用了[`generic-package`基础架构](#1861-generic-package-tutorial)。

`linux`依赖项会自动添加，因此不需要在`FOO_DEPENDENCIES`中指定它。

您可能已经注意到，与其他包基础结构不同，我们显式调用第二个基础结构。这使得一个包可以构建一个内核模块，如果需要，还可以使用任何其他包基础结构来构建普通的用户层组件(库、可执行文件……)。仅使用`kernel-module`基础架构是不够的;**必须**使用另一个包基础架构。

让我们看一个更复杂的例子:

```bash
01: ################################################################################
02: #
03: # foo
04: #
05: ################################################################################
06:
07: FOO_VERSION = 1.2.3
08: FOO_SOURCE = foo-$(FOO_VERSION).tar.xz
09: FOO_SITE = http://www.foosoftware.org/download
10: FOO_LICENSE = GPL-2.0
11: FOO_LICENSE_FILES = COPYING
12:
13: FOO_MODULE_SUBDIRS = driver/base
14: FOO_MODULE_MAKE_OPTS = KVERSION=$(LINUX_VERSION_PROBED)
15:
16: ifeq ($(BR2_PACKAGE_LIBBAR),y)
17: FOO_DEPENDENCIES += libbar
18: FOO_CONF_OPTS += --enable-bar
19: FOO_MODULE_SUBDIRS += driver/bar
20: else
21: FOO_CONF_OPTS += --disable-bar
22: endif
23:
24: $(eval $(kernel-module))
26: $(eval $(autotools-package))
```

在这里，我们看到我们有一个基于autotools的包，它还构建位于子目录`driver/base`中的内核模块，如果启用libbar，则构建位于子目录`driver/bar`中的内核模块，并定义在构建模块时传递给Linux构建系统的变量`KVERSION`。

### 18.20.2. `kernel-module` reference

内核模块基础架构的主要宏是`kernel-module`。与其他包基础结构不同，它不是独立的，并且需要在它之后调用任何其他`*-package`宏。

`kernel-module`宏定义了post-build和post-target-install钩子来构建内核模块。如果包的`.mk`需要访问构建的内核模块，它应该在调用`kernel-module`之后**注册**的post-build钩子中这样做。类似地，如果包的`.mk`需要在安装后访问内核模块，它应该在调用`kernel-module`后**注册**的安装后钩子中这样做。下面是一个例子:

```bash
$(eval $(kernel-module))

define FOO_DO_STUFF_WITH_KERNEL_MODULE
    # Do something with it...
endef
FOO_POST_BUILD_HOOKS += FOO_DO_STUFF_WITH_KERNEL_MODULE

$(eval $(generic-package))
```

最后，与其他包架构不同的是，没有`host-kernel-module`变体来构建宿主内核模块。

可选地定义下列额外变量，以进一步配置内核模块的构建:

*   `FOO_MODULE_SUBDIRS` 可以设置为内核模块源代码所在的一个子目录或多个子目录(相对于包源代码的顶层目录)。如果设置为空或未设置，则认为内核模块的源代码位于包源代码树的顶部。
*   `FOO_MODULE_MAKE_OPTS` 可以设置为包含传递给Linux构建系统的额外变量定义。

你也可以引用(但**不能**设置!)这些变量:

*   `LINUX_DIR` 包含了Linux内核被提取和构建的路径。
*   `LINUX_VERSION` 包含用户配置的版本字符串。
*   `LINUX_VERSION_PROBED` 包含内核的真实版本字符串，可以通过运行`make -C $(LINUX_DIR) kernelrelease`获取。
*   `KERNEL_ARCH` 包含当前架构的名称，如`arm`、`mips`…


## 18.21. Infrastructure for asciidoc documents
--------------------------------------------

你正在阅读的Buildroot手册完全是使用[AsciiDoc](http://asciidoc.org/)标记语法编写的。手册会被渲染成多种格式:

*   html
*   split-html
*   pdf
*   epub
*   text

尽管Buildroot只包含一个用AsciiDoc编写的文档，但就像包一样，有一个使用AsciiDoc语法渲染文档的基础架构。

同样对于包，AsciiDoc的基础架构可以从[br2-external树](#92-keeping-customizations-outside-of-buildroot)中获取。这允许br2-external树的文档与Buildroot文档匹配，因为它将呈现相同的格式，使用相同的布局和主题。

### 18.21.1. `asciidoc-document` tutorial

包基础结构的后缀是`-package`，而文档基础结构的后缀是`-document`。因此，AsciiDoc基础架构被命名为`asciidoc-document`。

下面是一个渲染简单AsciiDoc文档的例子。

```bash
01: ################################################################################
02: #
03: # foo-document
04: #
05: ################################################################################
06:
07: FOO_SOURCES = $(sort $(wildcard $(FOO_DOCDIR)/*))
08: $(eval $(call asciidoc-document))
```

在第7行，Makefile声明文档的源是什么。目前，文档的来源只能是本地的;Buildroot不会尝试下载任何东西来渲染文档。因此，您必须指明源的位置。通常，上面的字符串对于没有子目录结构的文档已经足够了。

在第8行，我们调用`asciidoc-document`函数，它会生成渲染文档所需的所有Makefile代码。

### 18.21.2. `asciidoc-document` reference

可以在`.mk`文件中设置用于提供元数据信息的变量列表如下(假设文档名称为`foo`):

*   `FOO_SOURCES`, 强制，定义文档的源文件。
*   `FOO_RESOURCES`, 可选，可以包含一个以空格分隔的路径列表，这些路径列表指向一个或多个包含所谓资源(如CSS或图像)的目录。默认为空。
*   `FOO_DEPENDENCIES`, 可选的是在构建此文档之前必须构建的包列表(很可能是host-packages)。
*   `FOO_TOC_DEPTH`, `FOO_TOC_DEPTH_<FMT>`, 可选的，这个文档的内容表的深度，它可以被指定的格式`<FMT>`覆盖(见上面的渲染格式列表，但都是大写的，并将下划线替换为破折号;参见下面的例子)。默认值:`1`。

还有其他钩子(有关钩子的一般信息，请参阅[第18.23节，“在各种构建步骤中可用的钩子”](#1823-hooks-available-in-the-various-build-steps))，文档可以设置为定义在各个步骤中要执行的额外操作:

*   `FOO_POST_RSYNC_HOOKS`来在Buildroot复制源代码之后运行其他命令。例如，这可以用于生成从树中提取信息的部分手册。例如，Buildroot使用这个钩子来生成附录中的表。
*   `FOO_CHECK_DEPENDENCIES_HOOKS`在必需的组件上运行额外的测试来生成文档。在AsciiDoc中，可以调用过滤器，即解析AsciiDoc块并适当渲染它的程序(例如[ditaa](http://ditaa.sourceforge.net/) 或 [aafigure](https://pythonhosted.org/aafigure/))。
*   `FOO_CHECK_DEPENDENCIES_<FMT>_HOOKS`，用于对指定的格式`<FMT>`运行额外的测试(参见上面的渲染格式列表)。

Buildroot设置以下变量，可以在上面的定义中使用:

*   `$(FOO_DOCDIR)`，类似于`$(FOO_PKGDIR)`，包含了包含`foo.mk`的目录的路径。它可以用来引用文档源，也可以用在钩子中，特别是在需要生成文档的部分内容时，可以用在后rsync钩子中。
*   与传统的包一样，`$(@D)`包含了将复制和构建文档的目录路径。

下面是一个使用了所有变量和所有钩子的完整示例:

```bash
01: ################################################################################
02: #
03: # foo-document
04: #
05: ################################################################################
06:
07: FOO_SOURCES = $(sort $(wildcard $(FOO_DOCDIR)/*))
08: FOO_RESOURCES = $(sort $(wildcard $(FOO_DOCDIR)/ressources))
09:
10: FOO_TOC_DEPTH = 2
11: FOO_TOC_DEPTH_HTML = 1
12: FOO_TOC_DEPTH_SPLIT_HTML = 3
13:
14: define FOO_GEN_EXTRA_DOC
15:     /path/to/generate-script --outdir=$(@D)
16: endef
17: FOO_POST_RSYNC_HOOKS += FOO_GEN_EXTRA_DOC
18:
19: define FOO_CHECK_MY_PROG
20:     if ! which my-prog >/dev/null 2>&1; then \
21:         echo "You need my-prog to generate the foo document"; \
22:         exit 1; \
23:     fi
24: endef
25: FOO_CHECK_DEPENDENCIES_HOOKS += FOO_CHECK_MY_PROG
26:
27: define FOO_CHECK_MY_OTHER_PROG
28:     if ! which my-other-prog >/dev/null 2>&1; then \
29:         echo "You need my-other-prog to generate the foo document as PDF"; \
30:         exit 1; \
31:     fi
32: endef
33: FOO_CHECK_DEPENDENCIES_PDF_HOOKS += FOO_CHECK_MY_OTHER_PROG
34:
35: $(eval $(call asciidoc-document))
```


## 18.22. Infrastructure specific to the Linux kernel package
----------------------------------------------------------

Linux内核包可以使用一些基于包钩子的特定基础结构来构建Linux内核工具或/和构建Linux内核扩展。

### 18.22.1. linux-kernel-tools

Buildroot提供了一个辅助基础架构，用于为Linux内核源代码中的目标构建一些用户空间工具。由于它们的源代码是内核源代码的一部分，因此存在一个特殊的包，`linux-tools`，并重用在目标上运行的Linux内核源代码。

让我们看一个Linux工具的例子。对于名为`foo`的新Linux工具，在现有的`package/linux-tools/Config.in`中创建一个新的菜单项。该文件将包含与将使用并显示在配置工具中的每个内核工具相关的选项描述。它基本上看起来像这样:

```bash
01: config BR2_PACKAGE_LINUX_TOOLS_FOO
02:     bool "foo"
03:     select BR2_PACKAGE_LINUX_TOOLS
04:     help
05:       This is a comment that explains what foo kernel tool is.
06:
07:       http://foosoftware.org/foo/
```

选项的名称以前缀`BR2_PACKAGE_LINUX_TOOLS_`开始，后面是工具的大写名称(就像包一样)。

**注意.** 与其他包不同，`linux-tools`包选项出现在`linux`内核菜单中，在`Linux Kernel Tools`子菜单下，而不是在`Target packages`主菜单下。

然后为每个linux工具添加一个新的`.mk.in`文件，名为`package/linux-tools/linux-tool-foo.mk.in`。它基本上看起来像这样:

```bash
01: ################################################################################
02: #
03: # foo
04: #
05: ################################################################################
06:
07: LINUX_TOOLS += foo
08:
09: FOO_DEPENDENCIES = libbbb
10:
11: define FOO_BUILD_CMDS
12:     $(TARGET_MAKE_ENV) $(MAKE) -C $(LINUX_DIR)/tools foo
13: endef
14:
15: define FOO_INSTALL_STAGING_CMDS
16:     $(TARGET_MAKE_ENV) $(MAKE) -C $(LINUX_DIR)/tools \
17:             DESTDIR=$(STAGING_DIR) \
18:             foo_install
19: endef
20:
21: define FOO_INSTALL_TARGET_CMDS
22:     $(TARGET_MAKE_ENV) $(MAKE) -C $(LINUX_DIR)/tools \
23:             DESTDIR=$(TARGET_DIR) \
24:             foo_install
25: endef
```

在第7行，我们将Linux工具`foo`注册到可用的Linux工具列表中。

在第9行，我们指定了该工具依赖的依赖项列表。只有当选择`foo`工具时，这些依赖项才会添加到Linux包依赖列表中。

Makefile的其余部分，第11-25行定义了在Linux工具构建过程的不同步骤中应该做什么，例如对于[`通用包`](#1861-generic-package-tutorial)。它们实际上只会在`foo`工具被选中时使用。唯一支持的命令是`_BUILD_CMDS`, `_INSTALL_STAGING_CMDS` 和 `_INSTALL_TARGET_CMDS`。

**注意.** **不能**调用`$(eval $(generic-package))`或任何其他包基础架构! Linux工具本身不是包，它们是`linux-tools`包的一部分。

### 18.22.2. linux-kernel-extensions

有些包提供了需要修改Linux内核树的新特性。这可以是以补丁的形式应用到内核树，也可以是以向内核树添加新文件的形式。Buildroot的Linux内核扩展基础架构提供了一个简单的解决方案，可以在提取内核源代码之后、应用内核补丁之前自动完成这项工作。使用这种机制打包的扩展示例包括实时扩展Xenomai和RTAI，以及一组树外 LCD屏幕驱动程序`fbtft`。

让我们看一个如何添加新的Linux扩展`foo`的例子。

首先，创建提供扩展的包`foo`:这个包是一个标准包;请参阅前面的章节，了解如何创建这样的包。这个包负责下载源代码存档、检查散列、定义许可证信息并构建用户空间工具(如果有的话)。

然后正确地创建Linux扩展:在现有的`linux/Config.ext.in`中创建一个新的菜单项。该文件包含了与将在配置工具中使用和显示的每个内核扩展相关的选项描述。它基本上看起来像这样:

```bash
01: config BR2_LINUX_KERNEL_EXT_FOO
02:     bool "foo"
03:     help
04:       This is a comment that explains what foo kernel extension is.
05:
06:       http://foosoftware.org/foo/
```

然后，为每个linux扩展添加一个名为`linux/linux-ext-foo.mk`的新`.mk`文件。它基本上应该包含:

```bash
01: ################################################################################
02: #
03: # foo
04: #
05: ################################################################################
06:
07: LINUX_EXTENSIONS += foo
08:
09: define FOO_PREPARE_KERNEL
10:     $(FOO_DIR)/prepare-kernel-tree.sh --linux-dir=$(@D)
11: endef
```

在第7行，我们将Linux扩展`foo`添加到可用的Linux扩展列表中。

在第9-11行，我们定义了该扩展修改Linux内核树时应该做的工作。这是特定于linux扩展的，可以使用由`foo`包定义的变量，如:`$(FOO_DIR)`或`$(FOO_VERSION)`，以及所有linux变量，如:`$(LINUX_VERSION)`或`$(LINUX_VERSION_PROBED)`， `$(KERNEL_ARCH)`，请参阅[这些内核变量的定义](#18202-kernel-module-reference)。


## 18.23. Hooks available in the various build steps
-------------------------------------------------

通用基础架构(以及派生的autotools和cmake基础架构)允许包指定钩子。这些定义了在现有步骤之后要执行的进一步操作。大多数hooks对于通用包并不是真的有用，因为`.mk`文件已经完全控制了包构造的每个步骤中执行的操作。

以下是可用的钩子点:

*   `LIBFOO_PRE_DOWNLOAD_HOOKS`
*   `LIBFOO_POST_DOWNLOAD_HOOKS`
*   `LIBFOO_PRE_EXTRACT_HOOKS`
*   `LIBFOO_POST_EXTRACT_HOOKS`
*   `LIBFOO_PRE_RSYNC_HOOKS`
*   `LIBFOO_POST_RSYNC_HOOKS`
*   `LIBFOO_PRE_PATCH_HOOKS`
*   `LIBFOO_POST_PATCH_HOOKS`
*   `LIBFOO_PRE_CONFIGURE_HOOKS`
*   `LIBFOO_POST_CONFIGURE_HOOKS`
*   `LIBFOO_PRE_BUILD_HOOKS`
*   `LIBFOO_POST_BUILD_HOOKS`
*   `LIBFOO_PRE_INSTALL_HOOKS` (仅针对主机包)
*   `LIBFOO_POST_INSTALL_HOOKS` (仅针对主机包)
*   `LIBFOO_PRE_INSTALL_STAGING_HOOKS` (只适用于目标包)
*   `LIBFOO_POST_INSTALL_STAGING_HOOKS` (只适用于目标包)
*   `LIBFOO_PRE_INSTALL_TARGET_HOOKS` (只适用于目标包)
*   `LIBFOO_POST_INSTALL_TARGET_HOOKS` (只适用于目标包)
*   `LIBFOO_PRE_INSTALL_IMAGES_HOOKS`
*   `LIBFOO_POST_INSTALL_IMAGES_HOOKS`
*   `LIBFOO_PRE_LEGAL_INFO_HOOKS`
*   `LIBFOO_POST_LEGAL_INFO_HOOKS`
*   `LIBFOO_TARGET_FINALIZE_HOOKS`

这些变量是变量名的列表，其中包含要在该挂钩点执行的操作。这允许在给定的挂钩点注册几个挂钩。下面是一个例子:

```bash
define LIBFOO_POST_PATCH_FIXUP
        action1
        action2
endef

LIBFOO_POST_PATCH_HOOKS += LIBFOO_POST_PATCH_FIXUP
```

### 18.23.1. Using the `POST_RSYNC` hook

`POST_RSYNC`钩子只在使用本地源的包中运行，无论是通过`local`站点方法还是通过`OVERRIDE_SRCDIR`机制。在这种情况下，包源会使用`rsync`从本地复制到buildroot构建目录中。不过，`rsync`命令不会复制源目录中的所有文件。属于版本控制系统的文件，如目录`.git`、`.hg`等不会被复制。对于大多数包来说，这已经足够了，但是给定的包可以使用`POST_RSYNC`钩子执行额外的操作。

原则上，钩子可以包含任何你想要的命令。然而，一个特定的用例是使用`rsync`故意复制版本控制目录。你在hook中使用的`rsync`命令可以使用以下变量:

*   `$(SRCDIR)`: 被覆盖的源目录的路径
*   `$(@D)`: 构建目录的路径

### 18.23.2. Target-finalize hook

包也可以在`LIBFOO_TARGET_FINALIZE_HOOKS`中注册钩子。这些钩子在构建所有包之后、生成文件系统映像之前运行。它们很少使用，您的包可能不需要它们。


## 18.24. Gettext integration and interaction with packages
--------------------------------------------------------

许多支持国际化的包都使用gettext库。这个库的依赖关系相当复杂，因此需要一些解释。

glibc C库集成了一个完整的gettext实现，支持翻译。因此，glibc内置了对本地语言的支持。

另一方面，uClibc和musl C库只提供了gettext功能的存根实现，它允许使用gettext函数编译库和程序，但没有提供完整的gettext实现的转换能力。使用这样的C库，如果需要真正的本地语言支持，可以由`gettext`包的`libintl`库提供。

因此，为了确保正确处理本地语言支持，Buildroot中可以使用NLS支持的包应该:

1.  当`BR2_SYSTEM_ENABLE_NLS=y`时，确保启用了NLS支持。对于autotools包，这是自动完成的，因此应该只对使用其他包基础架构的包进行此操作。
2.  将`$(TARGET_NLS_DEPENDENCIES)`添加到`<pkg>_DEPENDENCIES`包变量中。这种添加应该无条件地完成:该变量的值由核心基础架构自动调整，以包含相关的包列表。如果禁用了NLS支持，则此变量为空。如果启用了NLS支持，此变量包含`host-gettext`，以便在主机上可以使用编译翻译文件所需的工具。此外，如果使用uClibc或musl，该变量还包含`gettext`，以便获得完整的gettext实现。
3.  如果需要，将`$(TARGET_NLS_LIBS)`添加到链接标志中，以便使用`libintl`链接包。autotools包通常不需要这样做，因为它们通常会自动检测它们应该与`libintl`链接。然而，使用其他构建系统的包，或者有问题的基于autotools的包可能需要这个。`$(TARGET_NLS_LIBS)`应该无条件地添加到链接器标志中，因为核心会根据配置自动将其设置为空或定义为`-lintl`。

不应该对`Config.in`文件进行任何更改以支持NLS。

最后，某些包在目标上需要一些gettext实用程序，例如`gettext`程序本身，它允许从命令行检索翻译后的字符串。在这种情况下，包应该:

*   在他们的`Config.in`文件中使用`select BR2_PACKAGE_GETTEXT`，在上面的注释中表明它只是一个运行时依赖项。
*   不要在他们的`.mk`文件的`DEPENDENCIES`变量中添加任何`gettext`依赖。


## 18.25. Tips and tricks
----------------------

### 18.25.1. Package name, config entry name and makefile variable relationship

在Buildroot中，以下两者之间存在某种关系:

*   包名，即包目录名(以及`*.mk`文件的名称);
*   在`Config.in`文件中声明的配置项名称;
*   makefile变量前缀。

必须使用以下规则来维护这些元素之间的一致性:

*   包目录和`*.mk`名称是包名称本身(例如:`package/foo-bar_boo/foo-bar_boo.mk`);
*   make目标的名称是包本身的名称(例如:`foo-bar_boo`);
*   配置项是大写的包名，带有`.`和`-`字符替换为`_`，前缀为`BR2_PACKAGE_`(例如:`BR2_PACKAGE_FOO_BAR_BOO`);
*   `*.mk`文件变量前缀是用`.`和`-`字符替换为`_`的大写包名(例如:`FOO_BAR_BOO_VERSION`)。

### 18.25.2. How to check the coding style

Buildroot在`utils/check-package`中提供了一个脚本，用于检查新文件或更改后的文件的编码风格。它不是一个完整的语言验证器，但它捕获了许多常见的错误。它意味着在创建提交的补丁之前，在您创建或修改的实际文件中运行。

这个脚本可以用于包，文件系统的makefile, Config.in文件等。它不检查定义包基础结构的文件和其他一些包含类似通用代码的文件。

要使用它，运行`check-package`脚本，告诉你创建或更改了哪些文件:

```bash
$ ./utils/check-package package/new-package/*
```

如果你的路径中有`utils`目录，你还可以运行:

```bash
$ cd package/new-package/
$ check-package *
```

这个工具也可以用于br2-external中的包:

```bash
$ check-package -b /path/to/br2-ext-tree/package/my-package/*
```

### 18.25.3. How to test your package

添加新包后，重要的是在各种条件下测试它:它是否为所有体系结构构建?它是否使用不同的C库构建?它需要线程吗，NPTL?等等……

Buildroot运行[autobuilders](http://autobuild.buildroot.org/)，连续测试随机配置。然而，这些只会构建git树的`master`分支，而你的新软件包还不存在。

Buildroot在`utils/test-pkg`中提供了一个脚本，该脚本使用与自动构建器相同的基本配置，因此您可以在相同的条件下测试您的包。

首先，创建一个配置片段，其中包含启用包所需的所有必要选项，但不包含任何架构或工具链选项。例如，让我们创建一个配置片段，只启用`libcurl`，而不使用任何TLS后端:

```bash
$ cat libcurl.config
BR2_PACKAGE_LIBCURL=y
```

如果包需要更多配置选项，可以将它们添加到config片段中。例如，下面是你如何使用`openssl`作为TLS后端和`curl`程序来测试`libcurl`:

```bash
$ cat libcurl.config
BR2_PACKAGE_LIBCURL=y
```

然后运行`test-pkg`脚本，告诉它要使用哪个配置片段和要测试哪个包:

```bash
$ ./utils/test-pkg -c libcurl.config -p libcurl
```

默认情况下，`test-pkg`将根据自动构建器使用的工具链子集来构建你的包，Buildroot开发人员已将其选择为最有用和最有代表性的子集。如果你想测试所有的工具链，传入`-a`选项。注意，在任何情况下，内部工具链都被排除在外，因为它们的构建时间太长。

输出列出了所有测试过的工具链和相应的结果(摘录，结果是假的):

```bash
$ ./utils/test-pkg -c libcurl.config -p libcurl
                armv5-ctng-linux-gnueabi [ 1/11]: OK
              armv7-ctng-linux-gnueabihf [ 2/11]: OK
                        br-aarch64-glibc [ 3/11]: SKIPPED
                           br-arcle-hs38 [ 4/11]: SKIPPED
                            br-arm-basic [ 5/11]: FAILED
                  br-arm-cortex-a9-glibc [ 6/11]: OK
                   br-arm-cortex-a9-musl [ 7/11]: FAILED
                   br-arm-cortex-m4-full [ 8/11]: OK
                             br-arm-full [ 9/11]: OK
                    br-arm-full-nothread [10/11]: FAILED
                      br-arm-full-static [11/11]: OK
11 builds, 2 skipped, 2 build failed, 1 legal-info failed
```

结果的平均值为:

*   `OK`: 构建成功。
*   `SKIPPED`: “配置”片段中列出的一个或多个配置选项没有出现在最终配置中。这是由于工具链不满足选项的依赖关系，例如，例如一个`depends on BR2_USE_MMU`的包具有noMMU工具链。缺失的选项在输出构建目录中的`missing.config`中报告(默认为`~/br-test-pkg/TOOLCHAIN_NAME/`)。
*   `FAILED`: 构建失败。检查输出构建目录中的`logfile`文件，看看哪里出了问题:
    
    *   实际的构建失败了，
    *   法律信息失败了，
    *   其中一个初步步骤(下载配置文件，应用配置，对包运行`dirclean`)失败了。
    

当出现故障时，你可以使用相同的选项重新运行脚本(在修复包之后);该脚本将尝试为所有工具链重新构建使用`-p`指定的包，而不需要重新构建该包的所有依赖项。

`test-pkg`脚本接受一些选项，你可以通过运行以下命令来获得一些帮助:

```bash
$ ./utils/test-pkg -h
```

### 18.25.4. How to add a package from GitHub

GitHub上的包通常没有包含发布压缩包的下载区。不过，可以直接从GitHub上的仓库下载压缩包。众所周知，GitHub在过去改变了下载机制，因此应该使用GitHub辅助函数，如下所示。

```bash
# Use a tag or a full commit ID
FOO_VERSION = 1.0
FOO_SITE = $(call github,<user>,<package>,v$(FOO_VERSION))
```

**注意**

*   FOO_VERSION可以是标签，也可以是提交ID。
*   github生成的tarball名称与Buildroot的默认名称匹配(例如:`foo-f6fb6654af62045239caed5950bc6c7971965e60.tar.gz`)，因此无需在`.mk`文件中指定。
*   当使用提交ID作为版本时，应该使用完整的40个十六进制字符。
*   当标签在`v1.0`中包含一个像`v`这样的前缀时，那么`VERSION`变量应该只包含`1.0`，并且`v`应该直接添加到`SITE`变量中，如上所示。这确保了`VERSION`变量值可以用于与[release-monitoring.org](https://www.release-monitoring.org/)结果匹配。

如果你想添加的包在GitHub上有release部分，那么维护者可能已经上传了一个发布tarball，或者该版本可能只是从git标签中指向自动生成的tarball。如果维护者上传了一个发布tarball，我们更喜欢使用它，因为它可能略有不同(例如，它包含一个配置脚本，所以我们不需要执行AUTORECONF)。

你可以在发布页面上看到它是上传的tarball文件还是git标签:

![github_hash_mongrel2.png](github_hash_mongrel2.png)

*   如果它看起来像上面的图片，那么它是由维护者上传的，你应该使用该链接(在这个例子中:mongrel2-v1.9.2.tar.bz2)来指定`FOO_SITE`，而不是使用github助手。
*   另一方面，如果**only**“源代码”链接，那么它是自动生成的tar包，你应该使用github辅助函数。

### 18.25.5. How to add a package from Gitlab

与[18.25.4节，“如何从GitHub添加包”](#18254-how-to-add-a-package-from-github)中描述的`github`宏类似，Buildroot还提供了`gitlab`宏，以便从gitlab存储库中下载。它可以用于下载Gitlab生成的自动生成的压缩包，用于特定的标签或提交:

```bash
# Use a tag or a full commit ID
FOO_VERSION = 1.0
FOO_SITE = $(call gitlab,<user>,<package>,v$(FOO_VERSION))
```

默认情况下，它将使用`.tar.gz`压缩包，但Gitlab也提供了`.tar.gz`。因此，通过添加一个`<pkg>_SOURCE`变量，这个`.tar.bz2`压缩包可以被使用:

```bash
# Use a tag or a full commit ID
FOO_VERSION = 1.0
FOO_SITE = $(call gitlab,<user>,<package>,v$(FOO_VERSION))
```

如果上游开发人员在`https://gitlab.com/<project>/releases/`中上传了特定的压缩包，请不要使用此宏，而是直接使用到压缩包的链接。


## 18.26. Conclusion
-----------------

如您所见，向Buildroot添加软件包只是使用现有示例编写一个Makefile，并根据软件包所需的编译过程进行修改。

如果你打包的软件可能对其他人有用，别忘了向Buildroot邮件列表发送补丁(参见[第22.5节，“提交补丁”](#225-submitting-patches))!



# Chapter 19. Patching a package
------------------------------

在集成新包或更新现有包时，可能需要对软件的源代码打补丁，以便在Buildroot中交叉构建。

Buildroot提供了一个基础架构，可以在构建期间自动处理此问题。它支持三种应用补丁集的方式:下载的补丁、buildroot中提供的补丁和位于用户定义的全局补丁目录中的补丁。


## 19.1. Providing patches
-----------------------

### 19.1.1. Downloaded

如果需要应用可下载的补丁，则将其添加到`<packagename>_PATCH`变量中。如果一个条目包含`://`，那么Buildroot将假设它是一个完整的URL并从这个位置下载补丁。否则，Buildroot将假定补丁应该从`<packagename>_SITE`下载。它可以是单个补丁，也可以是包含一系列补丁的压缩包。

与所有下载一样，应该将哈希值添加到`<packagename>.hash`文件中。

这种方法通常用于来自Debian的软件包。

### 19.1.2. Within Buildroot

大多数补丁都是在Buildroot的package目录中提供的。它们通常旨在修复交叉编译、libc支持或其他此类问题。

这些补丁文件应该命名为`<number>-<description>.patch`.

**注意**

*   Buildroot附带的补丁文件文件名中不应该包含任何包版本引用。
*   补丁文件名中的`<number>`字段为申请顺序，从1开始;最好用0填充数字，最多4位，就像git-format-patch所做的那样。例如:`0001-foobar-the-buz.patch`
*   补丁邮件主题前缀不能编号。补丁应该使用`git format-patch -N`命令生成，因为这个编号是为series自动添加的。例如，补丁主题行应该看起来像`Subject: [PATCH] foobar the buz`而不是`Subject: [PATCH n/m] foobar the buz`。
*   以前，补丁必须以包的名称为前缀，如`<package>-<number>-<description>.patch`，但现在不再是这样了。随着时间的推移，现有的包将被修复。不要以包名作为补丁的前缀。
*   之前，`series`文件(`quilt`使用)也可以添加到包目录中。在这种情况下，`series`文件定义了补丁应用的顺序。这已经被弃用，将来会被移除。不要使用系列文件。

### 19.1.3. Global patch directory

`BR2_GLOBAL_PATCH_DIR`配置文件选项可用于指定一个或多个包含全局包补丁的目录列表，其中由空格分隔。详情请参阅[9.8节，“添加特定项目的补丁”](#98-adding-project-specific-patches)。


## 19.2. How patches are applied
-----------------------------

1.  如果定义了，运行`<packagename>_PRE_PATCH_HOOKS`命令;
2.  清理构建目录，删除任何现有的`*.rej`文件;
3.  如果定义了`<packagename>_PATCH`，则应用来自这些压缩包的补丁;
4.  如果在包的Buildroot目录或名为`<packageversion>`的包子目录中有一些`*.patch`文件，那么:
    
    *   如果包目录中存在`series`文件，则根据`series`文件应用补丁;
    *   否则，匹配`*.patch`的补丁文件将按字母顺序应用。因此，为了确保它们按照正确的顺序应用，强烈建议将补丁文件命名为:`<number>-<description>.patch`，其中`<number>`为应用顺序。
    
5.  如果定义了`BR2_GLOBAL_PATCH_DIR`，目录将按照指定的顺序被枚举。如上一步所述，应用补丁。
6.  如果有定义，请运行`<packagename>_POST_PATCH_HOOKS`命令。

如果第3步或第4步出现问题，则构建失败。


## 19.3. Format and licensing of the package patches
-------------------------------------------------

补丁的发布与它们应用的软件遵循相同的许可证(参见[13.2节，“遵守Buildroot许可证”](#132-complying-with-the-buildroot-license))。

应该在补丁的首部注释中添加一条消息，解释补丁的功能以及为什么需要它。

您应该在每个补丁的头部添加一个`Signed-off-by`声明，以帮助跟踪更改并证明该补丁是在与所修改的软件相同的许可证下发布的。

如果软件处于版本控制状态，建议使用上游SCM软件生成补丁集。

否则，将header与`diff -purN package-version.orig/ package-version/`命令的输出连接起来。

如果您更新一个现有的补丁(例如，当提升软件包版本时)，请确保现有的From头和Signed-off-by标记没有被删除，但要在适当的时候更新补丁注释的其余部分。

最后，补丁看起来应该像这样:

```bash
configure.ac: add C++ support test

Signed-off-by: John Doe <john.doe@noname.org>

--- configure.ac.orig
+++ configure.ac
@@ -40,2 +40,12 @@

AC_PROG_MAKE_SET
+
+AC_CACHE_CHECK([whether the C++ compiler works],
+               [rw_cv_prog_cxx_works],
+               [AC_LANG_PUSH([C++])
+                AC_LINK_IFELSE([AC_LANG_PROGRAM([], [])],
+                               [rw_cv_prog_cxx_works=yes],
+                               [rw_cv_prog_cxx_works=no])
+                AC_LANG_POP([C++])])
+
+AM_CONDITIONAL([CXX_WORKS], [test "x$rw_cv_prog_cxx_works" = "xyes"])
```

## 19.4. Additional patch documentation
------------------------------------

理想情况下，所有补丁都应通过“上游”预告片记录上游补丁或补丁提交（如果适用）。

在向主线移植已经被接受的upstream补丁时，最好引用提交的URL:

```bash
Upstream: <URL to upstream commit>
```

如果在Buildroot中发现了一个新问题，并且upstream通常受到该问题的影响(这不是Buildroot特有的问题)，用户应该提交补丁upstream，并在可能的情况下提供该提交的链接:

```bash
Upstream: <URL to upstream mailing list submission or merge request>
```

已经提交但被上游拒绝的补丁应注意这一点，并包括评论，说明尽管处于上游状态，该补丁仍被使用的原因。

注意:在上述任何场景中，添加几句话，说明可能需要对补丁进行的任何更改，也是明智的。

如果一个补丁没有在上游应用，那么应该注释说明:

```bash
Upstream: <URL to upstream mailing list submission or merge request>
```

添加此文档有助于简化包版本更新期间的补丁审查过程。



# Chapter 20. Download infrastructure
-----------------------------------

TODO



# Chapter 21. Debugging Buildroot
-------------------------------

在构建包时，可以检测`Buildroot`的步骤。定义变量`BR2_INSTRUMENTATION_SCRIPTS`，在空格分隔的列表中包含一个或多个脚本(或其他可执行文件)的路径，你想在每个步骤之前和之后调用它。这些脚本按顺序调用，有三个参数:

*   `start` 或 `end` 表示步骤的开始（或结束）；
*   即将开始或刚刚结束的步骤的名称;
*   包的名称。

例如:

```bash
make BR2_INSTRUMENTATION_SCRIPTS="/path/to/my/script1 /path/to/my/script2"
```

步骤如下:

*   `extract`
*   `patch`
*   `configure`
*   `build`
*   `install-host`, 当主机包安装在`$(HOST_DIR)`中时
*   `install-target`, 当目标包安装在`$(TARGET_DIR)`中时
*   `install-staging`, 当目标包安装在`$(STAGING_DIR)`中时
*   `install-image`, 当目标包在`$(BINARIES_DIR)`中安装文件时

该脚本可以访问以下变量:

*   `BR2_CONFIG`: Buildroot .config文件的路径
*   `HOST_DIR`, `STAGING_DIR`, `TARGET_DIR`: 参见 [第18.6.2节，“generic-package引用”](#1862-generic-package-reference)
*   `BUILD_DIR`: 解压和构建包的目录
*   `BINARIES_DIR`: 存储所有二进制文件(即图像)的地方
*   `BASE_DIR`: 基本输出目录



# Chapter 22. Contributing to Buildroot
-------------------------------------

您可以通过多种方式为Buildroot做出贡献:分析和修复bug，分析和修复由自动构建器检测到的包构建失败，测试和审查其他开发人员发送的补丁，按照我们的待办事项列表中的项目工作，并将您自己的改进发送到Buildroot或其手册。以下各节将对这些项目进行更详细的介绍。

如果您有兴趣为Buildroot做出贡献，您应该做的第一件事是订阅Buildroot邮件列表。这个列表是与其他Buildroot开发人员交互和贡献的主要方式。如果你还没有订阅，请参阅[Chapter 5, Community resources](#chapter-5-community-resources)的订阅链接。

如果你要接触代码，强烈建议使用git的Buildroot仓库，而不是从提取的源代码tar包开始。Git是开发并直接将补丁发送到邮件列表的最简单方法。请参阅[第3章，使用Buildroot](#chapter-3-getting-buildroot)以获取有关获取Buildroot git树的更多信息。


## 22.1. Reproducing, analyzing and fixing bugs
--------------------------------------------

贡献的第一种方式是查看[Buildroot bug tracker](https://bugs.buildroot.org/buglist.cgi?product=buildroot)中的开放bug报告。当我们努力保持尽可能少的bug数量时，所有对重现、分析和修复bug的帮助都是非常受欢迎的。即使你还没有看到完整的情况，也不要犹豫，在报告发现的bug报告中添加注释。


## 22.2. Analyzing and fixing autobuild failures
---------------------------------------------

Buildroot autobuilders是一组基于随机配置持续运行Buildroot构建的构建机器。这适用于Buildroot支持的所有体系结构、各种工具链和随机选择的包。在Buildroot上有大量的提交活动，这些自动构建器对于在提交后尽早检测问题有很大帮助。

所有构建结果可在[http://autobuild.buildroot.org](http://autobuild.buildroot.org)获得，统计数据可在[http://autobuild.buildroot.org/stats.php](http://autobuild.buildroot.org/stats.php)获得。每天，所有失败包的概述都会发送到邮件列表。

发现问题是很好的，但显然这些问题也必须被修复。非常欢迎您的贡献!基本上可以做两件事:

*   分析问题。每日摘要邮件不包含关于实际故障的详细信息:为了查看发生了什么，您必须打开构建日志并检查最后的输出。让人对邮件中的所有包进行此操作对其他开发人员非常有用，因为他们可以仅根据此输出进行快速初始分析。
*   解决问题。修复自动构建失败时，你应该遵循以下步骤。
    
    1.  检查是否可以通过使用相同的配置进行构建来重现问题。您可以手动执行此操作，也可以使用[br-reproduce-build](http://git.buildroot.org/buildroot-test/tree/utils/br-reproduce-build)脚本，该脚本将自动克隆一个Buildroot git存储库，检查正确的版本，下载并设置正确的配置，然后启动构建。
    2.  分析问题并制定解决方案。
    3.  通过从一个干净的Buildroot树开始并仅应用您的修复来验证问题是否真的被修复了。
    4.  将修复发送到Buildroot邮件列表(参见[第22.5节，“提交补丁”](#225-submitting-patches))。如果您针对包源创建了补丁，您还应该将补丁发送到上游，以便在以后的版本中修复问题，并且可以删除Buildroot中的补丁。在修复自动构建失败的补丁的提交信息中，添加一个对构建结果目录的引用，如下所示:
    

```bash
Fixes: http://autobuild.buildroot.org/results/51000a9d4656afe9e0ea6f07b9f8ed374c2e4069
```


## 22.3. Reviewing and testing patches
-----------------------------------

随着每天发送到邮件列表的补丁数量的增加，维护人员很难判断哪些补丁可以应用，哪些不能。贡献者可以通过检查和测试这些补丁来提供很大的帮助。

在评审过程中，不要犹豫地回复补丁提交的评论、建议或任何有助于大家理解补丁并使其更好的东西。请使用互联网风格的回复在回复补丁提交的纯文本电子邮件。

为了表示一个补丁的批准，有三个正式的标签来跟踪这个批准。要将您的标签添加到补丁中，请在原始作者的签名行下面回复批准标签。这些标记将由patchwork自动获取(参见[第22.3.1节，“从Patchwork中应用补丁”](#2231-applying-patches-from-patchwork))，并在补丁被接受时成为提交日志的一部分。

**Tested-by**

表示补丁测试成功。我们鼓励您指定您执行的测试类型(架构X和Y上的编译测试，目标A上的运行时测试，…)。这些额外的信息可以帮助其他测试人员和维护人员。

**Reviewed-by**

表示您审阅了该补丁的代码，并尽了最大努力来发现问题，但您对涉及的领域还不够熟悉，无法提供Acked-by标签。这意味着补丁中可能还有遗留的问题，会被在该领域有更多经验的人发现。如果发现此类问题，您的Reviewed-by标签仍然是合适的，您不会受到指责。

**Acked-by**

表示您审阅了该补丁的代码，并且您对涉及的领域足够熟悉，可以认为该补丁可以原样提交(不需要额外的更改)。万一后来发现补丁有问题，你的Acked-by可能会被认为是不合适的。因此，受攻击的补丁和受检查的补丁之间的区别主要在于，你准备好接受Acked补丁的责任，而不是经过检查的补丁。

如果您审阅了一个补丁并对其发表了评论，您应该简单地回复该补丁并说明这些评论，而不是提供一个Reviewed-by 或 Acked-by标签。只有当你判断补丁是好的时候，才应该提供这些标签。

值得注意的是，审查或攻击都不意味着已经执行了测试。要表明您已经审阅和测试了补丁，请提供两个单独的标签(Reviewed/Acked-by 和 Tested-by)。

还要注意，任何开发人员都可以提供Tested/Reviewed/Acked-by的标签，无一例外，我们鼓励每个人都这样做。Buildroot没有明确的核心开发人员组，只是碰巧有些开发人员比其他开发人员更活跃。维护者会根据提交者的跟踪记录来评估标签的价值。与新手提供的标签相比，普通贡献者提供的标签自然更值得信任。随着您更定期地提供标签，您的可信性(在维护者看来)将提高，但提供的任何标签都是有价值的。

Buildroot的Patchwork网站可以用于引入补丁以进行测试。有关使用Buildroot的Patchwork网站应用补丁的更多信息，请参阅[第22.3.1节，“从Patchwork应用补丁”](#2231-applying-patches-from-patchwork)。

### 22.3.1. Applying Patches from Patchwork

对于开发者来说，Buildroot的Patchwork网站的主要用途是将补丁导入本地git仓库以进行测试。

当在补丁管理界面中浏览补丁时，在页面顶部会提供一个`mbox`链接。复制此链接地址并运行以下命令:

```bash
$ git checkout -b <test-branch-name>
$ wget -O - <mbox-url> | git am
```

应用补丁的另一个选择是创建一个bundle。bundle是一组补丁，你可以使用patchwork接口将它们组合在一起。一旦打包文件被创建并被公开，你可以复制打包文件的`mbox`链接并使用上述命令应用打包文件。


## 22.4. Work on items from the TODO list
--------------------------------------

如果您想为Buildroot做出贡献，但不知道从哪里开始，并且您不喜欢上述任何主题，您总是可以处理[Buildroot待办事项清单](http://elinux.org/Buildroot#Todo_list)中的项目。请不要犹豫，首先在邮件列表或IRC上讨论某个项目。一定要编辑wiki以指示您何时开始处理一个项目，这样我们就可以避免重复工作。


## 22.5. Submitting patches
------------------------

### 注意

请不要将补丁附加到bug上，而是将它们发送到邮件列表中。

如果您对Buildroot进行了一些更改，并且希望将它们贡献到Buildroot项目中，请按照以下步骤进行操作。

### 22.5.1. The formatting of a patch

我们希望补丁以特定的方式格式化。这是必要的，可以方便地审查补丁，方便地将它们应用于git仓库，方便地查找历史上发生更改的方式和原因，并使使用`git bisect`来定位问题的来源成为可能。

首先，补丁必须有一个良好的提交消息。提交消息应该以单独的一行开始，一行是改动的简要摘要，以补丁涉及的区域作为前缀。一些好的提交标题的例子:

*   `package/linuxptp: bump version to 2.0`
*   `configs/imx23evk: bump Linux version to 4.19`
*   `package/pkg-generic: postpone evaluation of dependency conditions`
*   `boot/uboot: needs host-{flex,bison}`
*   `support/testing: add python-ubjson tests`

前缀后面的描述应以小写字母开头（即上面示例中的“bump”、“needs”、“postpone”、“add”）。

其次，提交消息的主体应该描述为什么需要进行此更改，如果有必要，还应该给出如何进行更改的详细信息。在编写提交消息时，不仅要考虑评审人员如何阅读它，还要考虑几年后你再次看到这个更改时，你将如何阅读它。

第三，补丁本身应该只做一项更改，但要完成全部更改。两个不相关或弱相关的更改通常应该在两个独立的补丁中完成。这通常意味着一个补丁只影响一个包。如果涉及多个更改，通常仍然可以将它们分成小的补丁，并按特定的顺序应用。小的补丁更容易评审，通常也更容易理解为什么要进行更改。但每个补丁必须是完整的。如果只打了第一个补丁而没有打第二个补丁，则不允许构建失败。这对于之后使用`git bisect`是必要的。

当然，当你进行开发时，你可能会在包之间来回切换，当然不会立即以足够干净的方式提交内容。因此，大多数开发人员都会重写提交的历史记录，以生成一组干净的、适合提交的提交。要做到这一点，你需要使用交互式变基。你可以在[在Pro Git book中](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History)中了解它。有时，使用`git reset --soft origin/master`命令丢弃你的历史记录，并使用`git add -i` 或 `git add -p`选择单独的更改更容易。

最后，应该对补丁进行签名。这可以通过在提交消息的末尾添加`Signed-off-by: Your Real Name <your@email.address>`来实现。如果配置正确，`git commit -s`会帮你完成这个任务。`Signed-off-by`标记意味着您可以在Buildroot许可证下发布补丁(即GPL-2.0+，但包补丁除外，它们具有上游许可证)，并且您可以这样做。详见[开发者原产地证书](http://developercertificate.org/)。

当添加新包时，你应该在单独的补丁中提交每个包。这个补丁应该更新到`package/Config.in`、`Config.in`包文件、`.mk`文件、`.hash`文件、任何初始化脚本和所有包补丁。如果包有许多子选项，有时最好将它们作为单独的后续补丁添加。摘要行应该类似于`<packagename>: new package`。对于简单的包，提交消息的主体可以是空的，也可以包含包的描述(如Configin帮助文本)。如果必须做任何特殊的事情来构建包，也应该在commit消息体中明确解释。

当你把一个包升级到新版本时，你还应该为每个包提交一个单独的补丁。不要忘记更新`.hash`文件，如果它还不存在，请添加它。另外，不要忘记检查`_LICENSE` 和 `_LICENSE_FILES`是否仍然有效。摘要行应该类似于`<packagename>: bump to version <new version>`。如果新版本只包含与现有版本相比的安全更新，摘要应该是`<packagename>: security bump to version <new version>`，并且提交消息体应该显示已固定的CVE编号。如果一些包补丁可以在新版本中删除，则应该明确解释为什么可以删除它们，最好使用上游提交ID。此外，任何其他需要的更改都应该明确说明，例如不再存在或不再需要的配置选项。

如果您有兴趣获得构建失败的通知以及您添加或修改的包的进一步更改，请将自己添加到开发者文件中。这应该在创建或修改包的同一个补丁中完成。更多信息请参阅[开发者文件](#chapter-23-developers-file-and-get-developers)。

Buildroot提供了一个方便的工具来检查你创建或修改的文件中常见的编码风格错误，名为`check-package`(更多信息请参见[18.25.2节，“如何检查编码风格”](#18252-how-to-check-the-coding-style))。

### 22.5.2. Preparing a patch series

从本地git视图中提交的更改开始，在生成补丁集之前，将开发分支重设到上游树的顶部。为此，运行:

```bash
$ git fetch --all --tags
$ git rebase origin/master
```

现在检查你提交的代码样式:

```bash
$ utils/docker-run make check-package
```

现在，您已经准备好生成并提交补丁集。

要生成它，运行:

```bash
$ git format-patch -M -n -s -o outgoing origin/master
```

这将在`outgoing`子目录中生成补丁文件，并自动添加`Signed-off-by`行。

生成补丁文件后，你可以使用你喜欢的文本编辑器在提交之前审查/编辑提交信息。

Buildroot提供了一个方便的工具来知道你的补丁应该发送给谁，名为`get-developers`(更多信息请参阅[第23章，DEVELOPERS文件和get-developers](#chapter-23-developers-file-and-get-developers))。这个工具读取你的补丁并输出适当的`git send-email`命令来使用:

```bash
$ ./utils/get-developers outgoing/*
```

使用`get-developers`的输出来发送你的补丁:

```bash
$ git send-email --to buildroot@buildroot.org --cc bob --cc alice outgoing/*
```

或者，`get-developers -e`可以直接与`git send-email`的`--cc-cmd`参数一起使用，自动抄送受影响的开发者:

```bash
$ git send-email --to buildroot@buildroot.org \
      --cc-cmd './utils/get-developers -e' origin/master
```

`git`可以配置为自动执行此操作:

```bash
$ git config sendemail.to buildroot@buildroot.org
$ git config sendemail.ccCmd "$(pwd)/utils/get-developers -e"
```

然后只需要做:

```bash
$ git send-email origin/master
```

注意，`git`应该配置为使用你的邮件账户。要配置`git`，请参阅`man git-send-email`或谷歌它。

如果你不使用`git send-email`，请确保发布的**补丁不换行**，否则它们将无法轻松应用。在这种情况下，修复你的电子邮件客户端，或者更好的是，学习使用`git send-email`。

### 22.5.3. Cover letter

如果你想在单独的邮件中展示整个补丁集，请在`git format-patch`命令中添加`--cover-letter`(有关更多信息，请参阅`man git-format-patch`)。这将为您的补丁系列的介绍电子邮件生成一个模板。

在以下情况下，求职信可以介绍你提出的修改建议:

*   系列中的大量提交;
*   对项目其余部分的变更产生深刻影响;
*   RFC [4];
*   无论何时你觉得它会帮助你展示你的工作，你的选择，审查过程等。

### 22.5.4. Patches for maintenance branches

当在维护分支上修复bug时，应该先在主分支上修复bug。该补丁的提交日志可能会包含一个提交后的说明，说明受影响的分支:

```bash
package/foo: fix stuff

Signed-off-by: Your Real Name <your@email.address>
---
Backport to: 2020.02.x, 2020.05.x
(2020.08.x not affected as the version was bumped)
```

这些更改将由维护者向后移植到受影响的分支。

然而，有些bug可能只适用于特定的版本，例如，因为它使用的是较旧版本的包。在这种情况下，补丁应该基于维护分支，而补丁主题前缀必须包括维护分支的名称(例如"[patch 2020.02.x]")。这可以通过`git format-patch`标志`--subject-prefix`来实现:

```bash
$ git format-patch --subject-prefix "PATCH 2020.02.x" \
    -M -s -o outgoing origin/2020.02.x
```

然后使用`git send-email`发送补丁，如上所述。

### 22.5.5. Patch revision changelog

当要求改进时，每次提交的新版本应该包括每次提交之间修改的更改日志。请注意，如果你的补丁系列是通过求职信介绍的，除了每次提交的更改日志外，还可以在求职信中添加一个整体的更改日志。重写补丁系列的最佳方式是通过交互式变基:`git rebase -i origin/master`。有关更多信息，请参阅git手册。

当添加到单个提交时，此更改日志会在编辑提交消息时添加。在`Signed-off-by`部分下面，添加`---`和你的变更日志。

尽管审阅者在邮件线程以及[patchwork](https://patchwork.ozlabs.org/project/buildroot/list/)中都可以看到更改日志，但当补丁将被合并时，`git`将自动忽略`---`下面的行。这是预期的行为:更改日志并不意味着永远保存在项目的`git`历史记录中。

以下为推荐布局:

```bash
Patch title: short explanation, max 72 chars

A paragraph that explains the problem, and how it manifests itself. If
the problem is complex, it is OK to add more paragraphs. All paragraphs
should be wrapped at 72 characters.

A paragraph that explains the root cause of the problem. Again, more
than one paragraph is OK.

Finally, one or more paragraphs that explain how the problem is solved.
Don't hesitate to explain complex solutions in detail.

Signed-off-by: John DOE <john.doe@example.net>

---
Changes v2 -> v3:
  - foo bar  (suggested by Jane)
  - bar buz

Changes v1 -> v2:
  - alpha bravo  (suggested by John)
  - charly delta
```

任何补丁版本都应该包含版本号。版本号简单地由字母`v`后跟一个大于或等于2的`integer`组成(即:"PATCH v2"， "PATCH v3"…)

这可以通过使用`git format-patch`选项`--subject-prefix`轻松处理:

```bash
$ git format-patch --subject-prefix "PATCH v4" \
    -M -s -o outgoing origin/master
```

从git 1.8.1版本开始，你可以使用`-v <n>`(其中<n>是版本号):

```bash
$ git format-patch -v4 -M -s -o outgoing origin/master
```

当您提供补丁的新版本时，请在[patchwork](https://patchwork.ozlabs.org/project/buildroot/list/)中标记旧版本为已取代。你需要在[patchwork](https://patchwork.ozlabs.org/project/buildroot/list/)上创建一个帐户，以便能够修改补丁的状态。请注意，您只能更改您自己提交的补丁的状态，这意味着您在[patchwork](https://patchwork.ozlabs.org/project/buildroot/list/)中注册的电子邮件地址应该与您用于向邮件列表发送补丁的电子邮件地址相匹配。

在向邮件列表提交补丁时，你也可以添加`--in-reply-to <message-id>`选项。要回复的邮件id可以在[patchwork](https://patchwork.ozlabs.org/project/buildroot/list/)的"Message Id"标签下找到。**in-reply-to**的优点是patchwork会自动将前一个版本的补丁标记为已取代。


## 22.6. Reporting issues/bugs or getting help
-------------------------------------------

在报告任何问题之前，请检查[邮件列表归档](#chapter-5-community-resources)是否有人已经报告和/或修复了类似的问题。

无论你选择报告bug还是获得帮助，无论是通过在[bug tracker](#chapter-5-community-resources)中打开一个bug，还是通过[向邮件列表发送邮件](#chapter-5-community-resources)，都有很多细节可以提供，以帮助人们重现问题并找到解决方案。

试着把自己想象成是在帮助别人;那样的话，你需要什么?

以下是在这种情况下提供的详细信息:

*   主机(操作系统/发行版)
*   Buildroot的版本
*   构建失败的目标
*   构建失败的包
*   失败的命令及其输出
*   任何你认为相关的信息

此外，你应该添加`.config`文件(或者如果你知道怎么做，一个`defconfig`;参见[9.3节，“存储Buildroot配置”](#93-storing-the-buildroot-configuration))。

如果这些细节太大，不要犹豫，使用pastebin服务。请注意，并不是所有可用的pastebin服务在下载原始粘贴时都会保留unix风格的行终止符。以下pastebin服务是已知的正确工作:- [https://gist.github.com/](https://gist.github.com/) - [http://code.bulix.org/](http://code.bulix.org/)


## 22.7. Using the runtime tests framework
---------------------------------------

Buildroot包含一个基于Python脚本和QEMU运行时执行的运行时测试框架。该框架的目标如下。

*   构建一个定义良好的Buildroot配置
*   可选地，验证构建输出的某些属性
*   可选地，在Qemu下启动构建结果，并验证给定功能是否按预期工作

使用运行时测试框架的入口点是`support/testing/run-tests`工具，它在工具的帮助-h描述中记录了一系列选项。一些常见的选项包括设置下载文件夹、输出文件夹、保留构建输出，对于多个测试用例，您可以为每个测试用例设置JLEVEL。

下面是运行测试用例的示例。

*   第一步，让我们看看所有的测试用例选项是什么。可以通过执行`support/testing/run-tests -l`列出测试用例。这些测试都可以在测试开发期间从控制台单独运行。一次一个，选择性地作为测试子集的一组。

```bash
$ support/testing/run-tests -l
List of tests
test_run (tests.utils.test_check_package.TestCheckPackage)
test_run (tests.toolchain.test_external.TestExternalToolchainBuildrootMusl) ... ok
test_run (tests.toolchain.test_external.TestExternalToolchainBuildrootuClibc) ... ok
test_run (tests.toolchain.test_external.TestExternalToolchainCCache) ... ok
test_run (tests.toolchain.test_external.TestExternalToolchainCtngMusl) ... ok
test_run (tests.toolchain.test_external.TestExternalToolchainLinaroArm) ... ok
test_run (tests.toolchain.test_external.TestExternalToolchainSourceryArmv4) ... ok
test_run (tests.toolchain.test_external.TestExternalToolchainSourceryArmv5) ... ok
test_run (tests.toolchain.test_external.TestExternalToolchainSourceryArmv7) ... ok
[snip]
test_run (tests.init.test_systemd.TestInitSystemSystemdRoFull) ... ok
test_run (tests.init.test_systemd.TestInitSystemSystemdRoIfupdown) ... ok
test_run (tests.init.test_systemd.TestInitSystemSystemdRoNetworkd) ... ok
test_run (tests.init.test_systemd.TestInitSystemSystemdRwFull) ... ok
test_run (tests.init.test_systemd.TestInitSystemSystemdRwIfupdown) ... ok
test_run (tests.init.test_systemd.TestInitSystemSystemdRwNetworkd) ... ok
test_run (tests.init.test_busybox.TestInitSystemBusyboxRo) ... ok
test_run (tests.init.test_busybox.TestInitSystemBusyboxRoNet) ... ok
test_run (tests.init.test_busybox.TestInitSystemBusyboxRw) ... ok
test_run (tests.init.test_busybox.TestInitSystemBusyboxRwNet) ... ok

Ran 157 tests in 0.021s

OK
```

*   然后运行一个测试用例:

```bash
$ support/testing/run-tests -d dl -o output_folder -k tests.init.test_busybox.TestInitSystemBusyboxRw
15:03:26 TestInitSystemBusyboxRw                  Starting
15:03:28 TestInitSystemBusyboxRw                  Building
15:08:18 TestInitSystemBusyboxRw                  Building done
15:08:27 TestInitSystemBusyboxRw                  Cleaning up
.
Ran 1 test in 301.140s

OK
```

标准输出表示测试是否成功。默认情况下，测试的输出文件夹将自动删除，除非将`-k`选项传递给**keep**输出目录。

### 22.7.1. Creating a test case

在Buildroot仓库中，测试框架通过`conf`, `infra` 和 `tests`文件夹组织在顶层的`support/testing/`中。所有的测试用例都在`tests`文件夹下，并被组织在代表测试类别的不同文件夹中。

熟悉如何创建测试用例的最好方法是看一些基本的文件系统`support/testing/tests/fs/`和`support/testing/tests/init/`测试脚本。这些测试很好地展示了基本测试的例子，包括检查构建结果和执行运行时测试。还有其他更高级的情况，使用嵌套的`br2-external`文件夹来提供骨架和额外的包。

创建一个基本的测试用例包括:

*   定义一个继承`infra.basetest.BRTest`的测试类
*   定义测试类的`config`成员，到Buildroot配置中为这个测试用例构建。它可以选择依赖运行时测试基础架构提供的配置片段:`infra.basetest.BASIC_TOOLCHAIN_CONFIG`来获得基本的架构/工具链配置，`infra.basetest.MINIMAL_CONFIG`来不构建任何文件系统。使用`infra.basetest.BASIC_TOOLCHAIN_CONFIG`的优点是提供了匹配的Linux内核映像，它允许在Qemu中引导生成的映像，而无需将构建Linux内核映像作为测试用例的一部分，因此显著减少了测试用例所需的构建时间。
*   实现一个`def test_run(self):`函数来实现在构建完成后运行的实际测试。它们可能是通过使用`run_cmd_on_host()`辅助函数在主机上运行命令来验证构建输出的测试。或者他们可以使用在测试用例中可用的`Emulator`对象`self.emulator`来引导Qemu中生成的系统。例如，`self.emulator.boot()`允许在Qemu中启动系统，`self.emulator.login()`允许登录，`self.emulator.run()`允许在Qemu中运行shell命令。

在创建测试脚本之后，将自己添加到`DEVELOPERS`文件中，成为测试用例的维护者。

### 22.7.2. Debugging a test case

当一个测试用例运行时，`output_folder`将包含以下内容:

```bash
$ ls output_folder/
TestInitSystemBusyboxRw/
TestInitSystemBusyboxRw-build.log
TestInitSystemBusyboxRw-run.log
```

`TestInitSystemBusyboxRw/` 是Buildroot的输出目录，只有在传递了`-k`选项时，它才会被保留。

`TestInitSystemBusyboxRw-build.log` 是Buildroot构建的日志。

`TestInitSystemBusyboxRw-run.log` 是Qemu启动和测试的日志。仅当构建成功且测试用例涉及在Qemu下启动时，此文件才会存在。

如果你想手动运行Qemu对构建结果进行手动测试，`TestInitSystemBusyboxRw-run.log`的前几行包含了要使用的Qemu命令行。

您还可以对`output_folder`中的当前源代码进行修改(例如，用于调试目的)，并重新运行标准的Buildroot make目标(以便重新生成具有新修改的完整图像)，然后重新运行测试。

### 22.7.3. Runtime tests and Gitlab CI

所有运行时测试都由Buildroot Gitlab CI基础架构定期执行，请参阅.gitlab.yml和[https://gitlab.com/buildroot.org/buildroot/-/jobs](https://gitlab.com/buildroot.org/buildroot/-/jobs)。

您还可以使用Gitlab CI来测试新的测试用例，或者验证在Buildroot中进行更改后现有测试是否继续工作。

为了实现这一点，你需要在Gitlab上创建一个Buildroot项目的分支，并能够将分支推送到你在Gitlab上的Buildroot分支。

您推送的分支的名称将决定是否会触发Gitlab CI管道，以及针对哪些测试用例。

在下面的例子中，<name>组件的分支名称是您选择的任意字符串。

*   要触发所有run-test测试用例作业，需要推送一个以`-runtime-tests`结尾的分支:

```bash
$ git push gitlab HEAD:<name>-runtime-tests
```

*   要触发一个或多个测试用例作业，推送一个分支，该分支以完整的测试用例名称(`tests.init.test_busybox.TestInitSystemBusyboxRo`)结尾，或者以某个测试类别的名称(`tests.init.test_busybox`)结尾:

```bash
$ git push gitlab HEAD:<name>-<test case name>
```

运行一个测试的例子:

```bash
$ git push gitlab HEAD:foo-tests.init.test_busybox.TestInitSystemBusyboxRo
```

运行同一组中多个测试的示例:

```bash
$ git push gitlab HEAD:foo-tests.init.test_busybox
$ git push gitlab HEAD:foo-tests.init
```

[4] RFC: (Request for comments) 更改建议



# Chapter 23. DEVELOPERS file and get-developers
----------------------------------------------

主Buildroot目录包含一个名为`DEVELOPERS`的文件，该文件列出了涉及Buildroot各个领域的开发人员。多亏了这个文件，`get-developers`工具允许:

*   通过解析补丁并将修改后的文件与相关开发人员进行匹配，计算出需要发送补丁的开发人员列表。详情请参阅[第22.5节，“提交补丁”](#225-submitting-patches)。
*   找出负责给定架构或包的开发人员，以便在此架构或包上发生构建失败时通知他们。这是通过与Buildroot的自动构建基础架构交互完成的。

我们要求在Buildroot中添加新包、新板或新功能的开发人员在` DEVELOPERS`文件中注册自己。例如，我们希望开发人员贡献一个新包，在他的补丁中包含对`DEVELOPERS`文件的适当修改。

`DEVELOPERS`文件格式在文件本身中有详细的文档说明。

位于`utils/`目录下的`get-developers`工具允许我们将`DEVELOPERS`文件用于各种任务:

*   当传递一个或多个补丁作为命令行参数时，`get-developers`将返回适当的`git send-email`命令。如果传入了`-e`选项，只有电子邮件地址会以适用于`git send-email --cc-cmd`的格式打印出来。
*   当使用`-a <arch>`命令行选项时，`get-developers`将返回负责给定架构的开发人员列表。
*   当使用`-p <package>`命令行选项时，`get-developers`将返回负责给定包的开发人员列表。
*   当使用`-c`命令行选项时，`get-developers`将查看Buildroot仓库中所有版本控制下的文件，并列出那些没有被任何开发人员处理过的文件。这个选项的目的是帮助完成`DEVELOPERS`文件。
*   当使用`-v`命令行选项时，它会验证开发者文件的完整性，并对不匹配的项发出警告。



# Chapter 24. Release Engineering
-------------------------------

## 24.1. Releases
--------------

Buildroot项目每季度发布一次，每月发布一次bug修复版本。每年的第一个版本是一个长期支持版本LTS。

*   季度发布:2020.02、2020.05、2020.08和2020.11
*   bug修复版本:2020.02.1,2020.02.2，…
*   LTS版本:2020.02,2021.02，…

该版本支持到下一个版本的第一个bug修复版本，例如，2020.05.x在2020.08.1发布时是EOL。

支持LTS版本，直到下一个LTS的第一个bug修复版本发布，例如2020.02.x，直到2021.02.1发布。


## 24.2. Development
-----------------

每个发布周期包括两个月的“主”分支开发和发布前一个月的稳定。在此阶段，没有新功能添加到`master`，只有bug修复。

稳定阶段从标记`-rc1`开始，直到发布之前，每周都会标记另一个候选发布版本。

为了处理稳定阶段的新功能和版本变化，可以为这些功能创建一个`next`分支。一旦创建了当前版本，`next`分支将被合并到`master`分支中，并继续进行下一个版本的开发周期。



Part IV. Appendix
=================

# Chapter 25. Makedev syntax documentation
----------------------------------------

makedev语法在Buildroot的几个地方被用来定义权限要做的更改，或者创建哪些设备文件以及如何创建它们，以避免调用mknod。

这种语法源自makedev实用工具，更完整的文档可以在`package/makedevs/README`文件中找到。

它采用空格分隔字段列表的形式，每行一个文件;这些字段包括:

|      |      |      |     |     |       |       |       |     |       |
| ---- | ---- | ---- | --- | --- | ----- | ----- | ----- | --- | ----- |
| name | type | mode | uid | gid | major | minor | start | inc | count |

以下是一些重要的块。

*   `name`是你要创建/修改的文件的路径
*   `type`是文件的类型，是:
    
    *   f: 普通文件
    *   d: 一个目录
    *   r: 递归地创建一个目录
    *   c: 字符设备文件
    *   b: 一个块设备文件
    *   p: 一个命名管道
    
*   `mode`是常见的权限设置(只允许数值)
*   `uid`和`gid`是要在这个文件上设置的UID和GID;可以是数值或实际名称
*   `major`和`minor`用于设备文件，设置为`-`用于其他文件
*   `start`、`inc`和`count`是用来创建一批文件的，可以简化为一个循环，从`start`开始，递增`inc`直到到达`count`。

假设你想要改变一个给定文件的权限;使用这种语法，你需要编写:

```bash
/usr/bin/foo f 755 0 0 - - - - -
/usr/bin/bar f 755 root root - - - - -
/data/buz f 644 buz-user buz-group - - - - -
```

或者，如果您想递归地更改目录的所有者/权限，您可以编写(将UID设置为foo, GID设置为bar，并将访问权限设置为rwxr-x---对于目录/usr/share/myapp和它下面的所有文件和目录):

```bash
/usr/share/myapp r 750 foo bar - - - - -
```

另一方面，如果您想为分区创建设备文件`/dev/hda`和相应的15个文件，则需要`/dev/hda`:

```bash
/dev/hda b 640 root root 3 0 0 0 -
```

然后对于`/dev/hda`, `/dev/hdaX`, `X`中1到15之间的分区对应的设备文件:

```bash
/dev/hda b 640 root root 3 1 1 1 15
```

如果启用了`BR2_ROOTFS_DEVICE_TABLE_SUPPORTS_EXTENDED_ATTRIBUTES`，则支持扩展属性。这是通过在描述文件的行之后添加以`|xattr`开头的行来实现的。目前，只有capability作为扩展属性被支持。

|       |            |
| ----- | ---------- |
| xattr | capability |

*   `|xattr`是一个表示扩展属性的“标志”
*   `capability`是添加到前一个文件的能力

如果你想把cap_sys_admin能力添加到二进制文件foo中，你可以这样写:

```bash
/usr/bin/foo f 755 root root - - - - -
|xattr cap_sys_admin+eip
```

您可以通过使用多个`|xattr`行来向文件添加多个功能。如果你想把cap_sys_admin和cap_net_admin能力添加到二进制文件foo中，你可以这样写:

```bash
/usr/bin/foo f 755 root root - - - - -
|xattr cap_sys_admin+eip
|xattr cap_net_admin+eip
```



# Chapter 26. Makeusers syntax documentation
------------------------------------------

创建用户的语法受上面的makedev语法的启发，但特定于Buildroot。

添加用户的语法是用空格分隔的字段列表，每行一个用户;这些字段包括:

|          |     |       |     |          |      |       |        |         |
| -------- | --- | ----- | --- | -------- | ---- | ----- | ------ | ------- |
| username | uid | group | gid | password | home | shell | groups | comment |

地点:

*   `username`是用户期望的用户名(即登录名)。它不能是`root`，并且必须是唯一的。如果设置为`-`，则只会创建一个组。
*   `uid`是用户想要的UID。它必须是唯一的，不能为`0`。如果设置为`-1`或`-2`，那么Buildroot将计算一个唯一的UID， `-1`表示系统UID从[100…999]，`-2`表示用户UID从[1000…1999]。
*   `group`是用户主组所需的名称。它不能是`root`。如果该组不存在，则创建该组。
*   `gid`是用户主组所需的gid。它必须是唯一的，不能为`0`。如果设置为`-1`或`-2`，并且组不存在，那么Buildroot将计算唯一的GID，其中`-1`表示从[100…999]到系统GID， `-2`表示从[1000…1999]到用户GID。
*   `password`是crypt(3)编码的密码。如果以`!`，则禁用登录。如果前缀为`=`，则将其解释为明文，并使用MD5进行密码编码。如果以`!=`，那么密码将被加密(使用MD5)，并且登录将被禁用。如果设置为`*`，则不允许登录。如果设置为`-`，则不会设置密码值。
*   `home`是用户想要的主目录。如果设置为-，则不会创建主目录，用户的主目录将是`/`。不允许明确地将`home`设置为`/`。
*   `shell`是用户想要的shell。如果设置为`-`，则`/bin/false`被设置为用户的shell。
*   `groups`是一个逗号分隔的额外组列表，用户应该属于其中的一部分。如果设置为`-`，则用户将不属于其他组。缺失的组将使用任意的`gid`创建。
*   `comment`(又名[GECOS](https://en.wikipedia.org/wiki/Gecos_field)字段)是一种几乎自由格式的文本。

对每个字段的内容有一些限制。

*   除了`comment`，所有字段都是强制的。
*   除了`comment`，字段不能包含空格。
*   任何字段都不能包含冒号(':')。

如果`home`不是`-`，那么主目录和下面的所有文件都将属于用户及其主组。

示例:

```bash
foo -1 bar -1 !=blabla /home/foo /bin/sh alpha,bravo Foo user
```

这将创建这个用户:

*   `username`(也就是登录名)是:`foo`
*   `uid`由Buildroot计算
*   主`group`是:`bar`
*   主组`gid`由Buildroot计算
*   明文`password`是:`blabla`，将是crypt(3)编码，并禁用登录。
*   `home`是: `/home/foo`
*   `shell`是: `/bin/sh`
*   `foo`也是`groups`的成员:`alpha`和`bravo`
*   `comment`是: `Foo user`

```bash
test 8000 wheel -1 = - /bin/sh - Test user
```

这将创建这个用户:

*   `username`(也就是登录名)是:`test`
*   `uid`是: `8000`
*   主`group`是: `wheel`
*   主组`gid`由Buildroot计算，并将使用rootfs框架中定义的值
*   `password`为空(即没有密码)。
*   `home`是`/`，但不属于`test`
*   `shell`是: `/bin/sh`
*   `test`不是任何其他`groups`的成员。
*   `comment`是: `Test user`


## 26.1. Caveat with automatic UIDs and GIDs
-----------------------------------------

当更新buildroot或在配置中添加或删除包时，自动UID和GID可能会发生更改。如果持久化文件是由该用户或组创建的，这可能会出现问题:升级后，它们会突然有不同的所有者。

因此，建议保留自动ID。这可以通过添加一个包含生成的ID的users表来实现。它只需要为实际创建持久文件的UID执行此操作，例如数据库。



# Chapter 27. Migrating from older Buildroot versions
---------------------------------------------------

有些版本引入了向后不兼容性。本节将解释这些不兼容性，并针对每个不兼容性说明如何完成迁移。


## 27.1. General approach
----------------------

要从旧的Buildroot版本迁移，请执行以下步骤。

1.  对于所有配置，请在旧的Buildroot环境中执行构建。运行`make graph-size`。将`graphs/file-size-stats.csv`保存到不同的位置。运行`make clean`删除其余的。
2.  请查看下面的迁移说明，并对外部包和自定义构建脚本进行必要的调整。
3.  更新Buildroot。
4.  从现有的`.config`运行`make menuconfig`。
5.  如果在Legacy菜单中启用了任何功能，请选中它的帮助文本，取消选中它，并保存配置。
6.  要了解更多细节，请查看git提交消息，查找你需要的包。切换到`packages`目录并运行`git log <old version>.. — <your packages>`。
7.  在新的Buildroot环境中构建。
8.  修复外部包的构建问题(通常是由于更新的依赖项)。
9.  运行`make graph-size`.
10.  将新的`file-size-stats.csv`与原始文件进行比较，检查是否没有必需的文件消失，是否没有新的不需要的大文件出现。
11.  对于覆盖Buildroot创建的文件的自定义覆盖中的配置(和其他)文件，请检查Buildroot生成的文件中是否有需要传播到您的自定义文件中的更改。


## 27.2. Migrating to 2016.11
--------------------------

在Buildroot 2016.11之前，一次只能使用一个br2-external树。随着Buildroot 2016.11的出现，可以同时使用多个自定义项(详情请参见[9.2节，“在Buildroot之外保留自定义”](#92-keeping-customizations-outside-of-buildroot))。

但这意味着较旧的br2-external树不能按原样使用。需要做一个小的更改:向br2-external树添加一个名称。

这可以通过几个步骤非常简单地完成:

*   首先，在br2-external树的根目录下创建一个名为`external.desc`的新文件，用一行代码定义br2-external树的名称:
    
```bash
$ echo 'name: NAME_OF_YOUR_TREE' >external.desc
```
    
**注意.** 在选择名称时要小心:它必须是唯一的，并且只能由集合`[A-Za-z0-9_]`中的ASCII字符组成。
    
*   然后，用新变量修改br2-external树中出现的每一个`BR2_EXTERNAL`:
    
```bash
$ find . -type f | xargs sed -i 's/BR2_EXTERNAL/BR2_EXTERNAL_NAME_OF_YOUR_TREE_PATH/g'
```

现在，你的br2-external树可以与Buildroot 2016.11一起使用。

**注意:** 这一更改使您的br2-external树在2016.11之前与Buildroot不兼容。


## 27.3. Migrating to 2017.08
--------------------------

在Buildroot 2017.08之前，主机包安装在`$(HOST_DIR)/usr`中(例如autotools `--prefix=$(HOST_DIR)/usr`)。在Buildroot 2017.08中，它们现在直接安装在`$(HOST_DIR)`中。

每当一个包安装了一个与`$(HOST_DIR)/lib`中的库链接的可执行文件时，它必须有一个RPATH指向该目录。

指向`$(HOST_DIR)/usr/lib`的RPATH不再被接受。
