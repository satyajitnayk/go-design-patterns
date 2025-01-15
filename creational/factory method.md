The **Factory Method Pattern** is a creational design pattern used to define an interface for creating objects, but allows subclasses or specific implementations to alter the type of objects that will be created.

---

### **What is the Factory Method Pattern?**

The pattern involves a **creator** class that defines a method for object creation, but the actual instantiation logic is handled by subclasses or specific implementations. This decouples the client code from the concrete classes it needs to instantiate.

---

### **Why Use the Factory Method?**

1. **Encapsulation**: It encapsulates the creation logic, hiding the instantiation details.
2. **Flexibility**: Makes it easier to introduce new types without modifying existing code.
3. **Single Responsibility Principle (SRP)**: The responsibility for object creation is delegated to the factory.
4. **Open/Closed Principle (OCP)**: You can extend the creation process without modifying the existing code.

---

### **Practical Example: Payment Processing**

Suppose you have different payment processors like `PayPal`, `CreditCard`, and `BankTransfer`. Instead of the client knowing how to instantiate these processors, a factory method can handle the creation.

---

### **Go Implementation**

#### **Step 1: Define the Product Interface**

```go
package main

import "fmt"

// PaymentProcessor defines the interface for all payment processors
type PaymentProcessor interface {
	ProcessPayment(amount float64)
}
```

#### **Step 2: Create Concrete Implementations**

```go
type PayPalProcessor struct{}

func (p *PayPalProcessor) ProcessPayment(amount float64) {
	fmt.Printf("Processing PayPal payment of $%.2f\n", amount)
}

type CreditCardProcessor struct{}

func (c *CreditCardProcessor) ProcessPayment(amount float64) {
	fmt.Printf("Processing Credit Card payment of $%.2f\n", amount)
}

type BankTransferProcessor struct{}

func (b *BankTransferProcessor) ProcessPayment(amount float64) {
	fmt.Printf("Processing Bank Transfer payment of $%.2f\n", amount)
}
```

#### **Step 3: Create the Factory**

The factory defines a method that determines which concrete implementation to create.

```go
type PaymentFactory struct{}

func (f *PaymentFactory) GetPaymentProcessor(paymentType string) PaymentProcessor {
	switch paymentType {
	case "paypal":
		return &PayPalProcessor{}
	case "creditcard":
		return &CreditCardProcessor{}
	case "banktransfer":
		return &BankTransferProcessor{}
	default:
		return nil
	}
}
```

#### **Step 4: Client Code**

The client interacts with the factory to get the appropriate processor.

```go
func main() {
	factory := &PaymentFactory{}

	// Get PayPal Processor
	paypalProcessor := factory.GetPaymentProcessor("paypal")
	paypalProcessor.ProcessPayment(100.0)

	// Get Credit Card Processor
	creditCardProcessor := factory.GetPaymentProcessor("creditcard")
	creditCardProcessor.ProcessPayment(250.0)

	// Get Bank Transfer Processor
	bankTransferProcessor := factory.GetPaymentProcessor("banktransfer")
	bankTransferProcessor.ProcessPayment(500.0)
}
```

---

### **Why Not Just Use `switch` in Client Code?**

```go
switch paymentType {
case "paypal":
    processor = &PayPalProcessor{}
case "creditcard":
    processor = &CreditCardProcessor{}
case "banktransfer":
    processor = &BankTransferProcessor{}
}
```

**Problems:**

1. **Tight Coupling**: The client knows all concrete types, violating SRP and OCP.
2. **Scalability Issues**: Adding a new processor requires modifying the client.

Using the factory method:

- The client interacts only with the factory and `PaymentProcessor` interface.
- Adding new processors doesn't affect the client code—only the factory needs an update.

---

### **When to Use the Factory Method Pattern?**

- When a class cannot anticipate the type of object it needs to create.
- When object creation logic becomes complex and needs encapsulation.
- When you want to decouple client code from concrete classes.
