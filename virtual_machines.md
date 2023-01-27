# Notes on Virtual Machine.

`programming language virtual machines need a page of their own.`

## Motivation

- What is the use and need of virtual machine?
  - They help (computer) security, (application) productivity and (application) portability.
  - Necessary for safe languages: 
    - Memory safety - ensures that a certain type of data in memory follows restrictions on that type.i.e an array never out of bounds.
    - Operation safety - operations on the data follow said restrictions.
    - Control safety - flow of execution never gets stuck or run wild.
  - Provides management of code and data.
            
## Types of VMs

- Full Instruction Set Architecture(ISA) virtual machine.
  - provides a full computer system ISA emulation and virtualization.
  - OS and applications can run as on an actual machine.
  - VirtualBox and Qemu, Xen.

- Application Binary Interface(ABI) virtual machine.
  - provides a guest process ABI emulation.
  - apps against that ABI can run in the process side by side with other processes of native ABI apps
  - Apple Rosetta translation layer for PowerPC emulation, Transmeta Code morphing for x86 emulation.
  
- Virtual ISA virtual machine.
  - provides a runtime engine so that applications coded in the virtual ISA can execute on it.
  - defines a high level and limited scope of ISA semantics, so it does not need the vm to emulate a full computer system.
  - Sun Microsystem JVM, Microsoft CLR.

- Language virtual machine.   
  - provides a runtime engine that executes programs expressed in a guest language.
  - can be categorized by the implementation of its execution engine: component that expresses the application operational semantics.
  - Interpretation and Compilation.
  - presented to the vm in the guest language, engine then interprets/translates program and also fulfill certain functionalities abstracted like memory management.
  - Runtime engines for Python, Ruby, Tcl.
  - Javascript Engines: Chrome(V8), Mozilla(SpiderMonkey), Safari(JavascriptCore), Microsoft(Chakra).
  - Android Java Vm: Dalvik(interpreter and just-in-time compiler), Android RunTime(ART)(Ahead-of-Time).
  - Apache Harmony

## Core components

- Loader and Dynamic Linker
  - load application package into memor, parse package into data structures and load additional resources needed by the application.
  - data structures in memory have semantic meaning such as code and data.
  - reflection data or metadata is produced at load time that help vm understand application.
  - dynamic linker resolves all referenced symbols into accessible memory addresses, may trigger loader to load more data and code.

- Execution engine
  - performs the operations specified by the code and is the core component of the virtual machine.

- Memory manager
  - manage its data and the memory containing the data.
  - can be categorised by visibility to the application:
    - Virtual machine data: 
      - Vm needs memory to load application code and hold supporting data.
      - Data here is invisible to the application and necessary for the application execution.
    - Application data: 
      - Application needs storage for its static and dynamic data.
      - Visible to the application.
      - Dynamic data stored in the application heap.
  - Memory manager usually manages only the application data leaving the virtual machine data to internal or underlying system.

- Thread Scheduler
  - allows system to have multiple control flows.
  - straightforward way to provide multitasking, parallelization, and event co-ordination.
  - garbage collection helps execution engine use RAM resource whereas thread scheduler helps use the processor resource.

- Language Extension
  - Safe or High-level language depend on the vm to access low-level resources due to safety requirements.
  - Provided through:
    - Runtime services: 
      - profiling, debugging, exception/signal handling and interoperability.
      - can be provided via a client/server arch.
      - APIs, runtime objects, environment variables.
    - Language extension: 
      - runtime services may not be flexible enough and usually limited to specific features defined by the language spec and its execution model.
      - Foreign Function Interface can extend capabilities beyond current language spec and execution model.
      - Code can be inlined or embedded in the host language or invoked via a well wrapped function interface, object, module or class.
      - C the most popular foreign language due to its low-level nature.

## Virtual ISA

- Virtual language is used as the compilation target of other language.
- Mostly used for intermediate representation purpose, hence not exactly readable.
- i.e Java bytecode, LLVM bitcode, ParrotVM bytecode.
- Assembly language may also be counted here.
- Virtual ISA is a kind of virtual language that defines the instruction set and execution model of a virtual machine.

## Data Structures in VMs

- Core data structures: Object, Class and Virtual Function Table.
- It has two kinds of data types
  - Primitive types - hold a direct value, number, boolean, return address.
  - Reference types - hold a pointer to an object, which is a instance of reference type such as class or an array. 
- A class defines two parts of data:
  - Instance data - owned by every object individually, including object fields and virtual methods
  - Class data - shared by all instances of same class, static fields and static methods.
- Sample method description

```c

````

## Design of the Execution Engine

- Core component of the virtual machine and rest of components are supportive to the execution engine.
- Design of the execution engine largely dictates the design of a VM.
- Basic execution mechanisms are interpretation and compilation.

```bash

  Assuming a program P written in language L is compiled to machine code C,
  compile-time refers to the time when program P is compiled from L to C,
  and runtime refers to the time progam P is executed in the form of C

```

## Notes

- Many virtual machines designs crossing boundaries.
- i.e A language virtual machine can employ the technique of a virtual ISA virtual machine by compiling the program into a kind of virtual ISA and then executing the code on a virtual machine of that virtual ISA.
- They are called `Runtime Engines` because the services provided by the virtual machine are mostly available at runtime.
- 