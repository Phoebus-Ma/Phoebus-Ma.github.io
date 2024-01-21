
# File System Firmware Loader

# 1. Introduction

这是U-Boot框架的文件系统固件加载器，它与一些Linux固件API非常接近。 Linux Firmware API 的详细信息可以参考 <https://01.org/linuxgraphics/gfx-docs/drm/driver-api/firmware/index.html>.

文件系统固件加载器可用于将文件系统格式的存储设备中的任何内容（固件、映像和二进制文件）加载到目标位置（例如内存），然后消费者驱动程序（例如 FPGA 驱动程序）可以将 FPGA 映像从目标位置编程到 FPGA 。

要启用固件加载程序，需要在 <board_name>_defconfig 中设置 CONFIG_FS_LOADER，例如“CONFIG_FS_LOADER=y”。


# 2. Firmware Loader API core features

## 2.1 设备树源中描述的固件存储设备

为了将固件加载的存储设备分区和分区等数据传递到固件加载器驱动程序，这些数据可以在 fs-loader 节点中定义，如下所示：

块设备示例：

```c
fs_loader0: fs-loader {
        bootph-all;
        compatible = "u-boot,fs-loader";
        phandlepart = <&mmc 1>;
};
```

<&mmc 1>表示块存储设备指针及其分区。

上面的例子是对块存储的描述，但是对于UBI存储设备，可以在FDT中描述如下：

ubi 的示例：

```c
fs_loader1: fs-loader {
        bootph-all;
        compatible = "u-boot,fs-loader";
        mtdpart = "UBI",
        ubivol = "ubi0";
};
```

然后，firmware-loader 属性可以添加到任何设备节点，该驱动程序将使用固件加载器进行加载。

固件加载器属性的值应使用 fs-loader 节点的 phandle 进行设置。 例如：

```c
firmware-loader = <&fs_loader0>;
```

如果大多数设备使用相同的fs-loader节点，则可以在/chosen节点下添加firmware-loader属性，而不是添加到每个设备节点。

例如:

```c
/{
        chosen {
                firmware-loader = <&fs_loader0>;
        };
};
```

在使用固件加载器的设备的每个相应驱动程序中，固件加载实例应由 DT phandle 创建。

例如，从 /chosen 获取 DT phandle 并创建实例：

```c
chosen_node = ofnode_path("/chosen");
if (!ofnode_valid(chosen_node)) {
        debug("/chosen node was not found.\n");
        return -ENOENT;
}

phandle_p = ofnode_get_property(chosen_node, "firmware-loader", &size);
if (!phandle_p) {
        debug("firmware-loader property was not found.\n");
        return -ENOENT;
}

phandle = fdt32_to_cpu(*phandle_p);
ret = uclass_get_device_by_phandle_id(UCLASS_FS_FIRMWARE_LOADER,
                                     phandle, &dev);
if (ret)
        return ret;
```

固件加载器驱动程序还设计为支持 U-Boot 环境变量，因此来自 FDT 的所有这些数据都可以在运行时通过 U-Boot 环境变量覆盖。

例如:

**storage_interface:**

存储接口，可以是“mmc”、“usb”、“sata”或“ubi”。

**fw_dev_part:**

块设备号及其分区，可以是“0:1”。

**fw_ubi_mtdpart:**

UBI设备mtd分区，可以是“UBI”。

**fw_ubi_volume:**

UBI卷，可以是“ubi0”。

当设置上述环境变量时，将使用环境值而不是来自 FDT 的数据。 这种设计的好处是允许用户在运行时通过 U-Boot 控制台更改存储属性数据，并将设置保存为存储中的默认环境值以供下一个电源周期使用，因此驱动程序和 FDT 都不需要编译。


## 2.2 文件系统固件加载器API

```c
int request_firmware_into_buf(struct udevice *dev,
                              const char *name,
                              void *buf, size_t size, u32 offset)
```

将固件加载到先前分配的缓冲区中

参数:

- struct udevice *dev: 一个驱动程序的实例
- const char *name: 固件文件名
- void *buf: 将固件加载到的缓冲区地址
- size_t size: 缓冲区大小
- u32 offset: 文件开始读入缓冲区的偏移量

**Returns:**

出错时总读取大小 -ve

**Description:**

固件直接加载到buf指向的缓冲区中

创建固件加载器实例后调用 request_firmware_into_buf API 的示例：

```c
ret = uclass_get_device_by_phandle_id(UCLASS_FS_FIRMWARE_LOADER,
                                     phandle, &dev);
if (ret)
        return ret;

request_firmware_into_buf(dev, filename, buffer_location, buffer_size,
                         offset_ofreading);
```

