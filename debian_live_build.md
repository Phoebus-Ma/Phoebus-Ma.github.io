## Creating A Custom Debian ISO 

**Platform: debian-live-11.7.0-amd64**

**Customize debian using live-build.**


### 1.Installing software packages
--------

```bash
$ apt update
$ apt install live-build
```


### 2.Creating a configure file
--------

```bash
$ mkdir debianiso
$ cd debianiso/
$ touch configure
$ chmod +x configure
$ vim configure
```

Add the following:

```bash
#!/bin/sh

set -e

lb config noauto \
        --mode debian \
        --system live \
        --interactive shell \
        --bootappend-live "boot=live components persistence persistence-encryption=luks console=tty1 console=ttyS0,115200" \
        --bootappend-install "boot=components console=tty1 console=ttyS0,115200" \
        --bootloaders grub-efi \
        --binary-image iso-hybrid \
        --debian-installer live \
		--debian-installer-gui true \
        --debian-installer-distribution bullseye \
        --distribution bullseye \
        --architectures amd64 \
        --mirror-bootstrap https://mirrors.ustc.edu.cn/debian/ \
        --mirror-chroot https://mirrors.ustc.edu.cn/debian/ \
        --mirror-binary https://mirrors.ustc.edu.cn/debian/ \
        --archive-areas 'main contrib non-free' \
        --backports true \
        --security true \
        --updates true \
        --source false \
        --linux-packages linux-image \
        --linux-flavours amd64 \
        --apt-recommends false \
        --binary-filesystem ext4 \
        --firmware-binary true \
        --firmware-chroot true \
        --initramfs live-boot \
        --iso-publisher phoebus \
        --iso-volume phoebus-0.1 \
        "${@}"
```

You can change the mirror address, the default mirror is：*http://deb.debian.org/debian/*.

### 3.Built iso
--------

```bash
$ ./configure
$ sudo lb build
```

It will automatically start building, and after a certain amount of time,
it will enter the chroot environment, where we will do the following:

```bash
>$ passwd
>$ apt updates
>$ apt install xserver-xorg-core xserver-xorg xinit lightdm xfce4* fdisk nano wget iputils-ping
>$ exit
```

It then automatically performs the other steps until the ISO is generated.

**live-image-amd64.hybrid.iso**


### Link:
--------

[https://manpages.debian.org/unstable/live-build/lb_config.1.en.html](https://manpages.debian.org/unstable/live-build/lb_config.1.en.html)
