Compact Note: Anonymous Functions and IIFE in Golang

----------------------------------------------------
1. FUNCTION TYPES IN GO
----------------------------------------------------
There are two main types of functions in Go:
1. Named (Standard) Functions
2. Anonymous Functions

-------------------------------
1.1 Named Functions
-------------------------------
- Declared with a name after the `func` keyword.
- Can be called (invoked) from anywhere in the program.
- Used for reusability and clarity.

Example:
```cpp
package main

import "fmt"

func add(a int, b int) {
    fmt.Println(a + b)
}

func main() {
    add(5, 7) // Invoking named function
}
```

-------------------------------
1.2 Anonymous Functions
-------------------------------
- Declared without a name.
- Cannot be declared and left unused in Go.
- Must be executed immediately or assigned to a variable.

Example (Assigned to variable):
```cpp
package main

import "fmt"

func main() {
    sum := func(a int, b int) int {
        return a + b
    }
    fmt.Println(sum(5, 7))
}
```

----------------------------------------------------
2. IMMEDIATELY INVOKED FUNCTION EXPRESSION (IIFE)
----------------------------------------------------
Definition:
An IIFE is an anonymous function that is defined and executed immediately.

Syntax:
```cpp
package main

import "fmt"

func main() {
    func(a int, b int) {
        fmt.Println(a + b)
    }(5, 7) // Immediately invoked with arguments
}
```

Steps:
1. Define an anonymous function.
2. Add `()` after the function body to invoke it immediately.
3. Pass arguments inside the parentheses.

----------------------------------------------------
3. TERMINOLOGY
----------------------------------------------------
- Invoke: The formal term for calling a function.
- Expression: Any valid piece of code that performs an action or returns a value.

Examples of expressions:
```cpp
a := 10                     // Variable declaration expression
if a > 0 { fmt.Println(a) } // If expression
add(5, 7)                   // Function invocation expression
```

----------------------------------------------------
4. INTERVIEW POINTS
----------------------------------------------------
Common questions:
- What is an Anonymous Function?
- What is an IIFE?
- Difference between "invoke" and "call"?
- What is an expression?

----------------------------------------------------
Summary:
----------------------------------------------------
- Named functions have identifiers and can be reused.
- Anonymous functions lack names and must be used immediately or assigned.
- IIFE = Function defined and executed instantly.
- “Invoke” = formally call a function.
- Understanding expressions and function types is crucial for Go interviews.
