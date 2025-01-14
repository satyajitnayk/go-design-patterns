### **SOLID Principles **

- best viewed as a guidelines rather than an absolute rules.

-Go isn’t object-oriented in the traditional sense, but it supports composition and interfaces, which allow adherence to the SOLID principles effectively. Below are explanations and examples:

---

### **1. Single Responsibility Principle (SRP)**

**Definition**: A class or module should have only one reason to change, meaning it should have one responsibility.

#### Example Violating SRP:

```go
package main

import (
	"fmt"
	"os"
)

type FileManager struct{}

func (fm *FileManager) SaveFile(filename, content string) {
	// Logic to save the file
	file, _ := os.Create(filename)
	defer file.Close()
	file.WriteString(content)
	fmt.Println("File saved.")
}

func (fm *FileManager) GenerateReport(data string) string {
	// Logic to generate a report
	return "Report: " + data
}

func main() {
	fm := FileManager{}
	report := fm.GenerateReport("Sales Data")
	fm.SaveFile("report.txt", report)
}
```

**Problem**: `FileManager` handles both report generation and file saving, which violates SRP.

---

#### Example Following SRP:

```go
package main

import (
	"fmt"
	"os"
)

type ReportGenerator struct{}

func (rg *ReportGenerator) GenerateReport(data string) string {
	return "Report: " + data
}

type FileManager struct{}

func (fm *FileManager) SaveFile(filename, content string) {
	file, _ := os.Create(filename)
	defer file.Close()
	file.WriteString(content)
	fmt.Println("File saved.")
}

func main() {
	reportGen := ReportGenerator{}
	fileManager := FileManager{}

	report := reportGen.GenerateReport("Sales Data")
	fileManager.SaveFile("report.txt", report)
}
```

**Benefit**: Each class has a single responsibility, making the code modular and easier to maintain.

---

### **2. Open/Closed Principle (OCP)**

**Definition**: Code should be open for extension but closed for modification.

#### Example Violating OCP:

```go
package main

import "fmt"

type Notification struct{}

func (n *Notification) SendNotification(method, message string) {
	if method == "email" {
		fmt.Println("Sending Email: ", message)
	} else if method == "sms" {
		fmt.Println("Sending SMS: ", message)
	}
}

func main() {
	notifier := Notification{}
	notifier.SendNotification("email", "Hello World")
	notifier.SendNotification("sms", "Hello World")
}
```

**Problem**: Adding a new notification method requires modifying the `SendNotification` function.

---

#### Example Following OCP:

```go
package main

import "fmt"

type Notifier interface {
	Send(message string)
}

type EmailNotifier struct{}

func (e *EmailNotifier) Send(message string) {
	fmt.Println("Sending Email: ", message)
}

type SMSNotifier struct{}

func (s *SMSNotifier) Send(message string) {
	fmt.Println("Sending SMS: ", message)
}

func Notify(notifiers []Notifier, message string) {
	for _, notifier := range notifiers {
		notifier.Send(message)
	}
}

func main() {
	email := &EmailNotifier{}
	sms := &SMSNotifier{}

	notifiers := []Notifier{email, sms}
	Notify(notifiers, "Hello World")
}
```

**Benefit**: Adding a new notification type only requires creating a new struct that implements `Notifier`.

---

### **3. Liskov Substitution Principle (LSP)**

**Definition**: Subtypes should be replaceable with their base types without altering the correctness of the program.

#### Example Violating LSP:

```go
package main

import "fmt"

type Bird struct{}

func (b *Bird) Fly() {
	fmt.Println("Flying...")
}

type Penguin struct {
	Bird
}

func (p *Penguin) Fly() {
	fmt.Println("Penguins can't fly!")
}

func main() {
	var bird Bird = Penguin{}
	bird.Fly() // This violates LSP because Penguins can’t fly!
}
```

**Problem**: Penguins can’t fly, but `Penguin` inherits `Fly()` from `Bird`.

---

#### Example Following LSP:

```go
package main

import "fmt"

type Flyer interface {
	Fly()
}

type Sparrow struct{}

func (s *Sparrow) Fly() {
	fmt.Println("Sparrow is flying...")
}

type Penguin struct{}

func (p *Penguin) Swim() {
	fmt.Println("Penguin is swimming...")
}

func LetFly(f Flyer) {
	f.Fly()
}

func main() {
	sparrow := &Sparrow{}
	LetFly(sparrow)

	// Penguins are not included in flyers, hence no LSP violation.
}
```

**Benefit**: Subtypes (e.g., `Penguin`) do not inherit behaviors that violate their nature.

---

### **4. Interface Segregation Principle (ISP)**

**Definition**: A class should not be forced to implement interfaces it does not use.Instead, create smaller, specific interfaces.

#### Example Violating ISP:

```go
package main

import "fmt"

type User interface {
	Login()
	BuyProduct()
	SellProduct()
	ManageUsers()
}

type Admin struct{}

func (a *Admin) Login() {
	fmt.Println("Admin logged in")
}

func (a *Admin) BuyProduct() {
	fmt.Println("Admin buying product")
}

func (a *Admin) SellProduct() {
	fmt.Println("Admin selling product")
}

func (a *Admin) ManageUsers() {
	fmt.Println("Admin managing users")
}

type Customer struct{}

func (c *Customer) Login() {
	fmt.Println("Customer logged in")
}

func (c *Customer) BuyProduct() {
	fmt.Println("Customer buying product")
}

func (c *Customer) SellProduct() {
	// Customers shouldn't implement SellProduct
}

func (c *Customer) ManageUsers() {
	// Customers shouldn't implement ManageUsers
}

func main() {
	admin := &Admin{}
	admin.Login()
	admin.ManageUsers()

	customer := &Customer{}
	customer.Login()
	customer.BuyProduct()
}

```

**Problem**: The `Customer` type is forced to implement the `SellProduct()` and `ManageUsers()` methods, even though those actions don’t make sense for a customer.

---

#### Example Following ISP:

```go
package main

import "fmt"

// Login-related actions
type Loggable interface {
	Login()
}

// Buying-related actions
type Buyer interface {
	BuyProduct()
}

// Selling-related actions
type Seller interface {
	SellProduct()
}

// Admin-specific actions
type AdminActions interface {
	ManageUsers()
}

type Admin struct{}

func (a *Admin) Login() {
	fmt.Println("Admin logged in")
}

func (a *Admin) BuyProduct() {
	fmt.Println("Admin buying product")
}

func (a *Admin) SellProduct() {
	fmt.Println("Admin selling product")
}

func (a *Admin) ManageUsers() {
	fmt.Println("Admin managing users")
}

type Customer struct{}

func (c *Customer) Login() {
	fmt.Println("Customer logged in")
}

func (c *Customer) BuyProduct() {
	fmt.Println("Customer buying product")
}

type SellerUser struct{}

func (s *SellerUser) Login() {
	fmt.Println("Seller logged in")
}

func (s *SellerUser) SellProduct() {
	fmt.Println("Seller selling product")
}

func main() {
	admin := &Admin{}
	admin.Login()
	admin.ManageUsers()

	customer := &Customer{}
	customer.Login()
	customer.BuyProduct()

	seller := &SellerUser{}
	seller.Login()
	seller.SellProduct()
}
```

**Benefit**: Smaller, focused interfaces ensure classes implement only what they need.

---

### **5. Dependency Inversion Principle (DIP)**

**Definition**: High-level modules should not depend on low-level modules. Both should depend on abstractions.

#### Example Violating DIP:

```go
package main

import "fmt"

type EmailService struct{}

func (e *EmailService) SendEmail(message string) {
	fmt.Println("Sending Email: ", message)
}

type Notification struct {
	emailService EmailService
}

func (n *Notification) Notify(message string) {
	n.emailService.SendEmail(message)
}

func main() {
	notifier := Notification{}
	notifier.Notify("Hello World")
}
```

**Problem**: `Notification` depends directly on `EmailService`.

---

#### Example Following DIP:

```go
package main

import "fmt"

type Notifier interface {
	Notify(message string)
}

type EmailService struct{}

func (e *EmailService) Notify(message string) {
	fmt.Println("Sending Email: ", message)
}

type SMSService struct{}

func (s *SMSService) Notify(message string) {
	fmt.Println("Sending SMS: ", message)
}

type Notification struct {
	notifier Notifier
}

func (n *Notification) Notify(message string) {
	n.notifier.Notify(message)
}

func main() {
	emailService := &EmailService{}
	smsService := &SMSService{}

	emailNotifier := Notification{notifier: emailService}
	emailNotifier.Notify("Hello Email World")

	smsNotifier := Notification{notifier: smsService}
	smsNotifier.Notify("Hello SMS World")
}
```

**Benefit**: `Notification` depends on an abstraction (`Notifier`), allowing flexibility to use any notifier type.

---

### Summary Table

| **Principle**         | **Core Idea**                                         | **Improvement**                                        |
| --------------------- | ----------------------------------------------------- | ------------------------------------------------------ |
| Single Responsibility | One class = one responsibility.                       | Reduces coupling and increases clarity.                |
| Open/Closed           | Open to extension, closed to modification.            | Easier to add features without changing core code.     |
| Liskov Substitution   | Subtypes must replace their base types seamlessly.    | Avoids unexpected behaviors in polymorphism.           |
| Interface Segregation | Use small, focused interfaces.                        | Prevents structs from implementing irrelevant methods. |
| Dependency Inversion  | Depend on abstractions, not concrete implementations. | Promotes loose coupling and testability.               |
