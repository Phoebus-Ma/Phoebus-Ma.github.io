
# Linux from scratch manual

## Table of Contents

## Preface
--------

### Foreword

- [Audience](Foreword.md#audience)
- [LFS Target Architectures](Foreword.md#lfs-target-architectures)
- [Prerequisites](Foreword.md#prerequisites)
- [LFS and Standards](Foreword.md#lfs-and-standards)
- [Rationale for Packages in the Book](Foreword.md#rationale-for-packages-in-the-book)
- [Typography](Foreword.md#typography)
- [Structure](Foreword.md#structure)
- [Errata and Security Advisories](Foreword.md#errata-and-security-advisories)


## I. Introduction
--------

### 1. Introduction

- [How to Build an LFS System](1.Introduction.md#11-how-to-build-an-lfs-system)
- [What's new since the last release](1.Introduction.md#12-whats-new-since-the-last-release)
- [Changelog](1.Introduction.md#13-changelog)
- [Resources](1.Introduction.md#14-resources)
- [Help](1.Introduction.md#15-help)


## II. Preparing for the Build
--------

### 2. Preparing the Host System

- [Introduction](2.Preparing_the_host_system.md#21-introduction)
- [Host System Requirements](2.Preparing_the_host_system.md#22-host-system-requirements)
- [Building LFS in Stages](2.Preparing_the_host_system.md#23-building-lfs-in-stages)
- [Creating a New Partition](2.Preparing_the_host_system.md#24-creating-a-new-partition)
- [Creating a File System on the Partition](2.Preparing_the_host_system.md#25-creating-a-file-system-on-the-partition)
- [Setting The $LFS Variable](2.Preparing_the_host_system.md#26-setting-the-lfs-variable)
- [Mounting the New Partition](2.Preparing_the_host_system.md#27-mounting-the-new-partition)

### 3. Packages and Patches

- [Introduction](3.Packages_and_patches.md#31-introduction)
- [All Packages](3.Packages_and_patches.md#32-all-packages)
- [Needed Patches](3.Packages_and_patches.md#33-needed-patches)

### 4. Final Preparations

- [Introduction](4.Final_preparations.md#41-introduction)
- [Creating a Limited Directory Layout in the LFS Filesystem](4.Final_preparations.md#42-creating-a-limited-directory-layout-in-the-lfs-filesystem)
- [Adding the LFS User](4.Final_preparations.md#43-adding-the-lfs-user)
- [Setting Up the Environment](4.Final_preparations.md#44-setting-up-the-environment)
- [About SBUs](4.Final_preparations.md#45-about-sbus)
- [About the Test Suites](4.Final_preparations.md#46-about-the-test-suites)


## III. Building the LFS Cross Toolchain and Temporary Tools
--------

### Important Preliminary Material

- [Introduction](Important_preliminary_material.md#introduction)
- [Toolchain Technical Notes](Important_preliminary_material.md#toolchain-technical-notes)
- [General Compilation Instructions](Important_preliminary_material.md#general-compilation-instructions)

### 5. Compiling a Cross-Toolchain

- [Introduction](5.Compiling_a_cross_toolchain.md#51-introduction)
- [Binutils-2.40 - Pass 1](5.Compiling_a_cross_toolchain.md#52-binutils-240---pass-1)
- [GCC-12.2.0 - Pass 1](5.Compiling_a_cross_toolchain.md#53-gcc-1220---pass-1)
- [Linux-6.1.11 API Headers](5.Compiling_a_cross_toolchain.md#54-linux-6111-api-headers)
- [Glibc-2.37](5.Compiling_a_cross_toolchain.md#55-glibc-237)
- [Libstdc++ from GCC-12.2.0](5.Compiling_a_cross_toolchain.md#56-libstdc-from-gcc-1220)

### 6. Cross Compiling Temporary Tools

- [Introduction](6.Cross_compiling_temporary_tools.md#61-introduction)
- [M4-1.4.19](6.Cross_compiling_temporary_tools.md#62-m4-1419)
- [Ncurses-6.4](6.Cross_compiling_temporary_tools.md#63-ncurses-64)
- [Bash-5.2.15](6.Cross_compiling_temporary_tools.md#64-bash-5215)
- [Coreutils-9.1](6.Cross_compiling_temporary_tools.md#65-coreutils-91)
- [Diffutils-3.9](6.Cross_compiling_temporary_tools.md#66-diffutils-39)
- [File-5.44](6.Cross_compiling_temporary_tools.md#67-file-544)
- [Findutils-4.9.0](6.Cross_compiling_temporary_tools.md#68-findutils-490)
- [Gawk-5.2.1](6.Cross_compiling_temporary_tools.md#69-gawk-521)
- [Grep-3.8](6.Cross_compiling_temporary_tools.md#610-grep-38)
- [Gzip-1.12](6.Cross_compiling_temporary_tools.md#611-gzip-112)
- [Make-4.4](6.Cross_compiling_temporary_tools.md#612-make-44)
- [Patch-2.7.6](6.Cross_compiling_temporary_tools.md#613-patch-276)
- [Sed-4.9](6.Cross_compiling_temporary_tools.md#614-sed-49)
- [Tar-1.34](6.Cross_compiling_temporary_tools.md#615-tar-134)
- [Xz-5.4.1](6.Cross_compiling_temporary_tools.md#616-xz-541)
- [Binutils-2.40 - Pass 2](6.Cross_compiling_temporary_tools.md#617-binutils-240---pass-2)
- [GCC-12.2.0 - Pass 2](6.Cross_compiling_temporary_tools.md#618-gcc-1220---pass-2)

### 7. Entering Chroot and Building Additional Temporary Tools

- [Introduction](7.Entering_chroot_and_building_additional_temporary_tools.md#71-introduction)
- [Changing Ownership](7.Entering_chroot_and_building_additional_temporary_tools.md#72-changing-ownership)
- [Preparing Virtual Kernel File Systems](7.Entering_chroot_and_building_additional_temporary_tools.md#73-preparing-virtual-kernel-file-systems)
- [Entering the Chroot Environment](7.Entering_chroot_and_building_additional_temporary_tools.md#74-entering-the-chroot-environment)
- [Creating Directories](7.Entering_chroot_and_building_additional_temporary_tools.md#75-creating-directories)
- [Creating Essential Files and Symlinks](7.Entering_chroot_and_building_additional_temporary_tools.md#76-creating-essential-files-and-symlinks)
- [Gettext-0.21.1](7.Entering_chroot_and_building_additional_temporary_tools.md#77-gettext-0211)
- [Bison-3.8.2](7.Entering_chroot_and_building_additional_temporary_tools.md#78-bison-382)
- [Perl-5.36.0](7.Entering_chroot_and_building_additional_temporary_tools.md#79-perl-5360)
- [Python-3.11.2](7.Entering_chroot_and_building_additional_temporary_tools.md#710-python-3112)
- [Texinfo-7.0.2](7.Entering_chroot_and_building_additional_temporary_tools.md#711-texinfo-702)
- [Util-linux-2.38.1](7.Entering_chroot_and_building_additional_temporary_tools.md#712-util-linux-2381)
- [Cleaning up and Saving the Temporary System](7.Entering_chroot_and_building_additional_temporary_tools.md#713-cleaning-up-and-saving-the-temporary-system)


## IV. Building the LFS System
--------

### 8. Installing Basic System Software

- [Introduction](8.Installing_basic_system_software.md#81-introduction)
- [Package Management](8.Installing_basic_system_software.md#82-package-management)
- [Man-pages-6.03](8.Installing_basic_system_software.md#83-man-pages-603)
- [Iana-Etc-20230202](8.Installing_basic_system_software.md#84-iana-etc-20230202)
- [Glibc-2.37](8.Installing_basic_system_software.md#85-glibc-237)
- [Zlib-1.2.13](8.Installing_basic_system_software.md#86-zlib-1213)
- [Bzip2-1.0.8](8.Installing_basic_system_software.md#87-bzip2-108)
- [Xz-5.4.1](8.Installing_basic_system_software.md#88-xz-541)
- [Zstd-1.5.4](8.Installing_basic_system_software.md#89-zstd-154)
- [File-5.44](8.Installing_basic_system_software.md#810-file-544)
- [Readline-8.2](8.Installing_basic_system_software.md#811-readline-82)
- [M4-1.4.19](8.Installing_basic_system_software.md#812-m4-1419)
- [Bc-6.2.4](8.Installing_basic_system_software.md#813-bc-624)
- [Flex-2.6.4](8.Installing_basic_system_software.md#814-flex-264)
- [Tcl-8.6.13](8.Installing_basic_system_software.md#815-tcl-8613)
- [Expect-5.45.4](8.Installing_basic_system_software.md#816-expect-5454)
- [DejaGNU-1.6.3](8.Installing_basic_system_software.md#817-dejagnu-163)
- [Binutils-2.40](8.Installing_basic_system_software.md#818-binutils-240)
- [GMP-6.2.1](8.Installing_basic_system_software.md#819-gmp-621)
- [MPFR-4.2.0](8.Installing_basic_system_software.md#820-mpfr-420)
- [MPC-1.3.1](8.Installing_basic_system_software.md#821-mpc-131)
- [Attr-2.5.1](8.Installing_basic_system_software.md#822-attr-251)
- [Acl-2.3.1](8.Installing_basic_system_software.md#823-acl-231)
- [Libcap-2.67](8.Installing_basic_system_software.md#824-libcap-267)
- [Shadow-4.13](8.Installing_basic_system_software.md#825-shadow-413)
- [GCC-12.2.0](8.Installing_basic_system_software.md#826-gcc-1220)
- [Pkg-config-0.29.2](8.Installing_basic_system_software.md#827-pkg-config-0292)
- [Ncurses-6.4](8.Installing_basic_system_software.md#828-ncurses-64)
- [Sed-4.9](8.Installing_basic_system_software.md#829-sed-49)
- [Psmisc-23.6](8.Installing_basic_system_software.md#830-psmisc-236)
- [Gettext-0.21.1](8.Installing_basic_system_software.md#831-gettext-0211)
- [Bison-3.8.2](8.Installing_basic_system_software.md#832-bison-382)
- [Grep-3.8](8.Installing_basic_system_software.md#833-grep-38)
- [Bash-5.2.15](8.Installing_basic_system_software.md#834-bash-5215)
- [Libtool-2.4.7](8.Installing_basic_system_software.md#835-libtool-247)
- [GDBM-1.23](8.Installing_basic_system_software.md#836-gdbm-123)
- [Gperf-3.1](8.Installing_basic_system_software.md#837-gperf-31)
- [Expat-2.5.0](8.Installing_basic_system_software.md#838-expat-250)
- [Inetutils-2.4](8.Installing_basic_system_software.md#839-inetutils-24)
- [Less-608](8.Installing_basic_system_software.md#840-less-608)
- [Perl-5.36.0](8.Installing_basic_system_software.md#841-perl-5360)
- [XML::Parser-2.46](8.Installing_basic_system_software.md#842-xmlparser-246)
- [Intltool-0.51.0](8.Installing_basic_system_software.md#843-intltool-0510)
- [Autoconf-2.71](8.Installing_basic_system_software.md#844-autoconf-271)
- [Automake-1.16.5](8.Installing_basic_system_software.md#845-automake-1165)
- [OpenSSL-3.0.8](8.Installing_basic_system_software.md#846-openssl-308)
- [Kmod-30](8.Installing_basic_system_software.md#847-kmod-30)
- [Libelf from Elfutils-0.188](8.Installing_basic_system_software.md#848-libelf-from-elfutils-0188)
- [Libffi-3.4.4](8.Installing_basic_system_software.md#849-libffi-344)
- [Python-3.11.2](8.Installing_basic_system_software.md#850-python-3112)
- [Wheel-0.38.4](8.Installing_basic_system_software.md#851-wheel-0384)
- [Ninja-1.11.1](8.Installing_basic_system_software.md#852-ninja-1111)
- [Meson-1.0.0](8.Installing_basic_system_software.md#853-meson-100)
- [Coreutils-9.1](8.Installing_basic_system_software.md#854-coreutils-91)
- [Check-0.15.2](8.Installing_basic_system_software.md#855-check-0152)
- [Diffutils-3.9](8.Installing_basic_system_software.md#856-diffutils-39)
- [Gawk-5.2.1](8.Installing_basic_system_software.md#857-gawk-521)
- [Findutils-4.9.0](8.Installing_basic_system_software.md#858-findutils-490)
- [Groff-1.22.4](8.Installing_basic_system_software.md#859-groff-1224)
- [GRUB-2.06](8.Installing_basic_system_software.md#860-grub-206)
- [Gzip-1.12](8.Installing_basic_system_software.md#861-gzip-112)
- [IPRoute2-6.1.0](8.Installing_basic_system_software.md#862-iproute2-610)
- [Kbd-2.5.1](8.Installing_basic_system_software.md#863-kbd-251)
- [Libpipeline-1.5.7](8.Installing_basic_system_software.md#864-libpipeline-157)
- [Make-4.4](8.Installing_basic_system_software.md#865-make-44)
- [Patch-2.7.6](8.Installing_basic_system_software.md#866-patch-276)
- [Tar-1.34](8.Installing_basic_system_software.md#867-tar-134)
- [Texinfo-7.0.2](8.Installing_basic_system_software.md#868-texinfo-702)
- [Vim-9.0.1273](8.Installing_basic_system_software.md#869-vim-901273)
- [Eudev-3.2.11](8.Installing_basic_system_software.md#870-eudev-3211)
- [Man-DB-2.11.2](8.Installing_basic_system_software.md#871-man-db-2112)
- [Procps-ng-4.0.2](8.Installing_basic_system_software.md#872-procps-ng-402)
- [Util-linux-2.38.1](8.Installing_basic_system_software.md#873-util-linux-2381)
- [E2fsprogs-1.47.0](8.Installing_basic_system_software.md#874-e2fsprogs-1470)
- [Sysklogd-1.5.1](8.Installing_basic_system_software.md#875-sysklogd-151)
- [Sysvinit-3.06](8.Installing_basic_system_software.md#876-sysvinit-306)
- [About Debugging Symbols](8.Installing_basic_system_software.md#877-about-debugging-symbols)
- [Stripping](8.Installing_basic_system_software.md#878-stripping)
- [Cleaning Up](8.Installing_basic_system_software.md#879-cleaning-up)

### 9. System Configuration

- [Introduction](9.System_configuration.md#91-introduction)
- [LFS-Bootscripts-20230101](9.System_configuration.md#92-lfs-bootscripts-20230101)
- [Overview of Device and Module Handling](9.System_configuration.md#93-overview-of-device-and-module-handling)
- [Managing Devices](9.System_configuration.md#94-managing-devices)
- [General Network Configuration](9.System_configuration.md#95-general-network-configuration)
- [System V Bootscript Usage and Configuration](9.System_configuration.md#96-system-v-bootscript-usage-and-configuration)
- [The Bash Shell Startup Files](9.System_configuration.md#97-the-bash-shell-startup-files)
- [Creating the /etc/inputrc File](9.System_configuration.md#98-creating-the-etcinputrc-file)
- [Creating the /etc/shells File](9.System_configuration.md#99-creating-the-etcshells-file)

### 10. Making the LFS System Bootable

- [Introduction](10.Making_the_lfs_system_bootable.md#101-introduction)
- [Creating the /etc/fstab File](10.Making_the_lfs_system_bootable.md#102-creating-the-etcfstab-file)
- [Linux-6.1.11](10.Making_the_lfs_system_bootable.md#103-linux-6111)
- [Using GRUB to Set Up the Boot Process](10.Making_the_lfs_system_bootable.md#104-using-grub-to-set-up-the-boot-process)

### 11. The End

- [The End](11.The_end.md#111-the-end)
- [Get Counted](11.The_end.md#112-get-counted)
- [Rebooting the System](11.The_end.md#113-rebooting-the-system)
- [Additional Resources](11.The_end.md#114-additional-resources)
- [Getting Started After LFS](11.The_end.md#115-getting-started-after-lfs)

## V. Appendices

- [A. Acronyms and Terms](A.Acronyms_and_terms.md)
- [B. Acknowledgments](B.Acknowledgments.md)
- [C. Dependencies](C.Dependencies.md)
- [D. Boot and sysconfig scripts version-20230101](D.Boot_and_sysconfig_scripts_version-20230101.md)
    - [/etc/rc.d/init.d/rc](D.Boot_and_sysconfig_scripts_version-20230101.md#d1-etcrcdinitdrc)
    - [/lib/lsb/init-functions](D.Boot_and_sysconfig_scripts_version-20230101.md#d2-liblsbinit-functions)
    - [/etc/rc.d/init.d/mountvirtfs](D.Boot_and_sysconfig_scripts_version-20230101.md#d3-etcrcdinitdmountvirtfs)
    - [/etc/rc.d/init.d/modules](D.Boot_and_sysconfig_scripts_version-20230101.md#d4-etcrcdinitdmodules)
    - [/etc/rc.d/init.d/udev](D.Boot_and_sysconfig_scripts_version-20230101.md#d5-etcrcdinitdudev)
    - [/etc/rc.d/init.d/swap](D.Boot_and_sysconfig_scripts_version-20230101.md#d6-etcrcdinitdswap)
    - [/etc/rc.d/init.d/setclock](D.Boot_and_sysconfig_scripts_version-20230101.md#d7-etcrcdinitdsetclock)
    - [/etc/rc.d/init.d/checkfs](D.Boot_and_sysconfig_scripts_version-20230101.md#d8-etcrcdinitdcheckfs)
    - [/etc/rc.d/init.d/mountfs](D.Boot_and_sysconfig_scripts_version-20230101.md#d9-etcrcdinitdmountfs)
    - [/etc/rc.d/init.d/udev_retry](D.Boot_and_sysconfig_scripts_version-20230101.md#d10-etcrcdinitdudev_retry)
    - [/etc/rc.d/init.d/cleanfs](D.Boot_and_sysconfig_scripts_version-20230101.md#d11-etcrcdinitdcleanfs)
    - [/etc/rc.d/init.d/console](D.Boot_and_sysconfig_scripts_version-20230101.md#d12-etcrcdinitdconsole)
    - [/etc/rc.d/init.d/localnet](D.Boot_and_sysconfig_scripts_version-20230101.md#d13-etcrcdinitdlocalnet)
    - [/etc/rc.d/init.d/sysctl](D.Boot_and_sysconfig_scripts_version-20230101.md#d14-etcrcdinitdsysctl)
    - [/etc/rc.d/init.d/sysklogd](D.Boot_and_sysconfig_scripts_version-20230101.md#d15-etcrcdinitdsysklogd)
    - [/etc/rc.d/init.d/network](D.Boot_and_sysconfig_scripts_version-20230101.md#d16-etcrcdinitdnetwork)
    - [/etc/rc.d/init.d/sendsignals](D.Boot_and_sysconfig_scripts_version-20230101.md#d17-etcrcdinitdsendsignals)
    - [/etc/rc.d/init.d/reboot](D.Boot_and_sysconfig_scripts_version-20230101.md#d18-etcrcdinitdreboot)
    - [/etc/rc.d/init.d/halt](D.Boot_and_sysconfig_scripts_version-20230101.md#d19-etcrcdinitdhalt)
    - [/etc/rc.d/init.d/template](D.Boot_and_sysconfig_scripts_version-20230101.md#d20-etcrcdinitdtemplate)
    - [/etc/sysconfig/modules](D.Boot_and_sysconfig_scripts_version-20230101.md#d21-etcsysconfigmodules)
    - [/etc/sysconfig/createfiles](D.Boot_and_sysconfig_scripts_version-20230101.md#d22-etcsysconfigcreatefiles)
    - [/etc/sysconfig/udev-retry](D.Boot_and_sysconfig_scripts_version-20230101.md#d23-etcsysconfigudev-retry)
    - [/sbin/ifup](D.Boot_and_sysconfig_scripts_version-20230101.md#d24-sbinifup)
    - [/sbin/ifdown](D.Boot_and_sysconfig_scripts_version-20230101.md#d25-sbinifdown)
    - [/lib/services/ipv4-static](D.Boot_and_sysconfig_scripts_version-20230101.md#d26-libservicesipv4-static)
    - [/lib/services/ipv4-static-route](D.Boot_and_sysconfig_scripts_version-20230101.md#d27-libservicesipv4-static-route)
- [E. Udev configuration rules](E.Udev_configuration_rules.md)
    - [55-lfs.rules](E.Udev_configuration_rules.md#e1-55-lfsrules)
- [F. LFS Licenses](F.LFS_licenses.md)
    - [Creative Commons License](F.LFS_licenses.md#f1-creative-commons-license)
    - [The MIT License](F.LFS_licenses.md#f2-the-mit-license)

**[Index](index.md)**

[User guide for virtualbox](User_guide_for_vbox.md)

**Copyright © 1999-2023 Gerard Beekmans**
