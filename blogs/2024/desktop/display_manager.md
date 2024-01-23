
# 显示管理器

显示管理器（或登录管理器）通常是一个在启动过程结束时代替默认 shell 显示的图形用户界面。与窗口管理器和桌面环境类似，显示管理器也有很多实现。每个显示管理器通常都能进行一定程度的定制。

参考：[Display manager](https://wiki.archlinux.org/title/Display_manager) 。


# 1. 显示管理器列表

## 1.1 控制台

- CDM

    - 用Bash编写的登录管理器。
    - [cdm](https://github.com/evertiro/cdm)

- Console TDM

    - 用Bash编写的xinit扩展。
    - [console-tdm](https://github.com/dopsi/console-tdm)

- nodm

    - 用于自动登录的显示管理器（自2017年以来不再维护）。
    - [nodm包](https://github.com/spanezz/nodm)

- Ly

    - 用于Linux和BSD的TUI（类似ncurses）显示管理器。支持X和Wayland会话。
    - [ly](https://github.com/nullgemm/ly)

- tbsm

    - Bash会话或应用程序启动器。支持X和Wayland会话。
    - [tbsm](https://loh-tar.github.io/tbsm/)

- emptty

    - TTY上的简单CLI显示管理器，支持X和Wayland。
    - [emptty-git](https://github.com/tvrzna/emptty/)

- loginx

    - 带有友好curses用户界面的getty/login/xinit组合。
    - [loginx](https://sourceforge.net/projects/loginx/)


## 1.2 图形界面

- Entrance

    - Enlightenment显示管理器。
    - [entrance-git](https://github.com/Obsidian-StudiosInc/entrance)

- GDM

    - GNOME显示管理器。
    - [gdm包](https://wiki.gnome.org/Projects/GDM)

- LightDM

    - 跨桌面显示管理器，可以使用任何工具包编写的各种前端。
    - [lightdm包](https://github.com/CanonicalLtd/lightdm/)

- LXDM

    - LXDE显示管理器。可以独立于LXDE桌面环境使用。
    - [lxdm包, lxdm-gtk3包](https://sourceforge.net/projects/lxdm/)

- SDDM

    - 基于QML的显示管理器，KDM的继任者；推荐用于Plasma和LXQt。
    - [sddm包](https://github.com/sddm/sddm)

- XDM

    - 支持XDMCP的X显示管理器，适合服务器主机。
    - [xorg-xdm包](https://man.archlinux.org/man/xdm.8)


## 1.3 登录守护进程

- greetd

    - 支持控制台和图形欢迎程序的登录守护程序。
    - [greetd](https://git.sr.ht/~kennylevinsen/greetd)
