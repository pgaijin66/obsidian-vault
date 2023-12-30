
#### Get memory statistics

```go
	// Get memory statistics
	var m runtime.MemStats
	runtime.ReadMemStats(&m)

	// Print memory usage information
	fmt.Printf("Allocated memory: %d bytes\n", m.Alloc)
	fmt.Printf("Total memory allocated and not yet freed: %d bytes\n", m.TotalAlloc)
	fmt.Printf("Memory obtained from system: %d bytes\n", m.Sys)
	fmt.Printf("Number of heap objects: %d\n", m.HeapObjects)
	fmt.Printf("Number of goroutines: %d\n", runtime.NumGoroutine())
```