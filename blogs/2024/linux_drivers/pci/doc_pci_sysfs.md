
# 5. Accessing PCI device resources through sysfs

sysfs 通常安装在 /sys，提供对支持它的平台上的 PCI 资源的访问。 例如，给定的总线可能如下所示：

```bash
/sys/devices/pci0000:17
|-- 0000:17:00.0
|   |-- class
|   |-- config
|   |-- device
|   |-- enable
|   |-- irq
|   |-- local_cpus
|   |-- remove
|   |-- resource
|   |-- resource0
|   |-- resource1
|   |-- resource2
|   |-- revision
|   |-- rom
|   |-- subsystem_device
|   |-- subsystem_vendor
|   `-- vendor
`-- ...
```

最上面的元素描述 PCI 域和总线号。 在本例中，域编号为 0000，总线编号为 17（两个值均为十六进制）。 该总线在插槽 0 中包含一个单一功能设备。为方便起见，复制了域和总线编号。 device目录下有几个文件，每个文件都有自己的功能。

| file               | function |
| ------------------ | -------- |
| class              | PCI class (ascii, ro) |
| config             | PCI config space (binary, rw) |
| device             | PCI device (ascii, ro) |
| enable             | Whether the device is enabled (ascii, rw) |
| irq                | IRQ number (ascii, ro) |
| local_cpus         | nearby CPU mask (cpumask, ro) |
| remove             | remove device from kernel's list (ascii, wo) |
| resource           | PCI resource host addresses (ascii, ro) |
| resource0..N       | PCI resource N, if present (binary, mmap, rw1) |
| resource0_wc..N_wc | PCI WC map resource N, if prefetchable (binary, mmap) |
| revision           | PCI revision (ascii, ro) |
| rom                | PCI ROM resource, if present (binary, ro) |
| subsystem_device   | PCI subsystem device (ascii, ro) |
| subsystem_vendor   | PCI subsystem vendor (ascii, ro) |
| vendor             | PCI vendor (ascii, ro) |

```bash
ro - read only file
rw - file is readable and writable
wo - write only file
mmap - file is mmapable
ascii - file contains ascii text
binary - file contains binary data
cpumask - file contains a cpumask type
```

[1] rw for IORESOURCE_IO (I/O port) regions only

只读文件是信息性的，对它们的写入将被忽略，“rom”文件除外。 可写文件可用于在设备上执行操作（例如更改配置空间、分离设备）。 mmapable 文件可通过偏移量 0 处的文件 mmap 获得，并可用于从用户空间进行实际设备编程。 请注意，某些平台不支持某些资源的映射，因此请务必检查任何尝试的映射的返回值。 其中最值得注意的是 I/O 端口资源，它也提供读/写访问。

“enable”文件提供了一个计数器，指示设备已启用的次数。 如果“enable”文件当前返回“4”，并且回显“1”，则它将返回“5”。 回显“0”会减少计数。 但是，即使返回到 0，某些初始化也可能无法逆转。

“rom”文件的特殊之处在于它提供对设备 ROM 文件（如果可用）的只读访问。 但默认情况下它是禁用的，因此应用程序应在尝试读取调用之前将字符串“1”写入文件以启用它，并在访问后通过将“0”写入文件来禁用它。 请注意，必须启用设备才能读取 ROM 才能成功返回数据。 如果驱动程序未绑定到设备，则可以使用上面记录的“enable”文件来启用它。

“删除”文件用于通过向文件写入非零整数来删除 PCI 设备。 这不涉及任何类型的热插拔功能，例如 关闭设备电源。 该设备将从内核的 PCI 设备列表中删除，其 sysfs 目录也将被删除，并且该设备将从附加到它的任何驱动程序中删除。 不允许移除 PCI 根总线。


## 5.1. Accessing legacy resources through sysfs

如果底层平台支持，sysfs 中也会提供传统 I/O 端口和 ISA 内存资源。 它们位于 PCI 类层次结构中，例如：

```bash
/sys/class/pci_bus/0000:17/
|-- bridge -> ../../../devices/pci0000:17
|-- cpuaffinity
|-- legacy_io
`-- legacy_mem
```

Legacy_io 文件是一个读/写文件，应用程序可以使用它来执行旧端口 I/O。 应用程序应打开文件，寻找所需的端口（例如 0x3e8）并执行 1、2 或 4 个字节的读取或写入。 Legacy_mem 文件应该使用与所需内存偏移量相对应的偏移量进行映射，例如 0xa0000 表示 VGA 帧缓冲区。 然后，应用程序可以简单地取消引用返回的指针（当然是在检查错误之后）来访问遗留内存空间。


## 5.2. Supporting PCI access on new platforms

为了支持如上所述的 PCI 资源映射，Linux 平台代码最好定义 ARCH_GENERIC_PCI_MMAP_RESOURCE 并使用该功能的通用实现。 为了通过 /proc/bus/pci 中的文件支持 mmap() 的历史接口，平台还可以设置 HAVE_PCI_MMAP。

或者，设置 HAVE_PCI_MMAP 的平台可以提供自己的 pci_mmap_resource_range() 实现，而不是定义 ARCH_GENERIC_PCI_MMAP_RESOURCE。

支持 PCI 资源写组合映射的平台必须定义 arch_can_pci_mmap_wc()，当允许写组合时，它应在运行时评估为非零。 支持 I/O 资源映射的平台类似地定义 arch_can_pci_mmap_io()。

遗留资源受 HAVE_PCI_LEGACY 定义的保护。 希望支持遗留功能的平台应该定义它并提供 pci_legacy_read、pci_legacy_write 和 pci_mmap_legacy_page_range 函数。
