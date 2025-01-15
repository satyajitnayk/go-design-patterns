The **Decorator Pattern** is a structural design pattern that dynamically adds behavior or responsibilities to objects without altering their structure. This pattern uses composition rather than inheritance to extend functionality.

---

### **What is the Decorator Pattern?**

- **Dynamic Behavior**: Enhances objects dynamically at runtime.
- **Composition Over Inheritance**: Wraps an object with additional behavior by embedding it within a decorator.

---

### **Why Use the Decorator Pattern?**

1. **Flexible Extension**: Add features dynamically without modifying existing classes.
2. **Adheres to Open/Closed Principle**: Allows extensions without changing existing code.
3. **Layered Behavior**: Combine multiple decorators for complex behavior.

---

### **Go Implementation**

Go supports the Decorator Pattern effectively using interfaces and composition.

---

#### **Basic Example: Text Formatting**

```go
package main

import "fmt"

// Component interface
type Text interface {
	Render() string
}

// Concrete Component
type PlainText struct {
	content string
}

func (pt *PlainText) Render() string {
	return pt.content
}

// Decorator: Bold
type BoldText struct {
	text Text
}

func (bt *BoldText) Render() string {
	return "<b>" + bt.text.Render() + "</b>"
}

// Decorator: Italic
type ItalicText struct {
	text Text
}

func (it *ItalicText) Render() string {
	return "<i>" + it.text.Render() + "</i>"
}

func main() {
	plain := &PlainText{content: "Hello, World!"}

	// Apply decorators
	bold := &BoldText{text: plain}
	italic := &ItalicText{text: bold}

	fmt.Println(italic.Render()) // Output: <i><b>Hello, World!</b></i>
}
```

---

### **Realistic Example: HTTP Middleware**

A common use case of the Decorator Pattern is middleware in web frameworks, where request handlers are wrapped with additional functionality like logging or authentication.

```go
package main

import (
	"fmt"
	"net/http"
)

// Basic Handler
func basicHandler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "Hello, World!")
}

// Logging Middleware
func loggingMiddleware(next http.HandlerFunc) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
		fmt.Println("Logging Request:", r.URL.Path)
		next(w, r) // Call the next handler
	}
}

// Authentication Middleware
func authMiddleware(next http.HandlerFunc) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
		if r.Header.Get("Authorization") != "Bearer token123" {
			http.Error(w, "Unauthorized", http.StatusUnauthorized)
			return
		}
		next(w, r) // Call the next handler
	}
}

func main() {
	http.HandleFunc("/", authMiddleware(loggingMiddleware(basicHandler)))
	fmt.Println("Server started at :8080")
	http.ListenAndServe(":8080", nil)
}
```

---

### **Advantages**

1. **Dynamic Behavior**: Add or remove features without changing the original class.
2. **Composable**: Stack multiple decorators to create complex behaviors.
3. **Adheres to SOLID**: Particularly the Open/Closed Principle.

---

### **Disadvantages**

1. **Complexity**: Too many decorators can make code harder to read and debug.
2. **Order Dependency**: The behavior of decorators may depend on their stacking order.

---

### **When to Use the Decorator Pattern?**

- When you need to dynamically add functionality to objects.
- When you want to avoid a complex hierarchy of subclasses.
- When features need to be optional and composable (e.g., middleware in web servers).
