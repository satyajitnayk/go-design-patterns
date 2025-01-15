The **Strategy Pattern** is a behavioral design pattern that defines a family of algorithms, encapsulates each one, and makes them interchangeable. This allows the algorithm to vary independently from the clients that use it.

---

### **What is the Strategy Pattern?**

- **Purpose**: Enables selecting an algorithm's behavior at runtime.
- **Participants**:
  - **Context**: Maintains a reference to a strategy object.
  - **Strategy Interface**: Common interface for all supported algorithms.
  - **Concrete Strategies**: Implement the strategy interface with specific behaviors.

---

### **Why Use the Strategy Pattern?**

1. **Flexibility**: Easily switch between algorithms at runtime.
2. **Decoupling**: Separates the algorithm implementation from the client.
3. **Reusability**: Algorithms can be reused across different contexts.

---

### **Go Implementation**

---

#### **Example: Payment System**

```go
package main

import "fmt"

// Strategy Interface
type PaymentStrategy interface {
	Pay(amount float64)
}

// Concrete Strategy: Credit Card Payment
type CreditCardPayment struct {
	cardNumber string
}

func (cc *CreditCardPayment) Pay(amount float64) {
	fmt.Printf("Paid %.2f using Credit Card %s.\n", amount, cc.cardNumber)
}

// Concrete Strategy: PayPal Payment
type PayPalPayment struct {
	email string
}

func (pp *PayPalPayment) Pay(amount float64) {
	fmt.Printf("Paid %.2f using PayPal account %s.\n", amount, pp.email)
}

// Context
type PaymentProcessor struct {
	strategy PaymentStrategy
}

// SetStrategy allows changing the payment strategy at runtime
func (p *PaymentProcessor) SetStrategy(strategy PaymentStrategy) {
	p.strategy = strategy
}

// ExecutePayment uses the selected strategy to make a payment
func (p *PaymentProcessor) ExecutePayment(amount float64) {
	if p.strategy == nil {
		fmt.Println("Payment strategy not set!")
		return
	}
	p.strategy.Pay(amount)
}

func main() {
	// Create a payment processor (context)
	paymentProcessor := &PaymentProcessor{}

	// Pay using Credit Card
	creditCard := &CreditCardPayment{cardNumber: "1234-5678-9012-3456"}
	paymentProcessor.SetStrategy(creditCard)
	paymentProcessor.ExecutePayment(100.00)

	// Pay using PayPal
	payPal := &PayPalPayment{email: "user@example.com"}
	paymentProcessor.SetStrategy(payPal)
	paymentProcessor.ExecutePayment(200.00)
}
```

---

### **Explanation**

1. **Strategy Interface (`PaymentStrategy`)**:

   - Defines a common interface for payment methods (`Pay`).

2. **Concrete Strategies (`CreditCardPayment`, `PayPalPayment`)**:

   - Implement the `PaymentStrategy` interface with specific payment logic.

3. **Context (`PaymentProcessor`)**:

   - Maintains a reference to the current payment strategy.
   - Delegates the payment execution to the selected strategy.

4. **Client**:
   - Configures the `PaymentProcessor` with the desired strategy and executes payments.

---

### **Output**

```
Paid 100.00 using Credit Card 1234-5678-9012-3456.
Paid 200.00 using PayPal account user@example.com.
```

---

### **Advantages**

1. **Open/Closed Principle**: Add new strategies without modifying existing code.
2. **Runtime Flexibility**: Change algorithms dynamically.
3. **Code Reusability**: Algorithms are encapsulated into separate classes.

---

### **Disadvantages**

1. **Increased Complexity**: More classes/interfaces may increase complexity.
2. **Overhead**: Switching strategies at runtime can be less efficient.

---

### **When to Use the Strategy Pattern?**

- When you need to choose from multiple algorithms dynamically.
- When you want to isolate complex algorithm logic from the client.
- When the client should not be aware of specific algorithm implementations.
