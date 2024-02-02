
# Semihosting

半主机是 ARM 让真实或虚拟目标与主机或主机调试器进行通信以进行基本操作（例如文件 I/O、控制台 I/O 等）的方式。请参阅 [Arm 的半主机文档](https://developer.arm.com/documentation/100863/latest/)）了解更多信息。


# 1. Platform Support

## 1.1 Versatile Express

对于在 armv8 虚拟 fastmodel 平台上进行开发，半主机是一个很有价值的工具，因为它允许在 eMMC 或其他 NV 介质可用之前访问映像/配置文件。

有两种主要的 ARM 虚拟固定虚拟平台 (FVP) 模型：[Versatile Express (VE) FVP 和 BASE FVP](http://www.arm.com/products/tools/models/fast-models/foundation-model.php)。 此处创建的初始 vexpress64 U-Boot 板使用免许可的 Foundation_v8 模拟器在 VE 虚拟平台上运行。 幸运的是，Foundation_v8 模拟器还支持 BASE_FVP 模型，公司可以购买该模型的许可证并包含更多功能。 因此，我们可以在 U-Boot 中通过使用 VE FVP（默认）运行任一模型，或者打开 CONFIG_BASE_FVP 以获得功能更齐全的模型。

不要创建类似于 armltd/vexpress64 的新 armv8 板，而是向现有板添加半主机调用，并通过 CONFIG_SEMIHOSTING 和 CONFIG_BASE_FVP 集启用。 还可以重用现有的板配置文件 vexpress_aemv8.h，但通过 CONFIG_BASE_FVP 的存在或不存在来区分这两个模型。 此更改已在 Foundation 和 Base fastmodel 模拟器上进行了测试并有效。


## 1.2 QEMU

另一个支持半主机的 ARM 模拟器是 [QEMU](https://www.qemu.org/)。 要启用半主机，请在配置 U-Boot 时启用 CONFIG_SERIAL_PROBE_ALL，并在调用 QEMU 时使用 -semihosting。 添加 -ngraphic 也很有帮助。 当使用半主机串行控制台时，QEMU 将阻塞等待输入。 这将导致 GUI 变得无响应。 为了缓解这种情况，请尝试添加 -ngraphic。 有关构建和运行 QEMU 的更多信息，请参阅 [开发板文档](https://docs.u-boot.org/en/latest/board/emulation/qemu-arm.html)。


## 1.3 OpenOCD

任何 ARM 平台都可以使用带有附加调试器的半主机。 [OpenOCD](https://openocd.org/) 就是这样一种调试器，它可以很好地支持各种板和 JTAG 适配器。 默认情况下不启用半主机，因此您需要启用它：

```bash
$ openocd -f <your board config> -c init -c halt -c \
      'arm semihosting enable' -c resume
```

请注意，启用半主机只能在使用 init 连接到主板后完成，并且必须在 CPU 停止时完成。 有关更扩展的示例，请参阅 [LS1046ARDB 文档](https://docs.u-boot.org/en/latest/board/nxp/ls1046ardb.html#ls1046ardb-jtag).


# 2. Loading files

半主机代码添加了一个“半主机文件系统”：

```bash
load hostfs - <address> <image>
```

这会将图像从主机文件系统加载到 RAM 的指定地址。 如果您使用的是 U-Boot SPL，则还可以使用 BOOT_DEVICE_SMH，它将加载 CONFIG_SPL_FS_LOAD_PAYLOAD_NAME。


# 3. Host console

通过启用 CONFIG_SERIAL_SEMIHOSTING，U-Boot 可以使用主机的控制台而不是物理串行设备。 如果您没有启用 CONFIG_DM_SERIAL，请确保禁用任何其他串行驱动程序。


# 4. Migrating from smhload

如果您使用 smhload 命令，则可以迁移如下命令：

```bash
smhload <file> <address> [<end var>]
```

到通用加载命令，例如：

```bash
load hostfs - <address> <file>
```

load 命令将使用文件的大小设置 filesize 变量。 fdt selected 命令已更新为采用大小而不是结束地址。 如果您将 initramfs 添加到设备树中，如下所示：

```bash
fdt chosen <address> <end var>
```

你现在可以运行：

```bash
fdt chosen <address> $filesize
```
