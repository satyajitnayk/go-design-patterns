A system where you receive customer data in CSV format, but your application processes it in JSON format. The **Adapter pattern** enables the seamless integration of the CSV data into the JSON-based system without changing the existing CSV parser code.

---

### Real-World Example: Customer Data Conversion (CSV to JSON)

```go
package main

import (
	"encoding/csv"
	"encoding/json"
	"fmt"
	"os"
	"strings"
)

// Adaptee: Handles CSV input and parses it into structured data
type CSVParser struct{}

func (c *CSVParser) ParseCSV(csvData string) ([]map[string]string, error) {
	reader := csv.NewReader(strings.NewReader(csvData))

	// Read all rows
	rows, err := reader.ReadAll()
	if err != nil {
		return nil, err
	}

	// Convert rows into key-value pairs
	if len(rows) < 1 {
		return nil, fmt.Errorf("empty CSV data")
	}

	headers := rows[0]
	var result []map[string]string
	for _, row := range rows[1:] {
		if len(row) != len(headers) {
			return nil, fmt.Errorf("row length mismatch")
		}
		record := make(map[string]string)
		for i, header := range headers {
			record[header] = row[i]
		}
		result = append(result, record)
	}
	return result, nil
}

// Target Interface: JSONConverter
type JSONConverter interface {
	ConvertToJSON(input string) (string, error)
}

// Adapter: Allows CSVParser to work where JSONConverter is required
type CSVToJSONAdapter struct {
	csvParser *CSVParser
}

func NewCSVToJSONAdapter() *CSVToJSONAdapter {
	return &CSVToJSONAdapter{csvParser: &CSVParser{}}
}

func (a *CSVToJSONAdapter) ConvertToJSON(input string) (string, error) {
	parsedData, err := a.csvParser.ParseCSV(input)
	if err != nil {
		return "", err
	}

	jsonData, err := json.MarshalIndent(parsedData, "", "  ")
	if err != nil {
		return "", err
	}
	return string(jsonData), nil
}

// Client function
func main() {
	// Example: Customer data in CSV format
	csvData := `Name,Age,Email
John Doe,30,john.doe@example.com
Jane Smith,25,jane.smith@example.com`

	// Create an adapter
	adapter := NewCSVToJSONAdapter()

	// Use the adapter to convert CSV to JSON
	jsonOutput, err := adapter.ConvertToJSON(csvData)
	if err != nil {
		fmt.Println("Error:", err)
		os.Exit(1)
	}

	// Print the JSON result
	fmt.Println("Converted JSON Data:")
	fmt.Println(jsonOutput)
}
```

---

### Key Components:

1. **Adaptee (`CSVParser`)**:

   - Parses CSV data into a slice of maps (`[]map[string]string`), where each map represents a row with headers as keys.
   - Handles the details of CSV parsing, such as mismatched row lengths and empty data.

2. **Target Interface (`JSONConverter`)**:

   - Defines the required method `ConvertToJSON`, which returns JSON-formatted output.

3. **Adapter (`CSVToJSONAdapter`)**:

   - Adapts `CSVParser` to fulfill the `JSONConverter` interface.
   - Converts the parsed CSV data into JSON format.

4. **Client (`main`)**:
   - Uses the `JSONConverter` interface to process CSV data as JSON, abstracting the complexity of CSV parsing.

---

### Why Is This More Realistic?

- **Use Case**: A common scenario where customer or transactional data comes in CSV but needs to be consumed as JSON by downstream services.
- **Error Handling**: Ensures robust handling of real-world issues, like mismatched row lengths or empty input.
- **Modularity**: Reuses the `CSVParser` logic without rewriting it for JSON support, making it maintainable and extensible.
