# Data Layouts

Typically calculating continuous stores of memory 

## Memory Calculations

Very straightforward, simply calculate the amount of bytes each type is. 

* Note if it's an array, it's size * number of elements. 

### Examples

`short a[100]` : 2 bytes * 100 = 200 B where B is bytes. 



## Variable Layout

Most modern ISAs require that data be **aligned**

*This means that an N-byte variable must start at A such that (A % N) == 0*

### Golden Rule

**The address of a variable is aligned based on the size of the variable**

### Sample Layouts 

* char is byte aligned (meaning any address is fine) 
* short is half-word (H) aligned (LSBit of address must be 0) 
* int is word aligned (W) (2 LSBit's of addr must be 0)

## Struct Alignment

In order to align a `struct` , the struct starts using the largest (primitive) field.

* The starting address is based on the largest field. 
* The size of the struct is a **multiple** of the largest primitive field. 
* The struct must **start** at an address $a$ such that `a % 4 == 0`
* **Pointers are 8 bytes on a 64-bit system, not the size of the primitive type**

### Example 

Given:

```c
struct {
  char w;
  int x[3];
  char y;
  short z;
} s;
```

What boundary should this be aligned to, and what is the total size of the struct?

#### Solution

The boundary is given by the largest primitive type which is an int (4 Bytes). This means that the size of the struct must be a multiple of the largest primitive type and that the starting address `a` must be divisible by four bytes. 

Choosing an arbitray starting adderss that's divisible by four (say 1000), we can do the following calculations. 

* `char w` is 1 byte so it can be placed anywhere (so at 1000)
* `int x[3]` where each is 4 bytes must start at a multiple of 4 (the nearest is 1004). **The other 3 bytes are just used as padding**
  * In total we would fill up 1004 to 1015 as we need 12 bytes.
* `char y` is 1 byte so it can be placed in the nearest byte (1016). 
* `short z` is 2 bytes and it must be placed into a multiple of two so we put it into `1018`
* Summing up, we get a total of 20 bytes (which is divisible by four, so we don't need to add any additional padding). 
* S = 1000 -> 1019

## Applications

Compilers **will not** optimize your structs for padding. Thus, to avoid issues with unnecessary memory use, consider always putting the **smallest** types first and then transitioning to larger ones. 



