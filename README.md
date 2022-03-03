# Swift Concurrency APIs

```swift 
func fetch(completion: @escaping (String) -> Void) {
    completion("Data Received")
}

// vs

func fetch() async throws -> String {
    "Data Received"
}
```

## Resources 

* [Apple Docs: Concurrency](https://developer.apple.com/documentation/swift/swift_standard_library/concurrency)
