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
        fetchPodcasts()
    }

    private func fetchPodcasts() {
        Task {
            let podcasts = try? await APIClient.fetch()
            guard let podcasts = podcasts else { return }

            Thread.isMainThread // true
            podcastName.text = podcasts[0].collectionName
        }
    }
}

// Test
let feedVC = FeedViewController()
feedVC.loadViewIfNeeded()
```
