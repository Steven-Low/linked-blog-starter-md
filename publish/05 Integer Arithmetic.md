### Flags and Their Usage

- CPSR holds the flags, the execption number, the Interrupt-Continuable Instruction (ICI) bits , and the Thumb bit. 
- The flags (N, Z, C and V) are used to determine whether or not an instruction will be *conditionally* executed.
- The flags are set and cleared based on one of 4 things:
	1) Instructions that are used for setting and clearing flags: `CMP`.
	2) Instructions set the flags by appending an "s" to the mnemonic: `ADDS`
	3) A direct write to the Program Status Regsiter, explicitly set or clear flags.
	4) A 16-bit Thumb ALU instruction.


### The C Flag 
- A carry flag is set if the result of an addition is **greater than or equal to 2^32** ; 
- A carry flag is set if the result of a subtraction is ***positive***.
- Normally used in 64-bit number.

```
	LDR  R3, =0x7B000000
	LDR  R4, =0x90000000
	ADDS R5, R4, R3       // >32 bit, generate C out
	ADC  R6, R6, #0       // R6 = 1 (add with carry)
```
```
	SUBS R0, R3, R4       // borrow, set C
	SBC  R1, R1, R2       // R1 = R1 - R2 - c (subtract with carry/borrow)
```
>[!tip] Tips
> In signed operation, when overflow (V) occurs, the carry (C) flag is set together.


### Carries and Overflow
- In <u>unsigned</u> operation, the **subtracted value** is transformed in 2's complement. 
- In <u>signed</u> operation, addition and subtraction does not require transformation because the value is already 2's complement. (Including the result). The condition of overflow for signed operation is same in ***both*** addition and subtraction.
##### Addition CV
###### a) Unsigned Overflow
- Carry out of MSB (C_4) is 1   --> Assuming 4 bits arithmetic
###### b) Signed Overflow
 - Carries in & out of MSB differ (C_4 != C_3)
##### Subtraction CV
###### a) Unsigned Overflow
- Carry out of MSB (C_4) is 0   --> Assuming 4 bits arithmetic
###### b) Signed Overflow
 - Carries in & out of MSB differ (C_4 != C_3)


### ARM Thumb-2 Instruction Encodings
- Two Instruction size: 16-bits and 32-bits
- For 16-bits, "S" suffix is added to the mnemonic, otherwise 32-bits is default.
- Reducing code size = faster code


### Multiplication

##### I) Single Length Products

| FORMAT               | OPERATION           |
| -------------------- | ------------------- |
| MUL   R1, R2, R3     | R1 <-- R2 x R3      |
| MLA   R1, R2, R3, R4 | R1 <-- R4 + R2 x R3 |
| MLS   R1, R2, R3, R4 | R1 <-- R4 - R2 x R3 |
`*MULS` affects only flag N and Z. No other multiply instruction affects the flags.


##### II) Double Length Products (64-bits)
- 64-bit unsigned and 64-bit signed multiply

| FORMAT                 | OPERATION                 |
| ---------------------- | ------------------------- |
| UMULL   R1, R2, R3, R4 | R2·R1 <-- R3 x R4         |
| UMLAL   R1, R2, R3, R4 | R2·R1 <-- R2·R1 + R3 x R4 |
| SMULL   R1, R2, R3, R4 | R2·R1 <-- R3 x R4         |
| SMLAL   R1, R2, R3, R4 | R2·R1 <-- R2·R1 + R3 x R4 |

>[!tip] Multiplication Overflow
> - Double-Length products (signed or unsigned): Overflow is **not possible to recognize.**
> - Single-Length **Unsigned** Product: double length product is non-zero
> - Single-Length **Signed** Product: double length product is not sign-extension of low half.


### Exercises
- Computing a 64-bit single length product.
```
LDR R0, =Alo
LDR R1, =Ahi
LDR R2, =Blo
LDR R3, =Bhi

MUL   R1, R1, R2      // R1 = Ahi x Blo
MAL   R1, R0, R3, R1  // R1 = Alo x Bhi + previous
UMULL R0, R2, R0, R2  // R2·R0 = Alo x Blo 
ADDS  R1, R1, R2      // R1 += Upper Half of Alo x Blo

STRD  R0, R1 [MEM]    // MEM <-- R1·R0 

```



### Division
- Two different version is required for signed vs unsigned division

| FORMAT            | OPERATION      |
| ----------------- | -------------- |
| UDIV   R1, R2, R3 | R1 <-- R2 / R3 |
| SDIV   R1, R2, R3 | R1 <-- R2 / R3 |
>[!error] Division Overflow
> - Division by zero
> - 2's complement: full scale negative (-2^31) divided by -1


- Computing a Remainder = dividend - divisor x quotient
```
SDIV R2, R0, R1      // R2 = R0 / R1
MLS  R3, R1, R2, R0  // R3 = R0 - R2 x R1
```


