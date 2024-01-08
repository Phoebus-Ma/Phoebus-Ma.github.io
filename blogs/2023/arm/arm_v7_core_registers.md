
# ARMv7 core registers

| Registers | User    | System | Hyp      | Supervisor | Abort    | Undefined | Monitor  | IRQ      | FIQ      |
| --------- | ------- | ------ | -------- | ---------- | -------- | --------- | -------- | -------- | -------- |
| R0        | R0_usr  |        |          |            |          |           |          |          |          |
| R1        | R1_usr  |        |          |            |          |           |          |          |          |
| R2        | R2_usr  |        |          |            |          |           |          |          |          |
| R3        | R3_usr  |        |          |            |          |           |          |          |          |
| R4        | R4_usr  |        |          |            |          |           |          |          |          |
| R5        | R5_usr  |        |          |            |          |           |          |          |          |
| R6        | R6_usr  |        |          |            |          |           |          |          |          |
| R7        | R7_usr  |        |          |            |          |           |          |          |          |
| R8        | R8_usr  |        |          |            |          |           |          |          | R8_fiq   |
| R9        | R9_usr  |        |          |            |          |           |          |          | R9_fiq   |
| R10       | R10_usr |        |          |            |          |           |          |          | R10_fiq  |
| R11       | R11_usr |        |          |            |          |           |          |          | R11_fiq  |
| R12       | R12_usr |        |          |            |          |           |          |          | R12_fiq  |
| SP        | SP_usr  |        | SP_hyp   | SP_svc     | SP_abt   | SP_und    | SP_mon   | SP_irq   | SP_fiq   |
| LR        | LP_usr  |        |          | LR_svc     | LR_abt   | LR_und    | LR_mon   | LR_irq   | LR_fiq   |
| PC        | PC      |        |          |            |          |           |          |          |          |
| APSR      | CPSR    | CPSR   | CPSR     | CPSR       | CPSR     | CPSR      | CPSR     | CPSR     | CPSR     |
|           |         |        | SPSR_hyp | SPSR_svc   | SPSR_abt | SPSR_und  | SPSR_mon | SPSR_irq | SPSR_fiq |
|           |         |        | ELR_hyp  |            |          |           |          |          |          |

- 内核寄存器    : R0-R2
- 堆栈指针寄存器: SP
- 链路寄存器    : LR
- 程序计数寄存器: PC
- 程序状态寄存器：CPSR，SPSR，ELR_hyp

如获取异常的处理器模式中所述，在获取异常时，处理器会更改模式，除非它已经处于必须获取异常的模式。 处理器以这种方式可能进入的每种模式都有：

堆栈指针的分组副本，例如 SP_irq 和 SP_hyp。

保存异常的首选返回地址的寄存器。 这是：

对于每个 PL1 模式，链接寄存器的分组副本，例如 LR_und 和 LR_mon。

对于PL2模式、Hyp模式，特殊寄存器ELR_hyp。

已保存的 CPSR 副本，在异常条目时创建，例如 SPSR_irq 和 SPSR_hyp。

此外，FIQ 模式还具有 ARM 核心寄存器 R8 至 R12 的 Banked 副本。

用户模式和系统模式共享相同的 ARM 内核寄存器。

用户模式、系统模式和Hyp模式共享相同的LR。

有关 SP、LR 和 PC 的应用程序级视图以及它们的替代描述（R13、R14 和 R15）的更多信息。

ARM内核寄存器伪代码：

```c
// The names of the Banked core registers.

enumeration RName {RName_0usr, RName_1usr, RName_2usr, RName_3usr, RName_4usr, RName_5usr,
                   RName_6usr, RName_7usr, RName_8usr, RName_8fiq, RName_9usr, RName_9fiq,
                   RName_10usr, RName_10fiq, RName_11usr, RName_11fiq, RName_12usr, RName_12fiq,
                   RName_SPusr, RName_SPfiq, RName_SPirq, RName_SPsvc,
                   RName_SPabt, RName_SPund, RName_SPmon, RName_SPhyp,
                   RName_LRusr, RName_LRfiq, RName_LRirq, RName_LRsvc,
                   RName_LRabt, RName_LRund, RName_LRmon,
                   RName_PC};

```
