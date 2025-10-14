# 🧠 Study Notes: Understanding Scope in Go

### 🔹 1. Why Scope Matters
Scope defines **where variables/functions are visible**.  
Without understanding it, debugging becomes chaos.

> [!warning] Scope confusion = invisible bugs. Learn this before anything else.

---

### 🔹 2. What is Scope?
- **Scope:** The region where an identifier (var/function) is accessible.  
- “In scope” = accessible; “Out of scope” = inaccessible.

---

### 🔹 3. Example Code
```go
package main
import "fmt"

var a = 20
var b = 30

func add(x, y int) {
    z := x + y
    fmt.Println(z)
}

func main() {
    var p, q = 30, 40
    add(p, q)
    add(a, b)
    add(a, p)
    // add(b, z) // ❌ error: undefined z
}
```

---

### 🔹 4. How Go Handles Scope (Simplified Memory View)

#### 🟦 Global Scope
- Declares `a`, `b`, `add`, and `main`.
- Accessible to all functions.

#### 🟩 Function Scope
- Each function call gets **its own local memory**.
- Variables like `p`, `q`, and `z` exist **only** in their respective functions.

> [!note] Local vars disappear once the function ends — they don’t persist.

#### 🟨 Temporary Scopes
Each `add()` call creates and destroys a new scope:
- `add(p,q)` → x=30, y=40 → z=70  
- `add(a,b)` → x=20, y=30 → z=50  
- `add(a,p)` → x=20, y=30 → z=50  

> [!warning] Each scope is temporary — `z` is gone after `add()` ends.

---

### 🔹 5. Scope Resolution Rules
1. **Check Local Scope First**  
2. **Then Check Global Scope**  
3. **No Cross-Scope Access** (functions can’t see each other’s locals)

---

### 🔹 6. Why `add(b, z)` Fails
- `b`: Found globally ✅  
- `z`: Not in `main` or global ❌ → `undefined: z`

---

### 🔹 7. Quick Recap
| Scope Type | Example | Visible To |
|-------------|----------|-------------|
| Global | `a`, `b`, `main`, `add` | Whole program |
| Local | `p`, `q`, `z` | Inside function only |

> [!tip] Interview Tip: Always trace from **local → global** when finding variable visibility.  
> [!tip] Pro Tip: Save your file (`Ctrl+S`) before running — many “errors” are just unsaved code!
