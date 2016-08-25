# 第二十九章
# 关于MIPS的具体细节

## 29.1 Loading constants into register
```
unsigned int f()
{
	return 0x12345678;
};
```

Listing 29.1: GCC 4.4.5 -O3 (assembly output)
```
li      $2,305397760                    # 0x12340000
j       $31
ori     $2,$2,0x5678 ; branch delay slot
```


Listing 29.2: GCC 4.4.5 -O3 (IDA)
```
lui     $v0, 0x1234
jr      $ra
li      $v0, 0x12345678 ; branch delay slot
```

## 29.2 Further reading about MIPS
[[Swe10](../Bibliography.md).]
