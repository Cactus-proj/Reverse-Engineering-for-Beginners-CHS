# Appendix F
# Cheatsheets

## F.1 IDA


key     | meaning
:-------|:---------------------------------------------
Space   | switch listing and graph view
C       | convert to code
D       | convert to data
A       | convert to string
\*      | convert to array
U       | undefine
O       | make offset of operand
H       | make decimal number
R       | make char
B       | make binary number
Q       | make hexadecimal number
N       | rename identificator
?       | calculator
G       | jump to address
:       | add comment
Ctrl-X  | show references to the current function, label, variable (incl. in local stack)
X       | show references to the function, label, variable,etc.
Alt-I   | search for constant
Ctrl-I  | search for the next occurrence of constant
Alt-B   | search for byte sequence
Ctrl-B  | search for the next occurrence of byte sequence
Alt-T   | search for text (including instructions, etc)
Ctrl-T  | search for the next occurrence of text
Alt-P   | edit current function
Enter   | jump to function, variable, etc
Esc     | get back
Num -   | fold function or selected area
Num +   | unhide function or area


## F.2 OllyDbg

hot-key | meaning
:-------|:-------------
F7      | trace into
F8      | step over
F9      | run
Ctrl-F2 | restart

## F.3 MSVC

option      | meaning
:-----------|:----------------------------------------
/O1         | minimize space
/Ob0        | no inline expansion
/Ox         | maximum optimizations
/GS-        | disable security checks (buffer overflows)
/Fa(file)   | generate assembly listing
/Zi         | enable debugging information
/Zp(n)      | pack structs on n-byte boundary
/MD         | produced executable will use MSVCR*.DLL

## F.4 GCC

option      | meaning
:-----------|:--------------------------
-Os         | code size optimization
-O3         | maximum optimization
-regparm=   | how many arguments are to be passed in registers
-o          | file set name of output file
-g          | produce debugging information in resulting executable
-S          | generate assembly listing file
-masm=intel | produce listing in Intel syntax
-fno-inline | do not inline functions

## F.5 GDB

option                          | meaning
:-------------------------------|:------------------------------------------------
break filename.c:number         | set a breakpoint on line number in source code
break function                  | set a breakpoint on function
break \*address                 | set a breakpoint on address
****                            | —”—
p variable                      | print value of variable
run                             | run
r                               | —”—
cont                            | continue execution
c                               | —”—
bt                              | print stack
set disassembly-flavor intel    | set Intel syntax
disas                           | disassemble current function
disas function                  | disassemble function
disas function,+50              | disassemble portion
disas $eip,+0x10                | —”—
disas/r                         | disassemble with opcodes
info registers                  | print all registers
info float                      | print FPU-registers
info locals                     | dump local variables (if known)
x/w ...                         | dump memory as 32-bit word
x/w $rdi                        | dump memory as 32-bit word at address stored in RDI
x/10w ...                       | dump 10 memory words
x/s ...                         | dump memory as string
x/i ...                         | dump memory as code
x/10c ...                       | dump 10 characters
x/b ...                         | dump bytes
x/h ...                         | dump 16-bit halfwords
x/g ...                         | dump giant (64-bit) words
finish                          | execute till the end of function
next                            | next instruction (don’t dive into functions)
step                            | next instruction (dive into functions)
set step-mode on                | do not use line number information while stepping
frame n                         | switch stack frame
info break                      | list of breakpoints
del n                           | set command-line arguments
