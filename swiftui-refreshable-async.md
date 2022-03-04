# Using `refreshable` in SwiftUI 

```swift
import SwiftUI

// MARK: - Model
struct Root: Decodable {
    let results: [Podcast]
}

struct Podcast: Decodable, Identifiable {
    let collectionName: String
    let artistName: String
    let feedURL: URL?
    let trackId: Int

    var id: Int { trackId }
}

// MARK: - Network
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

// MARK: - UI
struct ContentView: View {
    @State private var podcasts = [Podcast(collectionName:"Pull to fetch more podcasts...",
                                           artistName: "Alex Paul",
                                           feedURL: nil,
                                           trackId: 0)]

    var body: some View {
        List(podcasts) { podcast in
            VStack(alignment: .leading) {
                Text(podcast.collectionName)
                    .font(.headline)
                Text(podcast.artistName)
                    .foregroundColor(.secondary)
            }
        }
        .refreshable {
            do {
                podcasts = try await APIClient.fetch()
            } catch {
                podcasts = []
            }
        }
    }
}
```
