
# 窗口管理器

窗口管理器(WM)是一种系统软件，它在图形用户界面(GUI)中控制窗口系统中窗口的位置和外观。它可以是桌面环境(DE)的一部分，也可以独立使用。

参考：[Window Manager](https://wiki.archlinux.org/title/window_manager) 。

**注意**:窗口管理器是Xorg独有的。在Wayland上，与窗口管理器等效的称为合成器，因为它们也充当合成窗口管理器


# 1.预览

窗口管理器是X客户端，它控制绘制各种图形应用程序的框架(“窗口”)的外观和行为。它们决定边框、标题栏、大小和调整窗口大小的能力，通常还提供其他功能，
如保留区域用于粘贴dockapps(如Window Maker)，或选项卡窗口(如Fluxbox)。一些窗口管理器甚至与简单的实用程序捆绑在一起，比如启动程序或配置窗口管理器本身的菜单。

扩展窗口管理器提示规范用于允许窗口管理器以标准方式与服务器和其他客户端进行交互。

一些窗口管理器是作为更全面的桌面环境的一部分开发的，通常允许其他提供的应用程序更好地相互交互，为用户提供更一致的体验，并提供桌面图标、字体、工具栏、壁纸或桌面小部件等功能。

其他窗口管理器被设计成独立使用，让用户完全自由地选择要使用的其他应用程序。这允许用户创建更轻量级和定制的环境，根据自己的特定需求量身定制。
“额外的”，如桌面图标、工具栏、壁纸或桌面小部件，如果需要的话，必须添加额外的专用应用程序。

一些独立的窗口管理器也可以用来替换桌面环境的默认窗口管理器，就像一些面向桌面环境的窗口管理器也可以独立使用一样。

在安装窗口管理器之前，需要安装一个功能正常的X服务器。有关详细信息，请参阅Xorg。


## 1.1 类型

- Stacking (aka floating) window managers

堆叠(又名浮动)窗口管理器提供了传统的桌面隐喻，用于商业操作系统，如Windows和macOS。窗户就像桌子上的一张纸，可以堆叠在一起。对于可用的Arch Wiki页面，请参阅类别:堆叠窗口管理器。


- Tiling window managers

平铺窗口管理器“平铺”窗口，以便没有重叠的窗口。它们通常非常广泛地使用键绑定，并且很少(或根本没有)依赖于鼠标。
平铺窗口管理器可以是手动的，提供预定义的布局，或者两者兼而有之。对于可用的Arch Wiki页面，请参阅类别:平铺窗口管理器。


- Dynamic window managers

动态窗口管理器可以在平铺或浮动窗口布局之间动态切换。对于可用的Arch Wiki页面，请参阅类别:动态窗口管理器。


有关窗口管理器的比较，请参阅平铺窗口管理器的比较<https://wiki.archlinux.org/title/Comparison_of_tiling_window_managers>和Wikipedia: X窗口管理器的比较<https://en.wikipedia.org/wiki/Comparison_of_X_window_managers>。


# 2.窗口管理器列表

## 2.1 堆叠窗口管理器

- 2bwm

    - 快速浮动窗口管理器，具有2个边框的特点，在XCB库上编写，派生自Michael Cardell编写的mcwm。在2bwm中，一切都可以通过键盘访问，但可以使用指向设备来移动，调整大小和升高/降低。
    - [2bwm](https://github.com/venam/2bwm)

- 9wm

    - X11窗口管理器的灵感来自Plan 9的rio。
    - [9wm](https://github.com/9wm/9wm)

- AfterStep

    - 最初基于NeXTStep界面的外观和感觉，它为最终用户提供了一致、干净和优雅的桌面。AfterStep开发的目标是提供桌面配置的灵活性、改进美观性和有效地使用系统资源。
    - [afterstep-git](http://www.afterstep.org/)

- berry

    - 用c编写的小窗口管理器通过命令行客户端进行控制，允许用户通过热键守护进程(如sxhkd)控制窗口或通过shell脚本扩展功能。它提供了双边框、标题栏和窗口文本的可扩展主题选项。Berry将新窗口放置在未占用的空间中，并支持虚拟桌面。
    - [berry-git](https://berrywm.org/)

- Blackbox

    - 快速、轻量级的X窗口管理器，没有那些烦人的库依赖。Blackbox是用c++构建的，包含完全原创的代码(尽管图形实现类似于WindowMaker)。
    - [blackbox](http://blackboxwm.sourceforge.net/)

- Compiz

    - OpenGL合成管理器，使用GLX_EXT_texture_from_pixmap将重定向的顶层窗口绑定到纹理对象。它有一个灵活的插件系统，可以在大多数图形硬件上很好地运行。
    - [compiz, compiz-core](https://launchpad.net/compiz)

- cwm

    - cwm最初源于evilwm，但后来从零开始重写，cwm的目标是简单，并提供有用的功能，如搜索窗口。
    - [cwm](https://github.com/chneukirchen/cwm)

- eggwm

    - 轻量级的QT4/QT5窗口管理器。
    - [eggwm](https://aur.archlinux.org/packages/eggwm-qt5)

- Enlightenment

    - Enlightenment不仅是Linux/X11和其他工具的窗口管理器，而且还是一套库，可以帮助您创建漂亮的用户界面，比使用老式方法和与传统工具包进行斗争要少得多，更不用说传统的窗口管理器了。
    - [enlightenment](https://www.enlightenment.org/)

- evilwm

    - X窗口系统的极简窗口管理器。这里的“极简主义”并不意味着它太过简单而无法使用，而是意味着它省略了许多使其他窗口管理器无法使用的东西。
    - [evilwm](https://www.6809.org.uk/evilwm/)

- Fluxbox

    - 基于Blackbox 0.61.1代码的X的窗口管理器。它是非常轻的资源和易于处理，但却充满了功能，使一个简单和极快的桌面体验。它是使用c++构建的，并在MIT许可下获得许可。
    - [fluxbox](https://github.com/fluxbox/fluxbox)

- Flwm

    - 尝试将来自多个窗口管理器的最佳想法结合起来。主要的影响和代码库来自Chris Cannam的wm2。
    - [flwm](http://flwm.sourceforge.net/)

- FVWM

    - 强大的icccm兼容的多虚拟桌面窗口管理器的X窗口系统。开发是积极的，支持是优秀的。
    - [fvwm](https://www.fvwm.org/)

- Gala

    - 一个美丽的窗口管理器从elementaryos，Pantheon的一部分。也可以作为一个合成管理器，基于libmutter。
    - [gala](https://launchpad.net/gala)

- Goomwwm

    - X11窗口管理器作为洁净室软件项目在C语言中实现。它以最小的浮动布局管理窗口，同时为窗口切换、大小调整、移动、标记和平铺提供灵活的键盘驱动控件。它还具有快速、轻量级、非模态、xinerama感知以及尽可能兼容EWMH的特性。
    - [goomwwm](https://github.com/seanpringle/goomwwm)

- IceWM

    - IceWM的目标是速度快、简单、不妨碍用户。
    - [icewm](https://ice-wm.org/)

- jbwm

    - jbwm基于evilwm，其最小配置大小约为16kb，专注于小二进制文件大小和可用性，将可选标题栏和XFT标题栏字体呈现作为编译时选项，jbwm还具有比evilwm更易于使用的键绑定特性。
    - [jbwm](https://github.com/jefbed/jbwm)

- JWM

    - 用于x的窗口管理器。JWM是用C编写的，至少只使用Xlib。
    - [jwm](https://joewing.net/projects/jwm/)

- Karmen

    - X的窗口管理器，由Johan Veenhuizen编写。它被设计成“只是工作”。除了Xlib之外，没有配置文件和库依赖项。输入焦点模型是点击焦点。卡门的目标是ICCCM和EWMH合规。
    - [karmen](http://karmen.sourceforge.net/)

- KWin

    - 自KDE 4.0以来的标准KDE窗口管理器，它也是一个合成管理器。这允许KWin提供高级的图形效果，类似于Compiz，同时还提供以前KDE版本的所有特性(例如与KDE的其余部分非常好的集成，高级可配置性，焦点窃取预防，经过良好测试的窗口管理器，对行为不端的应用程序/工具包的健壮处理，等等)。同时也是韦兰的合成师。
    - [kwin](https://techbase.kde.org/Projects/KWin)

- lwm

    - X的窗口管理器，它试图避开你的脸。没有图标，没有按钮栏，没有图标底座，没有根菜单，什么都没有:如果你想要所有这些，那么其他程序可以提供。也没有可配置性:如果你想要，你需要一个不同的窗口管理器;一个帮助你的操作系统在它的邪恶征服你的磁盘空间和兼并你的物理内存。
    - [lwm](http://www.jfc.org.uk/software/lwm.html)

- Marco

    - MATE窗口管理器，Metacity的分支。
    - [marco](https://github.com/mate-desktop/marco)

- Metacity

    - 这个窗口管理器力求安静、小巧、稳定，继续工作，不引起您的注意。它被遗留的GNOME 2和GNOME闪回会话所使用，并被Mutter所取代。
    - [metacity](https://blogs.gnome.org/metacity/)

- MLVWM

    - 类似Macintosh的虚拟窗口管理器，一个FVWM的后代，它试图在布局和窗口设计上模仿mac OS X之前的Macintosh外观和感觉。
    - [mlvwm](http://www2u.biglobe.ne.jp/~y-miyata/mlvwm.html)

- Muffin

    - Cinnamon的窗口和合成管理器。Mutter的一个分支，基于Clutter，使用OpenGL。它不能在Cinnamon之外使用。
    - [muffin](https://github.com/linuxmint/muffin/)

- Mutter

    - GNOME的窗口和合成管理器，基于Clutter，使用OpenGL。也服务于韦兰合成器。
    - [mutter](https://gitlab.gnome.org/GNOME/mutter/)

- MWM

    - Motif窗口管理器(MWM)是一个基于Motif工具包的X窗口管理器。
    - [openmotif](https://sourceforge.net/projects/motif/)

- Openbox

    - 具有广泛标准支持的高度可配置窗口管理器。盒子的视觉风格以其简约的外观而闻名。Openbox使用box视觉风格，同时为主题开发人员提供比以前的box实现更多的选项。自2010年以来已经“功能完备”，但仍在继续维护。
    - [openbox](http://openbox.org/)

- pawm

    - X窗口系统的窗口管理器。所以它不是一个“桌面”，也不会给你提供一大堆无用的选项，只是运行你的X应用程序所需的设施，同时拥有一个友好易用的界面。
    - [pawm](https://www.pleyades.net/david/projects/pawm)

- PekWM

    - 窗口管理器曾经是基于ew++的窗口管理器，但是它已经进化到完全不像ew++了。它有一个非常扩展的功能集，包括窗口分组(类似于Ion, PWM或Fluxbox)，自动属性，Xinerama，支持钥匙链的键抓取器等等。
    - [pekwm](https://www.pekwm.org/)

- Sawfish

    - 使用基于lisp的脚本语言的可扩展窗口管理器。与大多数窗口管理器相比，它的策略非常小。它的目标只是尽可能以最灵活和最吸引人的方式管理窗口。所有高级窗口管理器函数都是用Lisp实现的，以便将来进行扩展或重新定义。
    - [sawfish](https://sawfish.wikia.com/wiki/Main_Page)

- sowm

    - 简单固执的窗口管理器，提供全屏切换，窗口居中和混合工作流(即鼠标和键盘)。
    - [sowm](https://github.com/dylanaraps/sowm)

- TinyWM

    - 作为极简主义的练习而创建的小窗口管理器。学习创建窗口管理器的一些基本知识可能会有所帮助。它由大约50行c语言组成，还有一个使用Python -xlib的Python版本。
    - [tinywm](http://incise.org/tinywm.html)

- twm

    - X的简单窗口管理器，Xorg自1989年以来使用的默认/回退。它提供了标题栏、形状窗口、几种形式的图标管理、用户定义的宏函数、点击输入和指针驱动的键盘焦点，以及用户指定的键和指针按钮绑定。
    - [xorg-twm](https://gitlab.freedesktop.org/xorg/app/twm/)

- ukwm

    - 一个轻量级的GTK+窗口管理器，UKUI桌面环境的默认窗口管理器。
    - [ukwm](https://github.com/ukui/ukwm/)

- UWM

    - UDE的终极窗口管理器。
    - [ude](http://udeproject.sourceforge.net/)

- Wind

    - 小浮动窗口管理器。尽量符合ICCCM, EHWM, NetWM。
    - [windwm](http://windwm.sourceforge.net/)

- WindowLab

    - 小巧简单的窗口管理器，设计新颖。它有一个点击焦点但不提高焦点的策略，一个窗口大小调整机制，允许在一个操作中改变窗口的一个或多个边缘，以及一个创新的菜单栏，与任务栏共享屏幕的同一部分。通过限制鼠标指针，可以防止窗口标题栏偏离屏幕边缘，并且在适当的时候，还可以将指针限制在任务栏/菜单栏上，以便使目标菜单项更容易点击。
    - [windowlab](https://github.com/nickgravgaard/windowlab)

- Window Maker

    - X11窗口管理器最初设计用于为GNUstep桌面环境提供集成支持。在所有可能的方面，它都再现了NEXTSTEP用户界面的优雅外观和感觉。它快速，功能丰富，易于配置，易于使用。
    - [windowmaker](https://windowmaker.org/)

- WM2

    - 它提供了一种不同寻常的窗口装饰风格，并且它的作者在窗口管理器中感到舒适的功能很少。Wm2是不可配置的，除非通过编辑源代码和重新编译代码，它实际上是为那些不特别希望他们的窗口管理器过于友好的人设计的。
    - [wm2](https://www.all-day-breakfast.com/wm2/)

- worm

    - Worm是X11的一个动态的、基于标记的窗口管理器。它支持浮动布局和主堆栈平铺布局，并且是公开开发的。它也是用Nim编程语言编写的。
    - [worm-git](https://github.com/codic12/worm)

- Xfwm

    - Xfce窗口管理器管理应用程序窗口在屏幕上的位置，提供漂亮的窗口装饰，管理工作区或虚拟桌面，并原生支持多屏幕模式。它提供了自己的合成管理器(来自X.Org Composite扩展)来实现真正的透明度和阴影。Xfce窗口管理器还包括用于用户特定命令和基本窗口操作的键盘快捷键编辑器，并提供用于高级调整的首选项对话框。
    - [xfwm4](https://docs.xfce.org/xfce/xfwm4/start)


## 2.2 平铺窗口管理器

- Bspwm

    - BSPWM是一个平铺窗口管理器，它将Windows表示为完整二叉树的叶子。它支持EWMH和多个监视器，并通过消息进行配置和控制。
    - [bspwm](https://github.com/baskerville/bspwm)

- EXWM

    - EXWM (Emacs X窗口管理器)是基于XELB构建的Emacs的全功能平铺X窗口管理器。它具有完全键盘驱动的操作，混合布局模式(平铺和堆叠)，动态工作空间支持，ICCCM/EWMH兼容性，RandR(多显示器)支持和内置系统托盘。
    - [emacs-exwm-git](https://github.com/ch11ng/exwm)

- Herbstluftwm

    - 手动平铺窗口管理器X11使用Xlib和Glib。布局是基于将帧分割成子帧，这些子帧可以再次分割，也可以用窗口填充(类似于i3/ musca)。可以在运行时添加/删除标签(或工作区或虚拟桌面等)。每个标签都包含自己的布局。每个监视器上只显示一个标签。这些标记是独立于监视器的(类似于xmonad)。它是通过herbstclient的ipc调用在运行时配置的。所以配置文件只是一个脚本，在启动时运行。(类似于wmii/musca)。
    - [herbstluftwm](https://herbstluftwm.org)

- i3

    - 平铺窗口管理器，完全从头开始编写。I3的创建是因为当时开发人员最喜欢的窗口管理器wmii没有提供我们想要的一些功能(例如，多显示器做得很好)，有一些bug，在很长一段时间内没有进展，而且根本不容易破解(源代码注释/文档完全缺乏)。值得注意的区别是在多监视器支持和树形比喻方面。为了提高速度，没有实现wmii的Plan 9接口。
    - [i3-wm](https://i3wm.org/)

- Larswm

    - 一个基于9wm的平铺窗口管理器。
    - [larswm](http://porneia.free.fr/larswm/larswm.html)

- LeftWM

    - 一个用Rust编写的平铺窗口管理器。
    - [leftwm](https://leftwm.org)

- Notion

    - 平铺式，选项卡式窗口管理器，用于X窗口系统，使用“平铺式”和“选项卡式”窗口。
    - Tiling: 您将屏幕划分为不重叠的“图块”。 每个窗口占用一个图块，并最大化它
    - Tabbing: 一个图块可能包含多个窗口 - 它们将被“选项卡式”。
    - Static: 大多数平铺窗口管理器都是“动态”的，这意味着它们会随着窗口的出现和消失而自动调整大小并在平铺窗口中移动。 相比之下，Notion 不会自动更改平铺。
    - Notion 是 Ion3 的一个分支。
    - [notion](https://notionwm.net/)

- Ratpoison

    - 简单的窗口管理器，没有臃肿的库依赖，没有花哨的图形，没有窗口装饰，没有啮齿动物依赖。它很大程度上模仿了GNU Screen，后者在虚拟终端市场上创造了奇迹。Ratpoison是用一个简单的文本文件配置的。Ratpoison的信息栏有些不同，因为它只在需要时显示。它既是一个应用程序启动器，也是一个通知栏。鼠毒不包括系统托盘。
    - [ratpoison](https://www.nongnu.org/ratpoison/)

- Stumpwm

    - 平铺，键盘驱动的X11窗口管理器，完全用Common Lisp编写。Stumpwm试图是可定制的，但视觉最小。它确实有各种钩子来附加您的个人定制和变量来调整，并且可以在运行时重新配置和重新加载。没有窗口装饰，没有图标，没有按钮，也没有系统托盘。它的信息栏可以设置为持续显示或仅在需要时显示。
    - [stumpwm](https://stumpwm.github.io/)


## 2.3 动态窗口管理器

- awesome

    - 高度可配置，x的下一代框架窗口管理器。它非常快，可扩展，并在GNU GPLv2许可下许可。在Lua中配置，它有一个系统托盘、信息栏和内置的启动器。它有一些用Lua编写的扩展。Awesome使用XCB而不是Xlib，这可能会提高速度。Awesome还有其他一些特性，比如早期替换的通知守护程序，类似于*框窗口管理器的右键菜单，以及许多其他的东西。
    - [awesome](https://awesomewm.org/)

- dwm

    - x的动态窗口管理器。它管理平铺、单片和浮动布局的窗口。所有的布局都可以动态应用，为正在使用的应用程序和执行的任务优化环境。不包括系统托盘或自动启动器，尽管dmenu与它集成得很好，因为它们来自同一作者。它没有文本配置文件。配置完全是通过修改C源代码完成的，每次修改源代码时都必须重新编译和重新启动。
    - [dwm](https://dwm.suckless.org/)

- echinus

    - X11的简单轻量级平铺和浮动窗口管理器。echinus最初是一个配置更简单的dwm分支，现在已成为支持EWMH的全功能重养育窗口管理器。它有一个与ewmh兼容的面板/任务栏，称为ouricoAUR。
    - [echinus](http://plhk.ru)

- FrankenWM

    - 基本上就是浮在水面上的怪物。添加在基本mwm之上的功能包括:更多的布局(斐波那契，相等堆栈，双堆栈)，间隙(和边框)可以动态调整，最小化/最大化单个窗口，隐藏/显示所有窗口，单独调整主和堆栈的大小，反转堆栈。
    - [frankenwm](https://github.com/sulami/FrankenWM)

- spectrwm

    - X11的小动态平铺窗口管理器，很大程度上受到xmonad和dwm的启发。它试图不碍事，这样有价值的屏幕空间就可以用来做更重要的事情。它具有相同的默认值，并使用文本文件进行配置。它是由黑客为黑客编写的，它力求小、紧凑和快速。它有一个内置的状态栏，由用户定义的脚本提供。
    - [spectrwm](https://github.com/conformal/spectrwm/)

- Qtile

    - 全功能，可编程的平铺窗口管理器，用Python编写。Qtile简单、小且可扩展。编写自己的布局、小部件和内置命令很容易。它完全是用Python编写和配置的，这意味着您可以利用该语言的全部功能和灵活性来满足您的需求。
    - [qtile](https://github.com/qtile/qtile)

- Wingo

    - 全功能的真正的混合窗口管理器，支持每个监视器的工作区，无论是浮动还是平铺模式都是经过考虑的。这允许在一个工作空间上平铺，而在另一个工作空间上浮动。Wingo可以用自己的命令语言编写脚本，完全是主题化的，并支持用户定义的钩子。Wingo是用Go语言编写的，没有运行时依赖关系。
    - [wingo-git](https://github.com/BurntSushi/wingo)

- xmonad

    - 动态平铺X11窗口管理器，在Haskell中编写和配置。在正常的WM中，您将花费一半的时间来对齐和搜索窗口。Xmonad通过自动化使工作变得更容易。XMonad是在Haskell中配置的。对于所有配置更改，都必须重新编译xmonad，因此必须安装Haskell编译器(超过100MB)。一个名为xmonad-contrib的大型库提供了许多附加特性。
    - [xmonad](https://xmonad.org/)


# 参考

[The Window Manager Report](https://www.gilesorr.com/wm/)

[What are the best window managers for Linux?](https://www.slant.co/topics/390/~best-window-managers-for-linux)

[A Memory Comparison of Light Linux Desktops](https://l3net.wordpress.com/2013/03/17/a-memory-comparison-of-light-linux-desktops/)

[xwinman.org: Other Window Managers](http://www.xwinman.org/others.php)

