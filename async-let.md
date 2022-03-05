# Using `async let`

`async let` allows for asychronous code to run in parallel.

```swift 
import UIKit

struct User {
    let twiiter: String
    let github: String
    let website: String
}

enum API {
    static func fetchTwitter() async -> String {
        "https://twitter.com/alexpaul"
    }

    static func fetchGithub() async -> String {
        "https://github.com/alexpaul"
    }

    static func fetchWebsite() async -> String {
        "https://alexpaul.dev"
    }
}

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()

        Task {
            async let twitter = API.fetchTwitter()
            async let github = API.fetchGithub()
            async let website = API.fetchWebsite()

            let user = await User(twiiter: twitter, github: github, website: website)

            print("User's socials are \(user)")

            /*
             User's socials are User(twiiter: "https://twitter.com/alexpaul", github: "https://github.com/alexpaul", website: "https://alexpaul.dev")
            */
        }
    }
}
```

## Resources 

* [Swift Language Guide: Calling Asynchronous Functions in Parallel](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html)
* [Hacking with Swift: How to call an async function using async let](https://www.hackingwithswift.com/quick-start/concurrency/how-to-call-an-async-function-using-async-let)
