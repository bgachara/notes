# Operating Systems

ref:
`Notes from Modern operating systems by Tanenbaum`
`Windows Fundamentals Notes from Reversing - Secrets of Reverse Engineering`
`CS162 Youtube - John Kubiatowicz`
`Operating Systems: Three Easy Pieces`
`linux system programming`

*the concepts and design patterns appear at many levels*
*the better you understand their design and implementation, the better use you'll make of them*

## Introduction

- Primary job is provide user programs with a better, simpler, cleaner model of the computer and handle all the resources.
- Resources include: processors, main memory, disks, printers, display, network interfaces and other i/o devices.
- Most computers have two modes of operation: Kernel mode and User mode.
- OS runs in kernel mode(supervisor mode): has access to all h/w and can execute any instruction.
- Abstraction is the key to managing complexity.
- Good abstraction turn nearly impossible tasks into two manageable ones
    - First one is defining and implementing the abstractions.
    - Second one is using abstractions to solve the problem at hand.
- One abstraction every computer user understands is the file.
- The job of the OS is to create good abstractions and then implement and manage the abstract objects thus created.
- The trick in resolving tension between simple and complex interfaces is rely on mechanisms that can be combined to provide generality.
- Abstraction provider definition the OS can be thought of as the top-down view and alternative bottom-up view would be of resource manager.
- Resource management includes multiplexing(sharing) resources in two different ways: in time and in space.
- Spooling(Simultaneous Peripheral Operation On Line)
- Multics(Multiplexed Information and Computing Service)
- Unix
- Posix - standard of Unix that defines a minimum system call interface that conformant Unix systems must support.


## Computer Hardware

- Processor 
  - fetch, decode, execute cycle.
  - introduction of pipelines for more work.
  - superscalar CPU.
  - Different register types.
  - Moore's Law - transistor doubling every 18 months.
  - Multithreading/ Hyperthreading - each thread appears to the CPU as a separate CPU.
  - Multicore chips.

- Memory 
  - should ideally be faster than the CPU so no memory access times.
  - constructed as a hierarchy of layers.
  - register -> cache -> main memory -> magnetic disk
  - questions to every cache situation:
    - when to put new item in cache.
    - which cache line to put new item in.
    - which item to remove from cache
    - where to put newly evicted item in main memory

- I/O Devices 
  - consist of the controller and the device itself.
  - controller is a chip or set of chips that physically controls the device...accepts commands from the OS.
  - Device driver is s/w that talks to a controller, giving commands and accepting responses.
  - To be used, device driver has to be put into OS so as to run in kernel mode.
  - Can run in user mode but rarely is this as its a feature where access in a controlled way is required.
  - device registers form the I/O port space.
  - I/O implemented in three ways
      - Busy waiting.
      - Interrupts.
      - Direct Memory Access.

- Buses 
  - As processors and memories got faster, ability of one bus became strained and thus more were added.
  - i.e : cache, local, memory, PCI, USB, IDE, SCSI and ISA.
  - Bask Input Output System (BIOS)
  - contains low-level i/o software i.e read keyboard, write to screen, disk i/o.
        

### Booting up computer

- BIOS started.
- Checks to see how much RAM is installed and whether basic devices are installed and responding correctly.
- Starts by scanning ISA and PCI buses to detect devices attached to them.
- If new devices from last boot, new devices are configured.
- Determines boot device by checking list of devices stored on CMOS.
- User can change this list by entering new device into list.
- First secto from boot device is read into memory and executed.
- Examines partition table at the end of boot sector to determine which partition is active.
- Secondary boot loader is read in from that partition.
- Reads in the OS from active partition and start it.
- OS queries the BIOS for configuration info.
- For each device it checks to see if it has the device driver.
- Loads them into kernel once it has all of them.
- Initializes the tables, creates necessary background processes and starts login program.


## Operating system ZOO

- Mainframe OS
- Server OS
- Multiprocessor OS.
- Personal computer OS.
- Handheld compuer OS
- Embedded OS. .QNX,VxWorks
- Sensor Node OS. .TinyOS
- Real-time OS. .e-Cos
- Smart card OS.


- System software lives at the lowe level interfacing directly with the kernel and system libraries.
- System programmer must have acute awareness of the hardware and operating system in which they work.
- High-level libraries abstract away the details of the hardware and operating systems

## Operating system concepts

### Process

- a program in execution, object code in execution
- associated with each process is its address space, a list of memory locations  from 0 to some maximum which can read and write to.
- Address space contains executable program, program's data, and its stack.
- also with each process is some resources, registers(program counter and stack pointer), open files, outstanding alarms, related processes and any other info needed to run program
- Encapsulate one or more threads sharing process resources.
- Fundamental tradeoff between protection and efficiency.
  - communication easier within a process
  - communication harder between processes.
- A process is thus a container with all information needed to run a program.
- Process starts life as an executable object code, which is a machine-runnable code in executable format that the kernel understands(ELF)
  - most important sections are the text section, bss section and data section.
- In many OS, all info on any process, other than content of its own address space is stored in a process table, which is an array of structures, one for each process in existence.
- A suspended process consists of its address space and its process table entry.
- Each person on a system has a UID which is attached to every process they start.
- They request and manipulate resources only via system calls.
- A process resources, along with the data and statistics related to it are stored in the kernel inside the process pd.
- Kernel seamlessly and transparently preempts and reschedules processes, sharing system processors among all processes.

- Threads
  - unit of activity within a process
  - abstraction responsible for executing code and maintaining the process running state.
  - a process can be single threaded or multi-threaded.
  - consists of a stack(stores local variables), processor state and a current location in the object code(stored in processor instruction pointer)
  - majority of other parts of a process are shared among threads, most notable process address space.
  - threads share virtual memory abstraction hwile maintaining the virtualized processor abstraction.
  - TCB(thread control block)
    - Holds contents of registers when thread not running.
    - stored in the kernel.
  - Threads encapsulate concurrency: active component
  - Address psaces encapuslate protection: passive component. keeps buggy programs from crashinf system.
  - Why have multiple threads per address space
    - parallelism: take advantage of actual hardware parallelism
    - concurrency: ease of handling I/O and other simultaneous events.
    
- Process hierarchy
  - each process has a unique PID, starting from 1 and all others following from that.
  - in linux they form a strict hierarchy, known as a process tree, rooted at the init process.
  - new processes are created via the fork() system call, which creates a duplicate of the calling process.
  - when a process is terminated it is not immediately removed from the system, kernel keeps parts of the process resident in memory to allow process parent to inquire about its status upon terminating, waiting on the process.
  - non-waited on processes are called zombie processes.

### Address spaces

- The set of accessible addresses + state asscoiated with them.
    - For a 32-bit processor: 2^32 = 4 billion(10^9) addresses.
    - For a 64-bit processor: 2^64 = 18 quadrillion(10^18) addresses.
- What happens when you read or write to an address??
- Simple multiplexing has no protection, so OS must protect itself from user programs.
  - Reliability
  - Security
  - Privacy
  - Fairness
- Simple protection
  - Base and bound: user registers to constrict operating area of a program, isolating program.
  - Simple address transaltion with base and bound, use of a base address for on-the-fly translation.
  - Segments and stacks.
  - Address space translation
    - program operates in an address space that is distinct from the physical memory space of the machine.
  - Paged virtual address.
    - instructions operate on virtual addresses
    - translated to a ohysical address via a page table by the hardware.
    - any page of address space can be in any page sized fram in memory.
- Holds executing program.
- On many computers addresses are 32 or 64 bits, giving address space of 2^64 bytes.
- Virtual memory augments processes where their address space is larger than what is available.

### Files

- most basic and fundamental abstraction in Linux.
- System calls needed to create files, remove files, read files and write files.
- Directory used to group files together.
- Before file manipulation, it must be opened, at which time permissions are checked, if permitted, file descriptor returned else error code.
- An ope file is referenced via a unique descriptor, a mapping from the metadata associated with the open file back to the specific file itself, fd.
- Large part of linux system programming consists of opening, manipulating, closing and otherwise using file descriptors.
- Regular file
  - contains byes of data organized into a linear array called a byte stream.
  - any of the bytes can read from or written to.
  - operations start at a specific byte, which is one's conceptual location in the file, file position or file offset.
  - file position is an essential piece of metadata that kernel associates with each open file
  - writing a byte to the middle of a file overwrites the byte previously located at that offset, thus not possible to expand a file by writing in the middle of it.
  - size of a file measured in bytes is called its length.
  - file's length can be changed via operation called truncation.
  - a single file can be opened more than once, by a different or even the same process, which receives a unique file descriptor
  - processes can share their fd
  - kernel does not impose any restrictions on concurrent file access.
  - user space programs mustcoordinate among themselvesto ensure proper synchronization
  - A file is referenced by an inode, uniwue to the filesystem
  - inode stores metadata associated with a file, modification timestamp, owner, type, length and location of the file's data but no filename.
  - inode is both a physical object located on disk in Unix style fs and also a conceptual entity represented by a data structure in the kernel.
  - accessing a file through its inode is cumbersome and potential security hole so files are always opened form userspace by a filename.
  - Directories are used to provide names with which to access the files.
  - A directory acts as a mapping of human-readbale names to inodes.
  - name and inode pair is called a link, physical on-disk mapping of this is implemented and managed by the kernel.
  - Directories are much like other files and even have associated inodes.
  - dentry - directory entry inside the kernel. dentry cache for fast lookups.
  - Hard links
    - when multiple links map different names to the same inode, hard links.
    - deleting a file involves unlinking it from the directory structure, remove name and inode pair
    - each inode contains link count to keep track of how many links refer to it.
  - Symbolic links
    - allow links to span filesystems as hard links are meaningless outside its inode's own filesystem
    - incur more overhead than hard links because resolving a symbolic link effectively involves resolving two files: symbolic link and the linked-to-file.
- Special file
  - kernel objects that are represented as files
  - Include
    - block device files
      - accessed as an array of bytes, and can be accessed in any order
      - generally storage devices
    - character device files
      - accessed via a linear queue of bytes
      - i.e keyboard
    - named pipes
      - are an IPC mechanism that provides a communication channel over a fd.
    - unix domain sockets
      - advanced form of IPC that work even on two different machines.
  - special files are a way to let certain abstractions fit into the filesystem.
- Filesystems and namespaces
  - Linux provides a global and unified namespace of files and directories.
  - A filesystem is a collection of files and directories in a formal and valid hierarchy.
  - Mounting and unmounting refers to the adding and removing of filesystems from the global namespace
  - Linux also supports virtual and network filesystems.
  - The smallest addressable unit of a block device is a sector, physical attribute of the device.
  - sectors come in powers of 2, block device cannot transfer or access data smaller than a sector and all I/O must occur interms of one or two sectors.
  - Likewise the smallest addressable unit on a filesystem is the block, which is an abstraction of the filesystem.
    - A block is usually power-of-two multiple of sector size.
  - Blocks are larger than sector but they must be smaller than the page size.(smallest addressable unit by memory management unit)
    - common block sizes are 512 bytes, 1KB and 4KB.
  - Linux supports per-process namespace as opposed to the global unified namespace preffered by Unix, allowing each process to have a unique view of system file and directory hierarchy.
  - By default each process inherits namespace of its parent, but a process may elect to create its own namespace with its own set of mount points and a unique root directory
- Mounted file system concept.
- Special files are provided in order to make i/o devices look like files.
- Pipe is pseudofile used to connect two processes

### Input/Output

- Every OS has I/O subsystem for managing its i/o devices.

### Protection

- Auth in linux is provided by users and groups.
- Each user is associated with a positive integer called a user ID, which in turn means each process is associated with exactly one uid, called the process real uid.
- In kernel uid is the only concept of a user
- Usernames and their respective uids are stored in etc/passwd.
- In addition to real uid, each process also has an effective uid, saved uid and filesystem uid
- supplementary groups are listed in etc/group.
- owners and permissions are stored in the file's inode.
- Files in UNIX are protected by giving them a 9-bit binary protection code.
- consist of 3 3-bit field, owner, group members, everyone.
- every bit has a field for read, write and execute access.
- rwxrwxrwx

### Dual Mode Operation

- Hardware providea at least two modes
  - Kernel Mode
  - User Mode
- Certain Ops are prohibited when running in user mode.
- Careful controlled transitions between user mode and kernel mode.
  - syscalls
    - process requests a system service, e.g exit
    - like a function call but outside the process
    - does not have the address of the system function to call.
    - marshall the syscall id and args in registers and exec syscall.
  - interrupts
    - external asynch event triggers content switch.
    - independent of user process.
    - e.g timer, i/o device
  - exceptions
    - internal synch event in process triggers context switch.
    - protection violation(seg fault). divide by zero
  
### Signals

- one way communication for asynchronous notifications
- can be sent from kernel to process, process to process or from process to itself.
- Number of signals??? each represented by numerical constant and a textual name
- Signals interrupt an executing process causing it to stop what its doing and perform a predetermined action.
- Processes may control what happens when they receive the signal with the exception of SIGKILL and SIGSTOP.
- Handled signals cause the execution of a user-supplied signal handler function

### IPC

- allowing processes toexchange information and notify each other of events is one of an operating systems most important jobs.
- IPC mechanisms suppported by Linux include
  - pipes
  - names pipes
  - semaphores
  - message queues
  - shared memory
  - futexes
  
### Error Handling

- An error is signified via a functions return value and described via a special variable, errno
- defined in errno.h
- errno variable may be read or written to directly, it is a modifiable lvalue
- list of errors and their description, lookup.
- C lib provides a number of functions for translating an errno value to the corresponding textual description.

### Shell

- Has the terminal standard input and standard output.


## System calls

- These are function invocations made from user space into kernel in order to request some service or resource from the operating system.
- applications are able trap into the kernel through this well defined mechanism and execute only code that the kernel allows it to execute.
- Mechanics of issuing them are highly machine dependent and are often expressed in assembly language
- Procedure library provided to aid in this regard.
- Programs always check results of a system call to see if an error occurred.
- System calls happen in a series of steps.
- POSIX system call standard.
- execve system call.
- Processes in Unix have memory divided in 3 segments: text, data, stack.
- Types: 
    - system calls for process management
    - system calls for file management.
    - system calls for directory management
- Windows 32 API 

## C library

- also known as libc
- provides wrappers for system calls, threading support and basic application facilities.
- gcc - GNU Compiler collection

## APIs and ABIs

- API
  - defines the interfaces by which one piece of software communicates with another at the source level.
- ABI 
  - defines the binary interface between two or more pieces of software on a particular architecture.
  - defines how application interacts with itself, the kernel and libraries.
  - ensures binary compatibility.
  - concerned with issues such as calling conventions, byte ordering, register use, system call invocation, linking, library behviour and binary object format.
  - enforced by the toolchain

## Operating system structure.

- Monolithic systems
- Layered systems.
- Microkernels.
- Client-server systems.
- Virtual machines.
- Exokernels.

## Device drivers

- They are distinct black boxes that make a particular piece of hardware respond to a well-defined internal programming interface,
   hiding completely the details of how the device works
- User activities are performed by means of a set of standardized calls that are independent of the specific driver,
  mapping those calls to device-specific operations that act on real hardware is then the role of the device driver.
- 

## Process Deep Dive


## Windows Fundamentals

- 

## xv6 

- Provides basic interfaces introduced by Ken Thompson and Ritchie Unix and well as mimicking Unix internal design.
- Unix provides a narrow interface whose mechanisms combine well, offering a surprising degree of generality.
- An xv6 process consists of user-space memory(instructions, data, stack) and per process state private to the kernel.
- pid assciated with each process.
- fork() creates new processes.
- OS interfaces
  - processes and memory
  - i/o and file descriptors
  - pipes
  - file system
  - 