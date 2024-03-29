
# Mellanox watchdog drivers

# 1. for x86 based system switches

该驱动程序为各种 Mellanox 以太网和 Infiniband 交换机系统提供看门狗功能。

Mellanox 看门狗器件是在可编程逻辑器件中实现的。

有两种类型的硬件看门狗实现。

**Type 1:**

&emsp;实际的硬件超时可以定义为 2 毫秒的幂。 例如 超时 20 秒将四舍五入为 32768 毫秒。 最大超时时间为 32 秒（32768 毫秒），不支持获取剩余时间。

**Type 2:**

&emsp;实际硬件超时以秒为单位定义。 它与用户定义的超时相同。 最大超时时间为 255 秒。 支持获取剩余时间。

**Type 3:**

&emsp;与类型 2 相同，但最大超时时间延长。 最大超时为 65535 秒。

旧系统中存在类型 1 硬件看门狗实现，所有新系统都具有类型 2 硬件看门狗。 两种类型的硬件实现也具有不同的寄存器映射。

类型 3 硬件看门狗实现可以存在于具有新编程器逻辑设备的所有 Mellanox 系统上。 它通过 WD 功能位来区分。 旧系统仍然只有一个主要看门狗。

Mellanox 系统可以有 2 个看门狗：主看门狗和辅助看门狗。 主看门狗设备和辅助看门狗设备可以在同一系统上同时启用。 看门狗中可以定义多种操作：系统重置、全速启动风扇以及增加寄存器计数器。 最后 2 个操作无需系统重置即可执行。 辅助看门狗装置提供无需复位的动作，这是可选的。 看门狗可以在探测期间启动，在这种情况下，在用户空间应用程序打开看门狗设备之前，看门狗核心将对其执行 ping 操作。 看门狗可以以无出路的方式初始化，即一旦启动就无法停止。

此 mlx-wdt 驱动程序支持两种硬件看门狗实现。

从通用 mlx_platform 驱动程序探测看门狗驱动程序。 Mlx_platform 驱动程序为 Mellanox 看门狗设备、身份名称（mlx-wdt-main 或 mlx-wdt-aux）、初始超时、到期时执行的操作和配置标志提供一组适当的寄存器。 看门狗配置标志：nowayout 和 start_at_boot，硬件看门狗版本 - type1 或 type2。 驱动程序在初始化期间检查先前的系统重置是否由看门狗完成。 如果是，它会发出有关此事件的通知。

对硬件寄存器的访问是通过通用寄存器映射接口执行的。 可编程逻辑设备寄存器具有小端顺序。
