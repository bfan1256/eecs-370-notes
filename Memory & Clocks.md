# Memory & Clocks

Unfortunately, what we soon begin to realize is that if we don't have memory (or any representation of state), then it's almost impossible to do any useful operations. 

## The S-R Latch

The S-R Latch is a way to do this: 

Image: https://www.allaboutcircuits.com/textbook/digital/chpt-10/the-gated-s-r-latch/

As you can see, depending on the values of 0, 0, and the start of the circuit, the values of Q and !Q will depend on its previous states. 

You can also see the truth variable above. 

Another useful way of approaching this is using the concept of SET (S) and Reset (R), where when the SET bit is set to 1, then we are SETTING Q to 1. 

Now, if we use S 0 and R 0, then we maintain the current state of Q and !Q

**S and R should never both be 1 1 as this introduces an infinite loop and undefined behavior**

## The D-Latch

The D-Latch allows us to use an additional D input and a Gating Signal that is *on top* of the S-R latch. 

### Reasoning for This

This is because we want to reduce the error of having both 1 and 1: 

Specifically we see that **R is 1 iff !D && G** and **S is 1 iff D && G** therefore making it IMPOSSIBLE for both to be 1 and 1

### The Gate Signal

Within the Truth Table, the next state is defined when the Gate Signal is set to 1. 

## Issues with Propagation Delay

Things through wires travel at the speed of light. Unfortunately, items are not perfectly on and perfectly off, but take a while to "heat up" 

## Introducing the Clock

The clock, in this case is a **pulse at a given frequency (given in hertz)**, we will read and update values on the **rise of the clock**

### Rising-Edge Triggered D Flip-Flop

Adding an additional Clock Gate that links to both the D-Latch and a FINAL D-Latch 

- When the clock is low, data is propagated into little q, and when clock is high, the data from little q is then propagated into Big Q

In practice, this means that whatever the value Data is when the Clock is Rising, that's the form of the data it will have

We can also add an `enable` parameter that allows data to change only when it is enabled and NOT when it's every clock rising edge



