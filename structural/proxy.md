The **Proxy Pattern** is a structural design pattern that provides a surrogate or placeholder for another object. It controls access to the original object, adding additional behavior like access control, lazy initialization, logging, etc.

---

### **What is the Proxy Pattern?**

- **Control Access**: Acts as an intermediary to manage access to the real object.
- **Enhancement**: Adds extra functionality without modifying the original object.

---

### **Why Use the Proxy Pattern?**

1. **Lazy Initialization**: Delay resource-intensive object creation until needed.
2. **Access Control**: Restrict access to sensitive objects.
3. **Logging/Monitoring**: Track requests to the real object.
4. **Remote Access**: Represent an object in a different address space.

---

### **Go Implementation**

Go can implement the Proxy Pattern using interfaces and structs to encapsulate the real object.

---

#### **Example: Database Access with a Logging Proxy**

```go
package main

import "fmt"

// Subject Interface
type Database interface {
	Query(query string) string
}

// RealSubject
type RealDatabase struct{}

func (db *RealDatabase) Query(query string) string {
	// Simulate a real database query
	return fmt.Sprintf("Result for query: %s", query)
}

// Proxy
type LoggingProxy struct {
	realDatabase *RealDatabase
}

func NewLoggingProxy() *LoggingProxy {
	return &LoggingProxy{
		realDatabase: &RealDatabase{},
	}
}

func (proxy *LoggingProxy) Query(query string) string {
	fmt.Printf("Logging: Executing query: %s\n", query)
	return proxy.realDatabase.Query(query)
}

func main() {
	// Client uses the proxy instead of directly accessing the real database
	db := NewLoggingProxy()
	result := db.Query("SELECT * FROM users")
	fmt.Println(result)

	result = db.Query("SELECT * FROM orders")
	fmt.Println(result)
}
```

---

### **Explanation**

1. **Subject Interface**: `Database` defines the operations (`Query`) that the proxy and real database will implement.
2. **RealSubject**: `RealDatabase` is the actual object that performs the database query.
3. **Proxy**: `LoggingProxy` adds logging functionality while delegating the actual work to `RealDatabase`.
4. **Client**: Interacts with the `LoggingProxy`, unaware of the `RealDatabase`.

---

### **Types of Proxy**

1. **Remote Proxy**: Provides access to an object in a different address space.
2. **Virtual Proxy**: Manages resource-intensive objects, loading them only when needed.
3. **Protection Proxy**: Controls access to sensitive operations or data.
4. **Logging/Monitoring Proxy**: Adds logging, caching, or monitoring functionality.

---

### **Advantages**

1. **Additional Behavior**: Add functionality like logging or access control without modifying the real object.
2. **Lazy Initialization**: Delay resource-intensive operations until needed.
3. **Access Control**: Protect sensitive objects or operations.

---

### **Disadvantages**

1. **Increased Complexity**: Adds an additional layer to the system.
2. **Performance Overhead**: Proxy introduces an extra level of indirection.

---

### **When to Use the Proxy Pattern?**

- When you want to control access to a resource.
- When you need logging, caching, or monitoring for an object.
- When the real object is expensive to create or located in a remote system.
