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

## Resources 

* [Apple Docs: Concurrency](https://developer.apple.com/documentation/swift/swift_standard_library/concurrency)
