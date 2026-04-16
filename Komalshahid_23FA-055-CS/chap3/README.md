# Python Multiprocessing — Chapter 3

This repository covers Python's `multiprocessing` module in depth. Each program demonstrates a specific concept related to process-based parallelism — from spawning and naming processes to killing them, running them in the background, and making them communicate using pipes and queues. The programs also cover process pools, barriers, and subclassing processes for custom behavior.

---

## Program Descriptions

---

### 1. `myFunc.py` — Helper Function

**Description:**
This file defines a simple reusable function `myFunc` that is imported and used by other programs in this repository. It prints output based on the number passed to it.

**How it works:**
The function takes a number `i`, prints which process called it, and then loops from 0 to `i` printing each value.

**Advantages:**
- Keeps code clean by separating reusable logic into its own file.
- Used across multiple programs to avoid code repetition.

**Disadvantages:**
- Does nothing on its own — only works when imported or called by another program.

**Where it can be used:**
- Any program that needs a simple testable function to run inside a process.

---

### 2. `spawning_processes.py` — Spawning Processes

**Description:**
This file shows the most basic way to create and run multiple processes using `multiprocessing.Process`. It spawns 6 processes, each calling `myFunc` with a different number.

**How it works:**
A loop runs 6 times, each iteration creating a new `Process` with `myFunc` as the target and `i` as the argument. Each process is started and immediately joined before the next one begins.

**Result:**
```
calling myFunc from process n°: 0
calling myFunc from process n°: 1
output from myFunc is: 0
calling myFunc from process n°: 2
...
```

**Advantages:**
- Simple and clear introduction to creating processes in Python.
- Shows how each process runs independently.

**Disadvantages:**
- Processes run one at a time (joined immediately), so no actual parallel execution.

**Where it can be used:**
- Running independent tasks in separate processes, such as data processing jobs or batch operations.

---

### 3. `spawning_processes_namespace.py` — Spawning Processes with Import

**Description:**
This file does the same as `spawning_processes.py` but imports `myFunc` from the separate `myFunc.py` file instead of defining it inline — demonstrating how to organize code across files.

**How it works:**
`myFunc` is imported from `myFunc.py`. A loop creates 6 processes, each running `myFunc` with a different argument, started and joined one by one.

**Result:**
Same output as `spawning_processes.py`.

**Advantages:**
- Shows good code organization — logic separated into its own module.
- Cleaner and more maintainable than defining everything in one file.

**Disadvantages:**
- Requires `myFunc.py` to be in the same folder, otherwise it will fail.

**Where it can be used:**
- Larger projects where helper functions are kept in separate files and reused across multiple scripts.

---

### 4. `naming_processes.py` — Naming Processes

**Description:**
This file shows how to assign a custom name to a process and how to retrieve the current process name from inside the process using `multiprocessing.current_process().name`.

**How it works:**
Two processes are created — one with a custom name `'myFunc process'` and one with a default name assigned by Python. Both run the same function which prints the process name when starting and exiting.

**Result:**
```
Starting process name = myFunc process
Starting process name = Process-2
Exiting process name = myFunc process
Exiting process name = Process-2
```

**Advantages:**
- Makes it easy to identify which process is doing what, especially useful for debugging.

**Disadvantages:**
- Only demonstrates naming — no actual task or data processing involved.

**Where it can be used:**
- Logging and monitoring systems where tracking individual process activity is important.

---

### 5. `process_in_subclass.py` — Process as a Subclass

**Description:**
This file shows how to create a custom process class by subclassing `multiprocessing.Process` and overriding the `run` method — similar to how threads are subclassed in Chapter 2.

**How it works:**
A class `MyProcess` extends `multiprocessing.Process` and overrides `run` to print the process name. A loop creates and starts 10 instances of `MyProcess`, each joined before the next starts.

**Result:**
```
called run method in MyProcess-1
called run method in MyProcess-2
...
called run method in MyProcess-10
```

**Advantages:**
- Clean and reusable — custom logic can be added directly inside the class.
- Follows object-oriented design principles.

**Disadvantages:**
- Processes run one at a time due to immediate joining — no parallelism.

**Where it can be used:**
- Building reusable process classes for tasks like file processing, data transformation, or network requests.

---

### 6. `killing_processes.py` — Terminating a Process

**Description:**
This file demonstrates how to forcefully stop a running process using `p.terminate()`, and how to check a process's state and exit code at different points in its lifecycle.

**How it works:**
A process running `foo` (which counts from 0 to 9 with 1-second sleeps) is started and immediately terminated before it can finish. The process state and exit code are printed at each step.

**Result:**
```
Process before execution: <Process ...> False
Process running: <Process ...> True
Process terminated: <Process ...> False
Process joined: <Process ...> False
Process exit code: -15
```

**Advantages:**
- Shows full process lifecycle — before, running, terminated, and joined states.
- Useful for understanding how to stop runaway or unresponsive processes.

**Disadvantages:**
- Forceful termination does not allow the process to clean up resources properly.

**Where it can be used:**
- Timeout handling, stopping processes that take too long, or canceling tasks on user request.

---

### 7. `run_background_processes.py` — Daemon vs Non-Daemon Processes

**Description:**
This file shows the difference between a daemon process (`daemon=True`) and a normal process (`daemon=False`). A daemon process is automatically killed when the main program exits, while a normal process keeps running until it finishes.

**How it works:**
Two processes run the same function `foo`. One is set as a daemon, the other is not. The main program ends after starting both — the daemon process gets killed automatically while the non-daemon process completes on its own.

**Result:**
Only the non-daemon process output is fully printed. The daemon process may be cut off.

**Advantages:**
- Daemon processes are useful for background tasks that should not block the program from exiting.

**Disadvantages:**
- Daemon processes can be killed before finishing their work — not suitable for tasks that must complete.

**Where it can be used:**
- Background monitoring, logging, heartbeat services, or any task that should stop when the main program ends.

---

### 8. `run_background_processes_no_daemons.py` — Both Non-Daemon Processes

**Description:**
This file is the same as `run_background_processes.py` except both processes have `daemon=False`. This shows what happens when neither process is a daemon — both run to completion regardless of when the main program finishes.

**How it works:**
Both processes run the same `foo` function with `daemon=False`. Both complete fully before the program ends.

**Result:**
Both processes print their full output — starting and exiting messages with all numbers.

**Advantages:**
- Guarantees both processes finish before the program exits.

**Disadvantages:**
- The main program has to wait for all processes to finish — not ideal for quick background tasks.

**Where it can be used:**
- Tasks where complete execution is required, such as writing results to a file or sending data to a server.

---

### 9. `processes_barrier.py` — Barrier for Process Synchronization

**Description:**
This file uses `multiprocessing.Barrier` to synchronize processes — making two processes wait for each other before continuing. Two other processes run without a barrier to show the difference in timing.

**How it works:**
Four processes are created. Two use `test_with_barrier` and call `synchronizer.wait()` — they both wait until the other arrives before printing the timestamp. The other two use `test_without_barrier` and print immediately without waiting.

**Result:**
```
process p3 - test_without_barrier ----> 2026-04-06 ...
process p4 - test_without_barrier ----> 2026-04-06 ...
process p1 - test_with_barrier ----> 2026-04-06 ...
process p2 - test_with_barrier ----> 2026-04-06 ...
```
The barrier processes show nearly identical timestamps, the non-barrier ones differ.

**Advantages:**
- Clearly shows the difference between synchronized and unsynchronized processes.
- Useful for parallel tasks that must reach a checkpoint together.

**Disadvantages:**
- If one process fails to reach the barrier, all others are blocked forever.

**Where it can be used:**
- Parallel simulations, phased computations, or any task where all workers must complete one stage before starting the next.

---

### 10. `process_pool.py` — Process Pool

**Description:**
This file uses `multiprocessing.Pool` to run a function across multiple inputs in parallel using a pool of worker processes — the simplest and most efficient way to parallelize a task over a list of data.

**How it works:**
A pool of 4 worker processes is created. The `function_square` function (which squares a number) is mapped over a list of numbers from 0 to 99 using `pool.map`. All 100 numbers are processed in parallel and results are collected in a list.

**Result:**
```
Pool: [0, 1, 4, 9, 16, 25, ... , 9801]
```

**Advantages:**
- Very clean and simple — no manual process creation or joining needed.
- Automatically distributes work across available CPU cores.
- `pool.map` collects results in order, just like the built-in `map`.

**Disadvantages:**
- All inputs and outputs must fit in memory.
- Not suitable for tasks that need shared state between workers.

**Where it can be used:**
- Image processing, data transformation, mathematical computations, or any task that needs to apply the same function to a large list of inputs.

---

### 11. `communicating_with_pipe.py` — Inter-Process Communication with Pipe

**Description:**
This file shows how two processes can communicate by passing data through a `multiprocessing.Pipe` — like a two-way channel connecting two processes.

**How it works:**
Three steps happen using two pipes. The first process sends numbers 0–9 into pipe 1. The second process reads from pipe 1, squares each number, and sends the result into pipe 2. The main process reads from pipe 2 and prints the squared values.

**Result:**
```
0
1
4
9
16
25
36
49
64
81
End
```

**Advantages:**
- Fast and direct communication between exactly two processes.
- Useful for creating data pipelines where output of one process feeds into another.

**Disadvantages:**
- Pipes connect only two endpoints — not suitable for communication between many processes.
- Requires careful management of which end to close to avoid blocking.

**Where it can be used:**
- Data pipelines, stream processing, or chained computations where one process feeds data to another.

---

### 12. `communicating_with_queue.py` — Inter-Process Communication with Queue

**Description:**
This file shows how a producer process and a consumer process communicate through a `multiprocessing.Queue` — a thread and process safe FIFO data structure.

**How it works:**
The producer generates 10 random numbers and puts them into the queue one per second, printing the queue size each time. The consumer checks the queue and pops items one by one until the queue is empty.

**Result:**
```
Process Producer : item 183 appended to queue producer-1
The size of queue is 1
Process Consumer : item 183 popped from by consumer-1
...
the queue is empty
```

**Advantages:**
- Safe for use with multiple processes — no manual locking needed.
- More flexible than pipes — supports multiple producers and consumers.

**Disadvantages:**
- The consumer checks `queue.empty()` which can be unreliable in fast multi-process scenarios — items may be added between the check and the get.

**Where it can be used:**
- Task queues, job schedulers, download managers, or any system with multiple producers and consumers running as separate processes.

---

## Applications

| Concept | Real-World Use |
|---|---|
| Spawning Processes | Batch jobs, parallel data processing |
| Naming Processes | Logging, debugging, process monitoring |
| Process Subclass | Reusable custom process logic |
| Killing Processes | Timeout handling, task cancellation |
| Daemon Processes | Background services, monitoring agents |
| Process Barrier | Phased parallel computation |
| Process Pool | Image processing, ML data pipelines |
| Pipe Communication | Stream processing, chained computations |
| Queue Communication | Task queues, job schedulers |

---

## About

Each program in this repository demonstrates a specific concept from Python's `multiprocessing` module. Together they cover the full range of process-based parallelism techniques — from basic process creation and lifecycle management to inter-process communication and pool-based parallel execution.