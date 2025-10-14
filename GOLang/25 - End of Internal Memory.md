

### 1. The Core Question: Where Are Function Expressions Stored?

Understanding how Go manages memory is fundamental to writing efficient and predictable code. A deep grasp of the program lifecycle—from source code to a running process—demystifies variable scope, performance characteristics, and the underlying mechanics of function calls. This document serves as a study note to explore these internals by answering a specific and insightful question about how Go handles functions defined within other functions.

[!note] When a function expression is assigned to a variable inside another function, is it stored on the stack or in the code segment?

To answer this, we must first understand the foundational concepts of a Go program's journey from a text file to a live, executing process.

### 2. The Two Phases of a Go Program

Every Go program undergoes a two-phase lifecycle before its output appears on your screen. Grasping the distinction between these phases is the key to understanding the program's memory layout and behavior.

- **Compilation Phase:** The process of converting human-readable Go source code into a single, executable binary file containing machine instructions.
- **Execution Phase:** The process of loading the compiled binary file into the computer's memory (RAM) and running its instructions line by line.

We will now examine the first of these phases in greater detail.

### 3. Phase 1: The Compilation Phase ()

The compilation phase begins when you invoke the Go compiler, typically with a command like `go build`. The compiler's primary job is to parse your Go code and translate it into a single, self-contained executable binary file—a file composed of the 0s and 1s that a computer's processor can directly understand.

During this phase, several critical activities occur:

- **Command:** The `go build main.go` command initiates the compilation process.
- **Output:** The result is a binary file (e.g., `main` on Linux/macOS or `main.exe` on Windows).
- **Code Segment Creation:** A special section within this binary file, called the **Code Segment**, is created.
- **Code Segment Contents:** The compiler places specific, unchangeable elements from your source code into this segment:
    - All function definitions, including `init`, `main`, `call`, and even the inner `add` function expression.
    - All constant variable definitions, such as `const a = 10`.

[!warning] The Code Segment is **read-only**. Its contents are determined and fixed at compile time and cannot be modified while the program is running.

With the binary file and its internal Code Segment now established, let's look at the sample program that will guide our analysis of the execution phase.

### 4. The Sample Go Program

The following Go code will serve as the basis for a step-by-step simulation of how a program is loaded into memory and executed.

```go
package main

import "fmt"

const a = 10
var p = 100

func call() {
    add := func(x, y int) {
        z := x + y
        fmt.Println(z)
    }
    add(5, 6)
    add(p, a)
}

func main() {
    call()
    fmt.Println(a)
}

func init() {
    fmt.Println("Hello")
}
```

The next section will trace this program's lifecycle from the moment it is executed in memory.

### 5. Phase 2: The Execution Phase ()

The execution phase begins when a user runs the compiled binary (e.g., by typing `./main` in the terminal). At this point, the operating system loads the static binary file from the disk into the computer's active memory (RAM), bringing the program to life as a running process.

When loaded into RAM, the process is allocated several key memory areas:

- **Code Segment:** The read-only Code Segment from the binary file is loaded directly into this area of RAM. It contains all function definitions and constants.
- **Data Segment:** This area is allocated for global variables that can be read and written to, such as the `p` variable in our example.
- **Stack:** A dynamic memory area used to manage active function calls. Each function call creates a "stack frame" to store its local variables and parameters.
- **Heap:** A memory area for dynamic allocations. This is the domain of Go's Garbage Collector (GC), a powerful process responsible for automatically managing this memory to prevent leaks.

We will now simulate the program's execution, focusing on the interactions between the Stack, Data Segment, and Code Segment.

### 6. Step-by-Step Execution Simulation

This section provides a chronological trace of the program's execution flow, detailing how the different memory segments are used.

**6.1. Program Initialization**

- The OS loads the binary's Code Segment into RAM, making all function definitions (`init`, `main`, `call`, `add`) and the constant `a` available.
- _Before any function code executes_, the runtime initializes global variables. The variable `p` is allocated in the **Data Segment** and set to `100`.
- With the memory segments prepared, the runtime begins execution.

**6.2.** `**init()**` **Function Execution**

- Go's runtime automatically executes the `init()` function before `main()`.
- A **stack frame** for `init()` is created and pushed onto the call stack.
- The `fmt.Println("Hello")` line executes, producing the program's first line of output.
- Once `init()` completes, its stack frame is destroyed (popped off the stack).

**6.3.** `**main()**` **and** `**call()**` **Function Execution**

- The runtime then executes the `main()` function, creating a new stack frame for it.
- Inside `main()`, the `call()` function is invoked. This immediately creates a new stack frame for `call()`, which is placed on top of `main`'s frame on the stack.

**6.4. The Answer Revealed: The** `**add**` **Variable**

Execution now enters the `call` function. When the line `add := func(...)` is executed, a new local variable named `add` is created within the `call` function's stack frame. This directly addresses our central question.

[!tip] The `add` variable on the stack **does not** contain the function's machine code. Instead, it holds a **reference** (or memory address) that points to the actual `add` function's definition, which was already placed in the read-only **Code Segment** during the compilation phase.

[!warning] This `add` function is now "bound" to the `call` function's scope. It can only be accessed from within `call` by using the `add` variable. No other function, including `main`, can access it directly.

**6.5. Tracing the** `**add**` **Invocations**

- `**add(5, 6)**`**:**
    - A new, temporary stack frame for this invocation of `add` is created on top of `call`'s frame.
    - The parameters `x=5` and `y=6` are stored in this new frame. The local variable `z` is calculated as `11`.
    - The value `11` is printed to the console.
    - Upon completion, this temporary stack frame is destroyed.
- `**add(p, a)**`**:**
    - Another temporary stack frame for `add` is created.
    - To resolve the arguments `p` and `a`, the runtime performs a sequential search. For `p`, it first checks the local `add` scope (not found), then the parent `call` scope (not found), and finally finds it in the global **Data Segment**. For `a`, it checks local scopes (not found), the Data Segment (not found), and ultimately finds the constant in the **Code Segment**.
    - The parameters `x=100` and `y=10` are passed. The local variable `z` is calculated as `110`.
    - The value `110` is printed to the console.
    - This stack frame is also destroyed.

**6.6. Program Conclusion**

- With both `add` calls complete, the `call` function finishes its execution. Its stack frame, including the `add` variable, is destroyed.

It's critical to note that while the `add` _variable_ on the stack is gone, the `add` function's actual machine code is untouched in the read-only Code Segment. It persists for the life of the program but is now inaccessible because the only reference to it has been destroyed.

- Control returns to the `main` function.
- The next line, `fmt.Println(a)`, executes. The program looks for `a` and finds it in the Code Segment, printing its value of `10`.
- Finally, the `main` function finishes. Its stack frame is destroyed, the stack becomes empty, and the program terminates.

This simulation clarifies the entire execution flow and the distinct roles of each memory segment.

### 7. Summary of Key Concepts

This analysis reveals several critical principles of Go's internal workings.

- **Compilation vs. Execution:** Go programs are first compiled into a static binary, which is then loaded into RAM for dynamic execution.
- **Code Segment:** A read-only memory area established at compile time. It stores all function definitions and constants. Its contents persist for the program's entire lifetime.
- **Data Segment:** A read-write memory area for global variables.
- **Stack:** A LIFO (Last-In, First-Out) data structure that manages active function calls via stack frames. It holds local variables, function parameters, and return addresses.
- **Function Expressions:** When a function expression is assigned to a variable, the variable itself lives on the stack (if local) and stores a **reference** to the function's actual code, which resides in the Code Segment.
- **Variable Lookup Order:** As demonstrated in the `add(p, a)` call, Go searches for variables in a strict order: first in the local scope (the current function's stack frame), then in the global scope (the Data Segment for variables like `p`), and finally in the Code Segment (for constants like `a`).

[!example] The final, correct program output as confirmed by our step-by-step simulation is: