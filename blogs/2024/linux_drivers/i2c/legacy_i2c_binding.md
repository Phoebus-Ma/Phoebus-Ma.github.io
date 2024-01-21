
# I2C device driver binding control from user-space in old kernels

**Note**

注意：本节仅在您处理内核 2.6 中的一些旧代码时才相关。 如果您使用更新的内核，则可以安全地跳过本节。


# 1. Introduction

到内核 2.6.32 为止，许多 I2C 驱动程序使用 <linux/i2c.h> 提供的辅助宏，该宏创建标准模块参数，让用户控制驱动程序如何探测 I2C 总线并连接到设备。 这些参数称为探测（让驱动程序探测额外的地址）、强制（强制将驱动程序附加到给定设备）和忽略（防止驱动程序探测给定地址）。

随着 I2C 子系统转换为标准设备驱动程序绑定模型，很明显不再需要这些每个模块的参数，并且可以进行集中实现。 [如何实例化 I2C 设备](https://www.kernel.org/doc/html/latest/i2c/instantiating-devices.html) 的“方法 4：从用户空间实例化”部分描述了基于 sysfs 的新接口。

下面是从旧模块参数到新接口的映射。


# 2. Attaching a driver to an I2C device

旧方法（模块参数）：

```bash
# modprobe <driver> probe=1,0x2d
# modprobe <driver> force=1,0x2d
# modprobe <driver> force_<device>=1,0x2d
```

新方法（sysfs接口）：

```bash
# echo <device> 0x2d > /sys/bus/i2c/devices/i2c-1/new_device
```


# 3. Preventing a driver from attaching to an I2C device

旧方法（模块参数）：

```bash
# modprobe <driver> ignore=1,0x2f
```

新方法（sysfs接口）：

```bash
# echo dummy 0x2f > /sys/bus/i2c/devices/i2c-1/new_device
# modprobe <driver>
```

当然，在加载驱动程序之前实例化虚拟设备非常重要。 虚拟设备将由 i2c-core 本身处理，防止其他驱动程序稍后绑定到它。 如果有问题的地址有一个真实的设备，并且您希望另一个驱动程序绑定到它，则只需传递有问题的设备的名称而不是虚拟设备的名称。
