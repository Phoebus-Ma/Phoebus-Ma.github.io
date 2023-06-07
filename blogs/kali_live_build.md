
## Creating A Custom Kali ISO
--------

**Platform: Kali-linux-amd64-desktop.**

**Create kali iso use kali live build.**


### 1.An Introduction to Building Your Own Kali ISO
--------

Building a customized Kali Linux image is not as complex as you may be thinking. It is easy, fun,
and rewarding! Kali Linux traditionally, has been a Live Image, but since Kali 2020.1 an Installer Image was introduced.
Both these images have different functions, and are also built in different ways.

 - Live Image - allows you to try Kali, without altering the system (making it great for USB).
 It is created using [live-build](https://live-team.pages.debian.net/live-manual/html/live-manual/index.en.html)
 - Installer Image - allows for you to customize Kali by picking packaging during installation, 
 such as picking the desktop environment as well as what metapackages get installed. This image is powered by
 [simple-cdd](https://wiki.debian.org/Simple-CDD) (which uses **debian-cd** to make **Debian-Installer**).

You can configure virtually any aspect of your Kali ISO build, such as adding packages from outside of Kali network repositories,
unattended installations to changing the default wallpaper. Our build-scripts provides a framework that uses a configuration set to
automate and customize all aspects of building the images. The Kali Linux development team use the same build-scripts to produce the official Kali ISO releases.


### 2.Kali Environment
--------

#### 2.1 Getting Ready - Setting Up The build-script Kali System

We first need to prepare the Kali ISO build environment by installing and setting up
the required packages with the following commands:

```bash
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y git live-build simple-cdd cdebootstrap curl
kali@kali:~$
kali@kali:~$ git clone https://gitlab.com/kalilinux/build-scripts/live-build-config.git
```

#### 2.2 Building an Updated Live Image

Now you can simply build an updated Kali ISO (with our default configuration) by entering
the live-build-config/ directory and running our build.sh wrapper script, as follows:

```bash
kali@kali:~$ cd live-build-config/
kali@kali:~/live-build-config$ ./build.sh --verbose
[...]
***
GENERATED KALI IMAGE: ./images/kali-linux-rolling-live-amd64.iso
***
```

The **build.sh** script will take a while to complete, as it downloads all of the required
packages needed to create your ISO. Good time for a drink.

#### 2.3 Building an Updated Installer Image

By default, it will generate a Live Image. If you want an Installer Image, add --installer:

```bash
kali@kali:~/live-build-config$ ./build.sh --verbose --installer
```

We are using the **--verbose** to output more on the screen rather than it being captured in just the **build.log** output.
If you want even more output, you can use **--debug** instead, which will then give even more information.


### 3.Non-Kali Debian-Based Environment
--------

#### 3.1 Setting Up The build-script Non-Kali Debian-Based System

You can build an Kali ISO on a Debian-based systems other than Kali Linux. The instructions below have been tested to work with both Debian and Ubuntu.

First, we prepare the system by ensuring it is fully updated, then proceed to download the Kali archive keyring and packages:

```bash
$ sudo apt update
$ sudo apt full-upgrade -y
$
$ wget https://http.kali.org/pool/main/k/kali-archive-keyring/kali-archive-keyring_2022.1_all.deb
$ wget https://http.kali.org/kali/pool/main/l/live-build/live-build_20230131+kali6_all.deb
```

*Note: You must check that **kali-archive-keyring_20YY.X_all.deb** & **live-build_20YYMMDD+kaliX_all.deb** are the latest files.*

With that completed, we install some additional dependencies and the previously downloaded files:

```bash
$ sudo apt install -y git live-build simple-cdd cdebootstrap curl
$
$ sudo dpkg -i kali-archive-keyring_2022.1_all.deb
$ sudo dpkg -i live-build_20230131+kali6_all.deb
```

With the environment all prepared, we start the process by setting up the build-script profile and clone out the build config:

```bash
$ cd /usr/share/debootstrap/scripts/
$ (echo "default_mirror http://http.kali.org/kali"; sed -e "s/debian-archive-keyring.gpg/kali-archive-keyring.gpg/g" sid) > /tmp/kali
$ sudo mv /tmp/kali .
$ sudo ln -s kali kali-rolling
$
$ cd ~/
$ git clone https://gitlab.com/kalilinux/build-scripts/live-build-config.git
$
$ cd live-build-config/
```

At this point, depending on the host OS and its version, we may need to edit build.sh to bypass a version check for debootstrap. We do this by commenting out the exit 1 below:

```bash
$ cat build.sh
[...]
		ver_debootstrap=$(dpkg-query -f '${Version}' -W debootstrap)
		if dpkg --compare-versions "$ver_debootstrap" lt "1.0.97"; then
			echo "ERROR: You need debootstrap (>= 1.0.97), you have $ver_debootstrap" >&2
			exit 1
		fi
[...]
```

With the above change made, **build.sh** should look similar:

```bash
$ cat build.sh
[...]
		ver_debootstrap=$(dpkg-query -f '${Version}' -W debootstrap)
		if dpkg --compare-versions "$ver_debootstrap" lt "1.0.97"; then
			echo "ERROR: You need debootstrap (>= 1.0.97), you have $ver_debootstrap" >&2
			#exit 1
		fi
[...]
```

At this point, we can build our ISO as normal:

```bash
$ ./build.sh --verbose
```


### 4.Re-building the Latest Kali Image
--------

By using the kali-last-snapshot branch, you are able to re-create the latest distributed image. We can do this by using **--distribution kali-last-snapshot**:

```bash
kali@kali:~$ time ./build.sh \
  --verbose \
  --installer \
  --distribution kali-last-snapshot \
  --version 2023.2 \
  --subdir kali-2023.2
[...]
***
GENERATED KALI IMAGE: ./images/kali-2023.2/kali-linux-2023.2-installer-amd64.iso
***
```


### 5.Configuring The Kali ISO Build (Optional)
--------

If you want to customize your Kali Linux ISO, this section will explain some of the details. Through the **kali-config/** directory,
there are a wide range of customization options, which are well-documented for [live-build](https://live-team.pages.debian.net/live-manual/html/live-manual/customization-overview.en.html)
page. Simple-CD is a little more limited with options. For the impatient, here are some of the highlights.

#### 5.1 Building Kali Live With Different Desktop Environments

Since Kali 2.0, we now support built in configurations for various desktop environments, including Xfce (default), Gnome, KDE, E17, I3WM, LXDE, MATE.
To build any of these, you would use syntax similar to the following:

```bash
kali@kali:~/live-build-config$ # These are the different Desktop Environment build options:
kali@kali:~/live-build-config$ #./build.sh --variant {xfce,gnome,kde,mate,e17,lxde,i3wm} --verbose
kali@kali:~/live-build-config$
kali@kali:~/live-build-config$ # To build a Gnome ISO:
kali@kali:~/live-build-config$ ./build.sh --variant gnome --verbose
kali@kali:~/live-build-config$
kali@kali:~/live-build-config$ # To build a KDE ISO:
kali@kali:~/live-build-config$ ./build.sh --variant kde --verbose
```

This is not required with the installer images, as it includes Xfce, Gnome and KDE by default.
You can add others by including their packages as explained in the section below.

#### 5.2 Controlling The Packages Included In Your Build

The list of packages included in your build will be present in the the respective kali-config/ directory. For example, if you’re wanting to edit:

 - The default Installer ISO, you would use the following package lists file - kali-config/installer-default/packages
 - The default Live ISO, you would use the following package lists file - kali-config/variant-default/package-lists/kali.list.chroot
 - A non-default Live ISO desktop environment, such as Gnome - kali-config/variant-gnome/package-lists/kali.list.chroot (You can replace Gnome with any supported desktop environments)
 - By default, these lists will includes the kali-linux-default metapackage, as well as some others. These can be commented out and replaced with a manual list of packages to include in the ISO for greater granularity.

#### 5.3 Overlaying Files In Your Build

With Live images, you have the option to include additional files or scripts in your build by overlaying them on the existing file-system, inside the includes.{chroot,binary,installer} directories, respectively.

For example, if we wanted to include our own custom script into the /root/ directory of the ISO (this would correspond to the chroot stage), then we would drop this script file in the kali-config/common/includes.chroot/ directory before building the ISO.

For more information see the [live-build documentation](https://live-team.pages.debian.net/live-manual/html/live-manual/customizing-contents.en.html).

#### 5.4 Build Hooks, Binary and Chroot

For live images, live-build supports hooks allows us to “hook scripts” in various stages of the Kali ISO live image. For more detailed information about hooks and how to use them, refer to the
[live-build manual](https://live-team.pages.debian.net/live-manual/html/live-manual/customizing-contents.en.html#507).

As an example, we recommend you check out the existing hooks in **kali-config/common/hooks/**.


### 6.Building a Kali Linux ISO for Different Architectures (Optional)
--------

By default, the build-script will generate the Kali image based on the architectures of the current operating system. If you wish to alter this:

 - x64: **./build.sh --verbose --arch amd64**
 - x86: **./build.sh --verbose --arch i386**

Building a Kali Linux ISO for Older i386 Architectures
The Kali Linux i386 ISO has PAE enabled. If you require a default kernel for older hardware with PAE disabled,
you will need to rebuild a Kali Linux ISO. The rebuilding process is much the same as described above,
except that the 686-pae parameter needs to be changed to 686 in **auto/config** before building:

```bash
kali@kali:~/live-build-config$ sed -i 's/686-pae/686/g' auto/config
kali@kali:~/live-build-config$
kali@kali:~/live-build-config$ ./build.sh --verbose --arch i386
```


### 7.Using A Custom Network Mirror For Building (Optional)
--------

If you build multiple images, you will find you are often waiting on build.sh to finish. There are a few ways to speed up the build process, such as:

 - Building Installer images as they often build quicker than Live images
 - Have less packages included (such as switching **kali-linux-default** to **kali-linux-top10**)
 - Improve access to packages

You often find that you are waiting on packages to be pulled down. You can either setup a local proxy on the same machine (such as apt-cacher or apt-cacher-ng).
Alternatively, you can setup a [local network mirror](https://www.kali.org/docs/community/setting-up-a-kali-linux-mirror/).

We can instruct the build-script to use a different mirror, by doing the following (assuming our network mirror is located at **http://192.168.0.101/kali**):

```bash
kali@kali:~/live-build-config$ echo "http://192.168.0.101/kali/" > .mirror
kali@kali:~/live-build-config$ ./build.sh --verbose
```


### 8.Help Screen
--------

You can see all the available command-line options by doing **--help**:

```bash
kali@kali:~/live-build-config$ ./build.sh --help
Usage: ./build.sh [<option>...]

  --distribution <arg>
  --proposed-updates
  --arch <arg>
  --verbose
  --debug
  --salt
  --installer
  --live
  --variant <arg>
  --version <arg>
  --subdir <arg>
  --get-image-path
  --no-clean
  --clean
  --help

More information: https://www.kali.org/docs/development/live-build-a-custom-kali-iso/
kali@bDesktop:~/live-build-config$
```


### 9.Testing Built Image
--------

After producing the issue, you can treat it like any Kali base image, so you can install it (either on bare metal or virtually), or copy to a CD/DVD/USB.

If you are wanting to quickly test the image before putting it “in production”, we can use qemu (and ovmf for UEFI). First we install the packages:

```bash
kali@kali:$ sudo apt update
kali@kali:$ sudo apt install -y qemu qemu-system-x86 ovmf
```

Next we produce a hard disk to use:

```bash
kali@kali:$ qemu-img create \
  -f qcow2 \
  /tmp/kali-test.hdd.img \
  20G
```

Afterwards, to boot from the image produced (we will be using the Live image on x64):

```bash
kali@kali:$ qemu-system-x86_64 \
  -enable-kvm \
  -drive if=virtio,aio=threads,cache=unsafe,format=qcow2,file=/tmp/kali-test.hdd.img \
  -cdrom /home/kali/live-build-config/images/kali-linux-rolling-live-amd64.iso \
  -boot once=d
```

The above will be a “BIOS” boot. For a “UEFI” boot:

```bash
kali@kali:$ qemu-system-x86_64 \
  -enable-kvm \
  -drive if=virtio,aio=threads,cache=unsafe,format=qcow2,file=/tmp/kali-test.hdd.img \
  -drive if=pflash,format=raw,readonly,file=/usr/share/OVMF/OVMF_CODE.fd \
  -drive if=pflash,format=raw,readonly,file=/usr/share/OVMF/OVMF_VARS.fd \
  -cdrom /home/kali/live-build-config/images/kali-linux-rolling-live-amd64.iso \
  -boot once=d
```

Note: We have set UEFI configuration file to be read only.


### Link
--------

[https://www.kali.org/docs/development/live-build-a-custom-kali-iso/](https://www.kali.org/docs/development/live-build-a-custom-kali-iso/)
