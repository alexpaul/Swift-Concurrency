# Swift Concurrency APIs

```swift 
func fetch(completion: @escaping () -> Void) {
    completion()
}

// vs

func fetch() async throws -> String {
    "Data Received"
}
```
