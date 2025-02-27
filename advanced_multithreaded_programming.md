
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


# 2.4 Synchronization

This section explores the **synchronization mechanisms** in OpenMP that safeguard *shared data* when multiple threads concurrently access or modify it. In shared-memory programming, synchronization ensures *correctness* and *consistency*. Here, we examine different synchronization constructs and their use cases, with examples illustrating how to prevent race conditions or guarantee ordering.

---

## 2.4.1 Single

**Key Concept**: The **`single`** construct identifies a block of code that only one thread in a team executes, while other threads skip that block. When a thread encounters `single`, it takes ownership of that block’s execution. Afterward, by default, all threads synchronize at an *implicit barrier*, ensuring everyone waits until the single block finishes (unless `nowait` is specified).

A practical scenario might involve reading input, setting parameters, or performing a one-time initialization. Let us formalize:

```c
#pragma omp parallel
#pragma omp single [clause ...]
{
   // Code executed by exactly one thread
}
```

The entry into the `single` region requires no special barrier. Only the chosen thread executes the block. By default, an implicit barrier occurs at the *end*, meaning all threads wait there unless `nowait` is used. This default barrier is essential when all threads need the results from the single thread’s work.

An **example** might have each thread perform computations in parallel, but only one thread prints a debug message or reads input data. The `single` directive ensures exactly one copy of that activity occurs.

The **intuition** is that `single` is a lightweight way to specify *one-thread-only* work inside a parallel region. The property that any thread may execute it—rather than enforcing the master thread—can sometimes be exploited for concurrency if the master is busy doing something else.

---

## 2.4.2 Master and Parallel Master Constructs

**Key Concept**: The **`master`** construct is similar to `single` in that it restricts a code region to one thread. However, it always enforces that the *master thread* of the team is the one executing the block. The other threads simply skip that block altogether without any implicit barrier. This absence of a barrier contrasts with `single`, which inserts a barrier at the block’s end by default.

```c
#pragma omp master
{
   // Only the master thread executes this block
}
```

In OpenMP 5.0, there is also **`parallel master`**, combining the start of a parallel region with the `master` concept, but crucially still meaning only the master thread performs the structured block. There is *no implicit barrier* at entry or exit for `master`. This can be faster when one wants to avoid stalling other threads.

The **intuition**: Use `master` when you need the **master thread** specifically to execute certain steps (perhaps for consistent I/O or to avoid switching threads for a delicate operation) and you do not need the implicit barrier that `single` would provide.

---

### 2.4.2.1 Example: Master

An illustration of `master` usage:

```c
#pragma omp parallel shared(x)
{
   int id = omp_get_thread_num();
   print_intermediate_results();

   #pragma omp master
   {
      do_some_data_initialization();
   }

   #pragma omp barrier
   {
      // After the barrier, all threads proceed simultaneously
   }

   #pragma omp for
   for (int i = 0; i < 5; i++) {
      x[i] += i;
   }

   #pragma omp master
   {
      print_final_results();
   }
}
```

In this code, only the master thread executes `do_some_data_initialization()` and `print_final_results()`. A manual `#pragma omp barrier` is inserted before the parallel loop to ensure that initialization is complete before all threads start using the initialized data. Notice that without a barrier, threads could race ahead and read uninitialized values if the master thread had not finished.

---

## 2.4.3 Critical

**Key Concept**: A **`critical`** section ensures that *only one thread* at a time can execute the enclosed structured block. Other threads attempting to enter the same named or unnamed critical section must wait until the executing thread finishes. In effect, `critical` is an *implementation-level mutual exclusion construct*, preventing data races in shared-memory regions.

```c
#pragma omp critical [name [hint(hint-expression)]]
{
   // Code block that only one thread can enter at a time
}
```

There is a performance note: `critical` can be *costly* because it forces threads to serialize whenever they encounter that region. One should thus consider whether *atomic* or *lock* constructs might be more efficient if only a specific memory operation needs protection.

An **example** might be updating a shared sum inside a long loop. Ensuring only one thread updates the sum at a time prevents race conditions. However, a `critical` might bottleneck performance if the loop is large.

---

### 2.4.3.1 Example: Critical

Below is a minimal illustration. Suppose each thread removes an item from two lists `listx` and `listy`, processing them in some manner. Two different named critical sections protect each list:

```c
#pragma omp parallel
{
   #pragma omp critical xvalues
   {
      Val x = remove_item(listx);
      process(x);
   }
   
   #pragma omp critical yvalues
   {
      Val y = remove_item(listy);
      process(y);
   }
}
```

In this code, a thread that is removing an item from `listx` under `critical xvalues` does not block other threads from entering the `yvalues` critical section. If the same (unnamed) critical section was used for both lists, then even accessing different lists would serialize all threads. By naming them differently, concurrency is increased.

---

## 2.4.4 Lock Routines (Runtime API)

**Key Concept**: An **OpenMP lock** is a lower-level mechanism that grants a single thread exclusive access to specific data or code regions. It functions like a more manual version of a critical section. Instead of guarding *code*, a lock typically guards *data*, letting the user decide precisely when to acquire and release it.

The basic lock routines are declared in `<omp.h>` as:
```c
void omp_set_lock(omp_lock_t *lock);
void omp_unset_lock(omp_lock_t *lock);
void omp_init_lock(omp_lock_t *lock);
void omp_destroy_lock(omp_lock_t *lock);

int  omp_test_lock(omp_lock_t *lock);
```

There are two lock types: **simple locks** and **nestable locks**. A simple lock cannot be set multiple times by the same thread, whereas a nestable lock can be acquired repeatedly by the same thread.

Locks create a memory fence, meaning all thread-visible shared variables are consistent upon acquiring or releasing a lock.

---

### 2.4.4.1 Lock Example

One might initialize and use a lock in the following way:

```c
omp_lock_t locka;
omp_init_lock(&locka);

#pragma omp parallel sections
{
   #pragma omp section
   {
      omp_set_lock(&locka);
      access_array_A();
      omp_unset_lock(&locka);
   }

   #pragma omp section
   {
      omp_set_lock(&locka);
      access_array_A();
      omp_unset_lock(&locka);
   }
}

omp_destroy_lock(&locka);
```

Here, both sections share the same lock (`locka`), thus serializing `access_array_A()`. If the array sections are disjoint, better performance might be obtained by using multiple locks so that different threads do not block each other unnecessarily.

---

## 2.4.5 Atomic

**Key Concept**: **`atomic`** is a special directive that ensures an *uninterrupted, atomic operation* on a specific memory location, often corresponding to hardware-level atomic operations if the CPU supports them. Unlike `critical`, which protects a code *block*, `atomic` specifically protects a *single load/store/update* on a shared variable.

The syntax often appears in forms like:

```c
#pragma omp atomic update
shared_var = shared_var + expression;
```

or simply:

```c
#pragma omp atomic
shared_var += expression;
```

The clauses such as `read`, `write`, `update`, or `capture` define how the atomic operation is performed. For instance, `capture` can read the old value and write a new one atomically.

---

### 2.4.5.1 Atomic Examples

A read-protected example:
```c
int tmp;
#pragma omp atomic read
tmp = shared_counter;
```
This ensures that reading from `shared_counter` is done atomically.

An update example:
```c
#pragma omp atomic
ic = ic + 1;
```
All threads increment `ic` in a way that prevents lost updates.

A capture example might look like this:
```c
int d;
#pragma omp atomic capture
{
   d = v;
   v += 1;
}
```
First the old value of `v` is captured into `d`, and then `v` is incremented, all in one atomic step.

---

### 2.4.5.2 Atomic vs. Critical

An **intuition** is that `atomic` is often more lightweight and suitable for protecting a single memory update, such as increments to a counter. A `critical` region is more expensive, especially if it encloses more complex operations. However, if you need to protect multiple statements or operate on multiple variables as a single mutual-exclusion region, `critical` might be more appropriate.

---

## 2.4.6 Barrier

**Key Concept**: A **`barrier`** is a synchronization point where *all threads in a team must arrive before any can proceed*. When a thread encounters a barrier, it waits until every other thread has also encountered that barrier. Only then do they all continue.

In OpenMP, `barrier` can be **explicit**:
```c
#pragma omp barrier
```
or **implicit**, typically at the end of worksharing constructs such as `for` or `sections`, unless `nowait` is used.

The **intuition** is that barriers are essential when partial results produced by different threads must be ready before moving to the next phase. However, barriers can *hurt performance* if used too frequently, because they force a strict synchronization among threads.

---

### 2.4.6.1 Barrier Example

Consider a parallel region where threads must each finish sorting different segments of an array before searching for a target value:

```c
#pragma omp parallel shared(x)
{
   int id = omp_get_thread_num();
   sort_part(id);
   
   #pragma omp barrier
   search_target(x);

   #pragma omp for nowait
   for(int i = 0; i < N; i++) {
      do_something(i);
   }
}
```

In this example, each thread first sorts part of the data. If the array segments are interdependent, we must ensure all partial sorts are complete before any thread calls `search_target(x)`. The barrier accomplishes that synchronization.

---

## 2.4.7 Ordered

**Key Concept**: The **`ordered`** construct forces *sequential ordering* of certain parts of a parallelized loop (or other concurrency scenario) that would otherwise run in parallel. Within a loop construct annotated with `ordered`, any code enclosed by `#pragma omp ordered` is executed *in the order of the original, sequential loop*.

A typical usage:

```c
#pragma omp for ordered
for(int i = 0; i < N; i++) {
   // Parallel portion
   #pragma omp ordered
   {
      // Region that must run in the iteration order
   }
}
```

The **intuition**: If a loop is otherwise safe to parallelize, but certain operations (like printing results in ascending iteration order) must remain strictly ordered, `ordered` allows partial ordering constraints. This often involves overhead, so it should be used only when necessary.

---

### 2.4.7.1 Ordered and Task Dependencies

OpenMP 5.0 extends `ordered` to handle task dependencies, enabling advanced patterns like *doacross loops*. The idea is to define *cross-iteration dependencies* where each iteration can only proceed after the previous iteration completes certain tasks. For instance:

```c
#pragma omp single
{
   for(int i = 0; i < 4; i++) {
      #pragma omp task firstprivate(i) depend(out: i)
      {
         // Some computation
         #pragma omp ordered depend(sink: i-1)
         // Ordered portion
         #pragma omp ordered depend(source)
      }
   }
}
```

This snippet ensures iteration `i` depends on iteration `i-1` (sink/source). The concurrency is preserved, except for the part that requires explicit ordering.

---

## 2.4.8 Summary of Synchronization

Synchronization constructs in OpenMP provide a range of granularity and power:

**`single`** ensures exactly one thread runs a block, inserting a default barrier at the end.  
**`master`** enforces that the master thread alone executes a block, with *no* implicit barrier.  
**`critical`** serializes a region among threads, guaranteeing *mutual exclusion* for that block.  
**Locks** allow *finer* manual control over access to data rather than code.  
**`atomic`** provides lightweight protection for a single memory operation.  
**`barrier`** enforces a *full synchronization point*, letting all threads catch up before proceeding.  
**`ordered`** partially serializes certain statements in a loop or tasks to retain a necessary order.

Each mechanism addresses different concurrency scenarios: controlling *who* executes a block, *how* data updates are protected, or *when* threads must sync up. By combining these constructs effectively, we can write correct and efficient shared-memory applications.


# 2.5 (Explicit) Tasks

This final section addresses **explicit tasks** in OpenMP, introduced to enable more dynamic and flexible parallelism beyond the static loop and sections models. The emphasis lies on *task parallelism*, where independent (or partially independent) operations can be encapsulated into tasks that are then executed by a team of threads in potentially unbounded or nested ways.

---

## 2.5.1 Parallelism Control Structures: Task Parallelism

Task parallelism focuses on enabling independent units of work to run in parallel, even if they do not fit the traditional loop-based (data-parallel) approach. The OpenMP constructs for task parallelism allow creation of tasks anywhere in the code. Each task has code, data, and an assigned thread to perform it.

In earlier worksharing constructs such as `sections` or `for`, tasks are *implicit* because threads simply perform those instructions in parallel. With explicit tasks, a program can spawn new tasks dynamically, for example during recursion or while traversing complex data structures. This flexibility leads to more powerful concurrency patterns that can reveal additional parallelism.

---

## 2.5.2 Tasking Terminology

OpenMP distinguishes two types of tasks:

An **implicit task** is created automatically at the start of a parallel region for each thread in the team. These are the usual tasks that run the code enclosed by `#pragma omp parallel`.

An **explicit task** is created whenever a `task` construct or related directive is encountered. The thread that encounters the construct may execute the task immediately or defer its execution to another thread, depending on scheduling choices made by the runtime. This enables a wide range of dynamic parallel behaviors.

The phrase **task region** denotes all code inside a single explicit task, including any nested tasks that might be generated. While **parallel regions** hold multiple implicit tasks, the introduction of explicit tasks broadens concurrency opportunities.

---

## 2.5.3 What Are Tasks?

A task is a package of code and data. Once created, it can be executed by any thread in the current team. The task has two principal components: the instructions (the function or statements to run) and the data environment (the variables and values it needs). The creation of a task does not necessarily mean it executes immediately. The runtime may queue it for execution by whichever thread becomes available.

The key concept of tasks is that concurrency can now be expressed with greater finesse than static loop boundaries. This allows creative strategies:

One thread might traverse a data structure, generating tasks as new parts of the structure are discovered. Another thread can concurrently pull tasks off a queue and execute them, ensuring balanced use of resources. This is useful for recursive algorithms, dynamic work generation, or scenarios where the amount of work is not known in advance.

---

## 2.5.4 Task Construct

OpenMP provides the `task` directive to create explicit tasks. It appears as:

```
#pragma omp task [clause ...]
structured-block
```

When a thread encounters `#pragma omp task`, it packages the code in `structured-block` and any relevant data (by default `firstprivate` for local variables, but subject to scoping clauses) into a task. The runtime can run it immediately or store it for later execution. Tasks may also be nested, meaning tasks can create further tasks recursively or iteratively.

Properties of explicit tasks include the possibility of specifying dependencies via the `depend` clause, controlling the order in which tasks may execute. A barrier or a `taskwait` ensures that all previously created tasks complete. The environment fosters dynamic load balancing because idle threads can help with the existing backlog of tasks.

---

### 2.5.4.1 Example: Task Construct Example

A minimal illustration is:

```
#pragma omp parallel
{
   #pragma omp single
   {
      #pragma omp task
      functionA();

      #pragma omp task
      functionB();

      #pragma omp task
      functionC();
   }
}
```

The `parallel` directive creates a team of threads. Only one thread (due to `single`) executes the code that creates tasks. Three tasks are then generated: `functionA`, `functionB`, and `functionC`. The runtime can distribute these tasks among all threads in the team. The threads run them in any order, possibly concurrently. After the `single` block ends, no threads can create further tasks in that block, but the tasks already generated remain to be executed. By the time the parallel region ends, all tasks have completed (they cannot outlive the region).

The intuition here is that work is separated into three calls (A, B, C). Instead of having a single thread run them sequentially, each call becomes a separate task that multiple threads can help finish. This separation often speeds up the total run time if the tasks involve substantial independent computations.

---

## 2.5.5 Taskloop Construct

OpenMP 5.0 introduced the `taskloop` construct, which allows loop iterations to be converted into explicit tasks, rather than the implicit tasks created by `parallel for`. The syntax looks like:

```
#pragma omp taskloop [clause ...]
for (int i = 0; i < N; i++) {
   // loop body
}
```

This is particularly useful for scenarios where the loop’s workload is large but irregular, or when further dependencies among iterations must be controlled. The runtime divides the loop into tasks of a certain size (possibly specified by `grainsize` or `num_tasks` clauses), distributing them across available threads.

The properties of `taskloop` can resemble manual blocking or chunking but automated by OpenMP. It blends data parallel (loop-based) logic with the dynamic scheduling advantages of tasks. In essence, each chunk of loop iterations becomes an explicit task that can be picked up at different times by different threads.

---

### 2.5.5.1 Example: Taskloop vs Task vs Manual Coding

A simple scalar multiply might look like this:

```
#pragma omp taskloop grainsize(GR)
for(int i = 0; i < SIZE; i++) {
   A[i] = B[i] * S;
}
```

If done with explicit tasks manually, one would repeatedly create tasks for each chunk in a loop. That can be verbose or tricky to maintain. The `taskloop` directive is more direct. It can produce better load balancing for dynamic scenarios than a static `parallel for`, while keeping code simpler than a fully manual approach.

---

## 2.5.6 Tasks (OpenMP 3.0+): Example Traversing Linked Lists Recursively

A common use case for explicit tasks is *recursive data structures*. If a list or tree is traversed, we might want to process left and right subbranches in parallel.

Consider a function:

```
void traverse(struct node *p) {
   if (p->left) {
      #pragma omp task
      traverse(p->left);
   }
   if (p->right) {
      #pragma omp task
      traverse(p->right);
   }
   process(p);
}
```

Each time we encounter a non-null left or right pointer, we create a new task to traverse that subtree. Because `process(p)` occurs after spawning tasks for the children, we can exploit concurrency without rewriting the entire structure as an iterative approach. If the data structure is large and balanced, many tasks can be generated, distributing the traversal among multiple threads efficiently.

---

## 2.5.7 Tasks (OpenMP 3.0+): Example Fibonacci Numbers

Another canonical illustration is the Fibonacci function. The function below is purely to demonstrate task creation patterns; it is not necessarily the fastest way to compute Fibonacci numbers. It does, however, showcase how a recursive call tree can spawn tasks at each step.

```
int fib(int n) {
   int i, j;
   if (n < 2) return n;
   else {
      #pragma omp task shared(i)
      i = fib(n - 1);
      #pragma omp task shared(j)
      j = fib(n - 2);
      #pragma omp taskwait
      return i + j;
   }
}
```

A single thread initially calls `fib(NW)`. Upon encountering each `#pragma omp task`, it spawns tasks that themselves call `fib(...)` recursively. The `taskwait` ensures that the function does not return until the two spawned tasks finish. This builds a binary tree of tasks, each child computing part of the Fibonacci sequence. Different threads can pick up tasks from the pool, leading to parallel execution.

---

### 2.5.7.1 Execution Yields a Binary Tree of Tasks

For instance, `fib(4)` yields a binary tree of tasks: `fib(3)` and `fib(2)` become tasks. Then `fib(3)` spawns `fib(2)` and `fib(1)`, while `fib(2)` spawns `fib(1)` and `fib(0)`. As the recursion unfolds, a task graph forms. Several threads can work on different branches simultaneously. Dependencies are locally enforced by `taskwait` at each function level.

The intuition is that each call can proceed independently except when it must combine results, which is exactly when `taskwait` synchronizes them. With large recursion depths, load balancing is improved because tasks are created on the fly.

---

## 2.5.8 Summary of (Explicit) Tasks

Explicit tasks in OpenMP allow dynamic parallelism by creating work units that can be deferred, nested, or scheduled flexibly. This is in contrast to purely static worksharing. The main constructs are:

`task`, which generates tasks at any point in the code.  
`taskloop`, which converts loop iterations into tasks rather than relying on static scheduling.  

The essential property is that concurrency is found not just in loops but in any piecewise independent blocks, especially helpful in recursive algorithms or irregular data structures. Task parallelism can yield more balanced use of processor resources and can scale well if the tasks are numerous and well-sized.

With explicit tasks, OpenMP supports both data parallelism and task parallelism, ensuring that a wide variety of scientific and industrial applications can leverage shared-memory multicore architectures effectively and efficiently.


