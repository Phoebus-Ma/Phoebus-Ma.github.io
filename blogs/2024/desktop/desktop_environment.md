
# 桌面环境
------------

桌面环境（DE）是桌面比拟的一个实现，由一捆程序组成，它们共享一个共同的图形用户界面（GUI）。

参考：[Desktop environment](https://wiki.archlinux.org/title/Desktop_environment) 。


# 1.概况
------------

桌面环境将各种组件捆绑在一起，以提供常见的图形用户界面元素，如图标、工具栏、壁纸和桌面小部件。此外，大多数桌面环境包括一套集成的应用程序和实用程序。
最重要的是，桌面环境提供了他们自己的窗口管理器，然而通常可以用另一个兼容的窗口管理器来代替。

用户可以自由地以任何方式配置他们的GUI环境。桌面环境只是提供了一个完成这一任务的完整而方便的手段。请注意，用户可以自由地混合和匹配来自多个桌面环境的应用程序。
例如，一个 KDE 用户可以安装和运行 GNOME 应用程序，如 Epiphany 网络浏览器，如果他们喜欢它而不是 KDE 的 Konqueror 网络浏览器。这种方法的一个缺点是，
]许多由桌面环境项目提供的应用程序在很大程度上依赖于各自桌面环境的基础库。因此，从一系列的桌面环境中安装应用程序将需要安装更多的依赖项。
寻求节约磁盘空间的用户通常会避免这种混合环境，或者选择只依赖少数外部库的替代品。

此外，由桌面环境提供的应用程序往往能更好地与他们的本地环境整合。从表面上看，使用不同部件工具包的混合环境会导致视觉上的差异（也就是说，界面会使用不同的图标和部件样式）。
在可用性方面，混合环境的行为可能并不相似（例如，单击与双击图标；拖放功能），因此可能会造成混乱或意外的行为。


# 2.桌面环境列表
------------

## 2.1 官方支持

- Budgie

    - Budgie是一个为现代用户设计的桌面环境，它注重简单和优雅。
    - [budgie](https://blog.buddiesofbudgie.org)

- Cinnamon

    - Cinnamon致力于提供传统的用户体验。Cinnamon是GNOME 3的一个分支。
    - [cinnamon](https://github.com/linuxmint/Cinnamon)

- Cutefish

    - Cutefish是一个全新的现代桌面环境。
    - [cutefish](https://cutefish-ubuntu.github.io)

- Deepin Desktop Environment

    - Deepin桌面界面和应用程序具有直观和优雅的设计。四处走动、分享和搜索等已经成为一种简单的快乐体验。
    - [deepin](https://www.deepin.org/)

- Enlightenment

    - Enlightenment桌面shell提供了一个基于Enlightenment Foundation Libraries的高效窗口管理器，以及其他必要的桌面组件，如文件管理器、桌面图标和小部件。它支持主题，同时仍然能够在较旧的硬件或嵌入式设备上运行。
    - [enlightenment](https://www.enlightenment.org/)

- GNOME

    - GNOME桌面环境是一个既具有现代(GNOME)会话又具有经典(GNOME classic)会话的有吸引力且直观的桌面。
    - [gnome](https://www.gnome.org/)

- GNOME Flashback

    - GNOME Flashback是GNOME 3的shell，它最初被称为GNOME回退模式。桌面布局和底层技术类似于GNOME 2。
    - [gnome-flashback](https://wiki.gnome.org/Projects/GnomeFlashback)

- KDE Plasma

    - KDE Plasma桌面环境是一个熟悉的工作环境。Plasma提供了现代桌面计算体验所需的所有工具，因此您可以从一开始就高效工作。
    - [plasma](https://www.kde.org/plasma-desktop)

- LXDE

    - 轻量级X11桌面环境是一个快速和节能的桌面环境。它有一个现代的界面，多语言支持，标准的键盘快捷键和附加功能，如标签文件浏览。LXDE从根本上被设计为轻量级的，它力求比其他环境占用更少的CPU和RAM。
    - [GTK 2: lxde, GTK 3: lxde-gtk3](https://lxde.org/)

- LXQt

    - LXQt是Qt的移植版本，也是LXDE(轻量级桌面环境)即将推出的版本。它是LXDE-Qt和Razor-qt项目合并的产物:一个轻量级、模块化、快速和用户友好的桌面环境。
    - [lxqt](https://lxqt.github.io/)

- MATE

    - Mate使用传统的隐喻为Linux用户提供了一个直观而有吸引力的桌面。MATE最初是GNOME 2的一个分支，但现在使用GTK 3。
    - [mate](https://mate-desktop.org/)

- Sugar

    - 糖学习平台是一个由活动组成的计算机环境，旨在帮助5至12岁的儿童通过富媒体表达共同学习。糖是全世界为每个孩子提供优质教育机会的努力的核心组成部分，目前全世界有40多个国家的近100万儿童使用糖，讲25种语言。糖提供了一种手段，帮助人们通过获得高质量的教育过上充实的生活，而目前有许多人错过了这种教育。
    - [sugar + sugar-fructose](https://sugarlabs.org/)

- UKUI

    - UKUI是一个轻量级的Linux桌面环境，基于GTK和Qt开发，UKUI是Ubuntu麒麟的默认桌面环境。
    - [ukui](https://www.ukui.org/)

- Xfce

    - Xfce体现了模块化和可重用性的传统UNIX哲学。它由许多组件组成，这些组件提供了现代桌面环境的全部功能，同时保持相对轻量级。它们是单独打包的，您可以在可用的软件包中进行选择，以创建最佳的个人工作环境。
    - [xfce4](https://xfce.org/)


## 2.2 非官方支持

- CDE

    - 通用桌面环境(CDE, Common Desktop Environment)是一个基于Motif小部件工具包的Unix和OpenVMS桌面环境。它是UNIX98工作站产品标准的一部分，长期以来一直是与商业Unix工作站相关联的“经典”Unix桌面。尽管它是一个遗留环境，但它仍然保持了对Linux系统的支持。
    - [cdesktopenv](https://sourceforge.net/projects/cdesktopenv/)

- COSMIC

    - COSMIC是一个基于Rust的桌面环境，由System76为Pop!操作系统，灵感来自高度定制的GNOME。第一个版本还没有完成，所以要做好收尾工作。
    - [cosmic-epoch-git](https://github.com/pop-os/cosmic-epoch)

- EDE

    - “Equinox桌面环境”是一个桌面环境，设计简单，非常轻量和快速。
    - [ede](https://edeproject.org/)

- KDE 1

    - “前向移植”在现代系统上工作。主要是为了怀旧，但现在实际工作，可以作为日常司机。AUR中也有越来越多的KDE 1应用程序。
    - [kde1-kdebase-git](https://invent.kde.org/historical/kde1-kdebase)

- Liri

    - Liri是一个具有现代设计和功能的桌面环境。Liri是夏威夷，Papyros和Liri项目的合并。高度实验性的。
    - [liri-shell-git](https://liri.io/)

- Lumina

    - Lumina是一个用Qt 5为FreeBSD编写的轻量级桌面环境，使用Fluxbox进行窗口管理。
    - [lumina-desktop](https://lumina-desktop.org/)

- Maui Shell

    - Maui Shell是台式机、平板电脑和手机的融合外壳。
    - [maui-shell-git](https://github.com/Nitrux/maui-shell)

- Moksha

    - 目前在基于ubuntu的Bodhi Linux中，Fork of Enlightenment是默认的桌面环境。
    - [moksha-git](https://www.bodhilinux.com/moksha-desktop/)

- Pantheon

    - Pantheon是最初为基本操作系统发行版创建的默认桌面环境。它是使用Vala和GTK3工具包从头编写的。在可用性和外观方面，桌面与GNOME Shell和macOS有一些相似之处。
    - [pantheon-session-git](https://elementary.io/)

- PaperDE

    - 桌面环境建立在Qt/Wayland和wayfire之上。
    - [paperde](https://gitlab.com/cubocore/paper/paperde)

- Phosh

    - Phosh是基于GNOME的Wayland shell用户界面，在移动设备上很有用。
    - [phosh](https://gitlab.gnome.org/World/Phosh/phosh)

- Plasma Mobile

    - Plasma Mobile是一个基于KDE Plasma栈和Wayland的移动桌面环境。
    - [plasma-mobile](https://invent.kde.org/plasma/plasma-mobile)

- theDesk

    - theDesk是一个尽可能透明的桌面环境。它使用Qt 5作为它的工具工具，KWin作为它的窗口管理器。
    - [thedesk](https://github.com/vicr123/thedesk)

- Trinity

    - Trinity桌面环境(TDE)项目是一个用于类unix操作系统的计算机桌面环境，其主要目标是保留KDE 3.5的整体计算风格。
    - [See Trinity](https://www.trinitydesktop.org/)


# 3.Custom environments
------------

桌面环境是安装完整图形环境的最简单方法。 但是，如果没有流行的桌面环境满足用户的要求，用户可以通过多种方式自由构建和自定义其图形环境。 一般来说，构建自定义环境涉及选择合适的窗口管理器或合成器、任务栏和许多应用程序（最低限度的选择通常包括终端仿真器、文件管理器和文本编辑器）。

桌面环境通常提供的其他组件有：

- [Desktop compositor(Xorg)](https://wiki.archlinux.org/title/Xorg#Composite)

- [Notification daemon](https://wiki.archlinux.org/title/Desktop_notifications#Standalone)

- [Display manager](https://wiki.archlinux.org/title/Display_manager#List_of_display_managers)

- [Screen locker](https://wiki.archlinux.org/title/List_of_applications#Screen_lockers)

- [Application launcher](https://wiki.archlinux.org/title/List_of_applications#Application_launchers)

- [Audio control](https://wiki.archlinux.org/title/List_of_applications#Volume_control)

- [Backlight control](https://wiki.archlinux.org/title/Backlight#Backlight_utilities)

- [Media control](https://wiki.archlinux.org/title/MPRIS#Control_utilities)

- [Polkit authentication agent](https://wiki.archlinux.org/title/Polkit#Authentication_agents)

- [Power management](https://wiki.archlinux.org/title/Power_management)

- [Screen capture](https://wiki.archlinux.org/title/Screen_capture)

- [Screen temperature](https://wiki.archlinux.org/title/Backlight#Color_correction)

- [Wallpaper setter](https://wiki.archlinux.org/title/List_of_applications#Wallpaper_setters)

- [Logout dialogue](https://wiki.archlinux.org/title/List_of_applications#Logout_UI)

- [Default applications](https://wiki.archlinux.org/title/XDG_MIME_Applications#mimeapps.list)


## 3.1 Use a different window manager

如果桌面环境有文章，请参阅其使用不同的窗口管理器部分，否则请查阅官方文档。

- [Budgie#Use a different window manager](https://wiki.archlinux.org/title/Budgie#Use_a_different_window_manager)

- [Cinnamon#Use a different window manager](https://wiki.archlinux.org/title/Cinnamon#Use_a_different_window_manager)

- [GNOME#Use a different window manager](https://wiki.archlinux.org/title/GNOME#Use_a_different_window_manager)

- [KDE#Use a different window manager](https://wiki.archlinux.org/title/KDE#Use_a_different_window_manager)

- [LXDE#Use a different window manager](https://wiki.archlinux.org/title/LXDE#Use_a_different_window_manager)

- [LXQt#Use a different window manager](https://wiki.archlinux.org/title/LXQt#Use_a_different_window_manager)

- [MATE#Use a different window manager](https://wiki.archlinux.org/title/MATE#Use_a_different_window_manager)

- [Xfce#Use a different window manager](https://wiki.archlinux.org/title/Xfce#Use_a_different_window_manager)

