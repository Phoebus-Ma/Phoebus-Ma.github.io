
# AOSP for TsingHua Download and Build

# 1. Android 镜像使用帮助

**注意: 本镜像是 AOSP 镜像，Android SDK因版权原因，我们不能提供镜像服务。**

**可访问 [https://cs.android.com](https://cs.android.com/) 或 [https://github.com/aosp-mirror](https://github.com/aosp-mirror) 在线搜索及浏览 AOSP 源码。**

参考 Google 教程 <https://source.android.com/setup/build/downloading>， 将 `https://android.googlesource.com/` 全部使用 `https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/` 代替即可。

由于使用 HTTPS 协议更安全，并且更便于我们灵活处理，所以强烈推荐使用 HTTPS 协议同步 AOSP 镜像。

**由于 AOSP 镜像造成CPU/内存负载过重，我们限制了并发数量，因此建议：**

1.  sync的时候并发数不宜太高，否则会出现 503 错误，即`-j`后面的数字不能太大，建议选择4。
2.  请尽量选择流量较小时错峰同步。


# 2. 过程摘录

(参考 <https://lug.ustc.edu.cn/wiki/mirrors/help/aosp> 编写)

下载 repo 工具:

```bash
$ mkdir ~/bin
$ PATH=~/bin:$PATH
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

或者使用tuna的[git-repo镜像](https://mirrors.tuna.tsinghua.edu.cn/help/git-repo/)


## 2.1 使用每月更新的初始化包

由于首次同步需要下载约 60GB 数据，过程中任何网络故障都可能造成同步失败，我们强烈建议您使用初始化包进行初始化。

下载 <https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly/aosp-latest.tar>，下载完成后记得根据 checksum.txt 的内容校验一下。

由于所有代码都是从隐藏的 `.repo` 目录中 checkout 出来的，所以我们只保留了 `.repo` 目录，下载后解压 再 `repo sync` 一遍即可得到完整的目录。

使用方法如下:

```bash
$ curl -OC - https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly/aosp-latest.tar # 下载初始化包
$ tar xf aosp-latest.tar
$ cd AOSP   # 解压得到的 AOSP 工程目录
# 这时 ls 的话什么也看不到，因为只有一个隐藏的 .repo 目录
$ repo sync # 正常同步一遍即可得到完整目录
# 或 repo sync -l 仅checkout代码
```

此后，每次只需运行 `repo sync` 即可保持同步。 **我们强烈建议您保持每天同步，并尽量选择凌晨等低峰时间**


## 2.2 传统初始化方法

建立工作目录:

```bash
$ mkdir WORKING_DIRECTORY
$ cd WORKING_DIRECTORY
```

初始化仓库:

```bash
$ repo init -u https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/platform/manifest
```

**如果提示无法连接到 gerrit.googlesource.com，请参照[git-repo的帮助页面](https://mirrors.tuna.tsinghua.edu.cn/help/git-repo)的更新一节。**

如果需要某个特定的 Android 版本([列表](https://source.android.com/setup/start/build-numbers#source-code-tags-and-builds))：

简单列表:

| Name        | Tag                | API Level |
| ----------- | ------------------ | --------- |
| Android 14  | android-14.0.0_r33 | 34        |
| Android 13  | android-13.0.0_r78 | 33        |
| Android 12  | android-12.1.0_r11 | 31        |
| Android 11  | android-11.0.0_r46 | 30        |
| Android 10  | android-10.0.0_r41 | 29        |
| Android 9   | android-9.0.0_r46  | 28        |
| Android 8.1 | android-8.1.0_r52  | 27        |
| Android 7.1 | android-7.1.2_r28  | 25        |
| Android 6   | android-6.0.1_r81  | 23        |
| Android 5.1 | android-5.1.1_r30  | 22        |
| Android 4.4 | android-4.4.4_r1   | 19        |


```bash
$ repo init -u https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/platform/manifest -b android-12.1.0_r11
```

同步源码树（以后只需执行这条命令来同步）：

```bash
$ repo sync -j4
```


# 3. 建立次级镜像

由于 AOSP 镜像需求量巨大，且 Git 服务占资源较多，TUNA 服务器因 AOSP 产生的负载已经占主要部分。 如果你是团队用户，我们强烈建议你通过 TUNA 建立次级镜像，再分享给团队内其他用户，减轻 TUNA 服务器压力。 建立 AOSP 镜像需要占用约 850G 磁盘。

具体步骤为:

下载 `repo` 工具和建立工作目录（略）

初始化:

```bash
$ repo init -u https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/mirror/manifest --mirror
```    

最后同步源码树:

```bash
$ repo sync -j4
```

同步完成后，运行 `git daemon --verbose --export-all --base-path=WORKING_DIR WORKING_DIR` (`WORKING_DIR`为代码树所在目录) 。

此后，其他用户使用 `git://ip.to.mirror/` 作为镜像即可。


# 4. 替换已有的 AOSP 源代码的 remote

如果你之前已经通过某种途径获得了 AOSP 的源码(或者你只是 init 这一步完成后)， 你希望以后通过 TUNA 同步 AOSP 部分的代码，只需要修改 `.repo/manifests.git/config`，将

```bash
url = https://android.googlesource.com/platform/manifest
```

更改为

```bash
url = https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/platform/manifest
```

或者可以不修改文件，而执行

```bash
$ git config --global url.https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/.insteadof https://android.googlesource.com
```


# 5. FAQ

1.  镜像的是什么？

    - AOSP 的 git 仓库

2.  为何不能通过浏览器访问？

    - 暂时没有 gitweb, 而且反正是 git bare 仓库，没有可以直接看到的内容。

    - 建议访问 [https://cs.android.com](https://cs.android.com/) 或 [https://github.com/aosp-mirror](https://github.com/aosp-mirror) 在线搜索及浏览 AOSP 源码。

3.  出现 `curl: (22) The requested URL returned error: 404 Not Found Server does not provide clone.bundle; ignoring.` 怎么办？

    - 无视即可。

    - 参见：[https://github.com/tuna/issues/issues/936](https://github.com/tuna/issues/issues/936)


# 6. 安装开发环境

Ubuntu 22.04:

```bash
$ sudo apt update
$ sudo apt install openjdk-11-jdk
```

```bash
$ sudo apt install build-essential flex bison gawk bc git ssh \
        g++-multilib gcc-multilib gperf libxml2-utils zlib1g-dev:i386 \
        zip unzip liblz4-tool libncurses-dev libssl-dev python-is-python3 \
        u-boot-tools file curl
```


# 7. 编译

配置:

```bash
$ cd android-12.1.0_r11/
$ source build/envsetup.sh
$ lunch aosp_arm-eng
```

编译:

```bash
$ m -j16
```

或者:

```bash
$ make -j16
```


# 8. 运行

```bash
$ emulator
```


# 9. 参考

- [清华大学 AOSP](https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/).
- [索尼编译 Android 11](https://developer.sony.com/open-source/aosp-on-xperia-open-devices/guides/aosp-build-instructions/build-aosp-android-11-0/).
