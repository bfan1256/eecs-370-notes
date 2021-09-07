# Instruction Set Architecture

## What is it? 

Instruction set architecture (ISA) is the lowest level code and is the interface between hardware and software (x86 Intel CPU vs Apple A9 have different ISAs). 

When your program is compiled, the ISA takes over and properly stores the instructions into specific **registries** including the Program Counter (PC) and the Instruction Register (IR) in a von Neumann Architecture. 

### What is a Bit?

The smallest unit of data storage (values of 0 and 1). 

- Byte: A collection of 8 continguous bits

### Interesting Questions

The important questions rely around what specific instructions can be implemented. For example, how about add, multiply, divide, sort etc.? How about doing branch or load/store (this include memory management, etc.) 

How many registers (places of fast storage for the CPU) do you want to have? 

How are instructions **actually** encoded? 

### Von Neumann Architecture

Von Neumann Architecture stores data and instructions in the same memory space: this includes stack and heap data, stsatic data, the text (which is the program compiled) and any reserved space.This allows programs to be viewed as data which simplifies storage. 

*Most generally used architecture*

### Registers

Registers are a small array of storage locations in the processor, it's directly a part of the processor which makes it super fast access.

- They can only be accessed **directly** (that means they cannot be found via an offset of another address)

*There can be special purpose registers including Program Counters or Instruction Registers*

- Return Address: Holds the return address of a subroutine
- Stack Pointer: Holds hte memory address of the stack 
- Frame Pointer: Holds the memory address of the start of the stack frame
- Program Counter: Cannot be directly accessed (due to security) 

#### Notes

- Instructions are stored sequentially in memory 
  - The next instruction is the next byte in memory (could implement a byte counter)
- When it comes to jumps and branches, (conditionals = branches), (jumps means jumping back say in a while loop)
- The stack is the activation records (the data that is stored, local variables, parameters, etc.)
- Heap is the dynamically allocated data -- `new` or `malloc()` 

#### Caveats

- Data can be viewed as instructions which could lead to malicious attacks of security especially if the instruction register is changed. 

#### Overall Process

1. Fetch - this g ets the next instruction. Using the Program Counter (PC) t o find the instruction and put it into the instruction register.
   1. The PC changes to "point" to the next instruction
2. Decode - Take memory space to determine functionality
3. Execute - Produce the outcome (aritmetic or logic operation on memory space)
4. Back to Step 1

## Instruction Encoding

C++ code is converted into **assembly code** which ultimately is converted into machine code (typically a one-to-one correspondence). Assembly code represents the most base level operations that are right before machine code. The process is shown below: 

| opcode | dest | src1 | src2 |
| ------ | ---- | ---- | ---- |
| ADD    | X2   | X1   | 100  |

**opcode** : represents the specific operations (ADD, SUBTRACT, MUL, DIVIDE, etc.)

**dest**: Where the result will be stored

**src1**: Data from X1 

**src2**: Data from X2

This specific example adds X1 (in the content registry) + a constant 100 and stores it into X2 (content registry) which will then be stored at a memory address.

*Typically speaking, can execute fast, but compilers are already optimized*

*Assembly Code and be mixed directly with C to produce some really really fast code.*

That being said, typically assembly code is restricted and limited to a speciifc ISA. 

### ARM Examples

| opcode | dest | src1 | src2                | Pseudocode   |
| ------ | ---- | ---- | ------------------- | ------------ |
| ADD    | X2   | X1   | X2                  | X3 = X1 + X2 |
| ADDI   | X3   | X3   | #3 (must use the #) | X3 = X3 + 3  |
| SUB    | X2   | X3   | X1                  | X2 = X3 - 1  |



## Instruction Encoding to Binary and More

| opcode | dest | src1 | src2 |
| ------ | ---- | ---- | ---- |
| ADD    | X2   | X3   | X1   |
| 011011 | 010  | 011  | 001  |

This totals: 011011010011001= 13977

**Total Number of N values can be encoded in the ceiling of $log_2 (n)$**

### Example

What is the max number of registers that can be designed in a machine given: 

- 16-bit Instructions
- Num. Opcodes = 100
- All instructions are (reg, reg) -> reg

#### Solution

1. We know that there are going to be 100 Opcodes which requires the ceil of $\log_2{100} = 7$, thus 7 out of the 16 bits will be used for opcodes. 
2. The rest of the 9 should be split evenly across the operands, which gives us a **3, 3-bit operands.** 
3. Using this knowledge, for each operand, we can subsequently access $2^3 = 8$ total registers.

### Example

Convert an instruction into binary, hex, and decimal 

**Problem:** ADD opcode is 53 

| Conversion | opcode     | dest      | src1       | src2       |
| ---------- | ---------- | --------- | ---------- | ---------- |
| Assembly   | ADD        | X2        | X3         | X1         |
| Binary     | 0110101    | 010       | 011        | 001        |
| Hex        | (0110) = 6 | (1010) =A | (1001) = 9 | (1001) = 9 |
| Decimal    | 27289      |           |            |            |

**Notes: To Convert to hex, take the binary, split it evenly into 4 and then convert every 4**

## Instruction Decoding to Assembly

To convert a decimal, hex, or binary into assembly, make sure you understand the **ISA itself** (how many bits for the opcode and operands?)

### Steps to Solve

1. Convert to Binary
2. Separate into Fields based on Bits
3. Convert each to decimal to correpsond the OPCodes
4. Convert Assembly Instruction Fields

### Examples

Convert 27292 into an assembly code. 

We're looking for the closest 2 that overpasses 27,292. Thus, we can see that $2^{14} + x$ , to find x we subtract $2^{14}$ , ultimately we get  $2^{14} + 2^{13} +  2^{11} + 2^{9} + 2^{7} + 2^{4} + 2^{3} + 2^{2}$ which ultimately gives us a binary representation of **0110 1010 1001 1100**

We then separate into fields (remembering that OPCODE is 7 bits and the rest are 3)

| Unit     | opcode  | dest | src1 | src2 |
| -------- | ------- | ---- | ---- | ---- |
| Binary   | 0110101 | 010  | 011  | 100  |
| Decimal  | 53      | 2    | 3    | 4    |
| Assembly | ADD     | X2   | X3   | X4   |

### Useful Conversions to Know

| Power    | Notation | Total      |
| -------- | -------- | ---------- |
| $2^{10}$ | k        | 1024       |
| $2^{20}$ | m        | 1048576    |
| $2^{30}$ | g        | 1073741824 |



## Accessing Memories

Memory can be accessed in multiple different ways from a CPU. 

### Direct Addressing 

Specifying the address as immediate (constant) in the instruction. However, this method is a little inpractical as you have to store a 32-bit for a memory address. 

```
load r1, M[ 1500 ]; // Load contents into r1 from 1500
jump #6000; // jump to address 6000
```

### Indirect Addressing

This is storing a reference address (like a pointer) into the register. 

```
load r1, M[ r2 ]
add r2, r2, #4
load r1, M[ r2 ]
```

### [Most Common] Base + Displacement

Load and then offset final addresses. 

- This is the most common addressing mode 
- Address is computed as a register value + an immediate constant 

```
load r1, M[ r2 + 4 ]
```

This is super useful for accesing items at structs (only store the memory addres for the start of the struct).

### Program Counter (PC) Relative

This is very useful for P1a 

Variation of base + displacement where PC register is the base. 

Acts as a relative distance from PC (which can be positive or negative) 

```
jump [ -8 ]
jump [ 2 ]
```

Typically, this is done by humans via **assembly language labeling**

#### Example Assembly

```
Address
3 		beq 0 1 2 // goto the end of program when reg1 == 0
This will go to PC + 1 + offset, which would be 3 + 1 + 2 so line 6
```

In this example, 2 acts as an offset where as a *symbolic address* goes to the location it should go. 



