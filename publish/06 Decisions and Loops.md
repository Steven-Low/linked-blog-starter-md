
### Branches in ARM v7
- The `B`, `BX` and `BL` instructions are used to change the Program Counter to a new address for processor to fetch. 
- The linker will calculate the PC-relative offset necessary to jump to ***myroutine***
```
B  myroutine
BL myroutine
```

- The `BX` and `BLX` instructions use an address contained in a register. That's it.
```
BX  R0
```

- Comparing Zero Branch
```
CMP   R0, #0
BEQ   LABEL       // double instruction

CBZ   R0, LABEL   // single instruction
CBNZ  R0, LABEL   // single instruction
```

>[!important] CMP vs CMN
> - CMN is the invert of CMP in which in add the 2nd operand to the first register instead of subtract it.
> - The assembler may convert in CMP to CMN in such case:
> ```
>  CMP  R0, #-20  // becoming CMN R0, #20
> ```
 


### Conditional Branch
- Unsigned: `BHI  BHS  BLO  BLS`
- Signed: `BGT  BGE  BLT  BLE`
- Shared: `BEQ  BNE`
>[!note] Note
>Conditional codes can refer back to [[03 Functions in Assembly#1) Setting and Using Condition Flags | Condition Flag]]



### The IT Block
- Every branch taken causes a delay of execution phase, reducing performance, therefore IT block reduce the need of branch. 
- Limitations:
	1) Only 1 to 4 instructions may be controlled
	2) Cannot contain branch or other IT instructions unless the last one
>[!special] Do you know?
> Instructions within IT blocks are 16-bits, this mean reducing code size => run faster
>  ```
> ITTTT AL                  // ALWAYS DEFAULT
> MULAL R1, R2, R3
> ADDAL R1, R2, R3
>  ```



### Compound Conditionals
##### Compound Logical Or
- Simply goto the "then" statement when first condition match and **SKIP** the not match case to "else".
``` 
// if (x < -100 || x > 100) 
	CMP R0, #-100
	BLT then
	CMP R0, #100
	BLE else
then: 
	...
else:
	...
```

#####  Compound Logical And
- Simply goto the "else" statement when the inverted condition matched. (To **SKIP** the "then" statement whenever possible)
- Use De Morgan's law to replace `&&` by `||`. (refer table below to invert conditions)
```
	CMP  R0, #-100
	BLT  else
	CMP  R0, #100
	BGT  else
then:
	...
else:
	...
```

##### Symmetrical Table of Boolean Algebra

![[Pasted image 20250113160152.png | 500]]


### LOOP

- A **while loop** evaluate the loop condition before the loop body

```
	MOV  R3, #3
loop:
	CMP  R3, #0    // alternative: CBZ (single instruction)
	BEQ  exit
	... do something ...
	SUB R3, #1
	B loop

exit:
```

- A for loop use only one branch at the end
```
	MOV  R3, #3
loop:
	... do something ...
	SUBS R3, R3, #1
	BNE loop          // if R3 = 0, finish
	
finish:
```

##### Example: Greatest Common Divisor (GCD)
- The pseudo code is beautiful :D
```
gcd:
	CMP R0, R1
	BEQ exit
	ITE HI
	SUBHI R0, R0, R1
	SUBLO R1, R1, R0
	B gcd
```


