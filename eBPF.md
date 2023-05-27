# Understanding eBPF

ref: `Learning eBPF`

## What and Why

- This a kernel technology that allows developers to write custom code that can be loaded into the kernel dynamically, changing the way the kernel behaves.
- This enables a new generation of highly performant networking, observability and security tools.
  - Performance tracing of pretty much any aspect of the system.
  - High-performance networking, with built0in visibility
  - Detecting and optionally preventing malicious activity.
- came to stand for Berkeley Packet Filter, introduced in linux in 1997, where it was used in the `tcpdump` utility as an efficient way to capture packets to be traced out.
- ref paper: `The BSD packet filter:New Architecture for User-level packet capture`
- later evolved to extended BPF, eBPF
- kprobes
  - allows traps to be set on almost any instruction in the kernel code.
  - developers could write kernel modules that attached functions to kprobes for debugging or performance measurement purposes.
- Ability to attach eBPF programs to kprobes added and revolutionized tracing on Linux.
- LSM(Linux Security Module) BPF
- Use `strace` to see how system calls an application makes.
- Kernel Modules
  - loaded and unloaded on demand
  - easy way to change how a kernel behaves.
  - can be distributed for use independent of official Linux release.
- eBPF comes with eBPF verifier which ensures programs are loaded only when it is safe to run.
- eBPF programs can be dynamically loaded and unloaded form the kernel, once attached to ana event they will be triggered by that event regardless of what caused the event.
- This leads to one of the great strengths of observability or security tooling that uses eBPF - it instantly gets visibility over everything happening on the machine.
- In environments running containers, this included visibility over all processes running inside those containers as well as on the host machine.
- eBPF programs are a very efficient way to add instrumentation.
- Once loaded and JIT-compiled the program runs as native machine instructions on the CPU.
- Additionally, there is no need to incur the cost of transitioning between kernel and user space to handle every event.
- For performance tracing and security observability, relevant events can be filtered within the kernel before incurring the costs of sending them to user space.
 