The **Chain of Responsibility** pattern is a behavioral design pattern that allows a request to pass through a chain of handlers. Each handler decides whether to process the request or pass it to the next handler in the chain.

---

### **What is the Chain of Responsibility Pattern?**

- **Decoupling**: The sender of a request is decoupled from its receiver(s).
- **Chain**: Multiple handlers are linked in a chain, and each handler decides whether to process the request.
- **Responsibility**: Each handler focuses only on its specific task.

---

### **Why Use the Chain of Responsibility?**

1. **Flexible Handling**: Easily add/remove/modify handlers without affecting the overall flow.
2. **Dynamic Chains**: Construct chains at runtime for dynamic request processing.
3. **Single Responsibility**: Each handler has a specific task, promoting clean code.

---

### **Go Implementation**

---

#### **Example: Support Ticket Processing**

```go
package main

import "fmt"

// Handler interface
type Handler interface {
	SetNext(handler Handler)
	Handle(request string)
}

// BaseHandler struct (provides default implementation for chaining)
type BaseHandler struct {
	next Handler
}

func (b *BaseHandler) SetNext(handler Handler) {
	b.next = handler
}

func (b *BaseHandler) Handle(request string) {
	if b.next != nil {
		b.next.Handle(request)
	}
}

// Concrete Handlers
type LevelOneSupport struct {
	BaseHandler
}

func (h *LevelOneSupport) Handle(request string) {
	if request == "password reset" {
		fmt.Println("Level 1 Support: Handling password reset request.")
	} else {
		fmt.Println("Level 1 Support: Passing to Level 2.")
		h.BaseHandler.Handle(request)
	}
}

type LevelTwoSupport struct {
	BaseHandler
}

func (h *LevelTwoSupport) Handle(request string) {
	if request == "network issue" {
		fmt.Println("Level 2 Support: Handling network issue request.")
	} else {
		fmt.Println("Level 2 Support: Passing to Level 3.")
		h.BaseHandler.Handle(request)
	}
}

type LevelThreeSupport struct {
	BaseHandler
}

func (h *LevelThreeSupport) Handle(request string) {
	fmt.Println("Level 3 Support: Handling advanced request:", request)
}

func main() {
	// Create handlers
	level1 := &LevelOneSupport{}
	level2 := &LevelTwoSupport{}
	level3 := &LevelThreeSupport{}

	// Set up the chain
	level1.SetNext(level2)
	level2.SetNext(level3)

	// Pass requests through the chain
	fmt.Println("Request 1: Password Reset")
	level1.Handle("password reset")

	fmt.Println("\nRequest 2: Network Issue")
	level1.Handle("network issue")

	fmt.Println("\nRequest 3: Server Crash")
	level1.Handle("server crash")
}
```

---

### **Explanation**

1. **Handler Interface**: Defines methods for setting the next handler (`SetNext`) and handling requests (`Handle`).
2. **BaseHandler**: Provides a common implementation for chaining to the next handler.
3. **Concrete Handlers**: Specific handlers (`LevelOneSupport`, `LevelTwoSupport`, `LevelThreeSupport`) perform the work or pass the request along.
4. **Chain Setup**: Handlers are linked together using `SetNext`.
5. **Request Handling**: A request passes through the chain until it’s handled.

---

### **Output**

```
Request 1: Password Reset
Level 1 Support: Handling password reset request.

Request 2: Network Issue
Level 1 Support: Passing to Level 2.
Level 2 Support: Handling network issue request.

Request 3: Server Crash
Level 1 Support: Passing to Level 2.
Level 2 Support: Passing to Level 3.
Level 3 Support: Handling advanced request: server crash
```

---

### **Advantages**

1. **Decoupled Logic**: The sender does not know which handler will process the request.
2. **Extensibility**: Easily add or remove handlers without breaking the chain.
3. **Focus**: Handlers are specific to their task.

---

### **Disadvantages**

1. **Performance Overhead**: Long chains may slow down request processing.
2. **No Guarantee**: If no handler processes the request, it may go unhandled.

---

### **When to Use the Chain of Responsibility?**

- When multiple handlers could process a request.
- When the order of processing matters.
- When you want to decouple senders from receivers.
