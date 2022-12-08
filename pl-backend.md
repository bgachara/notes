# PROGRAMMING LANGUAGES BACKEND

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
/ Just-In-Time try to provide the best of both worlds....warm and hot code,

## Compilers

- This is a tool that translates software written in one language into another language all while understanding both the form or syntax and content or meaning of the input language.

- It needs to understand the rules that govern syntax and meaning in the output language... and finally a scheme for mapping content from the source language to the target language.

- The compiler must preserve the meaning of the program being compiled.
/ how is said "meaning" determined.

- The compiler must improve the input program is some discernable way. 

### Parts of a compiler

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

**RISC - Reduced Instruction Set Computing**
/ introduced a "load and store" architecture.
/ led to more instructions but were executed faster.
/ more instructions led to need for more memory, aided by reducing prices of memory.

**Note on GC**

-  fully concurrent garbage collection is the future of automatic memory management. I’m talking garbage collectors that run in other threads and clean up after me without ever stopping me in the middle of what I’m doing.


- Ahead of Time compilation(**AOT**) vs Just in Time compilation(**JIT**)

## AOT

- Most straightforward compilers are ahead-of-time


## JIT

- is a hybrid of compiled and interpreted languages, runs interpreted first then notices the "hot" code which it then compiles in order to optimize.
- Trace based vs Method based.
- Trace: analyses what paths(traces) are often used, and to which methods they belong, more efficient and more overhead.
- Method: only analyses the calls, less efficient and less overhead.
- Disadvantage: memory usage and warmup time.
- Memory usage: you have to have both the compiled and interpreted version stored in memory.
- Also has to keep information about the method i.e number of times called, arguments, times spent.
- Warmup time: has to recognize that a method is called a lot and then take time to compile it.
- JIT is good for programs that run for a long time, background and network servers.

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
- Range check elimination