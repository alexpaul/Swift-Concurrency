# Swift Concurrency APIs

## 1. Actors

#### Snippet of a race condition 

```swift
import Foundation 

class Counter {
    private var value = 0
    
    func increment() -> Int {
        value += 1
        return value
    }
}

let counter = Counter()

DispatchQueue.concurrentPerform(iterations: 10) { _ in
    print(counter.increment())
}

/*
 Race condition as individual thread are all accessing and changing the values concurrently
 7
 3
 9
 5
 6
 4
 8
 1
 2
 10
 */

```

#### Using `actor` to avoid a race condition 

> At the time of this writing if using _Swift Playground_ ensure you `import UIKit` to use the `Task` [type](https://developer.apple.com/documentation/swift/task).

```swift 
import UIKit

// The `actor` type ensures no other concurrent thread causes a race condition on the `actor` type
actor CounterUsingActor {
    private var value = 0
    
    func increment() -> Int {
        value += 1
        return value
    }
}

let counterUsingActor = CounterUsingActor()

DispatchQueue.concurrentPerform(iterations: 10) { _ in
    // print(counterUsingActor.increment())
    // compiler-error above: Actor-isolated instance method 'increment()' can not be referenced from a non-isolated context

    Task.init {
        print(await counterUsingActor.increment())
    }
}

/*
 Prints as expected using `actor` and Swift 5.5 Concurrency APIs
 1
 2
 3
 4
 5
 6
 7
 8
 9
 10
 */

```

## Resources 

* [Apple Docs: Concurrency](https://developer.apple.com/documentation/swift/swift_standard_library/concurrency)
* [Video: Azam Sharp (Understanding Actors in Swift)](https://www.youtube.com/watch?v=I8y1fg1P-nI)
* [Hacking with Swift: What is an actor and why does Swift have them?
](https://www.hackingwithswift.com/quick-start/concurrency/what-is-an-actor-and-why-does-swift-have-them)
