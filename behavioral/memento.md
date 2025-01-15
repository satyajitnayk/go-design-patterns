The **Memento Pattern** is a behavioral design pattern that allows you to capture and restore an object's state without exposing its internal details. It's useful for implementing undo/redo functionality or managing checkpoints in an application's state.

---

### **What is the Memento Pattern?**

- **Purpose**: Save the state of an object to restore it later.
- **Participants**:
  - **Originator**: The object whose state you want to save.
  - **Memento**: A snapshot of the state.
  - **Caretaker**: Manages mementos and interacts with the originator to save or restore state.

---

### **Why Use the Memento Pattern?**

1. **State Management**: Enables undo/redo or checkpoint functionality.
2. **Encapsulation**: Protects the internal state of an object from being accessed directly.
3. **Separation of Concerns**: Keeps state-saving logic separate from the object's main functionality.

---

### **Go Implementation**

---

#### **Example: Text Editor with Undo Feature**

```go
package main

import "fmt"

// Memento represents the saved state
type Memento struct {
	state string
}

// Originator manages the internal state
type Editor struct {
	content string
}

func (e *Editor) Write(text string) {
	e.content += text
}

func (e *Editor) GetContent() string {
	return e.content
}

func (e *Editor) Save() *Memento {
	return &Memento{state: e.content}
}

func (e *Editor) Restore(m *Memento) {
	e.content = m.state
}

// Caretaker manages the mementos
type History struct {
	mementos []*Memento
}

func (h *History) Push(m *Memento) {
	h.mementos = append(h.mementos, m)
}

func (h *History) Pop() *Memento {
	if len(h.mementos) == 0 {
		return nil
	}
	last := h.mementos[len(h.mementos)-1]
	h.mementos = h.mementos[:len(h.mementos)-1]
	return last
}

func main() {
	editor := &Editor{}
	history := &History{}

	// Write some content
	editor.Write("Hello, ")
	history.Push(editor.Save())

	editor.Write("World!")
	history.Push(editor.Save())

	editor.Write(" This is a test.")
	fmt.Println("Current Content:", editor.GetContent()) // Output: Hello, World! This is a test.

	// Undo last action
	editor.Restore(history.Pop())
	fmt.Println("After Undo:", editor.GetContent()) // Output: Hello, World!

	// Undo another action
	editor.Restore(history.Pop())
	fmt.Println("After Another Undo:", editor.GetContent()) // Output: Hello,
}
```

---

### **Explanation**

1. **Originator (`Editor`)**:

   - Manages the state (`content`).
   - Saves its current state to a memento and restores a previous state.

2. **Memento (`Memento`)**:

   - Stores a snapshot of the originator's state.
   - Provides no methods to modify its state, maintaining immutability.

3. **Caretaker (`History`)**:

   - Stores and manages the history of mementos.
   - Provides methods to save (`Push`) and retrieve (`Pop`) mementos.

4. **Interaction**:
   - The editor saves its state to the history using `Save()`.
   - When an undo is needed, it restores a previous state from the history using `Restore()`.

---

### **Output**

```
Current Content: Hello, World! This is a test.
After Undo: Hello, World!
After Another Undo: Hello,
```

---

### **Advantages**

1. **Encapsulation**: The internal state of the originator is not exposed.
2. **Flexibility**: Allows undo/redo functionality without affecting other code.
3. **Separation of Concerns**: The caretaker handles state management, leaving the originator focused on its logic.

---

### **Disadvantages**

1. **Memory Usage**: Large or frequent mementos can increase memory consumption.
2. **Complexity**: Managing mementos may become complicated if the number of states is high.

---

### **When to Use the Memento Pattern?**

- When implementing undo/redo functionality.
- When needing to save checkpoints in a process.
- When preserving an object's state without exposing its details.
