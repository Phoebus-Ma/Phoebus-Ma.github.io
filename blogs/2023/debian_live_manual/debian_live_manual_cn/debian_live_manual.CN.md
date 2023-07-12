
# Debian Live Manual

## About
--------
### About this manual

- [1.About this manual](1.About_manual.CN.md#1-about-this-manual)
    - [1.1 For the impatient](1.About_manual.CN.md#11-for-the-impatient)
    - [1.2 Terms](1.About_manual.CN.md#12-terms)
    - [1.3 Authors](1.About_manual.CN.md#13-authors)
    - [1.4 Contributing to this document](1.About_manual.CN.md#14-contributing-to-this-document)
        - [1.4.1 Applying changes](1.About_manual.CN.md#141-applying-changes)
        - [1.4.2 Translation](1.About_manual.CN.md#142-translation)

### About the Debian Live Project

- [2.About the Debian Live Project](2.About_the_Debian_Live_Project.CN.md#2-about-the-debian-live-project)
    - [2.1 Motivation](2.About_the_Debian_Live_Project.CN.md#21-motivation)
        - [2.1.1 What is wrong with current live systems](2.About_the_Debian_Live_Project.CN.md#211-what-is-wrong-with-current-live-systems)
        - [2.1.2 Why create our own live system?](2.About_the_Debian_Live_Project.CN.md#212-why-create-our-own-live-system)
    - [2.2 Philosophy](2.About_the_Debian_Live_Project.CN.md#22-philosophy)
        - [2.2.1 Only unchanged packages from Debian "main"](2.About_the_Debian_Live_Project.CN.md#221-only-unchanged-packages-from-debian-main)
        - [2.2.2 No package configuration of the live system](2.About_the_Debian_Live_Project.CN.md#222-no-package-configuration-of-the-live-system)
    - [2.3 Contact](2.About_the_Debian_Live_Project.CN.md#23-contact)

## User
--------
### Installation

- [3.Installation](3.Installation.CN.md#3-installation)
    - [3.1 Requirements](3.Installation.CN.md#31-requirements)
    - [3.2 Installing live-build](3.Installation.CN.md#32-installing-live-build)
        - [3.2.1 From the Debian repository](3.Installation.CN.md#321-from-the-debian-repository)
        - [3.2.2 From source](3.Installation.CN.md#322-from-source)
    - [3.3 Installing live-boot and live-config](3.Installation.CN.md#33-installing-live-boot-and-live-config)
        - [3.3.1 From the Debian repository](3.Installation.CN.md#331-from-the-debian-repository)
        - [3.3.2 From source](3.Installation.CN.md#332-from-source)

### The basics

- [4.The basics](4.The_basics.CN.md#4-the-basics)
    - [4.1 What is a live system?](4.The_basics.CN.md#41-what-is-a-live-system)
    - [4.2 First steps: building an ISO hybrid image](4.The_basics.CN.md#42-first-steps-building-an-iso-hybrid-image)
    - [4.3 Using an ISO hybrid live image](4.The_basics.CN.md#43-using-an-iso-hybrid-live-image)
        - [4.3.1 Burning an ISO image to a physical medium](4.The_basics.CN.md#431-burning-an-iso-image-to-a-physical-medium)
        - [4.3.2 Copying an ISO hybrid image to a USB stick](4.The_basics.CN.md#432-copying-an-iso-hybrid-image-to-a-usb-stick)
        - [4.3.3 Using the space left on a USB stick](4.The_basics.CN.md#433-using-the-space-left-on-a-usb-stick)
        - [4.3.4 Booting the live medium](4.The_basics.CN.md#434-booting-the-live-medium)
    - [4.4 Using a virtual machine for testing](4.The_basics.CN.md#44-using-a-virtual-machine-for-testing)
        - [4.4.1 Testing an ISO image with QEMU](4.The_basics.CN.md#441-testing-an-iso-image-with-qemu)
        - [4.4.2 Testing an ISO image with VirtualBox](4.The_basics.CN.md#442-testing-an-iso-image-with-virtualbox)
    - [4.5 Building and using an HDD image](4.The_basics.CN.md#45-building-and-using-an-hdd-image)
    - [4.6 Building a netboot image](4.The_basics.CN.md#46-building-a-netboot-image)
        - [4.6.1 DHCP server](4.The_basics.CN.md#461-dhcp-server)
        - [4.6.2 TFTP server](4.The_basics.CN.md#462-tftp-server)
        - [4.6.3 NFS server](4.The_basics.CN.md#463-nfs-server)
        - [4.6.4 Netboot testing HowTo](4.The_basics.CN.md#464-netboot-testing-howto)
        - [4.6.5 Qemu](4.The_basics.CN.md#465-qemu)
    - [4.7 Webbooting](4.The_basics.CN.md#47-webbooting)
        - [4.7.1 Getting the webboot files](4.The_basics.CN.md#47-webbooting)
        - [4.7.2 Booting webboot images](4.The_basics.CN.md#472-booting-webboot-images)

### Overview of tools

- [5.Overview of tools](5.Overview_of_tools.CN.md#5-overview-of-tools)
    - [5.1 The live-build package](5.Overview_of_tools.CN.md#51-the-live-build-package)
        - [5.1.1 The lb config command](5.Overview_of_tools.CN.md#511-the-lb-config-command)
        - [5.1.2 The lb build command](5.Overview_of_tools.CN.md#512-the-lb-build-command)
        - [5.1.3 The lb clean command](5.Overview_of_tools.CN.md#513-the-lb-clean-command)
    - [5.2 The live-boot package](5.Overview_of_tools.CN.md#52-the-live-boot-package)
    - [5.3 The live-config package](5.Overview_of_tools.CN.md#53-the-live-config-package)

### Managing a configuration

- [6.Managing a configuration](6.Managing_a_configuration.CN.md#6-managing-a-configuration)
    - [6.1 Dealing with configuration changes](6.Managing_a_configuration.CN.md#61-dealing-with-configuration-changes)
        - [6.1.1 Why use auto scripts? What do they do?](6.Managing_a_configuration.CN.md#611-why-use-auto-scripts-what-do-they-do)
        - [6.1.2 Use example auto scripts](6.Managing_a_configuration.CN.md#612-use-example-auto-scripts)
    - [6.2 Clone a configuration published via Git](6.Managing_a_configuration.CN.md#62-clone-a-configuration-published-via-git)

### Customizing contents

- [7.Customization overview](7.Customization_overview.CN.md#7-customization-overview)
    - [7.1 Build time vs. boot time configuration](7.Customization_overview.CN.md#71-build-time-vs-boot-time-configuration)
    - [7.2 Stages of the build](7.Customization_overview.CN.md#72-stages-of-the-build)
    - [7.3 Supplement lb config with files](7.Customization_overview.CN.md#73-supplement-lb-config-with-files)
    - [7.4 Customization tasks](7.Customization_overview.CN.md#74-customization-tasks)

### Customizing package installation

- [8.Customizing package installation](8.Customizing_package_installation.CN.md#8-customizing-package-installation)
    - [8.1 Package sources](8.Customizing_package_installation.CN.md#81-package-sources)
        - [8.1.1 Distribution, archive areas and mode](8.Customizing_package_installation.CN.md#811-distribution-archive-areas-and-mode)
        - [8.1.2 Distribution mirrors](8.Customizing_package_installation.CN.md#812-distribution-mirrors)
        - [8.1.3 Distribution mirrors used at build time](8.Customizing_package_installation.CN.md#813-distribution-mirrors-used-at-build-time)
        - [8.1.4 Distribution mirrors used at run time](8.Customizing_package_installation.CN.md#814-distribution-mirrors-used-at-run-time)
        - [8.1.5 Additional repositories](8.Customizing_package_installation.CN.md#815-additional-repositories)
    - [8.2 Choosing packages to install](8.Customizing_package_installation.CN.md#82-choosing-packages-to-install)
        - [8.2.1 Package lists](8.Customizing_package_installation.CN.md#821-package-lists)
        - [8.2.2 Using metapackages](8.Customizing_package_installation.CN.md#822-using-metapackages)
        - [8.2.3 Local package lists](8.Customizing_package_installation.CN.md#823-local-package-lists)
        - [8.2.4 Local binary package lists](8.Customizing_package_installation.CN.md#824-local-binary-package-lists)
        - [8.2.5 Generated package lists](8.Customizing_package_installation.CN.md#825-generated-package-lists)
        - [8.2.6 Using conditionals inside package lists](8.Customizing_package_installation.CN.md#826-using-conditionals-inside-package-lists)
        - [8.2.7 Removing packages at install time](8.Customizing_package_installation.CN.md#827-removing-packages-at-install-time)
        - [8.2.8 Desktop and language tasks](8.Customizing_package_installation.CN.md#828-desktop-and-language-tasks)
        - [8.2.9 Kernel flavour and version](8.Customizing_package_installation.CN.md#829-kernel-flavour-and-version)
        - [8.2.10 Custom kernels](8.Customizing_package_installation.CN.md#8210-custom-kernels)
    - [8.3 Installing modified or third-party packages](8.Customizing_package_installation.CN.md#83-installing-modified-or-third-party-packages)
        - [8.3.1 Using packages.chroot to install custom packages](8.Customizing_package_installation.CN.md#831-using-packageschroot-to-install-custom-packages)
        - [8.3.2 Using an APT repository to install custom packages](8.Customizing_package_installation.CN.md#832-using-an-apt-repository-to-install-custom-packages)
        - [8.3.3 Custom packages and APT](8.Customizing_package_installation.CN.md#833-custom-packages-and-apt)
    - [8.4 Configuring APT at build time](8.Customizing_package_installation.CN.md#84-configuring-apt-at-build-time)
        - [8.4.1 Choosing apt or aptitude](8.Customizing_package_installation.CN.md#841-choosing-apt-or-aptitude)
        - [8.4.2 Using a proxy with APT](8.Customizing_package_installation.CN.md#842-using-a-proxy-with-apt)
        - [8.4.3 Tweaking APT to save space](8.Customizing_package_installation.CN.md#843-tweaking-apt-to-save-space)
        - [8.4.4 Passing options to apt or aptitude](8.Customizing_package_installation.CN.md#844-passing-options-to-apt-or-aptitude)
        - [8.4.5 APT pinning](8.Customizing_package_installation.CN.md#845-apt-pinning)

### Customizing contents

- [9.Customizing contents](9.Customizing_contents.CN.md#9-customizing-contents)
    - [9.1 Includes](9.Customizing_contents.CN.md#91-includes)
        - [9.1.1 Live/chroot local includes](9.Customizing_contents.CN.md#911-livechroot-local-includes)
        - [9.1.2 Binary local includes](9.Customizing_contents.CN.md#912-binary-local-includes)
    - [9.2 Hooks](9.Customizing_contents.CN.md#92-hooks)
        - [9.2.1 Chroot local hooks](9.Customizing_contents.CN.md#921-chroot-local-hooks)
        - [9.2.2 Binary local hooks](9.Customizing_contents.CN.md#922-binary-local-hooks)
        - [9.2.3 Boot-time hooks](9.Customizing_contents.CN.md#923-boot-time-hooks)
    - [9.3 Preseeding Debconf questions](9.Customizing_contents.CN.md#93-preseeding-debconf-questions)

### Customizing run time behaviours

- [10.Customizing run time behaviours](10.Customizing_run_time_behaviours.CN.md#10-customizing-run-time-behaviours)
    - [10.1 Customizing the live user](10.Customizing_run_time_behaviours.CN.md#101-customizing-the-live-user)
    - [10.2 Customizing locale and language](10.Customizing_run_time_behaviours.CN.md#102-customizing-locale-and-language)
    - [10.3 Persistence](10.Customizing_run_time_behaviours.CN.md#103-persistence)
        - [10.3.1 The persistence.conf file](10.Customizing_run_time_behaviours.CN.md#1031-the-persistenceconf-file)
        - [10.3.2 Using more than one persistence store](10.Customizing_run_time_behaviours.CN.md#1032-using-more-than-one-persistence-store)
        - [10.3.3 Using persistence with encryption](10.Customizing_run_time_behaviours.CN.md#1033-using-persistence-with-encryption)

### Customizing the binary image

- [11.Customizing the binary image](11.Customizing_the_binary_image.CN.md#11-customizing-the-binary-image)
    - [11.1 Bootloaders](11.Customizing_the_binary_image.CN.md#111-bootloaders)
    - [11.2 ISO metadata](11.Customizing_the_binary_image.CN.md#112-iso-metadata)

### Customizing Debian Installer

- [12.Customizing Debian Installer](12.Customizing_Debian_Installer.CN.md#12-customizing-debian-installer)
    - [12.1 Types of Debian Installer](12.Customizing_Debian_Installer.CN.md#121-types-of-debian-installer)
    - [12.2 Customizing Debian Installer by preseeding](12.Customizing_Debian_Installer.CN.md#122-customizing-debian-installer-by-preseeding)
    - [12.3 Customizing Debian Installer content](12.Customizing_Debian_Installer.CN.md#123-customizing-debian-installer-content)

## Project
--------
### Contributing to the project

- [13.Contributing to the project](13.Contributing_to_the_project.CN.md#13-contributing-to-the-project)
    - [13.1 Translation of man pages](13.Contributing_to_the_project.CN.md#131-translation-of-man-pages)

### Reporting bugs

- [14.Reporting bugs](14.Reporting_bugs.CN.md#14-reporting-bugs)
    - [14.1 Known issues](14.Reporting_bugs.CN.md#141-known-issues)
    - [14.2 Do the research](14.Reporting_bugs.CN.md#142-do-the-research)
    - [14.3 Rebuild from scratch](14.Reporting_bugs.CN.md#143-rebuild-from-scratch)
    - [14.4 Use up-to-date packages](14.Reporting_bugs.CN.md#144-use-up-to-date-packages)
    - [14.5 Collect information](14.Reporting_bugs.CN.md#145-collect-information)
    - [14.6 Isolate the failing case if possible](14.Reporting_bugs.CN.md#146-isolate-the-failing-case-if-possible)
    - [14.7 Use the correct package to report the bug against](14.Reporting_bugs.CN.md#147-use-the-correct-package-to-report-the-bug-against)
        - [14.7.1 At build time while bootstrapping](14.Reporting_bugs.CN.md#1471-at-build-time-while-bootstrapping)
        - [14.7.2 At build time while installing packages](14.Reporting_bugs.CN.md#1472-at-build-time-while-installing-packages)
        - [14.7.3 At boot time](14.Reporting_bugs.CN.md#1473-at-boot-time)
        - [14.7.4 At run time](14.Reporting_bugs.CN.md#1474-at-run-time)
    - [14.8 Where to report bugs](14.Reporting_bugs.CN.md#148-where-to-report-bugs)

### Coding Style

- [15.Coding Style](15.Coding_Style.CN.md#15-coding-style)
    - [15.1 Compatibility](15.Coding_Style.CN.md#151-compatibility)
    - [15.2 Indenting](15.Coding_Style.CN.md#152-indenting)
    - [15.3 Wrapping](15.Coding_Style.CN.md#153-wrapping)
    - [15.4 Variables](15.Coding_Style.CN.md#154-variables)
    - [15.5 Miscellaneous](15.Coding_Style.CN.md#155-miscellaneous)

## Examples
--------
### Examples

- [16.Examples](16.Examples.CN.md#16-examples)
    - [16.1 Using the examples](16.Examples.CN.md#161-using-the-examples)
    - [16.2 Tutorial 1: A default image](16.Examples.CN.md#162-tutorial-1-a-default-image)
    - [16.3 Tutorial 2: A web browser utility](16.Examples.CN.md#163-tutorial-2-a-web-browser-utility)
    - [16.4 Tutorial 3: A personalized image](16.Examples.CN.md#164-tutorial-3-a-personalized-image)
        - [16.4.1 First revision](16.Examples.CN.md#1641-first-revision)
        - [16.4.2 Second revision](16.Examples.CN.md#1642-second-revision)
    - [16.5 A VNC Kiosk Client](16.Examples.CN.md#165-a-vnc-kiosk-client)
    - [16.6 A minimal image for a 256MB USB key](16.Examples.CN.md#166-a-minimal-image-for-a-256mb-usb-key)
    - [16.7 A localized GNOME desktop and installer](16.Examples.CN.md#167-a-localized-gnome-desktop-and-installer)

## Appendix
--------
### Style guide

- [17.Style guide](17.Style_guide.CN.md#17-style-guide)
    - [17.1 Guidelines for authors](17.Style_guide.CN.md#171-guidelines-for-authors)
        - [17.1.1 Linguistic features](17.Style_guide.CN.md#1711-linguistic-features)
        - [17.1.2 Procedures](17.Style_guide.CN.md#1712-procedures)
    - [17.2 Guidelines for translators](17.Style_guide.CN.md#172-guidelines-for-translators)
        - [17.2.1 Translation hints](17.Style_guide.CN.md#1721-translation-hints)


@Update for 2023.
