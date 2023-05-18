# Distributed Systems

ref: `MIT 6.824: Distributed Systems`


## Introduction

- A set of cooperating computers over a network to get given workset done.
- Try everything else before building distributed systems, if you can build on one node build it.
- Why build?
  - Parallelism
  - Fault torelance.
    - Availability
    - Recoverability
      - non-volatile storage
    - Replication
      - as far away as possible.
  - Physical adherance
  - Security / Isolated.

- Challenges
  - Concurrency
  - Partial Failures
  - Performance.

- Implement
  - MapReduce
  - Raft for fault torelance
  - Use raft to build k/v server
  - Shard k/v server.

- Infrastructure for Applications
  - Storage
  - Communication
  - Computation.

- Implementations for distributes systems abstaractions
  - Remote Procedure Calls
  - Threads
  - Concurrency control

- Performance
  - Scalability speedup.

- Consistency
  - Strong consistent scheme
  - Weak consistent scheme
  
## Map Reduce

  - ref paper:`MapReduce paper`
  - takes in a Map function returns intermediate output
  - later takes in intermediate results and reduces them to single result.
  - map tasks and reduce tasks.
  - map functions should be pure.
  - Most constraining part was the network throughput.
  - 