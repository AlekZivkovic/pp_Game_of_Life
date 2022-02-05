# <p align= "center"> Parallel Programming H1 </p>
  <p align= "center"> Game of Life </p>
  
## Cellular automata
Cellular automata are a class of models that can be used to simulate various natural phenomena. They have been used to model tumor development, through a three-dimensional tissue map, and to predict cell growth over time. In neuroscience, cellular automata can be used to study the population of neuronal activation. There are many other applications, including those in chemistry, biology, ecology and many other branches of science.

Cellular automata are basically a set of (spatially) arranged units that we call cells. Each cell performs one of the possible actions, or takes one of the possible states, depending on its immediate environment. Perhaps the most famous example of cellular automata is Conway's Game of Life.

## Game of life
Conway's Game of Life is a cellular automaton consisting of a rectangular network of cells. Every cell can be in one of all states: alive and dead. The game takes place through time iterations, where the cells in one iteration calculate their state in the next. The state of the cell is calculated based on its current state, the current state of all 8 immediate neighbors of the cell and the following rules:

If the cell has less than 2 living neighbors, it will be dead in the next iteration
If the cell has more than 3 living neighbors, it will be dead in the next iteration
If the cell is alive and has 2 or 3 living neighbors, it will be alive in the next iteration
If the cell is dead and has 3 living neighbors, it will be alive in the next iteration

The basic data structure of the Game of Life is a state matrix, which for each cell a [i, j] contains data on whether the cell is alive or dead. When implementing the game, there is a problem of neighboring cells that are along the edge of the network (cells in the first column do not have "left" neighbors, etc.). There are several solutions to this problem such as:

Assume that non-existent neighbors are always dead
Introduce "cycles", so that the last column is the "left" neighbor of the first column, and the last row is the "upper" neighbor of the first row (effectively torus).
Use a different approach when creating tasks.

## Assignment
The task is to implement a parallel version of the Game of Life in the Python programming language, in several ways. During implementation, ensure that after executing the specified number of iterations, it is saved through state matrices over time (system states in each iteration), which is compatible with the given animation function (next cell in the file).

### Using threads that simulate one cell each and synchronizing with Keys, Semaphore and Conditions 
>Each thread simulates the operation of one cell in the system and has access to the states of its neighbors. Cells do not have access to the global iteration counter, but each cell internally keeps track of the current iteration number. In addition to the data matrix, it is necessary to introduce:

>1. List of counters of neighbors who read the current value (one counter for each cell). 
The eighth (last) neighbor who reads the value wakes up the cell so that it can write a new value in >the state matrix (wake up with a traffic light). Take care of the synchronization of neighbors who >change the value of the counter.
>2. Condition on which all cells that have entered a new value in the state matrix are waiting, >before moving on to the next iteration.
>3. A key-protected cell counter that has written a new value to the state matrix. The last cell activates the Condition for the next iteration. Take care of the order of taking and releasing the counter key and the condition key.

### Using threads that simulate one cell at a time and synchronizing message lines
>Each thread simulates the operation of one cell in the system. The state of each cell is stored inside the cell (system operation does not rely on a shared state matrix). Cells exchange their status information via the message queue. For the purposes of work analysis, a shared array of state matrices can be introduced (the i-th element of the array is the array of state arrays of the i-th iteration), in which cells write their states (cells cannot read from this array!).

### Using processes that simulate one cell at a time and synchronizing message lines
>Each process simulates the operation of a single cell in the system. The state of each cell is stored inside the cell (system operation does not rely on a shared state matrix). Cells exchange >their status information via the message queue. For the purposes of work analysis, implement a special service (additional process) in which all cells report a new state when changing (where messages contain cell coordinates, iteration number and new state). The service needs to reconstruct and save (or return to the main program) through the state matrix.

### Using multiple processes through the process pool, generating tasks at the cell set level
>Divide the state matrix into N parts (where the parameter is configurable) and generate a task for each part (call function whose parameters define which part of the matrix should be processed). The function should return a series of cell coordinates and their new values, and a matrix for the next iteration can be created in the main program. Current values of cells and neighbors can be read from the shared matrix.
