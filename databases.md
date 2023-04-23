# What is a Database??

`Jamie Brandon What is a database TigerBeetle talk`
`Database Engineering by Meta`
`Expert Mysql`


## Introduction

- Long lived data.
- Parts:
  - Storage Engine.
    - intel memory lantency checker.
    - prefetching
    - pipelining
    - speculation
    - fio 
      - disk perf tool
    - ordering
    - fsync
    - reliability
    - send code to data vs data to code??? cover for letency
    - why are query languages weird?
  - Concurrency control.
  - Query Language

## Anatomy of a DB.

- Good to understand how best to optimize a server and even how to utilize its features.
- Essential when modifying or extending its features

### Types of DBs

- Object Oriented Database systems.
  - access via a programmatic interface.
- Object Relational 
- Relational

## Structure

- Client Apps
  - Database connectors.
    - based on Open Database Connectivity model.
    - include client access, API, db driver
- Query Interface
  - i.e SQL
- Query Processing
  - query shipping
  - logical vs physical models of database design
  - query tree - turned from a logical plan to physical plan.
  - Steps
    - Parsing
    - Query Validation
    - Optimization
    - Plan Generation/Compilation
    - Execution
  - How does the query optimizer work??
    - Cost based optimization
    - Heuristic optimization
    - Semantic optimization
    - Parametric optimization
- Internal Representation of queries.
- Query Execution
  - Iterative
    - generate iterative programs from algebra-based query specs.
    - defined compiled functional primitives that are then combined in a call stack.
  - Interpretative
    - form query exec using exisiting compiled abstractions of basic operations.
    - reconstructed as a queue of method calls, which are each taken off queue and processed.
*compiled here means one thats been optimised and stored for future execution not actually compiled*
- File access
  - Goal is to find data we want quickly and efficiently without scanning more blocks than necessary.
  - Storage strategies
  - Buffering mechanisms
  - Index mechanisms
- Query results.

## MySQL structure

- SQL Interface
  - multi-threaded
- Parser
  - implemented in YACC and Lex
- Query Optimizer
- Query Execution