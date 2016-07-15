# Chapter 75
# Color Lines game practical joke


![](img\C75-1.png)
Figure 75.1: How this game looks usually

```
.text:00402C9C sub_402C9C      proc near               ; CODE XREF: sub_402ACA+52
.text:00402C9C                                         ; sub_402ACA+64 ...
.text:00402C9C
.text:00402C9C arg_0           = dword ptr  8
.text:00402C9C
.text:00402C9C                 push    ebp
.text:00402C9D                 mov     ebp, esp
.text:00402C9F                 push    ebx
.text:00402CA0                 push    esi
.text:00402CA1                 push    edi
.text:00402CA2                 mov     eax, dword_40D430
.text:00402CA7                 imul    eax, dword_40D440
.text:00402CAE                 add     eax, dword_40D5C8
.text:00402CB4                 mov     ecx, 32000
.text:00402CB9                 cdq
.text:00402CBA                 idiv    ecx
.text:00402CBC                 mov     dword_40D440, edx
.text:00402CC2                 call    _rand
.text:00402CC7                 cdq
.text:00402CC8                 idiv    [ebp+arg_0]
.text:00402CCB                 mov     dword_40D430, edx
.text:00402CD1                 mov     eax, dword_40D430
.text:00402CD6                 jmp     $+5
.text:00402CDB                 pop     edi
.text:00402CDC                 pop     esi
.text:00402CDD                 pop     ebx
.text:00402CDE                 leave
.text:00402CDF                 retn
.text:00402CDF sub_402C9C      endp
```



```
.text:00402B16                 mov     eax, dword_40C03C ; 10 here
.text:00402B1B                 push    eax
.text:00402B1C                 call    random
.text:00402B21                 add     esp, 4
.text:00402B24                 inc     eax
.text:00402B25                 mov     [ebp+var_C], eax
.text:00402B28                 mov     eax, dword_40C040 ; 10 here
.text:00402B2D                 push    eax
.text:00402B2E                 call    random
.text:00402B33                 add     esp, 4
```

```
.text:00402BBB                 mov     eax, dword_40C058 ; 5 here
.text:00402BC0                 push    eax
.text:00402BC1                 call    random
.text:00402BC6                 add     esp, 4
.text:00402BC9                 inc     eax
```


```
.00402BB8: 83C410         add         esp,010
.00402BBB: A158C04000     mov         eax,[00040C058]
.00402BC0: 31C0           xor         eax,eax
.00402BC2: 90             nop
.00402BC3: 90             nop
.00402BC4: 90             nop
.00402BC5: 90             nop
.00402BC6: 90             nop
.00402BC7: 90             nop
.00402BC8: 90             nop
.00402BC9: 40             inc         eax
.00402BCA: 8B4DF8         mov         ecx,[ebp][-8]
.00402BCD: 8D0C49         lea         ecx,[ecx][ecx]*2
.00402BD0: 8B15F4D54000   mov         edx,[00040D5F4]
```

![](img\C75-2.png)
Figure 75.2: Practical joke works
