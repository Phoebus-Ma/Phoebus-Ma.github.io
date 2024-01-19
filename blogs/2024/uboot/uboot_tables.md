
# U-boot 各文件目录介绍

以 u-boot-2023.10 为例：

| Directory path    | Usage                                 |
| ----------------- | ------------------------------------- |
| /arch             | 特定于架构的文件                        |
| /arch/arc         | 与ARC架构相关的文件                     |
| /arch/arm         | 与ARM架构相关的文件                     |
| /arch/m68k        | 与 m68k 架构相关的文件                  |
| /arch/microblaze  | 与 microblaze 架构相关的文件            |
| /arch/mips        | 与MIPS架构相关的文件                    |
| /arch/nios2       | 与 Altera NIOS2 架构相关的文件          |
| /arch/powerpc     | 与PowerPC架构相关的文件                 |
| /arch/riscv       | 与 RISC-V 架构相关的文件                |
| /arch/sandbox     | 与独立于硬件的“沙箱”相关的文件           |
| /arch/sh          | 与SH架构相关的文件                      |
| /arch/x86         | 与x86架构相关的文件                     |
| /arch/xtensa      | 与 Xtensa 架构相关的文件                |
| /api              | 用于外部应用程序的独立于机器/架构的 API  |
| /board            | 板级相关文件                           |
| /boot             | 支持镜像和启动                         |
| /cmd              | U-Boot 命令功能                        |
| /common           | 其他与架构无关的功能                    |
| /configs          | 板卡默认配置文件                        |
| /disk             | 磁盘驱动器分区处理代码                  |
| /doc              | 文档（ReST 和自述文件的组合）           |
| /drivers          | 设备驱动程序                           |
| /dts              | 用于构建内部 U-Boot fdt 的 Makefile    |
| /env              | 环境支持                               |
| /examples         | 独立应用程序的示例代码等                |
| /fs               | 文件系统代码（cramfs、ext2、jffs2 等）  |
| /include          | 头文件                                 |
| /lib              | 与所有架构相关的库例程                  |
| /Licenses         | 各种许可证文件                         |
| /net              | 联网代码                               |
| /post             | 开机自检                               |
| /scripts          | 各种构建脚本和 Makefile                 |
| /test             | 各种单元测试文件                        |
| /tools            | 用于构建和签署 FIT 映像等的工具          |
