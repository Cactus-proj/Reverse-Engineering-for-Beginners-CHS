# Chapter 77
# Hand decompiling + Z3 SMT solver

# 77.1 Hand decompiling

```
sub_401510      proc near
		; ECX = input
                mov     rdx, 5D7E0D1F2E0F1F84h
                mov     rax, rcx        ; input
                imul    rax, rdx
                mov     rdx, 388D76AEE8CB1500h
                mov     ecx, eax
                and     ecx, 0Fh
                ror     rax, cl
                xor     rax, rdx
                mov     rdx, 0D2E9EE7E83C4285Bh
                mov     ecx, eax
                and     ecx, 0Fh
                rol     rax, cl
                lea     r8, [rax+rdx]
                mov     rdx, 8888888888888889h
                mov     rax, r8
                mul     rdx
                shr     rdx, 5
                mov     rax, rdx
                lea     rcx, [r8+rdx*4]
                shl     rax, 6
                sub     rcx, rax
                mov     rax, r8
                rol     rax, cl
                ; EAX = output
                retn
sub_401510      endp
```



```
uint64_t f(uint64_t input)
{
	uint64_t rax, rbx, rcx, rdx, r8;

	ecx=input;

        rdx=0x5D7E0D1F2E0F1F84;
        rax=rcx;
        rax*=rdx;
        rdx=0x388D76AEE8CB1500;
        rax=_lrotr(rax, rax&0xF); // rotate right
        rax^=rdx;
        rdx=0xD2E9EE7E83C4285B;
        rax=_lrotl(rax, rax&0xF); // rotate left
        r8=rax+rdx;
        rdx=0x8888888888888889;
        rax=r8;
        rax*=rdx;
        rdx=rdx>>5;
        rax=rdx;
        rcx=r8+rdx*4;
        rax=rax<<6;
        rcx=rcx-rax;
        rax=r8
        rax=_lrotl (rax, rcx&0xFF); // rotate left
        return rax;
};
```



```
uint64_t f(uint64_t input)
{
	uint64_t rax, rbx, rcx, rdx, r8;

	ecx=input;

        rdx=0x5D7E0D1F2E0F1F84;
        rax=rcx;
        rax*=rdx;
        rdx=0x388D76AEE8CB1500;
        rax=_lrotr(rax, rax&0xF); // rotate right
        rax^=rdx;
        rdx=0xD2E9EE7E83C4285B;
        rax=_lrotl(rax, rax&0xF); // rotate left
        r8=rax+rdx;

        rdx=0x8888888888888889;
        rax=r8;
        rax*=rdx;
        // RDX here is a high part of multiplication result
        rdx=rdx>>5;
        // RDX here is division result!
        rax=rdx;

        rcx=r8+rdx*4;
        rax=rax<<6;
        rcx=rcx-rax;
        rax=r8
        rax=_lrotl (rax, rcx&0xFF); // rotate left
        return rax;
};
```



```
uint64_t f(uint64_t input)
{
	uint64_t rax, rbx, rcx, rdx, r8;

	ecx=input;

        rdx=0x5D7E0D1F2E0F1F84;
        rax=rcx;
        rax*=rdx;
        rdx=0x388D76AEE8CB1500;
        rax=_lrotr(rax, rax&0xF); // rotate right
        rax^=rdx;
        rdx=0xD2E9EE7E83C4285B;
        rax=_lrotl(rax, rax&0xF); // rotate left
        r8=rax+rdx;

        rdx=0x8888888888888889;
        rax=r8;
        rax*=rdx;
        // RDX here is a high part of multiplication result
        rdx=rdx>>5;
        // RDX here is division result!
        rax=rdx;

        rcx=(r8+rdx*4)-(rax<<6);
        rax=r8
        rax=_lrotl (rax, rcx&0xFF); // rotate left
        return rax;
};
```


Listing 77.1: Wolfram Mathematica
```
In[1]:=N[2^(64 + 5)/16^^8888888888888889]
Out[1]:=60.
```



```
uint64_t f(uint64_t input)
{
	uint64_t rax, rbx, rcx, rdx, r8;

	ecx=input;

        rdx=0x5D7E0D1F2E0F1F84;
        rax=rcx;
        rax*=rdx;
        rdx=0x388D76AEE8CB1500;
        rax=_lrotr(rax, rax&0xF); // rotate right
        rax^=rdx;
        rdx=0xD2E9EE7E83C4285B;
        rax=_lrotl(rax, rax&0xF); // rotate left
        r8=rax+rdx;

        rax=rdx=r8/60;

        rcx=(r8+rax*4)-(rax*64);
        rax=r8
        rax=_lrotl (rax, rcx&0xFF); // rotate left
        return rax;
};
```



```
uint64_t f(uint64_t input)
{
	uint64_t rax, rbx, rcx, rdx, r8;

        rax=input;
        rax*=0x5D7E0D1F2E0F1F84;
        rax=_lrotr(rax, rax&0xF); // rotate right
        rax^=0x388D76AEE8CB1500;
        rax=_lrotl(rax, rax&0xF); // rotate left
        r8=rax+0xD2E9EE7E83C4285B;

        rcx=r8-(r8/60)*60;
        rax=r8
        rax=_lrotl (rax, rcx&0xFF); // rotate left
        return rax;
};
```



```
uint64_t f(uint64_t input)
{
	uint64_t rax, rbx, rcx, rdx, r8;

        rax=input;
        rax*=0x5D7E0D1F2E0F1F84;
        rax=_lrotr(rax, rax&0xF); // rotate right
        rax^=0x388D76AEE8CB1500;
        rax=_lrotl(rax, rax&0xF); // rotate left
        r8=rax+0xD2E9EE7E83C4285B;

        return _lrotl (r8, r8 % 60); // rotate left
};
```



```
#include <stdio.h>
#include <stdint.h>
#include <stdbool.h>
#include <string.h>
#include <intrin.h>

#define C1 0x5D7E0D1F2E0F1F84
#define C2 0x388D76AEE8CB1500
#define C3 0xD2E9EE7E83C4285B

uint64_t hash(uint64_t v)
{
	v*=C1;
	v=_lrotr(v, v&0xF); // rotate right
	v^=C2;
	v=_lrotl(v, v&0xF); // rotate left
	v+=C3;
	v=_lrotl(v, v % 60); // rotate left
	return v;
};

int main()
{
	printf ("%llu\n", hash(...));
};
```


# 77.2 Now let’s use the Z3 SMT solver



```
1    from z3 import *
2
3    C1=0x5D7E0D1F2E0F1F84
4    C2=0x388D76AEE8CB1500
5    C3=0xD2E9EE7E83C4285B
6
7    inp, i1, i2, i3, i4, i5, i6, outp = BitVecs('inp i1 i2 i3 i4 i5 i6 outp', 64)
8   
9    s = Solver()
10   s.add(i1==inp*C1)
11   s.add(i2==RotateRight (i1, i1 & 0xF))
12   s.add(i3==i2 ^ C2)
13   s.add(i4==RotateLeft(i3, i3 & 0xF))
14   s.add(i5==i4 + C3)
15   s.add(outp==RotateLeft (i5, URem(i5, 60)))
16  
17   s.add(outp==10816636949158156260)
18  
19   print s.check()
20   m=s.model()
21   print m
22   print (" inp=0x%X" % m[inp].as_long())
23   print ("outp=0x%X" % m[outp].as_long())
```


```
...>python.exe 1.py
sat
[i1 = 3959740824832824396,
 i3 = 8957124831728646493,
 i5 = 10816636949158156260,
 inp = 1364123924608584563,
 outp = 10816636949158156260,
 i4 = 14065440378185297801,
 i2 = 4954926323707358301]
 inp=0x12EE577B63E80B73
outp=0x961C69FF0AEFD7E4
```

```
1    from z3 import *
2
3    C1=0x5D7E0D1F2E0F1F84
4    C2=0x388D76AEE8CB1500
5    C3=0xD2E9EE7E83C4285B
6
7   inp, i1, i2, i3, i4, i5, i6, outp = BitVecs('inp i1 i2 i3 i4 i5 i6 outp', 64)
8
9    s = Solver()
10   s.add(i1==inp*C1)
11   s.add(i2==RotateRight (i1, i1 & 0xF))
12   s.add(i3==i2 ^ C2)
13   s.add(i4==RotateLeft(i3, i3 & 0xF))
14   s.add(i5==i4 + C3)
15   s.add(outp==RotateLeft (i5, URem(i5, 60)))
16
17   s.add(outp==10816636949158156260)
18
19   s.add(inp!=0x12EE577B63E80B73)
20
21   print s.check()
22   m=s.model()
23   print m
24   print (" inp=0x%X" % m[inp].as_long())
25   print ("outp=0x%X" % m[outp].as_long())
```

```
...>python.exe 2.py
sat
[i1 = 3959740824832824396,
 i3 = 8957124831728646493,
 i5 = 10816636949158156260,
 inp = 10587495961463360371,
 outp = 10816636949158156260,
 i4 = 14065440378185297801,
 i2 = 4954926323707358301]
 inp=0x92EE577B63E80B73
outp=0x961C69FF0AEFD7E4
```

```
1    from z3 import *
2
3    C1=0x5D7E0D1F2E0F1F84
4    C2=0x388D76AEE8CB1500
5    C3=0xD2E9EE7E83C4285B
6
7    inp, i1, i2, i3, i4, i5, i6, outp = BitVecs('inp i1 i2 i3 i4 i5 i6 outp', 64)
8
9    s = Solver()
10   s.add(i1==inp*C1)
11   s.add(i2==RotateRight (i1, i1 & 0xF))
12   s.add(i3==i2 ^ C2)
13   s.add(i4==RotateLeft(i3, i3 & 0xF))
14   s.add(i5==i4 + C3)
15   s.add(outp==RotateLeft (i5, URem(i5, 60)))
16
17   s.add(outp==10816636949158156260)
18
19   # copypasted from http://stackoverflow.com/questions/11867611/z3py-checking-all-solutions-for-equation
20   result=[]
21   while True:
22      if s.check() == sat:
23          m = s.model()
24          print m[inp]
25          result.append(m)
26          # Create a new constraint the blocks the current model
27          block = []
28          for d in m:
29              # d is a declaration
30              if d.arity() > 0:
31                  raise Z3Exception("uninterpreted functions are not supported")
32              # create a constant from declaration
33              c=d()
34              if is_array(c) or c.sort().kind() == Z3_UNINTERPRETED_SORT:
35                  raise Z3Exception("arrays and uninterpreted sorts are not supported")
36              block.append(c != m[d])
37          s.add(Or(block))
38      else:
39              print "results total=",len(result)
40              break
```

```
1364123924608584563
1234567890
9223372038089343698
4611686019661955794
13835058056516731602
3096040143925676201
12319412180780452009
7707726162353064105
16931098199207839913
1906652839273745429
11130024876128521237
15741710894555909141
6518338857701133333
5975809943035972467
15199181979890748275
10587495961463360371
results total= 16
```

```
1    from z3 import *
2
3    C1=0x5D7E0D1F2E0F1F84
4    C2=0x388D76AEE8CB1500
5    C3=0xD2E9EE7E83C4285B
6
7    inp, i1, i2, i3, i4, i5, i6, outp = BitVecs('inp i1 i2 i3 i4 i5 i6 outp', 64)
8
9    s = Solver()
10   s.add(i1==inp*C1)
11   s.add(i2==RotateRight (i1, i1 & 0xF))
12   s.add(i3==i2 ^ C2)
13   s.add(i4==RotateLeft(i3, i3 & 0xF))
14   s.add(i5==i4 + C3)
15   s.add(outp==RotateLeft (i5, URem(i5, 60)))
16
17   s.add(outp & 0xFFFFFFFF == inp & 0xFFFFFFFF)
18
19   print s.check()
20   m=s.model()
21   print m
22   print (" inp=0x%X" % m[inp].as_long())
23   print ("outp=0x%X" % m[outp].as_long())
```

```
sat
[i1 = 14869545517796235860,
 i3 = 8388171335828825253,
 i5 = 6918262285561543945,
 inp = 1370377541658871093,
 outp = 14543180351754208565,
 i4 = 10167065714588685486,
 i2 = 5541032613289652645]
 inp=0x13048F1D12C00535
outp=0xC9D3C17A12C00535
```

```
1    from z3 import *
2
3    C1=0x5D7E0D1F2E0F1F84
4    C2=0x388D76AEE8CB1500
5    C3=0xD2E9EE7E83C4285B
6
7    inp, i1, i2, i3, i4, i5, i6, outp = BitVecs('inp i1 i2 i3 i4 i5 i6 outp', 64)
8
9    s = Solver()
10   s.add(i1==inp*C1)
11   s.add(i2==RotateRight (i1, i1 & 0xF))
12   s.add(i3==i2 ^ C2)
13   s.add(i4==RotateLeft(i3, i3 & 0xF))
14   s.add(i5==i4 + C3)
15   s.add(outp==RotateLeft (i5, URem(i5, 60)))
16
17   s.add(outp & 0xFFFFFFFF == inp & 0xFFFFFFFF)
18   s.add(outp & 0xFFFF == 0x1234)
19
20   print s.check()
21   m=s.model()
22   print m
23   print (" inp=0x%X" % m[inp].as_long())
24   print ("outp=0x%X" % m[outp].as_long())
```

```
sat
[i1 = 2834222860503985872,
 i3 = 2294680776671411152,
 i5 = 17492621421353821227,
 inp = 461881484695179828,
 outp = 419247225543463476,
 i4 = 2294680776671411152,
 i2 = 2834222860503985872]
 inp=0x668EEC35F961234
outp=0x5D177215F961234
```
