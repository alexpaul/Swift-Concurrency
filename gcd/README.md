# Grand Central Dispatch 

> [Dispatch](https://developer.apple.com/documentation/dispatch), also known as Grand Central Dispatch (GCD), contains language features, runtime libraries, and system enhancements that provide systemic, comprehensive improvements to the support for concurrent code execution on multicore hardware in macOS, iOS, watchOS, and tvOS.

## DispatchQueue

> [DispatchQueue](https://developer.apple.com/documentation/dispatch/dispatchqueue). An object that manages the execution of tasks serially or concurrently on your app's main thread or on a background thread.

## Creating a DispatchQueue

> [attributes](https://developer.apple.com/documentation/dispatch/dispatchqueue/2300059-init): The attributes to associate with the queue. Include the concurrent attribute to create a dispatch queue that executes tasks concurrently. If you omit that attribute, the dispatch queue executes tasks serially.


## Resources

* [Apple Docs: Diagnosing Memory, Thread, and Crash Issues Early](https://developer.apple.com/documentation/xcode/diagnosing-memory-thread-and-crash-issues-early)
* [Antoine van der Lee: Concurrent vs Serial DispatchQueue: Concurrency in Swift explained](https://www.avanderlee.com/swift/concurrent-serial-dispatchqueue/)
