# 第二十章
# 用线性同余生成器来产生伪随机数

```
#include <stdint.h>

// constants from the Numerical Recipes book
#define RNG_a 1664525
#define RNG_c 1013904223

static uint32_t rand_state;

void my_srand (uint32_t init)
{
        rand_state=init;
}

int my_rand ()
{
	rand_state=rand_state*RNG_a;
	rand_state=rand_state+RNG_c;
	return rand_state & 0x7fff;
}
```
## 20.1 x86
Listing 20.1: Optimizing MSVC 2013
```
_BSS	SEGMENT
_rand_state DD	01H DUP (?)
_BSS	ENDS

_init$ = 8
_srand	PROC
	mov	eax, DWORD PTR _init$[esp-4]
	mov	DWORD PTR _rand_state, eax
	ret	0
_srand	ENDP

_TEXT	SEGMENT
_rand	PROC
	imul	eax, DWORD PTR _rand_state, 1664525
	add	eax, 1013904223		; 3c6ef35fH
	mov	DWORD PTR _rand_state, eax
	and	eax, 32767		; 00007fffH
	ret	0
_rand	ENDP

_TEXT	ENDS
```


Listing 20.2: Non-optimizing MSVC 2013
```
_BSS	SEGMENT
_rand_state DD	01H DUP (?)
_BSS	ENDS

_init$ = 8
_srand	PROC
	push	ebp
	mov	ebp, esp
	mov	eax, DWORD PTR _init$[ebp]
	mov	DWORD PTR _rand_state, eax
	pop	ebp
	ret	0
_srand	ENDP

_TEXT	SEGMENT
_rand	PROC
	push	ebp
	mov	ebp, esp
	imul	eax, DWORD PTR _rand_state, 1664525
	mov	DWORD PTR _rand_state, eax
	mov	ecx, DWORD PTR _rand_state
	add	ecx, 1013904223		; 3c6ef35fH
	mov	DWORD PTR _rand_state, ecx
	mov	eax, DWORD PTR _rand_state
	and	eax, 32767		; 00007fffH
	pop	ebp
	ret	0
_rand	ENDP

_TEXT	ENDS
```

## 20.2 x64
Listing 20.3: Optimizing MSVC 2013 x64
```
_BSS	SEGMENT
rand_state DD	01H DUP (?)
_BSS	ENDS

init$ = 8
my_srand PROC
; ECX = input argument
	mov	DWORD PTR rand_state, ecx
	ret	0
my_srand ENDP

_TEXT	SEGMENT
my_rand	PROC
	imul	eax, DWORD PTR rand_state, 1664525	; 0019660dH
	add	eax, 1013904223				; 3c6ef35fH
	mov	DWORD PTR rand_state, eax
	and	eax, 32767				; 00007fffH
	ret	0
my_rand	ENDP

_TEXT	ENDS
```

## 20.3 32-bit ARM
Listing 20.4: Optimizing Keil 6/2013 (ARM mode)
```
my_srand PROC
        LDR      r1,|L0.52|  ; load pointer to rand_state
        STR      r0,[r1,#0]  ; save rand_state
        BX       lr
        ENDP

my_rand PROC
        LDR      r0,|L0.52|  ; load pointer to rand_state
        LDR      r2,|L0.56|  ; load RNG_a
        LDR      r1,[r0,#0]  ; load rand_state
        MUL      r1,r2,r1
        LDR      r2,|L0.60|  ; load RNG_c
        ADD      r1,r1,r2
        STR      r1,[r0,#0]  ; save rand_state
; AND with 0x7FFF:
        LSL      r0,r1,#17
        LSR      r0,r0,#17
        BX       lr
        ENDP

|L0.52|
        DCD      ||.data||
|L0.56|
        DCD      0x0019660d
|L0.60|
        DCD      0x3c6ef35f

        AREA ||.data||, DATA, ALIGN=2

rand_state
        DCD      0x00000000
```

## 20.4 MIPS

Listing 20.5: Optimizing GCC 4.4.5 (IDA)
```
my_srand:
; store \$a0 to rand_state:
                lui     $v0, (rand_state >> 16)
                jr      $ra
                sw      $a0, rand_state
my_rand:
; load rand\_state to \$v0:
                lui     $v1, (rand_state >> 16)
                lw      $v0, rand_state
                or      $at, $zero  ; load delay slot
; multiplicate rand\_state in \$v0 by 1664525 (RNG_a):
                sll     $a1, $v0, 2
                sll     $a0, $v0, 4
                addu    $a0, $a1, $a0
                sll     $a1, $a0, 6
                subu    $a0, $a1, $a0
                addu    $a0, $v0
                sll     $a1, $a0, 5
                addu    $a0, $a1
                sll     $a0, 3
                addu    $v0, $a0, $v0
                sll     $a0, $v0, 2
                addu    $v0, $a0
; add 1013904223 (RNG_c)
; the LI instruction is coalesced by IDA from LUI and ORI
                li      $a0, 0x3C6EF35F
                addu    $v0, $a0
; store to rand_state:
                sw      $v0, (rand_state & 0xFFFF)($v1)
                jr      $ra
                andi    $v0, 0x7FFF ; branch delay slot
```

```
#define RNG_a 1664525

int f (int a)
{
	return a*RNG_a;
}
```

Listing 20.6: Optimizing GCC 4.4.5 (IDA)
```
f:
                sll     $v1, $a0, 2
                sll     $v0, $a0, 4
                addu    $v0, $v1, $v0
                sll     $v1, $v0, 6
                subu    $v0, $v1, $v0
                addu    $v0, $a0
                sll     $v1, $v0, 5
                addu    $v0, $v1
                sll     $v0, 3
                addu    $a0, $v0, $a0
                sll     $v0, $a0, 2
                jr      $ra
                addu    $v0, $a0, $v0 ; branch delay slot
```

### 20.4.1 MIPS relocations
Listing 20.7: Optimizing GCC 4.4.5 (objdump)
```
# objdump -D rand_O3.o

...

00000000 <my_srand>:
   0:   3c020000        lui     v0,0x0
   4:   03e00008        jr      ra
   8:   ac440000        sw      a0,0(v0)

0000000c <my_rand>:
   c:   3c030000        lui     v1,0x0
  10:   8c620000        lw      v0,0(v1)
  14:   00200825        move    at,at
  18:   00022880        sll     a1,v0,0x2
  1c:   00022100        sll     a0,v0,0x4
  20:   00a42021        addu    a0,a1,a0
  24:   00042980        sll     a1,a0,0x6
  28:   00a42023        subu    a0,a1,a0
  2c:   00822021        addu    a0,a0,v0
  30:   00042940        sll     a1,a0,0x5
  34:   00852021        addu    a0,a0,a1
  38:   000420c0        sll     a0,a0,0x3
  3c:   00821021        addu    v0,a0,v0
  40:   00022080        sll     a0,v0,0x2
  44:   00441021        addu    v0,v0,a0
  48:   3c043c6e        lui     a0,0x3c6e
  4c:   3484f35f        ori     a0,a0,0xf35f
  50:   00441021        addu    v0,v0,a0
  54:   ac620000        sw      v0,0(v1)
  58:   03e00008        jr      ra
  5c:   30427fff        andi    v0,v0,0x7fff

...

# objdump -r rand_O3.o

...

RELOCATION RECORDS FOR [.text]:
OFFSET   TYPE              VALUE
00000000 R_MIPS_HI16       .bss
00000008 R_MIPS_LO16       .bss
0000000c R_MIPS_HI16       .bss
00000010 R_MIPS_LO16       .bss
00000054 R_MIPS_LO16       .bss

...
```
## 其他线程安全的版本的例子
