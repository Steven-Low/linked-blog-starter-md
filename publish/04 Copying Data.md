
##### 1) Register to Register
- MOV can load maximum 16-bit number {`0x0, ..., 0xFFFF` / `0, ..., 65535`}
```
MOV
```

##### 2) Immediate Constant to Register
```
MOV
MVN  R0, #1           // move inverted constant 0xfffffffe 
MOVW R0, #0xC0DE      // move to lower half of R0
MOVT R0, #0xBEEF      // move to upper half of R0
```

##### 3) Load Register Instructions
- Value loaded from `[memory]` to register is zERO extend by default.
- The most significant bits of register will be the content in higher address in memory.
```
LDR             // load word (32 bits)
LDRB            // load byte (8 bits)
LDRH            // load halfword (16 bits)
LDRD            // load double (64 bits) R
LDRSB           // load byte with sign extend
LDRSH           // load halfword with sign extend
```

##### 4) Store Register Instructions
```
STR             // store word
STRB            // store byte
STRH            // store halfword
STRD R0,R1,[R2] // store double mem[R2] <-- R1.R0
```
>[!question] Why no sign?
> Because data to be saved already finished processed.



### Addressing Modes ⭐
- Calculating a Memory Address for LDR / STR
- Adrs: Effective address
- MUST add prefix to the immediate value 
##### 1) Immediate Offset Mode
```
[R0]              // Adrs: R0
[R0, #4]          // Adrs: R0 + 4
```
##### 2) Register Offset Mode
```
[R0, R1]          // Adrs: R0 + R1
[R0, R1, LSL #2]  // Adrs: R0 + R1*4  !!!
```
##### 3) Pre-Indexed Mode
```
[R0, #4]!         // R0 <-- R0 + 4 ; Adrs: R0
```
##### 4) Post-Indexed Mode
```
[R0], #4         // R0 <-- R0 + 4 ; Adrs: R0
```

### Types of Addressing

- Immediate Addressing: Value included in an instruction
```
ADD   R1, R2, #100
```
>[!important] Immediate Value vs Constant
> An ***immediate value*** is not stored in memory but is part of the instruction while a ***constant*** is stored in memory like any other variable.

- Direct Addressing: Address is known
```
LDR   R1, =num
LDR   R1, [R1]
num:  .word 5
```

- Register Direct Addressing: Register-register operands
```
ADD   R1, R1, R2
```

- Register Indirect Addressing: 
```
LDR   R1, =arr
LDR   R2, [R1], #4
.data
      arr: .word 10
```

- Memory Indirect Addressing: Memory-memory
```
LDR  R1, =addr_num
LDR  R1, [R1]
LDR  R1, [R1]
.data
     num: .word 5
     addr_num: .word num
```

- PC-Relative Addressing: Special Case of Immediate Offset
```
LDR  R0, [PC, <displacement>]
```




### Pointer Arithmetic

```c
int16_t foo(int16_t **pps16){
	return **pps16;
}
```
>[!note]
> 1. `pps16` is a pointer to `[a pointer to an int16_t]` 
> 2. `*pps16` is a pointer to `[an int16_t]`
> 3. `**pps16` is a `int_16t`
```
    LDR   R0, =pps16
foo:
	LDR   R0, [R0]     // R0 = *pps16
	LDR   R0, [R0]     // R0 = **pps16
	BX    LR
```


### LDMIA/STMIA INSTRUCTIONS
- To load or store multiple block of data
- Increment after:  address start with Rn
```
// Copy 44 bytes: mem[R1] <-- mem[R0]

LDMIA   R0, {R2-R12}
STMIA   R1, {R2-R12}
```


### LDMDB/STMDB INSTRUCTIONS
- To load or store multiple block of data
- Decrement before: address just before Rn
```
// Copy 44 bytes: mem[R1-44] <-- mem[R0-44]

LDMDB   R0, {R2-R12}
STMDB   R1, {R2-R12}
```

### Memory Performance

-  Logically, memory is organized into bytes, each has its own unique 32-bit address.
- So, the most significant 30 bits tell where is the physical word.
- So, the least significant 2 bits tell what byte within the physical word. Now you know why address increment by four. `2^2 = 4`  
- Visual representation:

| <-- 8 bits --> | <-- 8 bits --> | <-- 8 bits --> | <-- 8 bits --> |
| -------------- | -------------- | -------------- | -------------- |
| `0x00`         | `0x00`         | `0x00`         | `0x00`         |
| `0x00`         | `0x00`         | `0x00`         | `0x00`         |

