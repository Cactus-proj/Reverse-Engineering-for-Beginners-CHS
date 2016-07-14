# Appendix C
# MIPS

## C.1 Registers

## C.1.1 General purpose registers GPR

Number      | Pseudoname |    Description
:-----------|:-----------|:-----------------------------
$0          | $ZERO      | Always zero. Writing to this register is effectively an idle instruction (NOP).
$1          | $AT        | Used as a temporary register for assembly macros and pseudoinstructions.
$2 …$3      | $V0 …$V1   | Function result is returned here.
$4 …$7      | $A0 …$A3   | Function arguments.
$8 …$15     | $T0 …$T7   | Used for temporary data.
$16 …$23    | $S0 …$S7   | Used for temporary data \*.
$24 …$25    | $T8 …$T9   | Used for temporary data.
$26 …$27    | $K0 …$K1   | Reserved for OS kernel.
$28         | $GP        | Global Pointer \**.
$29         | $SP        | SP \*.
$30         | $FP        | FP \*.
$31         | $RA        | RA.
n/a         | PC         | PC.
n/a         | HI         | high 32 bit of multiplication or division remainder \***.
n/a         | LO         | low 32 bit of multiplication and division remainder \***.

### C.1.2 Floating-point registers

Name        | Description
:-----------|:-------------------------------
$F0..$F1    | Function result returned here.
$F2..$F3    | Not used.
$F4..$F11   | Used for temporary data.
$F12..$F15  | First two function arguments.
$F16..$F19  | Used for temporary data.
$F20..$F31  | Used for temporary data \*.


\*     —Callee must preserve the value.
\**    —Callee must preserve the value ( except in PIC code).
\***   —accessible using the MFHI and MFLO instructions.


## C.2 Instructions

- R-type:

    ```
    instruction destination, source1, source2
    ```

    ```
    instruction destination/source1, source2
    ```

- I-type:
- J-type:

### C.2.1 Jump instructions
