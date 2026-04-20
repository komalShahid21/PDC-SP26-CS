Python MPI Programming — Chapter 4
This repository covers distributed computing using mpi4py — Python's interface to the Message Passing Interface (MPI). Each program demonstrates a specific MPI communication pattern, from basic hello world and point-to-point messaging to collective operations like broadcast, scatter, gather, reduce, and all-to-all. It also covers deadlock problems and virtual topologies.

Note: These programs require mpi4py and MPI to be installed, and must be run using mpiexec or mpirun instead of a regular python command.


Program Descriptions

1. helloworld_MPI.py — Hello World with MPI
Description:
This is the simplest MPI program. It launches multiple processes and each one prints "hello world" along with its rank (unique ID number). It is the starting point for understanding how MPI works.
How it works:
MPI.COMM_WORLD is the communicator that connects all running processes. Each process calls comm.Get_rank() to find out its own ID (rank) and prints it.
Result:
hello world from process  0
hello world from process  1
hello world from process  2
hello world from process  3
(Order may vary since all processes run in parallel.)
Advantages:

Very simple introduction to MPI concepts.
Shows how multiple processes run the same code but behave differently based on their rank.

Disadvantages:

Does not demonstrate any real communication between processes.

Where it can be used:

Testing that MPI is correctly installed and working on a system.


2. pointToPointCommunication.py — Point-to-Point Communication
Description:
This file demonstrates direct one-to-one communication between two specific processes using comm.send() and comm.recv(). Process 0 sends a number to process 4, and process 1 sends a string to process 8.
How it works:
Each process checks its rank. Process 0 sends the number 10000000 to process 4. Process 1 sends the string "hello" to process 8. Processes 4 and 8 receive and print the data sent to them.
Result:
my rank is: 0
sending data 10000000 to process 4
my rank is: 1
sending data hello to process 8
data received is = 10000000
data1 received is = hello
Advantages:

Most basic and flexible form of MPI communication.
Any two processes can exchange any type of data directly.

Disadvantages:

Does not scale well — managing many point-to-point connections manually becomes complex.
Blocking send/receive can cause processes to wait for each other.

Where it can be used:

Distributed systems where specific nodes need to exchange data directly, such as client-server models or pipeline stages.


3. deadLockProblems.py — Deadlock in MPI
Description:
This file demonstrates a classic deadlock situation in MPI where two processes are each waiting to receive from the other before sending — causing both to wait forever and the program to hang.
How it works:
Process 1 tries to recv from process 5 first, then send to process 5. Process 5 does send first then recv from process 1. Because process 1 is waiting to receive while process 5 is waiting to send (and vice versa in a different order), they can get stuck depending on buffer size.
Result:
May print rank messages and then hang, or complete if the MPI implementation buffers the send.
Advantages:

Clearly shows how deadlocks happen in parallel programs.
Helps understand why the order of send and receive matters.

Disadvantages:

The program may hang indefinitely depending on the MPI implementation.

Where it can be used:

Understanding and debugging communication issues in distributed programs. A real fix would use comm.sendrecv() or non-blocking isend/irecv.


4. broadcast.py — Broadcast
Description:
This file demonstrates comm.bcast() — a collective operation where one process (the root) sends the same data to all other processes at once.
How it works:
Process 0 sets variable_to_share = 100. All other processes set it to None. After comm.bcast() is called with root=0, every process receives the value 100 and prints it.
Result:
process = 0 variable shared = 100
process = 1 variable shared = 100
process = 2 variable shared = 100
process = 3 variable shared = 100
Advantages:

Very efficient — one call distributes data to all processes simultaneously.
Much simpler than sending individual messages to each process.

Disadvantages:

All processes receive the exact same data — not useful when different processes need different data.

Where it can be used:

Distributing configuration settings, model parameters in machine learning, or any shared data that all worker processes need.


5. scatter.py — Scatter
Description:
This file demonstrates comm.scatter() — a collective operation where process 0 splits a list and sends one unique piece to each process, so each process gets a different value.
How it works:
Process 0 has a list [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]. After comm.scatter(), each process receives exactly one element from the list based on its rank.
Result:
process = 0 variable shared = 1
process = 1 variable shared = 2
process = 2 variable shared = 3
...
process = 9 variable shared = 10
Advantages:

Efficiently distributes different pieces of data to different processes.
Clean and simple — no manual send loops needed.

Disadvantages:

The list size must match the number of processes exactly.
Only process 0 (root) holds the original full data.

Where it can be used:

Distributing dataset chunks to worker processes for parallel processing, such as dividing a large array for parallel computation.


6. gather.py — Gather
Description:
This file demonstrates comm.gather() — the opposite of scatter. Each process computes a value and sends it to process 0, which collects all results into a single list.
How it works:
Each process computes (rank+1)**2 (its rank plus one, squared). All values are gathered at process 0 using comm.gather(data, root=0). Process 0 then prints each received value.
Result:
rank = 0 ...receiving data to other process
process 0 receiving 4 from process 1
process 0 receiving 9 from process 2
process 0 receiving 16 from process 3
Advantages:

Simple way to collect results from all worker processes back to one place.
Pairs naturally with scatter for a complete distribute-compute-collect workflow.

Disadvantages:

All results are collected at one process — this can become a bottleneck for large datasets.

Where it can be used:

Collecting computation results from parallel workers, merging partial results in distributed algorithms.


7. alltoall.py — All-to-All Communication
Description:
This file demonstrates comm.Alltoall() — every process sends a unique piece of data to every other process and receives data from every other process simultaneously.
How it works:
Each process creates a senddata array based on its rank. After Alltoall, each process has a recvdata array containing one element received from each other process. Both the sent and received arrays are printed.
Result:
process 0 sending [0 1 2 3] receiving [0 0 0 0]
process 1 sending [0 2 4 6] receiving [1 2 3 4]
process 2 sending [0 3 6 9] receiving [2 4 6 8]
process 3 sending [0 4 8 12] receiving [3 6 9 12]
Advantages:

Every process communicates with every other process in one single operation.
Very efficient for problems that require full data exchange between all nodes.

Disadvantages:

Communication cost grows with the number of processes — not efficient for very large clusters.

Where it can be used:

Matrix transposition, FFT algorithms, or any parallel algorithm requiring total data exchange between all processes.


8. reduction.py — Reduction Operation
Description:
This file demonstrates comm.Reduce() — a collective operation where each process contributes data and all contributions are combined using an operation (here, MPI.SUM) and the final result is sent to process 0.
How it works:
Each process creates a senddata array based on its rank. comm.Reduce() sums all arrays element-by-element across all processes and stores the result in recvdata at process 0.
Result:
process 0 sending [0 1 2 3 4 5 6 7 8 9]
process 1 sending [0 2 4 6 8 10 12 14 16 18]
...
on task 0 after Reduce: data = [0 3 6 9 ...]
on task 1 after Reduce: data = [0 0 0 0 ...]
Advantages:

Efficient way to aggregate data from all processes using standard operations (SUM, MAX, MIN, etc.).
Only one process holds the final result, reducing memory usage.

Disadvantages:

Only process 0 gets the final reduced result — others receive zeros.
If all processes need the result, Allreduce should be used instead.

Where it can be used:

Computing global sums, averages, or maximums across distributed datasets — common in scientific computing and parallel machine learning.


9. virtualTopology.py — Virtual Cartesian Topology
Description:
This file demonstrates how to organize MPI processes into a 2D grid (cartesian topology) so each process knows its position (row, column) and can identify its neighbors (up, down, left, right).
How it works:
The total number of processes is arranged into a grid using comm.Create_cart(). Each process finds its row and column using Get_coords(), then finds its four neighbors using Shift(). All this information is printed by each process.
Result:
Building a 2 x 2 grid topology:
Process = 0 row = 0 column = 0
----> neighbour_processes[UP] = 2
neighbour_processes[DOWN] = 2
neighbour_processes[LEFT] = 1
neighbour_processes[RIGHT] = 1
...
Advantages:

Makes it easy to implement grid-based parallel algorithms where neighbors need to communicate.
Periodic boundaries (wrapping) are supported — edge processes connect to the opposite side.

Disadvantages:

Only works cleanly when the number of processes forms a near-square grid.
More complex to set up than simple communicators.

Where it can be used:

Physics simulations, image processing, cellular automata, or any algorithm that works on a 2D grid structure.


Applications
ConceptReal-World UseHello World MPITesting MPI setup, learning basicsPoint-to-PointClient-server models, pipeline stagesDeadlockDebugging distributed communication bugsBroadcastSharing model parameters, config dataScatterDistributing dataset chunks to workersGatherCollecting results from parallel workersAll-to-AllMatrix transposition, FFT algorithmsReductionGlobal sum/average in distributed computingVirtual TopologyGrid-based simulations, image processing

About
Each program in this repository demonstrates a specific MPI communication pattern using the mpi4py library. Together they cover the full range of MPI operations — from simple one-to-one messaging to complex collective communications and process topologies used in high-performance and distributed computing.