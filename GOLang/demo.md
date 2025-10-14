https://www.youtube.com/watch?v=xV5yj1mpDs4&list=PLpCqPSEm2Xe8sEY2haMDUVgwbkIs5NCJI&index=16&pp=iAQB
### 🧠 What is Package Scope?

- Package scope determines the visibility of variables, constants, and functions within a Go package.
- Identifiers declared at the package level are accessible throughout the same package.
- Use uppercase for exported (public) identifiers, lowercase for unexported (private) ones.

> [!note]  
Package scope helps organize code and control access, making programs modular and maintainable.

---

### ⚡ Declaring Package-Level Identifiers

- Declare variables, constants, and functions outside any function, at the top of your Go file.
- Example:
  ```go
  package main

  var Version = "1.0"      // Exported
  var debugMode = true     // Unexported

  func main() {
      // Access both Version and debugMode here
  }
  ```
- Exported identifiers (start with uppercase) are accessible from other packages.
- Unexported identifiers (start with lowercase) are only accessible within the same package.

> [!tip]  
Use exported identifiers for APIs and libraries, and unexported ones for internal logic.

---

### 🟩 Common Use Cases

- Configuration variables shared across multiple files in a package.
- Utility functions used by several components within the package.
- Constants for error messages or settings.

---

### 🚧 Cautions & Best Practices

- Avoid excessive use of package-level variables to prevent unwanted dependencies.
- Name exported identifiers clearly to avoid conflicts in importing packages.
- Do not rely on package-level state for concurrency-sensitive code.

> [!warning]  
Global state at package scope can lead to bugs in concurrent programs.

---

### 📝 Recap Table

| Concept                | Scope         | Example Identifier | Accessible From         |
|------------------------|--------------|--------------------|------------------------|
| Exported (Public)      | Package-wide | Version            | Other packages         |
| Unexported (Private)   | Package-wide | debugMode          | Same package only      |

---
