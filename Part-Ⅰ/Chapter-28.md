# 第二十八章
# 关于ARM的具体细节
## 28.1 Number sign (#) before number


## 28.2 Addressing modes

`ldr	x0, [x29,24]`


`ldr	w4, [x1],28`

C term         | ARM term                | C statement | how it works
:--------------|:------------------------|:------------|:--------------
Post-increment | post-indexed addressing | **\*ptr++** | use **\*ptr** value,then [increment](../Glossary.md) **ptr** pointer
Post-decrement | post-indexed addressing | **\*ptr--** | use **\*ptr** value,then [decrement](../Glossary.md) **ptr** pointer
Pre-increment  | pre-indexed addressing  | **\*++ptr** | [increment](../Glossary.md) **ptr** pointer,then use **\*ptr** value
Pre-decrement  | pre-indexed addressing  | **\*--ptr** | [decrement](../Glossary.md) **ptr** pointer,then use **\*ptr** value

## 28.3 Loading a constant into a register
### 28.3.1 32-bit ARM

```
unsigned int f()
{
	return 0x12345678;
};
```
Listing 28.1: GCC 4.6.3 -O3 ARM mode
```
f:
        ldr     r0, .L2
        bx      lr
.L2:
        .word   305419896 ; 0x12345678
```


Listing 28.2: GCC 4.6.3 -O3 -march=armv7-a (ARM mode)
```
movw    r0, #22136      ; 0x5678
movt    r0, #4660       ; 0x1234
bx      lr
```



```
MOV    R0, 0x12345678
BX     LR
```

### 28.3.2 ARM64

```
uint64_t f()
{
	return 0x12345678ABCDEF01;
};
```

Listing 28.3: GCC 4.9.1 -O3
```
mov	    x0, 61185   ; 0xef01
movk	x0, 0xabcd, lsl 16
movk	x0, 0x5678, lsl 32
movk	x0, 0x1234, lsl 48
ret
```

#### Storing floating-point number into register

```
double a()
{
	return 1.5;
};
```
Listing 28.4: GCC 4.9.1 -O3 + objdump
```
0000000000000000 <a>:
   0:   1e6f1000        fmov    d0, #1.500000000000000000e+000
   4:   d65f03c0        ret
```


```
double a()
{
	return 32;
};
```
Listing 28.5: GCC 4.9.1 -O3
```
a:
	ldr	d0, .LC0
	ret
.LC0:
	.word	0
	.word	1077936128
```


## 28.4 Relocs in ARM64

Listing 28.6: GCC (Linaro) 4.9 and objdump of object file
```
...>aarch64-linux-gnu-gcc.exe hw.c -c

...>aarch64-linux-gnu-objdump.exe -d hw.o

...

0000000000000000 <main>:
   0:   a9bf7bfd        stp     x29, x30, [sp,#-16]!
   4:   910003fd        mov     x29, sp
   8:   90000000        adrp    x0, 0 <main>
   c:   91000000        add     x0, x0, #0x0
  10:   94000000        bl      0 <printf>
  14:   52800000        mov     w0, #0x0                        // #0
  18:   a8c17bfd        ldp     x29, x30, [sp],#16
  1c:   d65f03c0        ret

...>aarch64-linux-gnu-objdump.exe -r hw.o

...

RELOCATION RECORDS FOR [.text]:
OFFSET           TYPE              VALUE
0000000000000008 R_AARCH64_ADR_PREL_PG_HI21  .rodata
000000000000000c R_AARCH64_ADD_ABS_LO12_NC  .rodata
0000000000000010 R_AARCH64_CALL26  printf
```

Listing 28.7: objdump of executable file
```
0000000000400590 <main>:
  400590:       a9bf7bfd        stp     x29, x30, [sp,#-16]!
  400594:       910003fd        mov     x29, sp
  400598:       90000000        adrp    x0, 400000 <_init-0x3b8>
  40059c:       91192000        add     x0, x0, #0x648
  4005a0:       97ffffa0        bl      400420 <puts@plt>
  4005a4:       52800000        mov     w0, #0x0                        // #0
  4005a8:       a8c17bfd        ldp     x29, x30, [sp],#16
  4005ac:       d65f03c0        ret

...

Contents of section .rodata:
 400640 01000200 00000000 48656c6c 6f210000  ........Hello!..
```
