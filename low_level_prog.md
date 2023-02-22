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

### Von Neumann Arch Extensions

- **Registers**: 
  - Memory cells placed directly on the CPU chip.
  - Faster but more complicated and expensive.
  - Don't use the bus.
  - Registers are based on transistors while main memory uses condensers.
  - Could implement main memory as registers are but:
    - Registers are more expensive.
    - Instructions encode the register number as part of their code hence more registers equals bigger instruction sizes.
    - Registers add complexity to circuits to address them, more complex circuits are harder to speed up, not easy to set up a large register file to work on 5ghz
  - Locality of reference
    - Temporal locality - accesses to one address are likely to be close in time.
    - Spatial locality - accesses X, the next one wil likely be close to X
  - General purpose registers
    - They are interchangeable and can be used in many different commands.
    - Names can be r1,r2,r3 ..... r15, represent meaning they bear for some special instruction i.e rc1 - cycle, rcx - cycle counter, rax- accumulator
    - *registers do not have addresses as they are implemented differently to main memory*
    - Some registers have system wide importance hence only modified by the OS. i.e rip register - stores address of next instruction. rflags - current program state.
    - Registers for floating point numbers or specialised parallel instructions. i.e multimedia xmm0 - xmm15.
  - System registers
    - designed to be used specifically by the OS.
    - They don't hold values in computations instead store information required by system-wide data structures.
    - i.e cr2,cr3 - virtual memory, cr8 - interrupts.
    
- **Hardware Stack**:
  - Stack is a ds in general that supports pushing and popping.
  - Used in computations and store of local variables and implement function call sequence.
  - There is hardware support for such data structure.
  - Useful to implement function calls in higher-level languages, save the context of computations.
  
- **Interrupts**:
  - Allows one to change execution order based on events external to the program itself.
  - Execution suspended, Registers saved and CPU executes corresponding handling routine.
  - i.e signal from external device, zero division, invalid instruction, privilege instruction in non-privileged environment.
  

- **Protection rings**:
  - Mechanism designed to limit the applications capabilities for security and robustness reasons.
  - Invented for Multics OS predecessor to Unix.
  - Each ring corresponds to certain privilege level.
  - Each instruction type is linked with one or more privilege levels and is not executable on others.
  - A CPU is always in a state corresponding to one of the so-called protection rings.
  - Each defines a set of allowed instructions.
  - 

- **Virtual Memory**:
  - This is an abstraction over physical memory, which helps distribute it between programs in a safe and effective way.
  - Also isolates programs from one another.


### Unix

- Unix postulates that everything is a file in the sense that looks like a stream of bytes.
- Abstract things like:
  - data access on a hard drive/ssd.
  - data exchange between programs.
  - interaction with external devices.
- OS purpose is to abstract and manage resources via a set of routines to handle communication with devices, files, programs.
- A programs cannot bypass the OS and interact with resources directly.
- Communication happens via *system calls* provided to user applications.
- Unix identifies a file with its descriptor as soon as it is opened by a program...this is nothing but an integer value.
- File is opened by invoking the open system call however 3 other files opened stdin, stdout, stderror. 0, 1, 2.
- Write system call invoked for stdout - writes a given amount of bytes from memory starting at a given address to a file with a given descriptor
