# Swift Concurrency APIs

## Function Definitions
```swift 
func fetch(completion: @escaping (Result<String, Error>) -> Void) {
    let possibleError: Error? = nil

    if let error = possibleError {
        completion(.failure(error))
    } else {
        completion(.success("Data Received"))
    }
}

// vs

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
