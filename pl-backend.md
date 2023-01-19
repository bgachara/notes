# PROGRAMMING LANGUAGES BACKEND

`everything behind the programming language syntax and semantics`

- A language feature is defined by its statics, the rules governing the use of the feature in a program, and its dynamics, the
  rules defining how programs using this feature are to be executed. The concept of safety
  emerges as the coherence of the statics and the dynamics of a language.

- Formal language for expressing computation and designed to be expressive, concise and clear.

```eng

I emphasize to students that my goal is for them to learn how to learn a programming language, rather than to
retain detailed specifics of any one programming language.

There is a tendency to prefer constructions that are simplest in that language rather than those that are best for the mac

```

1. Syntax — the form of programming languages.
2. Semantics — the meaning of programming languages.
3. Pragmatics — the implementation of programming languages/ how semantics are implemented/computed.

**Static Single Assignment**
**Instruction Set Architecture**
**Interpreters vs Compilers**
/ both build an *internal model of the structure and meaning of the program*.
/ major con of interpreter is when you have to do the same thing more than once, ran translator more than once.
/ Just-In-Time try to provide the best of both worlds....warm and hot code.
/ The difference in execution between interpreted and compiled native code is called the `interpretive overhead`.
/ :caused by parsing, semantic analysis or for byte-code interpreters instruction decoding and instruction dispatch.

## Compilers

- This is a tool that translates software written in one language into another language all while understanding both the form or syntax and content or meaning of the input language.

- It needs to understand the rules that govern syntax and meaning in the output language... and finally a scheme for mapping content from the source language to the target language.

- The compiler must preserve the meaning of the program being compiled.
/ how is said "meaning" determined.

- The compiler must improve the input program is some discernable way. 

### Parts of a compiler

- Scanning/Lexing/Lexical Analysis.
  - takes in the linear stream of characters and chunks them together into a series of sth akin to "words" also called tokens.
  - tokens can be single characters or several characters long.
  - whitespace and comments discarded.

- Parsing.
  - this is where our syntax gets a grammar, ability to compose larger expressions and statements out of smaller parts.
  - takes the flat sequence of tokens and builds a tree structure that mirrors the nested nature of the grammar.
  - this is the parse tree or the abstract syntax tree.
  - parser's job is to also provide syntax errors.

- Static Analysis.
  - First 2 stages are pretty much the same across all implementations.
  - Individual characteristics of a language start coming into play.
  - Only syntactic structure of the code is known at this stage.
  - First bit of analysis is `binding` or `resolution`, for-each identifier we find out where that name is defined and we wire them together.
  - This is where `scope` comes into play.
  - If the language is statistically typed, this is when we type check.
  - Once we know where they are declared, we can also figure their types and report a `type error` when declared operations aren't allowed between types.
  - All semantic insight that is visible to us (gleaned) from analysis needs to be stored somewhere.
  - They are often stored right back as attributes on the syntax tree itself, in fields left uninitialized during parsing.
  - Can also be stored in a look-up table off to the side, keys to this table being identifiers(name of vars and declarations): `symbol table`.
  - Most powerful bookkeeping tool is to transform the tool into an entirely new data structure that more expresses semantics of the code.

- Everything upto here is considered the **Front-end**.

- Intermediate representation(IR)
  - Compiler can be thought of as a pipeline where the preceding stage organises code in a simpler way for the next stage.
  - Front end is source language oriented while backend is destination architecture oriented.
  - In the middle, the code may be stored in some `intermediate representation` that isn't tied to either source or destination forms. IR acts an interface between the two languages.
  - Lets you support multiple source languages and target platforms with less effort.

- **Front-end** -> deals with the source language.
/ to check syntax the compiler must compare the program's structure against a definition of the language.
/ requires a formal definition, efficient mechanism for testing whether definition is met and how to procede on illegal input.
/ Parsing - Language level recognition, grammatical structure in stream of tokens.
/ Lexer, Scanner - Vocabulary level recognition, structure in a stream of characters.

- **Back-end** -> deals with the target language.
/ selects target-machine operations to implement each IR operation.
/ chooses order in which operations will execute efficiently.
/ decided which values will reside in memory and which ones will be in registers and asserts those choices.

- Middle -> Formal structure for representing the program in an IR whose meaning is language independent.

## CS ideas in Compilers

- Greedy algorithms(register allocation)
- Heuristic search techniques(list scheduling)
- Graph algorithms(dead-code algorithms)
- Dynamic programming(instruction selection)
- Finite automata and push-down automata(scanning and parsing)
- Fixed-point algorithms(Data flow analysis)
- Dynamic allocation, Synchronization, Naming, Locality, Memory hierarchy management and Pipeline scheduling.

## Compiler Types

- Single-pass compilers.
- Multi-pass compilers.
- Optimising compilers.
- Load-and-go compilers.
- Interactive compilers.


**RISC - Reduced Instruction Set Computing**
/ introduced a "load and store" architecture.
/ led to more instructions but were executed faster.
/ more instructions led to need for more memory, aided by reducing prices of memory.

## Garbage Collection

- Fully concurrent garbage collection is the future of automatic memory management. 
- I'm talking garbage collectors that run in other threads and clean up after me without ever stopping me in the middle of what I'm doing.


## Ahead of Time compilation(**AOT**) vs Just in Time compilation(**JIT**)

## AOT

- Most straightforward compilers are ahead-of-time.
- Also known as Static Compilation.


## JIT

- also called dynamic translation.
- is a hybrid of compiled and interpreted languages, runs interpreted first then notices the "hot" code which it then compiles in order to optimize.
- Trace based vs Method based.
- Trace: analyses what paths(traces) are often used, and to which methods they belong, more efficient and more overhead.
- Method / Tiering: only analyses the calls, less efficient and less overhead.
- Disadvantage: memory usage and warmup time.
- Memory usage: you have to have both the compiled and interpreted version stored in memory.
- Also has to keep information about the method i.e number of times called, arguments, times spent.
- Warmup time: has to recognize that a method is called a lot and then take time to compile it.
- Assumed goal of avoiding premature optimization.
- Loops and Functions are typical candidates for optimizations.
- JIT is good for programs that run for a long time, background and network servers.
- e.g Ionmonkey(Mozilla)

## Profiling

- Necessary for efficient optimizations.
- Sampling based(statistical approach,less precise and very efficient) or Event based(only triggered by certain events)
- Call graph
- Event tracing
- Static and Dynamic 
- Static Optimizations
  - Dead code elimination
  - Constant folding and propagation
  - Loop-Invariant code motion.
- Hotspot detection
- Method Inlining
- Range check elimination.

## Translator classification and structure

- Translators main function is to change the representation of an algorithm from one form to another and not execute it.
- T-diagrams are used to describe translators.
- T-diagrams were first introduced by Bratman(1961) and further refined by Earley and Sturgis.

### Classes of translators

- Assembler: map low-level language instructions into machine code which can then be executed directly.
- Macro-assembler: variation of the above but map into a sequence of machine-level instructions, extend the assembly language.
- Compiler: translators that map high-level language instructions into machine code.
- Pre-processor: translators that perform simple text substitutions before translations take place.
- High-level translator: map one high-level language into another.
- Decompiler/disassembler: refers to translators that take object code and try to regenerate source code at a higher level.

- Self-resident translators(generate code for their host machines) vs Cross translators (generate code for other machines in addition to host)
- *Load-and-go* translators.
- *Sequential conjuction* or *Short-circuit* approach to handling compound Boolean expressions.

## Code Generator

- decide memory locations for data, generating code to access such locations, selecting registers for intermediate calculations and indexing etc.

## Errors

- Error handler, error reporter, error recovery and error correction.
- Error recovery is when the translation process attempts to carry on after detecting an error and error correction/repair when it attempts to correct the error from context.
- Error detection at compile-time in the source code must not be confused with error detection at run-time when executing the object code.
- Many code generators are responsible for adding error-checking code to the program, can be rudimentary or heavy.
- Such code can drastically reduce the efficiency of a program.
- Mistaked detected at compile-time are known as *errors* while those that show up at run-time are *exceptions*

## Cross-compilation vs Bootstrapping

- The process of modifying an existing compiler to work on a new machine is called porting the compiler.
- Self-compiling compilers

## Simple machine architecture

- Many CPU chips used in modern computers have one or more internal *registers* or *accumulators*, which may be regarded as highly local memory where simple arithmetic and logical ops may be performed and between which local data transfers may take place.
- Fundamental register is the *instruction register* through which moves the bytes representing the fundamental machine-level instructions that processor can obey.
- Instructions can be extremely simple i.e "clear a register" or "move a byte from one register to another".
- Program counter or Instruction pointer is used to keep track of the address in memory of the next instruction to be fed to the processor's instruction register.
- 

### Address modes

- **Three-address code** - an *opcode*(operation code) is followed by two operands and a destination.
- operation   destination, operand, operand
- **Two-address code** - an operand is followed by one operand and one destination.
- operation   destination, operand
- **One-address code** - common in accumulator machines.

## Formal Languages

- Metalanguage - a language used to describe another language. i.e English is a kinda metalanguage.
- Syntax rules: describe the form.
- Semantic rules: describe the meaning.
- Backus-Naur-Form

## Grammars

- Set of rules for describing sentences.
- Grammar = { N, T, S, P }
  - *N* - a finite set of non-terminal symbols.
  - *T* - a finite set of terminal symbols.
  - *S* - a special goal or start or distinguished symbol.
  - *P* - a finite set of production rules or simply, productions.

## Static Analysis

- This is a way to check your code without running it.
- How to go about it.
  - Deciding what you want to check for.
  - Deciding how exactly to check for it.
  - Implementation details

### Misc Notes on Rust

- Use-after-free errors.
- Data races - unsynchronized access to shared memory(where at least one is a write).
- Ownership type system == affine or substructural
- **...placing restrictions on which aliases(references) to an object may be used to mutate it at any given point in the program's execution**
- rich set of APIs: concurrency abstractions, efficient data structures, memory management.
- `Iteration invalidation`(usually internally implemented as a pointer) - ds being iterated over is mutated during iteration.
- GC languages: memory only deallocated when it can no longer be used by the program. Avoid interior pointers (pointers into ds)
- To make them addressable, they need to be separate objects, references to which can now be strored in the array.
- Parameters are passed by value but copy are shallow.
- Memory management is automatic with the rules hence no need for a garbage collector.
- Mutable references: provide temporary ownership, borrowing
- Pass by value: Ownership transfer, Passed by Ref: Borrowing for certain lifetime.
- Shared reference = share read only data between multiple threads, allowed to co exist in same lifetime as long as both are read
- Double linked list impossible to impelement.

## Virtual Machines