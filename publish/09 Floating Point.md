
### IEEE 754-2008
- Defines four binary floating point format: 16-bit, 32-bit, 64-bit and 128-bit. Also known as half-precision, single-precision, double-precision, and quad precision.

![[Pasted image 20250113233009.png]]

### Floating Point Format
- The 3 components are:
1) sign bit
2) exponent
3) fraction (mantissa)

>[!warning] Warning
> ARM does not support data type double. Floating point arithmetic is 32-bit only.

### Floating Point Registers
ARM processor has 32 floating point registers. Starting from `{ s0 ... s31 }`
The upper half `{ s16 ... s31 }` should be preserved and not modified by functions. Use PUSH and POP to preserve and restore.

### Floating Point Constant
- Loading constant (immediate value is limited!)
```
// Load Immediate Constant
VMOV  S0, 0.125

// Load float from memory
pi: .float 3.14159
LDR   R0, =pi
VLDR  S0, [R0]
```

- Common VMOV Immediates
```
The first 31 multiples of 1:   {1.0, 2.0, 3.0, ... 31.0}
The first 32 multiples of 1/2: {0.5, 1.0, 1.5, ... 16.0}
The first 32 multiples of 1/4: {0.25, 0.5, 0.75, ... 8.0}
The first 32 multiples of 1/8: {0.125, 0.25, 0.375, ... 4.0}
```
>[!check] Zero Immediate?
> Immediate `VMOV S0, 0.0` is not supported! Hence, use this arithmetic instead:
> `VSUB.F32 S0, S0, S0`



### Moving Data

VMOV instructions: Copy data ***between*** ARM registers and the FPU or between two FPU registers.
#####  Data Type Identifiers

| Data Type        | Identifier |
| ---------------- | ---------- |
| Half-Precision   | .F16       |
| Single-Precision | .F32 or .F |
| Double-Precision | .F64 or .D |

##### Conversion between Integer and Floating Point
- Operands of `VCVT` Instruction must be floating point registers (`s0-s31`)
 ```
VCVT.F32.U32  S0, S1    // float <-- uint32_t
VCVT.F32.S32  S0, S1    // float <-- 2's complement / signed integer 32_t

VCVTR.U32.F32  S0, S1    // rounded unsigned integer <-- float
```
##### Load Floating Point Data
- single-precision: `s0`
- double-precision: `d0`
```
VLDR  S0, [R0]
VLDR  S0, [R0, #4]

VLDR  D0, [R0]
VLDR  D0, [R0, #4]
VLDR  D0, pi            // PC relative only
pi: .float 3.14157
```
##### Preserving Floating-Point Registers
- Use VPUSH and VPOP 
```
PUSH     {LR}
VPUSH    {S16}
VMOV     S16, S0
VADD.F32 S0, S0, S16
VPOP     {S16}
POP      {LR}
```


### Floating Point Arithmetic

- Comparing Real numbers

```
VCMP.F32   S0, 0.0
VMRS       APSR_nzcv, FPSCR    // Core Flags <-- FPU
IT         EQ
VMOVEQ     S1, 8.0
```


### Example:  IEEE Conversion
- Represent 1259.125 in single-precision formats

##### Step 1: Convert Decimal Number to Binary

##### Step 2: Normalize the number
- Format: `1.xxxxxxx * 2^E`
##### Step  3: Single Precision Representation
- S: sign
- E: exponent with bias `E' = E + 127`
- M: mantissa (fraction part after decimal point)
##### Step 4: Round mantissa up or down if necessary (LSB)

