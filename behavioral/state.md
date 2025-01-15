The **State Pattern** is a behavioral design pattern that allows an object to change its behavior when its internal state changes. It appears as though the object has changed its class.

---

### **What is the State Pattern?**

- **Purpose**: Encapsulates state-specific behavior into separate classes.
- **Key Idea**: The context delegates behavior to the current state object, which can change dynamically.
- **Participants**:
  - **Context**: Maintains a reference to a state object.
  - **State Interface**: Defines behavior associated with a state.
  - **Concrete States**: Implement specific behavior for each state.

---

### **Why Use the State Pattern?**

1. **Encapsulation**: Encapsulates state-specific logic in separate classes.
2. **Flexibility**: Allows dynamic changes in behavior without using conditional statements.
3. **Scalability**: Easy to add new states without modifying the context or existing states.

---

### **Go Implementation**

---

#### **Example: Vending Machine**

```go
package main

import "fmt"

// State Interface
type VendingMachineState interface {
	InsertMoney(amount float64)
	DispenseItem()
}

// Concrete State: No Money State
type NoMoneyState struct {
	vendingMachine *VendingMachine
}

func (s *NoMoneyState) InsertMoney(amount float64) {
	fmt.Printf("Money inserted: %.2f\n", amount)
	s.vendingMachine.currentState = s.vendingMachine.hasMoneyState
}

func (s *NoMoneyState) DispenseItem() {
	fmt.Println("Please insert money first.")
}

// Concrete State: Has Money State
type HasMoneyState struct {
	vendingMachine *VendingMachine
}

func (s *HasMoneyState) InsertMoney(amount float64) {
	fmt.Println("Money already inserted. Dispense the item first.")
}

func (s *HasMoneyState) DispenseItem() {
	fmt.Println("Item dispensed.")
	s.vendingMachine.currentState = s.vendingMachine.noMoneyState
}

// Context
type VendingMachine struct {
	noMoneyState  VendingMachineState
	hasMoneyState VendingMachineState
	currentState  VendingMachineState
}

func NewVendingMachine() *VendingMachine {
	v := &VendingMachine{}
	noMoneyState := &NoMoneyState{vendingMachine: v}
	hasMoneyState := &HasMoneyState{vendingMachine: v}

	v.noMoneyState = noMoneyState
	v.hasMoneyState = hasMoneyState
	v.currentState = noMoneyState
	return v
}

func (v *VendingMachine) InsertMoney(amount float64) {
	v.currentState.InsertMoney(amount)
}

func (v *VendingMachine) DispenseItem() {
	v.currentState.DispenseItem()
}

func main() {
	vm := NewVendingMachine()

	vm.DispenseItem()       // "Please insert money first."
	vm.InsertMoney(2.0)     // "Money inserted: 2.00"
	vm.InsertMoney(3.0)     // "Money already inserted. Dispense the item first."
	vm.DispenseItem()       // "Item dispensed."
	vm.DispenseItem()       // "Please insert money first."
}
```

---

### **Explanation**

1. **State Interface (`VendingMachineState`)**:

   - Defines methods (`InsertMoney`, `DispenseItem`) for states.

2. **Concrete States (`NoMoneyState`, `HasMoneyState`)**:

   - Implement the state-specific behavior.
   - Change the context's state based on actions.

3. **Context (`VendingMachine`)**:

   - Maintains references to all states and the current state.
   - Delegates behavior to the current state.

4. **Client**:
   - Interacts with the context, unaware of specific states.

---

### **Output**

```
Please insert money first.
Money inserted: 2.00
Money already inserted. Dispense the item first.
Item dispensed.
Please insert money first.
```

---

### **Advantages**

1. **Encapsulation**: State-specific logic is isolated.
2. **Open/Closed Principle**: Add new states without modifying existing code.
3. **Readable Code**: Avoids complex `if-else` or `switch` statements.

---

### **Disadvantages**

1. **Increased Complexity**: Adds multiple classes for each state.
2. **Context-State Dependency**: States depend on the context for transitions.

---

### **When to Use the State Pattern?**

- When an object’s behavior depends on its state and can change dynamically.
- When multiple states have distinct behaviors, requiring clear separation.
- When `if-else` or `switch` statements for state transitions become unmanageable.
