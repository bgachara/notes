# Understanding NodeJS

Ref:
`cododev: understanding Nodejs`
`nodejs.dev`


- You cannot find ready-made solutions for every occasion, but if you understand inner mechanismsof a complex system,
  you will be able to analyze and critically evaluate other people's experience and come at your own conclusions.
  
## Introduction

- Concepts covered here:
  - Buffers
  - Streams
  - File system
  - Event emitters
  - Module system
  - HTTP, HTTPS, HTTP/2
  - NET
  - UDP/Datagram - sockets
  - DNS
  - OS, Path
  - Child Processes, Worker Threads
  - Deploying
  - Debugging
  - Async and Perf hooks.
  - N-API
  - V8
  - WASI
  

### What is NodeJS?  
  
  - Built in Cpp
  - A nodejs app runs in a single process, without creating a new thread for every request.
  - Provides a set of asynchronous i/o primitives in its standard library that prevent Js code from blocking.
  - Libraries in nodejs are written using non-blocking paradigms, making blocking the exception rather than the norm.
  - Allows node to handle thousands of concurrent connections with a single server without introducing burden of managing thread concurrency.
  -  
  - Javascript Engine - convert Js to Binary.
  - V8 - introduced by Google.
  - ECMAScript - javascript language specification.
  - Embed V8 into your source code.
  - Dependencies:
    - Libuv - C library, abstract non-blocking I/O ops to a consistent interface cross all supported platforms.
    - V8 - 
    - Llhttp
    - C-ares
    - OpenSSL
    - Zlib
  - Event Loop
  - Setting to production:
    - logging kept at a minimum, essential level
    - more caching levels take place to optimise performance as opposed to recompilation.

### EventEmitter

  - is a module that facilitates communication/interaction between objects in Node.
  - core of Node asynchronous event-driven architecture, many of node's built-in inherit from EventEmitter including Express.js
  - Emitter objects emit named events that cause previously registered listeners to be called.
  - So an emitter object basically has two main features
    - Emitting name events.
    - Registering and unregistering listener functions.
  - MyEmitter.on('eventname', c1)
  - MyEmitter.emit('eventname')
  
### Buffers

  - understand binary, decimals, hexadecimals.
  - container that takes in data of some fixed length then sends it out.
  - way to deal with raw binary data.
  - always fixed size.
  - great understanding of encodings.
  - Each character in Hex is 4bits.

### File System

  - sequence of bits and depending on encoding could represent different things.
  - file data - metadata of a file.
  - Three ways to create a file
    - Promises API.
    - Callback API.
    - Synchronous API - blocks main thread.
  - Async generator / iterator.
  - Open - save a file descriptor. 
  - Watch functionality.    

### Streams
  
  - Defn: An abstract interface for working with streaming data in Node.js.
  - Data streaming.
  - Writable streams
  - Readable streams
  - Duplex / Transform Streams
  - create file -> create writestream -> create buffer -> point stream to buffer.
  - 16KB chunks.
  - Internal buffer - 16384 bytes, (stream.writableHighWaterMark), (stream.writableLength) - get filled space in buffer.
  - Has events, properties and methods to it.
  - reduce number of writes as waiting for buffer to fill before writing out.
  - Drain before writing more.
  - Read and Write streams have one internal buffer.
  - writableLength should never be larger than writableHighWaterMark as this will lead to extreme buffering.
  - Watch and compare memory impact of different implementations.  
  - Duplex Stream - Has Readable Internal buffer, Writable Internal buffer.
  - Transform Stream --> read from Readable buffer process data then write to Writable buffer.

### Networking
  
  - Networking card come with unique MAC address. hex number.
  - packet - data container. sent via switch to other computer.
  - source and destination fields on the packet.
  - Routers - work with IP addresses(unique to each device on network).
  - Internet Service Provider (ISP)
  - 127.0.0.1 - loop back address / localhost.
  - Ports on addresses have to be configured to do sth.
  - Network Layers
    - Physical layer --> Cable, Bits, Signal, Binary Transmission.
    - Data Link layer --> Cable, Frames, MAC Addresses
    - Network layer --> IP Addresses, Packets, Path Determination
    - Transport layer --> Segments, End-to-End connections, UDP/TCP
    - Application layer --> Data
  - OSI Model
    - 
  - current cables can move upto 1GB per second.
  - net module base of all networking on node.
  - TCP
    - data is received as exactly it was sent.
    - three way handshake
    - Headers
      - source port.
      - destination port(16 bits)
      - sequence number(32 bits)
      - ack numbers(32 bits)
      - Other headers
      -
  - UDP
      - Headers
        - source port(16 bits)
        - destination port(16 bits)
        - segment length(16 bits)
        - checksum(16 bits)
    - data prefers speed, highly lossy data, video, audio.