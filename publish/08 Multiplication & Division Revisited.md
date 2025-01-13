
### Multiplication via Bit Shift
- Multiply by 10 can be thought as multiply by (8+2)
```
LSL  R1, R0, #3           // R1 = R0 * 8
ADD  R0, R1, R0, LSL #1   // R0 = R1 + R0 * 2
```

- Multiply by 5
```
LSL R0, R0, R0, LSL #2    // R0 = R0 + R0 * 4
```

- Multiply by 7
```
RSB R0, R0, R0, LSL #3    // R0 = R0 * 8 - R0
```

- Multiply by Arbitrary constants tricks:
1) Binary Decomposition
2) Substring Optimization
3) Factoring

>[!tip] RSB Instruction
> Reverse Subtract is useful in the case where operand combination of LSL or LSR is used! Therefore: `RSB R1, R1, R1, LSL 9   // R1 = (2^9 - 1) * R1 `    



### Division via Bit Shift
- Division other than powers of 2 is problematic: 
1) Distributive in one direction: `60/10 ≠ 60/8 + 60/2 `
2) Dividend of negative will be truncated

>[!note] To divide by 2^k
> Add `2^k -1` to negative dividends before the k-bit arithmetic shift right (ASR)


### Division by an Arbitrary Constant
- Using Integer multiplication to do integer division
```
A / d = A x 1/d
      = (A x 2^N/d) ÷ 2^N
      = (A x 2^N/d) >> N 
```
```
EXAMPLE:  Divide by 100 in 32 bits wide

LDR   R0, [&x]
LDR   R1, =42949673    // 2^32 ÷ 100 (ROUNDED)
SMULL R0, R1, R0, R1   // R1·R0 <-- R0 x R1
STR   R1, [...]        // The result is at R1 (MSB) 
```


### Finding Remainder Without Divide Instruction
- Finding remainder **without** divide instruction <u>if and only if</u> divisible by `2^k`
	I) Positive dividend: `Remainder = N & (2^k - 1)`
		`AND R0, R0, #7`
	II) Negative dividend: `Remainder = (N & (2^k - 1)) - 2^k`
	   `ANDS R0, R0, #7`
	   `IT   NE`
	   `SUBNE R0, R0, 8`
		   

- When y ≠ `2^k`:
```
SDIV   R2, R0, R1      // R2 = quotient
MLS    R3, R1, R2, R0  // R3 = remainder
```


### Finding Modulus
- Modulus is a kind of remainder that is greater than zero (adjusted remainder)
- When y = `2^k`:   N & (2^k - 1)
```
AND  R0, R0, #k
```
- When y ≠ `2^k`:
```
SDIV   R2, R0, R1      // R2 = quotient
MLS    R3, R1, R2, R0  // R3 = remainder

AND    R2, R1, R3, ASR 31  // take divisor if negative
ADD    R0, R3, R2          // add divisor|0 to remainder to get modulus
```


