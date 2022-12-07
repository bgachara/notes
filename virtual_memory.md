# Virtual Memory

`Notes on Virtual Memory`
`Any problem in computer science can be solved using indirection`

## Problems for VM

- Memory Space not enough.
- Memory Fragmentation.
- Program corruption.

- Virtual memory maps programs memory space to RAM memory space providing flexibility for the system.

- Complete isolation of programs prevents sharing of data between program i.e fonts, icons, specs.

## How VM works

- Virtual Addresses(what the program uses) vs Physical Addresses(what h/w uses to talk to the RAM)
- Translation happens between VA and PA.
- virtual memory: what the program sees.
- physical memory: what is in the computer.
- For data that can not fit in memory, it is mapped disk.
- Virtual Address size is set by the ISA

## Page Tables

- Keeping track of VA -> PA mappings.
- Page Table Entry(PTE) and Page Table Size.
- Fine-grain mapping(maps each word address) vs Coarse-grain mapping(maps chunks of address(pages))
- 4kb pages to 2mb pages. 

## Address Translation

- Virtual page number and page offsets
- Page offsets pass through.

## Page Faults

- Occurs when the data is not in memory and has to be loaded from disk.
- Page fault exception.
- PFE caught by OS and page chosen for eviction, if page is dirty it needs to be written back to disk first.
- After loading, Os jumps back to exception.

## Memory Protection

- Linux uses random offsets to separate programs in the address space, enhance security.

- To make VM fast we add a special Page Table cache, the Translation Lookaside Buffer(TLB)
- Separate TLBs, one for instructions(iTLB) and another for data(dTLB)

## Multi-level Page Tables

## TLB and Cache

- Physical Cache - slow vs Virtual Cache - fast
- Programs can't share a virtual cache.
- Virtually Indexed, Physically Tagged (VIPT) most common L1 cache.