# Study Notes: Understanding Structs in Go

### 1.0 Introduction: What is a Struct?

In Go, structs are the cornerstone of building complex, custom data types. While the language provides fundamental built-in types like `int` and `string`, real-world applications require us to group related data into more meaningful structures. A user's profile, for example, isn't just a name or an age; it's a collection of both. Structs provide the mechanism to create these custom data aggregates cleanly and efficiently, forming the basis for clear and maintainable code.

The fundamental purpose of a struct can be broken down as follows:

- **Definition:** A struct is a composite, user-defined type that aggregates different data fields.
- **Function:** Its primary function is to group related data into a single, cohesive unit. For instance, a `User` struct can bundle a `Name` (a `string`) and an `Age` (an `int`) into one logical entity.
- **Analogy:** Just as Go provides built-in types, structs empower you to define your own custom types tailored to the specific needs of your program.

```markdown
[!note]
Structs are blueprints for creating custom data structures. They don't hold data themselves; they define the shape of the data.
```

With this foundational understanding, let's examine the practical syntax for defining a struct in Go, which is the first step toward the critical concept of creating live data from these blueprints.

### 2.0 Defining a Struct: The Blueprint

Before you can create a variable of a custom type, you must first define its structure, or "blueprint." This definition is a compile-time construct that tells the Go compiler what fields an instance of this type will have and what their respective data types are.

The syntax for defining a struct follows a clear pattern:

- You begin with the `type` keyword to signal a new type definition.
- Next comes the name for your new custom type (e.g., `User`). By convention, names are capitalized to make them exported and accessible to other packages.
- This is followed by the `struct` keyword, indicating the kind of type being defined.
- Finally, a pair of curly braces `{}` encloses the list of fields. Each field has a name and a type.

Here is a practical example of defining a `User` struct:

```go
type User struct {
    Name string
    Age  int
}
```

```markdown
[!tip] Key Terminology: Properties
The fields inside a struct, like `Name` and `Age`, are called **properties** or **member variables**. They define the data that any instance of the struct will hold.
```

Once this blueprint is defined, we can use it to create actual data objects, known as instances.

### 3.0 Creating and Using Struct Instances

After defining a struct blueprint, you can create concrete values from it. This process is called **instantiation**, and the resulting value is referred to as an **instance** or an **object**. Each instance is a distinct piece of data in memory that conforms to the shape defined by the struct.

#### 3.1 Creating an Instance

You can declare and initialize a struct instance in a few ways. Go offers both an explicit, multi-line declaration and a more common shorthand syntax.

1. **Explicit Declaration:** You can first declare a variable of the struct type and then assign it an initialized value.
2. **Shorthand Declaration:** A more concise approach is to use the `:=` shorthand operator, which declares and initializes the variable in a single step. Go's compiler is smart enough to automatically infer the type, making this the most common and concise way to create struct instances.

#### 3.2 Accessing Properties

To read or modify the data within a struct instance, you use the dot (`.`) operator followed by the property name.

```go
fmt.Println(user1.Name) // Accesses the Name property of the user1 instance
```

Understanding how to create and access these instances is crucial, but to prevent future bugs, we must now turn to the most important concept of all: how these instances exist independently from one another in memory.

### 4.0 Structs in Memory: The Most Important Concept

To prevent subtle but significant bugs, we must understand the single most important concept about structs: the relationship between the definition and its instances in memory. As the source instructor emphasizes, this is the area where new programmers make the most mistakes.

- **The Blueprint (Compile-Time):** The `type User struct { ... }` definition is static, read-only information. During compilation, this blueprint is stored in the application's "code segment." It is not a live piece of data that can be changed at runtime; it is simply a definition.
- **The Instances (Execution-Time):** When your program runs and a function is called, variables like `user1` and `user2` are created. Each one is a separate instance allocated its own block of memory on the function's stack frame. This memory block is structured according to the blueprint and holds the actual values for that specific instance (e.g., "Habib" and `30` for `user1`).

```markdown
[!warning] Caution: Instances are Independent
Creating `user2` does **not** modify or overwrite `user1`. A common mistake is to think the `User` blueprint itself holds the data, and that creating a new instance overwrites the values within that single blueprint. This is incorrect. Each instance gets its own unique block of memory. Think of two people at a restaurant who both order biryani. They receive two separate, physical plates of food. Although the *type* of food is the same, the plates are independent. One person's actions with their plate do not affect the other's.
```

The following code example will put these principles into practice, demonstrating their independent nature.

### 5.0 Complete Code Example

The following program combines all the concepts discussed. It defines the `User` struct, creates two independent instances (`user1` and `user2`), and accesses their properties to demonstrate that they hold different data in separate memory locations.

```go
package main

import "fmt"

type User struct {
    Name string
    Age  int
}

func main() {
    // Create the first instance of User
    user1 := User{
        Name: "Habib",
        Age:  30,
    }

    // Print properties of the first instance
    fmt.Println("Name:", user1.Name)
    fmt.Println("Age:", user1.Age)

    // Create the second instance of User
    user2 := User{
        Name: "Roki",
        Age:  16,
    }

    // Print properties of the second instance
    fmt.Println("Name:", user2.Name)
    fmt.Println("Age:", user2.Age)
}
```

**Expected Output:**

```
Name: Habib
Age: 30
Name: Roki
Age: 16
```

The output clearly shows that `user1` and `user2` are separate entities, each maintaining its own distinct state. To solidify this understanding, let's recap the key terminology.

### 6.0 Key Terminology Recap

This section serves as a quick-reference glossary for the essential terms related to Go structs.

- **Struct**: The blueprint or template for a custom data type. It is a **compile-time definition** stored in the application's code segment and does not hold any data itself.
- **Property / Member Variable**: The individual fields or variables defined within a struct (e.g., `Name`, `Age`).
- **Instance / Object**: A concrete variable created from a struct's blueprint. It is the actual data that exists in memory **at run-time**, typically on a function's stack frame. Each instance is independent and has its own allocated memory.
- **Instantiate**: The process of creating an instance (or object) of a struct type.