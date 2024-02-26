
# Tables

- [1. 简介](#1-introduction)
- [2. 设备树控制](#2-devicetree-control-in-u-boot)
- [3. Qemu设备树](#3-devicetree-in-qemu)


# 1. Introduction

U-Boot 使用设备树进行配置。 这包括开发板使用的设备、使用 binman 创建的映像的格式、用于控制台的 UART、用于安全启动的公钥以及许多其他内容。


## 1.1 为什么 U-Boot 将 <thing> 放入设备树中？

对于刚接触 U-Boot 的人来说，这个问题经常出现，特别是那些来自 Linux 的人，他们习惯于对设备树中的内容有非常严格的规则。

U-Boot 使用与 Linux 相同的设备树，但添加了引导加载程序环境所需的更多内容 (查看 [Adding tweaks for U-Boot](https://docs.u-boot.org/en/latest/develop/devicetree/control.html#dttweaks)).

U-Boot 没有用户空间来提供策略和配置。 它无法执行 Linux 所做的操作，即运行程序并查找文件系统来确定如何启动。 因此配置和运行时信息会进入 U-Boot 中的设备树。

当然可以：

- 将表添加到U-Boot二进制文件的rodata部分；
- 以不同的格式将一些信息附加到U-Boot的末尾；
- 修改链接描述文件以引入包含一些信息的文件；
- 将东西放入ACPI表中；
- 链接到 UEFI 切换块结构中并将内容放入其中。

但请不要。 一般来说，设备树与保存 U-Boot 配置的位置相同。

所以，请不要问为什么 U-Boot 将 <thing> 放入设备树中。 这是它唯一可以去的地方。 它是一种非常适合 U-Boot 在运行时需要了解的任何内容的数据结构。

请注意，可以直接使用 platdata，这样驱动程序就可以避免 SPL 中的 devicetreee。 但 of-platdata 是避免设备树开销的现代方法，因此请改用它。


# 2. Devicetree Control in U-Boot

此功能通过扁平设备树 (fdt) 提供 U-Boot 的运行时配置。

此功能旨在使单个 U-Boot 二进制文件能够支持多个板，每个板的精确配置由扁平设备树 (fdt) 控制。 这是 Linux 内核针对 ARM 和 RISC-V 所采用的方法，并且 PowerPC 已经使用了一段时间。

fdt 是实现运行时配置的便捷工具，原因有以下三个：

- fdt 已经有了优秀的基础设施：编译器检查文本文件并将其转换为紧凑的二进制格式，并且 U-Boot (libfdt) 中已经提供了一个库来处理这种格式；
- 它是可扩展的，因为它由良好的分层格式的节点和属性组成；
- 增量阅读是相当有效的。

arch/<arch>/dts 目录包含一个 Makefile，用于构建 devicetree blob 并将其嵌入到 U-Boot 映像中。 这很有用，因为它允许 U-Boot 根据它在那里找到的内容来配置自身。 如果您有许多具有不同外设的类似板，您可以在 devicetree 文件中描述每个板的功能，并拥有一个通用源库。

要启用此功能，请将 CONFIG_OF_CONTROL 添加到您的主板配置文件中。


## 2.1 什么是扁平化设备树？

fdt 可以以源格式指定为文本文件。 要了解 fdt 语法，请查看规范 ([dtspec](https://www.devicetree.org/specifications/))。

还有一个针对编译器和相关工具的邮件列表 ([dtlist](https://www.spinics.net/lists/devicetree-compiler/))。

如果您想知道，OF 代表开放固件。 这遵循 Linux 中使用的约定。


## 2.2 工具

为了创建扁平化的设备树，使用设备树编译器。 这是由 U-Boot 自动提供的。 如果您有 dtc 的系统版本（通常位于“device-tree-compiler”包中），则当前未使用该系统版本。

如果你想构建自己的 dtc，它保存在这里：

```bash
git://git.kernel.org/pub/scm/utils/dtc/dtc.git
```

您可以使用以下方法解码二进制文件：

```bash
dtc -I dtb -O dts <filename.dtb>
```

该存储库还包括用于读取和写入二进制文件中的属性的 fdtget/fdtput。 U-Boot 添加了自己的 fdtgrep 用于创建文件的子集。


## 2.3 从哪里可以获得我的主板的 devicetree 文件？

您可能会发现 Linux 内核有一个合适的文件。 查看 arch/<arch>/boot/dts 中的内核源代码。

如果没有，您可能会找到其他具有合适文件的板，您可以根据需要进行修改。 在板目录中查找扩展名为 .dts 的文件。

如果做不到这一点，您可以自己从头开始编写一个！


## 2.4 配置

使用:

```c
#define CONFIG_DEFAULT_DEVICE_TREE   "<name>"
```

设置 devicetree 源的文件名。 然后将您的 devicetree 文件放入：

```bash
arch/<arch>/dts/<name>.dts
```

这应该包括您的 CPU 或 SOC 的 devicetree 文件，放置在 arch/<arch>/dts 中，然后使用您的主板的 u-boot-dtsi 文件进行所需的任何调整。

如果定义了 CONFIG_OF_EMBED，那么它将被拾取并构建到 U-Boot 映像（包括 u-boot.bin）中。 这仅适用于调试和开发，不建议用于生产设备。

如果定义了 CONFIG_OF_SEPARATE，那么它将被构建并与 u-boot-nodtb.bin 一起放置在 u-boot.dtb 文件中，组合结果放置在 u-boot.bin 中，这样您仍然可以只刷新 u-boot.bin 到你的主板上。 如果您使用 CONFIG_SPL_FRAMEWORK，则将构建 u-boot.img 以包含设备树二进制文件。

如果定义了 CONFIG_OF_BOARD，则特定于板的例程将在运行时提供设备树，例如，如果较早的引导加载程序阶段创建它并将其传递给 U-Boot。

如果定义了 CONFIG_BLOBLIST，则设备树可能来自前一阶段传递的 bloblist（如果存在）。

如果定义了 CONFIG_SANDBOX，那么它将在启动时从文件中读取。 使用 U-Boot 的 -d 标志指定要读取的文件，-D 表示默认文件，-T 表示测试设备树，用于运行沙箱单元测试。

您不能同时使用多个选项。

要使用您自己编译的 devicetree 文件，请将 EXT_DTB=<filename> 传递给“make”，如下所示：

```bash
make EXT_DTB=boot/am335x-boneblack-pubkey.dtb
```

然后U-Boot会将该文件复制到u-boot.dtb，如果使用的话将其放入.img文件中，以及u-boot-dtb.bin中。

如果您希望将 fdt 放在内存中的不同地址，可以定义“fdtcontroladdr”环境变量。 这是 fdt 二进制 blob 的十六进制地址，并将覆盖任一选项。 请注意，当只有编译环境可用时，会在重定位之前检查此环境变量。 因此，例如，无法在保存的 SPI/NAND 闪存环境中定义此变量（它将被忽略）。 重定位后，该变量将被设置为新重定位的 fdt blob 的地址。 它是只读的，无法更改。 它可以选择性地用于通过 bootm/bootz 命令控制 Linux 的启动过程。

要使用它，请将类似的内容放入您的开发板头文件中：

```c
#define CFG_EXTRA_ENV_SETTINGS       "fdtcontroladdr=10000\0"
```

构建:

板卡配置完成后，可以通过两种方式构建fdt支持的u-boot：

构建从 CONFIG_DEFAULT_DEVICE_TREE 定义的默认 dts：

```bash
$ make
```

构建用户指定的 dts 文件：

```bash
$ make DEVICE_TREE=<dts-file-name>
```


## 2.5 为 U-Boot 添加调整

强烈建议 U-Boot 中的 devicetree 文件与 Linux 中的 devicetree 文件完全相同，以便随时同步它们。

U-Boot 当然是一个与 Linux 非常不同的项目，例如 它在更严格的内存和代码大小限制下运行。 Linux 可能使用具有通用时钟格式 (CCF) 的完整时钟驱动程序来查找 UART 的输入时钟，而 U-Boot 通常希望在运行太多代码之前尽早输出横幅。

第二个区别是 U-Boot 包含不同的阶段。 对于 SPL，约束更加极端，设备树被缩小以删除不需要的节点，甚至转换为 C 代码以避免访问开销。

U-Boot 自动查找并包含一个包含主板标准设备树更新的文件，在与主文件相同的目录中搜索它们，按以下顺序：

```bash
<orig_filename>-u-boot.dtsi
<CONFIG_SYS_SOC>-u-boot.dtsi
<CONFIG_SYS_CPU>-u-boot.dtsi
<CONFIG_SYS_VENDOR>-u-boot.dtsi
u-boot.dtsi
```

仅选择其中之一，但当然您可以在该文件中 #include 另一个，以创建共享文件的层次结构。


## 2.6 外部 .dtsi 片段

除了描述当前的硬件之外，U-Boot 还使用其控制 dtb 来实现各种配置目的。 例如，用于验证启动的公钥以特定格式嵌入在 /signature 节点中。

如上所述，U-Boot 构建系统会自动包含一个 *-u-boot.dtsi 文件（如果找到），其中包含 U-Boot 特定的怪癖。 但是，某些数据（例如提到的公钥）不适合上游 U-Boot，但最好在 U-Boot 存储库之外保存和维护。 您可以使用 CONFIG_DEVICE_TREE_INCLUDES 指定 .dtsi 文件列表，在构建 .dtb 文件时也将包含这些文件。


## 2.7 重定向, SPL 和 TPL

U-Boot 可以分为三个阶段：TPL、SPL 和U-Boot 本身。

完整的设备树可用于 U-Boot 本身，但通常只有一个子集（或根本没有）可用于 TPL 和 SPL。 有关更多详细信息，请参阅 doc/driver-model/design.rst 中的“预重定位支持”和“SPL 支持”。


## 2.8 在 SPL 中使用多个 DTB (CONFIG_SPL_MULTI_DTB)

在某些罕见的情况下，希望 SPL 能够从众多 DTB 中选择一个。 这通常不是很有用，因为 SPL 的 DTB 很小并且通常适合多个平台。 然而，DTB 有时包含在多个平台上工作的信息（例如 IO 调整参数）。 在这种情况下，可以使用 CONFIG_SPL_MULTI_DTB。 此选项将包含 SPL_OF_LIST 中列出的多个 DTB 的 FIT 映像附加到 SPL。 调用 board_fit_config_name_match() 来选择正确的 DTB。

如果 board_fit_config_name_match() 依赖于 DM（例如 DM 驱动程序访问包含板 ID 的 EEPROM），则可以从通用 DTB 开始，然后在检测后切换到正确的 DTB。 为此，平台代码必须调用 fdtdec_resetup()。 根据返回的标志，平台可能必须使用 dm_uninit() 和 dm_init_and_scan() 重新初始化 DM 子系统。


## 2.9 局限性

Devicetree 有助于降低支持使用相同 SOC/CPU 的主板变体的复杂性。

然而，U-Boot 旨在针对单一架构类型和 CPU 类型进行构建。 例如，不可能构建在 AT91 和 OMAP 板上运行的单个 ARM 二进制文件，依赖于 fdt 来配置各种功能。 这是因为您必须在构建时选择 arch/arm/cpu/arm926ejs（omap 或 at91）中的 CPU 系列之一。 同样，U-Boot 无法针对多种 cpu 类型或架构构建。

重要的是要了解 fdt 仅选择平台/驱动程序中可用的选项。 它还不能添加新的驱动程序。 因此，您仍然必须具有 CONFIG 选项才能启用驱动程序。 例如，您需要定义 CONFIG_SYS_NS16550 来引入 NS16550 驱动程序，但可以使用 fdt 来指定 UART 时钟、外设地址等。从广义上讲，CONFIG 选项通常控制引入哪些驱动程序文件，并且 fdt 控制这些文件的工作方式。


## 2.10 历史

U-Boot 配置之前是使用板配置文件中的 CONFIG 选项完成的。 这最终在近 10,000 个选项中失控。

U-Boot 与 Linux 大约在同一时间采用了 devicetree，早期的主板在 Linux 之前就使用了它（例如 Snow）。 这两个项目是并行开发的，某些板的绑定仍然存在一些差异。 虽然有人讨论过为 devicetree 文件建立一个单独的存储库，但实际上 Linux 内核 Git 存储库已成为存储这些文件的地方，U-Boot 会获取副本并使用 u-boot.dtsi 文件添加调整。


# 3. Devicetree in QEMU

对于 ARM、RISC-V 和一个 PPC 目标上的 QEMU，设备树由 QEMU 即时创建。 它旨在用于 Linux，但也可以由 U-Boot 使用，只要合并 U-Boot 所需的任何节点/属性即可。

当 CONFIG_OF_BOARD 启用时。


## 3.1 获取QEMU设备树

当 QEMU 生成自己的 devicetree 并传递给 U-Boot 时，您可以使用 -dtb u-boot.dtb 强制 QEMU 使用 U-Boot 的树内版本。

要获取 qemu 生成的设备树，请添加 -machine dumpdtb=qemu.dtb，例如：

```bash
qemu-system-arm -machine virt -machine dumpdtb=qemu.dtb

qemu-system-aarch64 -machine virt -machine dumpdtb=qemu.dtb

qemu-system-riscv64 -machine virt -machine dumpdtb=qemu.dtb
```


## 3.2 合并到 U-Boot 节点/属性中

各种 U-Boot 功能需要 U-Boot 设备树中的节点和属性，但目前 QEMU 不知道这些。 要使用它们，您必须手动合并适当的部分。

一种方法是使用 dtc。 此命令依次对每个 .dtb 文件运行 dtc，以生成文本文件。 它会删除 qemu 上的重复标头。 然后它将它们连接起来并通过 dtc 运行它们以编译输出：

```bash
qemu-system-arm -machine virt -machine dumpdtb=qemu.dtb
cat  <(dtc -I dtb qemu.dtb) <(dtc -I dtb u-boot.dtb | grep -v /dts-v1/) | dtc - -o merged.dtb
```

然后，您可以使用合并的设备树运行 qemu，例如：

```bash
qemu-system-arm -machine virt -nographic -bios u-boot.bin -dtb merged.dtb
```

请注意，某些版本的 qemu 中似乎存在一个错误，其中 dumpdtb 的输出与提供给 U-Boot 的输出不太匹配。
