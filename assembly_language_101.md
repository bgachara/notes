# Assembly Language 101

`Notes from Introduction to 64bit Intel Assembly Language Processing for Linux`
`Also Assembly 64`


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

## Advantages of learning Assembly Language

- Better understanding of Architecture Issues
      - basic instruction set.
      - processor registers
      - memory addressing
      - hardware interfacing
      - input / output

- Understanding Toolchain
      - process of taking code written and converting it to machine executable format.
      - includes the compiler, assembler, linker, loader and debugger.

- Improve algorithm development skills
      - practice with a more explicit language hence better expression of ideas needed

- Improve understanding of functions/ procedures
      - understand how they work.
      - contents and structure of the function call frame i.e activation record.
      - understand recursive functions

- Understanding of I/O buffering

- Understand compiler scope
      - understand scope and capabilities of the compiler

- Introduce multiprocess concepts
      - shared memory, threaded processing.

- Introduction of Interrupt processing concepts.
      - interrupt handling, interrupt service handling, vector interrupts.

- Understanding how a compiler implements features of a high level language can aid in efficiency features selection.

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

## Program Format

- A properly formatted assembly source file consists
      - Data section where initialized data is declared and defined.
      - BSS section where uninitialized data is declared.
      - Text section where code is placed.
- The semicolon(;) is used to note program comments.
- Numerical values can be specified in decimal, hex or octal i.e Ox7f, 777q.
- Constants are defined with equ <name> equ <value>
- Data section
      - initialized data declared here.
      - <variableName>  <dataType>  <intialValue>
      - declaration: dw, db, dd, dq, ddq, dt.
- BSS section
      - uninitilized data
      - <variableName> <resType> <count>
- Text section
      - code placed here.
      - specified one per line and must be valid with operands and all
      - include some headers or labels that define the initial program entry.
      - system service should be used to inform OS that the program should be terminated.

## Toolchain

- Set of programming tools used to create a program is referred to as the toolchain.
- Namely:
      - Assembler
            - program that will read an assembly language input file and convert code into machine language binary file(object file).
            - Comments are removed, varable names and labels are converted into addresses.
            - Capable of creating a list file, shows line number, relative address, machine language version of the instruction and original source line.
            - There is a one-to-one correspondence between the assembly instructions and binary machine language.
            - Steps taken on first pass
                  - Create symbol table.
                  - Expand macros.
                  - Evaluate constant expressions.
                  - Addresses are assigned to all statements in the program.
            - Steps taken on second pass
                  - Final generation of code.
                  - Creation of list file.
                  - Create object file.
            - Assembly directives are instructions to assembler so not translated into instructions for the CPU.
      - Linker
            - Combine one more object files into a single executable file.
            - Any routines from user of system libraries are included
            - -g *debugging*, -o <filename>*filename follow*
            - extern keyword used to declare functions not in curent file.
            - machine language code copied from each object file into a single executable.
            - Linker must adjust the relocatable addresses as necessary.
            - Dynamic linking - postpone the resolution of some symbols until a program is being executed. .so unix and .dll windows.
            - Assemble/Link script.
      - Loader
            - load the program from secondary storage into primary storage.
            - loader will read a properly formatted executable file, create a new process and load the code into memory and mark as ready for execution.
            - operating system scheduler will make a decision about which process is executed and when the process is executed.
            - invoked by typing the program name.
      - Debugger
            - used to control the execution of the program, examine variables, other memory(stack) and display porgram output if any.
            - Allows for testing and debugging activities of the program.
            - Data Display Debugger(DDD) is the frontend to the GNU Debugger(GDB)
            - Breakpoint - Execution pause location, to pause the program at a user selected location.
            - Code will run upto and not including the breakpoint.
            - Next(full next instruction) vs Step(just one step i.e step into function) command.

## Instruction set 

- Instructions include but are not limited to:
      - Data movement
            - mov <dest> <src>
            - must be of the same size.
            - dest not be an immediate and both cannot be memory.
            - access memory via brackets[], omitting the brackets will instead obtain address of the item.
            - lea also load address.
      - Conversion Instructions
            - convert from one size to another size. i.e byte to double-word.
            - narrowing conversion - from a larger type to a smaller type. no special instruction needed, lower portion of the memory location or register accessed directly.
            - widening conversion - from a smaller type to a larger type. upper order must be set based on sign of the original value.
      - Arithmetic Instructions
            - addition, subtraction, multiplication, division on integer values.
            - add <dest> <src>
            - adc <dest> <src> = <dest> = <dest> + <src> + <carryBit>
            - sub <dest> - <src>
            - different instructions for unsigned instructions(mul) and signed multiplication(imul)
            - mul <src> - must be a register or a memory location.
            - multiply produces double sized results.
            - imul <src>, imul <dest>,<src/imm> imul <dest> <src> <imm>
            - <div> <idiv>
            - division by zero will crash a program                                                
      - Logical Instructions
            - and, or, xor
            - shift ops - isolate subset of bits, mult or div by powers of 2. logical shift, arithmetic shift,
            - rotate operations
      - Control Instructions
            - structures such as IF statements and looping.
            - control instructions refer to conditional jumping cmp <op1> <op2> AND unconditional jumping. jmp <label>
            - program label is target or a location to jump to. starts like varibale and terminated by :
            - iteration: basic loop can be implemented by a counter cehcked at the bottom or top of a loop with a compare or conditional loop.
            - loop instruction available: loop <label>


## Addressing modes

- The addressing modes are the supported methods for accessing a value in memory using the address of a data item being accessed.
- This might include the name of a variable or the location in an array.
- Basic addressing modes are:
      - Register
            - operand is CPU register eax, ebx.
      - Immediate
            - operand is an immediate value
      - Memory
            - operand is a location in memory(accessed via an address), indirection or deferencing.
- On a 64bit arch , addresses require 64-bits. Use [] to access memory.


## Process Stack

- A stack is a type of data structure where items are added and then removed from the stack in reverse order. LIFO
- Heavily used for storage of information during procedure or function calls.
- Push and Pop operations. push <operand64> pop <operand64> operand can be register or memory not an immediate.
- Stack is implemented in reverse in memory.
- Stack starts in high memory and grows downward.
- Temporary store information such as call frames for function calls.
- Heap and stack grow towards each other, program carsh if they ever meet.
- Basic ops of push and pop adjust stack register pointer *rsp* during their operation.


## Program development

- Main steps:
      - Understand the problem.
            - understand what is required esp input information and expected results or output.
      - Create the algorithm
            - Steps to solve problem once understood.
      - Implement the program.
      - Test/Debug the program.

- Error Terminology
      - Assembler error
      - Run-time error
      - Logic error


## Macros

- Set of instructions that can be inserted wherever needed.
- Useful when the same set of code must be utilised numerous times.
- Should be placed on the source file before the data and code sections.
- Can either be single-line macros or multi-line macros.


## Functions

- Functions and procedures help break-up a program into smaller parts making it easier to code,debug and maintain.
- They involve two main actions:
      - Linkage - since can be called from multiple places, must be able to return to the correct place it was originally called.
      - Argument Transmission - must be able to access parameters to operate on or to return results.
- Non-static local variables declared in a function are stack dynamic local variables by default.
- Statically declared variables are assigned memory locations for the entire execution of the program...no runtime overhead though.
- Function declaration:
      global <procName>
      <procName>:
            ;function body
      ret
- Standard calling convention
- Linkage:
      - about getting to and from a function call correctly.
      - Two instructions: call <funcName> and ret
      - call saves address of where to return to when function completes, placing rip onto the stack.
      - ret pops the current top of the stack(rsp) into the rip. thus address restored.
- Argument transmission:
      - sending info to a function and obtaining a result as appropriate for the specific function.
      - transmission by value = call-by-value.
      - transmission by address = call-by-reference.
      - can either be via:
            - placing values in register.
            - globally defined variables.
            - putting values and/or addresses on stack.
- Calling convention
      - general idea is program state(specific registers, stack) is saved, function executed then state is restored.
      - prologue: helps save the state.
      - epilogue: restores the state
- Parameter passing
      - combo of registers and the stack is used to pass params to and from a function.
      - first six in register and other via the stack.
      - for fp args, xmm0-xmm7.
- Register usage
      - some regs are exected to be preserved across a function call.
- Call frame
      - Items on the stack as part of a function call are referred to as call frame..activation record or call stack.
      - Include; 
            - Return address
            - Preserved registers
            - Passed arguments.
            - Stack dynamic local variable
- Caller
- Callee

## System services

- Console output, keyboard input, file services, time/date, requesting memory...services apps need from operating services.
- System interface is the interface between an executing process and the operating system.
- Calling system services;
      - logically similar to calling a function, where function code is located within the operating system.
      - when calling system services , arguments are placed in standard argument registers.
      - system services dont typically use stack-based arguments.
      - system service code is placed in rax register, number assigned to specific sysserv being requested.
      - codes can not be changed by application programs.
      - After the system code and arguments are set, the syscall instruction is executed.
      - The syscall instruction will pause the current process and transfer control to the operating system which will perfrom instruction in rax register.
- i.e Newline character, console output, console input, file open ops.

/*more to be updates here*/


## Multiple source files

- 

## Stack Buffer Overflow

- A stack buffer overflow can occur when a program overflows a stack-based dynamic variable.(see stack-based local variables)
- Cause program bugs or even crash the program.
- Overwritten will be variables, preserved registers, frame pointer, return address, stack-based parameters.
- Stack Smashing  