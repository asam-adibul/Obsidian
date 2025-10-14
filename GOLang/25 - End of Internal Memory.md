```cpp
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

-----

## 📝 Go Internal Memory Study Notes

>[\!note] This document summarizes the internal memory management and execution phases of Go programs.

### 🚀 Go Program Execution Phases

Go code execution consists of two main phases:

  * **Compilation Phase**:

      * The `go` compiler converts the human-readable Go source code into a **binary executable file** (machine code - 0s and 1s).
      * **Constants** (e.g., `const a = 10`) and **function definitions** are stored in the **code segment** of this binary file.
      * The compiler checks for the presence of a `main` function. Without it, compilation will fail.

  * **Execution Phase**:

      * When the binary file is run, the program utilizes various memory segments in **RAM**.

-----

### 🧠 RAM Memory Segments

Go programs utilize the following memory segments in RAM during execution:

  * ### Code Segment

      * [\!tip] **Read-Only**: Stores the compiled machine code, including constant values and function definitions.
      * These definitions are loaded from the binary file before the program starts running.

  * ### Data Segment

      * [\!note] Stores **global variables** (e.g., `var p = 100`).
      * Values in the data segment **can be modified** during program execution, unlike constants in the code segment.

  * ### Stack

      * [\!example]
        ```cpp
        func myFunc(param int) { // A stack frame is created for myFunc
            localVar := param + 1 // localVar is stored in this stack frame
            // ...
        }
        ```
      * [\!note] Manages **function calls** and their **local variables**.
      * Each function call creates a **stack frame**, which is a block of memory allocated on the stack.
      * Stack frames store function parameters and local variables specific to that function's execution.
      * Function expressions assigned to variables (e.g., `add := func(...)`) store a **reference** (memory address) to their actual definition in the code segment within the current stack frame, not the entire function code.
      * When a function completes execution, its stack frame is **removed (popped out)** from the stack.

  * ### Heap

      * [\!tip] Used for **dynamic memory allocation**.
      * Managed by the **Garbage Collector (GC)**, which automatically reclaims memory that is no longer in use.

-----

### ⚙️ Example Code Execution Flow

Let's trace the execution of the provided Go code:

1.  **Compilation**:

      * `const a = 10`, `func call()`, `func main()`, `func init()` are stored in the code segment.
      * The nested function `add := func(x, y int)` within `call()` is also stored in the code segment during compilation.

2.  **Execution - Program Start**:

      * **Global variable `p`**: `var p = 100` is moved to the **data segment**.
      * **`init()` function**:
          * An `init` stack frame is created.
          * `fmt.Println("Hello")` is executed, printing "Hello".
          * The `init` stack frame is removed.
      * **`main()` function**:
          * A `main` stack frame is created.
          * `call()` is invoked.

3.  **`call()` Function Execution**:

      * A `call` stack frame is created.
      * `add := func(x, y int)`: The variable `add` within the `call` stack frame stores a **reference** to the `add` function definition in the code segment.
      * `add(5, 6)`:
          * An `add` stack frame is created with `x=5`, `y=6`.
          * `z := x + y` calculates `z=11`.
          * `fmt.Println(z)` prints "11".
          * The `add` stack frame is removed.
      * `add(p, a)`:
          * The program searches for `p` (found in data segment, value 100) and `a` (found in code segment, value 10).
          * Another `add` stack frame is created with `x=100`, `y=10`.
          * `z := x + y` calculates `z=110`.
          * `fmt.Println(z)` prints "110".
          * The `add` stack frame is removed.
      * The `call` stack frame is removed.

4.  **`main()` Function Continues**:

      * `fmt.Println(a)`: The program searches for `a` (found in code segment, value 10).
      * Prints "10".
      * The `main` stack frame is removed.

[\!example]
Final Output:

```
Hello
11
110
10
```

For a detailed explanation, watch the video: [http://www.youtube.com/watch?v=Kwu\_4e6rwd4](http://www.youtube.com/watch?v=Kwu_4e6rwd4)