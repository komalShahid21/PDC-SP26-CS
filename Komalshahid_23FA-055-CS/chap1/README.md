# Python Basics — Chapter 1

This repository covers the core building blocks of Python programming. Each program focuses on a specific concept — from working with classes and data structures to reading and writing files, controlling program flow with loops, and running tasks using serial, multithreading, and multiprocessing approaches. The programs also compare execution times to show how different methods perform on the same task.

## Program Descriptions

### 1. `classes.py` — Object-Oriented Programming (Classes)

**Description:**
This file demonstrates how to create a class in Python, create objects from it, and use inheritance. It also shows how class variables (shared across all objects) behave differently from instance variables.

**How it works:**
The program defines a class called `Myclass` with a shared variable `common` and a method. Two objects are created from the class and the effect of changing `common` on both objects is observed. A second class `AnotherClass` inherits from `Myclass`, showing how inheritance works.

**Result:**
Prints the values of `myvariable` and `common` as they change, showing how class and instance variables behave differently.

**Advantages:**
- Demonstrates code reuse through classes and inheritance.
- Clear and easy to follow for understanding OOP basics.

**Disadvantages:**
- Only covers simple OOP concepts — real-world classes can be much more complex.

**Where it can be used:**
- Building applications like student management systems, bank accounts, or any program that models real-world objects.

---

### 2. `dir.py` — If/Else and For Loop Basics

**Description:**
This file shows how to use `if/elif/else` to check whether a number is positive, negative, or zero, and how to use a `for` loop to calculate the sum of numbers in a list.

**How it works:**
The program checks the value of `num` and prints the appropriate message. It then iterates through a list of numbers, adds them all up, and prints the total sum.

**Result:**
Prints `"Positive number"` and then `"The sum is 83"`.

**Advantages:**
- Simple and easy to read.
- Good introduction to decision-making and iteration in Python.

**Disadvantages:**
- Values are hardcoded — cannot be changed without editing the file.

**Where it can be used:**
- Grade checkers, budget calculators, or any program that needs to make decisions based on numbers.

---

### 3. `do_something.py` — Helper Function (Random Number Generator)

**Description:**
This file defines a reusable helper function that is imported by other programs in this repository. It uses Python's `random` module to generate random decimal numbers and store them in a list.

**How it works:**
The function `do_something` takes a count and a list as arguments, then fills the list with that many random numbers using `random.random()`. It is used by `serial_test.py`, `multithreading_test.py`, and `multiprocessing_test.py`.

**Advantages:**
- Keeps the code clean by separating reusable logic into its own file.
- Makes it easy to test the same task across serial, threaded, and multiprocess approaches.

**Disadvantages:**
- Does nothing on its own — only works when imported by another program.

**Where it can be used:**
- Any program that needs to generate and store large amounts of random data.

---

### 4. `file.py` — Reading and Writing Files

**Description:**
This file shows how to create a text file, write content into it, and then read that content back — covering the basic file input/output operations in Python.

**How it works:**
The program opens (or creates) a file called `test.txt` in write mode, writes two lines into it, and closes it. It then reopens the file in read mode, reads all the content, and prints it.

**Result:**
```
first line of file
second line of file
```

**Advantages:**
- Simple and clear introduction to file handling in Python.
- Shows how data can be saved to and loaded from a file.

**Disadvantages:**
- Every time it runs, it overwrites the file (uses `'w'` mode).
- No error handling if the file cannot be opened.

**Where it can be used:**
- Saving user data, writing log files, generating reports, or any program that needs to store information between runs.

---

### 5. `flow.py` — If, For, and While Loops

**Description:**
This file covers three fundamental control flow tools in Python: `if/elif/else` for making decisions, `for` loops for iterating over a list, and `while` loops for repeating a block of code until a condition is met.

**How it works:**
- The `if` block checks whether `num` is positive, zero, or negative.
- The `for` loop sums all numbers in a list.
- The `while` loop adds natural numbers from 1 to 10.

**Result:**
```
Positive number
The sum is 83
The sum is 55
```

**Advantages:**
- Covers three important concepts in one file.
- Easy to read and understand.

**Disadvantages:**
- All values are hardcoded — no user input involved.

**Where it can be used:**
- Calculators, data filtering, automation scripts, or any program that needs to repeat tasks or make decisions.

---

### 6. `lists.py` — Lists, Dictionaries, and Tuples

**Description:**
This file demonstrates Python's three main data structures: lists (ordered and changeable), dictionaries (key-value pairs), and tuples (ordered and unchangeable). It shows how to create, access, and update each one.

**How it works:**
The program creates a list and modifies its values, creates a dictionary and updates one of its entries, creates a tuple, and uses the built-in `len` function to get the size of the list.

**Result:**
Prints updated list values, a dictionary value, the tuple contents, and the length of the list.

**Advantages:**
- Shows multiple data structures side by side for easy comparison.

**Disadvantages:**
- No printed labels — you need to read the code to understand what each output represents.

**Where it can be used:**
- Almost every Python program uses lists and dictionaries for storing user data, settings, records, and more.

---

### 7. `serial_test.py` — Serial Execution

**Description:**
This file runs a task 10 times in a row, one after another, and measures the total time taken. It serves as the baseline for comparing performance against threading and multiprocessing.

**How it works:**
The program calls `do_something` 10 times sequentially, each time filling a list with 10 million random numbers. The start and end times are recorded using Python's `time` module.

**Result:**
```
List processing complete.
serial time= 18.5 seconds
```
*(Time varies depending on the machine.)*

**Advantages:**
- Simple to follow — tasks run in a predictable order.
- Good baseline to compare with threading and multiprocessing.

**Disadvantages:**
- Slow — must finish each task before starting the next.

**Where it can be used:**
- Scripts where tasks must run in a specific order, or where speed is not a concern.

---

### 8. `multithreading_test.py` — Multithreading

**Description:**
This file runs the same task using 10 threads simultaneously and measures how long it takes, showing how multithreading works in Python.

**How it works:**
The program creates 10 threads, each calling `do_something` to fill a list with 10 million random numbers. All threads are started together and the program waits for all of them to finish using `.join()`.

**Result:**
```
List processing complete.
multithreading time= X seconds
```

**Advantages:**
- Good for tasks that involve a lot of waiting (like reading files or downloading data).
- Threads share memory so they are lighter than processes.

**Disadvantages:**
- Due to Python's GIL (Global Interpreter Lock), threads do not truly run in parallel for CPU-heavy tasks — so speed improvement over serial may be minimal.

**Where it can be used:**
- Web servers, file downloads, reading/writing multiple files simultaneously.

---

### 9. `multiprocessing_test.py` — Multiprocessing

**Description:**
This file runs the same task using 10 separate processes in true parallel and measures the execution time, showing how multiprocessing differs from threading.

**How it works:**
The program creates 10 independent processes, each running `do_something` with 10 million items. Each process has its own memory space and runs on a separate CPU core.

**Result:**
```
List processing complete.
multiprocesses time= X seconds
```
Usually faster than serial execution for CPU-heavy tasks.

**Advantages:**
- True parallelism — each process runs on a separate CPU core.
- Not limited by Python's GIL, making it better for CPU-intensive tasks.

**Disadvantages:**
- Uses more memory than threading since each process has its own copy of data.
- Slightly more complex to set up and debug.

**Where it can be used:**
- Data processing, machine learning, scientific computing, image processing.

---

### 10. `thread_and_processes.py` — Threading vs Multiprocessing Comparison

**Description:**
This file runs the same task using both multithreading and multiprocessing in a single program, printing the time taken for each so the performance difference can be directly compared.

**How it works:**
The program first runs the task using 10 threads, then runs it again using 10 processes. The time for each approach is printed separately. A serial version is also included in the code but commented out.

**Result:**
Prints threading time and processes time. Multiprocessing is generally faster for this type of CPU-heavy task.

**Advantages:**
- Allows direct comparison of two concurrency approaches in one place.
- Useful for understanding which method suits which type of task.

**Disadvantages:**
- Runs a heavy workload (10 million items x 10 workers), so execution takes time.
- Mixing two approaches in one file can be harder to follow for beginners.

**Where it can be used:**
- Performance testing and benchmarking Python programs.

---

## Applications

| Concept | Real-World Use |
|---|---|
| Classes & Inheritance | Web apps, games, desktop software |
| File Handling | Log systems, data storage, config files |
| Loops & Conditions | Calculators, data filtering, automation |
| Lists & Dictionaries | Storing records, settings, user data |
| Multithreading | Web servers, downloading, I/O tasks |
| Multiprocessing | Data science, AI/ML, image processing |

## About

Each program in this repository demonstrates a specific Python concept. The serial, threading, and multiprocessing programs run the same task in three different ways and print the execution time, making it easy to compare performance across approaches.