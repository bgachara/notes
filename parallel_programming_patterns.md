# Parallel Programming

`Notes from Patterns for parallel programming`
`Also Structured Parallel Porgramming`


## Introduction

- A design pattern describes a good solution to a recurring problem in a particular context.
- Follows some format: pattern name, description of the context, forces(goals and constraints) and the solution.
- A parallel computer is a collection of processing elements that cooperate to solve problems quickly.
- One major motivation of using parallel processing: achieve a speedup
  - speedup(using P processors) = Execution time(using 1 processor)/ Execution time(using P processors)
- Small notes on parallelism
  - Communication limited the maximum speedup achieved.
  - Minimizing cost of communication improves speedup
  - Imbalance in work assingment limits speedup
  - Improving work distribution of work improved speedup.
  - Communication costs can dominate a parallel computation, severely limiting speedup.
  

## History of processor performance

- Wider data paths. i.e 4bit - 8bit - 16bit - 32bit
- More efficient pipelining. 3.5 cpi - 1.1 cpi
- Exploiting instruction-level parallelism. susperscalar
- Faster clock rates. 10mhz - 200mhz - 3Ghz

```
  
> Defn:
   **Power Density Wall**
    - Power wall - unacceptable growth in power usage with clock rate
    - Instruction-level parallelism wall - limits to available low-level parallelism.
    - Memory wall - growing disparity of processor speeds relative to memory speed. 
   **Non Uniform Memory Access***(NUMA)
   ** **

> The difference CPU and GPU is that CPU is designed to excel at executing a sequence of operations called a thread, as fast as possible and can execute a few tens of them
  in parallel, GPU is designed to excel at executing thousands of them in parallel(amortizing the slower single-thread performance to achieve greater throughput)
  
> GPU is specialised for highly parallel computations hence more transistors are devoted to data processing rather than data caching and flow control.
  also floating point computations
  GPU can hide memory access latencies with computations instead of relying on large data caches and complex flow control.
```

## Parallel Programming Models

- Desired Properties
  - Performance - achievable, scalable, predictable, tunable.
  - Productivity - expressive, composable, debuggable, maintainable
  - Portability - functionality and performance.

- Abstractions instead of mechanisms
  - avoid directly using hardware mechanisms, instead use abstractions that map onto these mechanisms.
  - portability is impaired severely when programming close to h/w.
  - nested parallelism is important and nearly impossible to manage well using mandatory parallelism implied by specific mechanisms.
  - other mechanisms for parallelism i.e implementations across machines may vary ..i.e threads on one, vectors on another.

- Expression of Regular Data Parallelism.
  - key to achieving scalability.

- Composability
  - is the ability to use a feature without regard to other features being used elsewhere in your program.
  - ideally every feature in a programming language is composable with every other.
  - principal issues: incompatibility and inability to support hierarchial composition

- Portability of functionality
  - being able to run code on a wide variety of platforms, regardless of operating systems and processors is desirable.

- Performance Portability
  - Performance maintained on machines to come and those not tested on yet.

- Safety, Determinism, Maintainability
  - Parallelism invariably introduces non-determinism.
  

### Overview of programming models.

- Cilk Plus
  - A parallel Cilk program is a serial program with keyword annotations indicating where parallelism is permitted and strong guarantee of serial equivalence.
  - Fork-join to support irregular parallel programming patterns and nesting
  - Parallel loops to support regular parallel programming patterns i.e map
  - Support for explicit vectorization via array sections i.e pragma simd and elemental functions.
  - Hyperobjects to support efficient reduction
  - Efficient load balancing via work-stealing.
  
- Thread Building Blocks(TBB)
  - Based on task programming
  - Library so can be used with any compiler supporting ISO C++.
  - Relies of templates and generic programming(algos are written with few assumptions about data structs hence huge reuse potential) 
  - Template library supporting both regular and irregular parallelism.
  - Direct support for a variety of parallel patterns: map, fork-join, task graphs, reduction, scan and pipelines.
  - Efficient work-stealing load balancing
  - A collection of thread-safe data structures.
  - Efficient low-level primitives for atomic operations and memory allocation.
  
- OpenMP
  - Based ona set of compiler directives or pragmas combined with an API for thread management.
  - Most common use of OpenMP is to parallelize loops within programs.
  - Creation of teams of threads that jointly execute a block of code.
  - Conversion of loops with bounded extents to parallel execution by a team of threads with a simple annotation syntax
  - A tasking model that supports execution by an explicit team of threads.
  - Support for atomic operations and locks
  - Support for reductionsm but only with a predefined set of operations.
  
- Array Building Blocks(ArBB)
  - Compiler independent.
  - Manages data as well as code.
  - Instead of tasks, parallel computations are expressed using set of operations that can act over collections of data.
  - Supports parallelism by specification of sequences of data-parallel operations.
  - High-level data parallel programming with both elemental functions and vector operations.
  - Efficient collective operations
  - Automatic fusion of multiple operations into intensive kernels
  - Dynamic code generation under programmer control.
  - Offload to attached co-processors without change to source code.
  - Deterministic by default, safe by design.

- OpenCL
  - Includes a kernel language for specifying kernels and an API for managing data transfer and execution of kernels from the host
  - Ability to offload computation and data to an attached co-processor with a separate memory space.
  - Invocation of a regular grid of parallel operations using a commin kernel function.
  - Support of a task queue for managing asynchronous kernel invocations

### Parallel Computations Themes

: parallel thinking

- Decomposing work into pieces that can safely be performed in parallel.
- Assigning work to processors.
- Managing communication/synchronization between processors so that it does not limit speedup
- Write code in parallel programming languages.

: how parallel computers work

- performance characteristicsof implementations
- design tradeoffs, performance vs cost vs convenience
- characteristics of hardware(communication speedup)
- know about h/w as you care about efficiency and performance.

: thinking of efficiency

- fast != efficient, i.e 2x speedup on 10 processor system.
- programmer pers: making use of machine capabilities.
- h/w pers: right capabilities to put into the system.


### Demo pattern language

- Finding concurrency 
  - Identify concurrency and expose it for use in the algorithm design.
- Algorithm structure
  - High-level structures for organising an algorithm to take advantage of parallelism.
  - Considerations on designing parallel algos
    - The total amount of computational work
    - The span(critical path)
    - The total amount of communication(implicit in sharing memory)
- Supporting structures  
  - How the parallel program will be organized and techniques used to manage shared data.
- Implementation mechanism
  - Software specific constructs for implementing parallel program.
