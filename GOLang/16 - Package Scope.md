# 🗒️ Go Study Note: Package Scope

>[!note] Go's **package scope** defines the visibility of identifiers like variables and functions across different packages. The fundamental rule is simple: visibility is determined by capitalization.

## 🔑 The Core Rule: Capitalization

>[!tip] Exported vs. Unexported Identifiers

- **Exported (Public):** Identifiers that begin with a **capital letter** are exported. They are visible and can be accessed from any other package that imports their package.
- **Unexported (Private):** Identifiers that begin with a **lowercase letter** are unexported. They are private _to the package_ and only accessible from within their own package.

--------------------------------------------------------------------------------

[!info] Why Modules are Necessary While you can compile multiple files within the _same_ package using a command like `go run .`, this approach doesn't scale to projects organized across _different_ packages. Managing dependencies and enabling communication between separate packages requires a formal system: Go modules.

## 📁 Creating a Custom Package

Creating custom packages is fundamental for organizing code and controlling visibility in Go. The setup process involves two key steps:

- **Directory Structure:** A new package is created by placing one or more `.go` files into a new directory. By convention, the package name declared in the files should match the directory name (e.g., files in the `mathlib` directory should start with `package mathlib`).
- **Module Initialization:** In your project's root directory, you must initialize a Go module. The command `go mod init example.com` creates a `go.mod` file and establishes a unique **module path** (`example.com`). This path serves as the absolute root for all import paths within that module.

>[!example] Custom Package Code The following code defines a `mathlib` package with both exported and unexported members.

```go
// In file: mathlib/math.go
package mathlib

// Money is an exported variable.
var Money = 100

// Add returns the sum of two integers.
func Add(x, y int) int {
    return x + y
}

// subtract is unexported and only visible within the mathlib package.
func subtract(x, y int) int {
    return x - y
}
```

--------------------------------------------------------------------------------

## 📥 Using a Custom Package

After creating a package and initializing the module, its exported members become available for use in any other package within the module.

- **Import the Package:** Use the `import` keyword to make the custom package available. The import path is a combination of the module path from `go.mod` and the package's directory path. Note that standard library packages (like `"fmt"`) are imported directly by name, while your custom packages must be prefixed with the module path (e.g., `"example.com/mathlib"`).
- **Access Exported Members:** Use dot notation with the package name to access its exported identifiers (e.g., `mathlib.Add(5, 4)` or `mathlib.Money`).
- **Unexported Members are Inaccessible:** Attempting to access an unexported identifier like `mathlib.subtract` from outside its package will result in a compile-time error, as it is considered private to the `mathlib` package.

>[!example] Consuming the Custom Package The `main.go` file imports and uses the `mathlib` package.

```go
// In file: main.go
package main

import (
    "fmt"
    "example.com/mathlib" // Import the custom package
)

func main() {
    // Accessing the exported function
    sum := mathlib.Add(7, 4)
    fmt.Printf("Sum: %d\n", sum) // Output: Sum: 11

    // Accessing the exported variable
    fmt.Printf("Available Money: %d\n", mathlib.Money) // Output: Available Money: 100

    // This would cause a compile error because 'subtract' is unexported:
    // diff := mathlib.subtract(7, 4)
    // fmt.Println(diff)
}
```

--------------------------------------------------------------------------------

>[!summary] In Go, capitalization is the sole mechanism for controlling public (exported) versus private (unexported) scope between packages. The `go mod init` command is essential for creating a module that allows Go to manage and locate your custom packages.

```go
// --- File: go.mod ---
// This file is created by running `go mod init example.com` in the project root.
// module example.com
//
// go 1.21.0


// --- File: mathlib/math.go ---
package mathlib

// Money is an exported variable.
var Money = 100

// Add returns the sum of two integers.
func Add(x, y int) int {
	return x + y
}

// subtract is unexported and only visible within the mathlib package.
func subtract(x, y int) int {
	return x - y
}


// --- File: main.go ---
package main

import (
	"fmt"
	"example.com/mathlib" // Import the custom package
)

func main() {
	// Accessing the exported function
	sum := mathlib.Add(7, 4)
	fmt.Printf("Sum: %d\n", sum) // Output: Sum: 11

	// Accessing the exported variable
	fmt.Printf("Available Money: %d\n", mathlib.Money) // Output: Available Money: 100

	// This would cause a compile error because 'subtract' is unexported:
	// diff := mathlib.subtract(7, 4)
	// fmt.Println(diff)
}
```

#Go #Programming #PackageScope #QuickNotes