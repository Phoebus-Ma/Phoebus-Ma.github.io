
# Linux USB gadget configured through configfs

25th April 2013


# 1. Overview

USB Linux Gadget 是一种具有 UDC（USB 设备控制器）的设备，可以连接到 USB 主机以扩展其附加功能，例如串行端口或大容量存储功能。

gadget被其主机视为一组配置，每个配置都包含许多接口，从gadget的角度来看，这些接口被称为函数，每个函数代表例如 串行连接或 SCSI 磁盘。

Linux 提供了许多功能供gadget使用。

创建gadget意味着决定将有哪些配置以及每个配置将提供哪些功能。

Configfs（请参阅 [Configfs - 用户空间驱动的内核对象配置](https://www.kernel.org/doc/html/latest/filesystems/configfs.html)）非常适合告诉内核有关 上述决定。 本文档介绍了如何执行此操作。

它还描述了如何设计 configfs 集成到gadget中。


# 2. Requirements

为了使其工作，configfs 必须可用，因此 .config 中的 CONFIGFS_FS 必须为“y”或“m”。 截至撰写本文时，USB_LIBCOMPOSITE 选择 CONFIGFS_FS。


# 3. Usage

(描述通过 configfs 提供的第一个功能的原始帖子可以在这里看到：<http://www.spinics.net/lists/linux-usb/msg76388.html>)

```bash
$ modprobe libcomposite
$ mount none $CONFIGFS_HOME -t configfs
```

其中 CONFIGFS_HOME 是 configfs 的挂载点。


## 3.1 Creating the gadgets

对于要创建的每个gadget，必须创建其相应的目录：

```bash
$ mkdir $CONFIGFS_HOME/usb_gadget/<gadget name>
```

例如：

```bash
$ mkdir $CONFIGFS_HOME/usb_gadget/g1

...
...
...

$ cd $CONFIGFS_HOME/usb_gadget/g1
```

每个gadget都需要指定其供应商 ID <VID> 和产品 ID <PID>：

```bash
$ echo <VID> > idVendor
$ echo <PID> > idProduct
```

gadget还需要其序列号、制造商和产品字符串。 为了有地方存储它们，必须为每种语言创建一个字符串子目录，例如：

```bash
$ mkdir strings/0x409
```

然后可以指定字符串：

```bash
$ echo <serial number> > strings/0x409/serialnumber
$ echo <manufacturer> > strings/0x409/manufacturer
$ echo <product> > strings/0x409/product
```

可以将更多自定义字符串描述符创建为语言目录中的目录，并将字符串文本写入字符串目录中的“s”属性：

```bash
$ mkdir strings/0x409/xu.0 $ echo <string text> > strings/0x409/xu.0/s
```

在函数驱动程序支持的情况下，函数可能允许符号链接到这些自定义字符串描述符，以将这些字符串与类描述符相关联。


## 3.2 Creating the configurations

每个gadget都会包含许多配置，必须创建它们相应的目录：

```bash
$ mkdir configs/<name>.<number>
```

其中 <name> 可以是文件系统中合法的任何字符串，<number> 是配置的编号，例如：

```bash
$ mkdir configs/c.1

...
...
...
```

每个配置还需要其字符串，因此必须为每种语言创建一个子目录，例如：

```bash
$ mkdir configs/c.1/strings/0x409
```

然后可以指定配置字符串：

```bash
$ echo <configuration> > configs/c.1/strings/0x409/configuration
```

还可以为配置设置一些属性，例如：

```bash
$ echo 120 > configs/c.1/MaxPower
```


## 3.3 Creating the functions

gadget会提供一些功能，对于每个功能都必须创建相应的目录：

```bash
$ mkdir functions/<name>.<instance name>
```

其中 <name> 对应于允许的函数名称之一，实例名称是文件系统中允许的任意字符串，例如：

```bash
$ mkdir functions/ncm.usb0 # usb_f_ncm.ko gets loaded with request_module()

...
...
...
```

每个函数都提供其特定的属性集，具有只读或读写访问权限。 在适用的情况下，需要将其写入适当的地方。 请参阅 Documentation/ABI/testing/configfs-usb-gadget 了解更多信息。


## 3.4 将功能与其配置相关联

此时创建了许多gadget，每个gadget都有许多指定的配置和许多可用的功能。 剩下的就是指定哪个功能在哪个配置中可用（相同的功能可以在多个配置中使用）。 这是通过创建符号链接来实现的：

```bash
$ ln -s functions/<name>.<instance name> configs/<name>.<number>
```

例如：

```bash
$ ln -s functions/ncm.usb0 configs/c.1

...
...
...
```


## 3.5 Enabling the gadget

上述所有步骤都是为了构成配置和功能的gadget。

示例目录结构可能如下所示：

```bash
.
./strings
./strings/0x409
./strings/0x409/serialnumber
./strings/0x409/product
./strings/0x409/manufacturer
./configs
./configs/c.1
./configs/c.1/ncm.usb0 -> ../../../../usb_gadget/g1/functions/ncm.usb0
./configs/c.1/strings
./configs/c.1/strings/0x409
./configs/c.1/strings/0x409/configuration
./configs/c.1/bmAttributes
./configs/c.1/MaxPower
./functions
./functions/ncm.usb0
./functions/ncm.usb0/ifname
./functions/ncm.usb0/qmult
./functions/ncm.usb0/host_addr
./functions/ncm.usb0/dev_addr
./UDC
./bcdUSB
./bcdDevice
./idProduct
./idVendor
./bMaxPacketSize0
./bDeviceProtocol
./bDeviceSubClass
./bDeviceClass
```

这样的gadget必须最终启用，以便 USB 主机可以枚举它。

为了启用该gadget，必须将其绑定到 UDC（USB 设备控制器）：

```bash
$ echo <udc name> > UDC
```

其中 <udc name> 是 /sys/class/udc/* 中找到的名称之一，例如：

```bash
$ echo s3c-hsotg > UDC
```


## 3.6 Disabling the gadget

```bash
$ echo "" > UDC
```


## 3.7 Cleaning up

从配置中删除函数：

```bash
$ rm configs/<config name>.<number>/<function>
```

其中 <config name>.<number> 指定配置，<function> 是指向从配置中删除的函数的符号链接，例如：

```bash
$ rm configs/c.1/ncm.usb0

...
...
...
```

删除配置中的字符串目录：

```bash
$ rmdir configs/<config name>.<number>/strings/<lang>
```

例如：

```bash
$ rmdir configs/c.1/strings/0x409

...
...
...
```

并删除配置：

```bash
$ rmdir configs/<config name>.<number>
```

例如：

```bash
rmdir configs/c.1

...
...
...
```

删除函数（但不会卸载函数模块）：

```bash
$ rmdir functions/<name>.<instance name>
```

例如：

```bash
$ rmdir functions/ncm.usb0

...
...
...
```

删除gadget中的字符串目录：

```bash
$ rmdir strings/<lang>
```

例如：

```bash
$ rmdir strings/0x409
```

最后删除gadget：

```bash
$ cd ..
$ rmdir <gadget name>
```

例如：

```bash
$ rmdir g1
```


# 4. Implementation design

下面介绍了 configfs 的工作原理。 在 configfs 中有项目和组，两者都表示为目录。 项目和组之间的区别在于，组可以包含其他组。 下图中仅显示了一个项目。 项目和组都可以具有属性，这些属性以文件的形式表示。 用户可以创建和删除目录，但不能删除文件，文件可以是只读的，也可以是读写的，具体取决于它们所代表的内容。

configfs 的文件系统部分在 config_items/groups 和 configfs_attributes 上运行，它们是通用的，并且对于所有配置的元素具有相同的类型。 然而，它们嵌入到特定用途的较大结构中。 下图中，“cs”包含 config_item，“sa”包含 configfs_attribute。

文件系统视图将如下所示：

```bash
./
./cs        (directory)
   |
   +--sa    (file)
   |
   .
   .
   .
```

每当用户读/写“sa”文件时，就会调用一个接受 struct config_item 和 struct configfs_attribute 的函数。 在所述函数中，使用众所周知的container_of技术检索“cs”和“sa”，并且调用适当的sa函数（显示或存储）并传递“cs”和字符缓冲区。 “show”用于显示文件内容（将数据从cs复制到缓冲区），而“store”用于修改文件内容（将数据从缓冲区复制到cs），但这取决于实现者 这两个函数来决定它们实际做什么。

```bash
typedef struct configured_structure cs;
typedef struct specific_attribute sa;

                                       sa
                       +----------------------------------+
        cs             |  (*show)(cs *, buffer);          |
+-----------------+    |  (*store)(cs *, buffer, length); |
|                 |    |                                  |
| +-------------+ |    |       +------------------+       |
| | struct      |-|----|------>|struct            |       |
| | config_item | |    |       |configfs_attribute|       |
| +-------------+ |    |       +------------------+       |
|                 |    +----------------------------------+
| data to be set  |                .
|                 |                .
+-----------------+                .
```

文件名由配置项/组设计者决定，而目录一般可以随意命名。 一个组可以自动创建多个默认子组。

有关 configfs 的更多信息，请参阅 [Configfs - Userspace-driven Kernel Object Configuration](https://www.kernel.org/doc/html/latest/filesystems/configfs.html).

上面描述的概念转化为 USB gadget是这样的：

1. gadget有其配置组，其中包含一些属性（idVendor、idProduct 等）和默认子组（配置、函数、字符串）。 写入属性会使信息存储在适当的位置。 在配置、函数和字符串子组中，用户可以创建其子组来表示给定语言的配置、函数和字符串组。

2. 用户创建配置和函数，在配置中创建函数的符号链接。 当写入gadget的 UDC 属性时，将使用此信息，这意味着将gadget绑定到 UDC。 drivers/usb/gadget/configfs.c 中的代码会迭代所有配置，并且在每个配置中迭代所有函数并绑定它们。 这样整个gadget就被绑定了。

3. 文件 drivers/usb/gadget/configfs.c 包含以下代码：

   - gadget's config_group
   - gadget's default groups (configs, functions, strings)
   - 将函数与配置关联起来（符号链接）

4. 每个 USB 函数自然都有自己想要配置的视图，因此特定函数的 config_groups 在函数实现文件 drivers/usb/gadget/f_*.c 中定义。

5. 函数的代码是按照它使用的方式编写的。

usb_get_function_instance()，它又调用request_module。 因此，只要 modprobe 工作，特定功能的模块就会自动加载。 请注意，反之则不然：在一个gadget被禁用并拆除后，模块仍保持加载状态。
