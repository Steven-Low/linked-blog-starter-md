
### Binary Whole Number

| 2^n  | Unit      |
| ---- | --------- |
| 2^10 | Kilo (K)  |
| 2^20 | Mega (M)  |
| 2^30 | Giga (G)  |
| 2^40 | Tera (T)  |
| 2^50 | Penta (P) |

### Binary to Decimal Conversion
Fraction part may involve tedious summing process such as 0.5 + 0.25 + 0.125 + 0.0625... Hence, an easier method will be shifting the decimal point to the end of fractional binary number, then divide the result with the 2 to the power of shifted digits.

```
10.010111 = 10010111 / 2^6
          = 151 / 64
          = 2.359375 
```


### Representation Error
- Consider the representation of 1/10 using 8 fractional bits
```
Absolute error = Desired value - Represented value
               = | 1/10 - 00011001/2^8 |
		       = 0.00234375

Relative error = Absolute error / Desired value x 100
			   = 2.34 %
```


### Decimal to Binary Conversion
##### Method 1: Separate problem into 2 parts:
- Integer part: Use repeated division
- Fractional part: Use repeated multiplication
##### Method 2: Decompose the decimal into corresponding sum of power 2
- Sum up from the most significant power 2 first


### Number Representations
- Resolution (fineness): Determined by the number of digits 
- Precision (reproducibility): How many digits remain the same every time measure
- Accuracy (correctness): How accurate is the measurement


### Finding 2's Complement
##### Method 1: Standard Way
- Invert all bits
- Adding 1 to the least-significant bit position
##### Method 2: Turnaround Way
- Copy right to left until first 1
- Copy remaining in opposite bits

### 2's Complement to Signed Decimal Conversion
##### Method 1: 
- Positive Value (MSB = 0): Same as unsigned (no need 2's conversion)
- Negative Value (MSB = 1): Do 2's then add a leading minus sign
##### Method 2: 
- Use Polynomial evaluation but make the sign of ***most-significant bit*** position ***negative***.  E.g. Consider an 8 bit example below:
``` 
10001100 = -128 + 8 + 4
         = -116
```
>[!important] Hints
>One should also consider "Zero extending" and "Sign extending" during the conversion, whereas if positive, add leading zeroes; if negative, add leading ones until sufficient bits value is matched.



### Power Relationship
- A power relationship is formed when each base digit can be represented by another base digit in integer pair(s).
- For instance, each digit of hexadecimal number can easily be represented by 4 base 2 digits. Or each digit of octal number can easily be represented by 3 binary digits.
```
0x2F = 0010·1111
027 = 010·111 
```
- Example: Convert 784_9 to base 3
- Since 3^2 = 9,  each base 9 digit can be represented with 2 base 3 digit.
```
784 = 21·22·11
```
