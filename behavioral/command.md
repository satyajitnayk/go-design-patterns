The **Command Pattern** is a behavioral design pattern used to encapsulate a request as an object. This allows you to parameterize objects with operations, delay execution, or support undoable operations.

---

### **What is the Command Pattern?**

- **Encapsulation**: Encapsulates a request as an object.
- **Decoupling**: Decouples the sender (who makes the request) from the receiver (who executes it).
- **Flexibility**: Allows queuing, logging, or undoing requests.

---

### **Why Use the Command Pattern?**

1. **Undoable Operations**: Easily support undo/redo functionality.
2. **Request Queueing**: Queue commands for later execution.
3. **Separation of Concerns**: Decouple the invoker from the receiver.

---

### **Go Implementation**

Go implements the Command Pattern using interfaces and structs to encapsulate actions.

---

#### **Example: Smart Home Automation**

```go
package main

import "fmt"

// Command Interface
type Command interface {
	Execute()
}

// Receiver: Light
type Light struct{}

func (l *Light) On() {
	fmt.Println("Light is turned ON")
}

func (l *Light) Off() {
	fmt.Println("Light is turned OFF")
}

// Concrete Command: TurnLightOn
type TurnLightOn struct {
	light *Light
}

func (cmd *TurnLightOn) Execute() {
	cmd.light.On()
}

// Concrete Command: TurnLightOff
type TurnLightOff struct {
	light *Light
}

func (cmd *TurnLightOff) Execute() {
	cmd.light.Off()
}

// Invoker: RemoteControl
type RemoteControl struct {
	command Command
}

func (rc *RemoteControl) SetCommand(cmd Command) {
	rc.command = cmd
}

func (rc *RemoteControl) PressButton() {
	rc.command.Execute()
}

func main() {
	// Receiver
	light := &Light{}

	// Commands
	turnOnCommand := &TurnLightOn{light: light}
	turnOffCommand := &TurnLightOff{light: light}

	// Invoker
	remote := &RemoteControl{}

	// Turn light ON
	remote.SetCommand(turnOnCommand)
	remote.PressButton()

	// Turn light OFF
	remote.SetCommand(turnOffCommand)
	remote.PressButton()
}
```

---

### **Explanation**

1. **Command Interface**: `Command` defines an `Execute` method for all commands.
2. **Concrete Commands**: `TurnLightOn` and `TurnLightOff` encapsulate specific actions on the receiver (`Light`).
3. **Receiver**: `Light` is the object that performs the actual operations (e.g., turning on/off).
4. **Invoker**: `RemoteControl` triggers the command by calling `Execute`.

---

### **Advantages**

1. **Undo/Redo Support**: Commands can be stored and reversed.
2. **Extensibility**: Easily add new commands without modifying existing code.
3. **Decoupling**: The invoker does not need to know the receiver’s details.

---

### **Disadvantages**

1. **Complexity**: Increases the number of classes and objects.
2. **Overhead**: Simple operations may become over-engineered.

---

### **When to Use the Command Pattern?**

- When you need to queue or log operations.
- When you need undoable operations.
- When you want to decouple invokers from receivers.
