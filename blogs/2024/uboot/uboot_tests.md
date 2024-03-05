
# Tables

- [1. 测试简介](#1-introduction-to-testing)
- [2. U-Boot pytest套件](#2-u-boot-pytest-suite)
- [3. 编写测试](#3-writing-tests)
- [4. Sandbox测试](#4-sandbox-tests)


# 1. Introduction to testing

U-Boot的代码量很大。 此文件描述了如何测试此代码以及添加新功能时应编写哪些测试。


## 1.1 Running tests

要在沙箱上运行大多数测试，请输入以下内容：

```bash
make check
```

在U-Boot目录下。 请注意，使用此命令仅运行 pytest 套件。

有些测试需要很长时间才能运行，并标有@pytest.mark.slow。 要仅运行快速程序，请输入：

```bash
make qcheck
```

也可以只运行工具（patman、binman 等）的测试。 此类测试包含在这些工具中，即不运行实际的 U-Boot 单元测试。 输入：

```bash
make tcheck
```

您还可以并行运行选择测试：

```bash
make pcheck
```

以上所有内容都使用带有参数的测试/运行脚本来选择运行哪些测试。 有关更多信息，请参阅 [U-Boot pytest](#2-u-boot-pytest-suite) 套件。


## 1.2 Sandbox

U-Boot 可以构建为用户空间应用程序（例如用于 Linux）。 这允许在不需要目标硬件的情况下执行测试。 “沙盒”目标提供了此功能，并在测试中广泛使用。

有关更多信息，请参阅[沙盒测试](#4-sandbox-tests)。


## 1.3 Pytest Suite

许多测试可以使用 test/py 中的 pytest 套件进行。 它可以在沙箱或真实硬件上运行。 它依赖于 U-Boot 控制台来注入测试命令并检查结果。 它的运行速度比 C 代码慢，但提供了统一大量测试并总结其结果的能力。

您可以使用以下命令在沙箱上运行测试：

```bash
./test/py/test.py --bd sandbox --build
```

这将产生 HTML 输出 build-sandbox/test-log.html

一些测试使用其他版本的沙箱运行。 例如，sandbox_flattree 在禁用 livetree（分层设备树）的情况下运行测试。 您还可以使用 -k 选择特定测试：

```bash
./test/py/test.py --bd sandbox_flattree --build -k hello
```

有一些在 SPL 中运行的特殊测试。 为此，您需要 sandbox_spl 构建：

```bash
./test/py/test.py --bd sandbox_spl --build -k test_spl
```

有关 pytest 套件的更多信息，请参阅 test/py/README.md。

有关如何直接运行测试（而不是通过 pytest）的信息，请参阅沙盒测试。


## 1.4 tbot

Tbot 提供了一种在目标硬件上执行测试的方法。 它旨在自动在许多主板上尝试 U-Boot 和 Linux（以及可能的其他软件）。 它可用于创建连续的测试环境。 请参阅 <http://www.tbot.tools> 了解更多信息。


## 1.5 Ad-hoc tests

有几个在 pytest 环境之外运行的临时测试：

**test/fs**

文件系统测试（shell脚本）

**test/image**

FIT 和 legacy图像测试（shell 脚本和 Python）

**test/stdint**

测试stdint.h可以在U-Boot中使用（shell脚本）

**trace**

测试跟踪功能（shell 脚本）

TODO: 将这些移至 pytest 中。


## 1.6 When to write tests

如果您在未经测试的情况下将代码添加到 U-Boot，那么您就是在冒险。 即使您在提交时执行了彻底的手动测试，当将来对 U-Boot 进行更改时，它也可能会中断。 如果其他更改与之交互，它甚至可能在应用于主线时中断。 一个好的心态是，未经测试的代码可能不起作用，应该删除。

您可以假设 Pytest 套件将在补丁被主线接受之前运行，因此这可以防止未来的破坏。

另一方面，有相当多的代码没有被测试覆盖，或者很少被覆盖。 所以这里有一些建议：

- 如果要添加新的 uclass，请添加沙箱驱动程序和使用它的测试；
- 如果您正在修改现有测试所涵盖的代码，请添加新的测试用例来涵盖您的更改；
- 如果您正在修改的代码尚未经过测试，请考虑编写一个。 即使是非常基本的测试也是有用的，并且可以被其他人学习和增强。 添加到测试中要容易得多 - 编写新的大型测试对于大多数贡献者来说似乎令人畏惧。

请参阅文档: tests_writing 了解如何编写测试。


## 1.7 Future work

将现有的 shell 脚本转换为 pytest 测试。


# 2. U-Boot pytest suite

## 2.1 Introduction

该工具旨在通过使用控制台界面执行 U-Boot shell 命令来测试 U-Boot。 存在单个顶级脚本来执行或附加到 U-Boot 控制台，对其运行整个测试脚本，并总结结果。 这种方法的优点是：

- 测试的执行方式与用户或脚本与 U-Boot 交互的方式相同； 不能有任何断开。
- 无需在 U-Boot 本身中编写或嵌入测试相关代码。 有人断言，用 Python 编写与测试相关的代码比用 C 编写更简单、更灵活。但是请参阅 [Writing Tests](#3-writing-tests) 了解注意事项和更多讨论/分析。
- 通过这种方式与 U-Boot 交互相当简单。


## 2.2 Requirements

测试套件是使用 pytest 实现的。 与 U-Boot 控制台的交互涉及执行一些二进制文件并与其标准输入/标准输出交互。 您将需要实现测试套件在适当的时间调用的各种“挂钩”脚本。

为了至少运行测试套件，我们需要安装 Python 3 和适用于 Python 3 的 pip。 所有必需的 python 模块都在 /test/py/ 目录中的requirements.txt 文件中描述，并且可以通过以下命令安装：

```bash
pip install -r requirements.txt
```

为了在其支持的平台上执行某些测试，将需要其他工具。 以下是不完整的列表：

- gdisk
- dfu-util
- dtc
- openssl
- sudo OR guestmount
- e2fsprogs
- util-linux
- coreutils
- dosfstools
- efitools
- guestfs-tools
- mount
- mtools
- sbsigntool
- udisks2

请使用适合您的发行版的命令，将这些工具与提供它们的软件包相匹配。

测试脚本支持：

- 在本地计算机上作为子进程执行 U-Boot 的沙箱端口，并通过 stdin/stdout 与其交互。
- 执行外部“挂钩”脚本，将 U-Boot 二进制文件刷新到物理板上，连接到板的控制台流，并重置板。 进一步的细节稍后描述。

在测试中应避免使用命令“sudo”。 要创建磁盘映像，请使用 guestfs-tools 包提供的命令 virt-make-fs。 此命令创建一个带有 QEMU 的虚拟机，并在其中生成磁盘映像。

命令 virt-make-fs 需要对当前内核的读访问权限。 在 Ubuntu 上只有 root 有此权限。 您可以添加包含以下内容的脚本 /etc/initramfs-tools/hooks/vmlinuz 来解决该问题：

```bash
#!/bin/sh
echo "chmod a+r vmlinuz-*"
chmod a+r /boot/vmlinuz-*
```

该脚本应为 chmod 755。每当更新初始 RAM 文件系统时都会调用该脚本。


### 2.2.1 Using virtualenv to provide requirements

为了确保可重复性，推荐的运行测试套件的方法是使用 virtualenv 设置必要的环境。 这可以通过以下命令完成：

```bash
cd /path/to/u-boot
sudo apt-get install python3 python3-virtualenv
virtualenv -p /usr/bin/python3 venv
. ./venv/bin/activate
pip install -r test/py/requirements.txt
```


## 2.3 Testing sandbox

要在沙箱端口（U-Boot 构建为本机用户空间应用程序）上运行测试套件，只需执行：

```bash
./test/py/test.py --bd sandbox --build
```

–bd 选项告诉测试套件正在测试哪种板类型。 这让测试套件知道该板具有哪些功能，从而准确地知道可以测试什么。

–build 选项告诉 U-Boot 编译 U-Boot。 或者，您可以忽略此选项并在运行测试脚本之前以您选择的任何方式自行构建 U-Boot。

测试脚本将附加到 U-Boot，执行主板的所有有效测试，然后打印测试过程的摘要。 测试会话的完整日志将写入${build_dir}/test-log.html。 最好在 Web 浏览器中查看，但也可以直接以纯文本形式读取，也许可以借助 html2text 实用程序。

如果沙箱崩溃（例如出现段错误），您将看到如下消息：

```bash
test/py/u_boot_spawn.py:171: in expect
    c = os.read(self.fd, 1024).decode(errors='replace')
E   ValueError: U-Boot exited with signal 11 (Signals.SIGSEGV)
```


### 2.3.1 Controlling output

默认情况下会报告短回溯。 如果您想要更长的时间，请在运行测试时传递 --tb=long 。 有关更多选项，请参阅 pytest 文档。


### 2.3.2 Running tests in parallel

注意：目前并非所有测试都可以并行运行，因此通常的方法是只运行那些可以并行运行的测试。

首先安装对并行测试的支持：

```bash
sudo apt install python3-pytest-xdist
```

或:

```bash
pip3 install pytest-xdist
```

然后使用 -n 标志并行运行测试：

```bash
test/py/test.py -B sandbox --build --build-dir /tmp/b/sandbox -q -k \
    'not slow and not bootstd and not spi_flash' -n16
```

您还可以使用 make pcheck 并行运行所有测试。 这最多使用 16 个线程，因为设置时间很长，并且测试数量不足 1000 个。

请注意，test-log.html 输出目前在并行测试中无法正常工作。 所有线程同时写入它，所以它是乱码。

请注意，工具/测试仍然会依次运行每个工具的测试，尽管在此期间它们确实并行运行。 例如，buildman 测试并行运行，然后 binman 测试并行运行。 将它们全部并行运行将具有显着的优势，但这将需要大量的重构，例如 更多地使用 pytest 装置。 代码覆盖率测试被省略，因为它们由于 Python 限制而无法并行运行。


### 2.3.3 Testing under a debugger

如果需要在调试器下运行沙箱，可以传递命令行选项 –gdbserver COMM。 这会导致发生两件事：

它将在 gdbserver 下运行，而不是直接运行 U-Boot，并通过通道 COMM 进行调试通信。 您可以将调试器附加到沙箱进程以对其进行调试。 有关 COMM 有效值的详细信息，请参阅 man gdbserver 和下面的示例。

测试中的所有超时均被禁用，允许 U-Boot 在任意时间段内执行命令。 如果 U-Boot 在调试期间停在断点处，这非常有用。

一个用法示例是：

Window 1:

```bash
./test/py/test.py --bd sandbox --gdbserver localhost:1234
```

Window 2:

```bash
gdb ./build-sandbox/u-boot -ex 'target remote localhost:1234'
```

或者，您可以省略 -ex 选项，并在 gdb 启动后手动将命令键入到其中。

您可以使用任何您想要的调试器，只要它支持 gdb 远程协议，或者任何围绕 gdb 的图形包装器。

一些测试故意导致沙箱进程退出，例如 测试重置命令或沙箱的 CTRL-C 处理。 发生这种情况时，您需要将调试器附加到新的沙箱实例。 如果这些测试与您的调试会话无关，您可以使用 pytest 的 -k 命令行选项跳过它们； 请参阅下一节。


## 2.4 Command-line options

**--board-type, --bd, -B**

设置待测单板的类型。 例如，沙箱或海滨。

**–board-identity`, –id**

设置要测试的板的身份。 这允许区分连接到同一主机的相同类型物理板的多个实例。 该参数不会由测试脚本以任何方式解释，而是简单地传递给下面描述的挂钩脚本，并且可以以任何认为必要的特定于站点的方式使用。

**--build**

指示测试脚本应在运行测试之前编译 U-Boot 本身。 如果使用此选项，请确保已设置构建过程所需的任何环境变量，例如 $CROSS_COMPILE。

**--buildman**

表示-build应该使用buildman来构建U-Boot。 在这种情况下不需要设置 $CROSS_COMPILE` 因为 buildman 会处理它。

**--build-dir**

设置包含已编译的 U-Boot 二进制文件的目录。 如果省略，则为 ${source_dir}/build-${board_type}。

**--result-dir**

设置将结果（例如日志文件）写入其中的目录。 如果省略，则使用构建目录。

**--persistent-data-dir**

设置用于存储持久测试数据的目录。 这是可以在测试运行中重复使用的测试数据，例如文件系统映像。

pytest 还实现了许多自己的命令行选项。 下面提到了常用的选项。 请参阅 pytest 文档以获取完整的详细信息。 执行 py.test –version 以获得简短摘要。 请注意，U-Boot 的 test.py 脚本将所有命令行参数直接传递给 pytest 进行处理。

**-k**

选择要运行的测试。 默认设置是运行所有已知的测试。 此选项采用单个参数，用于过滤测试名称。 支持简单的逻辑运算符。 例如：

- “-k ums”仅运行名称中带有“ums”的测试。
- “-k ut_dm”仅运行名称中带有“ut_dm”的测试。请注意，在这种情况下，“ut_dm”是测试的参数，而不是测试名称。完整的测试名称是例如 “test_ut[ut_dm_leak]”。
- ‘-k not Reset’运行除名称中带有“reset”的测试之外的所有内容。
- ‘-k ut or hush’仅运行名称中带有“ut”或“hush”的测试。
- ‘-k not (ut or hush)’运行除了名称中带有“ut”或“hush”的测试之外的所有内容。

**-s**

防止 pytest 隐藏测试的标准输出。 这允许您在 pytest 的标准输出上实时查看 U-Boot 的控制台日志。


## 2.5 Testing real hardware

用于与真实硬件交互的工具和技术在不同的主机和目标系统以及用户的想法之间会有很大的不同。 因此，测试套件不会尝试以任何方式直接与真实硬件交互。 相反，它通过 $PATH 执行一组标准化的“hook”脚本。 这些脚本代表测试套件实现某些操作。 这使得测试套件变得简单，并与与 U-Boot 功能无关的系统差异隔离。

### 2.5.1 Hook scripts

#### 2.5.1.1 Environment variables

运行钩子脚本时设置以下环境变量：

- UBOOT_BOARD_TYPE 正在测试的板类型。
- UBOOT_BOARD_IDENTITY 正在测试的主板标识，如果未指定则为 na。
- UBOOT_SOURCE_DIR U-Boot 源目录。
- UBOOT_TEST_PY_DIR 源目录中 test/py/ 的完整路径。
- UBOOT_BUILD_DIR U-Boot 构建目录。
- UBOOT_RESULT_DIR 测试结果目录。
- UBOOT_PERSISTENT_DATA_DIR 测试持久数据目录。


#### 2.5.1.2 u-boot-test-console

此脚本提供对 U-Boot 控制台的访问。 脚本的 stdin/stdout 应连接到板的控制台。 这个进程应该继续无限期地运行，直到被杀死。 测试套件将与所有其他挂钩并行运行此脚本。

该脚本可以被实现，例如 通过 exec() 执行 cu、kermit、conmux 等。

如果您能够在 QEMU 等硬件模拟器下运行 U-Boot，那么您可能会从此脚本生成该模拟器。 但是，请注意，每个测试脚本运行时可能会多次调用 u-boot-test-reset，并且每次都必须使 U-Boot 从头开始执行。 希望您的模拟器包含一个虚拟重置按钮！ 如果没有，您可以从 u-boot-test-reset 启动模拟器，同时安排此控制台进程始终与当前模拟器实例通信。


#### 2.5.1.3 u-boot-test-flash

在针对主板运行测试套件之前，必须进行一些安排，以便主板执行要测试的特定 U-Boot 二进制文件。 通常这涉及将 U-Boot 二进制文件写入主板的闪存 ROM。 测试套件为此目的调用此挂钩脚本。

该脚本应该同步执行整个刷新过程； 该脚本仅应在刷新完成后退出，并且板重置将导致执行新刷新的 U-Boot 二进制文件。

可以想象这个脚本不会做任何事情。 这在以下情况下可能有用：

- 在运行测试套件之前，其他一些进程已经将所需的 U-Boot 二进制文件写入主板的闪存中。
- 该板允许将 U-Boot 直接下载到 RAM 中，并从那里执行。 使用此功能将减少主板闪存的磨损，因此如果可用且不需要 U-Boot 冷启动测试，则可能更可取。 如果使用此功能，则 u-boot-test-reset 脚本应执行此下载，因为可以想象，在单次测试运行中可以多次重置主板。

由用户来确定这些情况是否存在，并适当地编写此挂钩脚本。

该脚本通常通过调用某些 SoC 或主板特定的供应商刷新实用程序来实现。


#### 2.5.1.4 u-boot-test-reset

每当测试套件需要重置目标板时，都会执行此脚本。 在执行第一个测试函数之前，保证至少发生一次。 如果任何测试失败，测试基础设施将再次执行此脚本以将 U-Boot 恢复到运行状态，然后再运行下一个测试功能。

该脚本可能会通过与连接到电路板重置信号的某种形式的继电器或电子开关进行通信来实现。

该脚本的语义要求当它被执行时，U-Boot将从头开始运行。 如果要测试的 U-Boot 二进制文件已写入闪存，则该脚本所需要做的就是向板的复位信号发出脉冲。 但是，在某些情况下，此脚本可能会执行其他操作。 例如，它可能会调用某些 SoC 或主板特定的供应商实用程序，以便将 U-Boot 二进制文件直接下载到 RAM 中并执行。 这将避免 u-boot-test-flash 将 U-Boot 实际写入闪存的需要，从而减少闪存芯片的磨损。


#### 2.5.1.5 Examples

<https://source.denx.de/u-boot/u-boot-test-hooks> 包含一些工作示例挂钩脚本，在为您的平台实现挂钩脚本时可能作为参考有用。 这些脚本不被视为 U-Boot 本身的一部分。


### 2.5.2 Board-type-specific configuration

每个板都有不同的配置和行为。 通过解析构建目录中的 .config 文件，可以自动检测其中许多差异。 但是，某些差异尚无法自动处理。

对于每个板，可能存在可选的 Python 模块 u_boot_board_${board_type} 来向测试脚本提供特定于板的信息。 这些模块中定义的任何全局值都可供任何测试函数使用。 这些脚本中包含的数据必须纯粹源自 U-Boot 源代码。 因此，这些配置文件也是 U-Boot 源代码树的一部分。


### 2.5.3 Execution environment configuration

每个用户的硬件设置都可以测试由特定板的 U-Boot 配置实现的不同功能子集。 例如，U-Boot 配置可能支持 USB 设备模式和 USB 海量存储，但这只能在主板和运行测试脚本的主机之间连接 USB 电缆的情况下进行测试。

对于每个板，可能存在可选的 Python 模块 u_boot_boardenv_${board_type} 和 u_boot_boardenv_${board_type}_${board_identity} 来向测试脚本提供板特定和板身份特定的信息。 这些模块中定义的任何全局值都可供任何测试函数使用。 其中包含的数据特定于特定用户的硬件配置。 因此，这些配置文件不是 U-Boot 源代码树的一部分，应安装在源代码树之外。 用户应在运行测试脚本之前设置 $PYTHONPATH 以允许加载这些模块。


### 2.5.4 Board module parameter usage

测试脚本依赖于板模块定义的以下变量：

- 目前没有


### 2.5.5 U-Boot .config feature usage

测试脚本依赖于各种 U-Boot .config 功能，或者直接为了测试这些功能，或者间接为了从正在运行的 U-Boot 实例查询信息来测试其他功能。

一个例子是 md 命令的测试需要了解用于测试的 RAM 地址。 该数据是从 bdinfo 命令的输出中解析的，因此依赖于启用的 CONFIG_CMD_BDI。

有关依赖项的完整列表，请在测试脚本中搜索以下实例：

- buildconfig.get(…
- @pytest.mark.buildconfigspec(…
- @pytest.mark.notbuildconfigspec(…


### 2.5.6 Complete invocation example

假设您已将挂钩脚本安装到 $HOME/ubtest/bin 中，并将任何所需的环境配置 Python 模块安装到 $HOME/ubtest/py 中，那么您可能会按如下方式调用测试脚本：

如果U-Boot已经构建：

```bash
PATH=$HOME/ubtest/bin:$PATH \
PYTHONPATH=${HOME}/ubtest/py/${HOSTNAME}:${PYTHONPATH} \
./test/py/test.py --bd seaboard
```

如果您希望测试脚本也为您编译 U-Boot，那么您可能需要设置 $CROSS_COMPILE 以允许这样做，并按如下方式调用测试脚本：

```bash
CROSS_COMPILE=arm-none-eabi- \
PATH=$HOME/ubtest/bin:$PATH \
PYTHONPATH=${HOME}/ubtest/py/${HOSTNAME}:${PYTHONPATH} \
./test/py/test.py --bd seaboard --build
```

或者，使用 buildman 来处理它：

```bash
PATH=$HOME/ubtest/bin:$PATH \
PYTHONPATH=${HOME}/ubtest/py/${HOSTNAME}:${PYTHONPATH} \
./test/py/test.py --bd seaboard --build --buildman
```


## 2.6 Writing tests

有关编写 pytest 测试的详细信息，请参阅 pytest 文档。 U-Boot 测试套件的具体细节如下所述。

每个测试函数都应该使用名为 u_boot_console 的测试装置。 这提供了与 U-Boot 控制台交互以及检索板和环境配置信息的方法。

函数 u_boot_console.run_command() 在 U-Boot 控制台上执行 shell 命令，并返回该命令的所有输出。 这允许验证或解释命令输出。 此函数验证 U-Boot 控制台上看不到某些字符串。 其中包括 shell 错误消息和 U-Boot 登录消息（以便检测意外的板重置）。 有关“坏”字符串的完整列表，请参阅 u_boot_console_base.py 的源代码。 一些测试场景预计会触发这些字符串。 使用 u_boot_console.disable_check() 暂时禁用对特定字符串的检查。 有关示例，请参阅 test_unknown_cmd.py。

主板和主板环境配置值可以作为 u_boot_console.config 对象的子字段进行访问，例如 u_boot_console.config.ram_base。

构建配置值（来自 .config）可以通过字典 u_boot_console.config.buildconfig 访问，其中键等于 Kconfig 变量名称。


# 3. Writing Tests

这描述了如何在 U-Boot 中编写测试并描述了可能的选项。


## 3.1 Test types

U-Boot 中有两种基本类型的测试：

- Python 测试, 在 test/py/tests;
- C 测试, 在 test/ 和 its 子目录.

(lib/efi_selftest/ 中还有 UEFI 测试，此处未考虑。)

Python 测试通过命令行与 U-Boot 对话。 它们支持沙箱和真实硬件。 它们通常不需要将测试代码构建到 U-Boot 本身中。 由于命令行界面并且有两个独立的进程，它们的运行速度相当慢。 Python 测试相当容易编写。 由于 pytest 的输出量很大，有时调试它们可能有点棘手。

C 测试直接在 U-Boot 中编写。 虽然它们可以在板上使用，但它们更常与沙箱一起使用，因为它们显然会增加 U-Boot 代码大小。 只要存在所需的设施，C 测试就很容易编写。 如果他们不这样做，则可能涉及重构或向沙箱添加新功能。 它们运行速度快且易于调试。

无论使用哪种测试类型，所有测试都由 pytest 框架收集并运行，因此通常不需要单独运行它们。 这意味着有意义时可以使用 C 测试，而无意义时可以使用 Python 测试。

下表显示了如何决定是编写 C 还是 Python 测试：

| Attribute              | C test                                     | Python test                                             |
| ---------------------- | ------------------------------------------ | ------------------------------------------------------- |
| 跑得快吗？              | Yes                                        | No (两个独立的进程)                                      |
| 容易写吗？              | Yes, 沙箱或目标系统中是否存在所需的测试功能   | Yes                                                     |
| 需要 U-Boot 中的代码吗？| Yes                                        | No, 前提是可以使用命令行执行测试并确定结果                  |
| 容易调试吗？            | Yes                                        | No, 因为无法访问 U-Boot 状态，并且输出量有时需要进行一些挖掘 |
| 可以用gdb吗？           | Yes, 直接地                                | Yes, 通过 –gdbserver                                     |
| 可以在板上运行吗？       | 有些可以，但前提是编译并且不依赖于 sandboxau | 一些可以                                                  |


## 3.2 Python or C

通常，在 U-Boot 中，我们鼓励对所有功能使用沙箱进行 C 测试。 这可以实现快速测试、轻松开发，并允许贡献者进行更改，而无需使用数十块板进行测试。

当测试需要设置或与正在运行的主机交互（例如生成图像，然后运行 U-Boot 以检查它们是否可以加载），或者无法在沙箱上运行时，应使用 Python 测试。 这些通常不应依赖于沙箱运行，而应在 U-Boot 支持的任何主板上正常运行。


## 3.3 Mixing Python and C

两全其美的方法有时是让 Python 测试进行设置并执行一些操作，然后使用“检查器”C 单元测试进行检查。 这可以通过以下步骤来实现：

在检查器测试中添加 UT_TESTF_MANUAL 标志，以便 ut 命令默认不运行它；

在名称中添加 _norun 后缀，以便 pytest 知道也跳过它；

在 Python 测试中，使用 ut 命令的 -f 标志来强制检查器测试运行它，例如：

```bash
# Do the Python part
host load ...
bootm ...

# Run the checker to make sure that everything worked
ut -f bootstd vbe_test_fixup_norun
```

请注意，除了 UT_TESTF_MANUAL 标志之外，“手动”C 测试中的代码与任何其他 C 测试一样。 它仍然使用 ut_assert…() 和其他此类构造，在本例中是为了检查 Python 测试中是否发生了预期的事情。


## 3.4 How slow are Python tests?

在底层，当在沙箱上运行时，Python 测试通过启动沙箱测试并通过管道连接到它来工作。 每次与 U-Boot 进程的交互都至少需要一次上下文切换来处理管道交互。 测试向 U-Boot 发送命令，然后 U-Boot 做出反应并显示一些输出，然后测试看到该输出并继续。 当然，在实际硬件上，通信延迟（例如使用串行控制台）会使速度变慢。

为了进行比较，请考虑检查“md”（内存转储）的测试。 以下所有时间均为近似值，是在 AMD 2950X 系统上测得的。 这是 Python 中的测试：

```bash
@pytest.mark.buildconfigspec('cmd_memory')
def test_md(u_boot_console):
    """Test that md reads memory as expected, and that memory can be modified
    using the mw command."""

    ram_base = u_boot_utils.find_ram_base(u_boot_console)
    addr = '%08x' % ram_base
    val = 'a5f09876'
    expected_response = addr + ': ' + val
    u_boot_console.run_command('mw ' + addr + ' 0 10')
    response = u_boot_console.run_command('md ' + addr + ' 10')
    assert(not (expected_response in response))
    u_boot_console.run_command('mw ' + addr + ' ' + val)
    response = u_boot_console.run_command('md ' + addr + ' 10')
    assert(expected_response in response)
```

这会运行一些命令并检查输出。 请注意，它运行命令，等待响应，然后再次检查预期的结果。 如果单独运行，大约需要 800 毫秒，包括测试收集。 对于 1000 次运行，需要 19 秒，或每次运行 19 毫秒。 当然，运行 1000 次并没有多大用处，因为我们只想运行一次。

没有完全等效的 C 测试，但这里有一个类似的测试“ms”（内存搜索）：

```bash
/* Test 'ms' command with bytes */
static int mem_test_ms_b(struct unit_test_state *uts)
{
   u8 *buf;

   buf = map_sysmem(0, BUF_SIZE + 1);
   memset(buf, '\0', BUF_SIZE);
   buf[0x0] = 0x12;
   buf[0x31] = 0x12;
   buf[0xff] = 0x12;
   buf[0x100] = 0x12;
   ut_assertok(console_record_reset_enable());
   run_command("ms.b 1 ff 12", 0);
   ut_assert_nextline("00000030: 00 12 00 00 00 00 00 00 00 00 00 00 00 00 00 00    ................");
   ut_assert_nextline("--");
   ut_assert_nextline("000000f0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 12    ................");
   ut_assert_nextline("2 matches");
   ut_assert_console_end();

   ut_asserteq(2, env_get_hex("memmatches", 0));
   ut_asserteq(0xff, env_get_hex("memaddr", 0));
   ut_asserteq(0xfe, env_get_hex("mempos", 0));

   unmap_sysmem(buf);

   return 0;
}
MEM_TEST(mem_test_ms_b, UT_TESTF_CONSOLE_REC);
```

这会直接在 U-Boot 中运行命令，然后检查控制台输出，也是直接在 U-Boot 中。 如果单独运行，则需要 100 毫秒。 对于 1000 次运行，需要 660 毫秒，即每次运行 0.66 毫秒。

因此，总体而言，单独运行 C 测试的速度可能快 8 倍，交互速度可能快 25 倍。

还应该指出的是，C 测试相当容易调试。 您可以在 do_mem_search() 上设置断点，它实现了“ms”命令，单步查看可能出现的问题等。这也可以通过 pytest 实现，但需要两个终端和 –gdbserver。


## 3.5 Why does speed matter?

许多开发活动依赖于运行测试：

- ‘git bisect run make qcheck’可用于查找失败的提交；
- 测试驱动开发依赖于构建/测试的快速迭代；
- U-Boot 的持续集成 (CI) 系统利用测试。 运行。

所有沙箱测试通常需要 90 秒，运行每个 qemu 测试大约需要 30 秒。 这与目前构建所有板所需的时间相形见绌。

随着 U-Boot 不断扩展其功能集，快速可靠的测试是提高开发人员生产力和幸福感的关键因素。


## 3.6 Writing C tests

C 测试被安排到通常由“ut”命令执行的套件中。 每个套件都有自己的文件。 本节介绍如何完成一些常见的测试任务。

(lib/efi_selftest/ 中还有 UEFI C 测试，此处未考虑。)


### 3.6.1 Add a new driver model test

在为新的或现有的 uclass 添加测试、向 uclass 添加新的操作或功能、添加新的 ofnode 或 dev_read_() 函数或与驱动程序模型相关的任何其他内容时使用此选项。

为您的测试找到一个合适的位置，可能靠近现有代码中的其他测试函数，或者在新文件中。 每个 uclass 应该有自己的测试文件。

声明测试：

```bash
/* Test that ... */
static int dm_test_uclassname_what(struct unit_test_state *uts)
{
   /* test code here */

   return 0;
}
DM_TEST(dm_test_uclassname_what, UT_TESTF_SCAN_FDT);
```

如果适用，请将“uclassname”替换为您的 uclass 名称。 将“内容”替换为您正在测试的内容。

DM_TEST() 的标志在 test/test.h 中定义，您通常需要 UT_TESTF_SCAN_FDT，以便扫描设备树并绑定所有设备并准备使用。 DM_TEST 宏自动添加 UT_TESTF_DM，以便测试运行者知道这是一个驱动程序模型测试。

驱动程序模型测试的特殊之处在于，每次测试都会重新创建整个驱动程序模型状态。 例如，这可以确保如果先前的测试删除了设备，则不会影响后续测试。 驱动程序模型测试还使用 livetree 和 flattree 运行，以确保两个 devicetree 实现按预期工作。

示例提交: c48cb7ebfb4 (“sandbox: 添加 ADC 单元测试”) [1]

[1] <https://gitlab.denx.de/u-boot/u-boot/-/commit/c48cb7ebfb4>


### 3.6.2 Add a C test to an existing suite

当您添加或修改驱动程序模型之外的现有功能时，请使用此功能。 一个例子是 bloblist。

在与套件其余部分相同的文件中添加一个新函数，并将其注册到套件中。 例如，要添加新的 mem_search 测试：

```bash
/* Test 'ms' command with 32-bit values */
static int mem_test_ms_new_thing(struct unit_test_state *uts)
{
      /* test code here*/

      return 0;
}
MEM_TEST(mem_test_ms_new_thing, UT_TESTF_CONSOLE_REC);
```

请注意，MEM_TEST() 宏是在文件顶部定义的。

示例提交: 9fe064646d2 (“bloblist: 支持搬迁到更大的空间”) [1]

[1] <https://gitlab.denx.de/u-boot/u-boot/-/commit/9fe064646d2>


### 3.6.3 Add a new test suite

每个套件都应该专注于一个功能或子系统，因此如果您正在编写其中的新功能或子系统，则应该添加一个新套件。

在 test/ 或子目录中创建一个新文件，并定义一个宏来注册套件。 例如：

```bash
#include <common.h>
#include <console.h>
#include <mapmem.h>
#include <dm/test.h>
#include <test/ut.h>

/* Declare a new wibble test */
#define WIBBLE_TEST(_name, _flags)   UNIT_TEST(_name, _flags, wibble_test)

/* Tetss go here */

/* At the bottom of the file: */

int do_ut_wibble(struct cmd_tbl *cmdtp, int flag, int argc, char *const argv[])
{
  struct unit_test *tests = UNIT_TEST_SUITE_START(wibble_test);
  const int n_ents = UNIT_TEST_SUITE_COUNT(wibble_test);

  return cmd_ut_category("cmd_wibble", "wibble_test_", tests, n_ents, argc, argv);
}
```

然后按照上面的方法添加新的测试。

通过添加到 cmd_ut_sub[] 来在 test/cmd_ut.c 中注册这个新套件：

```bash
/* Within cmd_ut_sub[]... */

U_BOOT_CMD_MKENT(wibble, CONFIG_SYS_MAXARGS, 1, do_ut_wibble, "", ""),
```

并向 ut_help_text[] 添加新帮助：

```bash
"ut wibble - Test the wibble feature\n"
```

如果您的功能以特定 Kconfig 为条件，那么您可以使用 #ifdef 来控制它。

最后，通过添加到同一目录中的 Makefile 将测试添加到构建中：

```bash
obj-$(CONFIG_$(SPL_)CMDLINE) += wibble.o
```

请注意，CMDLINE 在 SPL 中从未启用，因此此测试仅出现在 U-Boot 中。 请参阅下文了解如何进行 SPL 测试。

和以前一样，如果需要，您可以添加额外的 Kconfig 检查：

```bash
ifneq ($(CONFIG_$(SPL_)WIBBLE),)
obj-$(CONFIG_$(SPL_)CMDLINE) += wibble.o
endif
```

示例提交: 919e7a8fb64 (“test: 为 bloblist 添加一个简单的测试”) [1]

[1] <https://gitlab.denx.de/u-boot/u-boot/-/commit/919e7a8fb64>


### 3.6.4 Making the test run from pytest

所有 C 测试都必须从 pytest 运行。 通常这是自动的，因为 pytest 会扫描 U-Boot 可执行文件以查找要运行的可用测试。 只要您的测试套件有一个“ut”子命令，它就会运行。 这同样适用于驱动程序模型测试，因为它们使用“ut dm”子命令。

请参阅 test/py/tests/test_ut.py 了解单元测试的运行方式。


### 3.6.5 Add a C test for SPL

注意：C 测试目前仅适用于 sandbox_spl。 目前，其他板卡中没有针对现有 SPL 测试的机制，即使它们内置于映像中也是如此。

SPL 测试无法从“ut”命令运行，因为 SPL 中没有可用的命令。 相反，当给出 -u 标志时，沙箱（仅）在启动时调用 ut_run_list() 。 这将运行可用的单元测试，无论它们位于哪个套件中。

要创建新的 SPL 测试，请遵循与上述相同的规则，添加到现有套件或创建新套件。

SPL 测试的一个示例是 spl_test_load()。


## 3.7 Writing Python tests

请参阅 [U-Boot pytest suite](#2-u-boot-pytest-suite) 了解如何编写 Python 测试的简要说明。 您应该能够使用 test/py/tests 中的现有测试作为示例。


# 4. Sandbox tests

## 4.1 Test Design

大多数 uclass 和 U-Boot 的许多函数都有沙箱测试。 这允许在开发人员友好的环境中检查大部分代码。

沙箱提供了一种编写和运行单元测试的方法。 单元测试的传统方法是构建许多小的可执行文件，每个测试或测试类别一个。 使用沙箱，所有测试尽可能共享少量可执行文件（例如沙箱的“u-boot”，sandbox_spl的“u-boot-spl”和“u-boot”），并且可以非常快速地运行。 绝大多数测试可以在“沙箱”构建上运行。


## 4.2 可用的测试

一些可用的测试是：

- command_ut: 命令解析和处理的单元测试

- compression: U-Boot 压缩算法的单元测试，对于安全检查很有用。 它支持 gzip、bzip2、lzma 和 lzo。

- image: 镜像的单元测试:
    - test/image/test-imagetools.sh - 多文件镜像
    - test/py/tests/test-fit.py - FIT 镜像

- tracing: test/trace/test-trace.sh 测试追踪系统 (see README.trace)

- 验证 boot: test/py/tests/test_vboot.py

如果您更改或增强任何 U-Boot 子系统，您应该编写或扩展测试并将其包含在您的补丁系列提交中。 U-Boot 的一些较旧区域的测试覆盖范围仍然有限，我们需要努力改进它。

请注意，其中许多测试都是作为命令实现的，如果需要（并启用），您可以在板上本机运行这些命令。

要运行所有测试，请使用“make check”。


## 4.3 直接运行沙箱测试

通常，测试是使用 pytest 套件运行的。 在沙箱上运行 pytest 很简单，而且总是能正确完成任务。 例如，某些测试需要先设置文件才能工作。

但也可以直接运行一些沙箱测试。 例如，这将运行 dm_test_gpio() 测试，您可以在 test/dm/gpio.c 中找到该测试：

```bash
$ ./u-boot -T -c "ut dm gpio"


U-Boot 2021.01

Model: sandbox
DRAM:  128 MiB
WDT:   Started with servicing (60s timeout)
MMC:   mmc2: 2 (SD), mmc1: 1 (SD), mmc0: 0 (SD)
In:    serial
Out:   vidconsole
Err:   vidconsole
Model: sandbox
SCSI:
Net:   eth0: eth@10002000, eth5: eth@10003000, eth3: sbe5, eth6: eth@10004000
Test: dm_test_gpio: gpio.c
Test: dm_test_gpio: gpio.c (flat tree)
Failures: 0
```

-T 选项告诉 U-Boot 沙箱使用“测试”设备树 (test.dts) 运行，而不是选择普通 sandbox.dts 的 -D - 这是必要的，因为许多测试依赖于测试设备树中的节点或属性 。 如果您尝试在没有 -T 的情况下运行测试，那么您可能会看到失败，例如：

```bash
$ ./u-boot -c "ut dm gpio"


U-Boot 2021.01

DRAM:  128 MiB
WDT:   Not found!
MMC:
In:    serial
Out:   serial
Err:   serial
SCSI:
Net:   No ethernet found.
Please run with test device tree:
    ./u-boot -d arch/sandbox/dts/test.dtb
Test: dm_test_gpio: gpio.c
test/dm/gpio.c:37, dm_test_gpio(): 0 == gpio_lookup_name("b4", &dev, &offset, &gpio): Expected 0x0 (0), got 0xffffffea (-22)
Test: dm_test_gpio: gpio.c (flat tree)
test/dm/gpio.c:37, dm_test_gpio(): 0 == gpio_lookup_name("b4", &dev, &offset, &gpio): Expected 0x0 (0), got 0xffffffea (-22)
Failures: 2
```

如果您忘记使用 -T 标志，上面的消息应该会提供提示。 即使使用 -D 运行也会产生不同的结果。

您可以轻松地在这些测试中使用 gdb，而不需要 –gdbserver：

```bash
$ gdb --args u-boot -T -c "ut dm gpio"
...
(gdb) break dm_test_gpio
Breakpoint 1 at 0x1415bd: file test/dm/gpio.c, line 37.
(gdb) run -T -c "ut dm gpio"
Starting program: u-boot -T -c "ut dm gpio"
Test: dm_test_gpio: gpio.c

Breakpoint 1, dm_test_gpio (uts=0x5555558029a0 <global_dm_test_state>)
    at files/test/dm/gpio.c:37
37           ut_assertok(gpio_lookup_name("b4", &dev, &offset, &gpio));
(gdb)
```

然后，您可以根据需要单步执行并查看变量。


## 4.4 多次运行测试

有些测试可能存在竞争条件，这些条件很难在单个测试中检测到。 在进行下一个测试之前，可以使用“-r”标志多次运行每个单独的测试。

这在运行单个测试时最有用，因为多次运行所有测试可能需要一段时间。

例如：

```bash
=> ut dm -r1000 dm_test_rtc_set_get
...
Test: dm_test_rtc_set_get: rtc.c (flat tree)
Test: dm_test_rtc_set_get: rtc.c
test/dm/rtc.c:257, dm_test_rtc_reset(): old_base_time == base_time: Expected 0x62e7453c (1659323708), got 0x62e7453d (1659323709)
Test: dm_test_rtc_set_get: rtc.c (flat tree)
Test: dm_test_rtc_set_get: rtc.c
Test: dm_test_rtc_set_get: rtc.c (flat tree)
...
Test dm_test_rtc_reset failed 3 times
```


## 4.5 隔离一个会破坏另一个测试的测试

运行单元测试时，某些测试可能会产生副作用，导致后续测试中断。 当使用“ut dm”或类似的命令时，有时会出现这种情况。

您可以使用 ut 命令的 -I 参数来隔离此问题。 首先使用 ut info 查看有多少个测试，然后使用二分搜索来定位问题。 请注意，您可能需要在每次迭代后重新启动 U-Boot，因此 U-Boot 的 -c 参数很有用。

例如，让我们保持 dm_test_host() 失败：

```bash
=> ut dm
...
Test: dm_test_get_stats: core.c
Test: dm_test_get_stats: core.c (flat tree)
Test: dm_test_host: host.c
test/dm/host.c:71, dm_test_host(): 0 == ut_check_delta(mem_start): Expected 0x0 (0), got 0xffffcbb0 (-13392)
Test: dm_test_host: host.c (flat tree)
Test <NULL> failed 1 times
Test: dm_test_host_dup: host.c
Test: dm_test_host_dup: host.c (flat tree)
...
```

然后，您可以告诉 U-Boot 在序列中的不同点运行失败的测试：

=> ut info Test suites: 21 Total tests: 645

```bash
$ ./u-boot -T -c "ut dm -I300:dm_test_host"
...
Test: dm_test_pinctrl_single: pinmux.c (flat tree)
Test: dm_test_host: host.c
test/dm/host.c:71, dm_test_host(): 0 == ut_check_delta(mem_start): Expected 0x0 (0), got 0xfffffdb0 (-592)
Test: dm_test_host: host.c (flat tree)
Test dm_test_host failed 1 times (position 300)
Failures: 4
```

所以它发生在位置 300 之前。尝试 150 显示失败，所以我们尝试 75：

```bash
$ ./u-boot  -T  -c "ut dm -I75:dm_test_host"
...
Test: dm_test_autoprobe: core.c
Test: dm_test_autoprobe: core.c (flat tree)
Test: dm_test_host: host.c
Test: dm_test_host: host.c (flat tree)
Failures: 0
```

成功了，所以我们尝试 120，等等，直到最终我们发现问题首先发生在位置 82。

```bash
$ ./u-boot -T -c “ut dm -I82:dm_test_host” … Test: dm_test_blk_flags: blk.c Test: dm_test_blk_flags: blk.c (flat tree) Test: dm_test_host: host.c test/dm/host.c:71, dm_test_host(): 0 == ut_check_delta(mem_start): Expected 0x0 (0), got 0xffffc960 (-13984) Test: dm_test_host: host.c (flat tree) Test dm_test_host failed 1 times (position 82) Failures: 1
```

由此我们可以推断出 dm_test_blk_flags() 导致了 dm_test_host() 的问题。


## 4.6 直接运行 sandbox_spl 测试

SPL 是 U-Boot 正常之前的阶段。 它存在于 sandbox_spl 构建中，因此您可以像这样运行 SPL：

```bash
./spl/u-boot-spl
```

SPL 测试的特殊之处在于，如果给出 -u 标志，它们就会运行（当然，仅在 SPL 阶段）：

```bash
./spl/u-boot-spl -u

U-Boot SPL 2021.01-00723-g43c77b51be5-dirty (Jan 24 2021 - 16:38:24 -0700)
Running 5 driver model tests
Test: dm_test_of_plat_base: of_platdata.c (flat tree)
Test: dm_test_of_plat_dev: of_platdata.c (flat tree)
Test: dm_test_of_plat_parent: of_platdata.c (flat tree)
Test: dm_test_of_plat_phandle: of_platdata.c (flat tree)
Test: dm_test_of_plat_props: of_platdata.c (flat tree)
Failures: 0


U-Boot 2021.01-00723-g43c77b51be5-dirty (Jan 24 2021 - 16:38:24 -0700)

DRAM:  128 MiB
...
```

不可能在 U-Boot 中运行 SPL 测试，首先是因为它们没有内置到 U-Boot 中，其次是因为环境非常不同，例如 一些 SPL 测试依赖于仅在 SPL 中可用的 of-platdata。

请注意，运行后，SPL 会继续正常启动到 U-Boot。 您可以添加“-c exit”以使 U-Boot 退出而不做任何进一步的操作。 目前无法运行 SPL 测试然后停止，因为 pytest 要求 U-Boot 生成预期的横幅。

您可以使用 -k 标志来选择运行哪些测试：

```bash
./spl/u-boot-spl -u -k dm_test_of_plat_parent
```

当然，您可以将 gdb 与 sandbox_spl 一起使用，就像使用沙箱一样。


## 4.7 直接运行所有测试

运行所有沙箱测试的快速方法是：

```bash
./u-boot -T -c "ut all"
```

在 2021 年的硬件上，它通常会在 6 秒内运行单线程，其中 2 秒是时间测试中的延迟。

这不应被视为“make check”的替代品，但对 git bisect 等很有帮助。


## 4.8 内置了哪些测试？

无论使用什么沙箱构建，存在哪些测试取决于构建哪些源文件。 对于 sandbox_spl，由于 test/dm/Makefile 中的构建规则，构建了 of_platdata 测试：

```bash
ifeq ($(CONFIG_SPL_BUILD),y)
obj-$(CONFIG_SPL_OF_PLATDATA) += of_platdata.o
else
...other tests for non-spl
endif
```

您可以通过查找 linker_list 来获取 U-Boot ELF 文件中的测试列表：

```bash
$ nm /tmp/b/sandbox_spl/spl/u-boot-spl |grep 2_dm_test
000000000001f200 D _u_boot_list_2_dm_test_2_dm_test_of_plat_base
000000000001f220 D _u_boot_list_2_dm_test_2_dm_test_of_plat_dev
000000000001f240 D _u_boot_list_2_dm_test_2_dm_test_of_plat_parent
000000000001f260 D _u_boot_list_2_dm_test_2_dm_test_of_plat_phandle
000000000001f280 D _u_boot_list_2_dm_test_2_dm_test_of_plat_props
```


## 4.9 编写测试

查看 [Writing Tests](#3-writing-tests) 了解如何编写新测试。
