
## Table of Contents

### Preface
--------

- [Foreword](Preface.md#foreword)
- [Who Would Want to Read this Book](Preface.md#who-would-want-to-read-this-book)
- [Organization](Preface.md#organization)


### I. Introduction
--------

#### 1. Welcome to BLFS

- [Which Sections of the Book Do I Want?](1.Welcome_to_blfs.md#11-which-sections-of-the-book-do-i-want)
- [Conventions Used in this Book](1.Welcome_to_blfs.md#12-conventions-used-in-this-book)
- [Book Version](1.Welcome_to_blfs.md#13-book-version)
- [Mirror Sites](1.Welcome_to_blfs.md#14-mirror-sites)
- [Getting the Source Packages](1.Welcome_to_blfs.md#15-getting-the-source-packages)
- [Change Log](1.Welcome_to_blfs.md#16-change-log)
- [Mailing Lists](1.Welcome_to_blfs.md#17-mailing-lists)
- [BLFS Wiki](1.Welcome_to_blfs.md#18-blfs-wiki)
- [Asking for Help and the FAQ](1.Welcome_to_blfs.md#19-asking-for-help-and-the-faq)
- [Credits](1.Welcome_to_blfs.md#110-credits)
- [Contact Information](1.Welcome_to_blfs.md#111-contact-information)

#### 2. Important Information

- [Notes on Building Software](2.Important_information.md#21-notes-on-building-software)
- [The /usr Versus /usr/local Debate](2.Important_information.md#22-the-usr-versus-usrlocal-debate)
- [Optional Patches](2.Important_information.md#23-optional-patches)
- [BLFS Systemd Units](2.Important_information.md#24-blfs-systemd-units)
- [About Libtool Archive (.la) files](2.Important_information.md#25-about-libtool-archive-la-files)
- [Libraries: Static or shared?](2.Important_information.md#26-libraries-static-or-shared)
- [Locale Related Issues](2.Important_information.md#27-locale-related-issues)
- [Going Beyond BLFS](2.Important_information.md#28-going-beyond-blfs)


### II. Post LFS Configuration and Extra Software
--------

#### 3. After LFS Configuration Issues

- [Creating a Custom Boot Device](3.After_lfs_configuration_issues.md#31-creating-a-custom-boot-device)
- [About Console Fonts](3.After_lfs_configuration_issues.md#32-about-console-fonts)
- [About Firmware](3.After_lfs_configuration_issues.md#33-about-firmware)
- [About Devices](3.After_lfs_configuration_issues.md#34-about-devices)
- [Configuring for Adding Users](3.After_lfs_configuration_issues.md#35-configuring-for-adding-users)
- [About System Users and Groups](3.After_lfs_configuration_issues.md#36-about-system-users-and-groups)
- [The Bash Shell Startup Files](3.After_lfs_configuration_issues.md#37-the-bash-shell-startup-files)
- [The /etc/vimrc and ~/.vimrc Files](3.After_lfs_configuration_issues.md#38-the-etcvimrc-and-vimrc-files)
- [Customizing your Logon with /etc/issue](3.After_lfs_configuration_issues.md#39-customizing-your-logon-with-etcissue)

#### 4. Security

- [Vulnerabilities](4.Security.md#41-vulnerabilities)
- [make-ca-1.12](4.Security.md#42-make-ca-112)
- [CrackLib-2.9.8](4.Security.md#43-cracklib-298)
- [cryptsetup-2.4.3](4.Security.md#44-cryptsetup-243)
- [Cyrus SASL-2.1.28](4.Security.md#45-cyrus-sasl-2128)
- [GnuPG-2.4.0](4.Security.md#46-gnupg-240)
- [GnuTLS-3.8.0](4.Security.md#47-gnutls-380)
- [GPGME-1.18.0](4.Security.md#48-gpgme-1180)
- [iptables-1.8.9](4.Security.md#49-iptables-189)
- [Setting Up a Network Firewall](4.Security.md#410-setting-up-a-network-firewall)
- [libcap-2.67 with PAM](4.Security.md#411-libcap-267-with-pam)
- [Linux-PAM-1.5.2](4.Security.md#412-linux-pam-152)
- [liboauth-1.0.3](4.Security.md#413-liboauth-103)
- [libpwquality-1.4.5](4.Security.md#414-libpwquality-145)
- [MIT Kerberos V5-1.20.1](4.Security.md#415-mit-kerberos-v5-1201)
- [Nettle-3.8.1](4.Security.md#416-nettle-381)
- [NSS-3.88.1](4.Security.md#417-nss-3881)
- [OpenSSH-9.2p1](4.Security.md#418-openssh-92p1)
- [p11-kit-0.24.1](4.Security.md#419-p11-kit-0241)
- [Polkit-122](4.Security.md#420-polkit-122)
- [polkit-gnome-0.105](4.Security.md#421-polkit-gnome-0105)
- [Shadow-4.13](4.Security.md#422-shadow-413)
- [ssh-askpass-9.2p1](4.Security.md#423-ssh-askpass-92p1)
- [stunnel-5.68](4.Security.md#424-stunnel-568)
- [Sudo-1.9.13p1](4.Security.md#425-sudo-1913p1)
- [Tripwire-2.4.3.7](4.Security.md#426-tripwire-2437)
- [volume_key-0.3.12](4.Security.md#427-volume_key-0312)

#### 5. File Systems and Disk Management

- [About initramfs](5.File_systems_and_disk_management.md#51-about-initramfs)
- [btrfs-progs-6.1.3](5.File_systems_and_disk_management.md#52-btrfs-progs-613)
- [dosfstools-4.2](5.File_systems_and_disk_management.md#53-dosfstools-42)
- [Fuse-3.13.1](5.File_systems_and_disk_management.md#54-fuse-3131)
- [jfsutils-1.1.15](5.File_systems_and_disk_management.md#55-jfsutils-1115)
- [LVM2-2.03.18](5.File_systems_and_disk_management.md#56-lvm2-20318)
- [About Logical Volume Management (LVM)](5.File_systems_and_disk_management.md#57-about-logical-volume-management-lvm)
- [About RAID](5.File_systems_and_disk_management.md#58-about-raid)
- [mdadm-4.2](5.File_systems_and_disk_management.md#59-mdadm-42)
- [ntfs-3g-2022.10.3](5.File_systems_and_disk_management.md#510-ntfs-3g-2022103)
- [gptfdisk-1.0.9](5.File_systems_and_disk_management.md#511-gptfdisk-109)
- [parted-3.5](5.File_systems_and_disk_management.md#512-parted-35)
- [reiserfsprogs-3.6.27](5.File_systems_and_disk_management.md#513-reiserfsprogs-3627)
- [smartmontools-7.3](5.File_systems_and_disk_management.md#514-smartmontools-73)
- [sshfs-3.7.3](5.File_systems_and_disk_management.md#515-sshfs-373)
- [xfsprogs-6.1.1](5.File_systems_and_disk_management.md#516-xfsprogs-611)

**Packages for UEFI Boot**

- [efivar-38](5.File_systems_and_disk_management.md#517-efivar-38)
- [efibootmgr-18](5.File_systems_and_disk_management.md#518-efibootmgr-18)
- [GRUB-2.06 for EFI](5.File_systems_and_disk_management.md#519-grub-206-for-efi)
- [Using GRUB to Set Up the Boot Process with UEFI](5.File_systems_and_disk_management.md#520-using-grub-to-set-up-the-boot-process-with-uefi)

#### 6. Editors

- [Bluefish-2.2.12](6.Editors.md#61-bluefish-2212)
- [Ed-1.19](6.Editors.md#62-ed-119)
- [Emacs-28.2](6.Editors.md#63-emacs-282)
- [Gedit-44.2](6.Editors.md#64-gedit-442)
- [JOE-4.6](6.Editors.md#65-joe-46)
- [Kate-22.12.2](6.Editors.md#66-kate-22122)
- [Mousepad-0.5.10](6.Editors.md#67-mousepad-0510)
- [Nano-7.2](6.Editors.md#68-nano-72)
- [Vim-9.0.1273](6.Editors.md#69-vim-901273)

#### 7. Shells

- [Dash-0.5.12](7.Shells.md#71-dash-0512)
- [Tcsh-6.24.07](7.Shells.md#72-tcsh-62407)
- [zsh-5.9](7.Shells.md#73-zsh-59)

#### 8. Virtualization

- [qemu-7.2.0](8.Virtualization.md#81-qemu-720)


### III. General Libraries and Utilities
--------

#### 9. General Libraries

- [Apr-1.7.2](9.General_libraries.md#91-apr-172)
- [Apr-Util-1.6.3](9.General_libraries.md#92-apr-util-163)
- [Aspell-0.60.8](9.General_libraries.md#93-aspell-0608)
- [Boost-1.81.0](9.General_libraries.md#94-boost-1810)
- [brotli-1.0.9](9.General_libraries.md#95-brotli-109)
- [CLucene-2.3.3.4](9.General_libraries.md#96-clucene-2334)
- [dbus-glib-0.112](9.General_libraries.md#97-dbus-glib-0112)
- [Double-conversion-3.2.1](9.General_libraries.md#98-double-conversion-321)
- [duktape-2.7.0](9.General_libraries.md#99-duktape-270)
- [enchant-2.3.3](9.General_libraries.md#910-enchant-233)
- [Exempi-2.6.3](9.General_libraries.md#911-exempi-263)
- [fftw-3.3.10](9.General_libraries.md#912-fftw-3310)
- [GLib-2.74.5](9.General_libraries.md#913-glib-2745)
- [GLibmm-2.66.5](9.General_libraries.md#914-glibmm-2665)
- [GMime-3.2.7](9.General_libraries.md#915-gmime-327)
- [gobject-introspection-1.74.0](9.General_libraries.md#916-gobject-introspection-1740)
- [Gsl-2.7.1](9.General_libraries.md#917-gsl-271)
- [gspell-1.12.0](9.General_libraries.md#918-gspell-1120)
- [ICU-72.1](9.General_libraries.md#919-icu-721)
- [inih-56](9.General_libraries.md#920-inih-56)
- [Intel-gmmlib-22.3.4](9.General_libraries.md#921-intel-gmmlib-2234)
- [Jansson-2.14](9.General_libraries.md#922-jansson-214)
- [JS-102.8.0](9.General_libraries.md#923-js-10280)
- [JSON-C-0.16](9.General_libraries.md#924-json-c-016)
- [JSON-GLib-1.6.6](9.General_libraries.md#925-json-glib-166)
- [keyutils-1.6.1](9.General_libraries.md#926-keyutils-161)
- [libaio-0.3.113](9.General_libraries.md#927-libaio-03113)
- [libarchive-3.6.2](9.General_libraries.md#928-libarchive-362)
- [libassuan-2.5.5](9.General_libraries.md#929-libassuan-255)
- [libatasmart-0.19](9.General_libraries.md#930-libatasmart-019)
- [libatomic_ops-7.6.14](9.General_libraries.md#931-libatomic_ops-7614)
- [libblockdev-2.28](9.General_libraries.md#932-libblockdev-228)
- [libbytesize-2.7](9.General_libraries.md#933-libbytesize-27)
- [libcloudproviders-0.3.1](9.General_libraries.md#934-libcloudproviders-031)
- [libdaemon-0.14](9.General_libraries.md#935-libdaemon-014)
- [libgcrypt-1.10.1](9.General_libraries.md#936-libgcrypt-1101)
- [libgpg-error-1.46](9.General_libraries.md#937-libgpg-error-146)
- [libgrss-0.7.0](9.General_libraries.md#938-libgrss-070)
- [libgsf-1.14.50](9.General_libraries.md#939-libgsf-11450)
- [libgudev-237](9.General_libraries.md#940-libgudev-237)
- [libgusb-0.4.5](9.General_libraries.md#941-libgusb-045)
- [libical-3.0.16](9.General_libraries.md#942-libical-3016)
- [libidn-1.41](9.General_libraries.md#943-libidn-141)
- [libidn2-2.3.4](9.General_libraries.md#944-libidn2-234)
- [libiodbc-3.52.15](9.General_libraries.md#945-libiodbc-35215)
- [libksba-1.6.3](9.General_libraries.md#946-libksba-163)
- [liblinear-245](9.General_libraries.md#947-liblinear-245)
- [libmbim-1.26.4](9.General_libraries.md#948-libmbim-1264)
- [libpaper-1.1.24+nmu5](9.General_libraries.md#949-libpaper-1124nmu5)
- [libportal-0.6](9.General_libraries.md#950-libportal-06)
- [libptytty-2.0](9.General_libraries.md#951-libptytty-20)
- [libqalculate-4.5.1](9.General_libraries.md#952-libqalculate-451)
- [libqmi-1.30.8](9.General_libraries.md#953-libqmi-1308)
- [libseccomp-2.5.4](9.General_libraries.md#954-libseccomp-254)
- [libsigc++-2.12.0](9.General_libraries.md#955-libsigc-2120)
- [libsigsegv-2.14](9.General_libraries.md#956-libsigsegv-214)
- [libssh2-1.10.0](9.General_libraries.md#957-libssh2-1100)
- [libstatgrab-0.92.1](9.General_libraries.md#958-libstatgrab-0921)
- [libtasn1-4.19.0](9.General_libraries.md#959-libtasn1-4190)
- [libunique-1.1.6](9.General_libraries.md#960-libunique-116)
- [libunistring-1.1](9.General_libraries.md#961-libunistring-11)
- [libunwind-1.6.2](9.General_libraries.md#962-libunwind-162)
- [liburcu-0.14.0](9.General_libraries.md#963-liburcu-0140)
- [libusb-1.0.26](9.General_libraries.md#964-libusb-1026)
- [libuv-1.44.2](9.General_libraries.md#965-libuv-1442)
- [libxkbcommon-1.5.0](9.General_libraries.md#966-libxkbcommon-150)
- [libxml2-2.10.3](9.General_libraries.md#967-libxml2-2103)
- [libxslt-1.1.37](9.General_libraries.md#968-libxslt-1137)
- [libwacom-2.6.0](9.General_libraries.md#969-libwacom-260)
- [libwpe-1.14.1](9.General_libraries.md#970-libwpe-1141)
- [libyaml-0.2.5](9.General_libraries.md#971-libyaml-025)
- [LZO-2.10](9.General_libraries.md#972-lzo-210)
- [mtdev-1.1.6](9.General_libraries.md#973-mtdev-116)
- [Node.js-18.14.1](9.General_libraries.md#974-nodejs-18141)
- [npth-1.6](9.General_libraries.md#975-npth-16)
- [NSPR-4.35](9.General_libraries.md#976-nspr-435)
- [PCRE-8.45](9.General_libraries.md#977-pcre-845)
- [PCRE2-10.42](9.General_libraries.md#978-pcre2-1042)
- [Popt-1.19](9.General_libraries.md#979-popt-119)
- [Pth-2.0.7](9.General_libraries.md#980-pth-207)
- [Qca-2.3.5](9.General_libraries.md#981-qca-235)
- [Talloc-2.4.0](9.General_libraries.md#982-talloc-240)
- [telepathy-glib-0.24.2](9.General_libraries.md#983-telepathy-glib-0242)
- [Uchardet-0.0.8](9.General_libraries.md#984-uchardet-008)
- [Umockdev-0.17.16](9.General_libraries.md#985-umockdev-01716)
- [Wayland-1.21.0](9.General_libraries.md#986-wayland-1210)
- [Wayland-Protocols-1.31](9.General_libraries.md#987-wayland-protocols-131)
- [wpebackend-fdo-1.14.0](9.General_libraries.md#988-wpebackend-fdo-1140)
- [wv-1.2.9](9.General_libraries.md#989-wv-129)
- [Xapian-1.4.22](9.General_libraries.md#990-xapian-1422)

#### 10. Graphics and Font Libraries

- [AAlib-1.4rc5](10.Graphics_and_font_libraries.md#101-aalib-14rc5)
- [babl-0.1.98](10.Graphics_and_font_libraries.md#102-babl-0198)
- [Exiv2-0.27.6](10.Graphics_and_font_libraries.md#103-exiv2-0276)
- [FreeType-2.13.0](10.Graphics_and_font_libraries.md#104-freetype-2130)
- [Fontconfig-2.14.2](10.Graphics_and_font_libraries.md#105-fontconfig-2142)
- [FriBidi-1.0.12](10.Graphics_and_font_libraries.md#106-fribidi-1012)
- [gegl-0.4.40](10.Graphics_and_font_libraries.md#107-gegl-0440)
- [giflib-5.2.1](10.Graphics_and_font_libraries.md#108-giflib-521)
- [GLM-0.9.9.8](10.Graphics_and_font_libraries.md#109-glm-0998)
- [Graphite2-1.3.14](10.Graphics_and_font_libraries.md#1010-graphite2-1314)
- [HarfBuzz-7.0.0](10.Graphics_and_font_libraries.md#1011-harfbuzz-700)
- [JasPer-4.0.0](10.Graphics_and_font_libraries.md#1012-jasper-400)
- [Little CMS-1.19](10.Graphics_and_font_libraries.md#1013-little-cms-119)
- [Little CMS-2.14](10.Graphics_and_font_libraries.md#1014-little-cms-214)
- [libexif-0.6.24](10.Graphics_and_font_libraries.md#1015-libexif-0624)
- [Libgxps-0.3.2](10.Graphics_and_font_libraries.md#1016-libgxps-032)
- [libjpeg-turbo-2.1.5.1](10.Graphics_and_font_libraries.md#1017-libjpeg-turbo-2151)
- [libmng-2.0.3](10.Graphics_and_font_libraries.md#1018-libmng-203)
- [libmypaint-1.6.1](10.Graphics_and_font_libraries.md#1019-libmypaint-161)
- [libpng-1.6.39](10.Graphics_and_font_libraries.md#1020-libpng-1639)
- [libraw-0.21.1](10.Graphics_and_font_libraries.md#1021-libraw-0211)
- [librsvg-2.54.5](10.Graphics_and_font_libraries.md#1022-librsvg-2545)
- [Libspiro-20220722](10.Graphics_and_font_libraries.md#1023-libspiro-20220722)
- [libtiff-4.5.0](10.Graphics_and_font_libraries.md#1024-libtiff-450)
- [libwebp-1.3.0](10.Graphics_and_font_libraries.md#1025-libwebp-130)
- [mypaint-brushes-1.3.1](10.Graphics_and_font_libraries.md#1026-mypaint-brushes-131)
- [newt-0.52.23](10.Graphics_and_font_libraries.md#1027-newt-05223)
- [opencv-4.7.0](10.Graphics_and_font_libraries.md#1028-opencv-470)
- [OpenJPEG-2.5.0](10.Graphics_and_font_libraries.md#1029-openjpeg-250)
- [Pixman-0.42.2](10.Graphics_and_font_libraries.md#1030-pixman-0422)
- [Poppler-23.02.0](10.Graphics_and_font_libraries.md#1031-poppler-23020)
- [Potrace-1.16](10.Graphics_and_font_libraries.md#1032-potrace-116)
- [Qpdf-11.2.0](10.Graphics_and_font_libraries.md#1033-qpdf-1120)
- [qrencode-4.1.1](10.Graphics_and_font_libraries.md#1034-qrencode-411)
- [sassc-3.6.2](10.Graphics_and_font_libraries.md#1035-sassc-362)
- [woff2-1.0.2](10.Graphics_and_font_libraries.md#1036-woff2-102)

#### 11. General Utilities

- [Asciidoctor-2.0.18](11.General_utilities.md#111-asciidoctor-2018)
- [Bogofilter-1.2.5](11.General_utilities.md#112-bogofilter-125)
- [Compface-1.5.2](11.General_utilities.md#113-compface-152)
- [desktop-file-utils-0.26](11.General_utilities.md#114-desktop-file-utils-026)
- [dos2unix-7.4.4](11.General_utilities.md#115-dos2unix-744)
- [Graphviz-7.1.0](11.General_utilities.md#116-graphviz-710)
- [GTK-Doc-1.33.2](11.General_utilities.md#117-gtk-doc-1332)
- [Highlight-4.4](11.General_utilities.md#118-highlight-44)
- [ibus-1.5.27](11.General_utilities.md#119-ibus-1527)
- [ImageMagick-7.1.0-61](11.General_utilities.md#1110-imagemagick-710-61)
- [ISO Codes-4.12.0](11.General_utilities.md#1111-iso-codes-4120)
- [lsof-4.95.0](11.General_utilities.md#1112-lsof-4950)
- [mandoc-1.14.6](11.General_utilities.md#1113-mandoc-1146)
- [pinentry-1.2.1](11.General_utilities.md#1114-pinentry-121)
- [Rep-gtk-0.90.8.3](11.General_utilities.md#1115-rep-gtk-09083)
- [Screen-4.9.0](11.General_utilities.md#1116-screen-490)
- [shared-mime-info-2.2](11.General_utilities.md#1117-shared-mime-info-22)
- [Sharutils-4.15.2](11.General_utilities.md#1118-sharutils-4152)
- [telepathy-mission-control-5.16.6](11.General_utilities.md#1119-telepathy-mission-control-5166)
- [tidy-html5-5.8.0](11.General_utilities.md#1120-tidy-html5-580)
- [Time-1.9](11.General_utilities.md#1121-time-19)
- [tree-2.1.0](11.General_utilities.md#1122-tree-210)
- [unixODBC-2.3.11](11.General_utilities.md#1123-unixodbc-2311)
- [Xdg-user-dirs-0.18](11.General_utilities.md#1124-xdg-user-dirs-018)

#### 12. System Utilities

- [AccountsService-22.08.8](12.System_utilities.md#121-accountsservice-22088)
- [acpid-2.0.34](12.System_utilities.md#122-acpid-2034)
- [at-3.2.5](12.System_utilities.md#123-at-325)
- [autofs-5.1.8](12.System_utilities.md#124-autofs-518)
- [BlueZ-5.66](12.System_utilities.md#125-bluez-566)
- [Bubblewrap-0.7.0](12.System_utilities.md#126-bubblewrap-070)
- [Colord-1.4.6](12.System_utilities.md#127-colord-146)
- [cpio-2.13](12.System_utilities.md#128-cpio-213)
- [cups-pk-helper-0.2.7](12.System_utilities.md#129-cups-pk-helper-027)
- [dbus-1.14.6](12.System_utilities.md#1210-dbus-1146)
- [Fcron-3.2.1](12.System_utilities.md#1211-fcron-321)
- [GPM-1.20.7](12.System_utilities.md#1212-gpm-1207)
- [Hdparm-9.65](12.System_utilities.md#1213-hdparm-965)
- [LSB-Tools-0.10](12.System_utilities.md#1214-lsb-tools-010)
- [Lm-sensors-3-6-0](12.System_utilities.md#1215-lm-sensors-3-6-0)
- [Logrotate-3.21.0](12.System_utilities.md#1216-logrotate-3210)
- [MC-4.8.29](12.System_utilities.md#1217-mc-4829)
- [ModemManager-1.18.12](12.System_utilities.md#1218-modemmanager-11812)
- [notification-daemon-3.20.0](12.System_utilities.md#1219-notification-daemon-3200)
- [p7zip-17.04](12.System_utilities.md#1220-p7zip-1704)
- [Pax-20201030](12.System_utilities.md#1221-pax-20201030)
- [pciutils-3.9.0](12.System_utilities.md#1222-pciutils-390)
- [pm-utils-1.4.1](12.System_utilities.md#1223-pm-utils-141)
- [Raptor-2.0.15](12.System_utilities.md#1224-raptor-2015)
- [Rasqal-0.9.33](12.System_utilities.md#1225-rasqal-0933)
- [Redland-1.0.17](12.System_utilities.md#1226-redland-1017)
- [sg3_utils-1.47](12.System_utilities.md#1227-sg3_utils-147)
- [Sysstat-12.7.2](12.System_utilities.md#1228-sysstat-1272)
- [Systemd-252](12.System_utilities.md#1229-systemd-252)
- [UDisks-2.9.4](12.System_utilities.md#1230-udisks-294)
- [UnRar-6.2.6](12.System_utilities.md#1231-unrar-626)
- [UnZip-6.0](12.System_utilities.md#1232-unzip-60)
- [UPower-1.90.0](12.System_utilities.md#1233-upower-1900)
- [usbutils-015](12.System_utilities.md#1234-usbutils-015)
- [Which-2.21 and Alternatives](12.System_utilities.md#1235-which-221-and-alternatives)
- [Zip-3.0](12.System_utilities.md#1236-zip-30)

#### 13. Programming

- [Autoconf2.13](13.Programming.md#131-autoconf213)
- [Cbindgen-0.24.3](13.Programming.md#132-cbindgen-0243)
- [Clisp-2.49](13.Programming.md#133-clisp-249)
- [CMake-3.25.2](13.Programming.md#134-cmake-3252)
- [Doxygen-1.9.6](13.Programming.md#135-doxygen-196)
- [GCC-12.2.0](13.Programming.md#136-gcc-1220)
- [GC-8.2.2](13.Programming.md#137-gc-822)
- [GDB-13.1](13.Programming.md#138-gdb-131)
- [Git-2.39.2](13.Programming.md#139-git-2392)
- [Running a Git Server](13.Programming.md#1310-running-a-git-server)
- [Guile-3.0.9](13.Programming.md#1311-guile-309)
- [Librep-0.92.7](13.Programming.md#1312-librep-0927)
- [LLVM-15.0.7](13.Programming.md#1313-llvm-1507)
- [Lua-5.4.4](13.Programming.md#1314-lua-544)
- [Lua-5.2.4](13.Programming.md#1315-lua-524)
- [Mercurial-6.3.2](13.Programming.md#1316-mercurial-632)
- [NASM-2.16.01](13.Programming.md#1317-nasm-21601)
- [Patchelf-0.17.2](13.Programming.md#1318-patchelf-0172)
- [Perl Modules](13.Programming.md#1319-perl-modules)
- [Perl Module Dependencies](13.Programming.md#1320-perl-module-dependencies)
- [PHP-8.2.3](13.Programming.md#1321-php-823)
- [Python-2.7.18](13.Programming.md#1322-python-2718)
- [Python-3.11.2](13.Programming.md#1323-python-3112)
- [Python Modules](13.Programming.md#1324-python-modules)
- [Python Dependencies](13.Programming.md#1325-python-dependencies)
- [Ruby-3.2.1](13.Programming.md#1326-ruby-321)
- [Rustc-1.67.1](13.Programming.md#1327-rustc-1671)
- [SCons-4.4.0](13.Programming.md#1328-scons-440)
- [slang-2.3.3](13.Programming.md#1329-slang-233)
- [Subversion-1.14.2](13.Programming.md#1330-subversion-1142)
- [Running a Subversion Server](13.Programming.md#1331-running-a-subversion-server)
- [SWIG-4.1.1](13.Programming.md#1332-swig-411)
- [Sysprof-3.46.0](13.Programming.md#1333-sysprof-3460)
- [Tk-8.6.13](13.Programming.md#1334-tk-8613)
- [Vala-0.56.4](13.Programming.md#1335-vala-0564)
- [Valgrind-3.20.0](13.Programming.md#1336-valgrind-3200)
- [yasm-1.3.0](13.Programming.md#1337-yasm-130)

**Java**

- [Java-19.0.2](13.Programming.md#1338-java-1902)
- [OpenJDK-19.0.2](13.Programming.md#1339-openjdk-1902)
- [Configuring the JAVA environment](13.Programming.md#1340-configuring-the-java-environment)
- [apache-ant-1.10.13](13.Programming.md#1341-apache-ant-11013)


### IV. Networking
--------

#### 14. Connecting to a Network

- [Advanced Network Setup](14.Connecting_to_a_network.md#141-advanced-network-setup)
- [dhcpcd-9.4.1](14.Connecting_to_a_network.md#142-dhcpcd-941)
- [DHCP-4.4.3-P1](14.Connecting_to_a_network.md#143-dhcp-443-p1)

#### 15. Networking Programs

- [bridge-utils-1.7.1](15.Networking_programs.md#151-bridge-utils-171)
- [cifs-utils-7.0](15.Networking_programs.md#152-cifs-utils-70)
- [iw-5.19](15.Networking_programs.md#153-iw-519)
- [NcFTP-3.2.6](15.Networking_programs.md#154-ncftp-326)
- [Net-tools-2.10](15.Networking_programs.md#155-net-tools-210)
- [NFS-Utils-2.6.2](15.Networking_programs.md#156-nfs-utils-262)
- [ntp-4.2.8p15](15.Networking_programs.md#157-ntp-428p15)
- [rpcbind-1.2.6](15.Networking_programs.md#158-rpcbind-126)
- [rsync-3.2.7](15.Networking_programs.md#159-rsync-327)
- [Samba-4.17.5](15.Networking_programs.md#1510-samba-4175)
- [Wget-1.21.3](15.Networking_programs.md#1511-wget-1213)
- [Wireless Tools-29](15.Networking_programs.md#1512-wireless-tools-29)
- [wpa_supplicant-2.10](15.Networking_programs.md#1513-wpa_supplicant-210)

#### 16. Networking Utilities

- [Avahi-0.8](16.Networking_utilities.md#161-avahi-08)
- [BIND Utilities-9.18.12](16.Networking_utilities.md#162-bind-utilities-91812)
- [NetworkManager-1.42.0](16.Networking_utilities.md#163-networkmanager-1420)
- [network-manager-applet-1.30.0](16.Networking_utilities.md#164-network-manager-applet-1300)
- [Nmap-7.93](16.Networking_utilities.md#165-nmap-793)
- [Traceroute-2.1.2](16.Networking_utilities.md#166-traceroute-212)
- [Whois-5.4.3](16.Networking_utilities.md#167-whois-543)
- [Wireshark-4.0.3](16.Networking_utilities.md#168-wireshark-403)

#### 17. Networking Libraries

- [c-ares-1.19.0](17.Networking_libraries.md#171-c-ares-1190)
- [cURL-7.88.1](17.Networking_libraries.md#172-curl-7881)
- [GeoClue-2.7.0](17.Networking_libraries.md#173-geoclue-270)
- [glib-networking-2.74.0](17.Networking_libraries.md#174-glib-networking-2740)
- [kdsoap-2.1.1](17.Networking_libraries.md#175-kdsoap-211)
- [ldns-1.8.3](17.Networking_libraries.md#176-ldns-183)
- [libevent-2.1.12](17.Networking_libraries.md#177-libevent-2112)
- [libmnl-1.0.5](17.Networking_libraries.md#178-libmnl-105)
- [libnl-3.7.0](17.Networking_libraries.md#179-libnl-370)
- [libnma-1.10.6](17.Networking_libraries.md#1710-libnma-1106)
- [libnsl-2.0.0](17.Networking_libraries.md#1711-libnsl-200)
- [libpcap-1.10.3](17.Networking_libraries.md#1712-libpcap-1103)
- [libpsl-0.21.2](17.Networking_libraries.md#1713-libpsl-0212)
- [libndp-1.8](17.Networking_libraries.md#1714-libndp-18)
- [Libslirp-4.7.0](17.Networking_libraries.md#1715-libslirp-470)
- [libsoup-2.74.3](17.Networking_libraries.md#1716-libsoup-2743)
- [libsoup-3.2.2](17.Networking_libraries.md#1717-libsoup-322)
- [libtirpc-1.3.3](17.Networking_libraries.md#1718-libtirpc-133)
- [neon-0.32.5](17.Networking_libraries.md#1719-neon-0325)
- [nghttp2-1.52.0](17.Networking_libraries.md#1720-nghttp2-1520)
- [rpcsvc-proto-1.4.3](17.Networking_libraries.md#1721-rpcsvc-proto-143)
- [Serf-1.3.9](17.Networking_libraries.md#1722-serf-139)
- [uhttpmock-0.5.3](17.Networking_libraries.md#1723-uhttpmock-053)

#### 18. Text Web Browsers

- [Links-2.28](18.Text_web_browsers.md#181-links-228)
- [Lynx-2.8.9rel.1](18.Text_web_browsers.md#182-lynx-289rel1)

#### 19. Mail/News Clients

- [Fetchmail-6.4.36](19.Mail_news_clients.md#191-fetchmail-6436)
- [mailx-12.5](19.Mail_news_clients.md#192-mailx-125)
- [Mutt-2.2.9](19.Mail_news_clients.md#193-mutt-229)
- [Procmail-3.22](19.Mail_news_clients.md#194-procmail-322)
- [Other Mail and News Programs](19.Mail_news_clients.md#195-other-mail-and-news-programs)


### V. Servers
--------

#### 20. Major Servers

- [Apache-2.4.55](20.Major_servers.md#201-apache-2455)
- [BIND-9.18.12](20.Major_servers.md#202-bind-91812)
- [ProFTPD-1.3.8](20.Major_servers.md#203-proftpd-138)
- [vsftpd-3.0.5](20.Major_servers.md#204-vsftpd-305)

#### 21. Mail Server Software

- [Dovecot-2.3.20](21.Mail_server_software.md#211-dovecot-2320)
- [Exim-4.96](21.Mail_server_software.md#212-exim-496)
- [Postfix-3.7.4](21.Mail_server_software.md#213-postfix-374)
- [sendmail-8.17.1](21.Mail_server_software.md#214-sendmail-8171)

#### 22. Databases

- [Important Notes About Upgrading Database Server Software](22.Databases.md#221-important-notes-about-upgrading-database-server-software)
- [Berkeley DB-5.3.28](22.Databases.md#222-berkeley-db-5328)
- [lmdb-0.9.29](22.Databases.md#223-lmdb-0929)
- [MariaDB-10.6.12](22.Databases.md#224-mariadb-10612)
- [PostgreSQL-15.2](22.Databases.md#225-postgresql-152)
- [SQLite-3.40.1](22.Databases.md#226-sqlite-3401)

#### 23. Other Server Software

- [OpenLDAP-2.6.4](23.Other_server_software.md#231-openldap-264)
- [Unbound-1.17.1](23.Other_server_software.md#232-unbound-1171)


### VI. Graphical Components
--------

#### 24. Graphical Environments

- [Introduction to Xorg-7](24.Graphical_environments.md#241-introduction-to-xorg-7)
- [util-macros-1.20.0](24.Graphical_environments.md#242-util-macros-1200)
- [xorgproto-2022.2](24.Graphical_environments.md#243-xorgproto-20222)
- [libXau-1.0.11](24.Graphical_environments.md#244-libxau-1011)
- [libXdmcp-1.1.4](24.Graphical_environments.md#245-libxdmcp-114)
- [xcb-proto-1.15.2](24.Graphical_environments.md#246-xcb-proto-1152)
- [libxcb-1.15](24.Graphical_environments.md#247-libxcb-115)
- [Xorg Libraries](24.Graphical_environments.md#248-xorg-libraries)
- [libxcvt-0.1.2](24.Graphical_environments.md#249-libxcvt-012)
- [xcb-util-0.4.1](24.Graphical_environments.md#2410-xcb-util-041)
- [xcb-util-image-0.4.1](24.Graphical_environments.md#2411-xcb-util-image-041)
- [xcb-util-keysyms-0.4.1](24.Graphical_environments.md#2412-xcb-util-keysyms-041)
- [xcb-util-renderutil-0.3.10](24.Graphical_environments.md#2413-xcb-util-renderutil-0310)
- [xcb-util-wm-0.4.2](24.Graphical_environments.md#2414-xcb-util-wm-042)
- [xcb-util-cursor-0.1.4](24.Graphical_environments.md#2415-xcb-util-cursor-014)
- [Mesa-22.3.5](24.Graphical_environments.md#2416-mesa-2235)
- [xbitmaps-1.1.2](24.Graphical_environments.md#2417-xbitmaps-112)
- [Xorg Applications](24.Graphical_environments.md#2418-xorg-applications)
- [xcursor-themes-1.0.6](24.Graphical_environments.md#2419-xcursor-themes-106)
- [Xorg Fonts](24.Graphical_environments.md#2420-xorg-fonts)
- [XKeyboardConfig-2.38](24.Graphical_environments.md#2421-xkeyboardconfig-238)
- [Xwayland-22.1.8](24.Graphical_environments.md#2422-xwayland-2218)
- [Xorg-Server-21.1.7](24.Graphical_environments.md#2423-xorg-server-2117)
- [Xorg Drivers](24.Graphical_environments.md#2424-xorg-drivers)
- [twm-1.0.12](24.Graphical_environments.md#2425-twm-1012)
- [xterm-379](24.Graphical_environments.md#2426-xterm-379)
- [xclock-1.1.1](24.Graphical_environments.md#2427-xclock-111)
- [xinit-1.4.2](24.Graphical_environments.md#2428-xinit-142)
- [Xorg-7 Testing and Configuration](24.Graphical_environments.md#2429-xorg-7-testing-and-configuration)
- [Tuning Fontconfig](24.Graphical_environments.md#2430-tuning-fontconfig)
- [TTF and OTF fonts](24.Graphical_environments.md#2431-ttf-and-otf-fonts)
- [Xorg Legacy](24.Graphical_environments.md#2432-xorg-legacy)

#### 25. Graphical Environment Libraries

- [Amtk-5.6.1](25.Graphical_environment_libraries.md#251-amtk-561)
- [Atkmm-2.28.3](25.Graphical_environment_libraries.md#252-atkmm-2283)
- [at-spi2-core-2.46.0](25.Graphical_environment_libraries.md#253-at-spi2-core-2460)
- [Cairo-1.17.6](25.Graphical_environment_libraries.md#254-cairo-1176)
- [libcairomm-1.0 (cairomm-1.14.0)](25.Graphical_environment_libraries.md#255-libcairomm-10-cairomm-1140)
- [Cogl-1.22.8](25.Graphical_environment_libraries.md#256-cogl-1228)
- [Clutter-1.26.4](25.Graphical_environment_libraries.md#257-clutter-1264)
- [clutter-gst-3.0.27](25.Graphical_environment_libraries.md#258-clutter-gst-3027)
- [clutter-gtk-1.8.4](25.Graphical_environment_libraries.md#259-clutter-gtk-184)
- [colord-gtk-0.3.0](25.Graphical_environment_libraries.md#2510-colord-gtk-030)
- [FLTK-1.3.8](25.Graphical_environment_libraries.md#2511-fltk-138)
- [Freeglut-3.4.0](25.Graphical_environment_libraries.md#2512-freeglut-340)
- [gdk-pixbuf-2.42.10](25.Graphical_environment_libraries.md#2513-gdk-pixbuf-24210)
- [gdk-pixbuf-xlib-2.40.2](25.Graphical_environment_libraries.md#2514-gdk-pixbuf-xlib-2402)
- [GLEW-2.2.0](25.Graphical_environment_libraries.md#2515-glew-220)
- [GLU-9.0.2](25.Graphical_environment_libraries.md#2516-glu-902)
- [GOffice-0.10.55](25.Graphical_environment_libraries.md#2517-goffice-01055)
- [Grantlee-5.3.1](25.Graphical_environment_libraries.md#2518-grantlee-531)
- [Graphene-1.10.8](25.Graphical_environment_libraries.md#2519-graphene-1108)
- [GTK+-2.24.33](25.Graphical_environment_libraries.md#2520-gtk-22433)
- [GTK+-3.24.36](25.Graphical_environment_libraries.md#2521-gtk-32436)
- [GTK-4.8.3](25.Graphical_environment_libraries.md#2522-gtk-483)
- [GTK Engines-2.20.2](25.Graphical_environment_libraries.md#2523-gtk-engines-2202)
- [Gtkmm-3.24.7](25.Graphical_environment_libraries.md#2524-gtkmm-3247)
- [gtk-vnc-1.3.1](25.Graphical_environment_libraries.md#2525-gtk-vnc-131)
- [gtksourceview-3.24.11](25.Graphical_environment_libraries.md#2526-gtksourceview-32411)
- [gtksourceview4-4.8.4](25.Graphical_environment_libraries.md#2527-gtksourceview4-484)
- [gtksourceview5-5.6.2](25.Graphical_environment_libraries.md#2528-gtksourceview5-562)
- [imlib2-1.9.1](25.Graphical_environment_libraries.md#2529-imlib2-191)
- [kColorPicker-0.2.0](25.Graphical_environment_libraries.md#2530-kcolorpicker-020)
- [kImageAnnotator-0.6.0](25.Graphical_environment_libraries.md#2531-kimageannotator-060)
- [keybinder-0.3.1](25.Graphical_environment_libraries.md#2532-keybinder-031)
- [keybinder-3.0-0.3.2](25.Graphical_environment_libraries.md#2533-keybinder-30-032)
- [libadwaita-1.2.2](25.Graphical_environment_libraries.md#2534-libadwaita-122)
- [libdazzle-3.44.0](25.Graphical_environment_libraries.md#2535-libdazzle-3440)
- [libhandy-1.8.1](25.Graphical_environment_libraries.md#2536-libhandy-181)
- [Libdrm-2.4.115](25.Graphical_environment_libraries.md#2537-libdrm-24115)
- [libepoxy-1.5.10](25.Graphical_environment_libraries.md#2538-libepoxy-1510)
- [libglade-2.6.4](25.Graphical_environment_libraries.md#2539-libglade-264)
- [libnotify-0.8.1](25.Graphical_environment_libraries.md#2540-libnotify-081)
- [libxklavier-5.4](25.Graphical_environment_libraries.md#2541-libxklavier-54)
- [Pango-1.50.12](25.Graphical_environment_libraries.md#2542-pango-15012)
- [Pangomm-2.46.3](25.Graphical_environment_libraries.md#2543-pangomm-2463)
- [Qt-5.15.8](25.Graphical_environment_libraries.md#2544-qt-5158)
- [QtWebEngine-5.15.12](25.Graphical_environment_libraries.md#2545-qtwebengine-51512)
- [startup-notification-0.12](25.Graphical_environment_libraries.md#2546-startup-notification-012)
- [Tepl-6.4.0](25.Graphical_environment_libraries.md#2547-tepl-640)
- [WebKitGTK-2.38.5](25.Graphical_environment_libraries.md#2548-webkitgtk-2385)

#### 26. Display Managers

- [GDM-43.0](26.Display_managers.md#261-gdm-430)
- [lightdm-1.32.0](26.Display_managers.md#262-lightdm-1320)
- [lxdm-0.5.3](26.Display_managers.md#263-lxdm-053)

#### 27. Window Managers

- [Fluxbox-1.3.7](27.Window_managers.md#271-fluxbox-137)
- [IceWM-3.3.1](27.Window_managers.md#272-icewm-331)
- [openbox-3.6.1](27.Window_managers.md#273-openbox-361)
- [sawfish-1.13.0](27.Window_managers.md#274-sawfish-1130)

#### 28. Icons

- [adwaita-icon-theme-43](28.Icons.md#281-adwaita-icon-theme-43)
- [breeze-icons-5.103.0](28.Icons.md#282-breeze-icons-51030)
- [gnome-icon-theme-3.12.0](28.Icons.md#283-gnome-icon-theme-3120)
- [gnome-icon-theme-extras-3.12.0](28.Icons.md#284-gnome-icon-theme-extras-3120)
- [gnome-icon-theme-symbolic-3.12.0](28.Icons.md#285-gnome-icon-theme-symbolic-3120)
- [gnome-themes-extra-3.28](28.Icons.md#286-gnome-themes-extra-328)
- [hicolor-icon-theme-0.17](28.Icons.md#287-hicolor-icon-theme-017)
- [icon-naming-utils-0.8.90](28.Icons.md#288-icon-naming-utils-0890)
- [lxde-icon-theme-0.5.1](28.Icons.md#289-lxde-icon-theme-051)
- [oxygen-icons5-5.103.0](28.Icons.md#2810-oxygen-icons5-51030)


### VII. KDE
--------

#### 29. Introduction to KDE

- [KDE Preliminaries](29.Introduction_to_kde.md#291-kde-preliminaries)
- [extra-cmake-modules-5.103.0](29.Introduction_to_kde.md#292-extra-cmake-modules-51030)
- [Phonon-4.11.1](29.Introduction_to_kde.md#293-phonon-4111)
- [Phonon-backend-gstreamer-4.10.0](29.Introduction_to_kde.md#294-phonon-backend-gstreamer-4100)
- [Phonon-backend-vlc-0.11.3](29.Introduction_to_kde.md#295-phonon-backend-vlc-0113)
- [Polkit-Qt-0.114.0](29.Introduction_to_kde.md#296-polkit-qt-01140)
- [libdbusmenu-qt-0.9.3+16.04.20160218](29.Introduction_to_kde.md#297-libdbusmenu-qt-093160420160218)
- [Plasma-wayland-protocols-1.10.0](29.Introduction_to_kde.md#298-plasma-wayland-protocols-1100)
- [kuserfeedback-1.2.0](29.Introduction_to_kde.md#299-kuserfeedback-120)

#### 30. KDE Frameworks 5

- [KDE Frameworks 5 Pre-installation Configuration](30.KDE_frameworks_5.md#301-kde-frameworks-5-pre-installation-configuration)
- [Building KDE Frameworks 5 (KF5)](30.KDE_frameworks_5.md#302-building-kde-frameworks-5-kf5)

#### 31. KDE Frameworks 5 Based Applications

- [Ark-22.12.2](31.KDE_frameworks_5_based_applications.md#311-ark-22122)
- [Kdenlive-22.12.2](31.KDE_frameworks_5_based_applications.md#312-kdenlive-22122)
- [KMix-22.12.2](31.KDE_frameworks_5_based_applications.md#313-kmix-22122)
- [kio-extras-22.12.2](31.KDE_frameworks_5_based_applications.md#314-kio-extras-22122)
- [Khelpcenter-22.12.2](31.KDE_frameworks_5_based_applications.md#315-khelpcenter-22122)
- [Konsole-22.12.2](31.KDE_frameworks_5_based_applications.md#316-konsole-22122)
- [libkexiv2-22.12.2](31.KDE_frameworks_5_based_applications.md#317-libkexiv2-22122)
- [Okular-22.12.2](31.KDE_frameworks_5_based_applications.md#318-okular-22122)
- [libkdcraw-22.12.2](31.KDE_frameworks_5_based_applications.md#319-libkdcraw-22122)
- [Gwenview-22.12.2](31.KDE_frameworks_5_based_applications.md#3110-gwenview-22122)
- [libkcddb-22.12.2](31.KDE_frameworks_5_based_applications.md#3111-libkcddb-22122)
- [k3b-22.12.2](31.KDE_frameworks_5_based_applications.md#3112-k3b-22122)
- [Further KDE5 packages](31.KDE_frameworks_5_based_applications.md#3113-further-kde5-packages)

#### 32. KDE Plasma 5

- [Building Plasma 5](32.KDE_plasma_5.md#321-building-plasma-5)


### VIII. GNOME
--------

#### 33. GNOME Libraries and Desktop

**Libraries**

- [Gcr-3.41.1](33.GNOME_libraries_and_desktop.md#331-gcr-3411)
- [Gcr-4.0.0](33.GNOME_libraries_and_desktop.md#332-gcr-400)
- [gsettings-desktop-schemas-43.0](33.GNOME_libraries_and_desktop.md#333-gsettings-desktop-schemas-430)
- [libsecret-0.20.5](33.GNOME_libraries_and_desktop.md#334-libsecret-0205)
- [rest-0.9.1](33.GNOME_libraries_and_desktop.md#335-rest-091)
- [totem-pl-parser-3.26.6](33.GNOME_libraries_and_desktop.md#336-totem-pl-parser-3266)
- [VTE-0.70.3](33.GNOME_libraries_and_desktop.md#337-vte-0703)
- [yelp-xsl-42.1](33.GNOME_libraries_and_desktop.md#338-yelp-xsl-421)
- [GConf-3.2.6](33.GNOME_libraries_and_desktop.md#339-gconf-326)
- [geocode-glib-3.26.4](33.GNOME_libraries_and_desktop.md#3310-geocode-glib-3264)
- [Gjs-1.74.1](33.GNOME_libraries_and_desktop.md#3311-gjs-1741)
- [gnome-autoar-0.4.3](33.GNOME_libraries_and_desktop.md#3312-gnome-autoar-043)
- [gnome-desktop-43.2](33.GNOME_libraries_and_desktop.md#3313-gnome-desktop-432)
- [gnome-menus-3.36.0](33.GNOME_libraries_and_desktop.md#3314-gnome-menus-3360)
- [gnome-video-effects-0.5.0](33.GNOME_libraries_and_desktop.md#3315-gnome-video-effects-050)
- [gnome-online-accounts-3.46.0](33.GNOME_libraries_and_desktop.md#3316-gnome-online-accounts-3460)
- [Grilo-0.3.15](33.GNOME_libraries_and_desktop.md#3317-grilo-0315)
- [libchamplain-0.12.21](33.GNOME_libraries_and_desktop.md#3318-libchamplain-01221)
- [libgdata-0.18.1](33.GNOME_libraries_and_desktop.md#3319-libgdata-0181)
- [libgee-0.20.6](33.GNOME_libraries_and_desktop.md#3320-libgee-0206)
- [libgtop-2.40.0](33.GNOME_libraries_and_desktop.md#3321-libgtop-2400)
- [libgweather-4.2.0](33.GNOME_libraries_and_desktop.md#3322-libgweather-420)
- [libpeas-1.34.0](33.GNOME_libraries_and_desktop.md#3323-libpeas-1340)
- [libshumate-1.0.3](33.GNOME_libraries_and_desktop.md#3324-libshumate-103)
- [libwnck-43.0](33.GNOME_libraries_and_desktop.md#3325-libwnck-430)
- [evolution-data-server-3.46.4](33.GNOME_libraries_and_desktop.md#3326-evolution-data-server-3464)
- [Tracker-3.4.2](33.GNOME_libraries_and_desktop.md#3327-tracker-342)
- [Tracker-miners-3.4.3](33.GNOME_libraries_and_desktop.md#3328-tracker-miners-343)
- [GSound-1.0.3](33.GNOME_libraries_and_desktop.md#3329-gsound-103)

**GNOME Desktop Components**

- [DConf-0.40.0 / DConf-Editor-43.0](33.GNOME_libraries_and_desktop.md#3330-dconf-0400--dconf-editor-430)
- [gnome-backgrounds-43.1](33.GNOME_libraries_and_desktop.md#3331-gnome-backgrounds-431)
- [Gvfs-1.50.3](33.GNOME_libraries_and_desktop.md#3332-gvfs-1503)
- [gexiv2-0.14.0](33.GNOME_libraries_and_desktop.md#3333-gexiv2-0140)
- [Nautilus-43.2](33.GNOME_libraries_and_desktop.md#3334-nautilus-432)
- [gnome-bluetooth-42.5](33.GNOME_libraries_and_desktop.md#3335-gnome-bluetooth-425)
- [gnome-keyring-42.1](33.GNOME_libraries_and_desktop.md#3336-gnome-keyring-421)
- [gnome-settings-daemon-43.0](33.GNOME_libraries_and_desktop.md#3337-gnome-settings-daemon-430)
- [gnome-control-center-43.4.1](33.GNOME_libraries_and_desktop.md#3338-gnome-control-center-4341)
- [Mutter-43.3](33.GNOME_libraries_and_desktop.md#3339-mutter-433)
- [gnome-shell-43.3](33.GNOME_libraries_and_desktop.md#3340-gnome-shell-433)
- [gnome-shell-extensions-43.1](33.GNOME_libraries_and_desktop.md#3341-gnome-shell-extensions-431)
- [gnome-session-43.0](33.GNOME_libraries_and_desktop.md#3342-gnome-session-430)
- [gnome-tweaks-40.10](33.GNOME_libraries_and_desktop.md#3343-gnome-tweaks-4010)
- [gnome-user-docs-43.0](33.GNOME_libraries_and_desktop.md#3344-gnome-user-docs-430)
- [Yelp-42.2](33.GNOME_libraries_and_desktop.md#3345-yelp-422)

#### 34. GNOME Applications

- [Baobab-43.0](34.GNOME_applications.md#341-baobab-430)
- [Brasero-3.12.3](34.GNOME_applications.md#342-brasero-3123)
- [Cheese-43.0](34.GNOME_applications.md#343-cheese-430)
- [EOG-43.2](34.GNOME_applications.md#344-eog-432)
- [Evince-43.1](34.GNOME_applications.md#345-evince-431)
- [Evolution-3.46.4](34.GNOME_applications.md#346-evolution-3464)
- [File-Roller-43.0](34.GNOME_applications.md#347-file-roller-430)
- [gnome-calculator-43.0.1](34.GNOME_applications.md#348-gnome-calculator-4301)
- [gnome-color-manager-3.36.0](34.GNOME_applications.md#349-gnome-color-manager-3360)
- [gnome-disk-utility-43.0](34.GNOME_applications.md#3410-gnome-disk-utility-430)
- [gnome-logs-43.0](34.GNOME_applications.md#3411-gnome-logs-430)
- [gnome-maps-43.4](34.GNOME_applications.md#3412-gnome-maps-434)
- [gnome-nettool-42.0](34.GNOME_applications.md#3413-gnome-nettool-420)
- [gnome-power-manager-43.0](34.GNOME_applications.md#3414-gnome-power-manager-430)
- [gnome-screenshot-41.0](34.GNOME_applications.md#3415-gnome-screenshot-410)
- [gnome-system-monitor-42.0](34.GNOME_applications.md#3416-gnome-system-monitor-420)
- [gnome-terminal-3.46.8](34.GNOME_applications.md#3417-gnome-terminal-3468)
- [gnome-weather-43.0](34.GNOME_applications.md#3418-gnome-weather-430)
- [Gucharmap-15.0.2](34.GNOME_applications.md#3419-gucharmap-1502)
- [Seahorse-43.0](34.GNOME_applications.md#3420-seahorse-430)
- [Vinagre-3.22.0](34.GNOME_applications.md#3421-vinagre-3220)


### IX. Xfce
--------

#### 35. Xfce Desktop

- [libxfce4util-4.18.1](35.Xfce_desktop.md#351-libxfce4util-4181)
- [Xfconf-4.18.0](35.Xfce_desktop.md#352-xfconf-4180)
- [libxfce4ui-4.18.2](35.Xfce_desktop.md#353-libxfce4ui-4182)
- [Exo-4.18.0](35.Xfce_desktop.md#354-exo-4180)
- [Garcon-4.18.0](35.Xfce_desktop.md#355-garcon-4180)
- [xfce4-panel-4.18.2](35.Xfce_desktop.md#356-xfce4-panel-4182)
- [thunar-4.18.4](35.Xfce_desktop.md#357-thunar-4184)
- [thunar-volman-4.18.0](35.Xfce_desktop.md#358-thunar-volman-4180)
- [tumbler-4.18.0](35.Xfce_desktop.md#359-tumbler-4180)
- [xfce4-appfinder-4.18.0](35.Xfce_desktop.md#3510-xfce4-appfinder-4180)
- [xfce4-power-manager-4.18.1](35.Xfce_desktop.md#3511-xfce4-power-manager-4181)
- [xfce4-settings-4.18.2](35.Xfce_desktop.md#3512-xfce4-settings-4182)
- [Xfdesktop-4.18.1](35.Xfce_desktop.md#3513-xfdesktop-4181)
- [Xfwm4-4.18.0](35.Xfce_desktop.md#3514-xfwm4-4180)
- [xfce4-session-4.18.1](35.Xfce_desktop.md#3515-xfce4-session-4181)

#### 36. Xfce Applications

- [Parole-4.18.0](36.Xfce_applications.md#361-parole-4180)
- [xfce4-terminal-1.0.4](36.Xfce_applications.md#362-xfce4-terminal-104)
- [Xfburn-0.6.2](36.Xfce_applications.md#363-xfburn-062)
- [Ristretto-0.13.0](36.Xfce_applications.md#364-ristretto-0130)
- [xfce4-dev-tools-4.18.0](36.Xfce_applications.md#365-xfce4-dev-tools-4180)
- [xfce4-notifyd-0.8.1](36.Xfce_applications.md#366-xfce4-notifyd-081)
- [xfce4-pulseaudio-plugin-0.4.5](36.Xfce_applications.md#367-xfce4-pulseaudio-plugin-045)


### X. LXDE
--------

#### 37. LXDE Desktop

- [lxmenu-data-0.1.5](37.LXDE_desktop.md#371-lxmenu-data-015)
- [libfm-extra-1.3.2](37.LXDE_desktop.md#372-libfm-extra-132)
- [menu-cache-1.1.0](37.LXDE_desktop.md#373-menu-cache-110)
- [libfm-1.3.2](37.LXDE_desktop.md#374-libfm-132)
- [PCManFM-1.3.2](37.LXDE_desktop.md#375-pcmanfm-132)
- [libwnck-2.30.7](37.LXDE_desktop.md#376-libwnck-2307)
- [LXPanel-0.10.1](37.LXDE_desktop.md#377-lxpanel-0101)
- [LXAppearance-0.6.3](37.LXDE_desktop.md#378-lxappearance-063)
- [LXSession-0.5.5](37.LXDE_desktop.md#379-lxsession-055)
- [lxde-common-0.99.2](37.LXDE_desktop.md#3710-lxde-common-0992)

#### 38. LXDE Applications

- [GPicView-0.2.5](38.LXDE_applications.md#381-gpicview-025)
- [lxappearance-obconf-0.2.3](38.LXDE_applications.md#382-lxappearance-obconf-023)
- [LXInput-0.3.5](38.LXDE_applications.md#383-lxinput-035)
- [LXRandR-0.3.2](38.LXDE_applications.md#384-lxrandr-032)
- [LXTask-0.1.10](38.LXDE_applications.md#385-lxtask-0110)
- [Vte-0.28.2](38.LXDE_applications.md#386-vte-0282)
- [LXTerminal-0.4.0](38.LXDE_applications.md#387-lxterminal-040)


### XI. X Software
--------

#### 39. Office Programs

- [AbiWord-3.0.5](39.Office_programs.md#391-abiword-305)
- [Gnumeric-1.12.55](39.Office_programs.md#392-gnumeric-11255)
- [LibreOffice-7.5.0](39.Office_programs.md#393-libreoffice-750)

#### 40. Graphical Web Browsers

- [Epiphany-43.1](40.Graphical_web_browsers.md#401-epiphany-431)
- [Falkon-22.12.2](40.Graphical_web_browsers.md#402-falkon-22122)
- [Firefox-102.8.0esr](40.Graphical_web_browsers.md#403-firefox-10280esr)
- [SeaMonkey-2.53.15](40.Graphical_web_browsers.md#404-seamonkey-25315)

#### 41. Other X-based Programs

- [Balsa-2.6.4](41.Other_x-based_programs.md#411-balsa-264)
- [feh-3.9.1](41.Other_x-based_programs.md#412-feh-391)
- [FontForge-20230101](41.Other_x-based_programs.md#413-fontforge-20230101)
- [Gimp-2.10.32](41.Other_x-based_programs.md#414-gimp-21032)
- [Gparted-1.5.0](41.Other_x-based_programs.md#415-gparted-150)
- [HexChat-2.16.1](41.Other_x-based_programs.md#416-hexchat-2161)
- [Inkscape-1.2.2](41.Other_x-based_programs.md#417-inkscape-122)
- [Pidgin-2.14.12](41.Other_x-based_programs.md#418-pidgin-21412)
- [Rox-Filer-2.11](41.Other_x-based_programs.md#419-rox-filer-211)
- [rxvt-unicode-9.31](41.Other_x-based_programs.md#4110-rxvt-unicode-931)
- [Thunderbird-102.8.0](41.Other_x-based_programs.md#4111-thunderbird-10280)
- [Tigervnc-1.13.0](41.Other_x-based_programs.md#4112-tigervnc-1130)
- [Transmission-4.0.1](41.Other_x-based_programs.md#4113-transmission-401)
- [xarchiver-0.5.4.20](41.Other_x-based_programs.md#4114-xarchiver-05420)
- [xdg-utils-1.1.3](41.Other_x-based_programs.md#4115-xdg-utils-113)
- [XScreenSaver-6.06](41.Other_x-based_programs.md#4116-xscreensaver-606)


### XII. Multimedia
--------

#### 42. Multimedia Libraries and Drivers

- [ALSA-1.2.7](42.Multimedia_libraries_and_drivers.md#421-alsa-127)
- [alsa-lib-1.2.8](42.Multimedia_libraries_and_drivers.md#422-alsa-lib-128)
- [alsa-plugins-1.2.7.1](42.Multimedia_libraries_and_drivers.md#423-alsa-plugins-1271)
- [alsa-utils-1.2.8](42.Multimedia_libraries_and_drivers.md#424-alsa-utils-128)
- [alsa-tools-1.2.5](42.Multimedia_libraries_and_drivers.md#425-alsa-tools-125)
- [alsa-firmware-1.2.4](42.Multimedia_libraries_and_drivers.md#426-alsa-firmware-124)
- [alsa-oss-1.1.8](42.Multimedia_libraries_and_drivers.md#427-alsa-oss-118)
- [AudioFile-0.3.6](42.Multimedia_libraries_and_drivers.md#428-audiofile-036)
- [FAAC-1_30](42.Multimedia_libraries_and_drivers.md#429-faac-1_30)
- [FAAD2-2.10.1](42.Multimedia_libraries_and_drivers.md#4210-faad2-2101)
- [fdk-aac-2.0.2](42.Multimedia_libraries_and_drivers.md#4211-fdk-aac-202)
- [FLAC-1.4.2](42.Multimedia_libraries_and_drivers.md#4212-flac-142)
- [frei0r-plugins-1.8.0](42.Multimedia_libraries_and_drivers.md#4213-frei0r-plugins-180)
- [gavl-1.4.0](42.Multimedia_libraries_and_drivers.md#4214-gavl-140)
- [gstreamer-1.22.0](42.Multimedia_libraries_and_drivers.md#4215-gstreamer-1220)
- [gst-plugins-base-1.22.0](42.Multimedia_libraries_and_drivers.md#4216-gst-plugins-base-1220)
- [gst-plugins-good-1.22.0](42.Multimedia_libraries_and_drivers.md#4217-gst-plugins-good-1220)
- [gst-plugins-bad-1.22.0](42.Multimedia_libraries_and_drivers.md#4218-gst-plugins-bad-1220)
- [gst-plugins-ugly-1.22.0](42.Multimedia_libraries_and_drivers.md#4219-gst-plugins-ugly-1220)
- [gst-libav-1.22.0](42.Multimedia_libraries_and_drivers.md#4220-gst-libav-1220)
- [gstreamer-vaapi-1.22.0](42.Multimedia_libraries_and_drivers.md#4221-gstreamer-vaapi-1220)
- [id3lib-3.8.3](42.Multimedia_libraries_and_drivers.md#4222-id3lib-383)
- [Liba52-0.7.4](42.Multimedia_libraries_and_drivers.md#4223-liba52-074)
- [Libao-1.2.0](42.Multimedia_libraries_and_drivers.md#4224-libao-120)
- [libass-0.17.0](42.Multimedia_libraries_and_drivers.md#4225-libass-0170)
- [libcanberra-0.30](42.Multimedia_libraries_and_drivers.md#4226-libcanberra-030)
- [libcddb-1.3.2](42.Multimedia_libraries_and_drivers.md#4227-libcddb-132)
- [libcdio-2.1.0](42.Multimedia_libraries_and_drivers.md#4228-libcdio-210)
- [libdiscid-0.6.2](42.Multimedia_libraries_and_drivers.md#4229-libdiscid-062)
- [libdvdcss-1.4.3](42.Multimedia_libraries_and_drivers.md#4230-libdvdcss-143)
- [Libdvdread-6.1.3](42.Multimedia_libraries_and_drivers.md#4231-libdvdread-613)
- [Libdvdnav-6.1.1](42.Multimedia_libraries_and_drivers.md#4232-libdvdnav-611)
- [Libdv-1.0.0](42.Multimedia_libraries_and_drivers.md#4233-libdv-100)
- [libmad-0.15.1b](42.Multimedia_libraries_and_drivers.md#4234-libmad-0151b)
- [libmpeg2-0.5.1](42.Multimedia_libraries_and_drivers.md#4235-libmpeg2-051)
- [libmusicbrainz-2.1.5](42.Multimedia_libraries_and_drivers.md#4236-libmusicbrainz-215)
- [libmusicbrainz-5.1.0](42.Multimedia_libraries_and_drivers.md#4237-libmusicbrainz-510)
- [libogg-1.3.5](42.Multimedia_libraries_and_drivers.md#4238-libogg-135)
- [libquicktime-1.2.4](42.Multimedia_libraries_and_drivers.md#4239-libquicktime-124)
- [libsamplerate-0.2.2](42.Multimedia_libraries_and_drivers.md#4240-libsamplerate-022)
- [libsndfile-1.2.0](42.Multimedia_libraries_and_drivers.md#4241-libsndfile-120)
- [libtheora-1.1.1](42.Multimedia_libraries_and_drivers.md#4242-libtheora-111)
- [libvorbis-1.3.7](42.Multimedia_libraries_and_drivers.md#4243-libvorbis-137)
- [libvpx-1.13.0](42.Multimedia_libraries_and_drivers.md#4244-libvpx-1130)
- [MLT-7.12.0](42.Multimedia_libraries_and_drivers.md#4245-mlt-7120)
- [Opus-1.3.1](42.Multimedia_libraries_and_drivers.md#4246-opus-131)
- [Pipewire-0.3.66](42.Multimedia_libraries_and_drivers.md#4247-pipewire-0366)
- [PulseAudio-16.1](42.Multimedia_libraries_and_drivers.md#4248-pulseaudio-161)
- [SBC-2.0](42.Multimedia_libraries_and_drivers.md#4249-sbc-20)
- [SDL-1.2.15](42.Multimedia_libraries_and_drivers.md#4250-sdl-1215)
- [SDL2-2.26.3](42.Multimedia_libraries_and_drivers.md#4251-sdl2-2263)
- [sound-theme-freedesktop-0.8](42.Multimedia_libraries_and_drivers.md#4252-sound-theme-freedesktop-08)
- [SoundTouch-2.3.2](42.Multimedia_libraries_and_drivers.md#4253-soundtouch-232)
- [Speex-1.2.1](42.Multimedia_libraries_and_drivers.md#4254-speex-121)
- [Taglib-1.13](42.Multimedia_libraries_and_drivers.md#4255-taglib-113)
- [v4l-utils-1.22.1](42.Multimedia_libraries_and_drivers.md#4256-v4l-utils-1221)
- [x264-20230215](42.Multimedia_libraries_and_drivers.md#4257-x264-20230215)
- [x265-20230215](42.Multimedia_libraries_and_drivers.md#4258-x265-20230215)
- [xine-lib-1.2.13](42.Multimedia_libraries_and_drivers.md#4259-xine-lib-1213)
- [XviD-1.3.7](42.Multimedia_libraries_and_drivers.md#4260-xvid-137)

#### 43. Audio Utilities

- [Audacious-4.2](43.Audio_utilities.md#431-audacious-42)
- [CDParanoia-III-10.2](43.Audio_utilities.md#432-cdparanoia-iii-102)
- [kwave-22.12.2](43.Audio_utilities.md#433-kwave-22122)
- [LAME-3.100](43.Audio_utilities.md#434-lame-3100)
- [mpg123-1.31.2](43.Audio_utilities.md#435-mpg123-1312)
- [pavucontrol-5.0](43.Audio_utilities.md#436-pavucontrol-50)
- [pnmixer-0.7.2](43.Audio_utilities.md#437-pnmixer-072)
- [vorbis-tools-1.4.2](43.Audio_utilities.md#438-vorbis-tools-142)

#### 44. Video Utilities

- [FFmpeg-5.1.2](44.Video_utilities.md#441-ffmpeg-512)
- [MPlayer-1.5](44.Video_utilities.md#442-mplayer-15)
- [Transcode-1.1.7](44.Video_utilities.md#443-transcode-117)
- [VLC-3.0.18](44.Video_utilities.md#444-vlc-3018)
- [xine-ui-0.99.14](44.Video_utilities.md#445-xine-ui-09914)

#### 45. CD/DVD-Writing Utilities

- [Cdrdao-1.2.4](45.CD_dvd-writing_utilities.md#451-cdrdao-124)
- [Cdrtools-3.02a09](45.CD_dvd-writing_utilities.md#452-cdrtools-302a09)
- [dvd+rw-tools-7.1](45.CD_dvd-writing_utilities.md#453-dvdrw-tools-71)
- [libburn-1.5.4](45.CD_dvd-writing_utilities.md#454-libburn-154)
- [libisoburn-1.5.4](45.CD_dvd-writing_utilities.md#455-libisoburn-154)
- [libisofs-1.5.4](45.CD_dvd-writing_utilities.md#456-libisofs-154)


### XIII. Printing, Scanning and Typesetting
--------

#### 46. Printing

- [Cups-2.4.2](46.Printing.md#461-cups-242)
- [cups-filters-1.28.16](46.Printing.md#462-cups-filters-12816)
- [ghostscript-10.00.0](46.Printing.md#463-ghostscript-10000)
- [Gutenprint-5.3.4](46.Printing.md#464-gutenprint-534)

#### 47. Scanning

- [SANE-1.0.32](47.Scanning.md#471-sane-1032)
- [XSane-0.999](47.Scanning.md#472-xsane-0999)

#### 48. Standard Generalized Markup Language (SGML)

- [sgml-common-0.6.3](48.Standard_generalized_markup_language_SGML.md#481-sgml-common-063)
- [docbook-3.1-dtd](48.Standard_generalized_markup_language_SGML.md#482-docbook-31-dtd)
- [docbook-4.5-dtd](48.Standard_generalized_markup_language_SGML.md#483-docbook-45-dtd)
- [OpenSP-1.5.2](48.Standard_generalized_markup_language_SGML.md#484-opensp-152)
- [OpenJade-1.3.2](48.Standard_generalized_markup_language_SGML.md#485-openjade-132)
- [docbook-dsssl-1.79](48.Standard_generalized_markup_language_SGML.md#486-docbook-dsssl-179)
- [DocBook-utils-0.6.14](48.Standard_generalized_markup_language_SGML.md#487-docbook-utils-0614)

#### 49. Extensible Markup Language (XML)

- [docbook-xml-4.5](49.Extensible_markup_language_XML.md#491-docbook-xml-45)
- [docbook-xml-5.0](49.Extensible_markup_language_XML.md#492-docbook-xml-50)
- [docbook-xml-5.1](49.Extensible_markup_language_XML.md#493-docbook-xml-51)
- [docbook-xsl-nons-1.79.2](49.Extensible_markup_language_XML.md#494-docbook-xsl-nons-1792)
- [itstool-2.0.7](49.Extensible_markup_language_XML.md#495-itstool-207)
- [xmlto-0.0.28](49.Extensible_markup_language_XML.md#496-xmlto-0028)

#### 50. PostScript

- [Enscript-1.6.6](50.PostScript.md#501-enscript-166)
- [ePDFView-0.1.8](50.PostScript.md#502-epdfview-018)
- [fop-2.8](50.PostScript.md#503-fop-28)
- [MuPDF-1.21.1](50.PostScript.md#504-mupdf-1211)
- [paps-0.7.1](50.PostScript.md#505-paps-071)

#### 51. Typesetting

- [Setting the PATH for TeX Live](51.Typesetting.md#511-setting-the-path-for-tex-live)
- [install-tl-unx](51.Typesetting.md#512-install-tl-unx)
- [texlive-20220321-source](51.Typesetting.md#513-texlive-20220321-source)
- [asymptote-2.85](51.Typesetting.md#514-asymptote-285)
- [biber-2.18](51.Typesetting.md#515-biber-218)
- [dvisvgm-3.0.3](51.Typesetting.md#516-dvisvgm-303)
- [xindy-2.5.1](51.Typesetting.md#517-xindy-251)


- [A. Creative Commons License](A.Creative_commons_license.md)

- [B. The MIT License](B.The_mit_license.md)

- [Glossary](Glossary.md)

- [Index](Index.md)