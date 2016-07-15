# Chapter 93
# Itanium


Listing 93.1: Linux kernel 3.2.0.4
```
#define TEA_ROUNDS              32
#define TEA_DELTA               0x9e3779b9

static void tea_encrypt(struct crypto_tfm *tfm, u8 *dst, const u8 *src)
{
        u32 y, z, n, sum = 0;
        u32 k0, k1, k2, k3;
        struct tea_ctx *ctx = crypto_tfm_ctx(tfm);
        const __le32 *in = (const __le32 *)src;
        __le32 *out = (__le32 *)dst;

        y = le32_to_cpu(in[0]);
        z = le32_to_cpu(in[1]);

        k0 = ctx->KEY[0];
        k1 = ctx->KEY[1];
        k2 = ctx->KEY[2];
        k3 = ctx->KEY[3];

        n = TEA_ROUNDS;

        while (n-- > 0) {
                sum += TEA_DELTA;
                y += ((z << 4) + k0) ^ (z + sum) ^ ((z >> 5) + k1);
                z += ((y << 4) + k2) ^ (y + sum) ^ ((y >> 5) + k3);
        }

        out[0] = cpu_to_le32(y);
        out[1] = cpu_to_le32(z);
}
```


Listing 93.2: Linux Kernel 3.2.0.4 for Itanium 2 (McKinley)
```
0090|                                   tea_encrypt:
0090|08 80 80 41 00 21                      adds r16 = 96, r32              // ptr to ctx->KEY [2]
0096|80 C0 82 00 42 00                      adds r8 = 88, r32               // ptr to ctx->KEY [0]
009C|00 00 04 00                            nop.i 0
00A0|09 18 70 41 00 21                      adds r3 = 92, r32               // ptr to ctx->KEY [1]
00A6|F0 20 88 20 28 00                      ld4 r15 = [r34], 4              // load z
00AC|44 06 01 84                            adds r32 = 100, r32;;           // ptr to ctx->KEY⤦ [3]
00B0|08 98 00 20 10 10                      ld4 r19 = [r16]                 // r19=k2
00B6|00 01 00 00 42 40                      mov r16 = r0                    // r0 always contain zero
00BC|00 08 CA 00                            mov.i r2 = ar.lc                // save lc register
00C0|05 70 00 44 10 10 9E FF FF FF 7F 20    ld4 r14 = [r34]                 // load y
00CC|92 F3 CE 6B                            movl r17 = 0xFFFFFFFF9E3779B9;; // TEA_DELTA
00D0|08 00 00 00 01 00                      nop.m 0
00D6|50 01 20 20 20 00                      ld4 r21 = [r8]                  // r21=k0
00DC|F0 09 2A 00                            mov.i ar.lc = 31                // TEA_ROUNDS is 32
00E0|0A A0 00 06 10 10                      ld4 r20 = [r3];;                // r20=k1
00E6|20 01 80 20 20 00                      ld4 r18 = [r32]                 // r18=k3
00EC|00 00 04 00                            nop.i 0
00F0|
00F0|                                   loc_F0:
00F0|09 80 40 22 00 20                      add r16 = r16, r17              // r16=sum, r17= TEA_DELTA
00F6|D0 71 54 26 40 80                      shladd r29 = r14, 4, r21        // r14=y, r21=k0
00FC|A3 70 68 52                            extr.u r28 = r14, 5, 27;;
0100|03 F0 40 1C 00 20                      add r30 = r16, r14
0106|B0 E1 50 00 40 40                      add r27 = r28, r20;;            // r20=k1
010C|D3 F1 3C 80                            xor r26 = r29, r30;;
0110|0B C8 6C 34 0F 20                      xor r25 = r27, r26;;
0116|F0 78 64 00 40 00                      add r15 = r15, r25              // r15=z
011C|00 00 04 00                            nop.i 0;;
0120|00 00 00 00 01 00                      nop.m 0
0126|80 51 3C 34 29 60                      extr.u r24 = r15, 5, 27
012C|F1 98 4C 80                            shladd r11 = r15, 4, r19        // r19=k2
0130|0B B8 3C 20 00 20                      add r23 = r15, r16;;
0136|A0 C0 48 00 40 00                      add r10 = r24, r18              // r18=k3
013C|00 00 04 00                            nop.i 0;;
0140|0B 48 28 16 0F 20                      xor r9 = r10, r11;;
0146|60 B9 24 1E 40 00                      xor r22 = r23, r9
014C|00 00 04 00                            nop.i 0;;
0150|11 00 00 00 01 00                      nop.m 0
0156|E0 70 58 00 40 A0                      add r14 = r14, r22
015C|A0 FF FF 48                            br.cloop.sptk.few loc_F0;;
0160|09 20 3C 42 90 15                      st4 [r33] = r15, 4              // store z
0166|00 00 00 02 00 00                      nop.m 0
016C|20 08 AA 00                            mov.i ar.lc = r2;;              // restore lc  register
0170|11 00 38 42 90 11                      st4 [r33] = r14                 // store y
0176|00 00 00 02 00 80                      nop.i 0
017C|08 00 84 00                            br.ret.sptk.many b0;;
```
