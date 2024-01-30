
# Remote Processor Framework

# 1. Introduction

本文介绍了各种片上系统 (SoC) 上的远程处理器的驱动程序模型。 术语远程处理器用于指示这不是 U-Boot 正在其上运行的处理器，而是可能由我们运行的处理器控制的另一个处理实体。

简化模型依赖于单个 UCLASS - UCLASS_REMOTEPROC

**UCLASS_REMOTEPROC:**

- drivers/remoteproc/rproc-uclass.c
- include/remoteproc.h

**Commands:**

- common/cmd_remoteproc.c

**Configuration:**

- CONFIG_REMOTEPROC 由驱动程序根据需要选择。
- 如果需要，命令的 CONFIG_CMD_REMOTEPROC。


# 2. How does it work - The driver

总的来说，驱动程序状态机转换通常如下：

```bash
        (entry)
        +-------+
    +---+ init  |
    |   |       | <---------------------+
    |   +-------+                       |
    |                                   |
    |                                   |
    |   +--------+                      |
Load|   |  reset |                      |
    |   |        | <----------+         |
    |   +--------+            |         |
    |        |Load            |         |
    |        |                |         |
    |   +----v----+   reset   |         |
    +-> |         |    (opt)  |         |
        |  Loaded +-----------+         |
        |         |                     |
        +----+----+                     |
             | Start                    |
         +---v-----+        (opt)       |
      +->| Running |        Stop        |
Ping  +- |         +--------------------+
(opt)    +---------+
```

（is_running 不改变状态） opt：由驱动程序实现的可选状态转换。

注意：状态机的确切行为取决于远程处理器，remoteproc 核心无意实现状态机逻辑。 某些处理器可能允许启动/停止而无需在中间重新加载图像，某些其他处理器可能只允许我们启动处理器（来自 EEPROM/OTP 的图像）等。

因此，驱动程序有责任根据需要处理设备所需的状态转换。

基本设计假设：

远程处理器可以对可能加载并从复位释放的特定固件进行操作。

驱动程序遵循标准 UCLASS DM。

以最低限度的形式：

```c
static const struct dm_rproc_ops sandbox_testproc_ops = {
        .load = sandbox_testproc_load,
        .start = sandbox_testproc_start,
};

static const struct udevice_id sandbox_ids[] = {
        {.compatible = "sandbox,test-processor"},
        {}
};

U_BOOT_DRIVER(sandbox_testproc) = {
        .name = "sandbox_test_proc",
        .of_match = sandbox_ids,
        .id = UCLASS_REMOTEPROC,
        .ops = &sandbox_testproc_ops,
        .probe = sandbox_testproc_probe,
};
```

这允许作为“init”命令的一部分或调用“rproc_init()”函数（如系统依赖项定义）的一部分来探测设备。

驱动程序应该维护它自己的状态机，该状态机适合它所维护的设备。 它至少必须提供加载和启动功能。 我们在这里假设设备需要被加载和启动，否则，没有使用remoteproc框架的真正目的。


# 3. Describing the device using platform data

重要提示：此支持不适用于较新的平台支持。 这仅适用于旧设备。 一旦所有必要的平台都转移到 DM/FDT，这种初始化模式最终将被删除。

考虑到许多平台尚未转向设备树模型，设备的简化定义如下：

```c
struct dm_rproc_uclass_pdata proc_3_test = {
        .name = "proc_3_legacy",
        .mem_type = RPROC_INTERNAL_MEMORY_MAPPED,
        .driver_plat_data = &mydriver_data;
};

U_BOOT_DRVINFO(proc_3_demo) = {
        .name = "sandbox_test_proc",
        .plat = &proc_3_test,
};
```

根据 Remoteproc 驱动程序的特定需求，可能还需要其他数据（例如：SoC 集成详细信息，如时钟句柄或类似的内容）。 有关更多详细信息，请参阅特定 Remoteproc 驱动程序的相应文档。 这些是通过 driver_plat_data 传递的。


# 4. Describing the device using device tree

别名的使用是可选的，但通常建议确保用户对平台有一致的使用模型。 这里使用的兼容字符串特定于所涉及的remoteproc驱动程序。
