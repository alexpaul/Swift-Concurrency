# Swift Concurrency APIs

## Function Definitions
```swift 
// Traditional way using Closures
func fetch(completion: @escaping (Result<String, Error>) -> Void) {
    let possibleError: Error? = nil

    if let error = possibleError {
        completion(.failure(error))
    } else {
        completion(.success("Data Received"))
    }
}

// vs

// Modern "Structured" way using Swift's 5.5 Concurrency APIs
func fetch() async throws -> String {
    let possibleError: Error? = nil

    guard possibleError == nil else {
        throw NSError(domain: "any error", code: 0)
    }

    return "Data Received"
}
```

***

## Call site

```swift
fetch { result in
    print(result)
}

Task {
    try await print(fetch())
}
```

***

## Resources 

* [Apple Docs: Concurrency](https://developer.apple.com/documentation/swift/swift_standard_library/concurrency)
* [Apple Docs: Dispatch (GCD)](https://developer.apple.com/documentation/dispatch)
