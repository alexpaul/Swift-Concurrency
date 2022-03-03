# Network using Swift 5.5 Concurrency APIs 

```swift
import UIKit

// Model
struct Root: Decodable {
    let results: [Podcast]
}

struct Podcast: Decodable {
    let collectionName: String
    let feedURL: URL?
}

// Network
enum APIClient {
    enum NetworkError: Error {
        case badURL
    }

    static func fetch() async throws -> [Podcast] {
        guard let url = URL(string: "https://itunes.apple.com/search?media=podcast&limit=200&term=swift") else {
            throw NetworkError.badURL
        }

        let (data, _) = try await URLSession.shared.data(from: url)

        let podcasts = try JSONDecoder().decode(Root.self, from: data).results

        return podcasts
    }
}

// UI
final class FeedViewController: UIViewController {
    private var podcastName = UILabel()

    override func viewDidLoad() {
        // In other to call an `async` function from a non-async context the code will
        // need to be wrapped in a `Task` block
        // otherwise this is the compiler error you will receive: 'async' call in a function that does not support concurrency
        Task {
            await fetchPodcasts()
        }
    }

    private func fetchPodcasts() async {
        let podcasts = try? await APIClient.fetch()
        guard let podcasts = podcasts else { return }

        Thread.isMainThread // true

        let firstPodcast = podcasts[0].collectionName
        podcastName.text = firstPodcast

        print("The first result is \(firstPodcast)")
        // The first result is Swift by Sundell
    }
}

// Test
let feedVC = FeedViewController()
feedVC.loadViewIfNeeded()
```
