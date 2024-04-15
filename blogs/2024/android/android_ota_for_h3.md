
# Android OTA for AllWinner H3

# 1. OTA的升级范围

原生Android 提供的Recovery 升级程序只支持更新 system 分区、recovery 分区及 boot 分区。除此之外，我们根据产品特点，给 Recovery 扩展了一些专有功能，以满足 BSP 的更新需要。

| 分区类型                      | 是否支持 |
| ---------------------------- | ------- |
| Boot 分区更新                 | √       |
| System 分区更新               | √       |
| Recovery 分区更新             | √       |
| Env 分区更新                  | √       |
| Bootloader 分区更新           | √       |
| Nand 方案 Boot0/Uboot 升级    | √       |
| TSD/EMMC 方案Boot0/Uboot 升级 | √       |
| sys_config.fex 更新           | √       |
| sys_partition.fex 更新        | ×       |

值得注意的是，BSP中的大部分关于模块的配置都集中在 sys_config.fex 中，如果需要更新 sys_config.fex 的配置，就必须通过更新 uboot。
在制作更新包时，要想新的 sys_config.fex 生效，务必记得在执行 make 命令之前先执行 get_uboot 确保当前的sys_config.fex 配置被打到 uboot 中。


# 2. 升级注意事项

## 2.1 OTA 不能改变分区数目及其大小

Recovery 只是一个运行在 Linux 上的一个普通应用程序，它并没有能力对现有分区表进行调整，所以第一次量产时就要将分区的数目和大小确定清楚，杜绝后续升级调整分区数目及其大小的想法，OTA 不能改变分区数目和分区的大小。


## 2.2 cache 分区的大小确定

原生Recovery 机制中，因为 Recovery 内的分区挂载路径与 Android 的分区挂载路径并不完全相同，所以在 Android 上层传入更新包地址时，必须要保证这个包路径在 Recovery 和 Android 系统都是相同的。
能够读写的分区中只有 cache 分区和 data 分区会被 Recovery 和 Android 系统同时挂载，这意味着需要将包放这两个分区中，Recovery 才能识别。所以 Google 原生策略中，当在外部储存选择一个升级包时，都默认复制到 cache 分区中。所以在划分分区时需要注意要分配 cache 分区足够大的空间， 否则可能出现无法容纳更新包而导致无法升级的问题。


## 2.3 misc 分区需要有足够的权限被读写

misc 分区 Recovery 与 Android 之间的桥梁，如果 misc 分区的读写权限过高，会导致上层应用无法对其写入数据，则会令 Recovery 功能异常。检验此功能存在问题时，请确保 misc 分区的设备节点/dev/block/xxx 和其软链接/dev/block/misc 有足够的权限被读写。比如，dolphin-fvd-p1 方案的nandg 的 group 权限为 system。

```bash
root@android:/dev/block/by-name# ls -l
lrwxrwxrwx root root 2000-0102 07:16 misc->/dev/block/mmcblk0p8(misc 分区软连接)
...
root@android:/dev/block# ls -l
brw-rw----root  system  93, 48 2000-01-02 07:16 mmcblk0p8
...
```


# 3. 制作OTA包

使用OTA 区分三个包：

- TargetFile: 包含制作时当前编译版本的 system 分区，boot 分区，recovery 分区等内容，可用于制作OTA 完整包和差分包。
- OTA 完整包：包含本次升级版本的所有内容，可以从之前各个版本直接升级到当前的版本。制作完整包需要当前版本的TargetFile。
- OTA 差分包：包含本次升级版本和之前特定一个版本的升级内容，只适用于之前特定一个版本升级到当前版本。制作差分包需要之前特定版本的TargetFile 和当前版本的 TargetFile。


## 3.1 制作不带签名的 OTA 包的步骤

1. 制作不带签名的TargetFile

在编译完Android 后，输入以下命令：

```bash
$ make ota_target_file
```

或者:

```bash
$ get_uboot
$ make target-files-packages
```

make_ota_target_file 只是封装一些命令，具体可以查看 device/softwinner/common/vendorsetup.sh 里面关于 make_ota_target_file 的定义。

get_uboot 命令作用是从 lichee 目录中复制必要的更新文件到更新包中。不执行该命令会导致后面的 make 动作报错。

最后得到TargetFile 的路径(device 表示编译的方案，time 表示当天的日期)：

out/target/product/[device1]/obj/PACKAGING/target_files_intermediates/[device2]-target_files-[time].zip

(其中 device1 = $tdevice, device2 = $TARGET_PRODUCT，可以echo $tdevice 可以看 device1,echo $TARGET_PRODUCT 可看 device2)

2. 制作不带签名的OTA 完整包

```bash
$ get_uboot
$ make otapackage -j8
```

或者

```bash
$ make ota_package
```

make_ota_package 只是封装一些命令，具体可以查看 device/softwinner/common/vendorsetup.sh 里面关于 make_ota_package 的定义。

get_uboot 命令作用是从 lichee 目录中复制必要的更新文件到更新包中。不执行该命令会导致后面的 make 动作报错。

得到的 OTA 完整包的路径(device 表示编译的方案，time 表示当天的日期）：

out/target/product/[device1]/[device2]-ota-[time].zip

(其中 device1 = $tdevice, device2 = $TARGET_PRODUCT，可以echo $tdevice 可以看 device1, echo $TARGET_PRODUCT 可看 device2)

3. 制作不带签名的OTA 差分包

要生成差分包，必须获得前一版本的 target-file 文件，具体参考前面如何制作TargetFile。

将要升级版本( 旧版) 的 target-file 文件拷贝到 Android 的根目录下，并重命名为old_target_files.zip。保证 Android 的根目录下只有一个*.zip 的文件。之后执行以下命名将可以生成差分包：

```bash
$ make otapackage_inc
```

得到的 OTA 差分包的路径(device 表示编译的方案，time 表示当天的日期）

out/target/product/[device1]/[device2]-ota-[time]-inc.zip

(其中device1 = $tdevice, device2 = $TARGET_PRODUCT，可以echo $tdevice 可以看device1, echo$TARGET_PRODUCT 可看 device2)

注意：

1. 该差分包仅对指定的前一版本固件有效。
2. 制作一个完整包，也会生成当前版本的一个 target-file 文件包。


## 3.2 制作带签名的 OTA 升级包

签名可以让Android 带有厂家私有的签名，在 OTA 升级的时候会预先校验签名，如果签名不匹配则无法进行OTA 升级，签名校验保证了 OTA 包的来源合法性，OTA 包的完成性（没有被第三方修改过）。制作带签名的OTA 升级包的流程如下：

1. 生成没有签名的TargetFile

参考制作没有待签名的OTA 包生成 TargetFile 的方法。

2. TargetFile 签名

```bash
$ ./build/tools/releasetools/sign_target_files_apks -d [key_path] -o [unsigned target file.zip] [signed target file.zip]
```

[key_path]为存放 key 文件夹的路径，需要包括 4 个 key 分别是 media，platform，releasekey，shared，具体包含以下文件：media.pem，media.x509.pem，platform.pk8，releasekey.pem，releasekey.x509.pem，shared.pk8，media.pk8，platform.pem，platform.x509.pem，releasekey.pk8，shared.pem，shared.x509.pem.

-o 表示替换一个公用的 ota_key，这个 key 确保第三方的 ota 包没有办法升级.

[unsigned_target_file.zip]表示上一步生成的没有签名的 TargetFile.

[signed_target_file.zip]表示命令输出得到的经过签名的 TargetFile 签名的时候需要按照提示输入相应的证书的密码。

3. 从签名过的TargetFile 得到镜像（boot.img,system.img 和 recovery.img)

```bash
$ ./build/tools/releasetools/img_from_target_files [signed_target_file.zip] [img.zip]
```

[signed_target_file.zip]表示经过签名的TargetFile.

[img.zip]表示命令输出得到的镜像压缩包.

4. 解压 img.zip,得到的 boot.img,system.img 和 recovery.img 复制到 target/product/[device1]/下面， 重新 pack 得到可烧录的固件，是签名过的固件。

5. 生成 ota 包完整包

```bash
$ ./build/tools/releasetools/ota_from_target_files -k [releaseKey] [signed_target_file.zip] [ota_full.zip]
```

[releaseKey] 和 sign_target_files_apks 用来签名的 release key 相对应.

[signed_target_file.zip]表示经过签名的TargetFile.

[ota_full.zip]表示命令输出得到的 OTA 完整包.

6. 生成 ota 包完整包

```bash
$ ./build/tools/releasetools/ota_from_target_files -k [releaseKey] -i [signed target file v1.zip] [signed_target_file_v2.zip] [ota_inc.zip]
```

[releaseKey] 和 sign_target_files_apks 用来签名的 release key 相对应.

[signed_target_file_v1.zip]表示经过签名的版本 v1 的 TargetFile.

[signed_target_file_v2.zip]表示经过签名的版本 v2 的 TargetFile.

[ota_inc.zip]表示命令输出得到的 OTA 完整包.


## 3.3 制作 OTA 包常见问题和注意事项

1. 执行签名命令的时候./build/tools/releasetools/sign_target_files_apks 出现类似的提示：

```bash
ERROR: no key specified for:
    xxxx.apk
    Use '-e <apkname>=' to specify a key (which may be an empty string to not sign this apk).
```

这种情况通常是定义这个 apk 的 Android.mk 文件编写不规范导致，解决方法修改这个 apk 的 Android.mk 文件，或者按照提示使用-e 参数，

例如-e xxx.apk=	表示不对这个 apk 重新签名.

或者-e xxx.apk=[pathToKey]/[key]，[pathToKey]是存放 key 的文件夹，[key]根据实际情况选择 media， platform，releasekey 和 shared 其中一个.

2. 如何生成key?

android/build/tools/mkkey.sh 脚本，打开脚本看到下面的一句定义:

```bash
AUTH='/C=CN/ST=GuangDong/L=ZhuHai/O=Allwinner/OU=Allwinner/CN=China/emailAddress=a llwinnertech@com'
```

请按照实际需求修改，然后执行

```bash
./mkkey.sh media
```

按照提示输入这个key 的密码.

然后就会在此目录下生成:media.pem	media.pk8	media.x509.pem 三个文件。

3. TargetFile 和固件是否匹配的区分在 Android 设备执行

```bash
adb pull /system/build.prop
```

会得到这个固件的build.prop 文件.

对于TargetFile，解压出来查看 SYSTEM/build.prop，对比这两个 build.prop 如果一致，表示这个固件和 TargetFile 是匹配的.


# 4. OTA扩展功能

## 4.1 支持外部储存读取更新包

Android 原生的OTA 升级包是放在/cache 分区的，但是随着版本的迭代，有可能出现 cache 分区不足以容纳OTA 升级包的状况。针对这种情况，新版本的Recovery 支持软连接形式，从U 盘、SD 卡直接读取更新包，不用再把更新包复制到 cache 分区中，从而减少升级时间。

该功能都封装在 adnroid/frameworks/base/swextend/os/java/softwinner/os/RecoverySystemEx.java 中，调用该类的静态方法 installPackageEx()方法，该方法需要传入更新包的路径和应用 Context 实例。


## 4.2 Usb-Recovery 功能

Usb-recovery模式达到让用户即使不进入Android 系统，也能够安装指定更新包的目的，让用户在系统异常无法进入系统的情况下，安装更新包恢复系统，给用户一条还原系统的通道。

Usb-recovery 模式是指将更新包改名为 update.zip，然后放到一个 u 盘的根目录上，插入 u 盘到小机中，按着小机的Usb-recovery 按键进入Usb-recovery。进行Usb-recovery 模式之后，recovery 会自动搜索并安装u 盘上的 update.zip 包。

Usb-recovery，即可通过U 盘升级 OTA 包，有两种方法：

方法(1) 在“设置”-->“备份和重置”-->“系统恢复\升级”-->选择需要升级的 OTA 包；
方法(2) 按住机器"recovery 键"，上电进入 recovery 升级；

必须特别注意的是升级包必须命名为 update.zip，且只能放在该分区的根目录。具体配置如下：

打开 Usb-Recovery 功能需要修改方案的 sys_config.fex 文件里相关配置，sys_config.fex 里面配置旁边也有相关说明，如下：

```bash
;--------------------------------------------------
;	used: 模块使能端	1：开启模块	0：关闭模块
;	mode: 模式选择	1：一键进入 OTA 升级	2：一键恢复（通过 sysrecovery 分区来恢复） 其他值：无效
;	recovery_key ： 按键配置 （例如：recovery_key= port:PH16<0><default>）
;--------------------------------------------------
[recovery_para]
used =	1
mode =	1
recovery_key = port:PH16<0><default><default><default>
```
