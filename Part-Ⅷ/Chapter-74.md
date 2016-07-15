# 第七十四章
# 任务管理器上的实用玩笑(Windows Vista)

让我们试一试能否对任务管理器做点微小的修改，让它能够识别出更多的CPU内核。

首先让我们思考一下，任务管理器是怎么知道内核的数量的？

![](img\C74-1.png) 图 74.1: IDA: 对 NtQuerySystemInformation() 的交叉引用


Listing 74.1: taskmgr.exe (Windows Vista)
```
.text:10000B4B3         xor     r9d, r9d
.text:10000B4B6         lea     rdx, [rsp+0C78h+var_C58] ; buffer
.text:10000B4BB         xor     ecx, ecx
.text:10000B4BD         lea     ebp, [r9+40h]
.text:10000B4C1         mov     r8d, ebp
.text:10000B4C4         call    cs:__imp_NtQuerySystemInformation ; 0
.text:10000B4CA         xor     ebx, ebx
.text:10000B4CC         cmp     eax, ebx
.text:10000B4CE         jge     short loc_10000B4D7
.text:10000B4D0
.text:10000B4D0 loc_10000B4D0:          ; CODE XREF: InitPerfInfo(void)+97
.text:10000B4D0                         ; InitPerfInfo(void)+AF
.text:10000B4D0         xor     al, al
.text:10000B4D2         jmp     loc_10000B5EA
.text:10000B4D7 ; ---------------------------------------------------------------------------
.text:10000B4D7
.text:10000B4D7 loc_10000B4D7:          ; CODE XREF: InitPerfInfo(void)+36
.text:10000B4D7         mov     eax, [rsp+0C78h+var_C50]
.text:10000B4DB         mov     esi, ebx
.text:10000B4DD         mov     r12d, 3E80h
.text:10000B4E3         mov     cs:?g_PageSize@@3KA, eax ; ulong g_PageSize
.text:10000B4E9         shr     eax, 0Ah
.text:10000B4EC         lea     r13, __ImageBase
.text:10000B4F3         imul    eax, [rsp+0C78h+var_C4C]
.text:10000B4F8         cmp     [rsp+0C78h+var_C20], bpl
.text:10000B4FD         mov     cs:?g_MEMMax@@3_JA, rax ; __int64 g_MEMMax
.text:10000B504         movzx   eax, [rsp+0C78h+var_C20] ; number of CPUs
.text:10000B509         cmova   eax, ebp
.text:10000B50C         cmp     al, bl
.text:10000B50E         mov     cs:?g_cProcessors@@3EA, al ; uchar g_cProcessors
```

```
typedef struct _SYSTEM_BASIC_INFORMATION {
        BYTE Reserved1[24];
        PVOID Reserved2[4];
        CCHAR NumberOfProcessors;
} SYSTEM_BASIC_INFORMATION;
```

![](img\C74-2.png)
Figure 74.2: Hiew: find the place to be patched


![](img\C74-3.png)
Figure 74.3: Hiew: patch it

![](img\C74-4.png)
Figure 74.4: Fooled Windows Task Manager


## 74.1 Using LEA to load values

Listing 74.2: taskmgr.exe (Windows Vista)
```
        xor     r9d, r9d
        div     dword ptr [rsp+4C8h+WndClass.lpfnWndProc]
        lea     rdx, [rsp+4C8h+VersionInformation]
        lea     ecx, [r9+2] ; put 2 to ECX
        mov     r8d, 138h
        mov     ebx, eax
; ECX=SystemPerformanceInformation
        call    cs:__imp_NtQuerySystemInformation ; 2

        ...

        mov     r8d, 30h
        lea     r9, [rsp+298h+var_268]
        lea     rdx, [rsp+298h+var_258]
        lea     ecx, [r8-2Dh] ; put 3 to ECX
; ECX=SystemTimeOfDayInformation
        call    cs:__imp_NtQuerySystemInformation ; not zero

        ...

        mov     rbp, [rsi+8]
        mov     r8d, 20h
        lea     r9, [rsp+98h+arg_0]
        lea     rdx, [rsp+98h+var_78]
        lea     ecx, [r8+2Fh] ; put 0x4F to ECX
        mov     [rsp+98h+var_60], ebx
        mov     [rsp+98h+var_68], rbp
; ECX=SystemSuperfetchInformation
        call    cs:__imp_NtQuerySystemInformation ; not zero
```
