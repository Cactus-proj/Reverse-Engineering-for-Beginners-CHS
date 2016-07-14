# Chapter 91
# Compiler’s anomalies


Listing 91.1: kdli.o from libserver11.a
```
.text:08114CF1                  loc_8114CF1: ; CODE XREF: __PGOSF539_kdlimemSer+89A
.text:08114CF1                               ; __PGOSF539_kdlimemSer+3994
.text:08114CF1 8B 45 08             mov     eax, [ebp+arg_0]
.text:08114CF4 0F B6 50 14          movzx   edx, byte ptr [eax+14h]
.text:08114CF8 F6 C2 01             test    dl, 1
.text:08114CFB 0F 85 17 08 00 00    jnz     loc_8115518
.text:08114D01 85 C9                test    ecx, ecx
.text:08114D03 0F 84 8A 00 00 00    jz      loc_8114D93
.text:08114D09 0F 84 09 08 00 00    jz      loc_8115518
.text:08114D0F 8B 53 08             mov     edx, [ebx+8]
.text:08114D12 89 55 FC             mov     [ebp+var_4], edx
.text:08114D15 31 C0                xor     eax, eax
.text:08114D17 89 45 F4             mov     [ebp+var_C], eax
.text:08114D1A 50                   push    eax
.text:08114D1B 52                   push    edx
.text:08114D1C E8 03 54 00 00       call    len2nbytes
.text:08114D21 83 C4 08             add     esp, 8
```

Listing 91.2: from the same code
```
.text:0811A2A5                  loc_811A2A5: ; CODE XREF: kdliSerLengths+11C
.text:0811A2A5                               ; kdliSerLengths+1C1
.text:0811A2A5 8B 7D 08             mov     edi, [ebp+arg_0]
.text:0811A2A8 8B 7F 10             mov     edi, [edi+10h]
.text:0811A2AB 0F B6 57 14          movzx   edx, byte ptr [edi+14h]
.text:0811A2AF F6 C2 01             test    dl, 1
.text:0811A2B2 75 3E                jnz     short loc_811A2F2
.text:0811A2B4 83 E0 01             and     eax, 1
.text:0811A2B7 74 1F jz             short   loc_811A2D8
.text:0811A2B9 74 37 jz             short   loc_811A2F2
.text:0811A2BB 6A 00                push    0
.text:0811A2BD FF 71 08             push    dword ptr [ecx+8]
.text:0811A2C0 E8 5F FE FF FF       call    len2nbytes
```
