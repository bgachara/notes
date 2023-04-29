# Understanding NodeJS

Ref:
`cododev: understanding Nodejs`
`nodejs.dev`


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
  
- What is NodeJS?  
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
    - 