# Assembly Language 101

`Human readable machine code`
`Entry into the world of Assembly Language`
`Its essential to reverse engineering both malicious and bening programs`
`Write memory corruption exploits`
`New h/w security often comes in the form of assembly instructions that need to be adopted`

## Introduction

- Called x86 because of the progression of Intel chips, 8086, 80186, 80286.
- Originally 16-bit architecture, later evolved to 32 and 64 bit but kept backwards compatibility.
- H/w starts at 16 bit and transitions it to 32 and 64 bit.
- I64 not same as x86.
- x86: more powerful and power hungry systems: PCs, servers, super-computers.
- Not very portable as closely tied to the architecture.
- More code to write as it lacks abstractions provided by high level languages.
- Has 8 generic registers: eax, ebx, ecx, edx, esi, edi, ebp, esp.
- E at the start stands for extended as extended from 16-bit archs.
- eflags register contain system and status flags, used to manage various processor modes and states.
- Instruction format: 
  - contain opcode and one or two operands.
  - operands represent data that is handled by the specific instruction.
  - can either be:
    - register name - to read from or written from. 
    - immediate - constant value embedded in the code.
    - memory address - hard-coded or offset calculated.

```asm

  eax
  
  0x3000400
  
  [0x4000349e]
  
  
  
```

> There exists some difference between the Intel notation and the AT&T notation


## Basic Instructions

- MOV - takes two operands: destination and source and moves data from source to destination.
      - destination can either be a memory address or a register.
      - source can be an immediate, memory address, register
      - only one can be a memory address, never both.

- Arithmetic:
      - ADD
      - SUB
      - MUL
      - DIV
      - IMUL
      - IDIV

- Comparing Operands
      - CMP

- Conditional branches
      - JCC 
      - Instructions that conditionally branch to a specified address based on some conditions.
      - Jcc is a generic name and has various variants that all test different set of flag various.

- Function calls.
      - CALL - calls a function - pushed current instruction pointer onto the stack and jumps to the specified address.
      - RET - returns to the caller - pops instruction pushed onto stack and resumes execution from that address also can increment ESP by specific no of bytes after popping.
      - function address can be specified just like any other operand, as an immediate, register or memory address.
