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

- Length of time taken to perform the algorithm, wrt running time and memory space.
- Adaptability of algorithm to different kind of computers.
- Quantitative behaviour of algorithms.
- Framework for algorithm analysis
  - Measuring an input's size.
    - time and space efficiencies measured as functions of input size.
  - Units for measuring running time.
    - rely on a measure that does not depend on extraneous factors, machine, network, compiler
    - identify the basic operation, one which contributes the most to the running time, and compute number of times its executed.
    - orders of growth
      - n, log n, nlogn, n^2, n^3, 2^n
    - worst, best and average case efficiencies
      - use basic operation to better explain this statistic.
    - amortized efficiency
      - not applied to single run of an algorithm but a sequence of operations performed on same ds.
      - entire sequence of n is better than worst-case multiplied by n.
- Asymptotic Notations and Basic efficiency classes
  - Big Oh, Big Omega, Big Theta.
  - Using limits for comparing orders of growth
- Analyzing nonrecursive algorithms
  - decide on a parameter indicating an input size.
  - identify the algo's basic operation
  - check whether number of times its executed depends on the input size, if it depends on another property, different calculations for efficiencies for both.
  - set up a sum expressing number of times algo's basic operation is executed.
  - establish order of growth.
- Analyzing recursive algorithms
  - decide on a parameter indicating an input size.
  - identify the algo's basic operation
  - check whether the number of times the basic operation is executed can vary on different inputs of the same size.
  - set up a recurrence relation, with an appropriate initial condition for the number of times the basic operation is executed.
  - solve the recurrence or at least ascertain order of growth of its solution.
- Empirical Analysis of Algorithms
  - overcome challenges of mathematical analysis.
  - General plan for this
    - Understand the experiment's purpose.
    - Decide on the efficiency metric M to be measured and the measurement unit(operation count vs a time unit)
    - Decide on characteristics of the input sample(range, size, etc)
    - Prepare a program implementing the algorithm for the experimetation
    - Generate a sample of units
    - Run the algorithms on the sample inputs and record the data observed
    - Analyze the data observed.
  - Different goals one can pursue in emprical analysis
    - checking the accuracy of a theoretical assertion about the algorithm's efficiency.
    - comparing efficiency of sevaral algorithms of solving the same problem, different implmentations of the same algorithms
    - develop hypothesis of algorithms efficiency class.
    - efficiency of program implementing algo' in a particular machine.
    - profiling
    - use random number generator for sample generation, linear congruential method.     

## Misc

- Sometimes we find that many questions have a similar structure and are instances of the same problem.