# 术语表

## real number numbers
which may contain a dot. this is float and double in C/C++.

## decrement
Decrease

## increment
Increase

## integral data type
usual numbers, but not a real ones. may be used for passing variables of boolean data type and enumerations.

## product
Multiplication result.

## arithmetic mean
a sum of all values divided by their count .

## stack pointer
A register pointing to a place in the stack.

## tail call
It is when the compiler (or interpreter) transforms the recursion (with which it is possible: tail recursion) into an iteration for efficiency: wikipedia.

## quotient
Division result.

## anti-pattern
Generally considered as bad practice.

## atomic operation
“oo&” stands for “indivisible” in Greek, so an atomic operation is guaranteed not to be interrupted by other threads.

## basic block
a group of instructions that do not have jump/branch instructions, and also don’t have jumps inside the block from the outside. In IDA it looks just like as a list of instructions without empty lines .

## callee
A function being called by another.

## caller
A function calling another.

## compiler intrinsic
A function specific to a compiler which is not an usual library function. The compiler generates a specific machine code instead of a call to it. Often, it’s a pseudofunction for a specific CPU instruction. Read more: ( 90 on page 857).

## CP/M
Control Program for Microcomputers: a very basic disk OS used before MS-DOS.

## dongle
Dongle is a small piece of hardware connected to LPT printer port (in past) or to USB. Its function was similar to a security token, it has some memory and, sometimes, a secret (crypto-)hashing algorithm.

## endianness
Byte order: 31 on page

## GiB
Gibibyte: 230 or 1024 mebibytes or 1073741824 bytes.

## heap
usually, a big chunk of memory provided by the OS so that applications can divide it by themselves as they wish. malloc()/free() work with the heap.

## jump offset
a part of the JMP or Jcc instruction’s opcode, to be added to the address of the next instruction, and this is how
the new PC is calculated. May be negative as well.

## kernel mode
A restrictions-free CPU mode in which the OS kernel and drivers execute. cf. user mode.

## leaf function
A function which does not call any other function.

## link register
(RISC) A register where the return address is usually stored. This makes it possible to call leaf functions without using the stack, i.e., faster.

## loop unwinding
It is when a compiler, instead of generating loop code for n iterations, generates just n copies of the loop body, in order to get rid of the instructions for loop maintenance.

## name mangling
used at least in C++, where the compiler needs to encode the name of class, method and argument types in one string, which will become the internal name of the function. You can read more about it here: [51.1.1 on page 522](Part-Ⅲ/Chapter-51.md#5111-简单的例子).

## NaN
not a number: a special cases for floating point numbers, usually signaling about errors .

## NEON
AKA “Advanced SIMD”—SIMD from ARM.

## NOP
“no operation”, idle instruction.

## NTAPI
API available only in the Windows NT line. Largely not documented by Microsoft.

## PDB
(Win32) Debugging information file, usually just function names, but sometimes also function arguments and local
variables names.

## POKE
BASIC language instruction for writing a byte at a specific address.

## register allocator
The part of the compiler that assigns CPU registers to local variables.

## reverse engineering
act of understanding how the thing works, sometimes in order to clone it.

## security cookie
A random value, different at each execution. You can read more about it here: [18.3 on page 268](Part-Ⅰ/Chapter-18.md#183-防止缓冲区溢出的方法).

## stack frame
A part of the stack that contains information specific to the current function: local variables, function arguments,
RA, etc.

## stdout
standard output.

## thunk function
Tiny function with a single role: call another function.

## tracer
My own simple debugging tool. You can read more about it here: [70.3 on page 703](Part-Ⅶ/Chapter-70.md#703-tracer).

## user mode
A restricted CPU mode in which it all application software code is executed. cf. kernel mode.

## Windows NT
Windows NT, 2000, XP, Vista, 7, 8.

## word
data type fitting in GPR. In the computers older than PCs, the memory size was often measured in words rather than
bytes.

## xoring
often used in the English language, which implying applying the XOR operation.
