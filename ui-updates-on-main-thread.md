# UI Updates on the Main Thread

## We have all encounterd UI thread issues

![Screen Shot 2022-03-04 at 7 11 34 AM](https://user-images.githubusercontent.com/1819208/156764008-b4bc3df6-df01-450f-8886-b8bca8c62146.png)

***

## Solving using GCD

```swift
import UIKit

enum Database {
    static func save(completion: @escaping (String) -> Void) {
        DispatchQueue.global().async {
            completion("Saving.....")
        }
    }
}

class ViewController: UIViewController {
    private var messageLabel = UILabel()

    override func viewDidLoad() {
        super.viewDidLoad()

        Database.save { [weak self] message in
            DispatchQueue.main.async {
                self?.messageLabel.text = message
                print(message)
            }
        }
    }
}
```

***

## Solving with Swift 5.5 API's 

```swift 
import UIKit

enum Database {
    static func save(completion: @escaping (String) -> Void) {
        DispatchQueue.global().async {
            completion("Saving.....")
        }
    }

    // Bridging a Closure API with newer async-await syntax
    static func save() async -> String {
        await withCheckedContinuation({ continuation in
            save { string in
                continuation.resume(returning: string)
            }
        })
    }
}

class ViewController: UIViewController {
    private var messageLabel = UILabel()

    override func viewDidLoad() {
        super.viewDidLoad()

        // Task {...} needed here since we're running an asynchronous block of code
        Task {
            let message = await Database.save()
            self.messageLabel.text = message
            print(message)
        }
    }
}
```
