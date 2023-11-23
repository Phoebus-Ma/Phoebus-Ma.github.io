
# OS Using Xfce Desktop Step

Tables
----

- [0. Base configuration](#0-base-configuration)

- [1. Install Wget](#1-install-wget)
    - [1.1 Wget-1.21.3](#11-wget-1213)

- [2. Install GPM](#2-install-gpm)
    - [2.1 GPM-1.20.7](#21-gpm-1207)

- [3. Lynx & OpenSSH](#3lynx--openssh)
    - [3.1 Lynx-2.8.9rel.1](#31-lynx-289rel1)
    - [3.2 OpenSSH-9.2p1](#32-openssh-92p1)

- [4. Chapter 2 script](#4-ch2-scripts)
    - [4.1 Important Information](#41-important-information)

- [5. Chapter 3 configuration](#5-ch3-configuration)
    - [5.1 After LFS Configuration Issues](#51-after-lfs-configuration-issues)

- [6. Make-ca 1](#6-make-ca-1)
    - [6.1 UnZip-6.0](#61-unzip-60)
    - [6.2 SQLite-3.40.1](#62-sqlite-3401)
    - [6.3 NSPR-4.35](#63-nspr-435)

- [7. Make-ca 2](#7-make-ca-2)
    - [7.1 NSS-3.88.1](#71-nss-3881)

- [8. Make-ca 3](#8-make-ca-3)
    - [8.1 libtasn1-4.19.0](#81-libtasn1-4190)
    - [8.2 p11-kit-0.24.1](#82-p11-kit-0241)
    - [8.3 make-ca-1.12](#83-make-ca-112)

- [9. Shadow](#9-shadow)
    - [9.1 libtirpc-1.3.3](#91-libtirpc)
    - [9.2 rpcsvc-proto-1.4.3](#92-rpcsvc-proto-143)
    - [9.3 libnsl-2.0.0](#93-libnsl-200)
    - [9.4 Sharutils-4.15.2](#94-sharutils-4152)
    - [9.5 Berkeley DB-5.3.28](#95-berkeley-db-5328)
    - [9.6 CrackLib-2.9.8](#96-cracklib-298)
    - [9.7 libpwquality-1.4.5](#97-libpwquality-145)
    - [9.8 Linux-PAM-1.5.2](#98-linux-pam-152)
    - [9.9 Shadow-4.13](#99-shadow-413)

- [10. None]()
    - [10. None]()

- [11. Sudo](#11-sudo)
    - [11.1 cyrus-sasl-2.1.28](#111-cyrus-sasl-2128)
    - [11.2 openldap-2.6.4](#112-openldap-264)
    - [11.3 procmail-3.22](#113-procmail-322)
    - [11.4 sendmail.8.17.1](#114-sendmail8171)
    - [11.5 reinstall procmail-3.22](#115-reinstall-procmail-322)
    - [11.6 sudo-1.9.13p1](#116-sudo-1913p1)

- [12. Reinstall wget](#12-reinstall-wget)
    - [12.1 pcre2-10.42](#121-pcre2-1042)
    - [12.2 libunistring-1.1](#122-libunistring-11)
    - [12.3 libidn2-2.3.4](#123-libidn2-234)
    - [12.4 libpsl-0.21.2](#124-libpsl-0212)
    - [12.5 wget-1.21.3](#125-wget-1213)

- [13. Reinstall lynx](#13-reinstall-lynx)
    - [13.1 zip30](#131-zip30)
    - [13.2 lynx2.8.9rel.1](#132-lynx289rel1)

- [14. X Window 1](#14-x-window-env-config)
    - [14.1 Introduction to Xorg-7](#141-introduction-to-xorg-7)

- [15. X Window 2](#15-x-window-2)
    - [15.1 util-macros-1.20.0](#151-util-macros-1200)
    - [15.2 xorgproto-2022.2](#152-xorgproto-20222)
    - [15.3 libXau-1.0.11](#153-libxau-1011)
    - [15.4 libXdmcp-1.1.4](#154-libxdmcp-114)
    - [15.5 icu4c-72_1-src](#155-icu4c-72_1-src)
    - [15.6 libxml2-2.10.3](#156-libxml2-2103)

- [16. X Window 3](#16-x-window-3)
    - [16.1 xcb-proto-1.15.2](#161-xcb-proto-1152)
    - [16.2 libxcb-1.15](#162-libxcb-115)
    - [16.3 pcre-8.45](#163-pcre-845)
    - [16.4 dbus-1.14.6](#164-dbus-1146)
    - [16.5 elogind-246.10](#165-elogind-24610)
    - [16.6 freetype-2.13.0](#166-freetype-2130)
    - [16.7 fontconfig-2.14.2](#167-fontconfig-2142)

- [17. X Window 4](#17-x-window-4)
    - [17.1 Xorg Libraries](#171-xorg-libraries)

- [18. X Window 5](#18-x-window-5)
    - [18.1 xcb-util-0.4.1](#181-xcb-util-041)
    - [18.2 xcb-util-image-0.4.1](#182-xcb-util-image-041)
    - [18.3 xcb-util-keysyms-0.4.1](#183-xcb-util-keysyms-041)
    - [18.4 xcb-util-renderutil-0.3.10](#184-xcb-util-renderutil-0310)
    - [18.5 xcb-util-wm-0.4.2](#185-xcb-util-wm-042)
    - [18.6 xcb-util-cursor-0.1.4](#186-xcb-util-cursor-014)

- [19. X Window 6 Mesa 1](#19-mesa-1)
    - [19.1 MarkupSafe-2.1.2](#191-markupsafe-212)
    - [19.2 Mako-1.2.4](#192-mako-124)
    - [19.3 libdrm-2.4.115](#193-libdrm-24115)

- [20. X Window 7 Mesa 2](#20-mesa-2)
    - [20.1 libuv-v1.44.2](#201-libuv-v1442)
    - [20.2 curl-7.88.1](#202-curl-7881)
    - [20.3 nettle-3.8.1](#203-nettle-381)
    - [20.4 lzo-2.10](#204-lzo-210)
    - [20.5 libarchive-3.6.2](#205-libarchive-362)
    - [20.6 nghttp2-1.52.0](#206-nghttp2-1520)

- [21. X Window 8 Mesa 3](#21-mesa-3)
    - [21.1 cmake-3.25.2](#211-cmake-3252)

- [22. X Window 9 Mesa 4](#22-mesa-4)
    - [22.1 libva-2.17.0](#221-libva-2170)

- [23. X Window 10 Mesa 5](#23-mesa-5)
    - [23.1 llvm-15.0.7.src](#231-llvm-1507src)

- [24. X Window 11 Mesa 6](#24-mesa-6)
    - [24.1 mesa-22.3.5](#241-mesa-2235)

- [25. X Window 12](#25-x-window-12)
    - [25.1 xbitmaps-1.1.2](#251-xbitmaps-112)
    - [25.2 libpng-1.6.39](#252-libpng-1639)
    - [25.3 Xorg Applications](#253-xorg-applications)

- [26. X Window 13](#26-x-window-13)
    - [26.1 xcursor-themes-1.0.6](#261-xcursor-themes-106)
    - [26.2 Xorg Fonts](#262-xorg-fonts)

- [27. X Window 14](#27-x-window-14)
    - [27.1 xkeyboard-config-2.38](#271-xkeyboard-config-238)
    - [27.2 libepoxy-1.5.10](#272-libepoxy-1510)
    - [27.3 libxcvt-0.1.2](#273-libxcvt-012)
    - [27.4 pixman-0.42.2](#274-pixman-0422)
    - [27.5 xorg-server-21.1.7](#275-xorg-server-2117)

- [28. X Window 15](#28-x-window-15)
    - [28.1 which-2.21](#281-which-221)
    - [28.2 pciutils-3.9.0](#282-pciutils-390)
    - [28.3 libevdev-1.13.0](#283-libevdev-1130)
    - [28.4 mtdev-1.1.6](#284-mtdev-116)
    - [28.5 xf86-input-evdev-2.10.6](#285-xf86-input-evdev-2106)
    - [28.6 xf86-video-vmware-13.4.0](#286-xf86-video-vmware-1340)

- [29. X Window 16](#29-x-window-16)
    - [29.1 twm-1.0.12](#291-twm-1012)
    - [29.2 xterm-379](#292-xterm-379)
    - [29.3 xclock-1.1.1](#293-xclock-111)
    - [29.4 xinit-1.4.2](#294-xinit-142)

- [30. X Window 17](#30-x-window-17)
    - [30.1 Tuning Fontconfig](#301-tuning-fontconfig)
    - [30.2 Xorg Legacy](#302-xorg-legacy)

- [31. At-spi2-core and Dependencies](#31-at-spi2-core-and-dependencies)
    - [31.1 dbus-1.14.6](#311-dbus-1146)
    - [31.2 sgml-common-0.6.3](#312-sgml-common-063)
    - [31.3 docbook-xml-4.5](#313-docbook-xml-45)
    - [31.4 docbook-xsl-nons-1.79.2](#314-docbook-xsl-nons-1792)
    - [31.5 libxslt-1.1.37](#315-libxslt-1137)
    - [31.6 GLib-2.74.5](#316-glib-2745)
    - [31.7 gobject-introspection-1.74.0](#317-gobject-introspection-1740)
    - [31.8 gsettings-desktop-schemas-43.0](#318-gsettings-desktop-schemas-430)
    - [31.9 at-spi2-core-2.46.0](#319-at-spi2-core-2460)

- [32. Shared-mime-info and dependencies](#32-shared-mime-info-and-dependencies)
    - [32.1 libjpeg-turbo-2.1.5.1](#321-libjpeg-turbo-2151)
    - [32.2 libpng-1.6.39](#322-libpng-1639)
    - [32.3 shared-mime-info-2.2](#323-shared-mime-info-22)

- [33. GTK+3 1](#33-gtk3-1)
    - [33.1 Markdown-3.4.1](#331-markdown-341)
    - [33.2 MarkupSafe-2.1.2](#332-markupsafe-212)
    - [33.3 Pygments-2.14.0](#333-pygments-2140)
    - [33.4 Jinja2-3.1.2](#334-jinja2-312)
    - [33.5 smartypants-2.0.1](#335-smartypants-201)
    - [33.6 typogrify-2.0.7](#336-typogrify-207)
    - [33.7 docutils-0.19](#337-docutils-019)
    - [33.8 gdk-pixbuf-2.42.10](#338-gdk-pixbuf-24210)
    - [33.9 libepoxy-1.5.10](#339-libepoxy-1510)

- [34. GTK+3 2](#34-gtk3-2)
    - [34.1 FriBidi-1.0.12](#341-fribidi-1012)
    - [34.2 Graphite2-1.3.14](#342-graphite2-1314)
    - [34.3 HarfBuzz-7.0.0](#343-harfbuzz-700)
    - [34.4 FreeType-2.13.0](#344-freetype-2130)
    - [34.5 Fontconfig-2.14.2](#345-fontconfig-2142)
    - [34.6 Cairo-1.17.6](#346-cairo-1176)
    - [34.7 Pango-1.50.12](#347-pango-15012)

- [35. GTK+3 3](#35-gtk3-3)
    - [35.1 Wayland-1.21.0](#351-wayland-1210)
    - [35.2 wayland-protocols-1.31](#352-wayland-protocols-131)
    - [35.3 libxkbcommon-1.5.0](#353-libxkbcommon-150)
    - [35.4 GTK+-3.24.36](#354-gtk-32436)

- [36. Exo and dependencies](#36-exo-and-dependencies)
    - [36.1 libxfce4util-4.18.1](#361-libxfce4util-4181)
    - [36.2 Xfconf-4.18.0](#362-xfconf-4180)
    - [36.3 libxfce4ui-4.18.2](#363-libxfce4ui-4182)
    - [36.4 Exo-4.18.0](#364-exo-4180)

- [37. LightDM 1](#37-lightdm-1)
    - [37.1 libgpg-error-1.46](#371-libgpg-error-146)
    - [37.2 libgcrypt-1.10.1](#372-libgcrypt-1101)

- [38. LightDM 2](#38-lightdm-2)
    - [38.1 libxml2-2.10.3](#381-libxml2-2103)
    - [38.2 itstool-2.0.7](#382-itstool-207)

- [39. LightDM 3](#39-lightdm-3)
    - [39.1 Xorg-Server-21.1.7](#391-xorg-server-2117)

- [40. LightDM 4](#40-lightdm-4)
    - [40.1 ISO Codes-4.12.0](#401-iso-codes-4120)
    - [40.2 libxklavier-5.4](#402-libxklavier-54)

- [41. LightDM 5](#41-lightdm-5)
    - [41.1 lightdm-1.32.0](#411-lightdm-1320)

- [42. Gstreamer](#42-gstreamer)
    - [42.1 gstreamer-1.22.0](#421-gstreamer-1220)
    - [42.2 gst-plugins-base-1.22.0](#422-gst-plugins-base-1220)
    - [42.3 gst-plugins-good-1.22.0](#423-gst-plugins-good-1220)
    - [42.4 gst-plugins-bad-1.22.0](#424-gst-plugins-bad-1220)
    - [42.5 gst-plugins-ugly-1.22.0](#425-gst-plugins-ugly-1220)

- [43. Notification](#43-notification)
    - [43.1 libogg-1.3.5](#431-libogg-135)
    - [43.2 libvorbis-1.3.7](#432-libvorbis-137)
    - [43.3 libcanberra-0.30](#433-libcanberra-030)
    - [43.4 notification-daemon-3.20.0](#434-notification-daemon-3200)
    - [43.5 libnotify-0.8.1](#435-libnotify-081)

- [44. Xfce 1](#44-xfce-1)
    - [44.1 taglib-1.13](#441-taglib-113)
    - [44.2 Garcon-4.18.0](#442-garcon-4180)
    - [44.3 libwnck-43.0](#443-libwnck-430)
    - [44.4 xfce4-panel-4.18.2](#444-xfce4-panel-4182)
    - [44.5 hicolor-icon-theme-0.17](#445-hicolor-icon-theme-017)
    - [44.6 thunar-4.18.4](#446-thunar-4184)
    - [44.7 libgudev-237](#447-libgudev-237)
    - [44.8 thunar-volman-4.18.0](#448-thunar-volman-4180)

- [45. Xfce 2](#45-xfce-2)
    - [45.1 tumbler-4.18.0](#451-tumbler-4180)
    - [45.2 xfce4-appfinder-4.18.0](#452-xfce4-appfinder-4180)

- [46. Xfce 3](#46-xfce-3)
    - [46.1 libusb-1.0.26](#461-libusb-1026)
    - [46.2 duktape-2.7.0](#462-duktape-270)
    - [46.3 Polkit-122](#463-polkit-122)
    - [46.4 UPower-1.90.0](#464-upower-1900)
    - [46.5 xfce4-power-manager-4.18.1](#465-xfce4-power-manager-4181)

- [47. Xfce 4](#47-xfce-4)
    - [47.1 XML-Simple-2.25](#471-xml-simple-225)
    - [47.2 icon-naming-utils-0.8.90](#472-icon-naming-utils-0890)
    - [47.3 gnome-icon-theme-3.12.0](#473-gnome-icon-theme-3120)
    - [47.4 xfce4-settings-4.18.2](#474-xfce4-settings-4182)

- [48. Xfce 5](#48-xfce-5)
    - [48.1 startup-notification-0.12](#481-startup-notification-012)
    - [48.2 Xfdesktop-4.18.1](#482-xfdesktop-4181)
    - [48.3 Xfwm4-4.18.0](#483-xfwm4-4180)

- [49. Xfce 6](#49-xfce-6)
    - [49.1 desktop-file-utils-0.26](#491-desktop-file-utils-026)
    - [49.2 GTK+-2.24.33](#492-gtk-22433)
    - [49.3 libglade-2.6.4](#493-libglade-264)
    - [49.4 GLU-9.0.2](#494-glu-902)
    - [49.5 XScreenSaver-6.06](#495-xscreensaver-606)
    - [49.6 shared-mime-info-2.2](#496-shared-mime-info-22)
    - [49.7 Graphviz-7.1.0](#497-graphviz-710)
    - [49.8 Vala-0.56.4](#498-vala-0564)
    - [49.9 AccountsService-22.08.8](#499-accountsservice-22088)
    - [49.10 polkit-gnome-0.105](#4910-polkit-gnome-0105)
    - [49.11 xfce4-session-4.18.1](#4911-xfce4-session-4181)

- [50. Xfce 7](#50-xfce-7)
    - [50.1 dbus-glib-0.112](#501-dbus-glib-0112)
    - [50.2 Parole-4.18.0](#502-parole-4180)

- [51. Xfce 8](#51-xfce-8)
    - [51.1 GnuTLS-3.8.0](#511-gnutls-380)
    - [51.2 VTE-0.70.3](#512-vte-0703)
    - [51.3 xfce4-terminal-1.0.4](#513-xfce4-terminal-104)

- [52. Xfce 9](#52-xfce-9)
    - [52.1 libburn-1.5.4](#521-libburn-154)
    - [52.2 libisofs-1.5.4](#522-libisofs-154)
    - [52.3 Xfburn-0.6.2](#523-xfburn-062)

- [53. Xfce 10](#53-xfce-10)
    - [53.1 libexif-0.6.24](#531-libexif-0624)
    - [53.2 Ristretto-0.13.0](#532-ristretto-0130)

- [54. Xfce 11](#54-xfce-11)
    - [54.1 xfce4-dev-tools-4.18.0](#541-xfce4-dev-tools-4180)
    - [54.2 xfce4-notifyd-0.8.1](#542-xfce4-notifyd-081)

- [55. Xfce 12](#55-xfce-12)
    - [55.1 libsndfile-1.2.0](#551-libsndfile-120)
    - [55.2 PulseAudio-16.1](#552-pulseaudio-161)
    - [55.3 xfce4-pulseaudio-plugin-0.4.5](#553-xfce4-pulseaudio-plugin-045)


# 0. Base configuration

1. 

```bash
export CFLAGS='-O2 -march=native -pipe'
export CXXFLAGS='-O2 -march=native -pipe'
export MAKEFLAGS='-j2'
```

2. 

```bash
# /etc/fstab
/dev/sda1 /boot ext4 defaults 1 2
```

3. 

```bash
cp linux-6.1.11/arch/x86_64/boot/bzImage /boot/vmlinuz-6.1.11-lfs-11.3-2
cp linux-6.1.11/System.map /boot/System.map-6.1.11-2
cp linux-6.1.11/.config /boot/config-6.1.11-2
```

```bash
# /boot/grub/grub.cfg
menuentry "" (
    linux   /boot/vmlinuz-6.1.11-2 root=/dev/sda2 ro
)
```

4. 

```bash
vim /etc/sysconfig/ifconfig.enp0s3
edit ip & gateway & broadcast
/etc/init.d/network restart
```


# 1. Install Wget
----

## 1.1 Wget-1.21.3

- <https://ftp.gnu.org/gnu/wget/wget-1.21.3.tar.gz>

- 1. unzip

```bash
cd /sources
mkdir BLFS
cd BLFS/
tar -xf wget-1.21.3.tar.gz
cd wget-1.21.3
```

- 2. build

```bash
./configure --prefix=/usr      \
            --sysconfdir=/etc  \
            --with-ssl=openssl &&
make
```

- 3. install

```bash
su -c "make install"
```

- 4. remove

```bash
cd ..
rm -rf wget-1.21.3
```


# 2. Install GPM
----

## 2.1 GPM-1.20.7

- <https://anduin.linuxfromscratch.org/BLFS/gpm/gpm-1.20.7.tar.bz2>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/gpm-1.20.7-consolidated-1.patch>
- <https://anduin.linuxfromscratch.org/BLFS/blfs-bootscripts/blfs-bootscripts-20230101.tar.xz>

- 1. kernel configuration

```bash
cd /sources/linux-6.1.11
make menuconfig
```

```bash
Device Drivers  --->
  Input device support ---> [CONFIG_INPUT]
    <*/M> Mouse interface   [CONFIG_INPUT_MOUSEDEV]
```

```bash
make
```

- 2. install kernel

```bash
cp linux-6.1.11/arch/x86_64/boot/bzImage /boot/vmlinuz-6.1.11-lfs-11.3-2
cp linux-6.1.11/System.map /boot/System.map-6.1.11-2
cp linux-6.1.11/.config /boot/config-6.1.11-2
```

```bash
vim /boot/grub/grub.cfg
```

```bash
menuentry "" (
    linux   /boot/vmlinuz-6.1.11-2 root=/dev/sda2 ro
)
```

- 3. unzip

```bash
tar -xf gpm-1.20.7.tar.bz2
cd gpm-1.20.7
```

- 4. build

```bash
patch -Np1 -i ../gpm-1.20.7-consolidated-1.patch &&
./autogen.sh                                     &&
./configure --prefix=/usr --sysconfdir=/etc      &&
make
```

- 5. install

```bash
make install                                          &&

install-info --dir-file=/usr/share/info/dir           \
             /usr/share/info/gpm.info                 &&

rm -fv /usr/lib/libgpm.a                              &&
ln -sfv libgpm.so.2.1.0 /usr/lib/libgpm.so            &&
install -v -m644 conf/gpm-root.conf /etc              &&

install -v -m755 -d /usr/share/doc/gpm-1.20.7/support &&
install -v -m644    doc/support/*                     \
                    /usr/share/doc/gpm-1.20.7/support &&
install -v -m644    doc/{FAQ,HACK_GPM,README*}        \
                    /usr/share/doc/gpm-1.20.7
```

- 6. configuration

```bash
cd /sources/BLFS
tar -xf  blfs-bootscripts-20230101.tar.xz
cd  blfs-bootscripts-20230101
make install-gpm
```

```bash
cat > /etc/sysconfig/mouse << "EOF"
# Begin /etc/sysconfig/mouse

MDEVICE="/dev/input/mice"
PROTOCOL="imps2"
GPMOPTS=""

# End /etc/sysconfig/mouse
EOF
```

```bash
/etc/init.d/gpm start
```

- 7. remove

```bash
cd ..
rm -rf gpm-1.20.7
```


# 3.Lynx & OpenSSH
----

## 3.1 Lynx-2.8.9rel.1

- <https://invisible-mirror.net/archives/lynx/tarballs/lynx2.8.9rel.1.tar.bz2>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/lynx-2.8.9rel.1-security_fix-1.patch>

- 1. unzip

```bash
tar -xf lynx2.8.9rel.1.tar.bz2
cd lynx2.8.9rel.1
```

- 2. build

```bash
patch -p1 -i ../lynx-2.8.9rel.1-security_fix-1.patch
```

```bash
./configure --prefix=/usr          \
            --sysconfdir=/etc/lynx \
            --datadir=/usr/share/doc/lynx-2.8.9rel.1 \
            --with-zlib            \
            --with-bzlib           \
            --with-ssl             \
            --with-screen=ncursesw \
            --enable-nls           \
            --enable-locale-charset &&
make
```

- 3. install

```bash
make install-full &&
chgrp -v -R root /usr/share/doc/lynx-2.8.9rel.1/lynx_doc
```

- 4. configuration

```bash
sed -e '/#LOCALE/     a LOCALE_CHARSET:TRUE'     \
    -i /etc/lynx/lynx.cfg
```

```bash
sed -e '/#DEFAULT_ED/ a DEFAULT_EDITOR:vi'       \
    -i /etc/lynx/lynx.cfg
```

```bash
sed -e '/#PERSIST/    a PERSISTENT_COOKIES:TRUE' \
    -i /etc/lynx/lynx.cfg
```

- 5. remove

```bash
cd ..
rm -rf lynx2.8.9rel.1
```

## 3.2 OpenSSH-9.2p1

- <https://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-9.2p1.tar.gz>

- 1. unzip

```bash
tar -xf openssh-9.2p1.tar.gz
cd openssh-9.2p1
```

- 2. build

```bash
install  -v -m700 -d /var/lib/sshd &&
chown    -v root:sys /var/lib/sshd &&

groupadd -g 50 sshd        &&
useradd  -c 'sshd PrivSep' \
         -d /var/lib/sshd  \
         -g sshd           \
         -s /bin/false     \
         -u 50 sshd
```

```bash
./configure --prefix=/usr                            \
            --sysconfdir=/etc/ssh                    \
            --with-privsep-path=/var/lib/sshd        \
            --with-default-path=/usr/bin             \
            --with-superuser-path=/usr/sbin:/usr/bin \
            --with-pid-dir=/run                      &&
make
```

- 3. install

```bash
make install &&
install -v -m755    contrib/ssh-copy-id /usr/bin     &&

install -v -m644    contrib/ssh-copy-id.1 \
                    /usr/share/man/man1              &&
install -v -m755 -d /usr/share/doc/openssh-9.2p1     &&
install -v -m644    INSTALL LICENCE OVERVIEW README* \
                    /usr/share/doc/openssh-9.2p1
```

- 4. configuration

```bash
cd /sources/BLFS/blfs-bootscripts-20230101
make install-sshd
```

- 5. remove

```bash
cd /sources/BLFS
rm -rf openssh-9.2p1
```


# 4. ch2 scripts
----

## 4.1 Important Information

1. 

```bash
cat > /usr/sbin/remove-la-files.sh << "EOF"
#!/bin/bash

# /usr/sbin/remove-la-files.sh
# Written for Beyond Linux From Scratch
# by Bruce Dubbs <bdubbs@linuxfromscratch.org>

# Make sure we are running with root privs
if test "${EUID}" -ne 0; then
    echo "Error: $(basename ${0}) must be run as the root user! Exiting..."
    exit 1
fi

# Make sure PKG_CONFIG_PATH is set if discarded by sudo
source /etc/profile

OLD_LA_DIR=/var/local/la-files

mkdir -p $OLD_LA_DIR

# Only search directories in /opt, but not symlinks to directories
OPTDIRS=$(find /opt -mindepth 1 -maxdepth 1 -type d)

# Move any found .la files to a directory out of the way
find /usr/lib $OPTDIRS -name "*.la" ! -path "/usr/lib/ImageMagick*" \
  -exec mv -fv {} $OLD_LA_DIR \;
###############

# Fix any .pc files that may have .la references

STD_PC_PATH='/usr/lib/pkgconfig
             /usr/share/pkgconfig
             /usr/local/lib/pkgconfig
             /usr/local/share/pkgconfig'

# For each directory that can have .pc files
for d in $(echo $PKG_CONFIG_PATH | tr : ' ') $STD_PC_PATH; do

  # For each pc file
  for pc in $d/*.pc ; do
    if [ $pc == "$d/*.pc" ]; then continue; fi

    # Check each word in a line with a .la reference
    for word in $(grep '\.la' $pc); do
      if $(echo $word | grep -q '.la$' ); then
        mkdir -p $d/la-backup
        cp -fv  $pc $d/la-backup

        basename=$(basename $word )
        libref=$(echo $basename|sed -e 's/^lib/-l/' -e 's/\.la$//')

        # Fix the .pc file
        sed -i "s:$word:$libref:" $pc
      fi
    done
  done
done

EOF

chmod +x /usr/sbin/remove-la-files.sh
```

```bash
/usr/sbin/remove-la-files.sh
```

2. 

```bash
vim /usr/bin/checkman.sh
```

```bash
#!/bin/sh
# Begin checkman.sh
# Usage: find /usr/share/man -type f | xargs checkman.sh
for a in "$@"
do
    # echo "Checking $a..."
    # Pure-ASCII manual page (possibly except comments) is OK
    grep -v '.\\"' "$a" | iconv -f US-ASCII -t US-ASCII >/dev/null 2>&1 \
        && continue
    # Non-UTF-8 manual page is OK
    iconv -f UTF-8 -t UTF-8 "$a" >/dev/null 2>&1 || continue
    # Found a UTF-8 manual page, bad.
    echo "UTF-8 manual page: $a" >&2
done
# End checkman.sh
```

```bash
chmod u+x /usr/bin/checkman.sh
```

```bash
find /usr/share/man -type f | xargs checkman.sh
```


# 5. ch3 configuration
----

## 5.1 After LFS Configuration Issues

1. 

```bash
/usr/sbin/useradd -D
```

```bash
mkdir /etc/skel
```

2. 

```bash
cat > /etc/profile << "EOF"
# Begin /etc/profile
# Written for Beyond Linux From Scratch
# by James Robertson <jameswrobertson@earthlink.net>
# modifications by Dagmar d'Surreal <rivyqntzne@pbzpnfg.arg>

# System wide environment variables and startup programs.

# System wide aliases and functions should go in /etc/bashrc.  Personal
# environment variables and startup programs should go into
# ~/.bash_profile.  Personal aliases and functions should go into
# ~/.bashrc.

# Functions to help us manage paths.  Second argument is the name of the
# path variable to be modified (default: PATH)
pathremove () {
        local IFS=':'
        local NEWPATH
        local DIR
        local PATHVARIABLE=${2:-PATH}
        for DIR in ${!PATHVARIABLE} ; do
                if [ "$DIR" != "$1" ] ; then
                  NEWPATH=${NEWPATH:+$NEWPATH:}$DIR
                fi
        done
        export $PATHVARIABLE="$NEWPATH"
}

pathprepend () {
        pathremove $1 $2
        local PATHVARIABLE=${2:-PATH}
        export $PATHVARIABLE="$1${!PATHVARIABLE:+:${!PATHVARIABLE}}"
}

pathappend () {
        pathremove $1 $2
        local PATHVARIABLE=${2:-PATH}
        export $PATHVARIABLE="${!PATHVARIABLE:+${!PATHVARIABLE}:}$1"
}

export -f pathremove pathprepend pathappend

# Set the initial path
export PATH=/usr/bin

# Attempt to provide backward compatibility with LFS earlier than 11
if [ ! -L /bin ]; then
        pathappend /bin
fi

if [ $EUID -eq 0 ] ; then
        pathappend /usr/sbin
        if [ ! -L /sbin ]; then
                pathappend /sbin
        fi
        unset HISTFILE
fi

# Set up some environment variables.
export HISTSIZE=1000
export HISTIGNORE="&:[bf]g:exit"

# Set some defaults for graphical systems
export XDG_DATA_DIRS=${XDG_DATA_DIRS:-/usr/share/}
export XDG_CONFIG_DIRS=${XDG_CONFIG_DIRS:-/etc/xdg/}
export XDG_RUNTIME_DIR=${XDG_RUNTIME_DIR:-/tmp/xdg-$USER}

# Set up a red prompt for root and a green one for users.
NORMAL="\[\e[0m\]"
RED="\[\e[1;31m\]"
GREEN="\[\e[1;32m\]"
if [[ $EUID == 0 ]] ; then
  PS1="$RED\u [ $NORMAL\w$RED ]# $NORMAL"
else
  PS1="$GREEN\u [ $NORMAL\w$GREEN ]\$ $NORMAL"
fi

for script in /etc/profile.d/*.sh ; do
        if [ -r $script ] ; then
                . $script
        fi
done

unset script RED GREEN NORMAL

# End /etc/profile
EOF
```

```bash
install --directory --mode=0755 --owner=root --group=root /etc/profile.d
```

3. 

```bash
cat > /etc/profile.d/bash_completion.sh << "EOF"
# Begin /etc/profile.d/bash_completion.sh
# Import bash completion scripts

# If the bash-completion package is installed, use its configuration instead
if [ -f /usr/share/bash-completion/bash_completion ]; then

  # Check for interactive bash and that we haven't already been sourced.
  if [ -n "${BASH_VERSION-}" -a -n "${PS1-}" -a -z "${BASH_COMPLETION_VERSINFO-}" ]; then

    # Check for recent enough version of bash.
    if [ ${BASH_VERSINFO[0]} -gt 4 ] || \
       [ ${BASH_VERSINFO[0]} -eq 4 -a ${BASH_VERSINFO[1]} -ge 1 ]; then
       [ -r "${XDG_CONFIG_HOME:-$HOME/.config}/bash_completion" ] && \
            . "${XDG_CONFIG_HOME:-$HOME/.config}/bash_completion"
       if shopt -q progcomp && [ -r /usr/share/bash-completion/bash_completion ]; then
          # Source completion code.
          . /usr/share/bash-completion/bash_completion
       fi
    fi
  fi

else

  # bash-completions are not installed, use only bash completion directory
  if shopt -q progcomp; then
    for script in /etc/bash_completion.d/* ; do
      if [ -r $script ] ; then
        . $script
      fi
    done
  fi
fi

# End /etc/profile.d/bash_completion.sh
EOF
```

```bash
install --directory --mode=0755 --owner=root --group=root /etc/bash_completion.d
```

4. 

```bash
cat > /etc/profile.d/dircolors.sh << "EOF"
# Setup for /bin/ls and /bin/grep to support color, the alias is in /etc/bashrc.
if [ -f "/etc/dircolors" ] ; then
        eval $(dircolors -b /etc/dircolors)
fi

if [ -f "$HOME/.dircolors" ] ; then
        eval $(dircolors -b $HOME/.dircolors)
fi

alias ls='ls --color=auto'
alias grep='grep --color=auto'
EOF
```

5. 

```bash
cat > /etc/profile.d/extrapaths.sh << "EOF"
if [ -d /usr/local/lib/pkgconfig ] ; then
        pathappend /usr/local/lib/pkgconfig PKG_CONFIG_PATH
fi
if [ -d /usr/local/bin ]; then
        pathprepend /usr/local/bin
fi
if [ -d /usr/local/sbin -a $EUID -eq 0 ]; then
        pathprepend /usr/local/sbin
fi

if [ -d /usr/local/share ]; then
        pathprepend /usr/local/share XDG_DATA_DIRS
fi

# Set some defaults before other applications add to these paths.
pathappend /usr/share/man  MANPATH
pathappend /usr/share/info INFOPATH
EOF
```

6. 

```bash
cat > /etc/profile.d/readline.sh << "EOF"
# Set up the INPUTRC environment variable.
if [ -z "$INPUTRC" -a ! -f "$HOME/.inputrc" ] ; then
        INPUTRC=/etc/inputrc
fi
export INPUTRC
EOF
```

7. 

```bash
cat > /etc/profile.d/umask.sh << "EOF"
# By default, the umask should be set.
if [ "$(id -gn)" = "$(id -un)" -a $EUID -gt 99 ] ; then
  umask 002
else
  umask 022
fi
EOF
```

8. 

```bash
cat > /etc/profile.d/i18n.sh << "EOF"
# Set up i18n variables
export LANG=en_US.utf8
EOF
```

9. 

```bash
cat > /etc/bashrc << "EOF"
# Begin /etc/bashrc
# Written for Beyond Linux From Scratch
# by James Robertson <jameswrobertson@earthlink.net>
# updated by Bruce Dubbs <bdubbs@linuxfromscratch.org>

# System wide aliases and functions.

# System wide environment variables and startup programs should go into
# /etc/profile.  Personal environment variables and startup programs
# should go into ~/.bash_profile.  Personal aliases and functions should
# go into ~/.bashrc

# Provides colored /bin/ls and /bin/grep commands.  Used in conjunction
# with code in /etc/profile.

alias ls='ls --color=auto'
alias grep='grep --color=auto'

# Provides prompt for non-login shells, specifically shells started
# in the X environment. [Review the LFS archive thread titled
# PS1 Environment Variable for a great case study behind this script
# addendum.]

NORMAL="\[\e[0m\]"
RED="\[\e[1;31m\]"
GREEN="\[\e[1;32m\]"
if [[ $EUID == 0 ]] ; then
  PS1="$RED\u [ $NORMAL\w$RED ]# $NORMAL"
else
  PS1="$GREEN\u [ $NORMAL\w$GREEN ]\$ $NORMAL"
fi

unset RED GREEN NORMAL

shopt -s histappend
PROMPT_COMMAND='history -a'
export IGNOREEOF=1

export CFLAGS="-O2 -march=native -pipe"
export CXXFLAGS="-O2 -march=native -pipe"
export MAKEFLAGS="-j2"

# End /etc/bashrc
EOF
```

10. 

```bash
cat > ~/.bash_profile << "EOF"
# Begin ~/.bash_profile
# Written for Beyond Linux From Scratch
# by James Robertson <jameswrobertson@earthlink.net>
# updated by Bruce Dubbs <bdubbs@linuxfromscratch.org>

# Personal environment variables and startup programs.

# Personal aliases and functions should go in ~/.bashrc.  System wide
# environment variables and startup programs are in /etc/profile.
# System wide aliases and functions are in /etc/bashrc.

if [ -f "$HOME/.bashrc" ] ; then
  source $HOME/.bashrc
fi

if [ -d "$HOME/bin" ] ; then
  pathprepend $HOME/bin
fi

# Having . in the PATH is dangerous
#if [ $EUID -gt 99 ]; then
#  pathappend .
#fi

# End ~/.bash_profile
EOF
```

```bash
cp .bash_profile /etc/skel
```

11. 

```bash
cat > ~/.profile << "EOF"
# Begin ~/.profile
# Personal environment variables and startup programs.

if [ -d "$HOME/bin" ] ; then
  pathprepend $HOME/bin
fi

# Set up user specific i18n variables
#export LANG=<ll>_<CC>.<charmap><@modifiers>

# End ~/.profile
EOF
```

```bash
cp .profile /etc/skel
```

12. 

```bash
cat > ~/.bashrc << "EOF"
# Begin ~/.bashrc
# Written for Beyond Linux From Scratch
# by James Robertson <jameswrobertson@earthlink.net>

# Personal aliases and functions.

# Personal environment variables and startup programs should go in
# ~/.bash_profile.  System wide environment variables and startup
# programs are in /etc/profile.  System wide aliases and functions are
# in /etc/bashrc.

if [ -f "/etc/bashrc" ] ; then
  source /etc/bashrc
fi

# Set up user specific i18n variables
#export LANG=<ll>_<CC>.<charmap><@modifiers>

# End ~/.bashrc
EOF
```

```bash
cp .bashrc /etc/skel
```

13. 

```bash
cat > ~/.bash_logout << "EOF"
# Begin ~/.bash_logout
# Written for Beyond Linux From Scratch
# by James Robertson <jameswrobertson@earthlink.net>

# Personal items to perform on logout.

# End ~/.bash_logout
EOF
```

```bash
cp .bash_logout /etc/skel
```

14. 

```bash
dircolors -p > /etc/dircolors
```

```bash
cp /etc/dircolors /etc/skel/.dircolors
```

15. 

```bash
vim .vimrc
```

```bash
" Begin .vimrc

set columns=80
" set wrapmargin=8
set ruler
" set showcmd
set mouse=r

" End .vimrc
```

16. 

```bash
clear > /etc/issue
```

```bash
vim /etc/issue
```

```bash
Baud Rate:      \b
Date:           \d
Time:           \t
System:         \s
Teletype:       \l
Machine:        \m
Hostname:       \n
Dimain Name:    \o
Kernel:         \r
Currently:      \U
kernel Built:   \v

This is \n.\o (\s \m \r) \t
```

17. 

```bash
cd /sources/BLFS/blfs-bootscripts-20230101
make install-random
```

```bash
/etc/init.d/random start
```

18. 

```bash
cd /etc/skel
```

```bash
chmod g-r .*
chmod o-r .*
```

19. 

```bash
useradd -m phoebus
passwd phoebus
```

```bash
cd /sources
chown phoebus BLFS
```


# 6. make-ca 1
----

Starting use normal user: phoebus

## 6.1 UnZip-6.0

- <https://downloads.sourceforge.net/infozip/unzip60.tar.gz>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/unzip-6.0-consolidated_fixes-1.patch>

- 1. unzip

```bash
tar -xf unzip60.tar.gz
cd unzip60
```

- 2. build

```bash
patch -Np1 -i ../unzip-6.0-consolidated_fixes-1.patch
```

```bash
make -f unix/Makefile generic
```

- 3. install

```bash
su
```

```bash
make prefix=/usr MANDIR=/usr/share/man/man1 \
 -f unix/Makefile install
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf unzip60
```

## 6.2 SQLite-3.40.1

- <https://sqlite.org/2022/sqlite-autoconf-3400100.tar.gz>
- <https://sqlite.org/2022/sqlite-doc-3400100.zip>

- 1. unzip

```bash
tar -xf sqlite-autoconf-3400100.tar.gz
cd sqlite-autoconf-3400100
```

- 2. build

```bash
unzip -q ../sqlite-doc-3400100.zip
```

```bash
./configure --prefix=/usr     \
            --disable-static  \
            --enable-fts5     \
            CPPFLAGS="-DSQLITE_ENABLE_FTS3=1            \
                      -DSQLITE_ENABLE_FTS4=1            \
                      -DSQLITE_ENABLE_COLUMN_METADATA=1 \
                      -DSQLITE_ENABLE_UNLOCK_NOTIFY=1   \
                      -DSQLITE_ENABLE_DBSTAT_VTAB=1     \
                      -DSQLITE_SECURE_DELETE=1          \
                      -DSQLITE_ENABLE_FTS3_TOKENIZER=1" &&
make
```

- 3. install

```bash
su
```

```bash
make install
```

```bash
install -v -m755 -d /usr/share/doc/sqlite-3.40.1 &&
cp -v -R sqlite-doc-3400100/* /usr/share/doc/sqlite-3.40.1
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf sqlite-autoconf-3400100
```

## 6.3 NSPR-4.35

- <https://archive.mozilla.org/pub/nspr/releases/v4.35/src/nspr-4.35.tar.gz>

- 1. unzip

```bash
tar -xf nspr-4.35.tar.gz
cd nspr-4.35
```

- 2. build

```bash
cd nspr                                                     &&
sed -ri '/^RELEASE/s/^/#/' pr/src/misc/Makefile.in &&
sed -i 's#$(LIBRARY) ##'   config/rules.mk         &&

./configure --prefix=/usr \
            --with-mozilla \
            --with-pthreads \
            $([ $(uname -m) = x86_64 ] && echo --enable-64bit) &&
make
```

- 3. install

```bash
su -c "make install"
```

- 4. remove

```bash
cd ..
rm -rf nspr-4.35
```


# 7. make-ca 2
----

## 7.1 NSS-3.88.1

- <https://archive.mozilla.org/pub/security/nss/releases/NSS_3_88_1_RTM/src/nss-3.88.1.tar.gz>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/nss-3.88.1-standalone-1.patch>

- 1. unzip

```bash
tar -xf nss-3.88.1.tar.gz
cd nss-3.88.1
```

- 2. build

```bash
patch -Np1 -i ../nss-3.88.1-standalone-1.patch &&

cd nss &&

make BUILD_OPT=1                  \
  NSPR_INCLUDE_DIR=/usr/include/nspr  \
  USE_SYSTEM_ZLIB=1                   \
  ZLIB_LIBS=-lz                       \
  NSS_ENABLE_WERROR=0                 \
  $([ $(uname -m) = x86_64 ] && echo USE_64=1) \
  $([ -f /usr/include/sqlite3.h ] && echo NSS_USE_SYSTEM_SQLITE=1)
```

- 3. test

```bash
cd tests &&
HOST=localhost DOMSUF=localdomain ./all.sh
cd ../
```

- 4. install

```bash
su
```

```bash
cd ../dist                                                          &&

install -v -m755 Linux*/lib/*.so              /usr/lib              &&
install -v -m644 Linux*/lib/{*.chk,libcrmf.a} /usr/lib              &&

install -v -m755 -d                           /usr/include/nss      &&
cp -v -RL {public,private}/nss/*              /usr/include/nss      &&
chmod -v 644                                  /usr/include/nss/*    &&

install -v -m755 Linux*/bin/{certutil,nss-config,pk12util} /usr/bin &&

install -v -m644 Linux*/lib/pkgconfig/nss.pc  /usr/lib/pkgconfig
```

```bash
exit
```

- 5. remove

```bash
cd ..
rm -rf nss-3.88.1
```


# 8. make-ca 3
----

## 8.1 libtasn1-4.19.0

- <https://ftp.gnu.org/gnu/libtasn1/libtasn1-4.19.0.tar.gz>

- 1. unzip

```bash
tar -xf libtasn1-4.19.0.tar.gz
cd libtasn1-4.19.0
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
su -c "make install"
```

- 4. remove

```bash
cd ..
rm -rf libtasn1-4.19.0
```

## 8.2 p11-kit-0.24.1

- <https://github.com/p11-glue/p11-kit/releases/download/0.24.1/p11-kit-0.24.1.tar.xz>

- 1. unzip

```bash
tar -xf p11-kit-0.24.1.tar.xz
cd p11-kit-0.24.1
```

- 2. build

```bash
sed '20,$ d' -i trust/trust-extract-compat &&
cat >> trust/trust-extract-compat << "EOF"
# Copy existing anchor modifications to /etc/ssl/local
/usr/libexec/make-ca/copy-trust-modifications

# Update trust stores
/usr/sbin/make-ca -r
EOF
```

```bash
mkdir p11-build &&
cd    p11-build &&

meson --prefix=/usr       \
      --buildtype=release \
      -Dtrust_paths=/etc/pki/anchors &&
ninja
```

- 3. install

```bash
su
```

```bash
ninja install &&
ln -sfv /usr/libexec/p11-kit/trust-extract-compat \
        /usr/bin/update-ca-certificates
```

```bash
exit
```

- 4. configuration

```bash
su
```

```bash
ln -sfv ./pkcs11/p11-kit-trust.so /usr/lib/libnssckbi.so
```

```bash
exit
```

- 5. remove

```bash
cd ../..
rm -rf p11-kit-0.24.1
```

## 8.3 make-ca-1.12

- <https://github.com/lfs-book/make-ca/releases/download/v1.12/make-ca-1.12.tar.xz>

- 1. unzip

```bash
tar -xf make-ca-1.12.tar.xz
cd make-ca-1.12
```

- 2. install

```bash
su
```

```bash
make install &&
install -vdm755 /etc/ssl/local
```

```bash
/usr/sbin/make-ca -g
```

```bash
exit
```

- 3. configuration

```bash
su
```

```bash
wget http://www.cacert.org/certs/root.crt &&
wget http://www.cacert.org/certs/class3.crt &&
openssl x509 -in root.crt -text -fingerprint -setalias "CAcert Class 1 root" \
        -addtrust serverAuth -addtrust emailProtection -addtrust codeSigning \
        > /etc/ssl/local/CAcert_Class_1_root.pem &&
openssl x509 -in class3.crt -text -fingerprint -setalias "CAcert Class 3 root" \
        -addtrust serverAuth -addtrust emailProtection -addtrust codeSigning \
        > /etc/ssl/local/CAcert_Class_3_root.pem &&
/usr/sbin/make-ca -r
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf make-ca-1.12
```


# 9. shadow
----

## 9.1 libtirpc

- <https://downloads.sourceforge.net/libtirpc/libtirpc-1.3.3.tar.bz2>

- 1. unzip

```bash
tar -xf libtirpc-1.3.3.tar.bz2
cd libtirpc-1.3.3/
```

- 2. build

```bash
./configure --prefix=/usr                                   \
            --sysconfdir=/etc                               \
            --disable-static                                \
            --disable-gssapi                                &&
make
```

- 3. install

```bash
su -c "make install"
```

- 4. remove

```bash
cd ..
rm -rf libtirpc-1.3.3
```


## 9.2 rpcsvc-proto-1.4.3

- <https://github.com/thkukuk/rpcsvc-proto/releases/download/v1.4.3/rpcsvc-proto-1.4.3.tar.xz>

- 1. unzip

```bash
tar -xf rpcsvc-proto-1.4.3.tar.xz
cd rpcsvc-proto-1.4.3/
```

- 2. build

```bash
./configure --sysconfdir=/etc &&
make
```

- 3. install

```bash
su -c "make install"
```

- 4. remove

```bash
cd ..
rm -rf rpcsvc-proto-1.4.3
```

## 9.3 libnsl-2.0.0

- <https://github.com/thkukuk/libnsl/releases/download/v2.0.0/libnsl-2.0.0.tar.xz>

- 1. unzip

```bash
tar -xf libnsl-2.0.0.tar.xz
cd libnsl-2.0.0
```

- 2. build

```bash
./configure --sysconfdir=/etc --disable-static &&
make
```

- 3. install

```bash
su -c "make install"
```

- 4. remove

```bash
cd ..
rm -rf libnsl-2.0.0
```

## 9.4 Sharutils-4.15.2

- <https://ftp.gnu.org/gnu/sharutils/sharutils-4.15.2.tar.xz>

- 1. unzip

```bash
tar -xf sharutils-4.15.2.tar.xz
cd sharutils-4.15.2
```

- 2. build

```bash
sed -i 's/BUFSIZ/rw_base_size/' src/unshar.c &&
sed -i '/program_name/s/^/extern /' src/*opts.h
```

```bash
sed -i 's/IO_ftrylockfile/IO_EOF_SEEN/' lib/*.c        &&
echo "#define _IO_IN_BACKUP 0x100" >> lib/stdio-impl.h &&

./configure --prefix=/usr &&
make
```

- 3. install

```bash
su -c "make install"
```

- 4. remove

```bash
cd ..
rm -rf sharutils-4.15.2
```

## 9.5 Berkeley DB-5.3.28

- <https://anduin.linuxfromscratch.org/BLFS/bdb/db-5.3.28.tar.gz>

- 1. unzip

```bash
tar -xf db-5.3.28.tar.gz
cd db-5.3.28
```

- 2. build

```bash
sed -i 's/\(__atomic_compare_exchange\)/\1_db/' src/dbinc/atomic.h
```

```bash
cd build_unix                        &&
../dist/configure --prefix=/usr      \
                  --enable-compat185 \
                  --enable-dbm       \
                  --disable-static   \
                  --enable-cxx       &&
make
```

- 3. install

```bash
su
```

```bash
make docdir=/usr/share/doc/db-5.3.28 install &&

chown -v -R root:root                        \
      /usr/bin/db_*                          \
      /usr/include/db{,_185,_cxx}.h          \
      /usr/lib/libdb*.{so,la}                \
      /usr/share/doc/db-5.3.28
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf db-5.3.28
```


## 9.6 CrackLib-2.9.8

- <https://github.com/cracklib/cracklib/releases/download/v2.9.8/cracklib-2.9.8.tar.bz2>
- <https://github.com/cracklib/cracklib/releases/download/v2.9.8/cracklib-words-2.9.8.bz2>

- 1. unzip

```bash
tar -xf cracklib-2.9.8.tar.bz2
cd cracklib-2.9.8
```

- 2. build

```bash
autoreconf -fiv &&

PYTHON=python3               \
./configure --prefix=/usr    \
            --disable-static \
            --with-default-dict=/usr/lib/cracklib/pw_dict &&
make
```

- 3. install

```bash
su -c "make install"
```

```bash
install -v -m644 -D    ../cracklib-words-2.9.8.bz2 \
                         /usr/share/dict/cracklib-words.bz2    &&

bunzip2 -v               /usr/share/dict/cracklib-words.bz2    &&
ln -v -sf cracklib-words /usr/share/dict/words                 &&
echo $(hostname) >>      /usr/share/dict/cracklib-extra-words  &&
install -v -m755 -d      /usr/lib/cracklib                     &&

create-cracklib-dict     /usr/share/dict/cracklib-words \
                         /usr/share/dict/cracklib-extra-words
```

- 4. remove

```bash
cd ..
rm -rf cracklib-2.9.8
```


## 9.7 libpwquality-1.4.5

- <https://github.com/libpwquality/libpwquality/releases/download/libpwquality-1.4.5/libpwquality-1.4.5.tar.bz2>

- 1. unzip

```bash
tar -xf libpwquality-1.4.5.tar.bz2
cd libpwquality-1.4.5
```

- 2. build

```bash
./configure --prefix=/usr                      \
            --disable-static                   \
            --with-securedir=/usr/lib/security \
            --with-python-binary=python3       &&
make
```

- 3. install

```bash
su -c "make install"
```

- 4. Configuring

```bash
mv /etc/pam.d/system-password{,.orig} &&
cat > /etc/pam.d/system-password << "EOF"
# Begin /etc/pam.d/system-password

# check new passwords for strength (man pam_pwquality)
password  required    pam_pwquality.so   authtok_type=UNIX retry=1 difok=1 \
                                         minlen=8 dcredit=0 ucredit=0 \
                                         lcredit=0 ocredit=0 minclass=1 \
                                         maxrepeat=0 maxsequence=0 \
                                         maxclassrepeat=0 gecoscheck=0 \
                                         dictcheck=1 usercheck=1 \
                                         enforcing=1 badwords="" \
                                         dictpath=/usr/lib/cracklib/pw_dict
# use sha512 hash for encryption, use shadow, and use the
# authentication token (chosen password) set by pam_pwquality
# above (or any previous modules). Also set the number of crypt rounds
# to the value used in shadow.
password  required    pam_unix.so        sha512 shadow use_authtok \
                                         rounds=500000

# End /etc/pam.d/system-password
EOF
```

- 5. remove

```bash
cd ..
rm -rf libpwquality-1.4.5
``` 


## 9.8 Linux-PAM-1.5.2

- <https://github.com/linux-pam/linux-pam/releases/download/v1.5.2/Linux-PAM-1.5.2.tar.xz>
- <https://github.com/linux-pam/linux-pam/releases/download/v1.5.2/Linux-PAM-1.5.2-docs.tar.xz>

- 1. unzip

```bash
tar -xf Linux-PAM-1.5.2.tar.xz
cd Linux-PAM-1.5.2
```

- 2. build

```bash
sed -e /service_DATA/d \
    -i modules/pam_namespace/Makefile.am &&
autoreconf
```

```bash
tar -xf ../Linux-PAM-1.5.2-docs.tar.xz --strip-components=1
```

```bash
sed -e 's/dummy elinks/dummy lynx/'                                    \
    -e 's/-no-numbering -no-references/-force-html -nonumbers -stdin/' \
    -i configure
```

```bash
./configure --prefix=/usr                        \
            --sbindir=/usr/sbin                  \
            --sysconfdir=/etc                    \
            --libdir=/usr/lib                    \
            --enable-securedir=/usr/lib/security \
            --docdir=/usr/share/doc/Linux-PAM-1.5.2 &&
make
```

- 3. install

```bash
su
```

```bash
install -v -m755 -d /etc/pam.d &&

cat > /etc/pam.d/other << "EOF"
auth     required       pam_deny.so
account  required       pam_deny.so
password required       pam_deny.so
session  required       pam_deny.so
EOF
```

```bash
rm -fv /etc/pam.d/other
```

```bash
make install &&
chmod -v 4755 /usr/sbin/unix_chkpwd
```

```bash
exit
```

- 4. configuring

```bash
install -vdm755 /etc/pam.d &&
cat > /etc/pam.d/system-account << "EOF" &&
# Begin /etc/pam.d/system-account

account   required    pam_unix.so

# End /etc/pam.d/system-account
EOF

cat > /etc/pam.d/system-auth << "EOF" &&
# Begin /etc/pam.d/system-auth

auth      required    pam_unix.so

# End /etc/pam.d/system-auth
EOF

cat > /etc/pam.d/system-session << "EOF" &&
# Begin /etc/pam.d/system-session

session   required    pam_unix.so

# End /etc/pam.d/system-session
EOF

cat > /etc/pam.d/system-password << "EOF"
# Begin /etc/pam.d/system-password

# use sha512 hash for encryption, use shadow, and try to use any previously
# defined authentication token (chosen password) set by any prior module.
# Use the same number of rounds as shadow.
password  required    pam_unix.so       sha512 shadow try_first_pass \
                                        rounds=500000

# End /etc/pam.d/system-password
EOF
```

```bash
cat > /etc/pam.d/other << "EOF"
# Begin /etc/pam.d/other

auth        required        pam_warn.so
auth        required        pam_deny.so
account     required        pam_warn.so
account     required        pam_deny.so
password    required        pam_warn.so
password    required        pam_deny.so
session     required        pam_warn.so
session     required        pam_deny.so

# End /etc/pam.d/other
EOF
```

- 5. remove

```bash
cd ..
rm -rf Linux-PAM-1.5.2
```

## 9.9 Shadow-4.13

- <https://github.com/shadow-maint/shadow/releases/download/4.13/shadow-4.13.tar.xz>

- 1. unzip

```bash
tar -xf shadow-4.13.tar.xz
cd shadow-4.13
```

- 2. build

```bash
sed -i 's@DICTPATH.*@DICTPATH\t/lib/cracklib/pw_dict@' etc/login.defs
```

```bash
sed -i 's/groups$(EXEEXT) //' src/Makefile.in          &&

find man -name Makefile.in -exec sed -i 's/groups\.1 / /'   {} \; &&
find man -name Makefile.in -exec sed -i 's/getspnam\.3 / /' {} \; &&
find man -name Makefile.in -exec sed -i 's/passwd\.5 / /'   {} \; &&

sed -e 's@#ENCRYPT_METHOD DES@ENCRYPT_METHOD SHA512@' \
    -e 's@#\(SHA_CRYPT_..._ROUNDS 5000\)@\100@'       \
    -e 's@/var/spool/mail@/var/mail@'                 \
    -e '/PATH=/{s@/sbin:@@;s@/bin:@@}'                \
    -i etc/login.defs                                 &&

./configure --sysconfdir=/etc               \
            --disable-static                \
            --with-group-name-max-length=32 &&
make
```

- 3. install

```bash
su -c "make exec_prefix=/usr install"
```

```bash
su -c "make -C man install-man"
```

- 4. configuring

```bash
su
```

```bash
install -v -m644 /etc/login.defs /etc/login.defs.orig &&
for FUNCTION in FAIL_DELAY               \
                FAILLOG_ENAB             \
                LASTLOG_ENAB             \
                MAIL_CHECK_ENAB          \
                OBSCURE_CHECKS_ENAB      \
                PORTTIME_CHECKS_ENAB     \
                QUOTAS_ENAB              \
                CONSOLE MOTD_FILE        \
                FTMP_FILE NOLOGINS_FILE  \
                ENV_HZ PASS_MIN_LEN      \
                SU_WHEEL_ONLY            \
                CRACKLIB_DICTPATH        \
                PASS_CHANGE_TRIES        \
                PASS_ALWAYS_WARN         \
                CHFN_AUTH ENCRYPT_METHOD \
                ENVIRON_FILE
do
    sed -i "s/^${FUNCTION}/# &/" /etc/login.defs
done
```

```bash
cat > /etc/pam.d/login << "EOF"
# Begin /etc/pam.d/login

# Set failure delay before next prompt to 3 seconds
auth      optional    pam_faildelay.so  delay=3000000

# Check to make sure that the user is allowed to login
auth      requisite   pam_nologin.so

# Check to make sure that root is allowed to login
# Disabled by default. You will need to create /etc/securetty
# file for this module to function. See man 5 securetty.
#auth      required    pam_securetty.so

# Additional group memberships - disabled by default
#auth      optional    pam_group.so

# include system auth settings
auth      include     system-auth

# check access for the user
account   required    pam_access.so

# include system account settings
account   include     system-account

# Set default environment variables for the user
session   required    pam_env.so

# Set resource limits for the user
session   required    pam_limits.so

# Display date of last login - Disabled by default
#session   optional    pam_lastlog.so

# Display the message of the day - Disabled by default
#session   optional    pam_motd.so

# Check user's mail - Disabled by default
#session   optional    pam_mail.so      standard quiet

# include system session and password settings
session   include     system-session
password  include     system-password

# End /etc/pam.d/login
EOF
```

```bash
cat > /etc/pam.d/passwd << "EOF"
# Begin /etc/pam.d/passwd

password  include     system-password

# End /etc/pam.d/passwd
EOF
```

```bash
cat > /etc/pam.d/su << "EOF"
# Begin /etc/pam.d/su

# always allow root
auth      sufficient  pam_rootok.so

# Allow users in the wheel group to execute su without a password
# disabled by default
#auth      sufficient  pam_wheel.so trust use_uid

# include system auth settings
auth      include     system-auth

# limit su to users in the wheel group
# disabled by default
#auth      required    pam_wheel.so use_uid

# include system account settings
account   include     system-account

# Set default environment variables for the service user
session   required    pam_env.so

# include system session settings
session   include     system-session

# End /etc/pam.d/su
EOF
```

```bash
cat > /etc/pam.d/chpasswd << "EOF"
# Begin /etc/pam.d/chpasswd

# always allow root
auth      sufficient  pam_rootok.so

# include system auth and account settings
auth      include     system-auth
account   include     system-account
password  include     system-password

# End /etc/pam.d/chpasswd
EOF

sed -e s/chpasswd/newusers/ /etc/pam.d/chpasswd >/etc/pam.d/newusers
```

```bash
for PROGRAM in chfn chgpasswd chsh groupadd groupdel \
               groupmems groupmod useradd userdel usermod
do
    install -v -m644 /etc/pam.d/chage /etc/pam.d/${PROGRAM}
    sed -i "s/chage/$PROGRAM/" /etc/pam.d/${PROGRAM}
done
```

```bash
if [ -f /etc/login.access ]; then mv -v /etc/login.access{,.NOUSE}; fi
```

```bash
if [ -f /etc/limits ]; then mv -v /etc/limits{,.NOUSE}; fi
```

```bash
cat > /etc/pam.d/chage << "EOF"
# Begin /etc/pam.d/chage

# always allow root
auth      sufficient  pam_rootok.so

# include system auth and account settings
auth      include     system-auth
account   include     system-account

# End /etc/pam.d/chage
EOF
```

```bash
exit
```

- 5. remove

```bash
cd ..
rm -rf shadow-4.13
```

# 10.revisiting shadow
--------

None.


# 11. sudo
--------

## 11.1 cyrus-sasl-2.1.28

- <https://github.com/cyrusimap/cyrus-sasl/releases/download/cyrus-sasl-2.1.28/cyrus-sasl-2.1.28.tar.gz>

- 1. unzip

```bash
tar -xf cyrus-sasl-2.1.28.tar.gz
cd cyrus-sasl-2.1.28
```

- 2. build

```bash
./configure --prefix=/usr        \
            --sysconfdir=/etc    \
            --enable-auth-sasldb \
            --with-dbpath=/var/lib/sasl/sasldb2 \
            --with-sphinx-build=no              \
            --with-ldap                         \
            --with-saslauthd=/var/run/saslauthd &&
make -j1
```

- 3. install

```bash
su
```

```bash
make install &&
install -v -dm755                          /usr/share/doc/cyrus-sasl-2.1.28/html &&
install -v -m644  saslauthd/LDAP_SASLAUTHD /usr/share/doc/cyrus-sasl-2.1.28      &&
install -v -m644  doc/legacy/*.html        /usr/share/doc/cyrus-sasl-2.1.28/html &&
install -v -dm700 /var/lib/sasl
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf cyrus-sasl-2.1.28
```

## 11.2 openldap-2.6.4

- <https://www.openldap.org/software/download/OpenLDAP/openldap-release/openldap-2.6.4.tgz>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/openldap-2.6.4-consolidated-1.patch>

- 1. unzip

```bash
tar -xf openldap-2.6.4.tgz
cd openldap-2.6.4
```

- 2. build

```bash
patch -Np1 -i ../openldap-2.6.4-consolidated-1.patch &&
autoconf &&

./configure --prefix=/usr     \
            --sysconfdir=/etc \
            --disable-static  \
            --enable-dynamic  \
            --enable-versioning=yes  \
            --disable-debug   \
            --disable-slapd &&

make depend &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf openldap-2.6.4
```

## 11.3 procmail-3.22

- <https://ftp.osuosl.org/pub/blfs/conglomeration/procmail/procmail-3.22.tar.gz>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/procmail-3.22-consolidated_fixes-1.patch>

- 1. unzip

```bash
tar -xf procmail-3.22.tar.gz
cd procmail-3.22
```

- 2. install

```bash
su
```

```bash
sed -i 's/getline/get_line/' src/*.[ch]                   &&
patch -Np1 -i ../procmail-3.22-consolidated_fixes-1.patch &&

make LOCKINGTEST=/tmp MANDIR=/usr/share/man install       &&
make install-suid
```

```bash
exit
```

- 3. remove

```bash
cd ..
rm -rf procmail-3.22
```

## 11.4 sendmail.8.17.1

- <https://ftp.sendmail.org/sendmail.8.17.1.tar.gz>

- 1. unzip

```bash
tar -xf sendmail.8.17.1.tar.gz
cd sendmail.8.17.1
```

- 2. install

```bash
su
```

```bash
groupadd -g 26 smmsp                               &&
useradd -c "Sendmail Daemon" -g smmsp -d /dev/null \
        -s /bin/false -u 26 smmsp                  &&
chmod -v 1777 /var/mail                            &&
install -v -m700 -d /var/spool/mqueue
```

```bash
exit
```

```bash
cat >> devtools/Site/site.config.m4 << "EOF"
APPENDDEF(`confENVDEF',`-DOPENSSL -DSASL -DLDAPMAP -DHASFLOCK')
APPENDDEF(`confLIBS', `-lssl -lcrypto -lsasl2 -lldap -llber -ldb')
APPENDDEF(`confINCDIRS', `-I/usr/include/sasl')
EOF
```

```bash
cat >> devtools/Site/site.config.m4 << "EOF"
define(`confMANGRP',`root')
define(`confMANOWN',`root')
define(`confSBINGRP',`root')
define(`confUBINGRP',`root')
define(`confUBINOWN',`root')
EOF

sed -i 's|/usr/man/man|/usr/share/man/man|' \
    devtools/OS/Linux           &&

cd sendmail                     &&
sh Build                        &&
cd ../cf/cf                     &&
cp generic-linux.mc sendmail.mc &&
sh Build sendmail.cf
```

```bash
su
```

```bash
chown phoebus:phoebus devtools/Site/site.config.m4
```

```bash
install -v -d -m755 /etc/mail &&
sh Build install-cf &&

cd ../..            &&
sh Build install    &&

install -v -m644 cf/cf/{submit,sendmail}.mc /etc/mail &&
cp -v -R cf/* /etc/mail                               &&

install -v -m755 -d /usr/share/doc/sendmail-8.17.1/{cf,sendmail} &&

install -v -m644 CACerts FAQ KNOWNBUGS LICENSE PGPKEYS README RELEASE_NOTES \
        /usr/share/doc/sendmail-8.17.1 &&

install -v -m644 sendmail/{README,SECURITY,TRACEFLAGS,TUNING} \
        /usr/share/doc/sendmail-8.17.1/sendmail &&

install -v -m644 cf/README /usr/share/doc/sendmail-8.17.1/cf &&

for manpage in sendmail editmap mailstats makemap praliases smrsh
do
    install -v -m644 $manpage/$manpage.8 /usr/share/man/man8
done &&

install -v -m644 sendmail/aliases.5    /usr/share/man/man5 &&
install -v -m644 sendmail/mailq.1      /usr/share/man/man1 &&
install -v -m644 sendmail/newaliases.1 /usr/share/man/man1 &&
install -v -m644 vacation/vacation.1   /usr/share/man/man1
```

```bash
exit
```

```bash
cd doc/op                                       &&
sed -i 's/groff/GROFF_NO_SGR=1 groff/' Makefile &&
make op.txt op.pdf
```

```bash
su
```

```bash
install -v -d -m755 /usr/share/doc/sendmail-8.17.1 &&
install -v -m644 op.ps op.txt op.pdf /usr/share/doc/sendmail-8.17.1 &&
cd ../..
```

```bash
exit
```

- 3. configuring

```bash
su
```

```bash
echo $(hostname) > /etc/mail/local-host-names
cat > /etc/mail/aliases << "EOF"
postmaster: root
MAILER-DAEMON: root

EOF
```

```bash
chmod g-w /etc/mail/aliases
```

```bash
newaliases
```

```bash
cd /etc/mail &&
m4 m4/cf.m4 sendmail.mc > sendmail.cf
```

```bash
cd /sources/BLFS/blfs-bootscripts-20230101
make install-sendmail
```

```bash
exit
```

- 4. remove

```bash
cd /sources/BLFS
rm -rf sendmail.8.17.1
```


## 11.5 reinstall procmail-3.22

- <https://ftp.osuosl.org/pub/blfs/conglomeration/procmail/procmail-3.22.tar.gz>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/procmail-3.22-consolidated_fixes-1.patch>

- 1. unzip

```bash
tar -xf procmail-3.22.tar.gz
cd procmail-3.22
```

- 2. install

```bash
su
```

```bash
/etc/init.d/sendmail start
```

```bash
sed -i 's/getline/get_line/' src/*.[ch]                   &&
patch -Np1 -i ../procmail-3.22-consolidated_fixes-1.patch &&

make LOCKINGTEST=/tmp MANDIR=/usr/share/man install       &&
make install-suid
```

```bash
exit
```

- 3. remove

```bash
cd ..
rm -rf procmail-3.22
```

## 11.6 sudo-1.9.13p1

- <https://www.sudo.ws/dist/sudo-1.9.13p1.tar.gz>

- 1. unzip

```bash
tar -xf sudo-1.9.13p1.tar.gz
cd sudo-1.9.13p1
```

- 2. build

```bash
./configure --prefix=/usr              \
            --libexecdir=/usr/lib      \
            --with-secure-path         \
            --with-all-insults         \
            --with-env-editor          \
            --docdir=/usr/share/doc/sudo-1.9.13p1 \
            --with-passprompt="[sudo] password for %p: " &&
make
```

- 3. install

```bash
su
```

```bash
make install &&
ln -sfv libsudo_util.so.0.0.0 /usr/lib/sudo/libsudo_util.so.0
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf sudo-1.9.13p1
```

# 12. reinstall wget
--------

## 12.1 pcre2-10.42

- <https://github.com/PCRE2Project/pcre2/releases/download/pcre2-10.42/pcre2-10.42.tar.bz2>

- 1. unzip

```bash
tar -xf pcre2-10.42.tar.bz2
cd pcre2-10.42
```

- 2. build

```bash
./configure --prefix=/usr                       \
            --docdir=/usr/share/doc/pcre2-10.42 \
            --enable-unicode                    \
            --enable-jit                        \
            --enable-pcre2-16                   \
            --enable-pcre2-32                   \
            --enable-pcre2grep-libz             \
            --enable-pcre2grep-libbz2           \
            --enable-pcre2test-libreadline      \
            --disable-static                    &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf pcre2-10.42
```

## 12.2 libunistring-1.1

- <https://ftp.gnu.org/gnu/libunistring/libunistring-1.1.tar.xz>

- 1. unzip

```bash
tar -xf libunistring-1.1
```

- 2. build

```bash
./configure --prefix=/usr    \
            --disable-static \
            --docdir=/usr/share/doc/libunistring-1.1 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libunistring-1.1
```

## 12.3 libidn2-2.3.4

- <https://ftp.gnu.org/gnu/libidn/libidn2-2.3.4.tar.gz>

- 1. unzip

```bash
tar -xf libidn2-2.3.4.tar.gz
cd libidn2-2.3.4
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libidn2-2.3.4
```

## 12.4 libpsl-0.21.2

- <https://github.com/rockdaboot/libpsl/releases/download/0.21.2/libpsl-0.21.2.tar.gz>

- 1. unzip

```bash
tar -xf libpsl-0.21.2.tar.gz
cd libpsl-0.21.2
```

- 2. build

```bash
sed -i 's/env python/&3/' src/psl-make-dafsa              &&
./configure --prefix=/usr --disable-static PYTHON=python3 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libpsl-0.21.2
```

## 12.5 wget-1.21.3

- <https://ftp.gnu.org/gnu/wget/wget-1.21.3.tar.gz>

- 1. unzip

```bash
tar -xf wget-1.21.3.tar.gz
cd wget-1.21.3
```

- 2. build

```bash
./configure --prefix=/usr      \
            --sysconfdir=/etc  \
            --with-ssl=openssl &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf wget-1.21.3
```


# 13. reinstall Lynx
--------

## 13.1 zip30

- <https://downloads.sourceforge.net/infozip/zip30.tar.gz>

- 1. unzip

```bash
tar -xf zip30.tar.gz
cd zip30
```

- 2. build

```bash
make -f unix/Makefile generic_gcc
```

- 3. install

```bash
sudo make prefix=/usr MANDIR=/usr/share/man/man1 -f unix/Makefile install
```

- 4. remove

```bash
cd ..
rm -rf zip30
```

## 13.2 lynx2.8.9rel.1

- <https://invisible-mirror.net/archives/lynx/tarballs/lynx2.8.9rel.1.tar.bz2>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/lynx-2.8.9rel.1-security_fix-1.patch>

- 1. unzip

```bash
tar -xf lynx2.8.9rel.1.tar.bz2c
cd lynx2.8.9rel.1
```

- 2. build

```bash
patch -p1 -i ../lynx-2.8.9rel.1-security_fix-1.patch
```

```bash
./configure --prefix=/usr          \
            --sysconfdir=/etc/lynx \
            --datadir=/usr/share/doc/lynx-2.8.9rel.1 \
            --with-zlib            \
            --with-bzlib           \
            --with-ssl             \
            --with-screen=ncursesw \
            --enable-nls           \
            --enable-locale-charset &&
make
```

- 3. install

```bash
sudo su
```

```bash
make install-full &&
chgrp -v -R root /usr/share/doc/lynx-2.8.9rel.1/lynx_doc
```

```bash
exit
```

- 4. configuring

```bash
sudo su
```

```bash
sed -e '/#LOCALE/     a LOCALE_CHARSET:TRUE'     \
    -i /etc/lynx/lynx.cfg
```

```bash
sed -e '/#DEFAULT_ED/ a DEFAULT_EDITOR:vi'       \
    -i /etc/lynx/lynx.cfg
```

```bash
sed -e '/#PERSIST/    a PERSISTENT_COOKIES:TRUE' \
    -i /etc/lynx/lynx.cfg
```

```bash
exit
```

- 5. remove

```bash
cd ..
rm -rf lynx2.8.9rel.1
```

================

# 14. X window 1
----

## 14.1 Introduction to Xorg-7

- 1. configuring

```bash
export XORG_PREFIX="/opt/xorg"
```

```bash
export XORG_CONFIG="--prefix=$XORG_PREFIX --sysconfdir=/etc \
    --localstatedir=/var --disable-static"
```

```bash
sudo su
```

```bash
cat > /etc/profile.d/xorg.sh << EOF
XORG_PREFIX="$XORG_PREFIX"
XORG_CONFIG="--prefix=\$XORG_PREFIX --sysconfdir=/etc --localstatedir=/var --disable-static"
export XORG_PREFIX XORG_CONFIG
EOF
chmod 644 /etc/profile.d/xorg.sh
```

```bash
cat > /etc/sudoers.d/xorg << EOF
Defaults env_keep += XORG_PREFIX
Defaults env_keep += XORG_CONFIG
EOF
```

```bash
cat >> /etc/profile.d/xorg.sh << "EOF"

pathappend $XORG_PREFIX/bin             PATH
pathappend $XORG_PREFIX/lib/pkgconfig   PKG_CONFIG_PATH
pathappend $XORG_PREFIX/share/pkgconfig PKG_CONFIG_PATH

pathappend $XORG_PREFIX/lib             LIBRARY_PATH
pathappend $XORG_PREFIX/include         C_INCLUDE_PATH
pathappend $XORG_PREFIX/include         CPLUS_INCLUDE_PATH

ACLOCAL="aclocal -I $XORG_PREFIX/share/aclocal"

export PATH PKG_CONFIG_PATH ACLOCAL LIBRARY_PATH C_INCLUDE_PATH CPLUS_INCLUDE_PATH
EOF
```

```bash
source /etc/profile.d/xorg.sh
```

```bash
echo "$XORG_PREFIX/lib" >> /etc/ld.so.conf
```

```bash
sed -e "s@X11R6/man@X11R6/share/man@g" \
    -e "s@/usr/X11R6@$XORG_PREFIX@g"   \
    -i /etc/man_db.conf
```

```bash
ln -svf $XORG_PREFIX/share/X11 /usr/share/X11
```

```bash
ln -svf $XORG_PREFIX /usr/X11R6
```

```bash
exit
```


# 15. X Window 2
----

## 15.1 util-macros-1.20.0

- <https://www.x.org/pub/individual/util/util-macros-1.20.0.tar.xz>

- 1. unzip

```bash
tar -xf util-macros-1.20.0.tar.xz
cd util-macros-1.20.0
```

- 2. build

```bash
./configure $XORG_CONFIG
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf util-macros-1.20.0
```

## 15.2 xorgproto-2022.2

- <https://xorg.freedesktop.org/archive/individual/proto/xorgproto-2022.2.tar.xz>

- 1. unzip

```bash
tar -xf xorgproto-2022.2.tar.xz
cd xorgproto-2022.2
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=$XORG_PREFIX -Dlegacy=true .. &&
ninja
```

- 3. install

```bash
sudo su
```

```bash
ninja install &&
mv -v $XORG_PREFIX/share/doc/xorgproto{,-2022.2}
```

```bash
exit
```

- 4. remove

```bash
cd ../..
rm -rf xorgproto-2022.2
```

## 15.3 libXau-1.0.11

- <https://www.x.org/pub/individual/lib/libXau-1.0.11.tar.xz>

- 1. unzip

```bash
tar -xf libXau-1.0.11.tar.xz
cd libXau-1.0.11
```

- 2. build

```bash
./configure $XORG_CONFIG &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libXau-1.0.11
```

## 15.4 libXdmcp-1.1.4

- <https://www.x.org/pub/individual/lib/libXdmcp-1.1.4.tar.xz>

- 1. unzip

```bash
tar -xf libXdmcp-1.1.4.tar.xz
cd libXdmcp-1.1.4
```

- 2. build

```bash
./configure $XORG_CONFIG --docdir=/usr/share/doc/libXdmcp-1.1.4 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libXdmcp-1.1.4
```

## 15.5 icu4c-72_1-src

- <https://github.com/unicode-org/icu/releases/download/release-72-1/icu4c-72_1-src.tgz>

- 1. unzip

```bash
tar -xf icu4c-72_1-src.tgz
cd icu4c-72_1-src
```

- 2. build

```bash
cd source                                    &&

./configure --prefix=/usr                    &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf icu4c-72_1-src
```

## 15.6 libxml2-2.10.3

- <https://download.gnome.org/sources/libxml2/2.10/libxml2-2.10.3.tar.xz>
- <https://www.w3.org/XML/Test/xmlts20130923.tar.gz>

- 1. unzip

```bash
tar -xf libxml2-2.10.3.tar.xz
cd libxml2-2.10.3
```

- 2. build

```bash
./configure --prefix=/usr           \
            --sysconfdir=/etc       \
            --disable-static        \
            --with-history          \
            --with-icu              \
            PYTHON=/usr/bin/python3 \
            --docdir=/usr/share/doc/libxml2-2.10.3 &&
make
```

```bash
tar xf ../xmlts20130923.tar.gz
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libxml2-2.10.3
```


# 16. X Window 3
----

## 16.1 xcb-proto-1.15.2

- <https://xorg.freedesktop.org/archive/individual/proto/xcb-proto-1.15.2.tar.xz>

- 1. unzip

```bash
tar -xf xcb-proto-1.15.2.tar.xz
cd xcb-proto-1.15.2
```

- 2. build

```bash
PYTHON=python3 ./configure $XORG_CONFIG
```

- 3. install

```bash
sudo make install
sudo rm -f $XORG_PREFIX/lib/pkgconfig/xcb-proto.pc
```

- 4. remove

```bash
cd ..
rm -rf xcb-proto-1.15.2
```

## 16.2 libxcb-1.15

- <https://xorg.freedesktop.org/archive/individual/lib/libxcb-1.15.tar.xz>

- 1. unzip

```bash
tar -xf libxcb-1.15.tar.xz
cd libxcb-1.15
```

- 2. build

```bash
PYTHON=python3                \
./configure $XORG_CONFIG      \
            --without-doxygen \
            --docdir='${datadir}'/doc/libxcb-1.15 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libxcb-1.15
```

## 16.3 pcre-8.45

- <https://sourceforge.net/projects/pcre/files/pcre/8.45/pcre-8.45.tar.bz2>

- 1. unzip

```bash
tar -xf pcre-8.45.tar.bz2
cd pcre-8.45
```

- 2. build

```bash
./configure --prefix=/usr                     \
            --docdir=/usr/share/doc/pcre-8.45 \
            --enable-unicode-properties       \
            --enable-pcre16                   \
            --enable-pcre32                   \
            --enable-pcregrep-libz            \
            --enable-pcregrep-libbz2          \
            --enable-pcretest-libreadline     \
            --enable-jit                      \
            --disable-static                 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf pcre-8.45
```

## 16.4 dbus-1.14.6

- <https://dbus.freedesktop.org/releases/dbus/dbus-1.14.6.tar.xz>

- 1. unzip

```bash
tar -xf dbus-1.14.6.tar.xz
cd dbus-1.14.6
```

- 2. build

```bash
./configure --prefix=/usr                        \
            --sysconfdir=/etc                    \
            --localstatedir=/var                 \
            --runstatedir=/run                   \
            --enable-user-session                \
            --disable-doxygen-docs               \
            --disable-xml-docs                   \
            --disable-static                     \
            --with-systemduserunitdir=no         \
            --with-systemdsystemunitdir=no       \
            --docdir=/usr/share/doc/dbus-1.14.6  \
            --with-system-socket=/run/dbus/system_bus_socket &&
make
```

- 3. install

```bash
sudo make install
```

```bash
ln -sfv /var/lib/dbus/machine-id /etc
```

- 4. configuring

```bash
sudo su
```

```bash
cat > /etc/dbus-1/session-local.conf << "EOF"
<!DOCTYPE busconfig PUBLIC
 "-//freedesktop//DTD D-BUS Bus Configuration 1.0//EN"
 "http://www.freedesktop.org/standards/dbus/1.0/busconfig.dtd">
<busconfig>

  <!-- Search for .service files in /usr/local -->
  <servicedir>/usr/local/share/dbus-1/services</servicedir>

</busconfig>
EOF
```

```bash
cd /sources/BLFS/blfs-bootscripts-20230101/
sudo make install-dbus
```

```bash
exit
```

- 5. remove

```bash
cd /sources/BLFS
rm -rf dbus-1.14.6
```

## 16.5 elogind-246.10

- <https://github.com/elogind/elogind/archive/v246.10/elogind-246.10.tar.gz>

- 1. kernel configuration

```bash
cd /sources/linux-6.1.11
sudo su
make menuconfig
```

```bash
General setup --->
    [*]     Control Group support                        [CONFIG_CGROUPS]
File systems --->
    [*]     Inotify support for userspace                [CONFIG_INOTIFY_USER]
            Pseudo filesystems --->
                [*]    Tmpfs POSIX Access Control Lists  [CONFIG_TMPFS_POSIX_ACL]
```

```bash
-*- Cryptographic API --->                               [CONFIG_CRYPTO]
   <M/*> Userspace cryptographic algorithm configuration [CONFIG_CRYPTO_USER]
   <M/*> User-space interface for hash algorithms        [CONFIG_CRYPTO_USER_API_HASH]
```

```bash
make
```

- 2. install kernel

```bash
cp linux-6.1.11/arch/x86_64/boot/bzImage /boot/vmlinuz-6.1.11-lfs-11.3-2
cp linux-6.1.11/System.map /boot/System.map-6.1.11-2
cp linux-6.1.11/.config /boot/config-6.1.11-2
```

```bash
exit
```

- 3. unzip

```bash
tar -xf elogind-246.10.tar.gz
cd elogind-246.10
```

- 4. build

```bash
sed -i '/Disable polkit/,+8 d' meson.build &&

sed '/request_name/i\
r = sd_bus_set_exit_on_disconnect(m->bus, true);\
if (r < 0)\
    return log_error_errno(r, "Failed to set exit on disconnect: %m");' \
    -i src/login/logind.c &&

mkdir build &&
cd    build &&

meson --prefix=/usr                        \
      --buildtype=release                  \
      -Dcgroup-controller=elogind          \
      -Ddbuspolicydir=/etc/dbus-1/system.d \
      -Dman=auto                           \
      ..  &&
ninja
```

- 5. install

```bash
sudo su
```

```bash
ninja install                                           &&
ln -sfv  libelogind.pc /usr/lib/pkgconfig/libsystemd.pc &&
ln -sfvn elogind /usr/include/systemd
```

```bash
exit
```

- 6. configuring

```bash
sudo su
```

```bash
sed -e '/\[Login\]/a KillUserProcesses=no' \
    -i /etc/elogind/logind.conf
```

```bash
cat >> /etc/pam.d/system-session << "EOF" &&
# Begin elogind addition

session  required    pam_loginuid.so
session  optional    pam_elogind.so

# End elogind addition
EOF
cat > /etc/pam.d/elogind-user << "EOF"
# Begin /etc/pam.d/elogind-user

account  required    pam_access.so
account  include     system-account

session  required    pam_env.so
session  required    pam_limits.so
session  required    pam_unix.so
session  required    pam_loginuid.so
session  optional    pam_keyinit.so force revoke
session  optional    pam_elogind.so

auth     required    pam_deny.so
password required    pam_deny.so

# End /etc/pam.d/elogind-user
EOF
```

```bash
exit
```

- 7. remove

```bash
cd ../..
rm -rf elogind-246.10
```

## 16.6 freetype-2.13.0

- <https://downloads.sourceforge.net/freetype/freetype-2.13.0.tar.xz>
- <https://downloads.sourceforge.net/freetype/freetype-doc-2.13.0.tar.xz>

- 1. unzip

```bash
tar -xf freetype-2.13.0.tar.xz
cd freetype-2.13.0
```

- 2. build

```bash
tar -xf ../freetype-doc-2.13.0.tar.xz --strip-components=2 -C docs
```

```bash
sed -ri "s:.*(AUX_MODULES.*valid):\1:" modules.cfg &&

sed -r "s:.*(#.*SUBPIXEL_RENDERING) .*:\1:" \
    -i include/freetype/config/ftoption.h  &&

./configure --prefix=/usr --enable-freetype-config --disable-static &&
make
```

- 3. install

```bash
sudo make install
```

```bash
sudo su
```

```bash
install -v -m755 -d /usr/share/doc/freetype-2.13.0 &&
cp -v -R docs/*     /usr/share/doc/freetype-2.13.0 &&
rm -v /usr/share/doc/freetype-2.13.0/freetype-config.1
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf freetype-2.13.0
```

## 16.7 fontconfig-2.14.2

- <https://www.freedesktop.org/software/fontconfig/release/fontconfig-2.14.2.tar.xz>

- 1. unzip

```bash
tar -xf fontconfig-2.14.2.tar.xz
cd fontconfig-2.14.2
```

- 2. build

```bash
./configure --prefix=/usr        \
            --sysconfdir=/etc    \
            --localstatedir=/var \
            --disable-docs       \
            --docdir=/usr/share/doc/fontconfig-2.14.2 &&
make
```

- 3. install

```bash
sudo make install
```

```bash
sudo su
```

```bash
install -v -dm755 \
        /usr/share/{man/man{1,3,5},doc/fontconfig-2.14.2/fontconfig-devel} &&
install -v -m644 fc-*/*.1         /usr/share/man/man1 &&
install -v -m644 doc/*.3          /usr/share/man/man3 &&
install -v -m644 doc/fonts-conf.5 /usr/share/man/man5 &&
install -v -m644 doc/fontconfig-devel/* \
                                  /usr/share/doc/fontconfig-2.14.2/fontconfig-devel &&
install -v -m644 doc/*.{pdf,sgml,txt,html} \
                                  /usr/share/doc/fontconfig-2.14.2
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf fontconfig-2.14.2
```


# 17. X Window 4
----

## 17.1 Xorg Libraries

- <https://www.x.org/pub/individual/lib/xtrans-1.4.0.tar.bz2>
- <https://www.x.org/pub/individual/lib/libX11-1.8.4.tar.xz>
- <https://www.x.org/pub/individual/lib/libXext-1.3.5.tar.xz>
- <https://www.x.org/pub/individual/lib/libFS-1.0.9.tar.xz>
- <https://www.x.org/pub/individual/lib/libICE-1.1.1.tar.xz>
- <https://www.x.org/pub/individual/lib/libSM-1.2.4.tar.xz>
- <https://www.x.org/pub/individual/lib/libXScrnSaver-1.2.4.tar.xz>
- <https://www.x.org/pub/individual/lib/libXt-1.2.1.tar.bz2>
- <https://www.x.org/pub/individual/lib/libXmu-1.1.4.tar.xz>
- <https://www.x.org/pub/individual/lib/libXpm-3.5.15.tar.xz>
- <https://www.x.org/pub/individual/lib/libXaw-1.0.14.tar.bz2>
- <https://www.x.org/pub/individual/lib/libXfixes-6.0.0.tar.bz2>
- <https://www.x.org/pub/individual/lib/libXcomposite-0.4.6.tar.xz>
- <https://www.x.org/pub/individual/lib/libXrender-0.9.11.tar.xz>
- <https://www.x.org/pub/individual/lib/libXcursor-1.2.1.tar.xz>
- <https://www.x.org/pub/individual/lib/libXdamage-1.1.6.tar.xz>
- <https://www.x.org/pub/individual/lib/libfontenc-1.1.7.tar.xz>
- <https://www.x.org/pub/individual/lib/libXfont2-2.0.6.tar.xz>
- <https://www.x.org/pub/individual/lib/libXft-2.3.7.tar.xz>
- <https://www.x.org/pub/individual/lib/libXi-1.8.tar.bz2>
- <https://www.x.org/pub/individual/lib/libXinerama-1.1.5.tar.xz>
- <https://www.x.org/pub/individual/lib/libXrandr-1.5.3.tar.xz>
- <https://www.x.org/pub/individual/lib/libXres-1.2.2.tar.xz>
- <https://www.x.org/pub/individual/lib/libXtst-1.2.4.tar.xz>
- <https://www.x.org/pub/individual/lib/libXv-1.0.12.tar.xz>
- <https://www.x.org/pub/individual/lib/libXvMC-1.0.13.tar.xz>
- <https://www.x.org/pub/individual/lib/libXxf86dga-1.1.6.tar.xz>
- <https://www.x.org/pub/individual/lib/libXxf86vm-1.1.5.tar.xz>
- <https://www.x.org/pub/individual/lib/libdmx-1.1.4.tar.bz2>
- <https://www.x.org/pub/individual/lib/libpciaccess-0.17.tar.xz>
- <https://www.x.org/pub/individual/lib/libxkbfile-1.1.2.tar.xz>
- <https://www.x.org/pub/individual/lib/libxshmfence-1.3.2.tar.xz>

- 1. unzip

```bash
cd /sources/BLFS
mkdir lib
```

```bash
cat > lib-7.md5 << "EOF"
ce2fb8100c6647ee81451ebe388b17ad  xtrans-1.4.0.tar.bz2
e932752126240f0846b35eef6b1f2c3d  libX11-1.8.4.tar.xz
964942427fcc5a9fa0539661421857c8  libXext-1.3.5.tar.xz
742863a552ecd53cdb957b7b276213cc  libFS-1.0.9.tar.xz
b444a0e4c2163d1bbc7b046c3653eb8d  libICE-1.1.1.tar.xz
ffa434ed96ccae45533b3d653300730e  libSM-1.2.4.tar.xz
e613751d38e13aa0d0fd8e0149cec057  libXScrnSaver-1.2.4.tar.xz
b122ff9a7ec70c94dbbfd814899fffa5  libXt-1.2.1.tar.bz2
ed52d396115fbc4d05300762aab79685  libXmu-1.1.4.tar.xz
b3c58c94e284fd6940d3615e660a0007  libXpm-3.5.15.tar.xz
c1ce21c296bbf3da3e30cf651649563e  libXaw-1.0.14.tar.bz2
86f182f487f4f54684ef6b142096bb0f  libXfixes-6.0.0.tar.bz2
af0a5f0abb5b55f8411cd738cf0e5259  libXcomposite-0.4.6.tar.xz
ebf7fb3241ec03e8a3b2af72f03b4631  libXrender-0.9.11.tar.xz
4cdd1886fe5cce6f68554296edb46db8  libXcursor-1.2.1.tar.xz
ca55d29fa0a8b5c4a89f609a7952ebf8  libXdamage-1.1.6.tar.xz
6d3f1b15bb5b0bb71ae9f0a5103c1fc4  libfontenc-1.1.7.tar.xz
c179daa707f5f432f1bc13977e5bb329  libXfont2-2.0.6.tar.xz
69dc24ba444a0f6bd99423dbf8fd0260  libXft-2.3.7.tar.xz
74055672a111a98ce2841d2ec4057b05  libXi-1.8.tar.bz2
228c877558c265d2f63c56a03f7d3f21  libXinerama-1.1.5.tar.xz
850cbc7c921c9d5d0135734b114ff6ac  libXrandr-1.5.3.tar.xz
66c9e9e01b0b53052bb1d02ebf8d7040  libXres-1.2.2.tar.xz
02f128fbf809aa9c50d6e54c8e57cb2e  libXtst-1.2.4.tar.xz
70bfdd14ca1a563c218794413f0c1f42  libXv-1.0.12.tar.xz
11a358e7229fa28dc9801c1e64fe2e18  libXvMC-1.0.13.tar.xz
74d1acf93b83abeb0954824da0ec400b  libXxf86dga-1.1.6.tar.xz
5b913dac587f2de17a02e17f9a44a75f  libXxf86vm-1.1.5.tar.xz
d2f1f0ec68ac3932dd7f1d9aa0a7a11c  libdmx-1.1.4.tar.bz2
1466cf950c914ad2db1dbb76c9a724db  libpciaccess-0.17.tar.xz
8af2275955d40166bb647b14e4896ab1  libxkbfile-1.1.2.tar.xz
faa74f7483074ce7d4349e6bdc237497  libxshmfence-1.3.2.tar.xz
EOF
```

All packages download to /sources/BLFS/lib.

```bash
cd /sources/BLFS/lib
md5sum -c ../lib-7.md5
```

- 2. build & install & remove

```bash
as_root()
{
  if   [ $EUID = 0 ];        then $*
  elif [ -x /usr/bin/sudo ]; then sudo $*
  else                            su -c \\"$*\\"
  fi
}

export -f as_root
```

```bash
bash -e
```

```bash
for package in $(grep -v '^#' ../lib-7.md5 | awk '{print $2}')
do
  packagedir=${package%.tar.?z*}
  tar -xf $package
  pushd $packagedir
  docdir="--docdir=$XORG_PREFIX/share/doc/$packagedir"
  case $packagedir in
    libXfont2-[0-9]* )
      ./configure $XORG_CONFIG $docdir --disable-devel-docs
    ;;

    libXt-[0-9]* )
      ./configure $XORG_CONFIG $docdir \
                  --with-appdefaultdir=/etc/X11/app-defaults
    ;;

    libXpm-[0-9]* )
      sed -i '/TestAll.*TRUE/s|^|//|' test/TestAllFiles.h
      ./configure $XORG_CONFIG $docdir --disable-open-zfile
    ;;
    
    * )
      ./configure $XORG_CONFIG $docdir
    ;;
  esac

  make
  make check 2>&1 | tee ../$packagedir-make_check.log
  as_root make install
  popd
  rm -rf $packagedir
  as_root /sbin/ldconfig
done
```

```bash
exit
```

```bash
grep -A9 summary *make_check.log
```


# 18. X Window 5
----

## 18.1 xcb-util-0.4.1

- <https://xcb.freedesktop.org/dist/xcb-util-0.4.1.tar.xz>

- 1. unzip

```bash
tar -xf xcb-util-0.4.1.tar.xz
cd xcb-util-0.4.1
```

- 2. build

```bash
./configure $XORG_CONFIG &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xcb-util-0.4.1
```


## 18.2 xcb-util-image-0.4.1

- <https://xcb.freedesktop.org/dist/xcb-util-image-0.4.1.tar.xz>

- 1. unzip

```bash
tar -xf xcb-util-image-0.4.1.tar.xz
cd xcb-util-image-0.4.1
```

- 2. build

```bash
./configure $XORG_CONFIG &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xcb-util-image-0.4.1
```


## 18.3 xcb-util-keysyms-0.4.1

- <https://xcb.freedesktop.org/dist/xcb-util-keysyms-0.4.1.tar.xz>

- 1. unzip

```bash
tar -xf xcb-util-keysyms-0.4.1.tar.xz
cd xcb-util-keysyms-0.4.1
```

- 2. build

```bash
./configure $XORG_CONFIG &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xcb-util-keysyms-0.4.1
```

## 18.4 xcb-util-renderutil-0.3.10

- <https://xcb.freedesktop.org/dist/xcb-util-renderutil-0.3.10.tar.xz>

- 1. unzip

```bash
tar -xf xcb-util-renderutil-0.3.10.tar.xz
cd xcb-util-renderutil-0.3.10
```

- 2. build

```bash
./configure $XORG_CONFIG &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xcb-util-renderutil-0.3.10
```

## 18.5 xcb-util-wm-0.4.2

- <https://xcb.freedesktop.org/dist/xcb-util-wm-0.4.2.tar.xz>

- 1. unzip

```bash
tar -xf xcb-util-wm-0.4.2.tar.xz
cd xcb-util-wm-0.4.2
```

- 2. build

```bash
./configure $XORG_CONFIG &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xcb-util-wm-0.4.2
```

## 18.6 xcb-util-cursor-0.1.4

- <https://xcb.freedesktop.org/dist/xcb-util-cursor-0.1.4.tar.xz>

- 1. unzip

```bash
tar -xf xcb-util-cursor-0.1.4.tar.xz
cd xcb-util-cursor-0.1.4
```

- 2. build

```bash
./configure $XORG_CONFIG &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xcb-util-cursor-0.1.4
```


# 19.  Mesa 1
----

## 19.1 MarkupSafe-2.1.2

- <https://files.pythonhosted.org/packages/source/M/MarkupSafe/MarkupSafe-2.1.2.tar.gz>

- 1. unzip

```bash
tar -xf MarkupSafe-2.1.2.tar.gz
cd MarkupSafe-2.1.2
```

- 2. build

```bash
pip3 wheel -w dist --no-build-isolation --no-deps $PWD
```

- 3. install

```bash
sudo pip3 install --no-index --find-links dist --no-cache-dir --no-user markupsafe
```

- 4. remove

```bash
cd ..
rm -rf MarkupSafe-2.1.2
```

## 19.2 Mako-1.2.4

- <https://files.pythonhosted.org/packages/source/M/Mako/Mako-1.2.4.tar.gz>

- 1. unzip

```bash
tar -xf Mako-1.2.4.tar.gz
cd Mako-1.2.4
```

- 2. build

```bash
pip3 wheel -w dist --no-build-isolation --no-deps $PWD
```

- 3. install

```bash
sudo pip3 install --no-index --find-links dist --no-cache-dir --no-user Mako
```

- 4. remove

```bash
cd ..
rm -rf Mako-1.2.4
```

## 19.3 libdrm-2.4.115

- <https://dri.freedesktop.org/libdrm/libdrm-2.4.115.tar.xz>

- 1. configure kernel

```bash
cd /sources/linux-6.1.11
sudo su
make menuconfig
```

```bash
CONFIG_DRM_VMWGFX=y
CONFIG_DRM_VMWGFX_FBCON=y
CONFIG_DRM_VMWGFX_MKSSTATS=y
```

```bash
make
```

```bash
exit
```

- 2. install kernel

```bash
cp linux-6.1.11/arch/x86_64/boot/bzImage /boot/vmlinuz-6.1.11-lfs-11.3-2
cp linux-6.1.11/System.map /boot/System.map-6.1.11-2
cp linux-6.1.11/.config /boot/config-6.1.11-2
```

- 3. unzip

```bash
tar -xf libdrm-2.4.115.tar.xz
cd libdrm-2.4.115
```

- 4. build

```bash
mkdir build &&
cd    build &&

meson setup  --prefix=$XORG_PREFIX \
             --buildtype=release   \
             -Dudev=true           \
             -Dvalgrind=disabled   &&
ninja
```

- 5. install

```bash
sudo ninja install
```

- 6. remove

```bash
cd ../..
rm -rf libdrm-2.4.115
```


# 20.  Mesa 2
----

## 20.1 libuv-v1.44.2

- <https://dist.libuv.org/dist/v1.44.2/libuv-v1.44.2.tar.gz>

- 1. unzip

```bash
tar -xf libuv-v1.44.2.tar.gz
cd libuv-v1.44.2
```

- 2. build

```bash
sh autogen.sh                              &&
./configure --prefix=/usr --disable-static &&
make 
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libuv-v1.44.2
```

## 20.2 curl-7.88.1

- <https://curl.se/download/curl-7.88.1.tar.xz>

- 1. unzip

```bash
tar -xf curl-7.88.1.tar.xz
cd curl-7.88.1
```

- 2. build

```bash
./configure --prefix=/usr                           \
            --disable-static                        \
            --with-openssl                          \
            --enable-threaded-resolver              \
            --with-ca-path=/etc/ssl/certs &&
make
```

- 3. install

```bash
sudo su
```

```bash
make install &&

rm -rf docs/examples/.deps &&

find docs \( -name Makefile\* -o -name \*.1 -o -name \*.3 \) -exec rm {} \; &&

install -v -d -m755 /usr/share/doc/curl-7.88.1 &&
cp -v -R docs/*     /usr/share/doc/curl-7.88.1
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf curl-7.88.1
```

## 20.3 nettle-3.8.1

- <https://ftp.gnu.org/gnu/nettle/nettle-3.8.1.tar.gz>

- 1. unzip

```bash
tar -xf nettle-3.8.1.tar.gz
cd nettle-3.8.1
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo su
```

```bash
make install &&
chmod   -v   755 /usr/lib/lib{hogweed,nettle}.so &&
install -v -m755 -d /usr/share/doc/nettle-3.8.1 &&
install -v -m644 nettle.html /usr/share/doc/nettle-3.8.1
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf nettle-3.8.1
```

## 20.4 lzo-2.10

- <https://www.oberhumer.com/opensource/lzo/download/lzo-2.10.tar.gz>

- 1. unzip

```bash
tar -xf lzo-2.10.tar.gz
cd lzo-2.10
```

- 2. build

```bash
./configure --prefix=/usr                    \
            --enable-shared                  \
            --disable-static                 \
            --docdir=/usr/share/doc/lzo-2.10 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf lzo-2.10
```

## 20.5 libarchive-3.6.2

- <https://github.com/libarchive/libarchive/releases/download/v3.6.2/libarchive-3.6.2.tar.xz>

- 1. unzip

```bash
tar -xf libarchive-3.6.2.tar.xz
cd libarchive-3.6.2
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo make install
sudo sed -i "s/iconv //" /usr/lib/pkgconfig/libarchive.pc
```

- 4. remove

```bash
cd ..
rm -rf libarchive-3.6.2
```

## 20.6 nghttp2-1.52.0

- <https://github.com/nghttp2/nghttp2/releases/download/v1.52.0/nghttp2-1.52.0.tar.xz>

- 1. unzip

```bash
tar -xf nghttp2-1.52.0.tar.xz
cd nghttp2-1.52.0
```

- 2. build

```bash
./configure --prefix=/usr     \
            --disable-static  \
            --enable-lib-only \
            --docdir=/usr/share/doc/nghttp2-1.52.0 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf nghttp2-1.52.0
```


# 21. Mesa 3
----

## 21.1 cmake-3.25.2

- <https://cmake.org/files/v3.25/cmake-3.25.2.tar.gz>

- 1. unzip

```bash
tar -xf cmake-3.25.2.tar.gz
cd cmake-3.25.2
```

- 2. build

```bash
sed -i '/"lib64"/s/64//' Modules/GNUInstallDirs.cmake &&

./bootstrap --prefix=/usr        \
            --system-libs        \
            --mandir=/share/man  \
            --no-system-jsoncpp  \
            --no-system-librhash \
            --parallel=2         \
            --docdir=/share/doc/cmake-3.25.2 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf cmake-3.25.2
```


# 22. Mesa 4
----

## 22.1 libva-2.17.0

- <https://github.com/intel/libva/releases/download/2.17.0/libva-2.17.0.tar.bz2>
- <https://github.com/intel/intel-vaapi-driver/releases/download/2.4.1/intel-vaapi-driver-2.4.1.tar.bz2>

- 1. unzip

```bash
tar -xf libva-2.17.0.tar.bz2
cd libva-2.17.0
```

- 2. build

```bash
./configure $XORG_CONFIG &&
make
```

```bash
sudo make install
```

```bash
tar -xvf ../intel-vaapi-driver-2.4.1.tar.bz2 &&
cd intel-vaapi-driver-2.4.1
```

```bash
./configure $XORG_CONFIG &&
make
```

```bash
sudo make install
```

- 3. remove

```bash
cd /sources/BLFS
rm -rf libva-2.17.0
```


# 23. Mesa 5
----

## 23.1 llvm-15.0.7.src

- <https://github.com/llvm/llvm-project/releases/download/llvmorg-15.0.7/llvm-15.0.7.src.tar.xz>
- <https://anduin.linuxfromscratch.org/BLFS/llvm/llvm-cmake-15.0.7.src.tar.xz>
- <https://github.com/llvm/llvm-project/releases/download/llvmorg-15.0.7/clang-15.0.7.src.tar.xz>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/clang-15.0.7-enable_default_ssp-1.patch>
- <https://github.com/llvm/llvm-project/releases/download/llvmorg-15.0.7/compiler-rt-15.0.7.src.tar.xz>

- 1. unzip

```bash
tar -xf llvm-15.0.7.src.tar.xz
cd llvm-15.0.7.src
```

- 2. build

```bash
tar -xf ../llvm-cmake-15.0.7.src.tar.xz &&
sed '/LLVM_COMMON_CMAKE_UTILS/s@../cmake@cmake-15.0.7.src@' \
    -i CMakeLists.txt
```

```bash
tar -xf ../clang-15.0.7.src.tar.xz -C tools &&
mv tools/clang-15.0.7.src tools/clang
```

```bash
tar -xf ../compiler-rt-15.0.7.src.tar.xz -C projects &&
mv projects/compiler-rt-15.0.7.src projects/compiler-rt
```

```bash
grep -rl '#!.*python' | xargs sed -i '1s/python$/python3/'
```

```bash
patch -Np2 -d tools/clang <../clang-15.0.7-enable_default_ssp-1.patch
```

```bash
mkdir -v build &&
cd       build &&

CC=gcc CXX=g++                                  \
cmake -DCMAKE_INSTALL_PREFIX=/usr               \
      -DLLVM_ENABLE_FFI=ON                      \
      -DCMAKE_BUILD_TYPE=Release                \
      -DLLVM_BUILD_LLVM_DYLIB=ON                \
      -DLLVM_LINK_LLVM_DYLIB=ON                 \
      -DLLVM_ENABLE_RTTI=ON                     \
      -DLLVM_TARGETS_TO_BUILD="host;AMDGPU;BPF" \
      -DLLVM_BINUTILS_INCDIR=/usr/include       \
      -DLLVM_INCLUDE_BENCHMARKS=OFF             \
      -DCLANG_DEFAULT_PIE_ON_LINUX=ON           \
      -Wno-dev -G Ninja ..                      &&
ninja
```

```bash
ninja docs-clang-html docs-clang-man
```

- 3. install

```bash
sudo su
```

```bash
ninja install &&
cp bin/FileCheck /usr/bin
```

```bash
exit
```

- 4. remove

```bash
cd /sources/BLFS
rm -rf llvm-15.0.7.src
```


# 24. Mesa 6
----

## 24.1 mesa-22.3.5

- <https://mesa.freedesktop.org/archive/mesa-22.3.5.tar.xz>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/mesa-22.3.5-add_xdemos-1.patch>

- 1. unzip

```bash
tar -xf mesa-22.3.5.tar.xz
cd mesa-22.3.5
```

- 2. build

```bash
patch -Np1 -i ../mesa-22.3.5-add_xdemos-1.patch
```

```bash
mkdir build &&
cd    build &&

meson setup                   \
      --prefix=$XORG_PREFIX   \
      --buildtype=release     \
      -Dplatforms=x11,wayland \
      -Dgallium-drivers=auto  \
      -Dvulkan-drivers=""     \
      -Dvalgrind=disabled     \
      -Dlibunwind=disabled    \
      ..                      &&

ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf 
```


# 25. X Window 12
----

## 25.1 xbitmaps-1.1.2

- <https://www.x.org/pub/individual/data/xbitmaps-1.1.2.tar.bz2>

- 1. unzip

```bash
tar -xf xbitmaps-1.1.2.tar.bz2
cd xbitmaps-1.1.2
```

- 2. build

```bash
./configure $XORG_CONFIG
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xbitmaps-1.1.2
```


## 25.2 libpng-1.6.39

- <https://downloads.sourceforge.net/libpng/libpng-1.6.39.tar.xz>
- <https://downloads.sourceforge.net/sourceforge/libpng-apng/libpng-1.6.39-apng.patch.gz>

- 1. unzip

```bash
tar -xf libpng-1.6.39.tar.xz
cd libpng-1.6.39
```

- 2. build

```bash
gzip -cd ../libpng-1.6.39-apng.patch.gz | patch -p1
```

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo su
```

```bash
make install &&
mkdir -v /usr/share/doc/libpng-1.6.39 &&
cp -v README libpng-manual.txt /usr/share/doc/libpng-1.6.39
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf libpng-1.6.39
```


## 25.3 Xorg Applications

- <https://www.x.org/pub/individual/app/iceauth-1.0.9.tar.xz>
- <https://www.x.org/pub/individual/app/luit-1.1.1.tar.bz2>
- <https://www.x.org/pub/individual/app/mkfontscale-1.2.2.tar.xz>
- <https://www.x.org/pub/individual/app/sessreg-1.1.3.tar.xz>
- <https://www.x.org/pub/individual/app/setxkbmap-1.3.3.tar.xz>
- <https://www.x.org/pub/individual/app/smproxy-1.0.7.tar.xz>
- <https://www.x.org/pub/individual/app/x11perf-1.6.1.tar.bz2>
- <https://www.x.org/pub/individual/app/xauth-1.1.2.tar.xz>
- <https://www.x.org/pub/individual/app/xbacklight-1.2.3.tar.bz2>
- <https://www.x.org/pub/individual/app/xcmsdb-1.0.6.tar.xz>
- <https://www.x.org/pub/individual/app/xcursorgen-1.0.8.tar.xz>
- <https://www.x.org/pub/individual/app/xdpyinfo-1.3.3.tar.xz>
- <https://www.x.org/pub/individual/app/xdriinfo-1.0.7.tar.xz>
- <https://www.x.org/pub/individual/app/xev-1.2.5.tar.xz>
- <https://www.x.org/pub/individual/app/xgamma-1.0.7.tar.xz>
- <https://www.x.org/pub/individual/app/xhost-1.0.9.tar.xz>
- <https://www.x.org/pub/individual/app/xinput-1.6.3.tar.bz2>
- <https://www.x.org/pub/individual/app/xkbcomp-1.4.6.tar.xz>
- <https://www.x.org/pub/individual/app/xkbevd-1.1.5.tar.xz>
- <https://www.x.org/pub/individual/app/xkbutils-1.0.5.tar.xz>
- <https://www.x.org/pub/individual/app/xkill-1.0.6.tar.xz>
- <https://www.x.org/pub/individual/app/xlsatoms-1.1.4.tar.xz>
- <https://www.x.org/pub/individual/app/xlsclients-1.1.5.tar.xz>
- <https://www.x.org/pub/individual/app/xmessage-1.0.6.tar.xz>
- <https://www.x.org/pub/individual/app/xmodmap-1.0.11.tar.xz>
- <https://www.x.org/pub/individual/app/xpr-1.1.0.tar.xz>
- <https://www.x.org/pub/individual/app/xprop-1.2.6.tar.xz>
- <https://www.x.org/pub/individual/app/xrandr-1.5.2.tar.xz>
- <https://www.x.org/pub/individual/app/xrdb-1.2.1.tar.bz2>
- <https://www.x.org/pub/individual/app/xrefresh-1.0.7.tar.xz>
- <https://www.x.org/pub/individual/app/xset-1.2.5.tar.xz>
- <https://www.x.org/pub/individual/app/xsetroot-1.1.3.tar.xz>
- <https://www.x.org/pub/individual/app/xvinfo-1.1.5.tar.xz>
- <https://www.x.org/pub/individual/app/xwd-1.0.8.tar.bz2>
- <https://www.x.org/pub/individual/app/xwininfo-1.1.5.tar.bz2>
- <https://www.x.org/pub/individual/app/xwud-1.0.6.tar.xz>

- 1. unzip

```bash
cat > app-7.md5 << "EOF"
5d3feaa898875484b6b340b3888d49d8  iceauth-1.0.9.tar.xz
c4a3664e08e5a47c120ff9263ee2f20c  luit-1.1.1.tar.bz2
fd2e6e5a297ac2bf3d7d54799bf69de0  mkfontscale-1.2.2.tar.xz
05423bb42a006a6eb2c36ba10393de23  sessreg-1.1.3.tar.xz
2f72c7170cdbadc8ef786b2f9cfd4a69  setxkbmap-1.3.3.tar.xz
9f7a4305f0e79d5a46c3c7d02df9437d  smproxy-1.0.7.tar.xz
e96b56756990c56c24d2d02c2964456b  x11perf-1.6.1.tar.bz2
dbcf944eb59343b84799b2cc70aace16  xauth-1.1.2.tar.xz
5b6405973db69c0443be2fba8e1a8ab7  xbacklight-1.2.3.tar.bz2
82a90e2feaeab5c5e7610420930cc0f4  xcmsdb-1.0.6.tar.xz
89e81a1c31e4a1fbd0e431425cd733d7  xcursorgen-1.0.8.tar.xz
f67116760888f2e06486ee3d179875d2  xdpyinfo-1.3.3.tar.xz
34aff1f93fa54d6a64cbe4fee079e077  xdriinfo-1.0.7.tar.xz
61219e492511b3d78375da76defbdc97  xev-1.2.5.tar.xz
41afaa5a68cdd0de7e7ece4805a37f11  xgamma-1.0.7.tar.xz
48ac13856838d34f2e7fca8cdc1f1699  xhost-1.0.9.tar.xz
ac6b7432726008b2f50eba82b0e2dbe4  xinput-1.6.3.tar.bz2
a11d4d6eeda762f13818684c0670f89f  xkbcomp-1.4.6.tar.xz
05ce1abd8533a400572784b1186a44d0  xkbevd-1.1.5.tar.xz
cf65ca1aaf4c28772ca7993cfd122563  xkbutils-1.0.5.tar.xz
f62b99839249ce9a7a8bb71a5bab6f9d  xkill-1.0.6.tar.xz
da5b7a39702841281e1d86b7349a03ba  xlsatoms-1.1.4.tar.xz
ab4b3c47e848ba8c3e47c021230ab23a  xlsclients-1.1.5.tar.xz
f33841b022db1648c891fdc094014aee  xmessage-1.0.6.tar.xz
0d66e07595ea083871048c4b805d8b13  xmodmap-1.0.11.tar.xz
9cf272cba661f7acc35015f2be8077db  xpr-1.1.0.tar.xz
33c090d8632a300e63efbf36edd6a333  xprop-1.2.6.tar.xz
f822a8d5f233e609d27cc22d42a177cb  xrandr-1.5.2.tar.xz
85f04a810e2fb6b41ab872b421dce1b1  xrdb-1.2.1.tar.bz2
33b04489e417d73c90295bd2a0781cbb  xrefresh-1.0.7.tar.xz
18ff5cdff59015722431d568a5c0bad2  xset-1.2.5.tar.xz
fa9a24fe5b1725c52a4566a62dd0a50d  xsetroot-1.1.3.tar.xz
d698862e9cad153c5fefca6eee964685  xvinfo-1.1.5.tar.xz
f783a209f2e3fa13253cedb65eaf9cdb  xwd-1.0.8.tar.bz2
26d46f7ef0588d3392da3ad5802be420  xwininfo-1.1.5.tar.bz2
5ff5dc120e8e927dc3c331c7fee33fc3  xwud-1.0.6.tar.xz
EOF
```

```bash
cd /sources/BLFS
mkdir app
cd app/
```

Download all packages to /sources/BLFS/app.

```bash
md5sum -c ../app-7.md5
```

- 2. build & install & remove

```bash
as_root()
{
  if   [ $EUID = 0 ];        then $*
  elif [ -x /usr/bin/sudo ]; then sudo $*
  else                            su -c \\"$*\\"
  fi
}

export -f as_root
```

```bash
bash -e
```

```bash
for package in $(grep -v '^#' ../app-7.md5 | awk '{print $2}')
do
  packagedir=${package%.tar.?z*}
  tar -xf $package
  pushd $packagedir
     case $packagedir in
       luit-[0-9]* )
         sed -i -e "/D_XOPEN/s/5/6/" configure
       ;;
     esac

     ./configure $XORG_CONFIG
     make
     as_root make install
  popd
  rm -rf $packagedir
done
```

```bash
exit
```

```bash
as_root rm -f $XORG_PREFIX/bin/xkeystone
```


# 26. X Window 13
----

## 26.1 xcursor-themes-1.0.6

- <https://www.x.org/pub/individual/data/xcursor-themes-1.0.6.tar.bz2>

- 1. unzip

```bash
tar -xf xcursor-themes-1.0.6.tar.bz2
cd xcursor-themes-1.0.6
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xcursor-themes-1.0.6
```


## 26.2 Xorg Fonts

- <https://zenlayer.dl.sourceforge.net/project/dejavu/dejavu/2.37/dejavu-fonts-ttf-2.37.tar.bz2>
- <https://www.x.org/pub/individual/font/font-util-1.4.0.tar.xz>
- <https://www.x.org/pub/individual/font/encodings-1.0.6.tar.xz>
- <https://www.x.org/pub/individual/font/font-alias-1.0.4.tar.bz2>
- <https://www.x.org/pub/individual/font/font-adobe-utopia-type1-1.0.4.tar.bz2>
- <https://www.x.org/pub/individual/font/font-bh-ttf-1.0.3.tar.bz2>
- <https://www.x.org/pub/individual/font/font-bh-type1-1.0.3.tar.bz2>
- <https://www.x.org/pub/individual/font/font-ibm-type1-1.0.3.tar.bz2>
- <https://www.x.org/pub/individual/font/font-misc-ethiopic-1.0.4.tar.bz2>
- <https://www.x.org/pub/individual/font/font-xfree86-type1-1.0.4.tar.bz2>

- 1. unzip

```bash
cat > font-7.md5 << "EOF"
ec6cea7a46c96ed6be431dfbbb78f366  font-util-1.4.0.tar.xz
c2bcfdc52b8b6462228342cedae2fed9  encodings-1.0.6.tar.xz
0497de0176a0dfa5fac2b0552a4cf380  font-alias-1.0.4.tar.bz2
fcf24554c348df3c689b91596d7f9971  font-adobe-utopia-type1-1.0.4.tar.bz2
e8ca58ea0d3726b94fe9f2c17344be60  font-bh-ttf-1.0.3.tar.bz2
53ed9a42388b7ebb689bdfc374f96a22  font-bh-type1-1.0.3.tar.bz2
bfb2593d2102585f45daa960f43cb3c4  font-ibm-type1-1.0.3.tar.bz2
4ee18ab6c1edf636b8e75b73e6037371  font-misc-ethiopic-1.0.4.tar.bz2
3eeb3fb44690b477d510bbd8f86cf5aa  font-xfree86-type1-1.0.4.tar.bz2
EOF
```

```bash
cd /sources/BLFS
mkdir font
cd font/
```

Download all packages to /source/BLFS/font.

```bash
md5sum -c ../font-7.md5
```

- 2. install dejavu

```bash
tar -xf dejavu-fonts-ttf-2.37.tar.bz2
cd dejavu-fonts-ttf-2.37
```

```bash
install -v -d -m755 /usr/share/fonts/dejavu &&
install -v -m644 ttf/*.ttf /usr/share/fonts/dejavu &&
fc-cache -v /usr/share/fonts/dejavu
```

```bash
fc-list -b
```

- 3. build & install & remove

```bash
as_root()
{
  if   [ $EUID = 0 ];        then $*
  elif [ -x /usr/bin/sudo ]; then sudo $*
  else                            su -c \\"$*\\"
  fi
}

export -f as_root
```

```bash
bash -e
```

```bash
for package in $(grep -v '^#' ../font-7.md5 | awk '{print $2}')
do
  packagedir=${package%.tar.?z*}
  tar -xf $package
  pushd $packagedir
    ./configure $XORG_CONFIG
    make
    as_root make install
  popd
  as_root rm -rf $packagedir
done
```

```bash
exit
```


# 27. X Window 14
----

## 27.1 xkeyboard-config-2.38

- <https://www.x.org/pub/individual/data/xkeyboard-config/xkeyboard-config-2.38.tar.xz>

- 1. unzip

```bash
tar -xf xkeyboard-config-2.38.tar.xz
cd xkeyboard-config-2.38
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=$XORG_PREFIX --buildtype=release .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf xkeyboard-config-2.38
```

## 27.2 libepoxy-1.5.10

- <https://download.gnome.org/sources/libepoxy/1.5/libepoxy-1.5.10.tar.xz>

- 1. unzip

```bash
tar -xf libepoxy-1.5.10.tar.xz
cd libepoxy-1.5.10
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf libepoxy-1.5.10
```

## 27.3 libxcvt-0.1.2

- <https://www.x.org/pub/individual/lib/libxcvt-0.1.2.tar.xz>

- 1. unzip

```bash
tar -xf libxcvt-0.1.2.tar.xz
cd libxcvt-0.1.2
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=$XORG_PREFIX --buildtype=release .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf libxcvt-0.1.2
```

## 27.4 pixman-0.42.2

- <https://www.cairographics.org/releases/pixman-0.42.2.tar.gz>

- 1. unzip

```bash
tar -xf pixman-0.42.2.tar.gz
cd pixman-0.42.2
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf pixman-0.42.2
```

## 27.5 xorg-server-21.1.7

- <https://www.x.org/pub/individual/xserver/xorg-server-21.1.7.tar.xz>

- 1. unzip

```bash
tar -xf xorg-server-21.1.7.tar.xz
cd xorg-server-21.1.7
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=$XORG_PREFIX \
      --localstatedir=/var  \
      -Dsuid_wrapper=true   \
      -Dxkb_output_dir=/var/lib/xkb &&
ninja
```

- 3. install

```bash
sudo su
```

```bash
ninja install &&
mkdir -pv /etc/X11/xorg.conf.d &&
cat >> /etc/sysconfig/createfiles << "EOF"
/tmp/.ICE-unix dir 1777 root root
/tmp/.X11-unix dir 1777 root root
EOF
```

```bash
exit
```

- 4. remove

```bash
cd ../..
rm -rf xorg-server-21.1.7
```


# 28. X Window 15
----

## 28.1 which-2.21

- <https://ftp.gnu.org/gnu/which/which-2.21.tar.gz>

- 1. unzip

```bash
tar -xf which-2.21.tar.gz
cd which-2.21
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf which-2.21
```


## 28.2 pciutils-3.9.0

- <https://mj.ucw.cz/download/linux/pci/pciutils-3.9.0.tar.gz>

- 1. unzip

```bash
tar -xf pciutils-3.9.0.tar.gz
cd pciutils-3.9.0
```

- 2. build

```bash
make PREFIX=/usr                \
     SHAREDIR=/usr/share/hwdata \
     SHARED=yes
```

- 3. install

```bash
sudo su
```

```bash
make PREFIX=/usr                \
     SHAREDIR=/usr/share/hwdata \
     SHARED=yes                 \
     install install-lib        &&

chmod -v 755 /usr/lib/libpci.so
```

```bash
cat > /etc/cron.weekly/update-pciids.sh << "EOF" &&
#!/bin/bash
/usr/sbin/update-pciids
EOF
chmod 754 /etc/cron.weekly/update-pciids.sh
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf pciutils-3.9.0
```


## 28.3 libevdev-1.13.0

- <https://www.freedesktop.org/software/libevdev/libevdev-1.13.0.tar.xz>

- 1. configuration kernel

```bash
cd /sources/linux-6.1.11
sudo su
make menuconfig
```

```bash
Device Drivers  --->
  Input device support --->
    <*> Generic input layer (needed for keyboard, mouse, ...) [CONFIG_INPUT]
    <*>   Event interface                   [CONFIG_INPUT_EVDEV]
    [*]   Miscellaneous devices  --->       [CONFIG_INPUT_MISC]
      <*/m>    User level driver support    [CONFIG_INPUT_UINPUT]
```

```bash
make
```

- 2. install kernel

```bash
cp linux-6.1.11/arch/x86_64/boot/bzImage /boot/vmlinuz-6.1.11-lfs-11.3-2
cp linux-6.1.11/System.map /boot/System.map-6.1.11-2
cp linux-6.1.11/.config /boot/config-6.1.11-2
```

- 3. unzip

```bash
tar -xf libevdev-1.13.0.tar.xz
cd libevdev-1.13.0
```

- 4. build

```bash
mkdir build &&
cd    build &&

meson --prefix=$XORG_PREFIX    \
      --buildtype=release      \
      -Ddocumentation=disabled &&
ninja
```

- 5. install

```bash
sudo ninja install
```

- 6. remove

```bash
cd ../..
rm -rf libevdev-1.13.0
```


## 28.4 mtdev-1.1.6

- <https://bitmath.org/code/mtdev/mtdev-1.1.6.tar.bz2>

- 1. unzip

```bash
tar -xf mtdev-1.1.6.tar.bz2
cd mtdev-1.1.6
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf mtdev-1.1.6
```


## 28.5 xf86-input-evdev-2.10.6

- <https://www.x.org/pub/individual/driver/xf86-input-evdev-2.10.6.tar.bz2>

- 1. unzip

```bash
tar -xf xf86-input-evdev-2.10.6.tar.bz2
cd xf86-input-evdev-2.10.6
```

- 2. build

```bash
./configure $XORG_CONFIG &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xf86-input-evdev-2.10.6
```


## 28.6 xf86-video-vmware-13.4.0

- <https://www.x.org/pub/individual/driver/xf86-video-vmware-13.4.0.tar.xz>

- 1. unzip

```bash
tar -xf xf86-video-vmware-13.4.0.tar.xz
cd xf86-video-vmware-13.4.0
```

- 2. build

```bash
./configure $XORG_CONFIG &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xf86-video-vmware-13.4.0
```


# 29. X Window 16
----

## 29.1 twm-1.0.12

- <https://www.x.org/pub/individual/app/twm-1.0.12.tar.xz>

- 1. unzip

```bash
tar -xf twm-1.0.12.tar.xz
cd twm-1.0.12
```

- 2. build

```bash
sed -i -e '/^rcdir =/s,^\(rcdir = \).*,\1/etc/X11/app-defaults,' src/Makefile.in &&
./configure $XORG_CONFIG &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf twm-1.0.12
```


## 29.2 xterm-379

- <https://invisible-mirror.net/archives/xterm/xterm-379.tgz>

- 1. unzip

```bash
tar -xf xterm-379.tgz
cd xterm-379
```

- 2. build

```bash
sed -i '/v0/{n;s/new:/new:kb=^?:/}' termcap &&
printf '\tkbs=\\177,\n' >> terminfo &&

TERMINFO=/usr/share/terminfo \
./configure $XORG_CONFIG     \
    --with-app-defaults=/etc/X11/app-defaults &&

make
```

- 3. install

```bash
sudo su
```

```bash
make install    &&
make install-ti &&

mkdir -pv /usr/share/applications &&
cp -v *.desktop /usr/share/applications/
```

```bash
cat >> /etc/X11/app-defaults/XTerm << "EOF"
*VT100*locale: true
*VT100*faceName: Monospace
*VT100*faceSize: 10
*backarrowKeyIsErase: true
*ptyInitialErase: true
EOF
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf xterm-379
```


## 29.3 xclock-1.1.1

- <https://www.x.org/pub/individual/app/xclock-1.1.1.tar.xz>

- 1. unzip

```bash
tar -xf xclock-1.1.1.tar.xz
cd xclock-1.1.1
```

- 2. build

```bash
./configure $XORG_CONFIG &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xclock-1.1.1
```


## 29.4 xinit-1.4.2

- <https://www.x.org/pub/individual/app/xinit-1.4.2.tar.xz>

- 1. unzip

```bash
tar -xf xinit-1.4.2.tar.xz
cd xinit-1.4.2
```

- 2. build

```bash
./configure $XORG_CONFIG --with-xinitdir=/etc/X11/app-defaults &&
make
```

- 3. install

```bash
sudo su
```

```bash
make install &&
ldconfig
```

```bash
chmod u+s $XORG_PREFIX/libexec/Xorg
sed -i '/$serverargs $vtarg/ s/serverargs/: #&/' $XORG_PREFIX/bin/startx
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf xinit-1.4.2
```


# 30. X Window 17
----

## 30.1 Tuning Fontconfig

- 1. configuration

```bash
mkdir -pv ~/.config/fontconfig &&
cat > ~/.config/fontconfig/fonts.conf << "EOF"
<?xml version='1.0'?>
<!DOCTYPE fontconfig SYSTEM 'fonts.dtd'>
<fontconfig>

  <match target="font" >
    <!-- autohint was the old automatic hinter when hinting was patent
    protected, so turn it off to ensure any hinting information in the font
    itself is used, this is the default -->
    <edit mode="assign" name="autohint">  <bool>false</bool></edit>

    <!-- hinting is enabled by default -->
    <edit mode="assign" name="hinting">   <bool>true</bool></edit>

    <!-- for the lcdfilter see https://www.spasche.net/files/lcdfiltering/ -->
    <edit mode="assign" name="lcdfilter"> <const>lcddefault</const></edit>

    <!-- options for hintstyle:
    hintfull: is supposed to give a crisp font that aligns well to the
    character-cell grid but at the cost of its proper shape.

    hintmedium: poorly documented, maybe a synonym for hintfull.
    hintslight is the default: - supposed to be more fuzzy but retains shape.

    hintnone: seems to turn hinting off.
    The variations are marginal and results vary with different fonts -->
    <edit mode="assign" name="hintstyle"> <const>hintslight</const></edit>

    <!-- antialiasing is on by default and really helps for faint characters
    and also for 'xft:' fonts used in rxvt-unicode -->
    <edit mode="assign" name="antialias"> <bool>true</bool></edit>

    <!-- subpixels are usually rgb, see
    http://www.lagom.nl/lcd-test/subpixel.php -->
    <edit mode="assign" name="rgba">      <const>rgb</const></edit>

    <!-- thanks to the Arch wiki for the lcd and subpixel links -->
  </match>

</fontconfig>
EOF
```

```bash
cat > /etc/fonts/conf.d/70-no-bitmaps.conf << "EOF"
<?xml version='1.0'?>
<!DOCTYPE fontconfig SYSTEM 'fonts.dtd'>
<fontconfig>
<!-- Reject bitmap fonts -->
 <selectfont>
  <rejectfont>
   <pattern>
     <patelt name="scalable"><bool>false</bool></patelt>
   </pattern>
  </rejectfont>
 </selectfont>
</fontconfig>
EOF
```


## 30.2 Xorg Legacy

- <https://www.x.org/pub/individual/app/bdftopcf-1.1.tar.bz2>
- <https://www.x.org/pub/individual/font/font-adobe-100dpi-1.0.3.tar.bz2>
- <https://www.x.org/pub/individual/font/font-adobe-75dpi-1.0.3.tar.bz2>
- <https://www.x.org/pub/individual/font/font-jis-misc-1.0.3.tar.bz2>
- <https://www.x.org/pub/individual/font/font-daewoo-misc-1.0.3.tar.bz2>
- <https://www.x.org/pub/individual/font/font-isas-misc-1.0.3.tar.bz2>
- <https://www.x.org/pub/individual/font/font-misc-misc-1.1.2.tar.bz2>

- 1. unzip

```bash
cat > legacy.dat << "EOF"
2a455d3c02390597feb9cefb3fe97a45 bdftopcf-1.1.tar.bz2
1347c3031b74c9e91dc4dfa53b12f143 font-adobe-100dpi-1.0.3.tar.bz2
6c9f26c92393c0756f3e8d614713495b font-adobe-75dpi-1.0.3.tar.bz2
cb7b57d7800fd9e28ec35d85761ed278 font-jis-misc-1.0.3.tar.bz2
0571bf77f8fab465a5454569d9989506 font-daewoo-misc-1.0.3.tar.bz2
a2401caccbdcf5698e001784dbd43f1a font-isas-misc-1.0.3.tar.bz2
c88eb44b3b903d79fb44b860a213e623 font-misc-misc-1.1.2.tar.bz2
EOF
```

```bash
cd /sources/BLFS
mkdir legacy
cd legacy
```

```bash
md5sum -c ../legacy.md5
```

- 2. build & install & remove

```bash
as_root()
{
  if   [ $EUID = 0 ];        then $*
  elif [ -x /usr/bin/sudo ]; then sudo $*
  else                            su -c \\"$*\\"
  fi
}

export -f as_root
```

```bash
bash -e
```

```bash
for package in $(grep -v '^#' ../legacy.md5 | awk '{print $2}')
do
  packagedir=${package%.tar.bz2}
  tar -xf $package
  pushd $packagedir
    ./configure $XORG_CONFIG
    make
    as_root make install
  popd
  rm -rf $packagedir
  as_root /sbin/ldconfig
done
```

```bash
exit
```


================

# 31. At-spi2-core and Dependencies
----

## 31.1 dbus-1.14.6

- <https://dbus.freedesktop.org/releases/dbus/dbus-1.14.6.tar.xz>

- 1. unzip

```bash
tar -xf dbus-1.14.6.tar.xz
cd dbus-1.14.6
```

- 2. build

```bash
./configure --prefix=/usr                        \
            --sysconfdir=/etc                    \
            --localstatedir=/var                 \
            --runstatedir=/run                   \
            --enable-user-session                \
            --disable-doxygen-docs               \
            --disable-xml-docs                   \
            --disable-static                     \
            --with-systemduserunitdir=no         \
            --with-systemdsystemunitdir=no       \
            --docdir=/usr/share/doc/dbus-1.14.6  \
            --with-system-socket=/run/dbus/system_bus_socket &&
make
```

- 3. install

```bash
sudo make install
```

- 4. configuration

```bash
sudo su
```

```bash
ln -sfv /var/lib/dbus/machine-id /etc
```

```bash
cat > /etc/dbus-1/session-local.conf << "EOF"
<!DOCTYPE busconfig PUBLIC
 "-//freedesktop//DTD D-BUS Bus Configuration 1.0//EN"
 "http://www.freedesktop.org/standards/dbus/1.0/busconfig.dtd">
<busconfig>

  <!-- Search for .service files in /usr/local -->
  <servicedir>/usr/local/share/dbus-1/services</servicedir>

</busconfig>
EOF
```

```bash
cd /sources/BLFS/blfs-bootscripts-20230101
make install-dbus
```

```bash
exit
```

- 5. remove

```bash
cd /sources/BLFS
rm -rf dbus-1.14.6
```

## 31.2 sgml-common-0.6.3

- <https://sourceware.org/ftp/docbook-tools/new-trials/SOURCES/sgml-common-0.6.3.tgz>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/sgml-common-0.6.3-manpage-1.patch>

- 1. unzip

```bash
tar -xf sgml-common-0.6.3.tgz
cd sgml-common-0.6.3
```

- 2. build

```bash
patch -Np1 -i ../sgml-common-0.6.3-manpage-1.patch &&
autoreconf -f -i
```

```bash
./configure --prefix=/usr --sysconfdir=/etc &&
make
```

- 3. install

```bash
sudo su
```

```bash
make docdir=/usr/share/doc install &&

install-catalog --add /etc/sgml/sgml-ent.cat \
    /usr/share/sgml/sgml-iso-entities-8879.1986/catalog &&

install-catalog --add /etc/sgml/sgml-docbook.cat \
    /etc/sgml/sgml-ent.cat
```

```bash
install-catalog --remove /etc/sgml/sgml-ent.cat \
    /usr/share/sgml/sgml-iso-entities-8879.1986/catalog &&

install-catalog --remove /etc/sgml/sgml-docbook.cat \
    /etc/sgml/sgml-ent.cat
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf sgml-common-0.6.3
```

## 31.3 docbook-xml-4.5

- <https://www.docbook.org/xml/4.5/docbook-xml-4.5.zip>

- 1. unzip

```bash
tar -xf docbook-xml-4.5.zip
cd docbook-xml-4.5
```

- 2. install

```bash
sudo su
```

```bash
install -v -d -m755 /usr/share/xml/docbook/xml-dtd-4.5 &&
install -v -d -m755 /etc/xml &&
chown -R root:root . &&
cp -v -af docbook.cat *.dtd ent/ *.mod \
    /usr/share/xml/docbook/xml-dtd-4.5
```

```bash
if [ ! -e /etc/xml/docbook ]; then
    xmlcatalog --noout --create /etc/xml/docbook
fi &&
xmlcatalog --noout --add "public" \
    "-//OASIS//DTD DocBook XML V4.5//EN" \
    "http://www.oasis-open.org/docbook/xml/4.5/docbookx.dtd" \
    /etc/xml/docbook &&
xmlcatalog --noout --add "public" \
    "-//OASIS//DTD DocBook XML CALS Table Model V4.5//EN" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5/calstblx.dtd" \
    /etc/xml/docbook &&
xmlcatalog --noout --add "public" \
    "-//OASIS//DTD XML Exchange Table Model 19990315//EN" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5/soextblx.dtd" \
    /etc/xml/docbook &&
xmlcatalog --noout --add "public" \
    "-//OASIS//ELEMENTS DocBook XML Information Pool V4.5//EN" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5/dbpoolx.mod" \
    /etc/xml/docbook &&
xmlcatalog --noout --add "public" \
    "-//OASIS//ELEMENTS DocBook XML Document Hierarchy V4.5//EN" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5/dbhierx.mod" \
    /etc/xml/docbook &&
xmlcatalog --noout --add "public" \
    "-//OASIS//ELEMENTS DocBook XML HTML Tables V4.5//EN" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5/htmltblx.mod" \
    /etc/xml/docbook &&
xmlcatalog --noout --add "public" \
    "-//OASIS//ENTITIES DocBook XML Notations V4.5//EN" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5/dbnotnx.mod" \
    /etc/xml/docbook &&
xmlcatalog --noout --add "public" \
    "-//OASIS//ENTITIES DocBook XML Character Entities V4.5//EN" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5/dbcentx.mod" \
    /etc/xml/docbook &&
xmlcatalog --noout --add "public" \
    "-//OASIS//ENTITIES DocBook XML Additional General Entities V4.5//EN" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5/dbgenent.mod" \
    /etc/xml/docbook &&
xmlcatalog --noout --add "rewriteSystem" \
    "http://www.oasis-open.org/docbook/xml/4.5" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5" \
    /etc/xml/docbook &&
xmlcatalog --noout --add "rewriteURI" \
    "http://www.oasis-open.org/docbook/xml/4.5" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5" \
    /etc/xml/docbook
```

```bash
if [ ! -e /etc/xml/catalog ]; then
    xmlcatalog --noout --create /etc/xml/catalog
fi &&
xmlcatalog --noout --add "delegatePublic" \
    "-//OASIS//ENTITIES DocBook XML" \
    "file:///etc/xml/docbook" \
    /etc/xml/catalog &&
xmlcatalog --noout --add "delegatePublic" \
    "-//OASIS//DTD DocBook XML" \
    "file:///etc/xml/docbook" \
    /etc/xml/catalog &&
xmlcatalog --noout --add "delegateSystem" \
    "http://www.oasis-open.org/docbook/" \
    "file:///etc/xml/docbook" \
    /etc/xml/catalog &&
xmlcatalog --noout --add "delegateURI" \
    "http://www.oasis-open.org/docbook/" \
    "file:///etc/xml/docbook" \
    /etc/xml/catalog
```

```bash
for DTDVERSION in 4.1.2 4.2 4.3 4.4
do
  xmlcatalog --noout --add "public" \
    "-//OASIS//DTD DocBook XML V$DTDVERSION//EN" \
    "http://www.oasis-open.org/docbook/xml/$DTDVERSION/docbookx.dtd" \
    /etc/xml/docbook
  xmlcatalog --noout --add "rewriteSystem" \
    "http://www.oasis-open.org/docbook/xml/$DTDVERSION" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5" \
    /etc/xml/docbook
  xmlcatalog --noout --add "rewriteURI" \
    "http://www.oasis-open.org/docbook/xml/$DTDVERSION" \
    "file:///usr/share/xml/docbook/xml-dtd-4.5" \
    /etc/xml/docbook
  xmlcatalog --noout --add "delegateSystem" \
    "http://www.oasis-open.org/docbook/xml/$DTDVERSION/" \
    "file:///etc/xml/docbook" \
    /etc/xml/catalog
  xmlcatalog --noout --add "delegateURI" \
    "http://www.oasis-open.org/docbook/xml/$DTDVERSION/" \
    "file:///etc/xml/docbook" \
    /etc/xml/catalog
done
```

```bash
exit
```

- 3. remove

```bash
cd ..
rm -rf docbook-xml-4.5
```

## 31.4 docbook-xsl-nons-1.79.2

- <https://github.com/docbook/xslt10-stylesheets/releases/download/release/1.79.2/docbook-xsl-nons-1.79.2.tar.bz2>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/docbook-xsl-nons-1.79.2-stack_fix-1.patch>
- <https://github.com/docbook/xslt10-stylesheets/releases/download/release/1.79.2/docbook-xsl-doc-1.79.2.tar.bz2>

- 1. unzip

```bash
tar -xf docbook-xsl-nons-1.79.2.tar.bz2
cd docbook-xsl-nons-1.79.2
```

- 2. install

```bash
patch -Np1 -i ../docbook-xsl-nons-1.79.2-stack_fix-1.patch
```

```bash
tar -xf ../docbook-xsl-doc-1.79.2.tar.bz2 --strip-components=1
```

```bash
sudo su
```

```bash
install -v -m755 -d /usr/share/xml/docbook/xsl-stylesheets-nons-1.79.2 &&

cp -v -R VERSION assembly common eclipse epub epub3 extensions fo        \
         highlighting html htmlhelp images javahelp lib manpages params  \
         profiling roundtrip slides template tests tools webhelp website \
         xhtml xhtml-1_1 xhtml5                                          \
    /usr/share/xml/docbook/xsl-stylesheets-nons-1.79.2 &&

ln -s VERSION /usr/share/xml/docbook/xsl-stylesheets-nons-1.79.2/VERSION.xsl &&

install -v -m644 -D README \
                    /usr/share/doc/docbook-xsl-nons-1.79.2/README.txt &&
install -v -m644    RELEASE-NOTES* NEWS* \
                    /usr/share/doc/docbook-xsl-nons-1.79.2
```

```bash
cp -v -R doc/* /usr/share/doc/docbook-xsl-nons-1.79.2
```

```bash
if [ ! -d /etc/xml ]; then install -v -m755 -d /etc/xml; fi &&
if [ ! -f /etc/xml/catalog ]; then
    xmlcatalog --noout --create /etc/xml/catalog
fi &&

xmlcatalog --noout --add "rewriteSystem" \
           "https://cdn.docbook.org/release/xsl-nons/1.79.2" \
           "/usr/share/xml/docbook/xsl-stylesheets-nons-1.79.2" \
    /etc/xml/catalog &&

xmlcatalog --noout --add "rewriteURI" \
           "https://cdn.docbook.org/release/xsl-nons/1.79.2" \
           "/usr/share/xml/docbook/xsl-stylesheets-nons-1.79.2" \
    /etc/xml/catalog &&

xmlcatalog --noout --add "rewriteSystem" \
           "https://cdn.docbook.org/release/xsl-nons/current" \
           "/usr/share/xml/docbook/xsl-stylesheets-nons-1.79.2" \
    /etc/xml/catalog &&

xmlcatalog --noout --add "rewriteURI" \
           "https://cdn.docbook.org/release/xsl-nons/current" \
           "/usr/share/xml/docbook/xsl-stylesheets-nons-1.79.2" \
    /etc/xml/catalog &&

xmlcatalog --noout --add "rewriteSystem" \
           "http://docbook.sourceforge.net/release/xsl/current" \
           "/usr/share/xml/docbook/xsl-stylesheets-nons-1.79.2" \
    /etc/xml/catalog &&

xmlcatalog --noout --add "rewriteURI" \
           "http://docbook.sourceforge.net/release/xsl/current" \
           "/usr/share/xml/docbook/xsl-stylesheets-nons-1.79.2" \
    /etc/xml/catalog
```

```bash
exit
```

- 3. remove

```bash
cd ..
rm -rf docbook-xsl-nons-1.79.2
```

## 31.5 libxslt-1.1.37

- <https://download.gnome.org/sources/libxslt/1.1/libxslt-1.1.37.tar.xz>

- 1. unzip

```bash
tar -xf libxslt-1.1.37.tar.xz
cd libxslt-1.1.37
```

- 2. build

```bash
./configure --prefix=/usr                          \
            --disable-static                       \
            --docdir=/usr/share/doc/libxslt-1.1.37 \
            PYTHON=/usr/bin/python3 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libxslt-1.1.37
```

## 31.6 GLib-2.74.5

- <https://download.gnome.org/sources/glib/2.74/glib-2.74.5.tar.xz>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/glib-2.74.5-skip_warnings-1.patch>

- 1. unzip

```bash
tar -xf glib-2.74.5.tar.xz
cd glib-2.74.5
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr       \
      --buildtype=release \
      -Dman=true          \
      ..                  &&
ninja
```

- 3. install

```bash
sudo su
```

```bash
ninja install &&

mkdir -p /usr/share/doc/glib-2.74.5 &&
cp -r ../docs/reference/{gio,glib,gobject} /usr/share/doc/glib-2.74.5
```

```bash
exit
```

- 4. remove

```bash
cd ../..
rm -rf glib-2.74.5
```

## 31.7 gobject-introspection-1.74.0

- <https://download.gnome.org/sources/gobject-introspection/1.74/gobject-introspection-1.74.0.tar.xz>

- 1. unzip

```bash
tar -xf gobject-introspection-1.74.0.tar.xz
cd gobject-introspection-1.74.0
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf gobject-introspection-1.74.0
```

## 31.8 gsettings-desktop-schemas-43.0

- <https://download.gnome.org/sources/gsettings-desktop-schemas/43/gsettings-desktop-schemas-43.0.tar.xz>

- 1. unzip

```bash
tar -xf gsettings-desktop-schemas-43.0.tar.xz
cd gsettings-desktop-schemas-43.0
```

- 2. build

```bash
sed -i -r 's:"(/system):"/org/gnome\1:g' schemas/*.in &&

mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf gsettings-desktop-schemas-43.0
```

## 31.9 at-spi2-core-2.46.0

- <https://download.gnome.org/sources/at-spi2-core/2.46/at-spi2-core-2.46.0.tar.xz>

- 1. unzip

```bash
tar -xf at-spi2-core-2.46.0.tar.xz
cd at-spi2-core-2.46.0
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr       \
      --buildtype=release \
      -Dsystemd_user_dir=/tmp .. &&
ninja
```

- 3. install

```bash
sudo su
```

```bash
ninja install &&
rm /tmp/at-spi-dbus-bus.service
```

```bash
exit
```

- 4. remove

```bash
cd ../..
rm -rf at-spi2-core-2.46.0
```


# 32. Shared-mime-info and dependencies
----

## 32.1 libjpeg-turbo-2.1.5.1

- <https://downloads.sourceforge.net/libjpeg-turbo/libjpeg-turbo-2.1.5.1.tar.gz>

- 1. unzip

```bash
tar -xf libjpeg-turbo-2.1.5.1.tar.xz
cd libjpeg-turbo-2.1.5.1
```

- 2. build

```bash
mkdir build &&
cd    build &&

cmake -DCMAKE_INSTALL_PREFIX=/usr \
      -DCMAKE_BUILD_TYPE=RELEASE  \
      -DENABLE_STATIC=FALSE       \
      -DCMAKE_INSTALL_DOCDIR=/usr/share/doc/libjpeg-turbo-2.1.5.1 \
      -DCMAKE_INSTALL_DEFAULT_LIBDIR=lib  \
      .. &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ../..
rm -rf libjpeg-turbo-2.1.5.1
```

## 32.2 libpng-1.6.39

- <https://downloads.sourceforge.net/libpng/libpng-1.6.39.tar.xz>

- 1. unzip

```bash
tar -xf libpng-1.6.39.tar.xz
cd libpng-1.6.39
```

- 2. build

```bash
gzip -cd ../libpng-1.6.39-apng.patch.gz | patch -p1
```

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo su
```

```bash
make install &&
mkdir -v /usr/share/doc/libpng-1.6.39 &&
cp -v README libpng-manual.txt /usr/share/doc/libpng-1.6.39
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf libpng-1.6.39
```

## 32.3 shared-mime-info-2.2

- <https://gitlab.freedesktop.org/xdg/shared-mime-info/-/archive/2.2/shared-mime-info-2.2.tar.gz>
- <https://anduin.linuxfromscratch.org/BLFS/xdgmime/xdgmime.tar.xz>

- 1. unzip

```bash
tar -xf shared-mime-info-2.2.tar.gz
cd shared-mime-info-2.2
```

- 2. build

```bash
tar -xf ../xdgmime.tar.xz &&
make -C xdgmime
```

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release -Dupdate-mimedb=true .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf shared-mime-info-2.2
```


# 33. GTK+3 1
----

## 33.1 Markdown-3.4.1

- <https://files.pythonhosted.org/packages/source/M/Markdown/Markdown-3.4.1.tar.gz>

- 1. unzip

```bash
tar -xf Markdown-3.4.1
cd Markdown-3.4.1
```

- 2. build

```bash
pip3 wheel -w dist --no-build-isolation --no-deps $PWD
```

- 3. install

```bash
sudo pip3 install --no-index --find-links dist --no-cache-dir --no-user Markdown
```

- 4. remove

```bash
cd ..
rm -rf Markdown-3.4.1
```

## 33.2 MarkupSafe-2.1.2

- <https://files.pythonhosted.org/packages/source/M/MarkupSafe/MarkupSafe-2.1.2.tar.gz>

- 1. unzip

```bash
tar -xf MarkupSafe-2.1.2.tar.gz
cd MarkupSafe-2.1.2
```

- 2. build

```bash
pip3 wheel -w dist --no-build-isolation --no-deps $PWD
```

- 3. install

```bash
sudo pip3 install --no-index --find-links dist --no-cache-dir --no-user markupsafe
```

- 4. remove

```bash
cd ..
rm -rf MarkupSafe-2.1.2
```

## 33.3 Pygments-2.14.0

- <https://files.pythonhosted.org/packages/source/P/Pygments/Pygments-2.14.0.tar.gz>

- 1. unzip

```bash
tar -xf Pygments-2.14.0
cd Pygments-2.14.0
```

- 2. build

```bash
pip3 wheel -w dist --no-build-isolation --no-deps $PWD
```

- 3. install

```bash
sudo pip3 install --no-index --find-links dist --no-cache-dir --no-user Pygments
```

- 4. remove

```bash
cd ..
rm -rf Pygments-2.14.0
```

## 33.4 Jinja2-3.1.2

- <https://files.pythonhosted.org/packages/source/J/Jinja2/Jinja2-3.1.2.tar.gz>

- 1. unzip

```bash
tar -xf Jinja2-3.1.2.tar.gz
cd Jinja2-3.1.2
```

- 2. build

```bash
pip3 wheel -w dist --no-build-isolation --no-deps $PWD
```

- 3. install

```bash
sudo pip3 install --no-index --find-links dist --no-cache-dir --no-user Jinja2
```

- 4. remove

```bash
cd ..
rm -rf Jinja2-3.1.2
```

## 33.5 smartypants-2.0.1

- <https://github.com/leohemsted/smartypants.py/archive/v2.0.1/smartypants-2.0.1.tar.gz>

- 1. unzip

```bash
tar -xf smartypants-2.0.1
cd smartypants-2.0.1
```

- 2. build

```bash
pip3 wheel -w dist --no-build-isolation --no-deps $PWD
```

- 3. install

```bash
sudo pip3 install --no-index --find-links dist --no-cache-dir --no-user smartypants
```

- 4. remove

```bash
cd ..
rm -rf smartypants-2.0.1
```

## 33.6 typogrify-2.0.7

- <https://files.pythonhosted.org/packages/source/t/typogrify/typogrify-2.0.7.tar.gz>

- 1. unzip

```bash
tar -xf typogrify-2.0.7.tar.gz
cd typogrify-2.0.7
```

- 2. build

```bash
pip3 wheel -w dist --no-build-isolation --no-deps $PWD
```

- 3. install

```bash
sudo pip3 install --no-index --find-links dist --no-cache-dir --no-user typogrify
```

- 4. remove

```bash
cd ..
rm -rf typogrify-2.0.7
```

## 33.7 docutils-0.19

- <https://downloads.sourceforge.net/docutils/docutils-0.19.tar.gz>

- 1. unzip

```bash
tar -xf docutils-0.19.tar.gz
cd docutils-0.19
```

- 2. build

```bash
pip3 wheel -w dist --no-build-isolation --no-deps $PWD
```

- 3. install

```bash
sudo su
```

```bash
pip3 install --no-index --find-links dist --no-cache-dir --no-user docutils &&

for f in /usr/bin/rst*.py; do
  ln -svf $(basename $f) /usr/bin/$(basename $f .py)
done
```

```bash
rm -rfv /usr/bin/__pycache__
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf docutils-0.19
```

## 33.8 gdk-pixbuf-2.42.10

- <https://download.gnome.org/sources/gdk-pixbuf/2.42/gdk-pixbuf-2.42.10.tar.xz>

- 1. unzip

```bash
tar -xf gdk-pixbuf-2.42.10.tar.xz
cd gdk-pixbuf-2.42.10
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release --wrap-mode=nofallback .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf gdk-pixbuf-2.42.10
```

## 33.9 libepoxy-1.5.10

- <https://download.gnome.org/sources/libepoxy/1.5/libepoxy-1.5.10.tar.xz>

- 1. unzip

```bash
tar -xf libepoxy-1.5.10.tar.xz
cd libepoxy-1.5.10
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf libepoxy-1.5.10
```


# 34. GTK+3 2
----

## 34.1 FriBidi-1.0.12

- <https://github.com/fribidi/fribidi/releases/download/v1.0.12/fribidi-1.0.12.tar.xz>

- 1. unzip

```bash
tar -xf fribidi-1.0.12.tar.xz
cd fribidi-1.0.12
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf fribidi-1.0.12
```

## 34.2 Graphite2-1.3.14

- <https://github.com/silnrsi/graphite/releases/download/1.3.14/graphite2-1.3.14.tgz>

- 1. unzip

```bash
tar -xf graphite2-1.3.14.tgz
cd graphite2-1.3.14
```

- 2. build

```bash
sed -i '/cmptest/d' tests/CMakeLists.txt
```

```bash
mkdir build &&
cd    build &&

cmake -DCMAKE_INSTALL_PREFIX=/usr .. &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ../..
rm -rf graphite2-1.3.14
```

## 34.3 HarfBuzz-7.0.0

- <https://github.com/harfbuzz/harfbuzz/releases/download/7.0.0/harfbuzz-7.0.0.tar.xz>

- 1. unzip

```bash
tar -xf harfbuzz-7.0.0.tar.xz
cd harfbuzz-7.0.0
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr        \
      --buildtype=release  \
      -Dgraphite2=enabled  &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf harfbuzz-7.0.0
```

## 34.4 FreeType-2.13.0

- <https://downloads.sourceforge.net/freetype/freetype-2.13.0.tar.xz>
- <https://downloads.sourceforge.net/freetype/freetype-doc-2.13.0.tar.xz>

- 1. unzip

```bash
tar -xf freetype-2.13.0.tar.xz
cd freetype-2.13.0
```

- 2. build

```bash
tar -xf ../freetype-doc-2.13.0.tar.xz --strip-components=2 -C docs
```

```bash
sed -ri "s:.*(AUX_MODULES.*valid):\1:" modules.cfg &&

sed -r "s:.*(#.*SUBPIXEL_RENDERING) .*:\1:" \
    -i include/freetype/config/ftoption.h  &&

./configure --prefix=/usr --enable-freetype-config --disable-static &&
make
```

- 3. install

```bash
sudo make install
```

```bash
sudo su
```

```bash
install -v -m755 -d /usr/share/doc/freetype-2.13.0 &&
cp -v -R docs/*     /usr/share/doc/freetype-2.13.0 &&
rm -v /usr/share/doc/freetype-2.13.0/freetype-config.1
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf freetype-2.13.0
```

## 34.5 Fontconfig-2.14.2

- <https://www.freedesktop.org/software/fontconfig/release/fontconfig-2.14.2.tar.xz>

- 1. unzip

```bash
tar -xf fontconfig-2.14.2.tar.xz
cd fontconfig-2.14.2
```

- 2. build

```bash
./configure --prefix=/usr        \
            --sysconfdir=/etc    \
            --localstatedir=/var \
            --disable-docs       \
            --docdir=/usr/share/doc/fontconfig-2.14.2 &&
make
```

- 3. install

```bash
sudo make install
```

```bash
sudo su
```

```bash
install -v -dm755 \
        /usr/share/{man/man{1,3,5},doc/fontconfig-2.14.2/fontconfig-devel} &&
install -v -m644 fc-*/*.1         /usr/share/man/man1 &&
install -v -m644 doc/*.3          /usr/share/man/man3 &&
install -v -m644 doc/fonts-conf.5 /usr/share/man/man5 &&
install -v -m644 doc/fontconfig-devel/* \
                                  /usr/share/doc/fontconfig-2.14.2/fontconfig-devel &&
install -v -m644 doc/*.{pdf,sgml,txt,html} \
                                  /usr/share/doc/fontconfig-2.14.2
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf fontconfig-2.14.2
```

## 34.6 Cairo-1.17.6

- <https://download.gnome.org/sources/cairo/1.17/cairo-1.17.6.tar.xz>

- 1. unzip

```bash
tar -xf cairo-1.17.6.tar.xz
cd cairo-1.17.6
```

- 2. build

```bash
sed 's/PTR/void */' -i util/cairo-trace/lookup-symbol.c
```

```bash
sed -e "/@prefix@/a exec_prefix=@exec_prefix@" \
    -i util/cairo-script/cairo-script-interpreter.pc.in
```

```bash
./configure --prefix=/usr    \
            --disable-static \
            --enable-tee &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf cairo-1.17.6
```

## 34.7 Pango-1.50.12

- <https://download.gnome.org/sources/pango/1.50/pango-1.50.12.tar.xz>

- 1. unzip

```bash
tar -xf pango-1.50.12.tar.xz
cd pango-1.50.12
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release --wrap-mode=nofallback .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf pango-1.50.12
```


# 35. GTK+3 3
----

## 35.1 Wayland-1.21.0

- <https://gitlab.freedesktop.org/wayland/wayland/-/releases/1.21.0/downloads/wayland-1.21.0.tar.xz>

- 1. unzip

```bash
tar -xf wayland-1.21.0.tar.xz
cd wayland-1.21.0
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr       \
      --buildtype=release \
      -Ddocumentation=false &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf wayland-1.21.0
```

## 35.2 wayland-protocols-1.31

- <https://gitlab.freedesktop.org/wayland/wayland-protocols/-/releases/1.31/downloads/wayland-protocols-1.31.tar.xz>

- 1. unzip

```bash
tar -xf wayland-protocols-1.31.tar.xz
cd wayland-protocols-1.31
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release &&
ninja
```

- 3. install

```bash
ninja install
```

- 4. remove

```bash
cd ../..
rm -rf wayland-protocols-1.31
```

## 35.3 libxkbcommon-1.5.0

- <https://xkbcommon.org/download/libxkbcommon-1.5.0.tar.xz>

- 1. unzip

```bash
tar -xf libxkbcommon-1.5.0.tar.xz
cd libxkbcommon-1.5.0
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr       \
      --buildtype=release \
      -Denable-docs=false .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf libxkbcommon-1.5.0
```

## 35.4 GTK+-3.24.36

- <https://download.gnome.org/sources/gtk+/3.24/gtk+-3.24.36.tar.xz>

- 1. unzip

```bash
tar -xf gtk+-3.24.36.tar.xz
cd gtk+-3.24.36
```

- 2. build

```bash
mkdir build &&
cd    build &&
meson setup --prefix=/usr           \
            --buildtype=release     \
            -Dman=true              \
            -Dbroadway_backend=true \
            .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. configuration

```bash
mkdir -vp ~/.config/gtk-3.0
cat > ~/.config/gtk-3.0/settings.ini << "EOF"
[Settings]
gtk-theme-name = Adwaita
gtk-icon-theme-name = oxygen
gtk-font-name = DejaVu Sans 12
gtk-cursor-theme-size = 18
gtk-toolbar-style = GTK_TOOLBAR_BOTH_HORIZ
gtk-xft-antialias = 1
gtk-xft-hinting = 1
gtk-xft-hintstyle = hintslight
gtk-xft-rgba = rgb
gtk-cursor-theme-name = Adwaita
EOF
```

```bash
cat > ~/.config/gtk-3.0/gtk.css << "EOF"
*  {
   -GtkScrollbar-has-backward-stepper: 1;
   -GtkScrollbar-has-forward-stepper: 1;
}
EOF
```

- 5. remove

```bash
cd ../..
rm -rf gtk+-3.24.36
```


# 36. Exo and dependencies
----

## 36.1 libxfce4util-4.18.1

- <https://archive.xfce.org/src/xfce/libxfce4util/4.18/libxfce4util-4.18.1.tar.bz2>

- 1. unzip

```bash
tar -xf libxfce4util-4.18.1.tar.bz2
cd libxfce4util-4.18.1
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libxfce4util-4.18.1
```

## 36.2 Xfconf-4.18.0

- <https://archive.xfce.org/src/xfce/xfconf/4.18/xfconf-4.18.0.tar.bz2>

- 1. unzip

```bash
tar -xf xfconf-4.18.0.tar.bz2
cd xfconf-4.18.0
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xfconf-4.18.0
```

## 36.3 libxfce4ui-4.18.2

- <https://archive.xfce.org/src/xfce/libxfce4ui/4.18/libxfce4ui-4.18.2.tar.bz2>

- 1. unzip

```bash
tar -xf libxfce4ui-4.18.2.tar.bz2
cd libxfce4ui-4.18.2
```

- 2. build

```bash
./configure --prefix=/usr --sysconfdir=/etc &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libxfce4ui-4.18.2
```

## 36.4 Exo-4.18.0

- <https://archive.xfce.org/src/xfce/exo/4.18/exo-4.18.0.tar.bz2>

- 1. unzip

```bash
tar -xf exo-4.18.0.tar.bz2
cd exo-4.18.0
```

- 2. build

```bash
./configure --prefix=/usr --sysconfdir=/etc &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf exo-4.18.0
```


# 37. LightDM 1
----

## 37.1 libgpg-error-1.46

- <https://www.gnupg.org/ftp/gcrypt/libgpg-error/libgpg-error-1.46.tar.bz2>

- 1. unzip

```bash
tar -xf libgpg-error-1.46.tar.bz2
cd libgpg-error-1.46
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
sudo install -v -m644 -D README /usr/share/doc/libgpg-error-1.46/README
```

- 4. remove

```bash
cd ..
rm -rf libgpg-error-1.46
```

## 37.2 libgcrypt-1.10.1

- <https://www.gnupg.org/ftp/gcrypt/libgcrypt/libgcrypt-1.10.1.tar.bz2>

- 1. unzip

```bash
tar -xf libgcrypt-1.10.1.tar.bz2
cd libgcrypt-1.10.1
```

- 2. build

```bash
./configure --prefix=/usr &&
make                      &&

make -C doc html                                                       &&
makeinfo --html --no-split -o doc/gcrypt_nochunks.html doc/gcrypt.texi &&
makeinfo --plaintext       -o doc/gcrypt.txt           doc/gcrypt.texi
```

```bash
make -C doc pdf
```

- 3. install

```bash
sudo su
```

```bash
make install &&
install -v -dm755   /usr/share/doc/libgcrypt-1.10.1 &&
install -v -m644    README doc/{README.apichanges,fips*,libgcrypt*} \
                    /usr/share/doc/libgcrypt-1.10.1 &&

install -v -dm755   /usr/share/doc/libgcrypt-1.10.1/html &&
install -v -m644 doc/gcrypt.html/* \
                    /usr/share/doc/libgcrypt-1.10.1/html &&
install -v -m644 doc/gcrypt_nochunks.html \
                    /usr/share/doc/libgcrypt-1.10.1      &&
install -v -m644 doc/gcrypt.{txt,texi} \
                    /usr/share/doc/libgcrypt-1.10.1
```

```bash
install -v -m644 doc/gcrypt.{pdf,ps,dvi} \
                    /usr/share/doc/libgcrypt-1.10.1
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf libgcrypt-1.10.1
```


# 38. LightDM 2
----

## 38.1 libxml2-2.10.3

- <https://download.gnome.org/sources/libxml2/2.10/libxml2-2.10.3.tar.xz>
- <https://www.w3.org/XML/Test/xmlts20130923.tar.gz>

- 1. unzip

```bash
tar -xf libxml2-2.10.3.tar.xz
cd libxml2-2.10.3
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr       \
      --buildtype=release \
      -Denable-docs=false .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf libxml2-2.10.3
```

## 38.2 itstool-2.0.7

- <https://files.itstool.org/itstool/itstool-2.0.7.tar.bz2>

- 1. unzip

```bash
tar -xf itstool-2.0.7.tar.bz2
cd itstool-2.0.7
```

- 2. build

```bash
PYTHON=/usr/bin/python3 ./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf itstool-2.0.7
```


# 39. LightDM 3
----

## 39.1 Xorg-Server-21.1.7

- <https://www.x.org/pub/individual/xserver/xorg-server-21.1.7.tar.xz>

- 1. unzip

```bash
tar -xf xorg-server-21.1.7.tar.xz
cd xorg-server-21.1.7
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=$XORG_PREFIX \
      --localstatedir=/var  \
      -Dsuid_wrapper=true   \
      -Dxkb_output_dir=/var/lib/xkb &&
ninja
```

- 3. install

```bash
sudo su
```

```bash
ninja install &&
mkdir -pv /etc/X11/xorg.conf.d &&
cat >> /etc/sysconfig/createfiles << "EOF"
/tmp/.ICE-unix dir 1777 root root
/tmp/.X11-unix dir 1777 root root
EOF
```

```bash
exit
```

- 4. remove

```bash
cd ../..
rm -rf xorg-server-21.1.7
```


# 40. LightDM 4
----

## 40.1 ISO Codes-4.12.0

- <https://ftp.debian.org/debian/pool/main/i/iso-codes/iso-codes_4.12.0.orig.tar.xz>

- 1. unzip

```bash
tar -xf iso-codes_4.12.0.orig.tar.xz
cd iso-codes_4.12.0.orig
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf iso-codes_4.12.0.orig
```

## 40.2 libxklavier-5.4

- <https://people.freedesktop.org/~svu/libxklavier-5.4.tar.bz2>

- 1. unzip

```bash
tar -xf libxklavier-5.4.tar.bz2
cd libxklavier-5.4
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libxklavier-5.4
```


# 41. LightDM 5
----

## 41.1 lightdm-1.32.0

- <https://github.com/CanonicalLtd/lightdm/releases/download/1.32.0/lightdm-1.32.0.tar.xz>
- <https://github.com/Xubuntu/lightdm-gtk-greeter/releases/download/lightdm-gtk-greeter-2.0.8/lightdm-gtk-greeter-2.0.8.tar.gz>

- 1. unzip

```bash
tar -xf lightdm-1.32.0.tar.xz
cd lightdm-1.32.0
```

- 2. build 1

```bash
sudo su
```

```bash
groupadd -g 65 lightdm       &&
useradd  -c "Lightdm Daemon" \
         -d /var/lib/lightdm \
         -u 65 -g lightdm    \
         -s /bin/false lightdm
```

```bash
exit
```

```bash
sed -i s/systemd/elogind/ data/pam/*
```

```bash
./configure --prefix=/usr                 \
            --libexecdir=/usr/lib/lightdm \
            --localstatedir=/var          \
            --sbindir=/usr/bin            \
            --sysconfdir=/etc             \
            --disable-static              \
            --disable-tests               \
            --with-greeter-user=lightdm   \
            --with-greeter-session=lightdm-gtk-greeter \
            --docdir=/usr/share/doc/lightdm-1.32.0 &&
make
```

```bash
sudo su
```

```bash
make install                                                  &&
cp tests/src/lightdm-session /usr/bin                         &&
sed -i '1 s/sh/bash --login/' /usr/bin/lightdm-session        &&
rm -rf /etc/init                                              &&
install -v -dm755 -o lightdm -g lightdm /var/lib/lightdm      &&
install -v -dm755 -o lightdm -g lightdm /var/lib/lightdm-data &&
install -v -dm755 -o lightdm -g lightdm /var/cache/lightdm    &&
install -v -dm770 -o lightdm -g lightdm /var/log/lightdm
```

```bash
exit
```

- 3. build 2

```bash
tar -xf ../lightdm-gtk-greeter-2.0.8.tar.gz &&
cd lightdm-gtk-greeter-2.0.8 &&

./configure --prefix=/usr                 \
            --libexecdir=/usr/lib/lightdm \
            --sbindir=/usr/bin            \
            --sysconfdir=/etc             \
            --with-libxklavier            \
            --enable-kill-on-sigterm      \
            --disable-libido              \
            --disable-libindicator        \
            --disable-static              \
            --disable-maintainer-mode     \
            --docdir=/usr/share/doc/lightdm-gtk-greeter-2.0.8 &&
make
```

```bash
sudo make install
```

- 4. install script

```bash
cd /sources/BLFS/blfs-bootscripts-20230101
sudo make install-lightdm
sudo telinit 5
```

- 5. remove

```bash
cd /sources/BLFS
rm -rf lightdm-1.32.0
```


================

# 42. Gstreamer
----

## 42.1 gstreamer-1.22.0

- <https://gstreamer.freedesktop.org/src/gstreamer/gstreamer-1.22.0.tar.xz>

- 1. unzip

```bash
tar -xf gstreamer-1.22.0.tar.xz
cd gstreamer-1.22.0
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson  --prefix=/usr       \
       --buildtype=release \
       -Dgst_debug=false   \
       -Dpackage-origin=https://www.linuxfromscratch.org/blfs/view/11.3/ \
       -Dpackage-name="GStreamer 1.22.0 BLFS" &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf gstreamer-1.22.0
```

## 42.2 gst-plugins-base-1.22.0

- <https://gstreamer.freedesktop.org/src/gst-plugins-base/gst-plugins-base-1.22.0.tar.xz>

- 1. unzip

```bash
tar -xf gst-plugins-base-1.22.0.tar.xz
cd gst-plugins-base-1.22.0
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson  --prefix=/usr       \
       --buildtype=release \
       -Dpackage-origin=https://www.linuxfromscratch.org/blfs/view/11.3/ \
       -Dpackage-name="GStreamer 1.22.0 BLFS"    \
       --wrap-mode=nodownload &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf gst-plugins-base-1.22.0
```

## 42.3 gst-plugins-good-1.22.0

- <https://gstreamer.freedesktop.org/src/gst-plugins-good/gst-plugins-good-1.22.0.tar.xz>

- 1. unzip

```bash
tar -xf gst-plugins-good-1.22.0.tar.xz
cd gst-plugins-good-1.22.0
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson  --prefix=/usr       \
       --buildtype=release \
       -Dpackage-origin=https://www.linuxfromscratch.org/blfs/view/11.3/ \
       -Dpackage-name="GStreamer 1.22.0 BLFS" &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf gst-plugins-good-1.22.0
```

## 42.4 gst-plugins-bad-1.22.0

- <https://gstreamer.freedesktop.org/src/gst-plugins-bad/gst-plugins-bad-1.22.0.tar.xz>

- 1. unzip

```bash
tar -xf gst-plugins-bad-1.22.0.tar.xz
cd gst-plugins-bad-1.22.0
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson  --prefix=/usr       \
       --buildtype=release \
       -Dgpl=enabled       \
       -Dpackage-origin=https://www.linuxfromscratch.org/blfs/view/11.3/ \
       -Dpackage-name="GStreamer 1.22.0 BLFS" &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf gst-plugins-bad-1.22.0
```

## 42.5 gst-plugins-ugly-1.22.0

- <https://gstreamer.freedesktop.org/src/gst-plugins-ugly/gst-plugins-ugly-1.22.0.tar.xz>

- 1. unzip

```bash
tar -xf gst-plugins-ugly-1.22.0.tar.xz
cd gst-plugins-ugly-1.22.0
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson  --prefix=/usr       \
       --buildtype=release \
       -Dgpl=enabled       \
       -Dpackage-origin=https://www.linuxfromscratch.org/blfs/view/11.3/ \
       -Dpackage-name="GStreamer 1.22.0 BLFS" &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf gst-plugins-ugly-1.22.0
```


# 43. Notification
----

## 43.1 libogg-1.3.5

- <https://downloads.xiph.org/releases/ogg/libogg-1.3.5.tar.xz>

- 1. unzip

```bash
tar -xf libogg-1.3.5.tar.xz
cd libogg-1.3.5
```

- 2. build

```bash
./configure --prefix=/usr    \
            --disable-static \
            --docdir=/usr/share/doc/libogg-1.3.5 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libogg-1.3.5
```

## 43.2 libvorbis-1.3.7

- <https://downloads.xiph.org/releases/vorbis/libvorbis-1.3.7.tar.xz>

- 1. unzip

```bash
tar -xf libvorbis-1.3.7.tar.xz
cd libvorbis-1.3.7
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo make install
sudo install -v -m644 doc/Vorbis* /usr/share/doc/libvorbis-1.3.7
```

- 4. remove

```bash
cd ..
rm -rf libvorbis-1.3.7
```

## 43.3 libcanberra-0.30

- <https://0pointer.de/lennart/projects/libcanberra/libcanberra-0.30.tar.xz>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/libcanberra-0.30-wayland-1.patch>

- 1. unzip

```bash
tar -xf libcanberra-0.30.tar.xz
cd libcanberra-0.30
```

- 2. build

```bash
patch -Np1 -i ../libcanberra-0.30-wayland-1.patch
```

```bash
./configure --prefix=/usr --disable-oss &&
make
```

- 3. install

```bash
sudo make docdir=/usr/share/doc/libcanberra-0.30 install
```

- 4. remove

```bash
cd ..
rm -rf libcanberra-0.30
```

## 43.4 notification-daemon-3.20.0

- <https://download.gnome.org/sources/notification-daemon/3.20/notification-daemon-3.20.0.tar.xz>

- 1. unzip

```bash
tar -xf notification-daemon-3.20.0.tar.xz
cd notification-daemon-3.20.0
```

- 2. build

```bash
./configure --prefix=/usr     \
            --sysconfdir=/etc \
            --disable-static  &&
make
```

- 3. install

```bash
sudo make install
```

- 4. test

```bash
pgrep -l notification-da &&
notify-send -i info Information "Hi ${USER}, This is a Test"
```

- 5. remove

```bash
cd ..
rm -rf notification-daemon-3.20.0
```

## 43.5 libnotify-0.8.1

- <https://download.gnome.org/sources/libnotify/0.8/libnotify-0.8.1.tar.xz>

- 1. unzip

```bash
tar -xf libnotify-0.8.1.tar.xz
cd libnotify-0.8.1
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr       \
      --buildtype=release \
      -Dgtk_doc=false     \
      -Dman=false .. &&
ninja
```

- 3. install

```bash
sudo su
```

```bash
ninja install &&
mv -v /usr/share/doc/libnotify{,-0.8.1}
```

```bash
exit
```

- 4. remove

```bash
cd ../..
rm -rf libnotify-0.8.1
```


# 44. Xfce 1
----

## 44.1 taglib-1.13

- <https://taglib.org/releases/taglib-1.13.tar.gz>

- 1. unzip

```bash
tar -xf taglib-1.13.tar.gz
cd taglib-1.13
```

- 2. build

```bash
mkdir build &&
cd    build &&

cmake -DCMAKE_INSTALL_PREFIX=/usr \
      -DCMAKE_BUILD_TYPE=Release  \
      -DBUILD_SHARED_LIBS=ON \
      .. &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ../..
rm -rf taglib-1.13
```

## 44.2 Garcon-4.18.0

- <https://archive.xfce.org/src/xfce/garcon/4.18/garcon-4.18.0.tar.bz2>

- 1. unzip

```bash
tar -xf garcon-4.18.0.tar.bz2
cd garcon-4.18.0
```

- 2. build

```bash
./configure --prefix=/usr --sysconfdir=/etc &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf garcon-4.18.0
```

## 44.3 libwnck-43.0

- <https://download.gnome.org/sources/libwnck/43/libwnck-43.0.tar.xz>

- 1. unzip

```bash
tar -xf libwnck-43.0.tar.xz
cd libwnck-43.0
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf libwnck-43.0
```

## 44.4 xfce4-panel-4.18.2

- <https://archive.xfce.org/src/xfce/xfce4-panel/4.18/xfce4-panel-4.18.2.tar.bz2>

- 1. unzip

```bash
tar -xf xfce4-panel-4.18.2.tar.bz2
cd xfce4-panel-4.18.2
```

- 2. build

```bash
./configure --prefix=/usr --sysconfdir=/etc &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xfce4-panel-4.18.2
```

## 44.5 hicolor-icon-theme-0.17

- <https://icon-theme.freedesktop.org/releases/hicolor-icon-theme-0.17.tar.xz>

- 1. unzip

```bash
tar -xf hicolor-icon-theme-0.17.tar.xz
cd hicolor-icon-theme-0.17
```

- 2. build

```bash
./configure --prefix=/usr
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf hicolor-icon-theme-0.17
```

## 44.6 thunar-4.18.4

- <https://archive.xfce.org/src/xfce/thunar/4.18/thunar-4.18.4.tar.bz2>

- 1. unzip

```bash
tar -xf thunar-4.18.4.tar.bz2
cd thunar-4.18.4
```

- 2. build

```bash
sed -i 's/\tinstall-systemd_userDATA/\t/' Makefile.in
```

```bash
./configure --prefix=/usr \
            --sysconfdir=/etc \
            --docdir=/usr/share/doc/thunar-4.18.4 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf thunar-4.18.4
```

## 44.7 libgudev-237

- <https://download.gnome.org/sources/libgudev/237/libgudev-237.tar.xz>

- 1. unzip

```bash
tar -xf libgudev-237.tar.xz
cd libgudev-237
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf libgudev-237
```

## 44.8 thunar-volman-4.18.0

- <https://archive.xfce.org/src/xfce/thunar-volman/4.18/thunar-volman-4.18.0.tar.bz2>

- 1. unzip

```bash
tar -xf thunar-volman-4.18.0.tar.bz2
cd thunar-volman-4.18.0
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf thunar-volman-4.18.0
```


# 45. Xfce 2
----

## 45.1 tumbler-4.18.0

- <https://archive.xfce.org/src/xfce/tumbler/4.18/tumbler-4.18.0.tar.bz2>

- 1. unzip

```bash
tar -xf tumbler-4.18.0.tar.bz2
cd tumbler-4.18.0
```

- 2. build

```bash
./configure --prefix=/usr --sysconfdir=/etc &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf tumbler-4.18.0
```

## 45.2 xfce4-appfinder-4.18.0

- <https://archive.xfce.org/src/xfce/xfce4-appfinder/4.18/xfce4-appfinder-4.18.0.tar.bz2>

- 1. unzip

```bash
tar -xf xfce4-appfinder-4.18.0.tar.bz2
cd xfce4-appfinder-4.18.0
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xfce4-appfinder-4.18.0
```


# 46. Xfce 3
----

## 46.1 libusb-1.0.26

- <https://github.com/libusb/libusb/releases/download/v1.0.26/libusb-1.0.26.tar.bz2>

- 1. unzip

```bash
tar -xf libusb-1.0.26.tar.bz2
cd libusb-1.0.26
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libusb-1.0.26
```

- 5. configuration

```bash
Device Drivers --->
  [*] USB support --->                   [CONFIG_USB_SUPPORT]
    <*/M> Support for Host-side USB      [CONFIG_USB]
    (Select any USB hardware device drivers you may need on the same page)
```

It is already ok for current kernel configure. Do nothing.


## 46.2 duktape-2.7.0

- <https://duktape.org/duktape-2.7.0.tar.xz>

- 1. unzip

```bash
tar -xf duktape-2.7.0.tar.xz
cd duktape-2.7.0
```

- 2. build

```bash
sed -i 's/-Os/-O2/' Makefile.sharedlibrary
make -f Makefile.sharedlibrary INSTALL_PREFIX=/usr
```

- 3. install

```bash
sudo make -f Makefile.sharedlibrary INSTALL_PREFIX=/usr install
```

- 4. remove

```bash
cd ..
rm -rf duktape-2.7.0
```

## 46.3 Polkit-122

- <https://gitlab.freedesktop.org/polkit/polkit/-/archive/122/polkit-122.tar.gz>

- 1. unzip

```bash
tar -xf polkit-122.tar.gz
cd polkit-122
```

- 2. build

```bash
sudo su
```

```bash
groupadd -fg 27 polkitd &&
useradd -c "PolicyKit Daemon Owner" -d /etc/polkit-1 -u 27 \
        -g polkitd -s /bin/false polkitd
```

```bash
exit
```

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr                 \
      --buildtype=release           \
      -Dman=true                    \
      -Dsession_tracking=libelogind \
      -Dsystemdsystemunitdir=/tmp   \
      -Dtests=true                  \
      -Djs_engine=duktape           \
      ..                            &&
ninja
```

- 3. install

```bash
sudo su
```

```bash
ninja install &&
rm -v /tmp/*.service
```

```bash
cat > /etc/pam.d/polkit-1 << "EOF"
# Begin /etc/pam.d/polkit-1

auth     include        system-auth
account  include        system-account
password include        system-password
session  include        system-session

# End /etc/pam.d/polkit-1
EOF
```

```bash
exit
```

- 4. remove

```bash
cd ../..
rm -rf polkit-122
```

## 46.4 UPower-1.90.0

- <https://gitlab.freedesktop.org/upower/upower/-/archive/v1.90.0/upower-v1.90.0.tar.bz2>

- 1. unzip

```bash
tar -xf upower-v1.90.0.tar.bz2
cd upower-v1.90.0
```

- 2. build

```bash

```

- 3. install

```bash

```

- 4. remove

```bash
cd ..
rm -rf upower-v1.90.0
```

## 46.5 xfce4-power-manager-4.18.1

- <https://archive.xfce.org/src/xfce/xfce4-appfinder/4.18/xfce4-appfinder-4.18.0.tar.bz2>

- 1. unzip

```bash
tar -xf xfce4-appfinder-4.18.0.tar.bz2
cd xfce4-appfinder-4.18.0
```

- 2. build

```bash
sed '/parse_version/d' -i src/linux/integration-test.py
```

```bash
mkdir build                         &&
cd    build                         &&
meson --prefix=/usr                        \
      --buildtype=release                  \
      -Dgtk-doc=false                      \
      -Dman=false                          \
      -Dsystemdsystemunitdir=no            \
      -Dudevrulesdir=/usr/lib/udev/rules.d \
      ..                            &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf xfce4-appfinder-4.18.0
```


# 47. Xfce 4
----

## 47.1 XML-Simple-2.25

- <https://www.cpan.org/authors/id/G/GR/GRANTM/XML-Simple-2.25.tar.gz>

- 1. unzip

```bash
tar -xf XML-Simple-2.25.tar.gz
cd XML-Simple-2.25
```

- 2. build

```bash
perl Makefile.PL &&
make             &&
make test
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf XML-Simple-2.25
```

## 47.2 icon-naming-utils-0.8.90

- <http://tango.freedesktop.org/releases/icon-naming-utils-0.8.90.tar.bz2>

- 1. unzip

```bash
tar -xf icon-naming-utils-0.8.90.tar.bz2
cd icon-naming-utils-0.8.90
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf icon-naming-utils-0.8.90
```

## 47.3 gnome-icon-theme-3.12.0

- <https://download.gnome.org/sources/gnome-icon-theme/3.12/gnome-icon-theme-3.12.0.tar.xz>

- 1. unzip

```bash
tar -xf gnome-icon-theme-3.12.0.tar.xz
cd gnome-icon-theme-3.12.0
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf gnome-icon-theme-3.12.0
```

## 47.4 xfce4-settings-4.18.2

- <https://archive.xfce.org/src/xfce/xfce4-settings/4.18/xfce4-settings-4.18.2.tar.bz2>

- 1. unzip

```bash
tar -xf xfce4-settings-4.18.2.tar.bz2
cd xfce4-settings-4.18.2
```

- 2. build

```bash
./configure --prefix=/usr --sysconfdir=/etc &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xfce4-settings-4.18.2
```


# 48. Xfce 5
----

## 48.1 startup-notification-0.12

- <https://www.freedesktop.org/software/startup-notification/releases/startup-notification-0.12.tar.gz>

- 1. unzip

```bash
tar -xf startup-notification-0.12.tar.gz
cd startup-notification-0.12
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo su
```

```bash
make install &&
install -v -m644 -D doc/startup-notification.txt \
    /usr/share/doc/startup-notification-0.12/startup-notification.txt
```

```bash
exit
```

- 4. remove

```bash
cd ..
rm -rf startup-notification-0.12
```

## 48.2 Xfdesktop-4.18.1

- <https://archive.xfce.org/src/xfce/xfdesktop/4.18/xfdesktop-4.18.1.tar.bz2>

- 1. unzip

```bash
tar -xf xfdesktop-4.18.1.tar.bz2
cd xfdesktop-4.18.1
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xfdesktop-4.18.1
```

## 48.3 Xfwm4-4.18.0

- <https://archive.xfce.org/src/xfce/xfwm4/4.18/xfwm4-4.18.0.tar.bz2>

- 1. unzip

```bash
tar -xf xfwm4-4.18.0.tar.bz2
cd xfwm4-4.18.0
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xfwm4-4.18.0
```


# 49. Xfce 6
----

## 49.1 desktop-file-utils-0.26

- <https://www.freedesktop.org/software/desktop-file-utils/releases/desktop-file-utils-0.26.tar.xz>

- 1. unzip

```bash
tar -xf desktop-file-utils-0.26.tar.xz
cd desktop-file-utils-0.26
```

- 2. build

```bash
sudo rm -fv /usr/bin/desktop-file-edit
```

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. configuration

```bash
sudo su
```

```bash
install -vdm755 /usr/share/applications &&
update-desktop-database /usr/share/applications
```

```bash
exit
```

- 5. remove

```bash
cd ../..
rm -rf desktop-file-utils-0.26
```

## 49.2 GTK+-2.24.33

- <https://download.gnome.org/sources/gtk+/2.24/gtk+-2.24.33.tar.xz>

- 1. unzip

```bash
tar -xf gtk+-2.24.33.tar.xz
cd gtk+-2.24.33
```

- 2. build

```bash
sed -e 's#l \(gtk-.*\).sgml#& -o \1#' \
    -i docs/{faq,tutorial}/Makefile.in      &&

./configure --prefix=/usr --sysconfdir=/etc &&

make
```

- 3. install

```bash
sudo make install
```

- 4. configuration

```bash
cat > ~/.gtkrc-2.0 << "EOF"
include "/usr/share/themes/Glider/gtk-2.0/gtkrc"
gtk-icon-theme-name = "hicolor"
EOF
```

```bash
cat > /etc/gtk-2.0/gtkrc << "EOF"
include "/usr/share/themes/Clearlooks/gtk-2.0/gtkrc"
gtk-icon-theme-name = "elementary"
EOF
```

- 5. remove

```bash
cd ..
rm -rf gtk+-2.24.33
```

## 49.3 libglade-2.6.4

- <https://download.gnome.org/sources/libglade/2.6/libglade-2.6.4.tar.bz2>

- 1. unzip

```bash
tar -xf libglade-2.6.4.tar.bz2
cd libglade-2.6.4
```

- 2. build

```bash
sed -i '/DG_DISABLE_DEPRECATED/d' glade/Makefile.in &&
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libglade-2.6.4
```

## 49.4 GLU-9.0.2

- <ftp://ftp.freedesktop.org/pub/mesa/glu/glu-9.0.2.tar.xz>

- 1. unzip

```bash
tar -xf glu-9.0.2.tar.xz
cd glu-9.0.2
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=$XORG_PREFIX -Dgl_provider=gl --buildtype=release .. &&
ninja
```

- 3. install

```bash
sudo ninja install
sudo rm -vf /usr/lib/libGLU.a
```

- 4. remove

```bash
cd ../..
rm -rf glu-9.0.2
```

## 49.5 XScreenSaver-6.06

- <https://www.jwz.org/xscreensaver/xscreensaver-6.06.tar.gz>

- 1. unzip

```bash
tar -xf xscreensaver-6.06.tar.gz
cd xscreensaver-6.06
```

- 2. build

```bash
sed -i 's/-lsystemd/-lelogind/' driver/Makefile.in
```

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. configuration

```bash
sudo su
```

```bash
cat > /etc/pam.d/xscreensaver << "EOF"
# Begin /etc/pam.d/xscreensaver

auth    include system-auth
account include system-account

# End /etc/pam.d/xscreensaver
EOF
```

```bash
exit
```

- 5. remove

```bash
cd ..
rm -rf xscreensaver-6.06
```

## 49.6 shared-mime-info-2.2

- <https://gitlab.freedesktop.org/xdg/shared-mime-info/-/archive/2.2/shared-mime-info-2.2.tar.gz>

- 1. unzip

```bash
tar -xf shared-mime-info-2.2.tar.gz
cd shared-mime-info-2.2
```

- 2. build

```bash
tar -xf ../xdgmime.tar.xz &&
make -C xdgmime
```

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr --buildtype=release -Dupdate-mimedb=true .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. remove

```bash
cd ../..
rm -rf shared-mime-info-2.2
```

## 49.7 Graphviz-7.1.0

- <https://gitlab.com/graphviz/graphviz/-/archive/7.1.0/graphviz-7.1.0.tar.bz2>

- 1. unzip

```bash
tar -xf graphviz-7.1.0.bz2
cd graphviz-7.1.0
```

- 2. build

```bash
sed -i '/LIBPOSTFIX="64"/s/64//' configure.ac &&

./autogen.sh              &&
./configure --prefix=/usr \
            --docdir=/usr/share/doc/graphviz-7.1.0
```

```bash
sed -i "s/0/$(date +%Y%m%d)/" builddate.h
```

```bash
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf graphviz-7.1.0
```

## 49.8 Vala-0.56.4

- <https://download.gnome.org/sources/vala/0.56/vala-0.56.4.tar.xz>

- 1. unzip

```bash
tar -xf vala-0.56.4.tar.xz
cd vala-0.56.4
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf vala-0.56.4
```

## 49.9 AccountsService-22.08.8

- <https://www.freedesktop.org/software/accountsservice/accountsservice-22.08.8.tar.xz>

- 1. unzip

```bash
tar -xf accountsservice-22.08.8.tar.xz
cd accountsservice-22.08.8
```

- 2. build

```bash
mkdir build &&
cd build &&

meson --prefix=/usr             \
      --buildtype=release       \
      -Dadmin_group=adm         \
      -Delogind=true            \
      -Dsystemdsystemunitdir=no \
      .. &&
ninja
```

- 3. install

```bash
sudo ninja install
```

- 4. configuration

```bash
cat > /etc/polkit-1/rules.d/40-adm.rules << "EOF"
polkit.addAdminRule(function(action, subject) {
   return ["unix-group:adm"];
   });
EOF
```

- 5. remove

```bash
cd ../..
rm -rf accountsservice-22.08.8
```

## 49.10 polkit-gnome-0.105

- <https://download.gnome.org/sources/polkit-gnome/0.105/polkit-gnome-0.105.tar.xz>
- <https://www.linuxfromscratch.org/patches/blfs/11.3/polkit-gnome-0.105-consolidated_fixes-1.patch>

- 1. unzip

```bash
tar -xf polkit-gnome-0.105.tar.xz
cd polkit-gnome-0.105
```

- 2. build

```bash
patch -Np1 -i ../polkit-gnome-0.105-consolidated_fixes-1.patch
```

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. configuration

```bash
sudo su
```

```bash
mkdir -p /etc/xdg/autostart &&
cat > /etc/xdg/autostart/polkit-gnome-authentication-agent-1.desktop << "EOF"
[Desktop Entry]
Name=PolicyKit Authentication Agent
Comment=PolicyKit Authentication Agent
Exec=/usr/libexec/polkit-gnome-authentication-agent-1
Terminal=false
Type=Application
Categories=
NoDisplay=true
OnlyShowIn=GNOME;XFCE;Unity;
AutostartCondition=GNOME3 unless-session gnome
EOF
```

```bash
exit
```

- 5. remove

```bash
cd ..
rm -rf polkit-gnome-0.105
```

## 49.11 xfce4-session-4.18.1

- <https://archive.xfce.org/src/xfce/xfce4-session/4.18/xfce4-session-4.18.1.tar.bz2>

- 1. unzip

```bash
tar -xf xfce4-session-4.18.1.tar.bz2
cd xfce4-session-4.18.1
```

- 2. build

```bash
./configure --prefix=/usr \
            --sysconfdir=/etc \
            --disable-legacy-sm &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xfce4-session-4.18.1
```


# 50. Xfce 7
----

## 50.1 dbus-glib-0.112

- <https://dbus.freedesktop.org/releases/dbus-glib/dbus-glib-0.112.tar.gz>

- 1. unzip

```bash
tar -xf dbus-glib-0.112.tar.gz
cd dbus-glib-0.112
```

- 2. build

```bash
./configure --prefix=/usr     \
            --sysconfdir=/etc \
            --disable-static &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf dbus-glib-0.112
```

## 50.2 Parole-4.18.0

- <https://archive.xfce.org/src/apps/parole/4.18/parole-4.18.0.tar.bz2>

- 1. unzip

```bash
tar -xf parole-4.18.0.tar.bz2
cd parole-4.18.0
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf parole-4.18.0
```


# 51. Xfce 8
----

## 51.1 GnuTLS-3.8.0

- <https://www.gnupg.org/ftp/gcrypt/gnutls/v3.8/gnutls-3.8.0.tar.xz>

- 1. unzip

```bash
tar -xf gnutls-3.8.0.tar.xz
cd gnutls-3.8.0
```

- 2. build

```bash
./configure --prefix=/usr \
            --docdir=/usr/share/doc/gnutls-3.8.0 \
            --with-default-trust-store-pkcs11="pkcs11:" &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf gnutls-3.8.0
```

## 51.2 VTE-0.70.3

- <https://gitlab.gnome.org/GNOME/vte/-/archive/0.70.3/vte-0.70.3.tar.gz>

- 1. unzip

```bash
tar -xf vte-0.70.3.tar.gz
cd vte-0.70.3
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr       \
      --buildtype=release \
      -Dfribidi=false     \
      -D_systemd=false .. &&
ninja
```

- 3. install

```bash
sudo ninja install
sudo rm -v /etc/profile.d/vte.*
```

- 4. remove

```bash
cd ../..
rm -rf vte-0.70.3
```

## 51.3 xfce4-terminal-1.0.4

- <https://archive.xfce.org/src/apps/xfce4-terminal/1.0/xfce4-terminal-1.0.4.tar.bz2>

- 1. unzip

```bash
tar -xf xfce4-terminal-1.0.4.tar.bz2
cd xfce4-terminal-1.0.4
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xfce4-terminal-1.0.4
```


# 52. Xfce 9
----

## 52.1 libburn-1.5.4

- <https://files.libburnia-project.org/releases/libburn-1.5.4.tar.gz>

- 1. unzip

```bash
tar -xf libburn-1.5.4.tar.gz
cd libburn-1.5.4
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libburn-1.5.4
```

## 52.2 libisofs-1.5.4

- <https://files.libburnia-project.org/releases/libisofs-1.5.4.tar.gz>

- 1. unzip

```bash
tar -xf libisofs-1.5.4.tar.gz
cd libisofs-1.5.4
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libisofs-1.5.4
```

## 52.3 Xfburn-0.6.2

- <https://archive.xfce.org/src/apps/xfburn/0.6/xfburn-0.6.2.tar.bz2>

- 1. unzip

```bash
tar -xf xfburn-0.6.2.tar.bz2
cd xfburn-0.6.2
```

- 2. build

```bash
./configure --prefix=/usr --disable-static &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xfburn-0.6.2
```


# 53. Xfce 10
----

## 53.1 libexif-0.6.24

- <https://github.com/libexif/libexif/releases/download/v0.6.24/libexif-0.6.24.tar.bz2>

- 1. unzip

```bash
tar -xf libexif-0.6.24.tar.bz2
cd libexif-0.6.24
```

- 2. build

```bash
./configure --prefix=/usr    \
            --disable-static \
            --with-doc-dir=/usr/share/doc/libexif-0.6.24 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libexif-0.6.24
```

## 53.2 Ristretto-0.13.0

- <https://archive.xfce.org/src/apps/ristretto/0.13/ristretto-0.13.0.tar.bz2>

- 1. unzip

```bash
tar -xf ristretto-0.13.0.tar.bz2
cd ristretto-0.13.0
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf ristretto-0.13.0
```


# 54. Xfce 11
----

## 54.1 xfce4-dev-tools-4.18.0

- <http://archive.xfce.org/src/xfce/xfce4-dev-tools/4.18/xfce4-dev-tools-4.18.0.tar.bz2>

- 1. unzip

```bash
tar -xf xfce4-dev-tools-4.18.0.tar.bz2
cd xfce4-dev-tools-4.18.0
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xfce4-dev-tools-4.18.0
```

## 54.2 xfce4-notifyd-0.8.1

- <https://archive.xfce.org/src/apps/xfce4-notifyd/0.8/xfce4-notifyd-0.8.1.tar.bz2>

- 1. unzip

```bash
tar -xf xfce4-notifyd-0.8.1.tar.bz2
cd xfce4-notifyd-0.8.1
```

- 2. build

```bash
./configure --prefix=/usr --sysconfdir=/etc &&
make
```

- 3. install

```bash
sudo make install
```

- 4. test

```bash
notify-send -i info Information "Hi ${USER}, This is a Test"
```

- 5. remove

```bash
cd ..
rm -rf xfce4-notifyd-0.8.1
```

# 55. Xfce 12
----

## 55.1 libsndfile-1.2.0

- <https://github.com/libsndfile/libsndfile/releases/download/1.2.0/libsndfile-1.2.0.tar.xz>

- 1. unzip

```bash
tar -xf libsndfile-1.2.0.tar.xz
cd libsndfile-1.2.0
```

- 2. build

```bash
./configure --prefix=/usr    \
            --docdir=/usr/share/doc/libsndfile-1.2.0 &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf libsndfile-1.2.0
```

## 55.2 PulseAudio-16.1

- <https://www.freedesktop.org/software/pulseaudio/releases/pulseaudio-16.1.tar.xz>

- 1. unzip

```bash
tar -xf pulseaudio-16.1.tar.xz
cd pulseaudio-16.1
```

- 2. build

```bash
mkdir build &&
cd    build &&

meson --prefix=/usr       \
      --buildtype=release \
      -Ddatabase=gdbm     \
      -Ddoxygen=false     \
      -Dbluez5=disabled   &&
ninja
```

- 3. install

```bash
sudo ninja install
```

```bash
sudo rm -fv /etc/dbus-1/system.d/pulseaudio-system.conf
```

- 4. remove

```bash
cd ../..
rm -rf pulseaudio-16.1
```

## 55.3 xfce4-pulseaudio-plugin-0.4.5

- <https://archive.xfce.org/src/panel-plugins/xfce4-pulseaudio-plugin/0.4/xfce4-pulseaudio-plugin-0.4.5.tar.bz2>

- 1. unzip

```bash
tar -xf xfce4-pulseaudio-plugin-0.4.5.tar.bz2
cd xfce4-pulseaudio-plugin-0.4.5
```

- 2. build

```bash
./configure --prefix=/usr &&
make
```

- 3. install

```bash
sudo make install
```

- 4. remove

```bash
cd ..
rm -rf xfce4-pulseaudio-plugin-0.4.5
```

> Base on BLFS 11.3 System V.
