# Operating Systems

`Notes from Modern operating systems by Tanenbaum`

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
- Abstraction provider definition the OS can be thought of as the top-down view and alternative bottom-up view would be of resource manager.
- Resource management includes multiplexing(sharing) resources in two different ways: in time and in space.
- Spooling(Simultaneous Peripheral Operation On Line)
- Multics(Multiplexed Information and Computing Service)
- Unix
- Posix - standard of Unix that defines a minimum system call interface that conformant Unix systems must support.

## Computer Hardware

- Processor - fetch, decode, execute cycle.
            - introduction of pipelines for more work.
            - superscalar CPU.
            - Different register types.
            - Moore's Law - transistor doubling every 18 months.
            - Multithreading/ Hyperthreading - each thread appears to the CPU as a separate CPU.
            - Multicore chips.

- Memory - should ideally be faster than the CPU so no memory access times.
         - constructed as a hierarchy of layers.
         - register -> cache -> main memory -> magnetic disk
         - questions to every cache situation:
            - when to put new item in cache.
            - which cache line to put new item in.
            - which item to remove from cache
            - where to put newly evicted item in main memory

- I/O Devices - consist of the controller and the device itself.
              - controller is a chip or set of chips that physically controls the device...accepts commands from the OS.
              - Device driver is s/w that talks to a controller, giving commands and accepting responses.
              - To be used, device driver has to be put into OS so as to run in kernel mode.
              - Can run in user mode but rarely is this as its a feature where access in a controlled way is required.
              - device registers form the I/O port space.
              - I/O implemented in three ways
                  - Busy waiting.
                  - Interrupts.
                  - Direct Memory Access.

- Buses - As processors and memories got faster, ability of one bus became strained and thus more were added.
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


## Operating system concepts

### Process

- a program in execution.
- associated with each process is its address space, a list of memory locations  from 0 to some maximum which can read and write to.
- Address space contains executable program, program's data, and its stack.
- also with each process is some resources, registers(program counter and stack pointer), open files, outstanding alarms, related processes and any other info needed to run program
- A process is thus a container with all information needed to run a program.
- In many OS, all info on any process, other than content of its own address space is stored in a process table, which is an array of structures, one for each process in existence.
- A suspended process consists of its address space and its process table entry.
- Each person on a system has a UID which is attached to every process they start.

### Address spaces

- Holds executing program.
- On many computers addresses are 32 or 64 bits, giving address space of 2^64 bytes.
- Virtual memory augments processes where their address space is larger than what is available.

### Files

- System calls needed to create files, remove files, read files and write files.
- Directory used to group files together.
- Before file manipulation, it must be opened, at which time permissions are checked, if permitted, file descriptor returned else error code.
- Mounted file system concept.
- Special files are provided in order to make i/o devices look like files.
- Pipe is pseudofile used to connect two processes

### Input/Output

- Every OS has I/O subsystem for managing its i/o devices.

### Protection

- Files in UNIX are protected by giving them a 9-bit binary protection code.
- consist of 3 3-bit field, owner, group members, everyone.
- every bit has a field for read, write and execute access.

### Shell

- Has the terminal standard input and standard output.

