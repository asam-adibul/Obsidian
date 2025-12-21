# Golang Study Notes: Slices

### **1. The Importance of Slices in Go**

- Slices are one of the most fundamental and powerful data structures in Go. They are also one of the most frequently tested concepts in technical interviews.
- A deep and precise understanding of slice internals—how they are structured, how they grow, and how they relate to underlying arrays—is crucial for passing Go engineering interviews, writing efficient, bug-free code, and avoiding common pitfalls that even experienced engineers fall for.

>[!note] Slices are the most common source of interview questions in Go. Mastering them is non-negotiable.

### **2. What is a Slice?**

- A slice is a flexible and powerful "view" into a contiguous block of memory, which is known as an **underlying array**. It provides a dynamic window into the array's elements.
- To use an analogy, if an array is an entire pizza, a slice is a single piece of that pizza. It's a portion of the whole, not the whole itself. You can create many different slices from the same pizza.

### **3. The Internal Structure of a Slice**

A slice is not the data itself; it's a small data structure called a "slice header" that describes a section of an array. This header contains three fields:

- **Pointer:** The memory address of the first element of the underlying array that the slice can access. This is where the slice's "view" begins.
- **Length (len):** The number of elements the slice currently contains. This is the count of elements you can access from the pointer. The length cannot exceed the capacity.
- **Capacity (cap):** The total number of elements in the underlying array, counting from the slice's starting **pointer** to the **very end** of that array. It represents the maximum length the slice can reach before a new backing array must be allocated.

>[!example] A slice is like a window onto an array.

- The **pointer** tells you where the window starts.
- The **length** tells you how many items you can see through the window right now.
- The **capacity** tells you how many items there are in total from the start of your window to the very end of the array.

>[!tip] The Golden Rule of Slices The key to solving any slice-related problem, especially in an interview, is to mentally track the state of these three fields—**pointer, length, and capacity**—after every single operation. If you can do this accurately, you can predict the output of any slice manipulation with 100% confidence.

### **4. Creating Slices**

There are several common ways to create a slice in Go, each suited for different scenarios.

#### **4.1 From an Existing Array**

- You can create a slice by specifying a half-open range `[low:high]` on an array. This creates a slice that includes the element at the `low` index up to, but not including, the element at the `high` index.

```go
// Create an array
underlyingArray := [6]string{"This", "is", "a", "Go", "interview", "question"}

// Create a slice from the array (index 1 up to, but not including, index 4)
mySlice := underlyingArray[1:4] // Contains {"is", "a", "Go"}
```

#### **4.2 From Another Slice**

- You can also "re-slice" an existing slice. This creates a new slice header that points to the same underlying array as the original slice, but potentially with a different pointer, length, and capacity.

```go
// mySlice from the previous example
// Create a new slice from mySlice
subSlice := mySlice[1:2] // Contains {"a"}
```

#### **4.3 Using a Slice Literal**

- This is the most common way to create a slice when you know the initial values. Go automatically creates an underlying array of the correct size and returns a slice that points to it.
- With a slice literal, the length and capacity are equal to the number of elements provided.

```go
// Create a slice using a literal
// Go creates an array of size 3 and a slice header pointing to it.
literalSlice := []int{1, 2, 5}
// len(literalSlice) is 3, cap(literalSlice) is 3
```

#### **4.4 Using the** `**make**` **Function**

- The built-in `make` function is used to create slices with a specified length and, optionally, capacity. The elements are zero-initialized (e.g., `0` for integers, `""` for strings).
- **With Length Only:**
    - `make([]T, len)` creates a slice where both the length and capacity are equal to `len`.
- **With Length and Capacity:**
    - `make([]T, len, cap)` creates a slice with a specified `len` and a separate, usually larger, `cap`.

#### **4.5 Creating a Nil Slice**

- Declaring a slice variable without initializing it creates a "nil slice."
- A nil slice is perfectly usable. Its header contains a nil pointer, and its length and capacity are both 0.

```go
var nilSlice []int
// pointer is nil, len is 0, cap is 0
```

### **5. Core Operations: Appending to a Slice**

The built-in `append` function is the primary way to add elements to a slice. It's a critical function to understand because its behavior depends on the slice's capacity.

#### **5.1 How** `**append**` **Works**

- **Scenario 1: Sufficient Capacity:** If the underlying array has enough capacity (`len < cap`), the element is added directly into the array after the existing elements. The slice's length is incremented, but the capacity remains unchanged. The function returns a new slice header that points to the _same_ underlying array.
- **Scenario 2: Insufficient Capacity:** If the capacity is not sufficient (`len == cap`), `append` triggers a critical multi-step process:
    1. **Allocate:** Go allocates a **new, larger underlying array** based on its growth strategy.
    2. **Copy:** It meticulously copies all elements from the old array to the new one.
    3. **Append:** It adds the new element(s) to the end of this new array.
    4. **Return:** It returns a **new slice header** whose pointer targets the new array, with an updated length and capacity. This is why you must always re-assign the result of `append` (e.g., `mySlice = append(mySlice, ...)`).

#### **5.2 Capacity Growth Strategy**

When Go needs to allocate a new, larger array, it follows a specific growth strategy to optimize performance and minimize the number of future allocations.

[!tip] **Slice Growth Rules**

- **If the original capacity is less than 1024:** The runtime **typically doubles** the capacity (new_cap ≈ 2 * old_cap).
- **If the original capacity is 1024 or more:** The runtime increases the capacity by **approximately 25%** (new_cap ≈ 1.25 * old_cap). This is an internal implementation detail and may vary slightly between Go versions.

### **6. Common Pitfalls and Key Behaviors**

#### **6.1 Shared Arrays and Unintended Side Effects**

This is the single most important concept to master. It is the number one source of tricky interview questions and subtle production bugs. Think of it this way: if the original array is your body, a slice might be your hand. If you then create a sub-slice to represent a ring on your finger, that ring is part of your hand, which is part of your body. Modifying the ring affects the hand, and both are still connected to the original source. The underlying data is the same.

[!warning] When you slice an existing array or slice, the new slice **shares the same underlying data**. Modifying an element through one slice will be visible in the other. This relationship is only broken when an `append` operation forces the allocation of a completely new underlying array for one of the slices.

```go
original := []int{10, 20, 30, 40}
slice1 := original[0:2] // {10, 20}, points to the same array
slice2 := original[1:3] // {20, 30}, points to the same array

// Modify the underlying data via slice2
// slice2[0] corresponds to original[1]
slice2[0] = 99

// The change is visible in both the original slice and slice1 because they
// all share the same underlying data.
// original is now {10, 99, 30, 40}
// slice1 is now {10, 99}
```

#### **6.2 Index Out of Range Error**

- You can only access or set elements of a slice using an index from `0` up to `length - 1`. Attempting to access an index equal to or greater than the length will result in a panic. This error occurs because Go's runtime bounds checking for direct index access (`mySlice[i]`) is performed strictly against the slice's **length**, not its capacity. The capacity only determines the space available for growth via `append`.
- To use the available capacity, you must grow the slice's length using the `append` function.

```go
// len=3, cap=5
mySlice := make([]int, 3, 5)

// This is valid because index 2 is within the length (0, 1, 2)
mySlice[2] = 100

// This causes a panic: runtime error: index out of range [3] with length 3
// Even though capacity is 5, you cannot directly access index 3.
// You must use `append` to grow the length.
// mySlice[3] = 200 // THIS WILL CRASH
```

### **7. Related Concept: Variadic Functions**

- Variadic functions in Go provide a convenient way to accept an arbitrary number of arguments of the same type. Internally, Go implements this by treating the variadic arguments as a slice.

```go
// This function accepts zero or more integers.
// Inside the function, `numbers` is treated as a slice: []int.
func sum(numbers ...int) int {
    total := 0
    for _, num := range numbers {
        total += num
    }
    return total
}

// Call examples
sum(1, 2)
sum(5, 10, 15, 20)
```