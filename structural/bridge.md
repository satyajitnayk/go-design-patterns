The **Bridge Pattern** is a structural design pattern that separates the abstraction from its implementation so that both can evolve independently. It is useful when you need to decouple tightly bound abstractions and their implementations.

---

### **What is the Bridge Pattern?**

The pattern introduces two hierarchies:

1. **Abstraction**: Represents the high-level logic.
2. **Implementation**: Handles the low-level details.

These hierarchies are connected via a "bridge" (interface), allowing changes to one hierarchy without affecting the other.

---

### **Why Use the Bridge Pattern?**

1. **Decoupling**: Separates abstraction from implementation.
2. **Flexibility**: Changes to abstraction or implementation are independent.
3. **Scalability**: Makes it easier to extend either hierarchy without modifying existing code.
4. **Avoid Explosion of Classes**: Prevents combinatorial growth of subclasses for multiple dimensions of variation.

---

### **Practical Example: Notification System**

Imagine you need to send notifications through different channels (Email, SMS) for various types of alerts (Warning, Info). Instead of creating separate classes for each combination (e.g., `EmailWarningNotification`, `SMSInfoNotification`), you can use the Bridge pattern.

---

### **Go Implementation**

#### **Step 1: Define the Implementation Interface**

This interface defines the methods for different communication channels.

```go
package main

import "fmt"

// NotificationSender is the implementation interface
type NotificationSender interface {
	SendNotification(message string)
}
```

#### **Step 2: Create Concrete Implementations**

These are the specific implementations of the `NotificationSender` interface.

```go
type EmailSender struct{}

func (e *EmailSender) SendNotification(message string) {
	fmt.Printf("Sending Email: %s\n", message)
}

type SMSSender struct{}

func (s *SMSSender) SendNotification(message string) {
	fmt.Printf("Sending SMS: %s\n", message)
}
```

#### **Step 3: Define the Abstraction**

The `Notification` struct represents the abstraction, which uses the `NotificationSender` interface.

```go
type Notification struct {
	sender NotificationSender
}

func (n *Notification) Notify(message string) {
	n.sender.SendNotification(message)
}
```

#### **Step 4: Extend the Abstraction**

You can create specific types of notifications by extending the abstraction.

```go
type WarningNotification struct {
	Notification
}

func NewWarningNotification(sender NotificationSender) *WarningNotification {
	return &WarningNotification{Notification{sender: sender}}
}

type InfoNotification struct {
	Notification
}

func NewInfoNotification(sender NotificationSender) *InfoNotification {
	return &InfoNotification{Notification{sender: sender}}
}
```

#### **Step 5: Client Code**

The client code interacts with the abstraction and does not need to know the implementation details.

```go
func main() {
	// Use EmailSender for notifications
	emailSender := &EmailSender{}
	warningNotification := NewWarningNotification(emailSender)
	warningNotification.Notify("This is a warning message.")

	// Use SMSSender for notifications
	smsSender := &SMSSender{}
	infoNotification := NewInfoNotification(smsSender)
	infoNotification.Notify("This is an info message.")
}
```

---

### **Output**

```
Sending Email: This is a warning message.
Sending SMS: This is an info message.
```

---

### **Key Benefits of Bridge Pattern**

1. **Separation of Concerns**: Abstraction (e.g., `Notification`) is independent of implementation (e.g., `EmailSender` or `SMSSender`).
2. **Scalability**: Adding a new notification type (e.g., `CriticalNotification`) or a new sender (e.g., `PushNotificationSender`) doesn't require modifying existing code.
3. **Flexibility**: Easily switch implementations at runtime.

---

### **When to Use the Bridge Pattern?**

- When you want to decouple an abstraction from its implementation.
- When you have multiple dimensions of variation (e.g., notification types and communication channels).
- When changes to one hierarchy (e.g., senders) should not affect the other (e.g., notification types).
