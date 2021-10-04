# C to Assembly

Converting C code to assembly is all about analyzing differnet parts of branching and ensuring that references to memory are properly calculated and accessed. 



## Control Flow with Functions

Considering a sample example as so: 

```c
int foo() {
  int x = 5;
  bar();
  x + 1 = 6;
  return;
}

int bar() {
	int x = 2;
  return;
}
```

In this situation, we have to recognize that **bar** and **foo** must have a way to return to the function of which they were called from. Thus, there must be an *assembly command* that allows them to link to a **stored location** (since bar and foo could be called from any function, we have to store the address in a register).

This is typically done with **Branch with Link** (BL).

### Saving Data to Stacks

There are only a set number of registers, when calling various functions, it's almost impossible to ensure that registers don't spill into each other. Thus, this is why we store items into the **stack** (as mentioned below), which is a memory frame that allows all the data for a given function to be temporarily stored as an inner function completes its work. 

**Spilling Registers**: The process of putting less frequently used variables (or those needed later) into memory. 

A value that is defined before a function call and needed after a function call is said to be **live**.

There are two options: **caller save** or **callee save**. Typically, the solution is to use both in specific situations. 

#### Caller Save

Caller save means that the "caller" must save to the stack any registesr whose values it needs later. 

Once the inner function returns, then load everything back. 

**Caller saves must store and load if the variables they hold are live across a function call**

**It is better to save non-live variables via caller save registers**

Thus for an example likes below:

```c
void foo() {
  int a, b, c, d;
  a = 5; // X1
  b = 6; // X2
  c = a + 1; // X3
  d = c - 1; // X4
  // must save X1 and X4
  bar();
  // restore X1 and X4
  d = a + d;
  return;
}
```

#### Callee Save

Callee save is the other way around where the function saves every registers before it will do anything else. Thus, knowing the registers that specific function would use, then we'd save all of those first.

```c
void foo() {
  int a, b, c, d;
  // save X1, X2, X3, X4 to stack
  a = 5; // X1
  b = 6; // X2
  c = a + 1; // X3
  d = c - 1; // X4
  bar();
  d = a + d;
  // restore X1, X2, X3, X4 from the stack
  return;
}
```

**Callee save must store and load if they use registers of any sort**

**It is much better to save live variables in callee save**

*`main()` is an exception as there is no need to store and load for callee-save as there is no higher function*

### Passing in Arguments

There are two options: registers vs memory

* Registers are fast access b ut few in number and the wrong size for some objects
* Memory is a good general solution but slow 
* **The solution is to use a hybrid approach** where the first few arguments are put into a register and the rest are put into the call stack

ARM conventions (most processors too) will allocate a region of the memory for a specific call stack

This is the concept behind a **stack frame** which are a set of static locations. 

## Variables

There are 3 locations where variables are stored: stack, heap, and static.

*All global variables and static variables are found in static*

*Stacks are local variables relative to a frame*

*Heap is any dynamically allocated memory*

Functions will always create new stack frames. 