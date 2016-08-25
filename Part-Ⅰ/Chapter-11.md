# 第十一章
# GOTO操作符
#include <stdio.h>


```
int main()
{
	printf ("begin\n");
	goto exit;
	printf ("skip me!\n");
exit:
	printf ("end\n");
};
```



Listing 11.1: MSVC 2012
```
$SG2934	DB	'begin', 0aH, 00H
$SG2936	DB	'skip me!', 0aH, 00H
$SG2937	DB	'end', 0aH, 00H

_main	PROC
	push	ebp
	mov	ebp, esp
	push	OFFSET $SG2934 ; 'begin'
	call	_printf
	add	esp, 4
	jmp	SHORT $exit$3
	push	OFFSET $SG2936 ; 'skip me!'
	call	_printf
	add	esp, 4
$exit$3:
	push	OFFSET $SG2937 ; 'end'
	call	_printf
	add	esp, 4
	xor	eax, eax
	pop	ebp
	ret	0
_main	ENDP
```

![](pic\C11-1.png)
Figure 11.1: Hiew

![](pic\C11-2.png)
Figure 11.2: Hiew   

![](pic\C11-3.png)
Figure 11.3: Patched executable output  

Listing 11.2: Optimizing MSVC 2012
```
$SG2981	DB	'begin', 0aH, 00H
$SG2983	DB	'skip me!', 0aH, 00H
$SG2984	DB	'end', 0aH, 00H

_main	PROC
	push	OFFSET $SG2981 ; 'begin'
	call	_printf
	push	OFFSET $SG2984 ; 'end'
$exit$4:
	call	_printf
	add	esp, 8
	xor	eax, eax
	ret	0
_main	ENDP
```
