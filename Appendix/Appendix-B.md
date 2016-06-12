# Appendix B
# ARM

## B.1 术语

**byte** 8-bit. The DB assembly 

**directive** is used for defining variables and arrays of bytes.

**halfword** 16-bit. DCW assembly directive —”—.

**word** 32位，DCD assembly directive —”—.

**doubleword** 64位。

**quadword** 128位。


## B.2 版本

* ARMv4: Thumb mode introduced.
* ARMv6: used in iPhone 1st gen., iPhone 3G (Samsung 32-bit RISC ARM 1176JZ(F)-S that supports Thumb-2)
* ARMv7: Thumb-2 was added (2003). was used in iPhone 3GS, iPhone 4, iPad 1st gen. (ARM Cortex-A8), iPad 2 (Cortex-
A9), iPad 3rd gen.
* ARMv7s: New instructions added. Was used in iPhone 5, iPhone 5c, iPad 4th gen. (Apple A6).
* ARMv8: 64-bit CPU, AKA ARM64 AKA AArch64. Was used in iPhone 5S, iPad Air (Apple A7). There is no Thumb mode
in 64-bit mode, only ARM (4-byte instructions).

## B.3 32-bit ARM (AArch32)

### B.3.1 通用寄存器

* R0— function result is usually returned using R0
* R1...R12—GPRs
* R13—AKA SP (stack pointer)
* R14—AKA LR (link register)
* R15—AKA PC (program counter)


### B.3.2 程序状态寄存器 (CPSR)

![](img\B-1.png)

### B.3.3 VFP (floating point) and NEON registers

![](img\B-2.png)


## B.4 64 -bit ARM (AArch64)

## B.4.1 通用寄存器


* X0— function result is usually returned using X0
* X0...X7—Function arguments are passed here.
* X8
* X9...X15—are temporary registers, the callee function can use and not restore them.
* X16
* X17
* X18
* X19...X29—callee function can use them, but must restore them upon exit.
* X29—used as FP (at least GCC)
* X30—“Procedure Link Register” AKA LR (link register).


![](img\B-3.png)

## B.5 指令


### B.5.1 条件代码表

![](img\B-4.png)