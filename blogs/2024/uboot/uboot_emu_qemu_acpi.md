
# ACPI on QEMU

QEMU 可以在 ARM、RISC-V（自 QEMU v8.0.0 起）和 x86 上提供 ACPI 表。

ACPI 支持需要以下 U-Boot 设置：

```bash
CONFIG_CMD_QFW=y
CONFIG_ACPI=y
CONFIG_GENERATE_ACPI_TABLE=y
```

在 x86 上，这些设置已包含在 defconfig 文件中。 ARM 和 RISC-V 默认使用设备树。

您可以将配置片段 acpi.config 添加到 make 命令中以初始化配置，而不是手动更新配置。 例如。

```bash
make qemu-riscv64_smode_defconfig acpi.config
```
