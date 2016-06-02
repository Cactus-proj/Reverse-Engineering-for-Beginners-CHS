# 第三章
# Hello,world!

让我们用《C语言程序设计》中最著名的代码例子开始吧：

```
#include <stdio.h>
int main() {
    printf("hello, world");
    return 0;
};
```

## 3.1 x86

### 3.1.1 MSVC

在MSVC 2010中编译一下：

`cl 1.cpp /Fa1.asm`
 
（/Fa 选项表示让编译器生产汇编列表文件）
清单 3.1: MSVC 2010

```
CONST   SEGMENT
$SG3830 DB     'hello, world', 00H
CONST   ENDS
PUBLIC  _main
EXTRN   _printf:PROC
; Function compile flags: /Odtp
_TEXT   SEGMENT
_main   PROC
    	push 	ebp
    	mov 	ebp, esp
    	push 	OFFSET $SG3830
    	call 	_printf
    	add 	esp, 4
    	xor 	eax, eax
    	pop 	ebp
    	ret 	0
_main   ENDP
_TEXT   ENDS
```

MSVC生成的是Intel汇编语法。Intel语法与AT&T语法的区别将会在后面讨论。

编译器会把`1.obj`文件连接成`1.exe`。

在我们的例子当中，文件包含两个部分：`CONST`（放数据）和`_TEXT`（放代码）。

字符串`"hello,world"`在C/C++ 类型为`const char[]`，然而它没有自己的变量名。

编译器需要以某种方法处理这个字符串，于是就自己给他定义了一个内部名称`$SG3830`。

所以例子可以改写为：

```
#include <stdio.h>

const char $SG3830[]="hello, world\n";

int main() 
{
    printf($SG3830);
    return 0;
}
```

我们回到汇编代码，正如我们看到的，字符串是由0字节结束的，这也是C/C++的标准。

在代码部分，`_TEXT`，只有一个函数：main()。函数main()与大多数函数一样都有开始的代码与结束的代码。

函数当中的开始代码结束以后，调用了printf()函数：`CALL _printf`。在被调用之前，保存我们问候语字符串的地址（或指向它的指针），已经在PUSH指令的帮助下，被存放在栈当中。

当printf()函数执行完返回到main()函数的时候，字符串地址(或指向它的指针)仍然在堆栈中。当我们完全不需要它的时候，堆栈指针（ESP寄存器）需要被修正。

`ADD ESP, 4`意思是ESP寄存器的值加4。

为什么是4呢？因为这是32位的程序，通过栈传送地址刚好需要4个字节。如果是64位的代码则需要8字节。`ADD ESP, 4`在效率上等同于`POP register`。

一些编辑器（如Intel C++编译器）在同样的情况下可能会用`POP ECX`代替ADD（例如这样的模式可以在Oracle RDBMS代码中看到，因为它是由Intel C++编译器编译的），这条指令的效果基本相同，但是ECX的寄存器内容会被改写。Intel C++编译器可能用`POP ECX`，因为这比`ADD ESP, X`需要的字节数更短，（1字节对应3字节）。

这里有一个在Oracle RDBMS中用`POP`而不用`ADD`的例子。
清单 3.2: Oracle RDBMS 10.2 Linux (app.o 文件)
```
.text:0800029A 		push 	ebx
.text:0800029B 		call 	qksfroChild
.text:080002A0 		pop 	ecx
```

在调用printf()之后，原来的C/C++代码执行`return 0`，返回0当做main()函数的返回结果。在生成的代码中，这被编译成指令`XOR EAX, EAX`。

XOR事实上就是异或，但是编译器经常用它来代替`MOV EAX, 0`原因就是它需要的字节更短（`XOR`需要2字节对应`MOV`需要5字节）。

有些编译器则用`SUB EAX, EAX` 就是EXA的值减去EAX，也就是返回0。

最后的`RET`指令返回控制权给调用者。通常这是C/C++的库函数代码，它会按顺序，把控制权返还给操作系统。

### 3.1.2 GCC

现在我们尝试同样的C/C++代码在linux中的GCC 4.4.1编译

`gcc 1.c -o 1`

下一步，在IDA反汇编的帮助下，我们看看main()函数是如何被创建的。IDA与MSVC一样，也是显示Intel语法。

我们也可以是GCC生成Intel语法的汇编代码，添加参数`-S -masm=intel`

代码清单 3.3:IDA里的代码

```
main proc near
var_10          = dword ptr -10h
    push ebp
    mov  ebp, esp
    and  esp, 0FFFFFFF0h
    sub  esp, 10h
    mov  eax, offset aHelloWorld ;` `"hello, world"
    mov [esp+10h+var_10], eax
    call _printf
    mov eax, 0
    leave
    retn
main            endp
```

结果几乎是相同的，`"hello,world"`字符串地址（保存在data段的）一开始保存在EAX寄存器当中，然后保存到栈当中。

此外在函数开始我们看到了`AND ESP, 0FFFFFFF0h`，这条指令将ESP寄存器中的值对齐为16字节。这让堆栈中的所有值，都被以相同的方式对齐。(如果分配的内存地址大小被对齐为4或16字节，CPU的性能会更好。)

`SUB ESP，10H`在栈上分配16个字节。 虽然在下面我们可以看到，这里其实只需要4个字节。

这是因为分配的堆栈的大小也被对齐为16位。

该字符串的地址（或这个字符串指针），不使用PUSH指令，直接存入到堆栈空间。`var_10`，是一个局部变量，也是`printf()`的参数。

然后调用`printf()`函数。

不像MSVC，当gcc编译不开启优化时，它使用`MOV EAX，0`清空EAX，而不用更短的指令。

最后一条指令，`LEAVE`相当于`MOV ESP，EBP`和POP EBP两条指令。换句话说，这相当于指令将堆栈指针（ESP）恢复，将EBP寄存器复原到其初始状态。

这是很有必要的，因为我们在函数的开头修改了这些寄存器的值（ESP和EBP）（执行`MOV EBP，ESP`/`AND ESP, ...`）。

### 3.1.3 GCC:AT&T 语法

我们来看一看在AT&T当中的汇编语法，这个语法在UNIX当中更普遍。
代码清单 3.4: 让我们用 GCC 4.7.3 编译
`gcc -S 1_1.c`

我们将得到这个：

代码清单 3.5: GCC 4.7.3
```
    	.file   "1_1.c"
    	.section    .rodata
.LC0:
    	.string "hello, world"
    	.text
    	.globl  main
    	.type   main, @function
main:
.LFB0:
    	.cfi_startproc
    	pushl %ebp
    	.cfi_def_cfa_offset 8
    	.cfi_offset 5, -8
    	movl %esp, %ebp
    	.cfi_def_cfa_register 5
    	andl $-16, %esp
    	subl $16, %esp
    	movl $.LC0, (%esp)
    	call printf
    	movl $0, %eax
    	leave
    	.cfi_restore 5
    	.cfi_def_cfa 4, 4
    	ret
    	.cfi_endproc
.LFE0:
    	.size   main, .-main
    	.ident  "GCC: (Ubuntu/Linaro 4.7.3-1ubuntu1) 4.7.3"
    	.section        .note.GNU-stack, "", @progbits
```

这段代码包含了很多的宏（用点开始）。目前我们不关心这个。

现在为了简单起见，我们先不看这些。（除了 .string ，就像C-string一样，编码一个null结尾的字符序列）。然后，我们将看到这个：

代码清单 3.6: GCC 4.7.3
```
.LC0:
    .string "hello, world"
main:
    pushl   %ebp
    movl %esp, %ebp
    andl $-16, %esp
    subl $16, %esp
    movl $.LC0, (%esp)
    call printf
    movl $0, %eax
    leave
    ret
```

在Intel与AT&T语法当中一些主要的区别就是：

* 操作数写在后面  
	在Intel语法中：\<instruction> \<destination operand> \<source operand>  
	在AT&T语法中：\<instruction> \<source operand> \<destination operand>  
	有一个记住它们的方法: 当你面对intel语法的时候，你可以想象把等号(=)放到2个操作数中间，当面对AT&T语法的时候，你可以放一个右箭头(→）到两个操作数之间。  
* AT&T: 在寄存器名之前需要写一个百分号(%)并且在数字前面需要美元符($)。方括号被圆括号替代。 
* AT&T: 一些添加到操作符后，用来表示数据形式的后缀：  
	– q — quad (64 bits)  
	– l — long (32 bits)  
	– w — word (16 bits)  
	– b — byte (8 bits)  

让我们回到上面的编译结果：它和在IDA里看到的是一样的。只有一点不同：0FFFFFFF0h 被写成了$-16，但这是一样的，10进制的16在16进制里表示为0x10。-0x10就等同于0xFFFFFFF0(针对于32位的数据类型)。

另外：返回值通常用`MOV`置0，而不用`XOR`。MOV仅仅加载（load）了一个值到寄存器。指令的名称并不直观(数据不会被移动，而是被复制)。在其他的构架上，这条指令会被称作“LOAD” 、 “STORE”或其他类似的名称。

## 3.2 x86-64

### 3.2.1 MSVC-x86-64

让我们来试试64-bit的MSVC：
代码清单 3.7: MSVC 2012 x64

```
$SG2989 DB      'hello, world', 00H
main    PROC
    sub    rsp, 40
    lea    rcx, OFFSET FLAT:$SG2923
    call   printf
    xor    eax, eax
    add    rsp, 40
    ret    0
main ENDP
```

在x86-64里，所有的寄存器都被扩展到64位，并且名字前都带有R-前缀。为了减少栈的使用(即减少对外部内存/缓存的访问)，通常的做法是：用寄存器来传递参数（类似于fastcall）。也就是，一部分参数通过寄存器传递，其余的通过栈传递。

在win64里，RCX,RDX,R8,R9寄存器被用来传递函数的4个参数，在这里我们可以看到指向给printf()函数的字符串的指针，没有用通过栈，而是用了`RCX`来传递。这些指针现在是64位的，所以他们通过64位寄存器来传递(带有R-前缀)，并且为了向后兼容，依旧可以使用E-前缀，访问32位的部分。

如下图所示，这是RAX/EAX/AX/AL在64位x86兼容cpu里的情况 ￼![](pic/C3-1.jpg)

在main()函数会返回一个int类型的值，在C/C++里为了兼容和移植性，依旧是32位的。这就是问什么，是`EAX`而不是`RAX`（即寄存器的低32位部分）在函数最后被清0。

在寄存器里也有40字节被分配给了局部堆栈。这被称为“影子空间”。这一点我们之后会提及：8.2.1 。

### 3.2.2 GCC-x86-64

这次试试GCC在64位的Linux里：

代码清单 3.8: GCC 4.4.6 x64

```
    .string "hello, world"
main:
    sub    rsp, 8
    mov    edi, OFFSET FLAT:.LC0 ; "hello, world"
    xor    eax, eax  ; number of vector registers passed
    call   printf
    xor    eax, eax
    add    rsp, 8
    ret
```

在Linux,\*BSD和Mac OS X里也使用同一种方式来传递函数参数。

前6个参数使用`RDI,RSI,RDX,RCX,R8,R9`来传递的，剩下的用栈。

所以在这个程序里，字符串指针被放到`EDI`（RDI的低32位部分）。但是为什么不用RDI的64位部分呢？

很有必要记住：`MOV`指令在64位模式下，对低32位进行写入操作的时候，会清空高32位的内容[Int13]。比如 `MOV EAX，011223344h`将会把值写到RAX里，并且清空RAX的高32位区域。 

如果我们打开编译好的对象文件(object file[.o]),我们会看到所有的指令的操作符：

代码清单 3.9: GCC 4.4.6 x64

```
.text:00000000004004D0                     main proc near
.text:00000000004004D0 48 83 EC 08             sub rsp, 8
.text:00000000004004D4 BF E8 05 40 00          mov edi, offset format ;"hello, world"
.text:00000000004004D9 31 C0                   xor eax, eax
.text:00000000004004DB E8 D8 FE FF FF          call _printf
.text:00000000004004E0 31 C0                   xor eax, eax
.text:00000000004004E2 48 83 C4 08             add rsp, 8
.text:00000000004004E6 C3                      retn
.text:00000000004004E6                     main endp
```

就像看到的那样，在`0x4004D4`那行写入`EDI`花了5个字节。如果把这句换向`EDI`给写入64位的值，会花掉7个字节.显然，GCC在试图节省空间，除此之外，数据段(data segment)中包含的字串不会被分配到高于4GiB地址的空间上。

可以看到在printf()函数调用前，`EAX`被清空了，这是因为在x86-64的 *NIX 系统上， 使用过的向量寄存器(vector registers)的数量会被传入`EAX` [Mit13]。


## 3.3 关于GCC 额外的一点

匿名的C字符串有常量的类型(3.1.1)，并且C字符串在常量段被分配的地址是一定不变的。基于这样的事实，就有一个有趣的结论：编译器可能只用了字符串的一部分。
让我们看看这个例子：

```
#include <stdio.h>

int f1()
{
	printf ("world\n");
}
int f2()
{
	printf ("hello world\n");
}
int main()
{
	f1();
	f2();
}
```
一般的C/C++编译器(包括MSVC)会分配给地址两个字符串，但是让我们看看GCC干了什么：
代码清单 3.10: GCC 4.8.1 + IDA listing
```
f1 		proc near

s 		= dword ptr -1Ch
		sub 	esp, 1Ch
		mov 	[esp+1Ch+s], offset s ; "world\n"
		call 	_puts
		add 	esp, 1Ch
		retn
f1 		endp

f2 		proc near

s 		= dword ptr -1Ch

		sub 	esp, 1Ch
		mov 	[esp+1Ch+s], offset aHello ; "hello "
		call	_puts
		add 	esp, 1Ch
		retn
f2 		endp

aHello 	db 'hello '
s 		db 'world',0xa,0
```
真的！当我们打印"hello world"字符串时，这两个单词被放在内存里相邻的位置。函数f2()中调用的`puts()`并不知道字符串已经被分开了。事实上字符串并没有被分开，只是在代码里被无形的分开了。

当`puts()`被f1()调用时，他使用“world”字符串加上一个0，`puts()`并不清楚字符串之后还有什么。

这个聪明的小技巧至少在GCC里被经常使用，他能够节省一些内存。

## 3.4 ARM

根据作者自身对ARM处理器的经验，选择了几款在嵌入式开发流行的编译器：
* 嵌入式领域很流行的：Keil Release 6/2013
* 苹果的Xcode 4.6.3 IDE(其中使用了LLVM-GCC4.2编译器)
* GCC 4.9 (Linaro) (for ARM64)

32位ARM的代码(包括Thumb 和 Thumb-2模式)被用在这本书的所有例子里，如果未做其他提示，我们谈论64位ARN是会叫他ARM64.

### 3.3.1 未进行代码优化的Keil 6/2013 编译：ARM模式

让我们在Keil里编译我们的例子

`armcc.exe –arm –c90 –O0 1.c`

armcc编译器可以生成intel语法的汇编程序列表，但是里面有高级的ARM处理器相关的宏，对我们来讲更希望看到“指令原来的样子”，所以让我们看看IDA反汇编之后的结果。

代码清单 3.11: 无优化的 Keil 6/2013 (ARM 模式) IDA

```
.text:00000000                  main
.text:00000000 10 40 2D E9              STMFD SP!, {R4,LR}
.text:00000004 1E 0E 8F E2              ADR R0, aHelloWorld ; "hello, world"
.text:00000008 15 19 00 EB              BL __2printf
.text:0000000C 00 00 A0 E3              MOV R0, #0
.text:00000010 10 80 BD E8              LDMFD SP!, {R4,PC}

.text:000001EC 68 65 6C 6C +aHelloWorld  DCB "hello, world",0 ; DATA XREF: main+4
```

在例子中，我们可以发现所有指令都是4字节的，因为我们编译的时候选择了ARM模式，而不是Thumb模式。

最开始的指令是`STMFD SP!, {R4, LR}`，这条指令类似x86平台的`PUSH`指令，会把2个寄存器（R4和LR）的值写到栈里。不过为了简化，在`armcc`编译器输出的汇编代码里会写成`PUSH {R4, LR}`，但这并不准确，因为PUSH命令只在Thumb模式下可用，所以为了减少困惑，我们用IDA来做反汇编工具。

这指令开始会减少`SP`的值，已加大栈空间，并且将R4和LR写入分配好的栈里。

这条指令（类似于PUSH的STMFD）允许一次压入好几个值，非常实用。有一点跟x86上的PUSH不同的地方也很赞，就是这条指令不像x86的PUSH只能对sp操作，而是可以指定操作任意的寄存器。

`ADR R0, aHelloWorld`这条指令将PC寄存器的值与`"hello, world"`字串的地址偏移相加放入R0，为什么说要PC参与这个操作那？这是因为代码是PIC（position-independet code）的，这段代码可以独立在内存运行，而不需要更改内存地址。ADR这条指令中，指令中字串地址和字串被放置的位置是不同的。但变化是相对的，这要看系统是如何安排字串放置的位置了。这也就说明了，为何每次获取内存中字串的绝对地址，都要把这个指令里的地址加上PC寄存器里的值了。

`BL __2print`这条指令用于调用printf()函数，这是来说下这条指令时如何工作的：

* 将BL指令（0xC）后面的地址写入LR寄存器；
* 然后把printf()函数的入口地址写入PC寄存器，进入printf()函数。


当printf()函数完成之后，函数会通过LR寄存器保存的地址，来进行返回操作。

函数返回地址的存放位置也正是“纯”RISC处理器（例如：ARM）和CISC处理器(例如x86)的区别。

另外，一个32位地址或者偏移不能被编码到BL指令里，因为BL指令只有24bits来存放地址，所有的ARM模式下的指令都是4bytes（32bits）,所以一条指令里不能放满4bytes的地址，这也就意味着最后2bits总会被设置成0，总的来说也就是有26bits的偏移（包括了最后2个bit一直被设为0）会被编码进去。这也够去访问大约±32M的了。

下面我们来看`MOV R0， #0`这条语句，这条语句就是把0写到了R0寄存器里，这是因为C函数返回了0，返回值当然是放在R0里的。

最后一条指令是`LDMFD SP!, R4,PC`，这条指令的作用跟开始的那条STMFD正好相反，这条指令将栈上的值保存到R4和PC寄存器里，并且增加SP栈寄存器的值。这非常类似x86平台里的POP指令。最前面那条STMFD指令成对保存了R4，和LR寄存器，LDMFD的时候将当时这两个值保存到了R4和PC里完成了函数的返回。

我前面也说过，函数的返回地址会保存到LD寄存器里。在函数的最开始会把他保存到栈里，这是因为main()函数里还需要调用printf()函数，这个时候就会影响LD寄存器。在函数的最后就会将LD拿出栈放入PC寄存器里，完成函数的返回操作。最后C/C++程序的main()函数会返回到类似系统加载器上或者CRT里面。

汇编代码里的DCB关键字用来定义ASCII字串数组，就像x86汇编里的DB关键字。

### 3.4.2未进行代码优化的Keil 6/2013 编译： thumb模式

让我们用下面的指令，将相同的例子用Keil的thumb模式来编译一下。

`armcc.exe –thumb –c90 –O0 1.c`

我们可以在IDA里得到下面这样的代码： 
Listing 3.12: Non-optimizing Keil 6/2013 (Thumb mode) + IDA

```
.text:00000000            main
.text:00000000 10 B5          PUSH {R4,LR}
.text:00000002 C0 A0          ADR R0, aHelloWorld ; "hello, world"
.text:00000004 06 F0 2E F9    BL __2printf
.text:00000008 00 20          MOVS R0, #0
.text:0000000A 10 BD          POP {R4,PC}
.text:00000304 68 65 6C 6C +aHelloWorld  DCB "hello, world",0 ; DATA XREF: main+2
```

我们首先就能注意到指令都是2bytes(16bits)的了，这正是thumb模式的特征，BL指令作为特例是2个16bits来构成的。只用16bits没可能加载printf()函数的入口地址到PC寄存器。所以前面的16bits用来加载函数偏移的高10bits位，后面的16bits用来加载函数偏移的低11bits位，正如我说过的，所有的thumb模式下的指令都是2bytes(16bits)。但是这样的话thumb指令就没法使用更大的地址。就像上面那样，最后一个bits的地址将会在编码指令的时候省略。总的来讲，BL在thumb模式下可以访问自身地址大于±2M大的周边的地址。

至于其他指令:PUSH和POP，它们跟上面讲到的STMFD跟LDMFD很类似，但这里不需要指定SP寄存器，ADR指令也跟上面的工作方式相同。MOVS指令将函数的返回值0写到了R0里，最后函数返回。

### 3.4.3开启代码优化的Xcode（LLVM）编译： ARM模式

Xcode 4.6.3不开启代码优化的情况下，会产生非常多冗余的代码，所以我们学习一个尽量小的版本。

开启-O3编译选项

Listing 3.13: Optimizing Xcode 4.6.3 (LLVM) (ARM mode)

```
__text:000028C4         _hello_world
__text:000028C4 80 40 2D E9                     STMFD   SP!, {R7,LR}
__text:000028C8 86 06 01 E3                     MOV     R0, #0x1686
__text:000028CC 0D 70 A0 E1                     MOV     R7, SP
__text:000028D0 00 00 40 E3                     MOVT    R0, #0
__text:000028D4 00 00 8F E0                     ADD     R0, PC, R0
__text:000028D8 C3 05 00 EB                     BL      _puts
__text:000028DC 00 00 A0 E3                     MOV     R0, #0
__text:000028E0 80 80 BD E8                     LDMFD   SP!, {R7,PC}
__cstring:00003F62 48 65 6C 6C +aHelloWorld_0    DCB "Hello world!", 0
```

STMFD和LDMFD对我们来说已经非常熟悉了。

MOV指令就是将0x1686写入R0寄存器里。这个值也正是字串”Hello world！”的指针偏移。

R7寄存器里放入了栈地址，我们继续。

MOVT R0， #0指令时将R0的高16bits写入0。这是因为普通情况下MOV这条指令在ARM模式下，只对低16bits进行操作。需要记住的是所有在ARM模式下的指令都被限定在32bits内。当然这个限制并不影响2个寄存器直接的操作。这也是MOVT这种写高16bits指令存在的意义。其实这样写的代码会感觉有点多余，因为`MOVS R0，#0x1686`这条指令也能把高16位清0。或许这就是相对于人脑来说编译器的不足。

`ADD R0，PC，R0`指令把R0寄存器的值与PC寄存器的值进行相加并且保存到R0寄存器里面，用来计算`"Hello world!"`这个字串的绝对地址。上面已经介绍过了，这是因为代码是PIC(Position-independent code)的，所以这里需要这么做。

BL指令用来调用printf()的替代函数puts()函数。

GCC将printf（）函数替换成了puts()。因为printf()函数只有一个参数的时候跟puts()函数是类似的。

printf()函数的字串参数里存在特殊控制符（例如 "%s","\n"，需要注意的是，程序里字串里没有"\n"，因为在puts()函数里这是不需要的）的时候，两个函数的功效就会不同。

为什么编译器会替换printf()到puts()那？因为puts()函数更快。

puts()函数效率更快是因为它只是做了字串的标准输出(stdout)并不用比较%符号。

下面，我们可以看到非常熟悉的`"MOV R0, #0"`指令，用来将R0寄存器设为0。

### 3.4.4 开启代码优化的Xcode(LLVM)编译thumb-2模式

在默认情况下，Xcode4.6.3会生成如下的thumb-2代码

Listing 3.14: Optimizing Xcode 4.6.3 (LLVM) (Thumb-2 mode)

```
__text:00002B6C                _hello_world
__text:00002B6C 80 B5          PUSH    {R7,LR}
__text:00002B6E 41 F2 D8 30    MOVW    R0, #0x13D8
__text:00002B72 6F 46          MOV     R7, SP
__text:00002B74 C0 F2 00 00    MOVT.W  R0, #0
__text:00002B78 78 44          ADD     R0, PC
__text:00002B7A 01 F0 38 EA    BLX     _puts
__text:00002B7E 00 20          MOVS    R0, #0
__text:00002B80 80 BD          POP     {R7,PC}
...
__cstring:00003E70 48 65 6C 6C 6F 20 +aHelloWorld DCB "Hello world!",0xA,0
```

BL和BLX指令在thumb模式下情况需要我们回忆下刚才讲过的，它是由一对16-bit的指令来构成的。在thumb-2模式下这条指令跟thumb一样被编码成了32-bit指令。非常容易观察到的是，thumb-2的指令的机器码也是从0xFx或者0xEx的。对于thumb和thumb-2模式来说，在IDA的结果里机器码的位置和这里是交替交换的。对于ARM模式来说4个byte也是反向的，这是因为他们用了不同的字节序。所以我们可以知道，MOVW，MOVT.W和BLX这几个指令的开始都是0xFx。

在thumb-2指令里有一条是"MOVW R0, #0x13D8",它的作用是写数据到R0的低16位里面。

`MOVT.W R0, #0`的作用类似与前面讲到的MOVT指令，但它可以工作在thumb-2模式下。

还有些跟上面不同的地方，比如BLX指令替代了上面用到的BL指令，这条指令不仅将控制puts()函数返回的地址放入了LR寄存器里，并且讲代码从thumb模式转换到了ARM模式（或者ARM转换到thumb（根据现有情况判断））。这条指令跳转到下面这样的位置（下面的代码是ARM编码模式）。

```
__symbolstub1:00003FEC              _puts  ; CODE XREF: _hello_world+E
__symbolstub1:00003FEC 44 F0 9F E5  LDR PC, =__imp__puts
```

可能会有细心的读者要问了:为什么不直接跳转到puts()函数里面去？

因为那样做会浪费内存空间。

很多程序都会使用额外的动态库(dynamic libraries)(Windows里面的DLL，还有\*NIX里面的.so，MAC OS X里面的.dylib),通常使用的库函数会被放入动态库中，当然也包括标准C函数puts()。

在可执行的二进制文件里(Windows的PE里的.exe文件，ELF和Mach-O文件)都会有输入表段。它是一个用来引入额外模块里模块名称和符号（函数或者全局变量）的列表。

系统加载器（OS loader）会加载所有需要的模块，当在主模块里枚举输入符号的时候，会把每个符号正确的地址与相应的符号确立起来。

在我们的这个例子里，`__imp__puts`就是一个系统加载器加载额外模块的32位的地址值。LDR指令只需要把这个值加载到PC里面去，就可以控制程序流程到puts()函数里去。

所以只需要在系统加载器里的时候，一次性的就能将每个符号所对应的地址确定下来，这是个提高效率的好方式。

外加，我们前面也指出过，我们没办法只用一条指令并且不做内存操作的情况下就将一个32bit的值保存到寄存器里，ARM并不是唯一的模式的情况下，程序里去跳入动态库中的某个函数里，最好的办法就是这样做一些类似与上面这样单一指令的函数（称做thunk function），然后从thumb模式里也能去调用。

在上面的例子（ARM编译的那个例子）中BL指令也是跳转到了同一个thunk function里。尽管没有进行模式的转变（所以指令里不存在那个”X”）。

#### 关于形实转换函数


### 3.4.5 ARM64

**GCC**

## 3.5 MIPS
### 3.5.1

### 3.5.2

### 3.5.3


### 3.5.4

### 3.5.5

## 3.5.5

## 3.7