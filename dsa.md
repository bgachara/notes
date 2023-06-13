# Data Structures and Algorithms

ref paper: `Primegean Frontend Masters course`
           `A common sense guide to DSA`
           `Introduction to the design and Analysis of Algorithms`
           `MIT Introduction to Algorithms Fall 2011`
          
`The most valuable acquisitions in a scientific or technical education are the general purpose mental tools which remain servicable for a life-time`      
`Any problem in computer science can be solved using indirection`

## Introduction

- This is a sequence of unambiguous instructions for solving a problem...i.e given a legitimate input, it returns an output in finite time.
- Algorithms are well-defined procedures for solving problems.
- Reason for using DSA
  - Efficiency
  - Abstraction
  - Reusability
- Should also be able to design and analyze new algorithms.
- Specific algorithmic design techniques can be interpreted as problem solving strategies useful regardless if a computer is present.

## General approaches in Algo Design

- Randomized algorithms
  - rely on the statistical properties of random numbers.
- Divide and conquer algorithms
  - rely on divide, conquer and combine.
- Dynamic programming solutions
  - subproblems are not independent of one another.
- Greedy algorithms
  - make decisions that look best at the moment, locally optimal in the hope that they will lead to globally optimal solutions.
- Approximation algorithms
  - do not compute optimal solutions, just solutions that are good enough, computationally expensive problems


## Design and Analysis of Algorithms.

- Algorithm design techniques.
- Classification of algorithm around design ideas.
- Utility in general problem solving techniques. 

## Fundamentals of Algorithmic Problem Solving

- Understanding the problem.
- Ascertaining the capabilities of the computational device
- Choosing between exact and appropriate problem solving.
- Algorithm design techniques
  - an algo' design technique is a general approach to solving problems algorithmically that is applicable to a variety of problems from different areas of computing.
- Designing an algorithm and data structures
- Methods of specifying an algorithm
- Proving an algorithm's correctness
- Analyzing an algorithm
- Coding it up

## Important Problem Types

- Sorting
- Searching
- String Processing
- Graph problems
- Combinatorial problems
- Geometric problems
- Numerical problems

## Fundamental Data Structures

def: a particular scheme of organizing related data items.
     nature of data items depend on the problem at hand.

- Linear Data Structures
  - Array
    - used to implement a variety of other data structures, i.e string(sequence of characters from an alphabet terminated by special character indicating character end)
  - Linked List
  - Array and Linked list are the two principal choices in representing a more abstract data structure i.e
  - Linear lists
    - Stacks
      - insertions and deletions only done at the end
    - Queues
      - front and back insertions and deletions, FIFO.
      - priority queue

- Graphs
  - non-empty set of vertices and pairs of edges
  - graph representations
    - adjacency matrix
      - 1 if vertices intersect, 0 if absent.
    - adjacency list
      - collection of linked list, one for each of the vertices
  - weighted graphs
    - graph with numbers assigned to its edges.
  - paths and cycles
    - connectivity and acyclicity.
    - directed path

- Trees
  - tree is a connected acyclic graph
  - graph that has no cycles but it is not necessarily connected is called a forest.
  - number of edges in a tree is one less than the number of its vertices.
  - rooted trees
  - state space trees: backtracking and branch-and-bound
  - Height of tree
  - ordered trees
    - binary trees

- Sets and Dictionaries
  - can be represented as bit vector
  - also represented as a list

## Computational Thinking

- Decomposition - break problem down into subproblems.
- Pattern Recognition - notice similarities, differences, properties or trends in data.
- Pattern Generalization - define a concept or idea in general terms via doing away with the unnecessary. Abstraction.
- Algorithm design - repeatable, step-by-step process to solve a particular problem.

> A procedure that  has all the characteristics  of an algorithm except that it possibly lacks finiteness may be called a computational method.

## Important features

- Finiteness - algorithm must always terminate after a finite number of steps.
- Definiteness - Each step must be precisely defined, actions must be rigorously and unambiguously specified for each case. Programming languages used to achieve this.
- Input - has zero or more inputs. Given as algo begins or dynamically as it runs.
- Outputs - zero or more quantities that have a relation to the inputs.
- Effectiveness - operations must be sufficiently basic

### Algorithmic Analysis

- Length of time taken to perform the algorithm.
- Adaptability of algorithm to different kind of computers.
- Quantitative behaviour of algorithms.
- 

## Misc

- Sometimes we find that many questions have a similar structure and are instances of the same problem.