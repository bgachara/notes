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

## Notes

- Many virtual machines designs crossing boundaries.
- i.e A language virtual machine can employ the technique of a virtual ISA virtual machine by compiling the program into a kind of virtual ISA and then executing the code on a virtual machine of that virtual ISA.
- They are called `Runtime Engines` because the services provided by the virtual machine are mostly available at runtime.
- 