
# PCI Endpoint Framework

## 9.1 Introduction

Linux 有一个全面的 PCI 子系统来支持在 Root Complex 模式下运行的 PCI 控制器。 该子系统具有扫描 PCI 总线、分配内存资源和 IRQ 资源、加载 PCI 驱动程序（基于供应商 ID、设备 ID）、支持其他服务（如热插拔、电源管理、高级错误报告和虚拟通道）的能力。

然而，某些 SoC 中集成的 PCI 控制器 IP 能够在根联合体模式或端点模式下运行。 PCI 端点框架将在 Linux 中添加端点模式支持。 这将有助于在 EP 系统中运行 Linux，该系统可以具有测试或验证、协处理器加速器等各种用例。


## 9.2 PCI Endpoint Core

PCI 端点核心层包含 3 个组件：端点控制器库、端点函数库以及用于将端点函数与端点控制器绑定的 configfs 层。


### 9.2.1. PCI Endpoint Controller(EPC) Library

EPC 库提供可供控制器使用的 API，该控制器可以在端点模式下运行。 它还提供供功能驱动程序/库使用的 API，以实现特定的端点功能。


#### 9.2.1.1. APIs for the PCI controller Driver

本节列出了 PCI Endpoint 内核提供供 PCI 控制器驱动程序使用的 API。

- devm_pci_epc_create()/pci_epc_create()

    &emsp;PCI 控制器驱动程序应实现以下操作：

    - write_header: 填充配置空间标头的操作；
    - set_bar: 配置 BAR 的操作；
    - clear_bar: 重置 BAR 的操作；
    - alloc_addr_space: 在 PCI 控制器地址空间中分配的操作；
    - free_addr_space: 释放分配的地址空间的操作；
    - raise_irq: 引发旧版、MSI 或 MSI-X 中断的操作；
    - start: 启动 PCI 链路的操作；
    - stop: ops 停止 PCI 链接。

    &emsp;然后 PCI 控制器驱动程序可以通过调用 devm_pci_epc_create()/pci_epc_create() 创建新的 EPC 设备。

- devm_pci_epc_destroy()/pci_epc_destroy()

    &emsp;PCI 控制器驱动程序可以使用 devm_pci_epc_destroy() 或 pci_epc_destroy() 销毁由 devm_pci_epc_create() 或 pci_epc_create() 创建的 EPC 设备。

- pci_epc_linkup()

    &emsp;为了通知所有功能设备它们所链接的 EPC 设备已与主机建立链接，PCI 控制器驱动程序应调用 pci_epc_linkup()。

- pci_epc_mem_init()

    &emsp;初始化用于分配EPC地址空间的pci_epc_mem结构。

- pci_epc_mem_exit()

    &emsp;清理在 pci_epc_mem_init() 期间分配的 pci_epc_mem 结构。


#### 9.2.1.2. EPC APIs for the PCI Endpoint Function Driver

本节列出了 PCI 端点核心提供供 PCI 端点功能驱动程序使用的 API。

- pci_epc_write_header()

    &emsp;PCI端点功能驱动程序应使用pci_epc_write_header()将标准配置头写入端点控制器。

- pci_epc_set_bar()

    &emsp;PCI 端点功能驱动程序应使用 pci_epc_set_bar() 配置基址寄存器，以便主机分配 PCI 地址空间。 功能驱动程序的寄存器空间通常使用此API进行配置。

- pci_epc_clear_bar()

    &emsp;PCI端点功能驱动程序应使用pci_epc_clear_bar()来重置BAR。

- pci_epc_raise_irq()

    &emsp;PCI 端点功能驱动程序应使用 pci_epc_raise_irq() 引发传统中断、MSI 或 MSI-X 中断。

- pci_epc_mem_alloc_addr()

    &emsp;PCI端点功能驱动程序应使用pci_epc_mem_alloc_addr()，从访问RC缓冲区所需的EPC地址空间分配内存地址。

- pci_epc_mem_free_addr()

    &emsp;PCI 端点功能驱动程序应使用 pci_epc_mem_free_addr() 来释放使用 pci_epc_mem_alloc_addr() 分配的内存空间。


#### 9.2.1.3. Other EPC APIs

EPC 库还提供其他 API。 这些用于将 EPF 设备与 EPC 设备绑定。 pci-ep-cfs.c可以作为使用这些API的参考。

- pci_epc_get()

    &emsp;根据控制器的设备名称获取对 PCI 端点控制器的引用。

- pci_epc_put()

    &emsp;释放对使用 pci_epc_get() 获得的 PCI 端点控制器的引用。

- pci_epc_add_epf()

    &emsp;将 PCI 端点功能添加到 PCI 端点控制器。 根据规范，一个 PCIe 设备最多可以有 8 个功能。

- pci_epc_remove_epf()

    &emsp;从 PCI 端点控制器中删除 PCI 端点功能。

- pci_epc_start()

    &emsp;PCI 端点功能驱动程序在配置端点功能并想要启动 PCI 链路后，应调用 pci_epc_start()。

- pci_epc_stop()

    &emsp;PCI 端点功能驱动程序应调用 pci_epc_stop() 来停止 PCI LINK。


### 9.2.2. PCI Endpoint Function(EPF) Library

EPF 库提供供功能驱动程序和 EPC 库使用的 API，以提供端点模式功能。


#### 9.2.2.1. EPF APIs for the PCI Endpoint Function Driver

本节列出了 PCI 端点核心提供供 PCI 端点功能驱动程序使用的 API。

- pci_epf_register_driver()

    &emsp;PCI端点功能驱动程序应实现以下操作：

    - bind: 当 EPC 设备绑定到 EPF 设备时要执行的操作；
    - unbind: 当 EPC 设备和 EPF 设备之间的绑定丢失时要执行的操作；
    - linkup: 当 EPC 设备与主机系统建立连接时要执行的操作。

    &emsp;然后，PCI Function 驱动程序可以使用 pci_epf_register_driver() 注册 PCI EPF 驱动程序。

- pci_epf_unregister_driver()

    &emsp;PCI Function 驱动程序可以使用 pci_epf_unregister_driver() 取消注册 PCI EPF 驱动程序。

- pci_epf_alloc_space()

    &emsp;PCI Function 驱动程序可以使用 pci_epf_alloc_space() 为特定 BAR 分配空间。

- pci_epf_free_space()

    &emsp;PCI Function 驱动程序可以通过调用 pci_epf_free_space() 释放分配的空间（使用 pci_epf_alloc_space）。


#### 9.2.2.2. APIs for the PCI Endpoint Controller Library

本节列出了 PCI 端点核心提供供 PCI 端点控制器库使用的 API。

- pci_epf_linkup()

    &emsp;当 EPC 设备建立与主机的连接时，PCI 端点控制器库调用 pci_epf_linkup()。


#### 9.2.2.3. Other EPF APIs

EPF 库还提供其他 API。 这些用于在 EPF 设备绑定到 EPC 设备时通知功能驱动程序。 pci-ep-cfs.c可以作为使用这些API的参考。

- pci_epf_create()

    &emsp;通过传递 PCI EPF 设备的名称来创建新的 PCI EPF 设备。 该名称将用于将 EPF 设备绑定到 EPF 驱动程序。

- pci_epf_destroy()

    &emsp;销毁创建的PCI EPF设备。

- pci_epf_bind()

    &emsp;当 EPF 设备已绑定到 EPC 设备时，应调用 pci_epf_bind()。

- pci_epf_unbind()

    &emsp;当 EPC 设备和 EPF 设备之间的绑定丢失时，应调用 pci_epf_unbind()。


## 9.3 Configuring PCI Endpoint Using CONFIGFS

Author:

Kishon Vijay Abraham I <kishon@ti.com>

PCI 端点核心公开 configfs 条目 (pci_ep) 来配置 PCI 端点功能并将端点功能与端点控制器绑定。 （有关配置 PCI 端点功能的其他机制的介绍，请参阅[简介](#91-introduction)）。


### 9.3.1. Mounting configfs

PCI Endpoint Core 层在挂载的 configfs 目录中创建 pci_ep 目录。 可以使用以下命令安装 configfs：

```bash
mount -t configfs none /sys/kernel/config
```


### 9.3.2. Directory Structure

pci_ep configfs 的根目录有两个目录：控制器和函数。 系统中存在的每个 EPC 设备将在控制器目录中拥有一个条目，并且系统中存在的每个 EPF 驱动程序将在功能目录中拥有一个条目。

```bash
/sys/kernel/config/pci_ep/
        .. controllers/
        .. functions/
```


### 9.3.3. Creating EPF Device

每个注册的 EPF 驱动程序都将列在控制器目录中。 EPF 驱动程序对应的条目将由 EPF 核心创建。

```bash
/sys/kernel/config/pci_ep/functions/
        .. <EPF Driver1>/
                ... <EPF Device 11>/
                ... <EPF Device 21>/
                ... <EPF Device 31>/
        .. <EPF Driver2>/
                ... <EPF Device 12>/
                ... <EPF Device 22>/
```

为了创建 <EPF Driver> 探测类型的 <EPF device>，用户必须在 <EPF DriverN> 内创建一个目录。

每个 <EPF device> 目录包含以下条目，可用于配置端点功能的标准配置标头。 （这些条目是在创建任何新的 <EPF 设备> 时由框架创建的）。

```bash
.. <EPF Driver1>/
        ... <EPF Device 11>/
                ... vendorid
                ... deviceid
                ... revid
                ... progif_code
                ... subclass_code
                ... baseclass_code
                ... cache_line_size
                ... subsys_vendor_id
                ... subsys_id
                ... interrupt_pin
                ... <Symlink EPF Device 31>/
                ... primary/
                        ... <Symlink EPC Device1>/
                ... secondary/
                        ... <Symlink EPC Device2>/
```

如果 EPF 设备必须与 2 个 EPC 关联（如非透明桥的情况），则应将连接到主接口的端点控制器的符号链接添加到“主”目录中，并应将连接到辅助接口的端点控制器的符号链接添加到“主”目录中。 添加到“辅助”目录中。

<EPF 设备> 目录可以具有到其他 <EPF 设备> 的符号链接 (<Symlink EPF Device 31>) 列表。 这些符号链接应该由用户创建来表示绑定到物理功能的虚拟功能。 在上述目录结构中，<EPF Device 11>是物理功能，<EPF Device 31>是虚拟功能。 EPF 设备一旦链接到另一个 EPF 设备，就无法链接到 EPC 设备。


### 9.3.4. EPC Device

每个注册的 EPC 设备都会在控制器目录中列出。 EPC设备对应的条目将由EPC核心创建。

```bash
/sys/kernel/config/pci_ep/controllers/
        .. <EPC Device1>/
                ... <Symlink EPF Device11>/
                ... <Symlink EPF Device12>/
                ... start
        .. <EPC Device2>/
                ... <Symlink EPF Device21>/
                ... <Symlink EPF Device22>/
                ... start
```

<EPC 设备> 目录将包含指向 <EPF 设备> 的符号链接列表。 这些符号链接应由用户创建，以表示端点设备中存在的功能。 只有代表物理功能的 <EPF Device> 才能链接到 EPC 设备。

<EPC Device> 目录还将有一个起始字段。 一旦“1”写入该字段，端点设备将准备好与主机建立链接。 这通常在所有 EPF 设备创建并与 EPC 设备链接后完成。

```bash
| controllers/
       | <Directory: EPC name>/
               | <Symbolic Link: Function>
               | start
| functions/
       | <Directory: EPF driver>/
               | <Directory: EPF device>/
                       | vendorid
                       | deviceid
                       | revid
                       | progif_code
                       | subclass_code
                       | baseclass_code
                       | cache_line_size
                       | subsys_vendor_id
                       | subsys_id
                       | interrupt_pin
                       | function
```


## 9.4 PCI Test Function

Author:

Kishon Vijay Abraham I <kishon@ti.com>

传统上，PCI RC 始终通过使用标准 PCI 卡（如以太网 PCI 卡、USB PCI 卡或 SATA PCI 卡）进行验证。 然而，通过在linux内核中添加EP-core，可以配置一个可以在EP模式下运行的PCI控制器作为测试设备。

PCI 端点测试设备是一个虚拟设备（在软件中定义），用于测试端点功能并充当其他 PCI 端点设备（以使用 EP 框架）的示例驱动程序。

PCI端点测试设备有以下寄存器：

1. PCI_ENDPOINT_TEST_MAGIC
2. PCI_ENDPOINT_TEST_COMMAND
3. PCI_ENDPOINT_TEST_STATUS
4. PCI_ENDPOINT_TEST_SRC_ADDR
5. PCI_ENDPOINT_TEST_DST_ADDR
6. PCI_ENDPOINT_TEST_SIZE
7. PCI_ENDPOINT_TEST_CHECKSUM
8. PCI_ENDPOINT_TEST_IRQ_TYPE
9. PCI_ENDPOINT_TEST_IRQ_NUMBER

- PCI_ENDPOINT_TEST_MAGIC

&emsp;该寄存器将用于测试 BAR0。 将写入已知模式并从 MAGIC 寄存器读回以验证 BAR0。

- PCI_ENDPOINT_TEST_COMMAND

&emsp;主机驱动程序将使用该寄存器来指示端点设备必须执行的功能。

| Bitfield | Description                                         |
| -------- | --------------------------------------------------- |
| Bit 0    | raise legacy IRQ                                    |
| Bit 1    | raise MSI IRQ                                       |
| Bit 2    | raise MSI-X IRQ                                     |
| Bit 3    | 读命令（从RC缓冲区读取数据）                          |
| Bit 4    | 写命令（将数据写入RC缓冲区）                          |
| Bit 5    | 复制命令（将数据从一个 RC 缓冲区复制到另一个 RC 缓冲区）|

- PCI_ENDPOINT_TEST_STATUS

&emsp;该寄存器反映 PCI 端点设备的状态。

| Bitfield | Description |
| -------- | ----------- |
| Bit 0    | 读取成功     |
| Bit 1    | 读取失败     |
| Bit 2    | 写入成功     |
| Bit 3    | 写入失败     |
| Bit 4    | 复制成功     |
| Bit 5    | 复制失败     |
| Bit 6    | IRQ raised  |
| Bit 7    | 源地址无效   |
| Bit 8    | 目标地址无效 |

- PCI_ENDPOINT_TEST_SRC_ADDR

&emsp;该寄存器包含 COPY/READ 命令的源地址（RC 缓冲区地址）。

- PCI_ENDPOINT_TEST_DST_ADDR

&emsp;该寄存器包含 COPY/WRITE 命令的目标地址（RC 缓冲区地址）。

- PCI_ENDPOINT_TEST_IRQ_TYPE

&emsp;该寄存器包含为 READ/WRITE/COPY 触发的中断类型（Legacy/MSI）和引发 IRQ（Legacy/MSI）命令。

可能的类型：

|        |   |
| ------ | - |
| Legacy | 0 |
| MSI    | 1 |
| MSI-X  | 2 |

- PCI_ENDPOINT_TEST_IRQ_NUMBER

&emsp;该寄存器包含触发的 ID 中断。

允许值：

|        |             |
| ------ | ----------- |
| Legacy | 0           |
| MSI    | [1 .. 32]   |
| MSI-X  | [1 .. 2048] |


## 9.5 PCI Test User Guide

Author：

Kishon Vijay Abraham I <kishon@ti.com>

本文档是帮助用户使用 pci-epf-test 功能驱动程序和 pci_endpoint_test 主机驱动程序来测试 PCI 的指南。 下面给出了主机端和 EP 端要遵循的步骤列表。


### 9.5.1. Endpoint Device

#### 9.5.1.1. Endpoint Controller Devices

要查找系统中端点控制器设备的列表：

```bash
# ls /sys/class/pci_epc/
  51000000.pcie_ep
```

如果启用 PCI_ENDPOINT_CONFIGFS：

```bash
# ls /sys/kernel/config/pci_ep/controllers
  51000000.pcie_ep
```


#### 9.5.1.2. Endpoint Function Drivers

要查找系统中的端点功能驱动程序列表：

```bash
# ls /sys/bus/pci-epf/drivers
  pci_epf_test
```

如果启用 PCI_ENDPOINT_CONFIGFS：

```bash
# ls /sys/kernel/config/pci_ep/functions
  pci_epf_test
```


#### 9.5.1.3. Creating pci-epf-test Device

PCI端点功能设备可以使用configfs创建。 要创建 pci-epf-test 设备，可以使用以下命令：

```bash
# mount -t configfs none /sys/kernel/config
# cd /sys/kernel/config/pci_ep/
# mkdir functions/pci_epf_test/func1
```

上面的“mkdir func1”创建将由 pci_epf_test 驱动程序探测的 pci-epf-test 功能设备。

PCI 端点框架使用以下可配置字段填充目录：

```bash
# ls functions/pci_epf_test/func1
  baseclass_code        interrupt_pin   progif_code     subsys_id
  cache_line_size       msi_interrupts  revid           subsys_vendorid
  deviceid              msix_interrupts subclass_code   vendorid
```

当设备绑定到驱动程序时，PCI 端点功能驱动程序会使用默认值填充这些条目。 pci-epf-test 驱动程序用 0xffff 填充vendorid，用 0x0001 填充interrupt_pin：

```bash
# cat functions/pci_epf_test/func1/vendorid
  0xffff
# cat functions/pci_epf_test/func1/interrupt_pin
  0x0001
```


#### 9.5.1.4. Configuring pci-epf-test Device

用户可以使用 configfs 条目配置 pci-epf-test 设备。 为了更改功能设备使用的vendorid和MSI中断数量，可以使用以下命令：

```bash
# echo 0x104c > functions/pci_epf_test/func1/vendorid
# echo 0xb500 > functions/pci_epf_test/func1/deviceid
# echo 16 > functions/pci_epf_test/func1/msi_interrupts
# echo 8 > functions/pci_epf_test/func1/msix_interrupts
```


#### 9.5.1.5. Binding pci-epf-test Device to EP Controller

为了使端点功能设备可用，它必须绑定到 PCI 端点控制器驱动程序。 使用 configfs 将功能设备绑定到系统中存在的控制器驱动程序之一：

```bash
# ln -s functions/pci_epf_test/func1 controllers/51000000.pcie_ep/
```

一旦完成上述步骤，PCI端点就准备好与主机建立链接。


#### 9.5.1.6. Start the Link

为了使端点设备与主机建立链接，_start_字段应填充为“1”：

```bash
# echo 1 > controllers/51000000.pcie_ep/start
```


### 9.5.2. RootComplex Device

#### 9.5.2.1. lspci Output

请注意，此处列出的设备对应于上面 1.4 中填充的值：

```bash
00:00.0 PCI bridge: Texas Instruments Device 8888 (rev 01)
01:00.0 Unassigned class [ff00]: Texas Instruments Device b500
```


#### 9.5.2.2. Using Endpoint Test function Device

在tools/pci/中添加的pcitest.sh可用于运行所有默认的PCI端点测试。 要编译此工具，应使用以下命令：

```bash
# cd <kernel-dir>
# make -C tools/pci
```

或者如果您希望在系统中编译并安装：

```bash
# cd <kernel-dir>
# make -C tools/pci install
```

该工具和脚本将位于 <rootfs>/usr/bin/ 中


##### 9.5.2.2.1. pcitest.sh Output

```bash
# pcitest.sh
BAR tests

BAR0:           OKAY
BAR1:           OKAY
BAR2:           OKAY
BAR3:           OKAY
BAR4:           NOT OKAY
BAR5:           NOT OKAY

Interrupt tests

SET IRQ TYPE TO LEGACY:         OKAY
LEGACY IRQ:     NOT OKAY
SET IRQ TYPE TO MSI:            OKAY
MSI1:           OKAY
MSI2:           OKAY
MSI3:           OKAY
MSI4:           OKAY
MSI5:           OKAY
MSI6:           OKAY
MSI7:           OKAY
MSI8:           OKAY
MSI9:           OKAY
MSI10:          OKAY
MSI11:          OKAY
MSI12:          OKAY
MSI13:          OKAY
MSI14:          OKAY
MSI15:          OKAY
MSI16:          OKAY
MSI17:          NOT OKAY
MSI18:          NOT OKAY
MSI19:          NOT OKAY
MSI20:          NOT OKAY
MSI21:          NOT OKAY
MSI22:          NOT OKAY
MSI23:          NOT OKAY
MSI24:          NOT OKAY
MSI25:          NOT OKAY
MSI26:          NOT OKAY
MSI27:          NOT OKAY
MSI28:          NOT OKAY
MSI29:          NOT OKAY
MSI30:          NOT OKAY
MSI31:          NOT OKAY
MSI32:          NOT OKAY
SET IRQ TYPE TO MSI-X:          OKAY
MSI-X1:         OKAY
MSI-X2:         OKAY
MSI-X3:         OKAY
MSI-X4:         OKAY
MSI-X5:         OKAY
MSI-X6:         OKAY
MSI-X7:         OKAY
MSI-X8:         OKAY
MSI-X9:         NOT OKAY
MSI-X10:        NOT OKAY
MSI-X11:        NOT OKAY
MSI-X12:        NOT OKAY
MSI-X13:        NOT OKAY
MSI-X14:        NOT OKAY
MSI-X15:        NOT OKAY
MSI-X16:        NOT OKAY
[...]
MSI-X2047:      NOT OKAY
MSI-X2048:      NOT OKAY

Read Tests

SET IRQ TYPE TO MSI:            OKAY
READ (      1 bytes):           OKAY
READ (   1024 bytes):           OKAY
READ (   1025 bytes):           OKAY
READ (1024000 bytes):           OKAY
READ (1024001 bytes):           OKAY

Write Tests

WRITE (      1 bytes):          OKAY
WRITE (   1024 bytes):          OKAY
WRITE (   1025 bytes):          OKAY
WRITE (1024000 bytes):          OKAY
WRITE (1024001 bytes):          OKAY

Copy Tests

COPY (      1 bytes):           OKAY
COPY (   1024 bytes):           OKAY
COPY (   1025 bytes):           OKAY
COPY (1024000 bytes):           OKAY
COPY (1024001 bytes):           OKAY
```


## 9.6 PCI NTB Function

Author:

Kishon Vijay Abraham I <kishon@ti.com>

PCI 非透明桥 (NTB) 允许两个主机系统通过将每个主机作为设备暴露给另一个主机来相互通信。 NTB 通常支持在远程计算机上生成中断、将内存范围公开为 BAR 以及执行 DMA 的能力。 它们还支持暂存器，这是 NTB 内可从两台机器访问的内存区域。

PCI NTB 功能允许两个不同的系统（或主机）通过配置端点实例来相互通信，从而将来自一个系统的事务路由到另一系统。

在下图中，PCI NTB 功能使用多个 PCI 端点 (EP) 实例配置 SoC，从而将来自一个 EP 控制器的事务路由到另一个 EP 控制器。 一旦 PCI NTB 功能为 SoC 配置了多个 EP 实例，HOST1 和 HOST2 就可以使用 SoC 作为桥梁相互通信。

```bash
   +-------------+                                   +-------------+
   |             |                                   |             |
   |    HOST1    |                                   |    HOST2    |
   |             |                                   |             |
   +------^------+                                   +------^------+
          |                                                 |
          |                                                 |
+---------|-------------------------------------------------|---------+
|  +------v------+                                   +------v------+  |
|  |             |                                   |             |  |
|  |     EP      |                                   |     EP      |  |
|  | CONTROLLER1 |                                   | CONTROLLER2 |  |
|  |             <----------------------------------->             |  |
|  |             |                                   |             |  |
|  |             |                                   |             |  |
|  |             |  SoC With Multiple EP Instances   |             |  |
|  |             |  (Configured using NTB Function)  |             |  |
|  +-------------+                                   +-------------+  |
+---------------------------------------------------------------------+
```


### 9.6.1. Constructs used for Implementing NTB

1. 配置区域；
2. 自暂存寄存器；
3. 对等暂存器寄存器；
4. Doorbell (DB) 寄存器；
5. 内存窗口 (MW).


#### 9.6.1.1. Config Region:

配置区域是特定于使用 NTB 端点功能驱动程序实现的 NTB 的构造。 主机和端点端 NTB 功能驱动程序将使用此区域相互交换信息。 配置区域具有用于配置端点控制器的控制/状态寄存器。 主机可以写入该区域以配置出站地址转换单元 (ATU) 并指示链路状态。 端点可以指示该区域内主机发出的命令的状态。 端点还可以使用该区域向主机指示暂存器偏移量和内存窗口数量。

配置区域的格式如下。 这里所有的字段都是32位的。

```bash
      +------------------------+
      |         COMMAND        |
      +------------------------+
      |         ARGUMENT       |
      +------------------------+
      |         STATUS         |
      +------------------------+
      |         TOPOLOGY       |
      +------------------------+
      |    ADDRESS (LOWER 32)  |
      +------------------------+
      |    ADDRESS (UPPER 32)  |
      +------------------------+
      |           SIZE         |
      +------------------------+
      |   NO OF MEMORY WINDOW  |
      +------------------------+
      |  MEMORY WINDOW1 OFFSET |
      +------------------------+
      |       SPAD OFFSET      |
      +------------------------+
      |        SPAD COUNT      |
      +------------------------+
      |      DB ENTRY SIZE     |
      +------------------------+
      |         DB DATA        |
      +------------------------+
      |            :           |
      +------------------------+
      |            :           |
      +------------------------+
      |         DB DATA        |
      +------------------------+


COMMAND:

      NTB function supports three commands:

        CMD_CONFIGURE_DOORBELL (0x1): Command to configure doorbell. Before
      invoking this command, the host should allocate and initialize
      MSI/MSI-X vectors (i.e., initialize the MSI/MSI-X Capability in the
      Endpoint). The endpoint on receiving this command will configure
      the outbound ATU such that transactions to Doorbell BAR will be routed
      to the MSI/MSI-X address programmed by the host. The ARGUMENT
      register should be populated with number of DBs to configure (in the
      lower 16 bits) and if MSI or MSI-X should be configured (BIT 16).

        CMD_CONFIGURE_MW (0x2): Command to configure memory window (MW). The
      host invokes this command after allocating a buffer that can be
      accessed by remote host. The allocated address should be programmed
      in the ADDRESS register (64 bit), the size should be programmed in
      the SIZE register and the memory window index should be programmed
      in the ARGUMENT register. The endpoint on receiving this command
      will configure the outbound ATU such that transactions to MW BAR
      are routed to the address provided by the host.

        CMD_LINK_UP (0x3): Command to indicate an NTB application is
      bound to the EP device on the host side. Once the endpoint
      receives this command from both the hosts, the endpoint will
      raise a LINK_UP event to both the hosts to indicate the host
      NTB applications can start communicating with each other.

ARGUMENT:

      The value of this register is based on the commands issued in
      command register. See COMMAND section for more information.

TOPOLOGY:

      Set to NTB_TOPO_B2B_USD for Primary interface
      Set to NTB_TOPO_B2B_DSD for Secondary interface

ADDRESS/SIZE:

      Address and Size to be used while configuring the memory window.
      See "CMD_CONFIGURE_MW" for more info.

MEMORY WINDOW1 OFFSET:

      Memory Window 1 and Doorbell registers are packed together in the
      same BAR. The initial portion of the region will have doorbell
      registers and the latter portion of the region is for memory window 1.
      This register will specify the offset of the memory window 1.

NO OF MEMORY WINDOW:

      Specifies the number of memory windows supported by the NTB device.

SPAD OFFSET:

      Self scratchpad region and config region are packed together in the
      same BAR. The initial portion of the region will have config region
      and the latter portion of the region is for self scratchpad. This
      register will specify the offset of the self scratchpad registers.

SPAD COUNT:

      Specifies the number of scratchpad registers supported by the NTB
      device.

DB ENTRY SIZE:

      Used to determine the offset within the DB BAR that should be written
      in order to raise doorbell. EPF NTB can use either MSI or MSI-X to
      ring doorbell (MSI-X support will be added later). MSI uses same
      address for all the interrupts and MSI-X can provide different
      addresses for different interrupts. The MSI/MSI-X address is provided
      by the host and the address it gives is based on the MSI/MSI-X
      implementation supported by the host. For instance, ARM platform
      using GIC ITS will have the same MSI-X address for all the interrupts.
      In order to support all the combinations and use the same mechanism
      for both MSI and MSI-X, EPF NTB allocates a separate region in the
      Outbound Address Space for each of the interrupts. This region will
      be mapped to the MSI/MSI-X address provided by the host. If a host
      provides the same address for all the interrupts, all the regions
      will be translated to the same address. If a host provides different
      addresses, the regions will be translated to different addresses. This
      will ensure there is no difference while raising the doorbell.

DB DATA:

      EPF NTB supports 32 interrupts, so there are 32 DB DATA registers.
      This holds the MSI/MSI-X data that has to be written to MSI address
      for raising doorbell interrupt. This will be populated by EPF NTB
      while invoking CMD_CONFIGURE_DOORBELL.
```


#### 9.6.1.2. Scratchpad Registers:

每个主机在 NTB 端点控制器的内存中分配有自己的寄存器空间。 它们可以从桥的两侧读取和写入。 它们由基于 NTB 构建的应用程序使用，可用于在设备两侧之间传递控制和状态信息。

暂存器寄存器有 2 个部分:

1. Self Scratchpad：主机自己的寄存器空间；
2. Peer Scratchpad: 远程主机的寄存器空间。


#### 9.6.1.3. Doorbell Registers:

主机使用门铃寄存器来相互中断。


#### 9.6.1.4. Memory Window:

两台主机之间的实际数据传输将使用内存窗口进行。


### 9.6.2. Modeling Constructs:

有 5 个或更多不同的区域（配置、自暂存器、对等暂存器、门铃、一个或多个内存窗口）需要建模以实现 NTB 功能。 至少需要一个内存窗口，同时允许多个内存窗口。 所有这些区域都应该映射到 BAR，以便主机访问这些区域。

如果为每个区域分配一个 32 位 BAR，该方案将如下所示：

| BAR NO | CONSTRUCTS USED |
|------- | --------------- |
| BAR0   | 配置区域         |
| BAR1   | Self Scratchpad |
| BAR2   | Peer Scratchpad |
| BAR3   | Doorbell        |
| BAR4   | Memory Window 1 |
| BAR5   | Memory Window 2 |

但是，如果我们为每个区域分配单独的 BAR，则在仅支持 64 位 BAR 的平台中，将没有足够的 BAR 用于所有区域。

为了得到大多数平台的支持，区域应该以提供 NTB 功能的方式打包并映射到 BAR，并确保主机不会访问任何不应该访问的区域。

EPF NTB 功能使用以下方案：

| BAR NO | CONSTRUCTS USED                 |
| ------ | ------------------------------- |
| BAR0   | Config Region + Self Scratchpad |
| BAR1   | Peer Scratchpad                 |
| BAR2   | Doorbell + Memory Window 1      |
| BAR3   | Memory Window 2                 |
| BAR4   | Memory Window 3                 |
| BAR5   | Memory Window 4                 |

使用此方案，对于基本 NTB 功能，3 个 BAR 就足够了。


#### 9.6.2.1. Modeling Config/Scratchpad Region:

```bash
+-----------------+------->+------------------+        +-----------------+
|       BAR0      |        |  CONFIG REGION   |        |       BAR0      |
+-----------------+----+   +------------------+<-------+-----------------+
|       BAR1      |    |   |SCRATCHPAD REGION |        |       BAR1      |
+-----------------+    +-->+------------------+<-------+-----------------+
|       BAR2      |            Local Memory            |       BAR2      |
+-----------------+                                    +-----------------+
|       BAR3      |                                    |       BAR3      |
+-----------------+                                    +-----------------+
|       BAR4      |                                    |       BAR4      |
+-----------------+                                    +-----------------+
|       BAR5      |                                    |       BAR5      |
+-----------------+                                    +-----------------+
  EP CONTROLLER 1                                        EP CONTROLLER 2
```

上图显示了在本地内存中分配的 HOST1（连接到 EP 控制器 1）的配置区域 + Scratchpad 区域。 HOST1 可以使用 EP 控制器 1 的 BAR0 访问配置区域和暂存器区域（自暂存器）。对等主机（连接到 EP 控制器 2 的 HOST2）也可以使用 EP 控制器 2 的 BAR1 访问此暂存器区域（对等暂存器）。 该图显示了为 HOST1 分配 Config 区域和 Scratchpad 区域的情况，但这同样适用于 HOST2。


#### 9.6.2.2. Modeling Doorbell/Memory Window 1:

```bash
+-----------------+    +----->+----------------+-----------+-----------------+
|       BAR0      |    |      |   Doorbell 1   +-----------> MSI-X ADDRESS 1 |
+-----------------+    |      +----------------+           +-----------------+
|       BAR1      |    |      |   Doorbell 2   +---------+ |                 |
+-----------------+----+      +----------------+         | |                 |
|       BAR2      |           |   Doorbell 3   +-------+ | +-----------------+
+-----------------+----+      +----------------+       | +-> MSI-X ADDRESS 2 |
|       BAR3      |    |      |   Doorbell 4   +-----+ |   +-----------------+
+-----------------+    |      |----------------+     | |   |                 |
|       BAR4      |    |      |                |     | |   +-----------------+
+-----------------+    |      |      MW1       +---+ | +-->+ MSI-X ADDRESS 3||
|       BAR5      |    |      |                |   | |     +-----------------+
+-----------------+    +----->-----------------+   | |     |                 |
  EP CONTROLLER 1             |                |   | |     +-----------------+
                              |                |   | +---->+ MSI-X ADDRESS 4 |
                              +----------------+   |       +-----------------+
                               EP CONTROLLER 2     |       |                 |
                                 (OB SPACE)        |       |                 |
                                                   +------->      MW1        |
                                                           |                 |
                                                           |                 |
                                                           +-----------------+
                                                           |                 |
                                                           |                 |
                                                           |                 |
                                                           |                 |
                                                           |                 |
                                                           +-----------------+
                                                            PCI Address Space
                                                            (Managed by HOST2)
```

上图显示了门铃和内存窗口 1 是如何映射的，以便 HOST1 可以在 HOST2 上引发门铃中断，以及 HOST1 如何使用内存窗口 1 (MW1) 访问 HOST2 公开的缓冲区。 这里，门铃和内存窗口 1 区域被分配在 EP 控制器 2 出站 (OB) 地址空间中。 为门铃和内存 window1 分配和配置 BAR 是在 NTB 端点功能驱动程序的初始化阶段完成的。 当 HOST2 发送 CMD_CONFIGURE_MW/CMD_CONFIGURE_DOORBELL 时，完成从 EP 控制器 2 OB 空间到 PCI 地址空间的映射。


#### 9.6.2.3. Modeling Optional Memory Windows:

其建模方式与 MW1 相同，但每个附加内存窗口都映射到单独的 BAR。


## 9.7 PCI Non-Transparent Bridge (NTB) Endpoint Function (EPF) User Guide

Author:

Kishon Vijay Abraham I <kishon@ti.com>

本文档是帮助用户使用 pci-epf-ntb 功能驱动程序和 ntb_hw_epf 主机驱动程序来实现 NTB 功能的指南。 下面给出了主机端和 EP 端要遵循的步骤列表。 有关使用可配置端点的 NTB 的硬件配置和内部结构，请参阅 PCI NTB 功能。


### 9.7.1. Endpoint Device

#### 9.7.1.1. Endpoint Controller Devices

为了实现 NTB 功能，至少需要两个端点控制器设备。

要查找系统中端点控制器设备的列表：

```bash
# ls /sys/class/pci_epc/
2900000.pcie-ep  2910000.pcie-ep
```

如果启用 PCI_ENDPOINT_CONFIGFS：

```bash
# ls /sys/kernel/config/pci_ep/controllers
2900000.pcie-ep  2910000.pcie-ep
```


#### 9.7.1.2. Endpoint Function Drivers

要查找系统中的端点功能驱动程序列表：

```bash
# ls /sys/bus/pci-epf/drivers
pci_epf_ntb   pci_epf_ntb
```

如果启用 PCI_ENDPOINT_CONFIGFS：

```bash
# ls /sys/kernel/config/pci_ep/functions
pci_epf_ntb   pci_epf_ntb
```


#### 9.7.1.3. Creating pci-epf-ntb Device

PCI端点功能设备可以使用configfs创建。 要创建 pci-epf-ntb 设备，可以使用以下命令：

```bash
# mount -t configfs none /sys/kernel/config
# cd /sys/kernel/config/pci_ep/
# mkdir functions/pci_epf_ntb/func1
```

上面的“mkdir func1”创建将由 pci_epf_ntb 驱动程序探测的 pci-epf-ntb 功能设备。

PCI 端点框架使用以下可配置字段填充目录：

```bash
# ls functions/pci_epf_ntb/func1
baseclass_code    deviceid          msi_interrupts    pci-epf-ntb.0
progif_code       secondary         subsys_id         vendorid
cache_line_size   interrupt_pin     msix_interrupts   primary
revid             subclass_code     subsys_vendor_id
```

当设备绑定到驱动程序时，PCI 端点功能驱动程序会使用默认值填充这些条目。 pci-epf-ntb 驱动程序用 0xffff 填充vendorid，用 0x0001 填充interrupt_pin：

```bash
# cat functions/pci_epf_ntb/func1/vendorid
0xffff
# cat functions/pci_epf_ntb/func1/interrupt_pin
0x0001
```


#### 9.7.1.4. Configuring pci-epf-ntb Device

用户可以使用其 configfs 条目配置 pci-epf-ntb 设备。 为了更改vendorid和deviceid，可以使用以下命令：

```bash
# echo 0x104c > functions/pci_epf_ntb/func1/vendorid
# echo 0xb00d > functions/pci_epf_ntb/func1/deviceid
```

PCI端点框架还会自动在功能属性目录中创建一个子目录。 该子目录与功能设备的名称具有相同的名称，并填充了可由用户配置的以下 NTB 特定属性：

```bash
# ls functions/pci_epf_ntb/func1/pci_epf_ntb.0/
db_count    mw1         mw2         mw3         mw4         num_mws
spad_count
```

NTB 功能的示例配置如下：

```bash
# echo 4 > functions/pci_epf_ntb/func1/pci_epf_ntb.0/db_count
# echo 128 > functions/pci_epf_ntb/func1/pci_epf_ntb.0/spad_count
# echo 2 > functions/pci_epf_ntb/func1/pci_epf_ntb.0/num_mws
# echo 0x100000 > functions/pci_epf_ntb/func1/pci_epf_ntb.0/mw1
# echo 0x100000 > functions/pci_epf_ntb/func1/pci_epf_ntb.0/mw2
```


#### 9.7.1.5. Binding pci-epf-ntb Device to EP Controller

NTB 功能设备应连接到连接到两台主机的两个 PCI 端点控制器。 使用 NTB 功能设备内的“主要”和“辅助”条目将一个 PCI 端点控制器连接到主接口，并将另一个 PCI 端点控制器连接到辅助接口：

```bash
# ln -s controllers/2900000.pcie-ep/ functions/pci-epf-ntb/func1/primary
# ln -s controllers/2910000.pcie-ep/ functions/pci-epf-ntb/func1/secondary
```

一旦完成上述步骤，两个 PCI 端点控制器就准备好与主机建立链接。


#### 9.7.1.6. Start the Link

为了使端点设备与主机建立链接，_start_字段应填充为“1”。 对于 NTB，两个 PCI 端点控制器都应与主机建立链接：

```bash
# echo 1 > controllers/2900000.pcie-ep/start
# echo 1 > controllers/2910000.pcie-ep/start
```


### 9.7.2. RootComplex Device

#### 9.7.2.1. lspci Output

请注意，此处列出的设备对应于上面“创建 pci-epf-ntb 设备”部分中填充的值：

```bash
# lspci
0000:00:00.0 PCI bridge: Texas Instruments Device b00d
0000:01:00.0 RAM memory: Texas Instruments Device b00d
```


#### 9.7.2.2. Using ntb_hw_epf Device

主机端软件遵循Linux中标准NTB软件架构。 所有现有的客户端 NTB 实用程序，如 NTB Transport Client 和 NTB Netdev、NTB Ping Pong Test Client 和 NTB Tool Test Client 都可以与 NTB 功能设备一起使用。

有关 NTB 的更多信息，请参阅 [Non-Transparent Bridge](https://www.kernel.org/doc/html/latest/driver-api/ntb.html).


## 9.8 PCI vNTB Function

Author:

Frank Li <Frank.Li@nxp.com>

PCI NTB 功能和 PCI vNTB 功能之间的区别是 PCI NTB 功能需要在两个端点实例上连接 HOST1 和 HOST2。

PCI vNTB功能仅使用一台主机和一个端点（EP），使用NTB连接EP和PCI主机：

```bash
+------------+         +---------------------------------------+
|            |         |                                       |
+------------+         |                        +--------------+
| NTB        |         |                        | NTB          |
| NetDev     |         |                        | NetDev       |
+------------+         |                        +--------------+
| NTB        |         |                        | NTB          |
| Transfer   |         |                        | Transfer     |
+------------+         |                        +--------------+
|            |         |                        |              |
|  PCI NTB   |         |                        |              |
|    EPF     |         |                        |              |
|   Driver   |         |                        | PCI Virtual  |
|            |         +---------------+        | NTB Driver   |
|            |         | PCI EP NTB    |<------>|              |
|            |         |  FN Driver    |        |              |
+------------+         +---------------+        +--------------+
|            |         |               |        |              |
|  PCI BUS   | <-----> |  PCI EP BUS   |        |  Virtual PCI |
|            |  PCI    |               |        |     BUS      |
+------------+         +---------------+--------+--------------+
    PCI RC                        PCI EP
```


### 9.8.1. Constructs used for Implementing vNTB

1. Config Region
2. Self Scratchpad Registers
3. Peer Scratchpad Registers
4. Doorbell (DB) Registers
5. Memory Window (MW)


#### 9.8.1.1. Config Region:

它与 PCI NTB 功能驱动程序相同。


#### 9.8.1.2. Scratchpad Registers:

它附加在配置区域之后。

```bash
+--------------------------------------------------+ Base
|                                                  |
|                                                  |
|                                                  |
|          Common Config Register                  |
|                                                  |
|                                                  |
|                                                  |
+-----------------------+--------------------------+ Base + span_offset
|                       |                          |
|    Peer Span Space    |    Span Space            |
|                       |                          |
|                       |                          |
+-----------------------+--------------------------+ Base + span_offset
|                       |                          |      + span_count * 4
|                       |                          |
|     Span Space        |   Peer Span Space        |
|                       |                          |
+-----------------------+--------------------------+
      Virtual PCI             Pcie Endpoint
      NTB Driver               NTB Driver
```


#### 9.8.1.3. Doorbell Registers:

主机使用门铃寄存器来相互中断。


#### 9.8.1.4. Memory Window:

两台主机之间的实际数据传输将使用内存窗口进行。


### 9.8.2. Modeling Constructs:

32-bit BARs.

| BAR NO | CONSTRUCTS USED |
| ------ | --------------- |
| BAR0   | Config Region   |
| BAR1   | Doorbell        |
| BAR2   | Memory Window 1 |
| BAR3   | Memory Window 2 |
| BAR4   | Memory Window 3 |
| BAR5   | Memory Window 4 |

64-bit BARs.

| BAR NO | CONSTRUCTS USED            |
| ------ | -------------------------- |
| BAR0   | Config Region + Scratchpad |
| BAR1   |                            |
| BAR2   | Doorbell                   |
| BAR3   |                            |
| BAR4   | Memory Window 1            |
| BAR5   |                            |


## 9.9 PCI Non-Transparent Bridge (NTB) Endpoint Function (EPF) User Guide

Author:

Frank Li <Frank.Li@nxp.com>

本文档是帮助用户使用 pci-epf-vntb 功能驱动程序和 ntb_hw_epf 主机驱动程序来实现 NTB 功能的指南。 下面给出了主机端和 EP 端要遵循的步骤列表。 有关使用可配置端点的 NTB 的硬件配置和内部结构，请参阅 PCI vNTB 功能。


### 9.9.1. Endpoint Device

#### 9.9.1.1. Endpoint Controller Devices

要查找系统中端点控制器设备的列表：

```bash
# ls /sys/class/pci_epc/
  5f010000.pcie_ep
```

如果启用 PCI_ENDPOINT_CONFIGFS：

```bash
# ls /sys/kernel/config/pci_ep/controllers
  5f010000.pcie_ep
```


#### 9.9.1.2. Endpoint Function Drivers

要查找系统中的端点功能驱动程序列表：

```bash
# ls /sys/bus/pci-epf/drivers
pci_epf_ntb  pci_epf_test  pci_epf_vntb
```

如果启用 PCI_ENDPOINT_CONFIGFS：

```bash
# ls /sys/kernel/config/pci_ep/functions
pci_epf_ntb  pci_epf_test  pci_epf_vntb
```


#### 9.9.1.3. Creating pci-epf-vntb Device

PCI端点功能设备可以使用configfs创建。 要创建 pci-epf-vntb 设备，可以使用以下命令：

```bash
# mount -t configfs none /sys/kernel/config
# cd /sys/kernel/config/pci_ep/
# mkdir functions/pci_epf_vntb/func1
```

上面的“mkdir func1”创建将由 pci_epf_vntb 驱动程序探测的 pci-epf-ntb 功能设备。

PCI 端点框架使用以下可配置字段填充目录：

```bash
# ls functions/pci_epf_ntb/func1
baseclass_code    deviceid          msi_interrupts    pci-epf-ntb.0
progif_code       secondary         subsys_id         vendorid
cache_line_size   interrupt_pin     msix_interrupts   primary
revid             subclass_code     subsys_vendor_id
```

当设备绑定到驱动程序时，PCI 端点功能驱动程序会使用默认值填充这些条目。 pci-epf-vntb 驱动程序用 0xffff 填充vendorid，用 0x0001 填充interrupt_pin：

```bash
# cat functions/pci_epf_vntb/func1/vendorid
0xffff
# cat functions/pci_epf_vntb/func1/interrupt_pin
0x0001
```


#### 9.9.1.4. Configuring pci-epf-vntb Device

用户可以使用其 configfs 条目配置 pci-epf-vntb 设备。 为了更改vendorid和deviceid，可以使用以下命令：

```bash
# echo 0x1957 > functions/pci_epf_vntb/func1/vendorid
# echo 0x0809 > functions/pci_epf_vntb/func1/deviceid
```

PCI端点框架还会自动在功能属性目录中创建一个子目录。 该子目录与功能设备的名称具有相同的名称，并填充了可由用户配置的以下 NTB 特定属性：

```bash
# ls functions/pci_epf_vntb/func1/pci_epf_vntb.0/
db_count    mw1         mw2         mw3         mw4         num_mws
spad_count
```

NTB 功能的示例配置如下：

```bash
# echo 4 > functions/pci_epf_vntb/func1/pci_epf_vntb.0/db_count
# echo 128 > functions/pci_epf_vntb/func1/pci_epf_vntb.0/spad_count
# echo 1 > functions/pci_epf_vntb/func1/pci_epf_vntb.0/num_mws
# echo 0x100000 > functions/pci_epf_vntb/func1/pci_epf_vntb.0/mw1
```

虚拟 PCI 总线的虚拟 NTB 驱动程序的示例配置：

```bash
# echo 0x1957 > functions/pci_epf_vntb/func1/pci_epf_vntb.0/vntb_vid
# echo 0x080A > functions/pci_epf_vntb/func1/pci_epf_vntb.0/vntb_pid
# echo 0x10 > functions/pci_epf_vntb/func1/pci_epf_vntb.0/vbus_number
```


#### 9.9.1.5. Binding pci-epf-ntb Device to EP Controller

NTB 功能设备应附加到与主机相连的 PCI 端点控制器。

```bash
# ln -s controllers/5f010000.pcie_ep functions/pci-epf-ntb/func1/primary
```

一旦完成上述步骤，PCI端点控制器就准备好与主机建立链接。


#### 9.9.1.6. Start the Link

为了使端点设备与主机建立链接，_start_字段应填充为“1”。 对于 NTB，两个 PCI 端点控制器都应与主机建立链接（imx8 不需要此步骤）：

```bash
# echo 1 > controllers/5f010000.pcie_ep/start
```


### 9.9.2. RootComplex Device

#### 9.9.2.1. lspci Output at Host side

请注意，此处列出的设备对应于上面“创建 pci-epf-ntb 设备”部分中填充的值：

```bash
# lspci
00:00.0 PCI bridge: Freescale Semiconductor Inc Device 0000 (rev 01)
01:00.0 RAM memory: Freescale Semiconductor Inc Device 0809
```


### 9.9.3. Endpoint Device / Virtual PCI bus

#### 9.9.3.1. lspci Output at EP Side / Virtual PCI bus

请注意，此处列出的设备对应于上面“创建 pci-epf-ntb 设备”部分中填充的值：

```bash
# lspci
10:00.0 Unassigned class [ffff]: Dawicontrol Computersysteme GmbH Device 1234 (rev ff)
```


#### 9.9.3.2. Using ntb_hw_epf Device

主机端软件遵循Linux中标准NTB软件架构。 所有现有的客户端 NTB 实用程序（例如 NTB Transport Client 和 NTB Netdev、NTB Ping Pong Test Client 和 NTB Tool Test Client）都可以与 NTB 功能设备一起使用。

有关 NTB 的更多信息，请参阅 [Non-Transparent Bridge](https://www.kernel.org/doc/html/latest/driver-api/ntb.html).


# 9.10 PCI Test Endpoint Function

name: 应为“pci_epf_test”以绑定到 pci_epf_test 驱动程序。

可配置字段：

|                  |                                                |
| ---------------- | ---------------------------------------------- |
| vendorid         | 应该是 0x104c                                   |
| deviceid         | 对于 DRA74x 应为 0xb500，对于 DRA72x 应为 0xb501 |
| revid            | 不关心                                          |
| progif_code      | 不关心                                          |
| subclass_code    | 不关心                                          |
| baseclass_code   | 应该是 0xff                                     |
| cache_line_size  | 不关心                                          |
| subsys_vendor_id | 不关心                                          |
| subsys_id        | 不关心                                          |
| interrupt_pin    | 应该是 1 - INTA, 2 - INTB, 3 - INTC, 4 -INTD    |
| msi_interrupts   | 应为 1 到 32，具体取决于要测试的 MSI 中断数量     |
| msix_interrupts  | 应为 1 到 2048，具体取决于要测试的 MSI-X 中断数量 |


# 9.11 PCI NTB Endpoint Function

1. 在 configfs 中创建 pci_epf_ntb 目录的子目录。

标准 EPF 可配置字段：

|                  |                                 |
| ---------------- | ------------------------------- |
| vendorid         | 应该是 0x104c                    |
| deviceid         | 对于 TI 的 J721E SoC 应为 0xb00d |
| revid            | 不关心                           |
| progif_code      | 不关心                           |
| subclass_code    | 应该是 0x00                      |
| baseclass_code   | 应该是 0x5                       |
| cache_line_size  | 不关心                           |
| subsys_vendor_id | 不关心                           |
| subsys_id        | 不关心                           |
| interrupt_pin    | 不关心                           |
| msi_interrupts   | 不关心                           |
| msix_interrupts  | 不关心                           |

2. 在 1 中创建的目录中创建一个子目录。

NTB EPF 特定可配置字段：

|            |                            |
| ---------- | -------------------------- |
| db_count   | 门铃数量； 默认 = 4         |
| mw1        | 内存窗口1的大小             |
| mw2        | 内存窗口2的大小             |
| mw3        | 内存窗口3的大小             |
| mw4        | 内存窗口4的大小             |
| num_mws    | 内存窗口数量； 最大值 = 4    |
| spad_count | 暂存器寄存器数量； 默认 = 64 |
