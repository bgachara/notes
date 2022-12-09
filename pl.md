# Understanding Programming Languages

## Properties of PL.

1. Syntax.
2. Names.
3. Type System.
4. Semantics.
5. Memory model.
6. Evaluation Mechanism.
7. Abstraction Mechanism.


-Increased ability to express ideas
-Improved background for choosing appropriate languages
-Increased ability to learn new languages
-Better understanding of significance of implementation
-Better use of languages that are already known
-Overall advancement of computing

## Language Features.

1. Garbage Collection
2. First-class functions.
3. Static type-checking.
4. Parametric Polymorphism
5. Immutable programming
6. Automatic type inference.
7. Algebraic data types.
8. Pattern matching.
9. Language Syntax.
10. Performance
11. Explicit memory management

- Type Errors. / accessing memory not allocated to the program. /
- Types and Optimizations.
- Type safety and Type checking.
/ type casting, pointer arihtmetic, explicit deallocation and dangling pointers /
- Compile time checking, before the program starts.
- Run time checking, after the program has started.
- Type checker.
- Type Inference.
/ buffer overflows, out-of-bound array access, dangling pointers.

*What is the standard library of a language*
*How do you know when a programming language is taking advantage of modern HPC*
*What and How important are programming paradigms*
*Regular vs Context-free languages*
*Clear understanding of Static Analysis*
*Cost of runtime safety checks and garbage collection*


- Control Structures.
    - Direct sequencing.
    - Conditional branching.
- Iterations.
    - Bounded Iterations.
    - Conditional / Unbounded Iterations.
- Subroutines / Procedures.


## Tools

- Antlr - ANother Tool for Language Recognition.
    - Parser that automates the construction of language recognizers.


## Domain Specific Languages.

- Internal Dsl and External Dsl.
- *identify areas ready for dsl application i.e sql,html,audio,js,*

## PL Error Types/Faults

- Buffer Overflows.
- Out-of-bounds array access.
- Dangling pointers.

## Taxonomy of safety violations

- Memory Access Errors.
    - Spatial Access Errors.
    - Temporal Access Errors.
- Type cast Errors.
- Memory Leak Errors - fail to release all the memory they allocate.


```eng

**Defn:**
A toolchain is a set of tools that are used in a chain with the purpose of compiling and running code written in
a specific programming language.

The C toolchain is a toolchain because in addition to the compiler, you also need the standard library which contains the implementations of standard functions in binary form (CPU instructions), the assembler, because technically the compiler outputs assembly language, which is a human readable format for the CPU instructions which is converted to the actual binary CPU instructions by the assembler (also called the object file), and finally the linker which links the standard library, our object files and possibly other libraries together to form the final executable.

```

- Language design is difficult because of the extraordinary complex and unexpected interaction effects between all parts of the language, difficult to design and maintain a strict structuring discipline.

- Programming Languages should aid in program design, documentation and debugging.


```eng

**Orthogonality** - Small set of well thought features that can be combined in a logical way to supply more powerful building blocks.
 - Ideally those features should not intefere with one another i.e inconsistencies, exceptional cases and arbitrary restrictions. 

```

## Types of PL abstraction

- Data Abstraction
- Control Abstraction

## Levels of Abstraction

- Basic Abstraction - most localised machine information.
- Structured Abstraction - intermediate information about the structure of a program.
- Unit Abstraction - large scale information in the program.