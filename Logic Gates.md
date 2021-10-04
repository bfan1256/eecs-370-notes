# Logic Gates

Logic gates make up the forefront of how computers work and how operations are eventually made. 

## The Materials 

From its basis, the materials that we use are conductors and insulators

**Conductors**: a material that permits electrical current to flow easily (low resistance to current flow) 

**Insulator**: a material that is a poor conductor of electrical current

**Semi-conductor**: A material that can switch between a conductor and an insulator

*Semiconductors form the basis for "logical switches" that make up digital circuits*

## Gates

### The Inverter

The inverter simply converts a bit. 

| Input | Output |
| ----- | ------ |
| 0     | 1      |
| 1     | 0      |

### The NAND

This stands for "NOT AND" (A NAND B = `! (A && B)`)

| Input | A && B | Output |
| ----- | ------ | ------ |
| 0, 0  | 0      | 1      |
| 0, 1  | 0      | 1      |
| 1, 0  | 0      | 1      |
| 1, 1  | 1      | 0      |

### NOR Gate

This stands for "NOT OR" (A NOR B = ! ( A || B))

**NOR and NAND is logically complete, meaning all gates can be implemented using only NORs**

| Input | A \|\| B | Output |
| ----- | -------- | ------ |
| 0, 0  | 0        | 1      |
| 0, 1  | 1        | 0      |
| 1, 0  | 1        | 0      |
| 1, 1  | 1        | 0      |

#### Number of Gates from the Basic Gates

This is simply $2^n$ where n is the number of combinations

### NOR Gates Example

#### INV (!) using NOR

This one is very straightforward, simply NORing A with A, we get 

A NOR A, which will be either 0, 0 or 1, 1 -> giving us 1 and 0 and thus satisfying INV

$$ !A = \ !(A || A )$$

### AND using NOR

We know that !!(A && B) = !(!A || !B) = (A NOR A) NOR (B NOR B)

A && B = !(!A) || !(!B) = (A NOR A) NOR (B NOR B)

#### OR using NOR

A || B = !(!(A || B)) = INV(A NOR B) = (A NOR B) NOR (A NOR B)

## Building an Addition Circuit

Let's first look at how to add two bits: 

We want to design a circuit that will take two bits as input (A and B) and generate a sum and carry bit (S and C)

| A    | B    | C    | S    |
| ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    |
| 0    | 1    | 0    | 1    |
| 1    | 0    | 0    | 1    |
| 1    | 1    | 1    | 0    |



The Carry Bit looks like we have A && B, and the Sum bit is simply A XOR B

Thus all we need to do is do A AND B for C and A XOR B for S 

*This is known as the half-adder*

### Dealing with Carry Bits

Now, we must consider a circuit that can add three bits: 

A, B, Cin -> S, Cout

We can do this as into a Full-Adder (two half-adders), then chaining multiple produces our full adder. 

**However, this has an extremely large propagation time for 32 or 64 bit adds**

## The Selecter

We want to design a circuit that can select between two inputs (i.e. select either A or B depending on a bit S) 

This is called the Multiplexer or Mux.

*  This is the basic gate of controlling data movement 

## The Decoder

The decoder takes in various inputs and decodes them with specific value outputs (i.e. think of a switch case statement)

- This is the basic gate of indexing