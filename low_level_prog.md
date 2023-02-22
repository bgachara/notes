# Low Level Programming

`Notes from the following books `
` - Low level programming.`
` - Secret life of programs`
` - Write Great Code: Understanding the machine`


## Introduction

- Model of computation is a set of basic operations and their respective costs.
- Most models of computations are also abstract machines. i.e church Lambda calculus, turing machines, von neumann
- Computer Architecture describes the functionality, organization and implementation of computer systems.
- Von Neumann was robust and easy to program hence its popularity.
- Desc:
  - This consists of one processor, one memory bank connected to a common bus
  - CPU execute instructions fetched from memory by a control unit.
  - ALU perfroms the needed calcs.
  - Memory stores only bits and stores both encoded instructions and data to operate on.
  - One byte = 8 bits
- Assembly Language for a chosen processor is a pl consisting of mnemonics for each possible binary encoded instruction(machine code).
- An architecture does not always define a precise instruction set unlike a model of computation.

### Von Neumann Arch Extansions

- Registers: 
  - Memory cells placed directly on the CPU chip.
  - Faster but more complicated and expensive.
  - Don't use the bus.
- Hardware Stack:
  - Stack is a ds in general that supports pushing and popping.
  - Used in computations and store of local variables and implement function call sequence.
  - 
- Interrupts:
  - Allows one to change execution order based on events external to the program itself.
  - Execution suspended, Registers saved and CPU executes corresponding handling routine.
  - i.e signal from external device, zero division, invalid instruction, privilege instruction in non-privileged environment.
- Protection rings:
