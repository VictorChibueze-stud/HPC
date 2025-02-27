
# Chapter 2: Advanced Multithreaded Programming Using OpenMP

## 2.1 Multithreaded Programming (Using OpenMP) in Context

This section provides a high-level view of **Multithreaded Programming** within the broader landscape of High Performance Computing (HPC). We introduce the motivation for parallelization, explain the role of shared-memory systems, and highlight why OpenMP (Open Multiprocessing) is a widely adopted API for programming multicore and multi-CPU architectures.

---

### 2.1.1 Parallelization + Programming

**Key Concept**: *Parallelization* involves analyzing large or computationally heavy sequential programs to identify independent (or partially independent) tasks that can be executed simultaneously. Once opportunities for concurrency are found, the program is *restructured* to exploit parallel hardware efficiently.  

1. **Parallel Program**  
   A parallel program can be viewed as the result of:
   1. **Parallelization**: The process of *discovering* and *exposing* parallelism within an application or algorithm.  
   2. **Programming**: The *implementation* or *coding* step, where we use parallel programming models and APIs (such as OpenMP) to realize the parallelization on a given hardware architecture.

2. **Sequential vs. Parallel**  
   - *Sequential Program*: Runs on a single processing unit executing one instruction after another in strict order.  
   - *Parallel Program*: Distributes work across multiple processing units (threads, cores, processors, or even nodes), potentially allowing portions of the code to run concurrently.

3. **Motivation**  
   - **Speedup**: Achieve results faster by distributing tasks.  
   - **Efficiency**: Utilize available hardware resources effectively (e.g., multicore CPUs).  
   - **Scalability**: Accommodate larger problems or datasets by adding more processors/cores.

**Formal Definition**  
In HPC terms, *parallelization* is often framed in a *task scheduling* or *data decomposition* perspective, where:
\[
\text{Parallel Program} = \text{Parallelization} + \text{(Parallel) Programming}.
\]
A general HPC workflow may include analyzing the application, partitioning the data or workload, mapping or scheduling those partitions to hardware threads/cores, then deploying and executing.

**Example**  
- A large matrix–vector multiplication:  
  1. *Identify Parallelism:* Each row of the matrix can be processed independently.  
  2. *Restructure:* Use OpenMP to spawn multiple threads, each computing part of the product.  

**Intuition**  
Parallelization is crucial to fully utilize today’s *multicore and manycore* hardware. Most modern CPUs have multiple cores, and supercomputers contain thousands (or millions) of cores. Leveraging concurrency leads to significant performance gains.

**Summary**  
- Parallelization systematically breaks large jobs into concurrently executable parts.  
- Parallel programming then refines and encodes these parts into parallel APIs or frameworks like OpenMP.

---

### 2.1.2 From Application to Parallel Execution: A Holistic Perspective

OpenMP-based multithreading fits into a broader HPC workflow:

1. **Application / Algorithm**  
   - The starting point (e.g., a numerical simulation, data analysis, or scientific computation).

2. **Software Partitioning & Parallelization**  
   - Break the application into *units of computation* (tasks or data chunks).  
   - Decide on concurrency strategy (e.g., data parallelism, task parallelism).

3. **Deployment**  
   - Map these computational units to hardware resources (threads, cores, processors).  
   - Optimize the mapping or scheduling for load balance and efficient resource usage.

4. **Execution & Results**  
   - Compile the parallel code.  
   - Run on shared-memory systems, distributed-memory clusters, or hybrid architectures.  
   - Gather and validate the results.

Here is a concise depiction often used in HPC:

- **Application** → **Algorithm** → **Software** → **Hardware** → **Execution** → **Results**

Each arrow may involve *compilation*, *resource allocation*, *scheduling*, and *optimization* steps for performance.

---

### 2.1.3 Why Learn Multithreaded Programming?

Modern supercomputers and even desktop machines rely heavily on *multicore* (and often manycore) architectures. The **TOP500** supercomputer list trends show:
- Steady growth in core count per node.
- Necessity for exploiting shared-memory parallelism within each node, beyond just large-scale distributed computing.

**Key Drivers**:
- **Performance**: Exploit all CPU cores for faster computation.  
- **Portability**: OpenMP is supported on a wide range of compilers and architectures.  
- **Scalability**: Enables stepping up from a single multicore CPU to large NUMA nodes and beyond.

---

### 2.1.4 Parallel Computers and Their Programming

Multithreaded programming takes place on machines that can vary widely in memory models and network topologies. Four often-discussed *machine models*:

1. **Shared-Memory (Symmetric Multiprocessing, SMP)**  
   - All processors share the same physical memory.  
   - Thread-based programming (OpenMP) is *natural* here.

2. **Distributed-Memory (Asymmetric Multiprocessing)**  
   - Each processor has local memory; data exchange uses message passing (e.g., MPI).  
   - Different address spaces require explicit communication.

3. **Hybrid Distributed Shared-Memory**  
   - Multiple nodes each containing shared-memory subsystems.  
   - Nodes connect via a network, forming a two-level memory hierarchy.

4. **Distributed Shared-Memory**  
   - Conceptually shared but physically distributed memory across nodes (e.g., certain large NUMA or ccNUMA systems).  

**Programming Models** sometimes overlap with these machine models. The OpenMP approach typically fits one of two broad *shared-memory models*:
1. **Shared Memory (without threads)** – less common for HPC.  
2. **Shared Memory (with threads)** – the main target of OpenMP.

Other known models:
- **Message Passing** (MPI),
- **Data Parallel** (e.g., GPU frameworks, OpenACC),
- **Hybrid SPMD** (Single Program, Multiple Data),
- **MPMD** (Multiple Program, Multiple Data).

OpenMP’s advantage is that *it does not strictly mandate* a specific hardware architecture—one can use OpenMP for multi-socket CPUs, NUMA systems, GPUs (through some extensions), and more.

---

### 2.1.5 Why OpenMP? Open Multiprocessing

**Key Concept**: *OpenMP* is an Application Programming Interface (API) that facilitates multithreading on shared-memory architectures (e.g., multicore CPUs).

**Formal Definition**  
OpenMP provides:
1. **Compiler Directives** (pragmas) – You annotate code regions (loops, sections) for parallel execution.  
2. **Runtime Library Routines** – Functions for querying the number of threads, scheduling, locks, etc.  
3. **Environment Variables** – To control default behaviors (e.g., the number of threads).

**Properties**:
- *De Facto Standard* for multicore and SMP programming.  
- Primarily supports **C/C++** and **Fortran**, languages widely used in HPC.  
- Compatible across vendors; typically requires only a `-fopenmp` or equivalent compiler flag.  
- Aims to be *easy to start with*, using incremental parallelization: one can parallelize a few loops or sections at a time.

**Example**:  
A simple OpenMP parallel construct in C/C++:
```c
#pragma omp parallel
{
   // Code here executes in parallel by multiple threads
}
```

**Intuition**  
OpenMP’s **fork–join** model spawns a *team of threads* on encountering a `#pragma omp parallel` directive; threads rejoin into one at the end of that region. This approach maps well to many HPC workloads where certain parts (loops or tasks) are clearly parallelizable.

**Relevance**  
- Shared memory parallelism is integral on each *node* of modern supercomputers.  
- Even for distributed-memory HPC using MPI across nodes, each node may host multiple CPU cores that are best utilized via OpenMP.

---

### 2.1.6 Chapter Structure and Focus

**Chapter 2** of this course focuses on *advanced* aspects of **Multithreaded Programming using OpenMP**. It is subdivided into the following major sections:

1. **Multithreaded Programming (using OpenMP) in Context**  
2. **Worksharing**  
3. **Data Sharing (Variables Scoping)**  
4. **Synchronization**  
5. **(Explicit) Tasks**

> **Disclaimer**: Some slides providing deeper detail on OpenMP constructs are marked "Self-study." These slides are not directly examined but are available for reference, practice, or follow-up questions.

In this current set of notes, we have concentrated on **(1) Multithreaded Programming in Context**: exploring the motivations, backgrounds, and high-level understanding of OpenMP. Subsequent sections will detail the individual constructs (worksharing loops, data scoping, synchronization mechanisms, and explicit tasks).

---

### 2.1.7 End-of-Section Summary

- **Parallelization** involves identifying independent or partially independent operations to run simultaneously.  
- **OpenMP** is a prominent API for shared-memory parallelism:
  - Easy-to-use compiler directives (`#pragma omp`) plus a runtime library.  
  - Broadly supported for C, C++, and Fortran in HPC.  
- **Shared vs. Distributed Memory**:
  - Shared-memory code is simpler to write with OpenMP but requires care with synchronization and data races.  
  - Distributed-memory typically uses MPI with explicit communication. Many HPC applications combine both (hybrid programming).
- **Why Learn It**: Leveraging OpenMP is vital to exploit the full power of modern multicore CPUs found in supercomputers, clusters, and ordinary desktops alike.

Having established the context of OpenMP multithreaded programming, we will next delve into **Section 2.2: Worksharing**, where we examine how to distribute loop iterations and other code regions among threads to gain parallel speedup.

# 2.2 Worksharing

One of OpenMP’s fundamental strengths lies in how *work* (particularly loop iterations and code sections) can be shared among multiple threads. This section focuses on **Worksharing Constructs**, which enable *parallel distribution* of tasks to exploit concurrency and achieve speedup on shared-memory systems.

---

## 2.2.1 Introduction to Worksharing Constructs

**Key Concept**: A *worksharing construct* instructs OpenMP to divide execution of a code region among threads in a team, such that **each thread** performs a *portion of the total work*. Typical worksharing constructs include:

1. **`sections` / `parallel sections`** – Divide different (non-iterative) blocks of code among threads.  
2. **`for` / `parallel for`** – Distribute loop iterations across threads.  
3. **`loop` / `parallel loop`** (introduced in OpenMP 5.0) – A more flexible iteration worksharing construct.  

### Formal Definition

When a *worksharing region* is encountered by an *active team of threads*, OpenMP coordinates the *split* of those instructions (loop iterations or code blocks). For loops, iterations are divided; for sections, discrete code segments are distributed. By default, an **implicit barrier** occurs at the *end* of a worksharing region, ensuring all threads complete their assigned portion before continuing.

---

## 2.2.2 The `sections` Construct

```c
#pragma omp sections [clause[, clause] ...]
{
   #pragma omp section
   structured-block

   #pragma omp section
   structured-block
   ...
}
```

**Definition & Purpose**  
- The **`sections`** construct is *non-iterative*: it deals with separate, *independent blocks* of code rather than loop iterations.  
- Each *`section`* block is executed *once* by *one* thread in the team. The specific mapping of sections to threads is **implementation-defined**.

**Properties**  
- By default, an **implicit barrier** occurs at the end of the `sections` region. Use `nowait` if you want to eliminate this barrier (and if your code is still correct without it).  
- Useful for *functional decomposition*, e.g., when different tasks or functions should execute concurrently.

**Example**

```c
#pragma omp parallel
{
   #pragma omp sections
   {
      #pragma omp section
      {
         // Code block A
      }

      #pragma omp section
      {
         // Code block B
      }
   } // implicit barrier here, unless nowait
}
```

In this example, *two sections* (`A` and `B`) can run in parallel, each handled by *one thread* from the team.

---

## 2.2.3 The `parallel sections` Construct (OpenMP 5.0+)

```c
#pragma omp parallel sections [clause[, clause] ...]
{
   #pragma omp section
   structured-block

   #pragma omp section
   structured-block
   ...
}
```

**Key Concept**  
- **`parallel sections`** is a *shorthand* or *shortcut* for writing a **`parallel`** construct that immediately contains a single `sections` region and no additional statements.  
- It combines the effect of:
  ```c
  #pragma omp parallel
  #pragma omp sections
  {
     ...
  }
  ```
  into one directive.

**Usage**  
This is handy when you know you want to spawn a new team of threads *just* to run separate code blocks in parallel. Instead of writing two pragmas, you merge them into one.

---

## 2.2.4 The Worksharing Loop: `for` / `parallel for`

### 2.2.4.1 Basic `for` Worksharing

```c
#pragma omp for
for (i = 0; i < N; i++) {
   // loop body
}
```

- The **`for`** (or **`do`** in Fortran) worksharing construct *divides the loop iterations* among threads in the *existing* team.  
- *No new threads* are created by `#pragma omp for`; instead, it *must* appear within a `parallel` region or be combined as `#pragma omp parallel for`.  
- An **implicit barrier** is inserted at the end of the loop by default, forcing all threads to finish their assigned iterations before any proceeds further.

#### Example

```c
#pragma omp parallel
{
   #pragma omp for
   for(int i = 0; i < N; i++) {
      // Parallel loop body
   }
   // Implicit barrier here unless nowait is used
}
```

Here, multiple threads created by `#pragma omp parallel` share the iterations from `0` to `N-1`.

---

### 2.2.4.2 `parallel for` Construct

```c
#pragma omp parallel for [clause[, clause] ...]
for (i = 0; i < N; i++) {
   // loop body
}
```

- A *shortcut construct* that *creates a team of threads* and simultaneously *distributes the loop iterations* among them.  
- Often used for *data-parallel loops* where each iteration is *independent* from others.

**Common Clauses**:

1. **`private(list)` / `firstprivate(list)` / `lastprivate(list)`** – Control *variable scoping* for each iteration.  
2. **`reduction(operator : list)`** – Accumulate partial results into a shared variable via a reduction operator.  
3. **`schedule(type[, chunk])`** – *Specifies* how iterations are distributed (scheduled) among threads.

> **Note**: By default, the loop index variable in `for (int i=...)` is treated as `private`, which is a special rule for loop indices.

---

#### The `schedule` Clause

```c
#pragma omp parallel for schedule(type[, chunk])
for (i = 0; i < N; i++) {
   // loop body
}
```

**Key Concept**: *Load Balancing* is crucial for performance. The **`schedule`** clause defines *how* iterations are assigned:

- **`static`**: Iterations are divided into contiguous blocks of size `chunk`.  
  - *Low runtime overhead*, but can be imbalanced if work per iteration varies.
- **`dynamic`**: Threads request blocks of size `chunk` *dynamically*, re-requesting new blocks once they finish.  
  - *Higher overhead*, but can handle variation in iteration workloads.
- **`guided`**: A special case of dynamic that uses decreasing block sizes to reduce scheduling overhead.  
- **`runtime`**: The scheduling choice is *deferred to runtime*, controlled by environment variables (e.g., `OMP_SCHEDULE`).  
- **`auto`**: Allows the compiler/runtime to *automatically* select a scheduling approach.

**Example**

```c
#pragma omp parallel for schedule(dynamic, 10)
for(int i = 0; i < N; i++) {
   // loop body
}
```

In this snippet, iterations come in *chunks of 10*. Once a thread finishes processing its chunk, it obtains the next available set of 10 iterations (until all iterations are distributed).

---

### 2.2.4.3 Collapsing Nested Loops and `ordered` Clause

When loops are **perfectly nested**, you may want to *parallelize multiple nested loops* as a single iteration space. This is where **`collapse(n)`** helps:

```c
#pragma omp parallel for collapse(2)
for (int i = 0; i < N; i++) {
   for (int j = 0; j < M; j++) {
      // 2D nested loop body
   }
}
```

- **`collapse(2)`** merges the two loops into an iteration space of size `N*M`.  
- Useful when `N` is small compared to the number of threads, but `N*M` is sufficiently large for good load balance.

Meanwhile, the **`ordered`** clause enforces an *ordered region* inside a loop. By default, distributing iterations among threads can execute them in *any order*. If you require a section of the loop to run in *sequential* iteration order, add `ordered`. For example:

```c
#pragma omp for ordered
for(int i = 0; i < N; i++) {
   // parallel portion

   #pragma omp ordered
   {
      // section that must be executed in the original loop order
   }
}
```

This is *rare* in typical data-parallel loops but can be valuable for partial ordering or controlling I/O sequences in a loop.

---

## 2.2.5 The `loop` Construct (OpenMP 5.0+)

```c
#pragma omp loop [clause[, clause] ...]
for ( ... ) {
   // loop body
}
```

**Key Concept**: The **`loop`** construct is *another* iterative worksharing construct introduced in OpenMP 5.0, designed to be more *flexible*.  

- It *may allow* the compiler/runtime to choose scheduling automatically, similar to `auto`.  
- *No explicit `schedule` clause is mandatory*, though it can be combined with other clauses.  
- By default, there is an *implicit barrier* at the end of the `loop` region (unless `nowait` is specified).

**Example**  
```c
#pragma omp parallel
{
   #pragma omp loop
   for(int i = 0; i < 10; i++) {
       printf("Hello from thread %d, i=%d\n", omp_get_thread_num(), i);
   }
}
```

In this example, multiple threads share the loop from `i=0` to `i=9`. The runtime determines how to *schedule* these iterations among threads.

---

## 2.2.6 The `parallel loop` Construct (OpenMP 5.0+)

Similar to `parallel for`, **`parallel loop`** fuses a `parallel` region with a `loop` region into a single directive:

```c
#pragma omp parallel loop [clause[, clause] ...]
for ( ... ) {
   // loop body
}
```

- Creates a team of threads and *immediately* distributes the iterations of the loop among them.  
- Equivalent to:
  ```c
  #pragma omp parallel
  #pragma omp loop
  for ( ... ) { ... }
  ```

**Benefits**:
- More direct, less verbose.  
- Potentially less overhead than writing separate constructs, depending on implementation.

---

## 2.2.7 Example: Using `loop`

**Code**  
```c
#include <stdio.h>
#include <omp.h>

int main() {
   #pragma omp parallel
   {
      #pragma omp loop
      for(int i = 0; i < 10; i++) {
         printf("Hello world from thread %d, i = %d\n",
                omp_get_thread_num(), i);
      }
   }
   return 0;
}
```

When run on a machine with, say, 4 or 8 or 20 threads, the iterations (`i = 0..9`) get distributed in an *unspecified order* among available threads. **The output** depends on the runtime’s distribution strategy and can interleave lines from different threads.

- By default, there is an **implicit barrier** at the end of the `loop`.  
- Threads can safely exit the parallel region only after all iterations have completed.

**Intuition**  
- The `loop` construct offers more flexibility and is particularly helpful when you simply want concurrency without specifying a particular scheduling approach.  
- For advanced load-balancing control, `parallel for` with a `schedule` clause may still be preferred.

---

## 2.2.8 End-of-Section Summary

1. **`sections` / `parallel sections`**  
   - Non-iterative constructs that split independent code blocks across threads.

2. **`for` / `parallel for`**  
   - Iterative constructs that distribute loop iterations.  
   - *Schedule choices* are vital for performance.  
   - Supports `collapse`, `ordered`, `reduction`, and various scoping clauses.

3. **`loop` / `parallel loop`** (OpenMP 5.0)  
   - Alternative iterative worksharing constructs that can offer simpler syntax and more runtime flexibility.  
   - Still provide an implicit synchronization barrier unless `nowait` is used.

4. **Implicit Barrier**  
   - By default, each worksharing region concludes with a barrier—*all threads must finish their respective portion of the work before any continues*.

5. **Performance Considerations**  
   - Overuse of barriers or poor scheduling can degrade performance.  
   - The granularity of parallel tasks or loop chunks can significantly affect speedup.

Overall, **worksharing** in OpenMP is a straightforward way to distribute work among threads. Mastering these constructs lays the groundwork for effectively parallelizing *common loop patterns* and distinct code segments, yielding significant performance gains on shared-memory architectures.


# 2.3 Data Sharing (Variables Scoping)

This section examines how OpenMP handles *data sharing* among threads. In particular, it focuses on the **scoping** of variables—that is, which variables are available to each thread and how new copies or shared copies of those variables are created. Proper data scoping avoids race conditions, clarifies code intent, and improves maintainability.

---

## 2.3.1 Data Sharing Variable Scoping Using Attribute Clauses

**Key Concept**: In OpenMP, *variable scoping* determines whether each thread sees its own *private* copy of a variable or whether threads share a single instance in memory. To declare these scoping rules, OpenMP uses **attribute clauses** such as `private`, `firstprivate`, `lastprivate`, `shared`, `threadprivate`, and others. These clauses apply to both **parallel** and **worksharing** constructs.

**Formal Definition**. Under a *parallel region* or a *worksharing region*, each variable can be placed in:
- A single shared storage location accessible to all threads (so that *writes* and *reads* can be visible across threads).
- A private storage location, giving each thread its own copy, typically uninitialized or specifically initialized.

**Example**. Consider a `parallel for` loop. By default, loop index variables are private to each thread, while most other global or file-scope variables are shared. If the programmer needs a different arrangement, attribute clauses override these defaults.

**Intuition**. Data sharing rules help *avoid data races*, which occur when multiple threads write to (or read and write) the same variable simultaneously without synchronization. By carefully choosing which variables are private or shared, programmers can eliminate unintended concurrent writes or preserve results that must be globally visible.

**Properties**. OpenMP default scoping rules in C/C++ state that:
- Global variables and static variables are **shared**.
- The loop iteration variable in a `for` is **private** by default.
- Other automatic variables (local to a function) typically become **shared** unless specified otherwise (or unless `default(none)` is used, forcing explicit scoping).

---

## 2.3.2 Data Sharing Default Data Storage Attributes

OpenMP imposes a set of *default rules* for variable storage attributes to simplify code writing. These defaults govern how variables are treated when a parallel region is encountered, unless the programmer explicitly changes these attributes with clauses.

In C/C++:
A global array such as `double A[100];` is shared among all threads if used inside a parallel region. Local static variables or global file-scope variables also end up shared. However, certain special variables—like a loop index—become private to each thread. If a programmer needs a stricter or different behavior, they can declare `default(none)` in the parallel directive to force explicit listing of `private`, `shared`, etc., for each variable.

An illustrative code excerpt is:

```c
double A[100];
int main() {
   int index[50];
   #pragma omp parallel
   {
      // By default, A and index are shared
      // Loop index variable in a for-loop is private
   }
   return 0;
}
```

In this example, `A` and `index` remain single, shared storage locations accessible by all threads. If a thread modifies `index`, every other thread sees this updated array. The default behavior can be overridden by specifying data-sharing clauses.

---

## 2.3.3 Data Sharing Changing Data Storage Attribute with Clauses

### Private Clause

OpenMP provides an attribute clause `private(list)` that creates new, uninitialized *private copies* of the listed variables for each thread. Once the parallel or worksharing region ends, those copies are lost and do not update any shared version outside that region.

For instance:

```c
int tmp;
#pragma omp parallel for private(tmp)
for(int j=0; j<1000; j++) {
   tmp += j;
}
printf("%d\n", tmp);
```

In this snippet, each thread obtains its own copy of `tmp`. Because these copies are uninitialized inside the parallel region, their initial values are unspecified. They also do not affect the original `tmp` declared outside. Consequently, at the end, `tmp` in the main thread remains unchanged. This approach prevents race conditions but can sometimes lead to incorrect behavior if the programmer expects `tmp` to accumulate a global sum.

### Firstprivate Clause

The `firstprivate(list)` clause is similar to `private` but initializes each thread’s private copy to the value that the variable had prior to entering the parallel or worksharing region. An example:

```c
int tmp = 0;
#pragma omp parallel for firstprivate(tmp)
for (int j = 0; j < 1000; j++) {
   tmp += j;
}
printf("%d\n", tmp);
```

Although each thread starts with `tmp` initialized to 0 (copied from the master thread’s value), changes within each thread remain local and are not aggregated into the global variable once the loop ends. Hence, outside the loop, the master thread’s `tmp` is still 0.

### Lastprivate Clause

The `lastprivate(list)` clause creates private copies for each thread but then *copies the value* from the **last iteration** in the sequential order back to the global variable after the loop finishes. In a for-loop:

```c
int tmp = 0;
#pragma omp parallel for lastprivate(tmp)
for (int j = 0; j < 5; j++) {
   tmp += j;
}
printf("%d\n", tmp);
```

Inside the loop, each thread has its own `tmp`. Once all threads complete, the value produced by the logically last iteration (in this case, `j = 4` in normal ascending order) is assigned to `tmp` in the master thread. Thus, `tmp` will end up with the final iteration’s private copy.

### Combining Firstprivate and Lastprivate

It is possible to declare a variable to be both firstprivate and lastprivate. In that case, each thread’s copy is initialized to the pre-parallel value, and the final iteration’s private value is transferred back to the original variable.

### Threadprivate Clause

When a global or file-scope variable is declared with `threadprivate`, OpenMP *replicates* that variable, so each thread has its own personal copy (preserved across multiple parallel regions if the same threads are reused).

For example:

```c
int globalCounter = 0;
#pragma omp threadprivate(globalCounter)

void incrementCounter() {
   globalCounter++;
}
```

Every thread has a separate `globalCounter`. When a given thread calls `incrementCounter()`, only that thread’s counter increments. Unlike a simple `private` variable, a `threadprivate` variable can *retain* its value across **different parallel regions** if the same thread team is active.

### Copyin Clause

If a `threadprivate` variable must be *initialized* in all threads to the same starting value, use `copyin`. It copies the master thread’s value of the threadprivate variable into the same threadprivate variable of every other thread in the team.

```c
int globalArr[100];
#pragma omp threadprivate(globalArr)

int main() {
   for(int i = 0; i < 100; i++) {
      globalArr[i] = i;
   }
   #pragma omp parallel copyin(globalArr)
   {
      // all threads see a threadprivate globalArr
      // initialized from the master’s contents
   }
   return 0;
}
```

### Copyprivate Clause

The `copyprivate` clause appears on a **single** construct and broadcasts the value of a private variable from the single thread that executes the single block to all other threads at the next barrier. This pattern is often used for a producer–consumer model where only one thread reads input or computes some data, and the result must be shared with every other thread.

```c
int Nsize, choice;
#pragma omp parallel private(Nsize, choice)
{
   #pragma omp single copyprivate(Nsize, choice)
   {
      // Only one thread reads these values
      input_parameters(&Nsize, &choice);
   }
   // After implicit barrier, every thread now has
   // the same Nsize and choice that the single thread read
   do_work(Nsize, choice);
}
```

One thread calls `input_parameters`, and its private copies of `Nsize` and `choice` are then broadcast to the other threads at the barrier that terminates the single region.

---

## 2.3.4 Data Sharing Variable Scoping: Example (1/4)

Consider this code:

```c
int tmp = 100;
#pragma omp parallel for
for (int j = 0; j < 5; j++) {
   tmp += j;
}
printf("temp value %d\n", tmp);
```

Without additional clauses, `tmp` is shared by all threads, creating a race condition. Different threads increment `tmp` concurrently, leading to unpredictable results. Using the default approach might produce partial sums that vary from run to run.

---

## 2.3.5 Data Sharing Variable Scoping: Example (2/4)

If `private(tmp)` is declared:

```c
int tmp = 100;
#pragma omp parallel for private(tmp)
for (int j = 0; j < 5; j++) {
   tmp += j;
}
printf("temp value %d\n", tmp);
```

Each thread has its own new, uninitialized `tmp` in the for-loop. Due to no initial value guarantee, each private copy of `tmp` starts with an undefined value (in older OpenMP specifications) or zero-initialized in certain compilers. In either case, the modifications do not affect the global `tmp` outside the loop. After completion, `tmp` in the master thread remains 100.

---

## 2.3.6 Data Sharing Variable Scoping: Example (3/4)

If `firstprivate(tmp)` is declared instead:

```c
int tmp = 100;
#pragma omp parallel for firstprivate(tmp)
for (int j = 0; j < 5; j++) {
   tmp += j;
}
printf("temp value %d\n", tmp);
```

Each thread’s private `tmp` is initialized to 100. The sums each thread computes stay in separate copies, so the global `tmp` outside remains 100 after the loop finishes. Threads never combine their partial results.

---

## 2.3.7 Data Sharing Variable Scoping: Example (4/4)

Finally, if `lastprivate(tmp)` is declared:

```c
int tmp = 100;
#pragma omp parallel for lastprivate(tmp)
for (int j = 0; j < 5; j++) {
   tmp += j;
}
printf("temp value %d\n", tmp);
```

All threads have their private version of `tmp`, likely uninitialized at first. Whichever thread executes the final iteration (in a strict ascending sense) writes its `tmp` value back to the shared `tmp`. Consequently, `tmp` in the master thread picks up the value from the iteration `j = 4`. If each thread’s private copy started at an indeterminate state, this can lead to confusion, but logically it means that the final iteration’s private copy is assigned to the global `tmp`.

If `firstprivate(tmp) lastprivate(tmp)` are combined, then each thread’s private `tmp` starts at 100, and the global `tmp` is updated from the final iteration’s private copy once the loop completes.

---

## Summary of Data Sharing (Variables Scoping)

Data scoping in OpenMP defines how variables are mapped to threads. The choice among `private`, `firstprivate`, `lastprivate`, `threadprivate`, or `shared` has a profound effect on code correctness and semantics. Properly chosen scoping clauses:
- Avoid unintended data races.
- Determine whether loop variables or global variables are replicated.
- Allow flexible communication patterns (e.g., `copyprivate` for broadcast).

Understanding these clauses is vital for writing robust, maintainable parallel programs. Each clause targets a specific scenario—some unify final results, others provide necessary local copies, and others preserve across multiple parallel regions. By systematically applying variable scoping rules, OpenMP programs remain clearer, safer, and more performance-focused.

