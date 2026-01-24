# Study Notes: Building a 'GET Products' REST API in Golang

These notes break down the process of creating a foundational REST API endpoint in Go for a demonstration e-commerce project. The focus is on the essential steps required to handle a `GET` request to fetch a list of products, structure the data, and serve it to a client application.

--------------------------------------------------------------------------------

### 1.0 Core Concepts: REST APIs and HTTP Methods

Before writing a single line of Go, it's critical to lock down the core principles that govern how modern web services communicate. A strategic understanding of REST architecture and fundamental HTTP methods is the absolute foundation for building any reliable and predictable backend.

#### 1.1 Project Goal

The primary objective is to build a backend service that can supply data for an e-commerce application. The key goals are:

- Develop a backend in Go using REST principles.
- Serve data to a pre-existing React frontend for demonstration purposes.
- Implement the first API endpoint, which handles a `GET` request to fetch a complete list of products.

#### 1.2 REST & HTTP Fundamentals

[!note] What is a REST API? REST (Representational State Transfer) is an architectural style, not a rigid protocol. A REST API is a web service that adheres to this architectural style, providing a structured way for systems to communicate over HTTP. It revolves around the concept of resources, which are identified by URLs.

Interaction with these resources is managed through a set of standard HTTP methods. While many methods exist, a small set of five primary verbs—GET, POST, PUT, PATCH, and DELETE—form the backbone of virtually all modern web applications. Mastering their purpose is the first major step toward effective backend development.

- **GET:** Retrieve resources or data from the server.
- **POST:** Create a new resource on the server.
- **PUT:** Update an existing resource completely.
- **PATCH:** Partially update an existing resource.
- **DELETE:** Remove a resource from the server.

With these core concepts established, we can proceed to the practical implementation of our first endpoint.

--------------------------------------------------------------------------------

### 2.0 API Endpoint Implementation: Step-by-Step

Now that we've covered the theory, let's get our hands dirty. This section walks through the tangible code required to bring our endpoint to life, from capturing the incoming request to executing our logic.

#### 2.1 Defining the Route and Handler

The first step in building an API is to define a URL path (a "route") and associate it with a specific function (a "handler") that will execute when a client requests that path. For this project, the `/products` route is mapped to the `GetProducts` handler function.

```cpp
mux.HandleFunc("/products", GetProducts)
```

#### 2.2 The Handler Function Signature

The handler function, `GetProducts`, is the heart of the endpoint. It accepts two critical parameters provided by Go's `net/http` package: `http.ResponseWriter` and `*http.Request`.

- `w http.ResponseWriter`: This is an interface used to _write_ the HTTP response back to the client. This includes setting headers, status codes, and the response body (e.g., JSON data).
- `r *http.Request`: This is a pointer to a struct that contains all the information about the incoming HTTP _request_ from the client, such as the request method (GET, POST, etc.), URL, and any data sent by the client.

[!example] The "Mango" Analogy To clarify the difference between a requested resource and the information within that request, consider an analogy:

- A client asks for "mangoes." The mango itself is the **resource**, identified by the URL path (e.g., `/mangoes`).
- The client specifies "10kg." This is **information** about the request, which would be contained within the `*http.Request` (`r`) object.
- The server packages the mango information (price, origin, etc.) and sends it back. This is the **response**, written using the `http.ResponseWriter` (`w`).

#### 2.3 Validating the Request Method

It is crucial to ensure that an endpoint only responds to the intended HTTP method. Since this handler is designed to fetch products, it should only accept `GET` requests. The code checks the request method and returns an error if it is anything other than `GET`.

```cpp
if r.Method != http.MethodGet {
    http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
    return
}
```

If the incoming request method is GET, this `if` condition evaluates to false, and execution continues to the next part of the function. For any other method (like POST or PUT), the condition is true, an error is sent to the client, and the `return` statement immediately stops any further processing.

[!warning] Understanding HTTP Status Codes HTTP status codes are standardized three-digit numbers sent in a response to indicate the outcome of a request. They are a critical part of API communication. Key examples include:

- `200 OK`: The request was successful.
- `201 Created`: A new resource was successfully created (typically in response to a `POST` request).
- `400 Bad Request`: The server cannot process the request due to a client-side error (e.g., malformed data).
- `404 Not Found`: The requested resource could not be found on the server.
- `500 Internal Server Error`: A generic error indicating an unexpected condition on the server that prevented it from fulfilling the request.

After validating the request, the next step is to prepare and send the product data.

--------------------------------------------------------------------------------

### 3.0 Managing and Serving Product Data

With our request validated, the handler's next job is to fetch our application data, package it correctly, and ship it back to the client as a clean JSON response.

#### 3.1 Defining the Data Structure (`Product` Struct)

In Go, a `struct` is used to define a custom data type by grouping together related fields. For this project, a `Product` struct is defined to model the properties of an e-commerce product.

```cpp
type Product struct {
    ID          int
    Title       string
    Description string
    Price       float64
    ImageURL    string
}
```

[!tip] Go's Visibility Rule In Go, the visibility of a struct field (or any identifier) is determined by its case.

- **Capitalized** fields (e.g., `Title`) are considered "public" or "exported." This means they can be accessed by other packages, which is essential for the standard `encoding/json` package to see and process them.
- **lowercase** fields are considered "private" or "unexported" and can only be accessed from within the same package.

#### 3.2 Creating Mock Data

For this initial implementation, a static list of products serves as a mock database. A global slice named `ProductList` is declared, and Go's special `init()` function is used to pre-populate this slice with sample product data when the program starts. The `init()` function is guaranteed to run before `main()`.

#### 3.3 Responding with JSON

The final step in the handler is to convert the Go slice of `Product` structs into the JSON format that web clients expect. This is accomplished using the standard library's `encoding/json` package. The line `json.NewEncoder(w).Encode(ProductList)` is a two-step process: first, `json.NewEncoder(w)` creates an encoder instance configured to stream its output directly to our `http.ResponseWriter`. Then, the `.Encode(ProductList)` method is called on that encoder to perform the conversion and send the data.

```cpp
json.NewEncoder(w).Encode(ProductList)
```

This sends the data, but the format of the resulting JSON keys can be further refined.

--------------------------------------------------------------------------------

### 4.0 Customizing JSON Output with Struct Tags

Controlling the exact structure and naming convention of JSON fields is crucial for API consistency and ensuring compatibility with client-side applications. Let's explore a common problem and Go's elegant solution.

#### 4.1 The Naive Approach: Lowercase Fields

By default, the `encoding/json` package uses our capitalized struct field names as keys in the JSON output (e.g., `"Title"`). A common goal is to use camelCase (e.g., `"title"`). A natural first attempt might be to simply change the struct field itself to be lowercase.

Let's try that with the `ID` field:

```cpp
type Product struct {
    id          int // Changed from ID to id
    Title       string
    Description string
    Price       float64
    ImageURL    string
}
```

When we run this code and check the API response, we discover a surprising result: the `id` field is now completely missing from the JSON output. This happens because, as we learned in the "Go's Visibility Rule" tip, a lowercase field is **private**. The `encoding/json` package, being an external package, does not have permission to access this private field and therefore omits it entirely. This is a critical learning moment: we need a way to keep our fields public (Capitalized) for Go's tooling while controlling the output format for external clients.

#### 4.2 The Solution: JSON Struct Tags

The correct way to solve this is with struct tags. Tags are metadata annotations added to struct fields that provide instructions to packages like `encoding/json`. To control the JSON key name, we use a `json` tag.

The syntax for a JSON struct tag is: `` `json:"id"` ``.

[!note] Common JSON Naming Conventions

- **camelCase**: The first word is lowercase, and the first letter of each subsequent word is capitalized (e.g., `imageURL`).
- **snake_case**: All words are lowercase and separated by underscores (e.g., `image_url`).

Your choice often depends on team or project conventions.

#### 4.3 Updated `Product` Struct with Tags

By adding JSON struct tags to each public field, we can instruct the encoder to use a specific key name in the output, achieving the desired camelCase format without making our fields private.

```cpp
type Product struct {
    ID          int     `json:"id"`
    Title       string  `json:"title"`
    Description string  `json:"description"`
    Price       float64 `json:"price"`
    ImageURL    string  `json:"imageURL"`
}
```

With the backend now producing well-formatted JSON, the final step is to ensure the frontend can access it.

--------------------------------------------------------------------------------

### 5.0 Frontend Integration and CORS

After the API endpoint is functioning correctly, a final but critical step is to configure it to communicate with web applications running on different domains or ports. This involves understanding and handling Cross-Origin Resource Sharing (CORS).

#### 5.1 The CORS Error

After confirming our endpoint works correctly using a tool like Postman, a new problem arises when integrating with a web frontend: the browser blocks the request with a CORS error. This happens because Postman, as a server-side tool, doesn't enforce the same cross-origin policies that browsers do.

[!warning] Cross-Origin Resource Sharing (CORS) Error CORS is a security feature built into web browsers that prevents a web page from making requests to a different "origin" (a combination of protocol, domain, and port) than the one that served the page. For example, a request from a React frontend running on `localhost:3000` to a Go backend on `localhost:8080` would be blocked by the browser by default.

#### 5.2 The Fix: Setting Response Headers

The solution is server-side. The backend API must include specific HTTP headers in its response to explicitly tell the browser that requests from other origins are permitted. The two key headers to set are `Access-Control-Allow-Origin` and `Content-Type`.

```cpp
// Allow requests from any origin
w.Header().Set("Access-Control-Allow-Origin", "*")

// Set the content type to application/json
w.Header().Set("Content-Type", "application/json")
```

Setting `Access-Control-Allow-Origin` to `*` is a permissive setting suitable for public APIs or development. In production, it is often restricted to specific, known origins.

Additionally, setting the `Content-Type` header to `application/json` is crucial. It informs the client what kind of data to expect, allowing tools like Postman and web browsers to automatically parse and format the JSON response correctly, avoiding the need for manual interpretation of a plain text stream.

--------------------------------------------------------------------------------

### 6.0 Complete Code Example

The following code block contains the complete, runnable Go program, combining all the concepts discussed in these notes into a single file.

```cpp
package main

import (
	"encoding/json"
	"log"
	"net/http"
)

// Product defines the structure for an API product
type Product struct {
	ID          int     `json:"id"`
	Title       string  `json:"title"`
	Description string  `json:"description"`
	Price       float64 `json:"price"`
	ImageURL    string  `json:"imageURL"`
}

// ProductList is a slice to store the products (acting as a mock DB)
var ProductList []Product

// The init function runs before main() and is used here to seed data
func init() {
	prod1 := Product{
		ID:          1,
		Title:       "Orange",
		Description: "An orange is a fruit of various citrus species.",
		Price:       100.50,
		ImageURL:    "https://example.com/orange.jpg",
	}
	prod2 := Product{
		ID:          2,
		Title:       "Apple",
		Description: "A green apple.",
		Price:       140.00,
		ImageURL:    "https://example.com/apple.jpg",
	}
	ProductList = append(ProductList, prod1, prod2)
}

// GetProducts is the handler for the /products endpoint
func GetProducts(w http.ResponseWriter, r *http.Request) {
	// Set headers to handle CORS and content type
	w.Header().Set("Access-Control-Allow-Origin", "*")
	w.Header().Set("Content-Type", "application/json")

	// Check if the request method is GET
	if r.Method != http.MethodGet {
		http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
		return
	}

	// Encode the product list into JSON and write to the response
	err := json.NewEncoder(w).Encode(ProductList)
	if err != nil {
		http.Error(w, "Failed to encode products", http.StatusInternalServerError)
	}
}

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/products", GetProducts)

	log.Println("Server running on port 8080")
	err := http.ListenAndServe(":8080", mux)
	if err != nil {
		log.Fatal("Server failed to start: ", err)
	}
}
``` 