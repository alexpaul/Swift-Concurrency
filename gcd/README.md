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
        
        /*
         Authenticated
         Fetched Audio Books
         Fetched Last Read Title
         Resuming "Good to Great"
        */
    }
}
```

***

## 4. Using Concurrent Queues along with the `.barrier` Flag

```swift
 
// Recall by default `DispatchQueue`'s are serial queues
// Here we create a Concurrent queue by using the `attributes` property
let queue = DispatchQueue(label: "queue demo", attributes: .concurrent)

// without the `.barrier` flag run serially
queue.async {
    sleep(1)
    print("running task 1")
}

/*
 Apple Docs: https://developer.apple.com/documentation/dispatch/dispatchworkitemflags/1780674-barrier
 When submitted to a concurrent queue, a work item with this flag acts as a barrier.
 Work items submitted prior to the barrier execute to completion, at which point the barrier work item executes.
 Once the barrier work item finishes, the queue returns to scheduling work items that were submitted after the barrier.
*/
 
// with the `.barrier` flag this work item will be executed before release work back to its thread
queue.async(flags: .barrier) {
    sleep(1)
    print("running task 2")
}

queue.async(flags: .barrier) {
    sleep(1)
    print("running task 3")
}

/*
 running task 1
 running task 2
 running task 3
*/
```

## 5. Semaphores

> [Semaphores](https://developer.apple.com/library/archive/documentation/Darwin/Conceptual/KernelProgramming/synchronization/synchronization.html) and locks are similar, except that with semaphores, more than one thread can be doing a given operation at once. Semaphores are commonly used when protecting multiple indistinct resources. For example, you might use a semaphore to prevent a queue from overflowing its bounds.
> OS X uses traditional counting semaphores rather than binary semaphores (which are essentially locks).



## Resources

* [Apple Docs: Concurrency Programming Guide](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationQueues/OperationQueues.html)
* [Apple Docs: Diagnosing Memory, Thread, and Crash Issues Early](https://developer.apple.com/documentation/xcode/diagnosing-memory-thread-and-crash-issues-early)
* [Antoine van der Lee: Concurrent vs Serial DispatchQueue: Concurrency in Swift explained](https://www.avanderlee.com/swift/concurrent-serial-dispatchqueue/)
* [Apple Docs: Concurrency Glossary](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Glossary/Glossary.html#//apple_ref/doc/uid/TP40008091-CH104-SW2)
* [Apple Docs: Framework Programming Guide](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPFrameworks/Frameworks.html#//apple_ref/doc/uid/10000183i)
* [Apple Docs: Processes and Threads](https://developer.apple.com/documentation/foundation/processes_and_threads)
* [Apple Docs: Thread Programming Guide](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/Introduction/Introduction.html#//apple_ref/doc/uid/10000057i-CH1-SW1)
* [Apple Docs: Dispatch Barrier](https://developer.apple.com/documentation/dispatch/dispatch_barrier)
* [Apple Docs: Kernel Programming Guide](https://developer.apple.com/library/archive/documentation/Darwin/Conceptual/KernelProgramming/About/About.html)
* [Apple Docs: DispatchWorkItemFlags](https://developer.apple.com/documentation/dispatch/dispatchworkitemflags)
  * [Apple Docs: `.barrier`](https://developer.apple.com/documentation/dispatch/dispatchworkitemflags/1780674-barrier)
