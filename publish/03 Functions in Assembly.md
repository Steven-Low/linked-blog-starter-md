### ARM User Registers

| Register Name | Usage                              |
| ------------- | ---------------------------------- |
| R0-R12        | General purpose registers          |
| R13           | Stack pointer (r13 or sp)          |
| R14           | Link register (calling subroutine) |
| R15           | Program counter (PC)               |
| CPSR          | Current Program Status Register    |
- The program counter (PC) contains the address of next instruction to be executed. The processor automatically increments this register by 4 after each fetched instruction.
- The link register (R14) holds the return address for subroutines.
- The stack pointer hold the address of top of stack.
##### Current Program Status Registers (CPSR)
^8379c4

- The first 4 bits are N, Z, C & V which are the condition flags.
	1) Negative: Signed result is negative
	2) Zero: Result of operation is 0.
	3) Carry: Operation result in carry out of MSB, or subtraction result in borrow.
	4) Overflow: Signed overflow occur in addition or subtraction or 2's complement.


### Four general instruction categories
##### 1) Setting and Using Condition Flags
- To ***set*** the CPSR flag during execution, the optional "s" modifier is added to the instruction. For instance, "add" + "s" = "adds".
- To ***use*** the flag, a conditional modifier may added to the basic instruction. For instance, "add" + "eq" = "addeq".

| `<cond>` | Description                    |
| -------- | ------------------------------ |
| al       | always (default)               |
| eq       | Z set                          |
| ne       | Z clear                        |
| ge       | N set V set or N clear V clear |
| lt       | N set V clear or N clear V set |
| gt       | same as ge but Z clear         |
| le       | same as lt but Z set           |
| hi       | C set and Z clear (unsigned)   |
| ls       | C clear or Z set (unsigned)    |
| hs / cs  | C set (unsigned)               |
| lo / cc  | C clear (unsigned)             |
| mi       | N set                          |
| pl       | N clear                        |
| vs       | V set (overflow)               |
| vc       | V clear (no overflow)          |
>[!tip] Tips
>The comparison is made on two registers of left to right, result in NZCV flags, considering positive and negative cases.

##### 2) Immediate Values
- Immediate values are constant. They can be specified in decimal, octal ("0"), hexadecimal ("0x") and binary ("0b").

##### 3) Load/Store Instructions
- The processor can transfer bytes (8 bits), half-words (16 bits), and words (32 bits) from memory to register, or from a register to memory.
- *Computational operations* are performed using 2 source operands and register as destination of the result.

##### 4) Branch Instructions
- Branch instruction changes the address of next instruction to be executed. Basically, there are 2 branch instructions. 
	1) Branch
	2) Branch and Link (to restore current position after subroutine call)
>[!note] 
>When the subroutine reach "BX LR" instruction, the PC will fetch the address stored in the link register since "BL" instruction previously cause LR <-- PC.


### Function Call and Return

| Instruction format | Operation                         |
| ------------------ | --------------------------------- |
| PUSH {R1, R2}      | SP <-- SP - 4<br>`MEM[SP]` <-- Rx |
| POP {R1, R2}       | SP <-- SP + 4<br>Rx <-- `MEM[SP]` |
- Nested return can be **optimized** by replacing "POP {LR} ; BX LR" with "POP {PC}".
- Sometimes, when the subroutine is at the last line, the "BL" can replace with "B" instead.



### Parameter Passing
- 2 instructions are needed to load the value from memory to the register when the memory location is distance from the instruction. This is because the instruction is stored in read only memory while the variables/stack/heap are stored in read/write memory. (There is a big gap between!)
```
LDR    R0, =z64
LDRD   R0, R1, [R0]       // R1.R0 <-- given R1 is MSB

.data
	z64: .word 128
```

- When the instruction is near the memory location (within 4095 bytes), address "displacement" can be used to obtain the value.
```
LDR    R0, z             // R0 <-- 256
.
<-- displacement is short -->
.
z: .word 256

```



### UXT & SXT INSTRUCTIONS

- Zero extend and sign extend are frequently used for preparing return value

| Instruction     | Operation                                                       |
| --------------- | --------------------------------------------------------------- |
| UXTB   Rn, op2  | Extract lowest byte in op2 then zero extend it and store at Rn. |
| UXTH   Rn, op2  | Extract lowest halfword in op2 then ...                         |
| SXTB    Rn, op2 | Extract lowest byte in op2 then sign extend it and store at Rn. |
| SXTH   Rn, op2  | Extract lowest halfword in op2 then ...                         |
