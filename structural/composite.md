The **Composite Pattern** is a structural design pattern used to compose objects into tree structures, representing part-whole hierarchies. It allows clients to treat individual objects and compositions of objects uniformly.

---

### **What is the Composite Pattern?**

- **Part-Whole Hierarchies**: Represents hierarchical structures like file systems, organization charts, etc.
- **Uniform Treatment**: Treat individual objects and groups of objects the same way using a common interface.

---

### **Why Use the Composite Pattern?**

- **Simplifies Client Code**: Clients can work with single objects or collections uniformly.
- **Hierarchical Structures**: Ideal for scenarios like directories containing files or other directories.

---

### **Go Implementation**

Go supports the Composite Pattern using interfaces and recursive compositions.

---

#### **Basic Example: File System**

```go
package main

import "fmt"

// Component interface
type Component interface {
	Display(indent string)
}

// Leaf: File
type File struct {
	name string
}

func (f *File) Display(indent string) {
	fmt.Println(indent + f.name)
}

// Composite: Directory
type Directory struct {
	name      string
	components []Component
}

func (d *Directory) Add(component Component) {
	d.components = append(d.components, component)
}

func (d *Directory) Display(indent string) {
	fmt.Println(indent + d.name)
	for _, component := range d.components {
		component.Display(indent + "  ")
	}
}

func main() {
	// Create leaf components
	file1 := &File{name: "File1.txt"}
	file2 := &File{name: "File2.txt"}
	file3 := &File{name: "File3.txt"}

	// Create composite components
	folder1 := &Directory{name: "Folder1"}
	folder2 := &Directory{name: "Folder2"}
	root := &Directory{name: "Root"}

	// Build the hierarchy
	folder1.Add(file1)
	folder1.Add(file2)
	folder2.Add(file3)
	root.Add(folder1)
	root.Add(folder2)

	// Display the structure
	root.Display("")
}
```

---

### **Output**

```
Root
  Folder1
    File1.txt
    File2.txt
  Folder2
    File3.txt
```

---

#### **Practical Example: UI Elements**

Imagine a UI framework where buttons, panels, and windows form a tree structure.

```go
package main

import "fmt"

// Component interface
type UIComponent interface {
	Render(indent string)
}

// Leaf: Button
type Button struct {
	name string
}

func (b *Button) Render(indent string) {
	fmt.Println(indent + "Button: " + b.name)
}

// Composite: Panel
type Panel struct {
	name       string
	components []UIComponent
}

func (p *Panel) Add(component UIComponent) {
	p.components = append(p.components, component)
}

func (p *Panel) Render(indent string) {
	fmt.Println(indent + "Panel: " + p.name)
	for _, component := range p.components {
		component.Render(indent + "  ")
	}
}

func main() {
	// Create leaf components
	button1 := &Button{name: "Submit"}
	button2 := &Button{name: "Cancel"}

	// Create composite components
	panel := &Panel{name: "Main Panel"}
	panel.Add(button1)
	panel.Add(button2)

	// Create a root window
	window := &Panel{name: "Main Window"}
	window.Add(panel)

	// Render the UI
	window.Render("")
}
```

---

### **Output**

```
Main Window
  Panel: Main Panel
    Button: Submit
    Button: Cancel
```

---

### **Advantages**

1. **Scalability**: Easily compose objects into complex structures.
2. **Uniformity**: Treat individual and composite objects uniformly.
3. **Extensibility**: Add new types of components without modifying existing code.

---

### **Disadvantages**

1. **Complexity**: Can introduce unnecessary complexity for simple structures.
2. **Overhead**: Managing parent-child relationships can increase resource usage.

---

### **When to Use the Composite Pattern?**

- When dealing with tree structures like file systems, menus, or organizational charts.
- When you want to treat individual and composite objects uniformly.
- When you need to recursively process hierarchical data.
