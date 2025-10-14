# 🗒️ Go Study Note: Function Expressions and Memory

### 1. Core Question

> [!note] Where is a function expression stored in Go?

- **Local variable** storing the function → **Stack**
    
- **Actual function code** → **Read-only Code Segment**
    

---

### 2. Go Program Phases

- **Compilation Phase:** Converts Go source to executable binary.
    
- **Execution Phase:** Loads binary into RAM and runs it.
    

---

### 3. Compilation Phase

- `go build main.go` → creates binary (`main` or `main.exe`)
    
- **Code Segment** (read-only) stores:
    
    - All function definitions (`init`, `main`, `call`, `add`)
        
    - Constants (`const a = 10`)
        
- **Warning:** Code Segment is **immutable** at runtime
    

---

### 4. Execution Phase

- RAM layout:
    
    - **Code Segment:** function definitions + constants
        
    - **Data Segment:** global variables (`var p = 100`)
        
    - **Stack:** local variables & function calls
        
    - **Heap:** dynamic allocations (managed by GC)
        

---

### 5. Sample Program

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

---

### 6. Execution Flow & Memory Use

**6.1 Program Init**

- OS loads Code Segment into RAM
    
- Global variable `p` allocated in Data Segment
    

**6.2 `init()`**

- Stack frame created
    
- Prints `"Hello"`
    
- Stack frame destroyed
    

**6.3 `main()` & `call()`**

- Stack frame for `main()`
    
- Stack frame for `call()`
    

**6.4 `add` Function**

> [!tip] `add` variable on stack stores **reference** → actual code in Code Segment  
> [!warning] Only accessible within `call()`

**6.5 `add` Invocations**

- `add(5,6)` → stack frame created, prints `11`
    
- `add(p,a)` → stack frame created, prints `110`
    
- Stack frames destroyed after execution
    

**6.6 Program End**

- Stack frames destroyed
    
- Code Segment persists until program terminates
    

---

### 7. Key Concepts

- **Compilation vs Execution:** Binary → RAM → Execution
    
- **Code Segment:** read-only, stores function code & constants
    
- **Data Segment:** read/write, stores global variables
    
- **Stack:** local vars, parameters, return addresses
    
- **Function Expressions:** variable on stack, code in Code Segment
    
- **Variable Lookup Order:** Local → Global → Constants (Code Segment)
    

> [!example] Program Output:

```
Hello
11
110
10
```