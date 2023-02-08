# Art of Efficient Programs

`notes from the book by the same name and additional notes on optimizations`


## Performance Fundamentals

- methodology for studying the performance of programs, based on measurements, benchmarking, profiling.
- need for understanding intracacies between processor and memory architecture.

### What is Performance?

- Efficiency vs Performance
- Efficiency deals with using resources optimally and not wasting them.
- An efficient program makes good use of computational hardware, doesn't leave resources idle:processor, memory, network.
- Speed - Throughput - amount of computations the program does in a given time.
- Performance: throughput, power consumption, real-time apps, context dependent
- Evaluating, estimating and predicting performance.
- Performance considerations and targets have their place at the design stage jsut like other design goals.
- Do not optimize prematurely and don't pessimize intentionally either.

### Learning list for high-perfomance programs

- Choosing the right algorithm.
- Using the CPU resources effectively.
- Using memory effectively.
- Avoiding unnecessary computations
- Use concurrency and multi-threading effectively.
- Using the programming language effectively.
- Measuring performance and interpreting results.

```bash

`side notes on optimisation`

- In an optimisation problem, a function is to maximized or minimized, this is the objective functio or the performance index..
- Variable in the objective function are denoted as design variable or decision variables and can take values from an upper and lower limit.
- The constraints are functions of the design variables.
- If the design variables satisfy all the constraints, they represent a feasable set.
- If the objective function and constraints re linear functions then the problem becomes a linear programming problem

- The role of designer is to provide a problem specification that details the parameters, constants, objectives and constraints that are achieved.
- The designer is responsible for crafting the problem and quantifying the merits of potential designs.
- Also supplies a baseline design or initial design point to the optimization algorithm.

- Feasible solutions are those that satisfy all the constraints in the optimisation problem.

- Design specification --> Initial Design --> Evaluate Performance --> Good -->No> Change design --> Evaluate Performance Yes> Final Design.

- An optimisation algorithm is used to incrementally improve the design until it can no longer be improved or until the budgeted time or cost has been reached.
- Designer analyses the result of the optimization process to ensure its sustainability for the final application.
- Misspecifications in problem, poor baseline design, improperly implementedand unsuitable optimisation algos.
- Optimize with respect to data not intuition.

- Optimization algos are limited by the designer's ability to specify the problem.
- No guarantee that the optimization algo will produce an optimal solution.


```
## Performance Measurements