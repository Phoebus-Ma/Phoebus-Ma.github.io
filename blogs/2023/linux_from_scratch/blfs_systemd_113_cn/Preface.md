
> [上一页](blfs_systemd_manual.md) &emsp;&emsp;&emsp;&emsp; [主目录](blfs_systemd_manual.md) &emsp;&emsp;&emsp;&emsp; [下一页](1.Welcome_to_blfs.md)

# Preface
--------

在帮助Linux从零开始一段时间后，我注意到我们收到了许多关于如何在基本LFS系统之外做事情的查询。当时，专门提供的与LFS有关的帮助只有LFS提示 (https://www.linuxfromscratch.org/hints). 大多数LFS提示都写得非常好，但我(和其他人)仍然认为需要更全面的帮助来Beyond LFS -因此是BLFS。

BLFS的目标不仅仅是将lfs提示转换为XML，尽管我们的大部分工作都是基于提示，实际上有些作者既写提示又写相关的BLFS部分。我们希望能够为您提供足够的信息，不仅使您能够按照自己的需要构建系统(无论是web服务器还是多媒体桌面系统)，而且您还可以在此过程中学习到很多关于系统配置的知识。

一如既往地感谢LFS/BLFS社区的每一个人;特别是那些提供指导，书面文本，回答问题，通常在事情出错时大喊大叫的人!

最后，我们鼓励你参与社区活动;在邮件列表或新闻门户上提问，并在Libera上加入#lfs和#lfs-support的乐趣。您可以在本书的 [Introduction](1.Welcome_to_blfs.md) 部分找到有关所有这些的更多详细信息。

Enjoy using BLFS.

    Mark Hymers
    markh <at> linuxfromscratch.org
    BLFS Editor (2001年7月至2003年3月)

我仍然记得我是如何找到BLFS项目并开始使用当时完成的说明的。我简直不敢相信，一个应用程序能够快速启动并运行是多么美妙的一件事，它还解释了为什么要这样做。不幸的是，对我来说，没过多久我就打开了页面上只有“待完成”的应用程序。我做了大多数人都会做的事，我等待别人去做。没过多久，我就开始在Bugzilla中寻找一些容易做的事情。与任何学习经验一样，“容易”的定义也在不断变化。

我们仍然鼓励您参与，因为BLFS从未真正完成。贡献或只是使用，我们希望您享受您的BLFS体验。

    Larry Lawrence
    larry <at> linuxfromscratch.org
    BLFS Editor (2003年3月- 2004年6月)

BLFS项目是LFS的自然发展。总之，这些项目为开源社区提供了独特的资源。他们从世界各地许多有才华的人贡献的源代码中构建一个完整的、功能齐全的软件系统的过程中揭开了神秘的面纱。它们真正允许用户实现“你的发行版，你的规则”的口号。

我们的目标是继续提供最好的可用资源，向您展示如何集成许多重要的开源应用程序。由于这些应用程序不断更新和开发新的应用程序，本书将永远不会完成。此外，在解释如何安装不同软件包的细微差别方面总是有改进的余地。为了做出这些改进，我们需要您的反馈。我鼓励您参与不同的邮件列表、新闻组和IRC频道，以帮助实现这些目标。

    Bruce Dubbs
    bdubbs <at> linuxfromscratch.org
    BLFS Editor (2004年6月至2006年12月，2011年2月至今)

我对[B]LFS项目的介绍实际上是偶然的。我试图使用在网上找到的一些操作指南和其他信息来构建一个GNOME环境。有几次，我遇到了一些构建问题，并在谷歌上搜索了一些旧的BLFS邮件列表消息。出于好奇，我访问了Linux From Scratch网站，不久之后就被吸引住了。从那以后，我再也没有使用过任何其他的Linux发行版。

我不能保证任何人都能感受到我在使用[B]LFS指令构建我的最初几个系统后所感受到的满足感，但我真诚地希望您的BLFS体验对您和对我一样有益。

BLFS项目在过去几年中有了显著的发展。有比以往更多的包指令和相关依赖。这个项目需要你的投入才能持续成功。如果你发现你喜欢建立BLFS，请考虑以任何方式帮助。BLFS需要数百小时的维护才能保持半电流。如果您对自己的编辑技能有足够的信心，请考虑加入BLFS团队。只要在邮件列表讨论中提供合理的建议和/或为本书的XML提供补丁，您就可能收到加入团队的邀请。

    Randy McMurchy
    randy <at> linuxfromscratch.org
    BLFS Editor (December 2006–January 2011)


## Foreword

本书的这个版本是为了在使用LFS书构建的系统之上构建时使用。我们已尽一切努力确保说明书的准确性和可靠性。许多人发现，在构建当前稳定或开发版本的LFS之后，使用本书中的说明可以提供一个稳定且非常现代的Linux系统。

Enjoy!

    Randy McMurchy
    2008年8月24日


## Who Would Want to Read this Book

这本书主要是针对那些基于LFS书建立了一个系统的人。对于那些正在使用其他发行版的人，以及由于这样或那样的原因想要手动构建软件并需要一些帮助的人来说，它也很有用。注意，本书中的材料，特别是依赖项清单，假设您正在使用一个基本的LFS系统，并且LFS书中列出的每个包都已经安装和配置。BLFS可用于创建一系列不同的系统，因此目标受众可能与LFS这本书一样广泛。如果你觉得LFS很有用，你也应该喜欢这个!

从7.4版开始，BLFS图书版本与LFS图书版本相匹配。这本书可能与LFS书的以前或以后的版本不兼容。


## Organization

本书分为以下十四个部分。

**Part I - Introduction**

    这一部分包含了理解本书其余部分所需要的基本信息。

**Part II - Post LFS Configuration and Extra Software**

    这里我们将介绍基本配置和安全问题。我们还讨论了LFS主书中没有涉及的一系列文本编辑器、文件系统和shell。

**Part III - General Libraries and Utilities**

    在本节中，我们将介绍全书中经常需要的库以及系统实用程序。关于编程的信息(包括重新编译GCC以支持它的所有语言)结束了这一部分。

**Part IV - Basic Networking**

    在这里，我们将解释在不使用LFS主书中介绍的简单静态IP设置时如何连接到网络。本文还介绍了网络库和命令行网络工具。

**Part V - Servers**

    这里我们向您展示如何设置邮件和其他服务器(如FTP、Apache等)。

**Part VI - X + Window Managers**

    这一部分解释了如何设置一个基本的X窗口系统，以及一些通用的X库和窗口管理器。

**Part VII - KDE**

    这一部分是为那些想要使用K桌面环境或它的一部分的人准备的。

**Part VIII - GNOME**

    GNOME是桌面环境领域中KDE的主要替代品。

**Part IX - Xfce**

    Xfce是GNOME和KDE的轻量级替代品。

**Part X - LXDE**

    LXDE是GNOME和KDE的另一种轻量级替代品。

**Part XI - More X Software**

    办公软件和图形网页浏览器对大多数人来说都很重要。它们以及一些通用的X software都可以在本书的这一部分找到。

**Part XII - Multimedia**

    这里我们将介绍多媒体库和驱动程序，以及一些音频、视频和cd写入程序。

**Part XIII - Printing, Scanning and Typesetting (PST)**

    本部分介绍文档处理，从Ghostscript、CUPS和DocBook等应用程序一直到texlive。

**Appendices**

    附录提供了不属于正文的信息;它们被列为参考资料。缩略词的词汇表是一个方便的功能。

> [上一页](blfs_systemd_manual.md) &emsp;&emsp;&emsp;&emsp; [主目录](blfs_systemd_manual.md) &emsp;&emsp;&emsp;&emsp; [下一页](1.Welcome_to_blfs.md)
