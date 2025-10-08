Basics of init() Function in Go

```go
package main

import "fmt"

var a = 10

func main() {
    fmt.Println("Hello to Init Function")
    fmt.Println(a)
}

func init() {
    fmt.Println("I am the first executed function")
    fmt.Println("from init", a)
}
```

⚙️ Step-by-Step Execution:
1. Global variables are initialized first → a = 10
2. The init() function runs automatically before main()
3. After init() finishes, main() runs

📘 Output:
I am the first executed function
from init 10
Hello to Init Function
10

🔍 Things to Remember:
✅ init() runs automatically before main()
✅ You cannot call init() manually
✅ You cannot pass arguments or return values to/from init()
✅ Each Go file can have only one init() function
✅ Package execution order → variable initialization → init() → main()
✅ Useful for setup tasks, configuration, or initialization before main() logic

---
Code From Class 
___

```go
package main

import "fmt"

var a = 10

func main() {

    fmt.Println("Hello to Init Function")

    fmt.Println(a)

}

  
func init() {

    fmt.Println("I am the first executed function")

    fmt.Println("from init", a)

}
```
