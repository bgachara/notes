# Binary Analysis

`Notes from the Practical Binary Analysis book`

## Introduction

- Binary Analysis is the science and art of analyzing the properties of binary computer programs called binaries and the machine code and data they contain.
- The goal of all binary analysis is to figure out and possibly modify the true properties of binary programs
- Techniques: 
  - Static Analysis
  - Dynamic Analysis
- Binary Instrumentation.
- Challenges:
  - No symbolic information
  - No type information
  - No high-level abstractions
  - Mixed code and data.
  - Location-dependent code and data.

## Anatomy of a binary

- Most computers perform their computations using the binary numerical system, which expresses all numbers as strings of ones and zeros. Binary code.
- All code and data belonging to a single program is stored in a single self-contained file, binary executable files.
- Compilation process phases:
  - Preprocessing
  - Compilation
  - Assembly - *relocatable tag. object file not executable.
  - Linking
  
> Relocatable files don't rely on being placed at any particular address in memory rather moved around at will without breaking assumptions in code.

> Object files are compiled independently from each other, so assembler has no way of knowing memory address of other object files when assembling an object file...
  hence need for them to be relocatable, link them together in any order to form a complete binary executable.

- Symbols and Stripped binaries

```eng

Write a C program that contains several functions and compile it into
an assembly file, an object file, and an executable binary, respectively.
Try to locate the functions you wrote in the assembly file and in the
disassembled object file and executable. Can you see the
correspondence between the C code and the assembly code? Finally,
strip the executable and try to identify the functions again.

```

## ELF Format

- Executable and Linkable Format, default for Linux systems
- Used for executable files, object files, shared libraries, core dumps.
- Components:
  - Executable header
    - Structured series of bytes telling you its an elf.
    - What kind of elf it is.
    - Where in file to find all other contents.
    - Use type definition to find format of the executable header.
    - The e_ident Array - 16-bit identifier.
    - e_type, e_machine, e_version
    - e_entry - entry point.
    - e_phoff and e_shoff - program and section headers.
    - e_flags, e_ehsize
  - Optional program headers
    - Segment view of the binary,used by the operating system and dynamic linker when loading an ELF into a process to locate relevant code and data and decide what to load in virtual memory
    - 
  - Sections
    - .init and .fini, constructor and destructor code.
    - .text, main code resides
    - .bss, .data and .rodata, uninitialized variables(block started by symbol), initialized variables, 
    - lazy binding and the .plt, .got, .got.plt(procedure linkable table, global offset table)
  - Optional section headers
    - code and data in an ELF binary are logically divided into contiguous nonoverlapping chunks called sections.
    - each section described by a section header, which denotes properties of the section and allows you to locate bytes belonging to the section.
    - To load and execute a binary in a process, need for different organization of the code and data in binary hence code segments, used at execution time
    - Sections exist at link time.

## PE Format

- Portable Executable format, Windows specific.
- Modified version of the Common Object File Format(COFF) also used on Unix before ELF.
- MS-DOS Header and MS-DOS Stub.
- Header is used to describe how to load and execute an MS-DOS stub which comes after header
- PE signature, File Header and Optional Header.

## Binary Loader

- Learn format, build loader
- The Binary File Descriptor library(libbfd) provides a common interface for reading and parsing all popular binary formats, compiled for a wide variety of architectures.

### Binary-loading Interface

: implement a binary loader in Rust.
- 