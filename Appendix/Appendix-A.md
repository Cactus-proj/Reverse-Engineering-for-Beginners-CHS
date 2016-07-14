# Appendix A
# x86
## A.1 Terminology


Common for 16-bit (8086/80286), 32-bit (80386, etc), 64-bit.
**byte** 8-bit. The DB assembly directive is used for defining variables and arrays of bytes. Bytes are passed in the 8-bit part
of registers: AL/BL/CL/DL/AH/BH/CH/DH/SIL/DIL/R*L.

**word** 16-bit. DW assembly directive —”—. Words are passed in the 16-bit part of the registers: AX/BX/CX/DX/SI/DI/R*W.

**double word** (“dword”) 32-bit. DD assembly directive —”—. Double words are passed in registers (x86) or in the 32-bit
part of registers (x64). In 16-bit code, double words are passed in 16-bit register pairs.

**quad word** (“qword”) 64-bit. DQ assembly directive —”—. In 32-bit environment, quad words are passed in 32-bit register
pairs.

**tbyte** (10 bytes) 80-bit or 10 bytes (used for IEEE 754 FPU registers).

**paragraph** (16 bytes)— term was popular in MS-DOS environment.
Data types of the same width (BYTE, WORD, DWORD) are also the same in Windows API.

## A.2 General purpose registers



### A.2.1 RAX/EAX/AX/AL

![](img\A-1.png)

### A.2.2 RBX/EBX/BX/BL

![](img\A-2.png)

### A.2.3 RCX/ECX/CX/CL

![](img\A-3.png)

### A.2.4 RDX/EDX/DX/DL

![](img\A-4.png)

### A.2.5 RSI/ESI/SI/SIL

![](img\A-5.png)

### A.2.6 RDI/EDI/DI/DIL

![](img\A-6.png)

### A.2.7 R8/R8D/R8W/R8L

![](img\A-7.png)

### A.2.8 R9/R9D/R9W/R9L

![](img\A-8.png)

### A.2.9 R10/R10D/R10W/R10L

![](img\A-9.png)

### A.2.9 R10/R10D/R10W/R10L

![](img\A-10.png)

### A.2.11 R12/R12D/R12W/R12L

![](img\A-11.png)

### A.2.12 R13/R13D/R13W/R13L

![](img\A-12.png)

### A.2.13 R14/R14D/R14W/R14L

![](img\A-13.png)

### A.2.17 RIP/EIP/IP

![](img\A-14.png)

### A.2.17 RIP/EIP/IPL

![](img\A-15.png)

### A.2.16 RBP/EBP/BP/BPL

![](img\A-16.png)

### A.2.17 RIP/EIP/IP

![](img\A-17.png)

```
MOV EAX, ...
JMP EAX
```

```
PUSH value
RET
```

### A.2.18 CS/DS/ES/SS/FS/GS


### A.2.19 Flags register

Bit (mask)       |Abbreviation (meaning)                     | Description
:----------------|:------------------------------------------|:------------------------------------------------
0 (1)            |CF                                         |(Carry) The CLC/STC/CMC instructions are used for setting/resetting/toggling this flag
2 (4)            |PF   (Parity)                              |(17.7.1)[Part-Ⅰ\Chapter-17.md]
4 (0x10)         |AF   (Adjust)                              |
6 (0x40)         |ZF   (Zero)                                |Setting to 0 if the last operation¡¯s result was 0.
7 (0x80)         |SF   (Sign)                                |
8 (0x100)        |TF   (Trap)                                |Used for debugging. If turned on, an exception is to be generated after each instruction¡¯s execution.
9 (0x200)        |IF   (Interrupt enable)                    |Are interrupts enabled. The CLI/STI instructions are used for setting/resetting the flag
10 (0x400)       |DF   (Direction)                           |A directions is set for the REP MOVSx, REP CMPSx, REP LODSx, REP SCASx instructions. The CLD/STD instructions are used for setting/resetting the flag
11 (0x800)       |OF   (Overflow)                            |
12, 13 (0x3000)  |IOPL (I/O privilege level) 80286           |
14 (0x4000)      |NT   (Nested task) 80286                   |
16 (0x10000)     |RF   (Resume) 80386                        |Used for debugging. The CPU ignores the hardware breakpoint in DRx if the flag is set.
17 (0x20000)     |VM   (Virtual 8086 mode) 80386             |
18 (0x40000)     |AC   (Alignment check) 80486               |
19 (0x80000)     |VIF  (Virtual interrupt) Pentium           |
20 (0x100000)    |VIP  (Virtual interrupt pending) Pentium   |
21 (0x200000)    |ID   (Identification) Pentium              |


## A.3 FPU registers

### A.3 FPU registers

Bit    | Abbreviation (meaning)            | Description                                  
:------|:----------------------------------|:---------------------------------------------
0      | IM (Invalid operation Mask)       |
1      | DM (Denormalized operand Mask)    |
2      | ZM (Zero divide Mask)             |
3      | OM (Overflow Mask)                |
4      | UM (Underflow Mask)               |
5      | PM (Precision Mask)               |
7      |IEM (Interrupt Enable Mask)        | Exceptions enabling, 1 by default (disabled)
8, 9   | PC (Precision Control)            | 00 — 24 bits (REAL4) ; 10 — 53 bits (REAL8) ; 11 — 64 bits (REAL10)
10, 11 | RC (Rounding Control)             | 00 — (by default) round to nearest ; 01 — round toward −∞ ; 10 — round toward +∞  11 — round toward 0    
12     | IC (Infinity Control)             | 0 — (by default) treat +∞ and −∞ as unsigned ; 1 — respect both +∞ and −∞

### A.3.2 Status Word

Bit         | Abbreviation (meaning)  | Description                                      
:-----------|:------------------------|:-------------------------------------------------
15          | B (Busy)                | Is FPU do something (1) or results are ready (0)
14          | C3                      |
13, 12, 11  | TOP                     | points to the currently zeroth register
10          | C2                      |
9           | C1                      |
8           | C0                      |
7           | IR (Interrupt Request)  |
6           | SF (Stack Fault)        |
5           | P (Precision)           |
4           | U (Underflow)           |
3           | O (Overflow)            |
2           | Z (Zero)                |
1           | D (Denormalized)        |
0           | I (Invalid operation)   |


### A.3.3 Tag Word

Bit     | Abbreviation (meaning)
:-------|:-----------------------
15, 14  | Tag(7)
13, 12  | Tag(6)
11, 10  | Tag(5)
9, 8    | Tag(4)
7, 6    | Tag(3)
5, 4    | Tag(2)
3, 2    | Tag(1)
1, 0    | Tag(0)



## A.4 SIMD registers
### A.4.1 MMX registers


###　A.4.2 SSE and AVX registers


## A.5 Debugging registers

- DR0 — address of breakpoint \#1
- DR1 — address of breakpoint \#2
- DR2 — address of breakpoint \#3
- DR3 — address of breakpoint \#4
- DR6 — a cause of break is reflected here
- DR7 — breakpoint types are set here

### A.5.1 DR6

Bit (mask)  | Description
:-----------|:------------------------------------
0 (1)       | B0 — breakpoint \#1 was triggered
1 (2)       | B1 — breakpoint \#2 was triggered
2 (4)       | B2 — breakpoint \#3 was triggered
3 (8)       | B3 — breakpoint \#4 was triggered
13 (0x2000) | BD — modification attempt of one of the DRx registers. may be raised if GD is enabled
14 (0x4000) | BS — single step breakpoint (TF flag was set in EFLAGS). Highest priority. Other bits may also be set.
15 (0x8000) | BT (task switch flag)

### A.5.2 DR7

Bit (mask)          | Description
:-------------------|:--------------------------------------------------
0 (1)               | L0 — enable breakpoint \#1 for the current task
1 (2)               | G0 — enable breakpoint \#1 for all tasks
2 (4)               | L1 — enable breakpoint \#2 for the current task
3 (8)               | G1 — enable breakpoint \#2 for all tasks
4 (0x10)            | L2 — enable breakpoint \#3 for the current task
5 (0x20)            | G2 — enable breakpoint \#3 for all tasks
6 (0x40)            | L3 — enable breakpoint \#4 for the current task
7 (0x80)            | G3 — enable breakpoint \#4 for all tasks
8 (0x100)           | LE — not supported since P6
9 (0x200)           | GE — not supported since P6
13 (0x2000)         | GD — exception is to be raised if any MOV instruction tries to modify one of the DRx registers
16,17 (0x30000)     | breakpoint #1: R/W — type
18,19 (0xC0000)     | breakpoint #1: LEN — length
20,21 (0x300000)    | breakpoint #2: R/W — type
22,23 (0xC00000)    | breakpoint #2: LEN — length
24,25 (0x3000000)   | breakpoint #3: R/W — type
26,27 (0xC000000)   | breakpoint #3: LEN — length
28,29 (0x30000000)  | breakpoint #4: R/W — type
30,31 (0xC0000000)  | breakpoint #4: LEN — length

The breakpoint type is to be set as follows (R/W):
- 00 — instruction execution
- 01 — data writes
- 10 — I/O reads or writes (not available in user-mode)
- 11 — on data reads or writes
N.B.: breakpoint type for data reads is absent, indeed.
Breakpoint length is to be set as follows (LEN):
- 00 — one-byte
- 01 — two-byte
- 10 — undefined for 32-bit mode, eight-byte in 64-bit mode
- 11 — four-byte


## A.6 Instructions

### A.6.1 Prefixes

**LOCK**

**LOCK**

**REPE/REPNE**

### A.6.2 Most frequently used instructions

**ADC**

```
; work with 64-bit values: add val1 to val2.
; .lo mean lowest 32 bits, .hi means highest.
ADD val1.lo, val2.lo
ADC val1.hi, val2.hi ; use CF set or cleared at the previous instruction
```

**ADD**
**AND**
**CALL**
**CMP**
**DEC**
**IMUL**
**INC**
**JCXZ, JECXZ, JRCXZ**
**JMP**
**Jcc**


**JAE**
**JA**
**JBE**
**JB**
**JC**
**JE**
**JGE**
**JG**
**JLE**
**JL**
**JNAE**
**JNA**
**JNBE**
**JNB**
**JNC**
**JNE**
**JNGE**
**JNG**
**JNLE**
**JNL**
**JNO**
**JNS**
**JNZ**
**JO**
**JPO**
**JP**
**JS**
**JZ**

**LAHF**
![](img\A-19.png)

**LEAVE**
**LEA**

```
int f(int a, int b)
{
    return a*8+b;
};
```
Listing A.1: Optimizing MSVC 2010
```
_a$ = 8             ; size = 4
_b$ = 12            ; size = 4
_f      PROC
        mov eax, DWORD PTR _b$[esp-4]
        mov ecx, DWORD PTR _a$[esp-4]
        lea eax, DWORD PTR [eax+ecx*8]
        ret 0
_f      ENDP
```

```
int f1(int a)
{
        return a*13;
};
```
Listing A.2: Intel C++ 2011
```
_f1     PROC NEAR
        mov         ecx, DWORD PTR [4+esp]      ; ecx = a
        lea         edx, DWORD PTR [ecx+ecx*8]  ; edx = a*9
        lea         eax, DWORD PTR [edx+ecx*4]  ; eax = a*9 + a*4 = a*13
        ret
```

**MOVSB/MOVSW/MOVSD/MOVSQ**

```
; copy 15 bytes from ESI to EDI
CLD         ; set direction to "forward"
MOV ECX, 3
REP MOVSD   ; copy 12 bytes
MOVSW       ; copy 2 more bytes
MOVSB       ; copy remaining byte
```

**MOVSX**
**MOVZX**
**MOV**
**MUL**
**NEG**
**NOP**
**NOT**
**OR**
**POP**
**PUSH**
**RET**
**SAHF**
![](img\A-20.png)

**SBB**
```
; work with 64-bit values: subtract val2 from val1.
; .lo mean lowest 32 bits, .hi means highest.
SUB val1.lo, val2.lo
SBB val1.hi, val2.hi ; use CF set or cleared at the previous instruction
```
**SCASB/SCASW/SCASD/SCASQ**
```
lea     edi, string
mov     ecx, 0FFFFFFFFh ; scan 2^32 − 1 bytes, i.e., almost "infinitely"
xor     eax, eax        ; 0 is the terminator
repne scasb
add     edi, 0FFFFFFFFh ; correct it

; now EDI points to the last character of the ASCIIZ string.

; lets determine string length'
; current ECX = -1-strlen

not     ecx
dec     ecx

; now ECX contain string length
```
**SHL**
**SHR**
![](img\A-21.png)
**SHRD**
**STOSB/STOSW/STOSD/STOSQ**

```
; store 15 0xAA bytes to EDI
CLD                 ; set direction to "forward"
MOV EAX, 0AAAAAAAAh
MOV ECX, 3
REP STOSD           ; write 12 bytes
STOSW               ; write 2 more bytes
STOSB               ; write remaining byte
```
**SUB**
**TEST**
**XCHG**
**XOR**

input A | input B | output
:-------|:--------|:---------
0       | 0       | 0
0       | 1       | 1
1       | 0       | 1
1       | 1       | 0

### A.6.3 Less frequently used instructions
**BSF** bit scan forward, see also: 25.2 on page 402
**BSR** bit scan reverse
**BSWAP** (byte swap), change value endianness.
**BTC** bit test and complement
**BTR** bit test and reset
**BTS** bit test and set
**BT** bit test
**CBW/CWD/CWDE/CDQ/CDQE** Sign-extend value:
    **CBW** convert byte in AL to word in AX
    **CWD** convert word in AX to doubleword in DX:AX
    **CWDE** convert word in AX to doubleword in EAX
    **CDQ** convert doubleword in EAX to quadword in EDX:EAX
    **CDQE** (x64) convert doubleword in EAX to quadword in RAX

>The process of stretching numbers by extending the sign bit is called sign extension. The 8086
>provides instructions (Fig. 3.29) to facilitate the task of sign extension. These instructions were initially
>named SEX (sign extend) but were later renamed to the more conservative CBW (convert byte to word)
>and CWD (convert word to double word).

**CLD** clear DF flag.
**CLI** (M) clear IF flag
**CMC** (M) toggle CF flag
**CMOVcc** conditional MOV: load if the condition is true. The condition codes are the same as in the Jcc instructions ( A.6.2 on
page 886).
**CMPSB/CMPSW/CMPSD/CMPSQ**

Listing A.3: base\ntos\rtl\i386\movemem.asm
```
; ULONG
; RtlCompareMemory (
;    IN PVOID Source1,
;    IN PVOID Source2,
;    IN ULONG Length
;    )
;
; Routine Description:
;
;   This function compares two blocks of memory and returns the number
;   of bytes that compared equal.
;
; Arguments:
;
;   Source1 (esp+4) - Supplies a pointer to the first block of memory to
;      compare.
;
;   Source2 (esp+8) - Supplies a pointer to the second block of memory to
;      compare.
;
;   Length (esp+12) - Supplies the Length, in bytes, of the memory to be
;      compared.
;
; Return Value:
;
;    The number of bytes that compared equal is returned as the function
;    value. If all bytes compared equal, then the length of the original
;    block of memory is returned.
;
;--

RcmSource1      equ     [esp+12]
RcmSource2      equ     [esp+16]
RcmLength       equ     [esp+20]

CODE_ALIGNMENT
cPublicProc _RtlCompareMemory,3
cPublicFpo 3,0

        push    esi                 ; save registers
        push    edi                 ;
        cld                         ; clear direction
        mov     esi,RcmSource1      ; (esi) -> first block to compare
        mov     edi,RcmSource2      ; (edi) -> second block to compare

;
;   Compare dwords, if any.
;

rcm10:  mov     ecx,RcmLength       ; (ecx) = length in bytes
        shr     ecx,2               ; (ecx) = length in dwords
        jz      rcm20               ; no dwords, try bytes
        repe    cmpsd               ; compare dwords
        jnz     rcm40               ; mismatch, go find byte

;
;   Compare residual bytes, if any.
;

rcm20:  mov     ecx,RcmLength       ; (ecx) = length in bytes
        and     ecx,3               ; (ecx) = length mod 4
        jz      rcm30               ; 0 odd bytes, go do dwords
        repe    cmpsb               ; compare odd bytes
        jnz     rcm50               ; mismatch, go report how far we got

;
;   All bytes in the block match.
;

rcm30:  mov     eax,RcmLength       ; set number of matching bytes
        pop     edi                 ; restore registers
        pop     esi                 ;
        stdRET  _RtlCompareMemory

;
;    When we come to rcm40, esi (and edi) points to the dword after the
;    one which caused the mismatch. Back up 1 dword and find the byte.
;    Since we know the dword didn't match, we can assume one byte won't.
;

rcm40:  sub     esi,4               ; back up
        sub     edi,4               ; back up
        mov     ecx,5               ; ensure that ecx doesn't count out
        repe    cmpsb               ; find mismatch byte

;
;   When we come to rcm50, esi points to the byte after the one that
;   did not match, which is TWO after the last byte that did match.
;

rcm50:  dec     esi                 ; back up
        sub     esi,RcmSource1      ; compute bytes that matched
        mov     eax,esi             ;
        pop     edi                 ; restore registers
        pop     esi                 ;
        stdRET  _RtlCompareMemory

stdENDP _RtlCompareMemory
```

**CPUID**
**DIV**
**IDIV**
**INT**
**INT 3**
**IN**
**IRET**
**LOOP**
**OUT**
**POPA**
**POPCNT**

>This branch of cryptography is fast-paced and very politically charged. Most designs are secret; a
>majority of military encryptions systems in use today are based on LFSRs. In fact, most Cray computers
>(Cray 1, Cray X-MP, Cray Y-MP) have a rather curious instruction generally known as “population count.”
>It counts the 1 bits in a register and can be used both to efficiently calculate the Hamming distance
>between two binary words and to implement a vectorized version of a LFSR. I’ve heard this called the
>canonical NSA instruction, demanded by almost all computer contracts.

**POPF**
**PUSHA**
**PUSHF**
**RCL**
![](img\A-22.png)
**RCR**
![](img\A-23.png)
**ROL/ROR**
![](img\A-24.png)
![](img\A-25.png)
**SAL**
**SAR**
![](img\A-26.png)
**SETcc**
**STC**
**STD**
**STI**
**SYSCALL**
**SYSENTER**
**UD2**


### A.6.4 FPU instructions

**FABS** replace value in ST(0) by absolute value in ST(0)
**FADD** op: ST(0)=op+ST(0)
**FADD** ST(0), ST(i): ST(0)=ST(0)+ST(i)
**FADDP** ST(1)=ST(0)+ST(1); pop one element from the stack, i.e., the values in the stack are replaced by their sum
**FCHS** ST(0)=-ST(0)
**FCOM** compare ST(0) with ST(1)
**FCOM** op: compare ST(0) with op
**FCOMP** compare ST(0) with ST(1); pop one element from the stack
**FCOMPP** compare ST(0) with ST(1); pop two elements from the stack
**FDIVR** op: ST(0)=op/ST(0)
**FDIVR** ST(i), ST(j): ST(i)=ST(j)/ST(i)
**FDIVRP** op: ST(0)=op/ST(0); pop one element from the stack
**FDIVRP** ST(i), ST(j): ST(i)=ST(j)/ST(i); pop one element from the stack
**FDIV** op: ST(0)=ST(0)/op
**FDIV** ST(i), ST(j): ST(i)=ST(i)/ST(j)
**FDIVP** ST(1)=ST(0)/ST(1); pop one element from the stack, i.e., the dividend and divisor values in the stack are replaced by quotient
**FILD** op: convert integer and push it to the stack.
**FIST** op: convert ST(0) to integer op
**FISTP** op: convert ST(0) to integer op; pop one element from the stack
**FLD1** push 1 to stack
**FLDCW** op: load FPU control word ( A.3 on page 882) from 16-bit op.
**FLDZ** push zero to stack
**FLD** op: push op to the stack.
**FMUL** op: ST(0)=ST(0)\*op
**FMUL** ST(i), ST(j): ST(i)=ST(i)\*ST(j)
**FMULP** op: ST(0)=ST(0)\*op; pop one element from the stack
**FMULP** ST(i), ST(j): ST(i)=ST(i)\*ST(j); pop one element from the stack
**FSINCOS** : tmp=ST(0); ST(1)=sin(tmp); ST(0)=cos(tmp)
**FSQRT** : ST(0) = √ST(0)
**FSTCW** op: store FPU control word ( A.3 on page 882) into 16-bit op after checking for pending exceptions.
**FNSTCW** op: store FPU control word ( A.3 on page 882) into 16-bit op.
**FSTSW** op: store FPU status word ( A.3.2 on page 883) into 16-bit op after checking for pending exceptions.
**FNSTSW** op: store FPU status word ( A.3.2 on page 883) into 16-bit op.
**FST** op: copy ST(0) to op 894
**FSTP** op: copy ST(0) to op; pop one element from the stack
**FSUBR** op: ST(0)=op-ST(0)
**FSUBR** ST(0), ST(i): ST(0)=ST(i)-ST(0)
**FSUBRP** ST(1)=ST(0)-ST(1); pop one element from the stack, i.e., the value in the stack is replaced by the difference
**FSUB** op: ST(0)=ST(0)-op
**FSUB** ST(0), ST(i): ST(0)=ST(0)-ST(i)
**FSUBP** ST(1)=ST(1)-ST(0); pop one element from the stack, i.e., the value in the stack is replaced by the difference
**FUCOM** ST(i): compare ST(0) and ST(i)
**FUCOM** compare ST(0) and ST(1)
**FUCOMP** compare ST(0) and ST(1); pop one element from stack.
**FUCOMPP** compare ST(0) and ST(1); pop two elements from stack.The instructions perform just like FCOM, but an exception is raised only if one of the operands is SNaN, while QNaN numbers are processed smoothly.
**FXCH** ST(i) exchange values in ST(0) and ST(i)
**FXCH** exchange values in ST(0) and ST(1)


### A.6.5 Instructions having printable ASCII opcode

ASCII character | hexadecimal code | x86 instruction
:---------------|:-----------------|:-------------------
0               | 30               | XOR
1               | 31               | XOR
2               | 32               | XOR
3               | 33               | XOR
4               | 34               | XOR
5               | 35               | XOR
7               | 37               | AAA
8               | 38               | CMP
9               | 39               | CMP
:               | 3a               | CMP
;               | 3b               | CMP
<               | 3c               | CMP
=               | 3d               | CMP
?               | 3f               | AAS
@               | 40               | INC
A               | 41               | INC
B               | 42               | INC
C               | 43               | INC
D               | 44               | INC
E               | 45               | INC
F               | 46               | INC
G               | 47               | INC
H               | 48               | DEC
I               | 49               | DEC
J               | 4a               | DEC
K               | 4b               | DEC
L               | 4c               | DEC
M               | 4d               | DEC
N               | 4e               | DEC
O               | 4f               | DEC
P               | 50               | PUSH
Q               | 51               | PUSH
R               | 52               | PUSH
S               | 53               | PUSH
T               | 54               | PUSH
U               | 55               | PUSH
V               | 56               | PUSH
W               | 57               | PUSH
X               | 58               | POP
Y               | 59               | POP
Z               | 5a               | POP
[               | 5b               | POP
\               | 5c               | POP
]               | 5d               | POP
^               | 5e               | POP
_               | 5f               | POP
\`              | 60               | PUSHA
a               | 61               | POPA
f               | 66               | (in 32-bit mode) switch to 16-bit operand size
g               | 67               | (in 32-bit mode) switch to 16-bit address size
h               | 68               | PUSH
i               | 69               | IMUL
j               | 6a               | PUSH
k               | 6b               | IMUL
p               | 70               | JO
q               | 71               | JNO
r               | 72               | JB
s               | 73               | JAE
t               | 74               | JE
u               | 75               | JNE
v               | 76               | JBE
w               | 77               | JA
x               | 78               | JS
y               | 79               | JNS
z               | 7a               | JP
