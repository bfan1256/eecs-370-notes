# Compiled Machine Code via Linkers & FP Arithmetic

In practice, programs are made form thousands or millions of lines of code. 

If we compile each file into separate **object files**, then we only need to recompile one file and **link** it to the other, unchanged object files. 

## Source Code to Execution

How do we actually get from C to actual results? 

1. Comiplation into Assembly Object Files
2. Linkers that link libraries used in the program 
3. Converted into an executable
4. DLL libraries take over and load the executable
5. Data is executed on the CPU

```bash
gcc test c main.c // This happens for 1-3
```

### How gcc Works

1. C Preprocesseor
   1. Handles macros like `#defines, #`if` 
2. Compiler
   1. Actulaly converts it into assembly
3. Assembler assembles into an object code form for the linker
4. Linker then links any other libraries, which then creates an executable

### What are object files? 

Object files are the files that are linked together to form a program in the memory

They consist of: 

#### Header 

The header contains metadata including hte size of all the pieces in a file: text segment, static data, symbol table, relocation tale etc.

#### Text segment

The actual machine code (i.e. executable code statements)

*This is all read-only by the OS*

#### Data Segment

This is the value of all initialized globals, and the initialized static locals 

*This doesn't contan uninitialized data, but for EECS 370 this is all included*

#### Symbol Table

This is what is used to link to bind public entities within this object file (function calls and globals)

It maps string symbol names to specific values 

This also shows **unresolved labels**

Contains local scope for addresses of static local variables

#### Relocation Table

Identifies specific instructions that rely on absolute addresses. These references must change if portions of programs are moved within memory. 

This helps determine symbolic addresses. 

#### Debug Info

This contans info on where variables are in the vaious stack frames, source code line numbers etc.



## The Linker

The linker takes multiple object files and makes one executable file. 

It does this by "stitching them together".

1. Take the text segments from each .o file and put them together
2. Concatenate all the data segments together from each .o files, to the end of text segments
   1. Libraries follow the same structure here.
3. Resolve all cross-references to labels using symbol location and relocation tables. 

From here, we have to do some extra work to determine the start and end of the text segment and the data segment 

Linkers **must relocate absolute referneces to reflect placement by the linker**

- PC-relative addressing does not need to be relolcated
- Absolute addresses must be relocated
- External references msut also be relocated 
- Data referneces (movz/movk) always relocate 

In the end, the executable file will not have a relocation table or symbol table...this is only used by the linker



## An Example

### Symbol Table

Symbol takes everything that is global (external functions, global variables, general functions defined at the high-level)

**Parameters of functions do not go into symbol tables**

```c
extern void bar(int);
extern char c[];
int a;
int foo (int x) {
  int b;
  a = c[3] + 1;
  bar(x);
  b = 27;
}
```



| symbol | location                                       |
| ------ | ---------------------------------------------- |
| bar    | - (since we don't know where it is)            |
| c      | - (external again)                             |
| a      | data segment (first part in the data segment ) |
| foo    | text segment                                   |



### Relocation Table

| line | type | Dep  |
| ---- | ---- | ---- |
| 6    | stur | a    |
| 6    | ldur | c    |
| 7    | bl   | bar  |



## Loader 

The loader takes the executable file that is sitting on the disk. It puts the executable file code images into memory and asks the operating system to schedule it as a new process.

- The loader makes sure there is enough adderss sapce to hold the text and data segments along with the stack
- The instructions and data are randomly put at a specific address
- Initializes all registers (PC and SP)

It also does a wide variety of complex things like dynamically linked libraries and much much more. 



## Floating Point Representation

We need to figure how to accurately represent real numbers 

This is where scientific notation could really come in as it allows us to vary the different precisions 

### IEEE 754 Floating Point Format

The IEEE 754 floating point format contains a Sign Bit, a Significand (or the *mantissa*), the exponent (used a biased 127 encoding)

#### Example

10.625 -> 1010.101 -> **1**.010101 x 2^3 (we assume the front is always one so we do not store it)

Using IEEE, we have 

0 1000010 010101000000000...

Why is the middle 1000010? Well it's because of our **127 bias**, so we take the **power: 3** and add **127** giving us: **127 + 3 = 130 -> 10000010**

Why do we do this? Because we need to represent positive and negative exponents

#### Example #2

Take 1 10000101 01011001000000000000

1. The first value is a 1, so it's a negative nnumber
2. The exponent is 10000101 which is 128 + 4 + 1 = 133, we subtract 127 and get a power of 6
3. We add our implicit 1 and so we have 1.01011001 in binary 
4. Thus we have -1010110.01 (in binary) = -86.25

## Representing Decimals in Binary

**Simply do the items to the left of the decimal separately from the stuff on the right**

### Example

`10.625` is simply doing `10` and then `.625` which is `1010` and `.101` giving us `1010.101` 

Of course, we can't represent everything (0.1, 0.2, 0.3, etc.) 

This is why we also don't want to compare a double with zero

## Multiplying Real Numbers

What's the best way to multiply two real numbers? This is where we have to consider how we represent FP numbers etc.

### The Algorithm

1. Convert to binary
2. Convert binary numbres to IEEE 754 floating-point numbers
3. Multiply
   1. Sign bit - `xor`
   2. Add exponents - Make sure you pay attention to the bias, meaning that we have to subtract 127
   3. Multiply the significants

#### Example

```
10.625 = 1010.101, 10 = 1010
This gives us 0 1000010 0101010000....
and 0 10000010 010000....

2. Add exponents: 
  10000010
+ 10000010
- 127
_____________
10000101

3. Multiplicating the significands (MAKE SURE YOU DON'T FORGET THE IMPLICIT 1): 110101001
4. Remove the implicit 1 again and store the rest 
5. XOR signs: giving 0
```



## Adding Real Numbers

Adding real numbers is more complicated, remember that we can only add like powers.

Thus we actually have to shift our IEEE 754 floating point number to get equal powers in order to add the matissa. 

Then we renormalize back to the proper scientific notation. 

*The issue arises when rounding to the correct number (i.e. shifting smaller exponent could lead to overflow)*

### Algorithm

1. Convert to IEEE 754
2. Shift smaller exponent right to match larger
3. Add significands
4. Normalize and update exponent
5. Check for "out of range"

### Example 

1. 101.125 + 13.75
2. 101.125 = 0 1000101 1001010010000
3. 13.75 = 0 1000010 101111
4. Since 13.75 is less, we have to shift this over
   1. This means that we need to shift the mantissa **to the right by the difference including the implied one bit**