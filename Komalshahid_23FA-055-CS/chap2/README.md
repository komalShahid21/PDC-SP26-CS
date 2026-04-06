# Python Threading — Chapter 2

This repository covers Python's threading module in depth. Each program demonstrates a specific threading concept — from creating basic threads and using locks, to advanced synchronization techniques like semaphores, events, conditions, barriers, and queues. The programs show how threads communicate, share resources safely, and coordinate with each other.

## Program Descriptions

### 1. `Thread_definition.py` — Basic Thread Creation

**Description:**
This file shows the simplest way to create and run multiple threads in Python using the `threading` module. It defines a function and runs it across 10 threads one at a time.

**How it works:**
A function `my_func` is defined that prints the thread number. A loop creates 10 threads, each calling `my_func` with its index. Each thread is started and immediately joined before the next one begins.

**Result:**
```
my_func called by thread N°0
my_func called by thread N°1
...
my_func called by thread N°9
```

**Advantages:**
- Very simple introduction to the `threading` module.
- Easy to see how threads are created, started, and joined.

**Disadvantages:**
- Threads run one at a time (joined immediately after starting), so there is no actual parallel execution.

**Where it can be used:**
- Any program that needs to run a function in a separate thread, such as background tasks or parallel processing.

---

### 2. `Thread_determine.py` — Naming and Identifying Threads

**Description:**
This file shows how to assign names to threads and identify which thread is running at any point using `threading.currentThread().getName()`.

**How it works:**
Three functions (`function_A`, `function_B`, `function_C`) are defined. Each prints its thread name when it starts and when it exits, with a 2-second sleep in between. Three threads are created with custom names and run in parallel.

**Result:**
```
function_A --> starting
function_B --> starting
function_C --> starting
function_A --> exiting
function_B --> exiting
function_C --> exiting
```
(Order may vary since threads run in parallel.)

**Advantages:**
- Shows how to track which thread is doing what using names.
- Useful for debugging multi-threaded programs.

**Disadvantages:**
- Only demonstrates naming and identification — no data sharing or synchronization between threads.

**Where it can be used:**
- Logging systems, debugging tools, or any application where tracking thread activity is important.

---

### 3. `Thread_name_and_processes.py` — Thread Names and Process ID

**Description:**
This file demonstrates how to create threads using a custom thread class and print the process ID that each thread belongs to.

**How it works:**
A class `MyThreadClass` extends `Thread`. Each thread prints the name it was given along with the process ID of the running process. Two threads are created, started, and joined.

**Result:**
```
ID of process running Thread#1
ID of process running Thread#2
End
```

**Advantages:**
- Shows how threads belong to the same process (same process ID).
- Introduces the concept of subclassing `Thread` for custom behavior.

**Disadvantages:**
- Very basic — only prints information without performing any real task.

**Where it can be used:**
- Understanding the relationship between threads and processes in Python.

---

### 4. `MyThreadClass.py` — Custom Thread Class

**Description:**
This file creates a custom thread class where each thread has a name and a random sleep duration. It runs 9 threads simultaneously and measures the total execution time.

**How it works:**
`MyThreadClass` extends `Thread` and accepts a name and duration. Each thread prints when it starts and when it finishes. Nine threads are created with random sleep durations (1–10 seconds), all started together, then joined.

**Result:**
```
---> Thread#1 running, belonging to process ID 12345
---> Thread#2 running, belonging to process ID 12345
...
---> Thread#1 over
--- X seconds ---
```

**Advantages:**
- Shows how to build reusable thread classes with custom parameters.
- Demonstrates true parallel execution — all threads run at the same time.

**Disadvantages:**
- Sleep durations are random, so execution time varies each run.

**Where it can be used:**
- Simulating parallel tasks like downloading multiple files or handling multiple user requests.

---

### 5. `MyThreadClass_lock.py` — Threads with Lock (Serial Execution)

**Description:**
This file adds a `Lock` to the thread class so that only one thread can run at a time. The lock is acquired at the start of the thread and released only after it finishes sleeping — forcing threads to run one after another.

**How it works:**
A `threading.Lock()` is created globally. In the `run` method, each thread acquires the lock before printing and sleeping, then releases it after finishing. This means the next thread can only start after the current one is completely done.

**Result:**
Threads run one at a time in order. Total time is the sum of all sleep durations.

**Advantages:**
- Shows how to prevent multiple threads from accessing shared resources at the same time.
- Useful for understanding thread safety.

**Disadvantages:**
- Defeats the purpose of multithreading — threads end up running serially, making it slower.

**Where it can be used:**
- Situations where a shared resource (like a file or database) can only be accessed by one thread at a time.

---

### 6. `MyThreadClass_lock_2.py` — Threads with Lock (Parallel Execution)

**Description:**
This file improves on `MyThreadClass_lock.py` by releasing the lock immediately after printing, so the sleep happens outside the lock. This allows threads to sleep in parallel while still protecting the print statement.

**How it works:**
The lock is acquired and released quickly (only around the print statement). The sleep runs after the lock is released, allowing other threads to print and then sleep at the same time.

**Result:**
All threads print quickly one after another, then sleep in parallel. Total time is much shorter than `MyThreadClass_lock.py`.

**Advantages:**
- Better use of locks — only the critical section (print) is protected, not the entire task.
- Threads run faster since they sleep in parallel.

**Disadvantages:**
- Requires careful thought about which parts of code actually need to be locked.

**Where it can be used:**
- Any scenario where only a specific part of a thread's work needs to be protected, not the entire operation.

---

### 7. `Rlock.py` — Reentrant Lock (RLock)

**Description:**
This file demonstrates `threading.RLock()` (Reentrant Lock), which allows the same thread to acquire the same lock multiple times without getting stuck (deadlock).

**How it works:**
A `Box` class uses an `RLock`. The `add` and `remove` methods both call `execute`, which also acquires the same lock. Since it is an `RLock`, the same thread can lock it multiple times safely. Two threads — one adding items and one removing items — run at the same time.

**Result:**
```
N° X items to ADD
N° X items to REMOVE
ADDED one item --> X item to ADD
REMOVED one item --> X item to REMOVE
...
```

**Advantages:**
- Prevents deadlocks when a thread needs to acquire the same lock more than once.
- Useful when methods call other methods that also use locks.

**Disadvantages:**
- Slightly more complex than a regular lock.
- Can hide design issues if overused.

**Where it can be used:**
- Recursive functions that need locking, or class methods that call each other while holding a lock.

---

### 8. `Semaphore.py` — Semaphore for Thread Signaling

**Description:**
This file demonstrates `threading.Semaphore`, which is used to control access between a producer and a consumer. The consumer waits until the producer signals that an item is ready.

**How it works:**
A semaphore is initialized to 0. The consumer calls `semaphore.acquire()` and blocks until the producer calls `semaphore.release()` after producing an item. This runs 10 times in a loop.

**Result:**
```
Consumer is waiting
Producer notify: item number 742
Consumer notify: item number 742
...
```

**Advantages:**
- Simple and effective way to signal between threads.
- Ensures the consumer never tries to read an item before it is produced.

**Disadvantages:**
- Only works for simple signaling — not suitable for complex coordination between many threads.

**Where it can be used:**
- Producer-consumer patterns, rate limiting, or controlling access to a limited number of resources.

---

### 9. `Condition.py` — Condition Variable for Producer-Consumer

**Description:**
This file uses `threading.Condition` to coordinate a producer and consumer thread. The producer adds items to a list and the consumer removes them, with both waiting when the list is full or empty.

**How it works:**
A shared list `items` and a `Condition` object are used. The producer waits if the list has 10 items and notifies the consumer after adding. The consumer waits if the list is empty and notifies the producer after removing. Both run 20 times.

**Result:**
Timestamped log messages showing items being produced and consumed, with threads waiting when the list is full or empty.

**Advantages:**
- Provides fine-grained control over thread coordination.
- Prevents overproduction and underproduction efficiently.

**Disadvantages:**
- More complex than a semaphore — requires careful use of `wait()` and `notify()`.

**Where it can be used:**
- Task queues, data pipelines, or any system where one thread produces data and another consumes it at a different speed.

---

### 10. `Event.py` — Event-Based Thread Communication

**Description:**
This file uses `threading.Event` to signal between a producer and consumer. The producer sets an event each time it adds an item, and the consumer waits for the event before reading.

**How it works:**
An `Event` object is shared between the producer and consumer. The producer appends a random item to the list, sets the event, and immediately clears it. The consumer waits for the event to be set before popping and logging the item.

**Result:**
Timestamped log messages showing items being appended by the producer and popped by the consumer.

**Advantages:**
- Simple one-way signaling between threads.
- Easy to understand and implement.

**Disadvantages:**
- Less control than `Condition` — only supports a simple set/clear/wait pattern.
- Race conditions can occur if the event is cleared before the consumer reacts.

**Where it can be used:**
- Notifying threads about state changes, triggering background tasks, or simple producer-consumer setups.

---

### 11. `Barrier.py` — Barrier for Thread Synchronization

**Description:**
This file uses `threading.Barrier` to make all threads wait at a specific point until every thread has reached it, then continue together — like a race where everyone waits at the finish line.

**How it works:**
Three runner threads are created, each sleeping for a random time (2–5 seconds) to simulate running. Each thread calls `finish_line.wait()` when it arrives. The program only continues once all three runners have reached the barrier.

**Result:**
```
START RACE!!!!
Louie reached the barrier at: Mon Apr 06 ...
Dewey reached the barrier at: Mon Apr 06 ...
Huey reached the barrier at: Mon Apr 06 ...
Race over!
```

**Advantages:**
- Ensures all threads reach the same point before any of them proceeds.
- Useful for synchronizing parallel tasks that must all complete before the next step.

**Disadvantages:**
- If one thread fails or takes too long, all other threads are blocked waiting.

**Where it can be used:**
- Parallel simulations, batch processing where all workers must finish a phase before starting the next, or multi-player game synchronization.

---

### 12. `Threading_with_queue.py` — Thread-Safe Queue

**Description:**
This file demonstrates the `Queue` class for safe communication between producer and consumer threads. The queue handles all the synchronization internally, making it the cleanest and safest approach for producer-consumer problems.

**How it works:**
A `Producer` thread adds 5 random items to a shared queue, one per second. Three `Consumer` threads continuously get items from the queue using `queue.get()` and call `queue.task_done()` when done. The queue blocks consumers automatically when it is empty.

**Result:**
```
Producer notify: item N°204 appended to queue by Thread-1
Consumer notify: 204 popped from queue by Thread-2
...
```

**Advantages:**
- Thread-safe by design — no need for manual locks.
- Multiple consumers can work in parallel from the same queue.
- Cleanest pattern for producer-consumer problems in Python.

**Disadvantages:**
- Consumers run in an infinite loop — the program does not terminate on its own without additional stop signals.

**Where it can be used:**
- Web scrapers, task schedulers, download managers, or any system where work items are produced and processed by multiple workers.

---

## Applications

| Concept | Real-World Use |
|---|---|
| Basic Threads | Background tasks, parallel processing |
| Thread Naming | Logging, debugging multi-threaded apps |
| Lock | Database writes, file access protection |
| RLock | Recursive methods needing thread safety |
| Semaphore | Rate limiting, resource access control |
| Condition | Data pipelines, task queues |
| Event | State change notifications, triggers |
| Barrier | Parallel simulations, phased computation |
| Queue | Web scrapers, download managers, task workers |


## About

Each program in this repository focuses on a different thread synchronization tool provided by Python's `threading` module. Together they cover the full range of techniques used to coordinate threads safely — from basic locking to advanced patterns like barriers and queues.