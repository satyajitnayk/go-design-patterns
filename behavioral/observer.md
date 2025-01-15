The **Observer Pattern** is a behavioral design pattern that establishes a one-to-many dependency between objects. When one object (the **subject**) changes its state, all its dependents (**observers**) are automatically notified.

---

### **What is the Observer Pattern?**

- **Purpose**: Notify multiple objects about changes in another object.
- **Participants**:
  - **Subject**: Maintains a list of observers and notifies them of any state changes.
  - **Observers**: Interested parties that get updated when the subject changes.

---

### **Why Use the Observer Pattern?**

1. **Decoupling**: Reduces the dependency between the subject and its observers.
2. **Real-Time Updates**: Automatically notifies observers of changes.
3. **Scalability**: New observers can be added without modifying the subject.

---

### **Go Implementation**

---

#### **Example: Weather Station with Multiple Displays**

```go
package main

import "fmt"

// Observer interface
type Observer interface {
	Update(temperature, humidity, pressure float64)
}

// Subject interface
type Subject interface {
	RegisterObserver(o Observer)
	RemoveObserver(o Observer)
	NotifyObservers()
}

// Concrete Subject
type WeatherStation struct {
	observers   []Observer
	temperature float64
	humidity    float64
	pressure    float64
}

func (ws *WeatherStation) RegisterObserver(o Observer) {
	ws.observers = append(ws.observers, o)
}

func (ws *WeatherStation) RemoveObserver(o Observer) {
	for i, observer := range ws.observers {
		if observer == o {
			ws.observers = append(ws.observers[:i], ws.observers[i+1:]...)
			break
		}
	}
}

func (ws *WeatherStation) NotifyObservers() {
	for _, observer := range ws.observers {
		observer.Update(ws.temperature, ws.humidity, ws.pressure)
	}
}

func (ws *WeatherStation) SetMeasurements(temperature, humidity, pressure float64) {
	ws.temperature = temperature
	ws.humidity = humidity
	ws.pressure = pressure
	ws.NotifyObservers()
}

// Concrete Observer: Phone Display
type PhoneDisplay struct {
	id string
}

func (pd *PhoneDisplay) Update(temperature, humidity, pressure float64) {
	fmt.Printf("Phone Display %s - Temp: %.1f°C, Humidity: %.1f%%, Pressure: %.1fhPa\n",
		pd.id, temperature, humidity, pressure)
}

// Concrete Observer: Desktop Display
type DesktopDisplay struct {
	id string
}

func (dd *DesktopDisplay) Update(temperature, humidity, pressure float64) {
	fmt.Printf("Desktop Display %s - Temp: %.1f°C, Humidity: %.1f%%, Pressure: %.1fhPa\n",
		dd.id, temperature, humidity, pressure)
}

func main() {
	// Create Weather Station
	weatherStation := &WeatherStation{}

	// Create Observers
	phone1 := &PhoneDisplay{id: "Phone1"}
	phone2 := &PhoneDisplay{id: "Phone2"}
	desktop := &DesktopDisplay{id: "Desktop1"}

	// Register Observers
	weatherStation.RegisterObserver(phone1)
	weatherStation.RegisterObserver(phone2)
	weatherStation.RegisterObserver(desktop)

	// Update Weather Station Measurements
	weatherStation.SetMeasurements(25.5, 60.0, 1012.0)
	weatherStation.SetMeasurements(26.0, 58.0, 1010.0)

	// Remove an Observer
	weatherStation.RemoveObserver(phone2)

	// Update Weather Station Measurements Again
	weatherStation.SetMeasurements(24.0, 65.0, 1015.0)
}
```

---

### **Explanation**

1. **Subject (`WeatherStation`)**:

   - Keeps track of registered observers.
   - Notifies all observers when its state changes using `NotifyObservers()`.

2. **Observers (`PhoneDisplay`, `DesktopDisplay`)**:

   - Implement the `Observer` interface.
   - React to changes in the subject by implementing the `Update()` method.

3. **Interaction**:
   - Observers are registered with the subject using `RegisterObserver()`.
   - When the subject's state changes, it calls `NotifyObservers()`, triggering `Update()` for all observers.

---

### **Output**

```
Phone Display Phone1 - Temp: 25.5°C, Humidity: 60.0%, Pressure: 1012.0hPa
Phone Display Phone2 - Temp: 25.5°C, Humidity: 60.0%, Pressure: 1012.0hPa
Desktop Display Desktop1 - Temp: 25.5°C, Humidity: 60.0%, Pressure: 1012.0hPa
Phone Display Phone1 - Temp: 26.0°C, Humidity: 58.0%, Pressure: 1010.0hPa
Phone Display Phone2 - Temp: 26.0°C, Humidity: 58.0%, Pressure: 1010.0hPa
Desktop Display Desktop1 - Temp: 26.0°C, Humidity: 58.0%, Pressure: 1010.0hPa
Phone Display Phone1 - Temp: 24.0°C, Humidity: 65.0%, Pressure: 1015.0hPa
Desktop Display Desktop1 - Temp: 24.0°C, Humidity: 65.0%, Pressure: 1015.0hPa
```

---

### **Advantages**

1. **Real-Time Updates**: Observers are notified automatically when the subject changes.
2. **Scalability**: Easily add or remove observers.
3. **Loose Coupling**: Observers and subjects are loosely coupled, promoting flexibility.

---

### **Disadvantages**

1. **Performance Overhead**: Notifying many observers can be time-consuming.
2. **Complex Debugging**: Difficult to trace issues when multiple observers are involved.

---

### **When to Use the Observer Pattern?**

- When multiple objects need to be updated in response to state changes in another object.
- When implementing event systems or real-time notifications.
