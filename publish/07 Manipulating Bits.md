
### Bit Patterns
- Also known as "mask", is a string of 1's and 0's designed to select a subset of bits from another operand.

```
LDR   R0, =(1<<6)     // A single 1 shifted left k bits
LDR   R0, =~(1<<6)    // A single 0 shifted left k bits (rest is 0s)
LDR   R0, =(1<<6) - 1 // A string of k 1's, preceded by all 0's
LDR   R0, =-(1<<6)    // A string of k 0's, preceded by all 1's
```


### Shift Instruction
##### LSL and LSR instructions
- Shift Left:  `LSL  R1, R2, R3` OR `LSL  R1, R2, #1`
- Shift Right: `LSR  R1, R2, R3` OR `LSR  R1, R2, #2`
##### ASR Instruction
- ASR instruction is similar to LSR instruction except that it consider the **sign** bit of the integer number to be shifted into the register.
- This ensure that the sign is preserved during division arithmetic
- `ASR  R0, R1, #2`    // Division by 4
##### ROR and RRX instructions
- Rotate right: `ROR 
- Rotate Right Extended: `RRX` , similar to ROR except that it insert current carry bit to the register and store the shifted out bit back to the carry bit.

### PRE-SHIFT Adjustment
- In negative dividend division operation, it is required to add 1 to the dividend register to yield valid sign result.
```
LDR    R0, =0x8000000A
ADD    R0, R0, R0, LSR 31    // Get the sign magnitude
ASR    R0, R0, #1            // divide by 2
```


### 64-bit Logical Shift Left
- To shift the 64 bits registers by k-bits
```
// EXAMPLE: k=2:

LSL    R1, R1, #2            // Shift the high register by 2 bits
ADD    R1, R1, R0 LSR #30    // Get the first 2 bits from low register
LSL    R0, R0, #2            // Shift the low register by 2 bits
```
>[!important] SEE THIS !!! 
> - To shift only 1 bit or rotate by one bit, simply add the register by itself, equivalent to multiply by 2 or shift left by 1.
> ```
> // EXAMPLE: k=1:
> 
> ADDS  R0, R0, R0     // Shift LS by 1 bit, set carry
> ADCS  R1, R1, R1     // Shift MS by 1 bit and add carry (rotate? set C)
> ```


### Bitwise Operation
There are four logical operations in ARM assembly:

| **Operation** | **Description**                  | **Effect**                                     |
|---------------|----------------------------------|-----------------------------------------------|
| `AND`         | Bitwise AND operation           | Sets each bit to 1 if both corresponding bits are 1 |
| `ORR`         | Bitwise OR operation            | Sets each bit to 1 if either of the corresponding bits is 1 |
| `EOR`         | Bitwise XOR (exclusive OR)      | Sets each bit to 1 if the corresponding bits are different |
| `BIC`         | Bit Clear (AND NOT) operation   | Clears bits in the first operand where the corresponding bits in the second operand are 1 |

### Bitfields Operation
- **Extracting Unsigned Bitfields**:
```
LSL R1, R0, 8    // Shift bitfield to the left end
LSR R1, R1, 23   // Abstract bitfield of length 9 by shifting 32 - 9 = 23
```
- **Extracting Signed Bitfields**:
```
LSL R1, R0, 8    // Shift bitfield to the left end
ASR R1, R1, 23   // Abstract bitfield while preserving sign extension
```

- Alternative: single instructions (given 15 is starting bit to clone, 9 is width)
	Unsigned: `UBFX  R1, R0, #15, #9`   
	Signed: `SBFX  R1, R0, #15, #9`

- **Clear Bitfields or Set Bitfields**:
```
BFC   R1, #0, #4         // Clear the first 4 bits 
BFI   R1, R2, #16, #16   // Set the upper half register with value R2
```

 
### Programming Tricks ️‍🔥

##### Register Swap Using Exclusive-OR 
- No need extra or temporary register !!!
```
EOR  R0, R0, R1
EOR  R1, R1, R0
EOR  R0, R0, R1
```

##### Selection Without a Branch
- We can make use of the inverse proportional of AND and BIC
- Example:  **R5 = (R0 < 0) ? R1 : R2**
```
AND  R3, R1, R0, ASR 31  // R3 = (R0 < 0) ? R1 : 0
BIC  R4, R2, R0, ASR 31  // R4 = (R0 < 0) ? 0  : R2
ORR  R5, R4, R3          // Either one of R3 or R4 will be 0!
```


##### Fast Absolute Value Function
- To do a 2's complement on negative value or do nothing on positive
```
EOR  R1, R0, R0, ASR #31   // ~s32  OR  s32
ADD  R0, R1, R0, LSR #31   // ~s32 + 1 (2's complement) OR s32 + 0
```

##### Integer Power Function
- Decompose exponents into power of 2
- Example: `x^100 = (x^64)(x^32)(x^4)`

```
ipow: 
	LDR   R2,=1     
loop:
	CBZ   R1, done
	LSRS  R1, R1, #1    // R1 = n; Store shifted out bit to C.
	MULCS R2, R2, R0    // Result *= x
	MUL   R0, R0, R0    // x *= x
	B     loop
done:
```


### Bit Banding
Bit banding is a technique used in ARM microcontroller to provide atomic bit-level access to memory regions in **single instruction**. There are specific bit-banding regions in the memory map which can be addressed through a corresponding word alias region:

| **Region Type**         | **Address Range**        | **Alias Region**          | **Alias Address Range**       |
|-------------------------|--------------------------|---------------------------|--------------------------------|
| SRAM Bit-Banding        | `0x20000000` - `0x200FFFFF` | SRAM Alias Region         | `0x22000000` - `0x23FFFFFF`    |
| Peripheral Bit-Banding  | `0x40000000` - `0x400FFFFF` | Peripheral Alias Region   | `0x42000000` - `0x43FFFFFF`    |
##### Finding the Alias Address
- The alias address to access the bit always have two zero bit at LSB.
```
Bit-band_Alias = 22000000 + 32 x Bit-band_region_offset + 4 x bit_number
```
>[!error] Mistake
> `0x23FFFFFF` is not used to address `0x200fffff`. Instead, the correct value is `0x23fffffc` which access the bit 7 (last bit) of bit-banding address `0x200fffff`. 
> - Noted that is is the 32-bit word from address `0x23fffffc - 0x23ffffff` that map to the bit!
