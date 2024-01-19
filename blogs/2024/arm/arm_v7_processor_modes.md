
# ARMv7 Processor Modes

| Processor mode | Abbr | Encoding | Privilege level | Implemented                    | Security state  |
| -------------- | ---- | -------- | --------------- | ------------------------------ | --------------- |
| User           | usr  | 10000    | PL0             | Always                         | Both            |
| FIQ            | fiq  | 10001    | PL1             | Always                         | Both            |
| IRQ            | irq  | 10010    | PL1             | Always                         | Both            |
| Supervisor     | svc  | 10011    | PL1             | Always                         | Both            |
| Monitor        | mon  | 10110    | PL1             | With Security Extensions       | Secure only     |
| Abort          | abt  | 10111    | PL1             | Always                         | Both            |
| Hyp            | hyp  | 11010    | PL2             | With Virtualization Extensions | Non-secure only |
| Undefined      | und  | 11011    | PL1             | Always                         | Both            |
| System         | sys  | 11111    | PL1             | Always                         | Both            |

模式更改可以在软件控制下进行，也可以由外部或内部异常引起。


## 1. User mode

操作系统在用户模式下运行应用程序以限制系统资源的使用。 在用户模式下执行的软件在 PL0 处执行。 用户模式下的执行有时被描述为非特权执行。 应用程序通常在用户模式下执行，并且任何在用户模式下执行的程序：

- 仅对系统资源进行非特权访问，这意味着它无法访问受保护的系统资源；

- 仅对内存进行非特权访问；

- 除非引发异常，否则无法更改模式。


## 2. FIQ mode

FIQ 模式是 FIQ 中断的默认模式。


## 3. IRQ mode

IRQ 模式是 IRQ 中断的默认模式。


## 4. Supervisor mode

管理员模式是发生管理员调用异常时的默认模式。

执行 SVC（主管调用）指令会生成主管调用异常，该异常将进入主管模式。

处理器在复位时进入管理模式。


## 5. Monitor mode

监控模式是发生安全监控调用异常的模式。

在PL1模式下，执行SMC（Secure Monitor Call）指令会产生Secure Monitor Call异常。

监控模式是一种安全模式，这意味着无论 SCR.NS 位的值如何，它始终处于安全状态。

在监控模式下运行的软件可以访问系统寄存器的安全和非安全副本。 这意味着监控模式提供了在安全和非安全安全状态之间进行更改的正常方法。

**Note**

区分以下两者很重要：

- Monitor mode

这是一种处理器模式，仅当实现包含安全扩展时才可用。 它在正常操作中使用，作为在安全和非安全状态之间转换的机制，如本节所述。

- Monitor debug-mode

这是一种调试模式，无论实现是否包含安全扩展，都可用。

监控模式仅作为安全扩展的一部分实现。


## 6. Abort mode

中止模式是发生数据中止异常或预取中止异常时的默认模式。


## 7. Hyp mode

Hyp 模式是非安全 PL2 模式，作为虚拟化扩展的一部分实现。 Hyp 模式是在非安全状态出现异常时进入的，必须进入 PL2。

管理程序调用异常和 Hyp 陷阱异常是作为虚拟化扩展的一部分实现的异常，并且始终在 Hyp 模式下发生。

**Note**

这意味着无法从安全状态获取管理程序调用异常和 Hyp Trap 异常。

在非安全 PL1 模式下，执行 HVC（Hypervisor Call）指令会生成 Hypervisor Call 异常。


## 8. Undefined mode

未定义模式是与指令相关的异常（包括任何执行未定义指令的尝试）所采取的默认模式。


## 9. System mode

在系统模式下执行的软件在 PL1 上执行。 系统模式具有与用户模式相同的可用寄存器，并且不会因任何异常而进入。


## Secure and Non-secure modes

在实现安全扩展的处理器中，大多数模式名称可以限定为安全或非安全，以指示处理器是否也处于安全状态或非安全状态。 例如：

如果处理器处于 Supervisor 模式和 Secure 状态，则它处于 Secure Supervisor 模式。

如果处理器处于用户模式和非安全状态，则它处于非安全用户模式。

**Note**

如相应模式描述中所示：

监控模式是一种安全模式，意味着它始终处于安全状态。

Hyp 模式是一种非安全模式，这意味着它只能在非安全状态下访问。


## Pseudocode details of mode operations

```c
// BadMode()
// =========

boolean BadMode(bits(5) mode)
    case mode of
        when '10000'  result = FALSE;                // User mode
        when '10001'  result = FALSE;                // FIQ mode
        when '10010'  result = FALSE;                // IRQ mode
        when '10011'  result = FALSE;                // Supervisor mode
        when '10110'  result = !HaveSecurityExt();   // Monitor mode
        when '10111'  result = FALSE;                // Abort mode
        when '11010'  result = !HaveVirtExt();       // Hyp mode
        when '11011'  result = FALSE;                // Undefined mode
        when '11111'  result = FALSE;                // System mode
        otherwise     result = TRUE;
    return result;
```
