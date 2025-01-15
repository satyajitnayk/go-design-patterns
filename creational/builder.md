## **What is the Builder Pattern?**

The Builder Pattern is a creational design pattern that provides a step-by-step approach to construct complex objects. Instead of creating objects in a single step, the Builder Pattern separates the construction logic into discrete methods, allowing the creation of objects with various configurations.

---

## **Why Use the Builder Pattern?**

1. **Handles Complexity**: Simplifies the creation of objects that require many parameters or complex initialization steps.
2. **Custom Configurations**: Allows for creating different representations of an object using the same construction process.
3. **Improved Readability**: Code becomes easier to understand, especially when constructing complex objects.
4. **Encapsulation**: Hides the details of the object creation process from the client.

---

## **Example Use Case**

Imagine building a custom **HTTP request**. The request might require various optional fields like headers, query parameters, and a body. The Builder Pattern can streamline this process.

---

## **Go Implementation**

### **Step 1: Define the Product**

The `HTTPRequest` represents the complex object being built.

```go
package main

import (
	"fmt"
	"strings"
)

type HTTPRequest struct {
	Method  string
	URL     string
	Headers map[string]string
	Body    string
}

func (r *HTTPRequest) String() string {
	return fmt.Sprintf(
		"Method: %s\nURL: %s\nHeaders: %v\nBody: %s\n",
		r.Method, r.URL, r.Headers, r.Body,
	)
}
```

---

### **Step 2: Create the Builder**

The `HTTPRequestBuilder` provides methods to set various parts of the `HTTPRequest`.

```go
type HTTPRequestBuilder struct {
	method  string
	url     string
	headers map[string]string
	body    string
}

func NewHTTPRequestBuilder() *HTTPRequestBuilder {
	return &HTTPRequestBuilder{headers: make(map[string]string)}
}

func (b *HTTPRequestBuilder) SetMethod(method string) *HTTPRequestBuilder {
	b.method = method
	return b
}

func (b *HTTPRequestBuilder) SetURL(url string) *HTTPRequestBuilder {
	b.url = url
	return b
}

func (b *HTTPRequestBuilder) AddHeader(key, value string) *HTTPRequestBuilder {
	b.headers[key] = value
	return b
}

func (b *HTTPRequestBuilder) SetBody(body string) *HTTPRequestBuilder {
	b.body = body
	return b
}

func (b *HTTPRequestBuilder) Build() *HTTPRequest {
	return &HTTPRequest{
		Method:  b.method,
		URL:     b.url,
		Headers: b.headers,
		Body:    b.body,
	}
}
```

---

### **Step 3: Client Code**

The client uses the builder to create an `HTTPRequest` with custom configurations.

```go
func main() {
	builder := NewHTTPRequestBuilder()

	request := builder.
		SetMethod("POST").
		SetURL("https://example.com/api").
		AddHeader("Content-Type", "application/json").
		AddHeader("Authorization", "Bearer token123").
		SetBody(`{"key": "value"}`).
		Build()

	fmt.Println(request)
}
```

---

## **Key Points**

1. **Step-by-Step Construction**: The builder allows adding only the desired parts (e.g., headers, body) without requiring everything at once.
2. **Fluent API**: Method chaining (`.SetMethod().SetURL()`) improves readability.
3. **Custom Objects**: You can use the same builder to create different HTTP requests by varying the method calls.
4. **Encapsulation**: The complex creation logic is hidden from the client, ensuring a clean and maintainable design.

---

## **Real-World Applications**

1. **Database Query Builders**: Build complex SQL queries dynamically.
2. **Form Generators**: Create dynamic forms with various input fields and configurations.
3. **Report Generators**: Configure reports with headers, footers, and content blocks.
