# Finite State Machines & Memory

## Definitions

Finite state machines offers an ability for us to understand all the possible states that a given state can be in. 

It models the system in specific state enumerations, and the conditions that allow the machine to transition between two states. 

This is often expressed via a directed graph or via table. 

### Moore Machine vs Mealy Machine 

Moore Machines are defined as `P(S)` while Mealy Machines are defined as `P(S, I)` where the Mealy Machines depend not only on the previous state, but also the new input, where as the Moore machines depend only on the previous state

## Using Memory

Doing everything via combinational logic systems is tedious, expensive, and ultimately can be slower than just directly accessing results or pre-computations found in read-only memory. 

### Read Only memory

- Memory where array values are constant and **non-volatile**
- *Programmable Read Only Memory* allows writing only once 

We can use ROM to represent **FSM** because we know all the given states possible. 

We can use ROM and Combinational Logic to design meaningful circuits that accurately represent FSM. (Decoders can be used to access ROM data)