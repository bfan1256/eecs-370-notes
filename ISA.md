# Instruction Set Architecture

## What is it? 

Instruction set architecture (ISA) is the lowest level code and is the interface between hardware and software (x86 Intel CPU vs Apple A9 have different ISAs). 

When your program is compiled, the ISA takes over and properly stores the instructions into specific **registries** including the Program Counter (PC) and the Instruction Register (IR) in a von Neumann Architecture. 

### Interesting Questions

The important questions rely around what specific instructions can be implemented. For example, how about add, multiply, divide, sort etc.? How about doing branch or load/store (this include memory management, etc.) 

How many registers (places of fast storage for the CPU) do you want to have? 

How are instructions **actually** encoded? 

### Von Neumann Architecture

Von Neumann Architecture stores data and instructions in the same memory space: this includes stack and heap data, stsatic data, the text (which is the program compiled) and any reserved space.This allows programs to be viewed as data which simplifies storage. 

*Most generally used architecture*

#### Notes

- instructions are stored sequentially in memory 
  - The next instruction is the next byte in memory (could implement a byte counter)
- When it comes to jumps and branches, (conditionals = branches), (jumps means jumping back say in a while loop)

#### Caveats

- Data can be viewed as instructions which could lead to malicious attacks of security especially if the instruction register is changed. 

#### Overall Process

1. Fetch - this g ets the next instruction. Using the Program Counter (PC) t o find the instruction and put it into the instruction register.
   1. The PC changes to "point" to the next instruction
2. Decode - Take memory space to determine functionality
3. Execute - Produce the outcome (aritmetic or logic operation on memory space)
4. Back to Step 1

## Instruction Encoding

C++ code is converted into assembly code which ultimately is converted into Machine Code. Assembly code represents the most base level operations that are right before machine code. The process is shown below: 

| opcode | dest | src1 | src2 |
| ------ | ---- | ---- | ---- |
| ADD    | X2   | X1   | 100  |

**opcode** : represents the specific operations (ADD, SUBTRACT, MUL, DIVIDE, etc.)

**dest**: Where the result will be stored

**src1**: Data from X1 

**src2**: Data from X2

This specific example adds X1 (in the content registry) + a constant 100 and stores it into X2 (content registry) which will then be stored at a memory address.