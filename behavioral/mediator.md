The **Mediator Pattern** is a behavioral design pattern that promotes loose coupling by preventing direct communication between components (colleagues). Instead, components communicate through a mediator that centralizes interaction logic.

---

### **What is the Mediator Pattern?**

- **Centralized Communication**: All communication between components is routed through a mediator.
- **Decoupling**: Components are independent of each other and only depend on the mediator.
- **Coordination**: The mediator encapsulates how objects interact.

---

### **Why Use the Mediator Pattern?**

1. **Simplifies Object Relationships**: Avoids complex dependencies between components.
2. **Centralizes Logic**: Interaction logic is placed in one class, improving maintainability.
3. **Decoupling**: Components become reusable and testable since they are unaware of each other.

---

### **Go Implementation**

---

#### **Example: Chatroom Communication**

```go
package main

import "fmt"

// Mediator interface
type Mediator interface {
	SendMessage(sender string, message string)
	RegisterUser(user *User)
}

// Concrete Mediator
type ChatRoom struct {
	users map[string]*User
}

func NewChatRoom() *ChatRoom {
	return &ChatRoom{users: make(map[string]*User)}
}

func (c *ChatRoom) RegisterUser(user *User) {
	c.users[user.name] = user
}

func (c *ChatRoom) SendMessage(sender string, message string) {
	for name, user := range c.users {
		if name != sender {
			user.ReceiveMessage(sender, message)
		}
	}
}

// Colleague
type User struct {
	name     string
	mediator Mediator
}

func NewUser(name string, mediator Mediator) *User {
	return &User{name: name, mediator: mediator}
}

func (u *User) SendMessage(message string) {
	fmt.Printf("%s sends: %s\n", u.name, message)
	u.mediator.SendMessage(u.name, message)
}

func (u *User) ReceiveMessage(sender string, message string) {
	fmt.Printf("%s receives from %s: %s\n", u.name, sender, message)
}

func main() {
	chatRoom := NewChatRoom()

	// Create users and register them with the chatroom
	alice := NewUser("Alice", chatRoom)
	bob := NewUser("Bob", chatRoom)
	charlie := NewUser("Charlie", chatRoom)

	chatRoom.RegisterUser(alice)
	chatRoom.RegisterUser(bob)
	chatRoom.RegisterUser(charlie)

	// Users send messages
	alice.SendMessage("Hi, everyone!")
	bob.SendMessage("Hey Alice!")
	charlie.SendMessage("Hello Alice and Bob!")
}
```

---

### **Explanation**

1. **Mediator Interface**: Defines methods for communication and registration (`SendMessage` and `RegisterUser`).
2. **Concrete Mediator**: `ChatRoom` manages communication between users and ensures messages are routed correctly.
3. **Colleagues**: `User` represents a participant in the chatroom and uses the mediator to send and receive messages.
4. **Interaction**: Users send messages via the `SendMessage` method of the mediator, and the mediator broadcasts them to all other users.

---

### **Output**

```
Alice sends: Hi, everyone!
Bob receives from Alice: Hi, everyone!
Charlie receives from Alice: Hi, everyone!

Bob sends: Hey Alice!
Alice receives from Bob: Hey Alice!
Charlie receives from Bob: Hey Alice!

Charlie sends: Hello Alice and Bob!
Alice receives from Charlie: Hello Alice and Bob!
Bob receives from Charlie: Hello Alice and Bob!
```

---

### **Advantages**

1. **Reduces Dependencies**: Components don’t reference each other directly.
2. **Centralized Control**: The mediator encapsulates interaction logic, making it easier to maintain.
3. **Reusability**: Components are independent and reusable.

---

### **Disadvantages**

1. **Mediator Complexity**: As the number of interactions increases, the mediator can become complex.
2. **Single Point of Failure**: The mediator is critical; if it fails, the entire system can break.

---

### **When to Use the Mediator Pattern?**

- When components have many-to-many relationships.
- When interaction logic is complex and needs centralization.
- When you want to reduce coupling between components.
