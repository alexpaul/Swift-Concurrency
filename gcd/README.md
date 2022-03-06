# Grand Central Dispatch 

> [Dispatch](https://developer.apple.com/documentation/dispatch), also known as Grand Central Dispatch (GCD), contains language features, runtime libraries, and system enhancements that provide systemic, comprehensive improvements to the support for concurrent code execution on multicore hardware in macOS, iOS, watchOS, and tvOS.

## 1. DispatchQueue

> [DispatchQueue](https://developer.apple.com/documentation/dispatch/dispatchqueue). An object that manages the execution of tasks serially or concurrently on your app's main thread or on a background thread.

## 2. Creating a DispatchQueue

> [attributes](https://developer.apple.com/documentation/dispatch/dispatchqueue/2300059-init): The attributes to associate with the queue. Include the concurrent attribute to create a dispatch queue that executes tasks concurrently. If you omit that attribute, the dispatch queue executes tasks serially.

#### Serial Queue
```swift
// By default `DispatchQueue`s are created as serial queues
let queue = DispatchQueue(label: "queue")

queue.async {
    print("First task being done.")
}

queue.async {
    print("Second task being done.")
}

/*
 First task being done.
 Second task being done.
*/
```

#### Concurrent Queue

```swift
// Here we are explicity creating a `concurrent` queue
// Now the tasks will be performed in parallel with no guarantee of order
let queue = DispatchQueue(label: "queue1", attributes: .concurrent)

queue.async {
    print("First task being done.")
}

queue.async {
    print("Second task being done.")
}

/*
 Second task being done.
 First task being done.
*/
```

#### Background Queue

```swift
DispatchQueue.global(qos: .background).async {
    completion("Data Recieved.")
}
```

#### Main Queue

```swift
DispatchQueue.main.async {
    self?.messageLabel.text = message
    print(message) // Data Recieved.
}
```

***

## 3. DispatchGroup

> [DispatchGroup](https://developer.apple.com/documentation/dispatch/dispatchgroup). Groups allow you to aggregate a set of tasks and synchronize behaviors on the group. You attach multiple work items to a group and schedule them for asynchronous execution on the same queue or different queues. When all work items finish executing, the group executes its completion handler. You can also wait synchronously for all tasks in the group to finish executing.

```swift
import UIKit

enum API {
    static func auth(completion: @escaping (String) -> Void) {
        // Network logic here....
        completion("Authenticated")
    }

    static func fetchUsersAudioBooks(completion: @escaping (String) -> Void) {
        // Network logic here....
        completion("Fetched Audio Books")
    }

    static func fetchLastReadTitle(completion: @escaping (String, String) -> Void) {
        // Network logic here....
        completion("Fetched Last Read Title", "Good to Great")
    }
}

class ViewController: UIViewController {
    private var messageLabel = UILabel()

    override func viewDidLoad() {
        super.viewDidLoad()

        let group = DispatchGroup()

        var audioTitle = ""

        group.enter()
        API.auth { result in
            print(result)
            group.leave()
        }

        group.enter()
        API.fetchUsersAudioBooks { result in
            print(result)
            group.leave()
        }

        group.enter()
        API.fetchLastReadTitle { result, title in
            print(result)
            audioTitle = title
            group.leave()
        }

        group.notify(queue: DispatchQueue.global()) {
            print("Resuming \"\(audioTitle)\"")
        }
    }
}
```

***

## Resources

* [Apple Docs: Concurrency Programming Guide](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationQueues/OperationQueues.html)
* [Apple Docs: Diagnosing Memory, Thread, and Crash Issues Early](https://developer.apple.com/documentation/xcode/diagnosing-memory-thread-and-crash-issues-early)
* [Antoine van der Lee: Concurrent vs Serial DispatchQueue: Concurrency in Swift explained](https://www.avanderlee.com/swift/concurrent-serial-dispatchqueue/)
* [Apple Docs: Concurrency Glossary](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Glossary/Glossary.html#//apple_ref/doc/uid/TP40008091-CH104-SW2)
* [Apple Docs: Framework Programming Guide](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPFrameworks/Frameworks.html#//apple_ref/doc/uid/10000183i)
