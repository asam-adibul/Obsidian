Function Expressions and Scope (Compact Summary)

1. Function Expressions
- A function expression assigns an anonymous function to a variable.
- That variable becomes callable like a normal function.

Example:
```cpp
package main

import "fmt"

func main() {
    add := func(a, b int) {
        fmt.Println(a + b)
    }
    add(2, 3) // Prints 5
}
```

2. Scope: Global vs Local
- Global functions are loaded before execution and can be called from anywhere.
- Local function expressions exist only after their definition line is executed.

Example (Error: undefined variable):
```cpp
package main

import "fmt"

func main() {
    add(2, 3) // ERROR: undefined: add

    add := func(a, b int) {
        fmt.Println(a + b)
    }
}
```

Correct order:
```cpp
package main

import "fmt"

func main() {
    add := func(a, b int) {
        fmt.Println(a + b)
    }
    add(2, 3) // Prints 5
}
```

3. Execution Flow in Go
- Loading Phase: Compiler loads all global functions.
- Execution Phase: main() runs line-by-line, creating local scope variables and functions dynamically.

4. Variable Shadowing
- A local variable/function with the same name as a global one hides the global version inside its scope.

Example:
```cpp
package main

import "fmt"

func add(a, b int) {
    fmt.Println("Global Add:", a + b)
}

func main() {
    add(10, 10) // Global Add: 20

    add := func(a, b int) {
        fmt.Println("Local Add:", a + b)
    }
    add(4, 5) // Local Add: 9
}
```

5. Key Takeaways

- Function Expression → Anonymous function assigned to variable.
- Global Scope → Functions available before runtime.
- Local Scope → Function expressions exist after definition line.
- Shadowing → Local variable/function hides global one within scope.
