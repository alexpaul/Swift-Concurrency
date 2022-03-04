# Actors

> [Swift Language Guide](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html): Like classes, actors are reference types, so the comparison of value types and reference types in Classes Are Reference Types applies to actors as well as classes. Unlike classes, actors allow only one task to access their mutable state at a time, which makes it safe for code in multiple tasks to interact with the same instance of an actor. For example, here’s an actor that records temperatures:

***

## Example 1

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

    Task {
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

***

## Example 2 

```swift
import UIKit

class Store {
    // the code here will be asuming both those functions are running on background threads with
    // potential race conditions occuring

    func purchase() {
        print("making a purchase....")
    }

    func cancel() {
        print("cancelling.....sorry to see you go...")
    }
}

actor StoreUsingActor {
    // using an `actor` will now avoid a potential race condition as opposed to the `class` above
    // as the `actor` reference will only allow one task to access its mutable state at a time

    // to the rescue is the `actor` type, it will ensure all work being done on its
    // methods a on the same task
    func purchase() {
        print("making a purchase....")
    }

    func cancel() {
        print("cancelling.....sorry to see you go...")
    }
}

let store = StoreUsingActor()

/*
store.purchase()
store.cancel()

Compiler Errro: Actor-isolated instance method 'purchase()' can not be referenced from a non-isolated context
*/

Task {
    await store.purchase()
    await store.cancel()
}
```

## Resources 

* [Swift Language Guide: Concurrency](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html)
* [Video: Azam Sharp (Understanding Actors in Swift)](https://www.youtube.com/watch?v=I8y1fg1P-nI)
* [Hacking with Swift: What is an actor and why does Swift have them?
](https://www.hackingwithswift.com/quick-start/concurrency/what-is-an-actor-and-why-does-swift-have-them)
