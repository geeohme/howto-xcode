# How to implement async/await for network calls using AI assistance

**Article ID:** KB-082
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 30-35 minutes

## Overview

This guide demonstrates how to leverage AI coding assistants in Xcode 26.1+ to implement modern async/await patterns for network calls in Swift 6. You'll learn to use ChatGPT, Claude, or GitHub Copilot to generate type-safe, concurrent networking code that replaces legacy completion handlers with Swift's native async/await syntax, resulting in cleaner, more maintainable code.

## Prerequisites

- Xcode 26.1+ installed with Swift 6 support
- Basic understanding of Swift networking concepts (URLSession)
- Familiarity with Swift optionals and error handling
- At least one AI provider configured in Xcode (see KB-031, KB-041, or KB-043)
- iOS 15+ deployment target for native async/await URLSession support

## Steps

### Step 1: Set Up Your AI Assistant for Concurrency Help

Before generating async/await code, configure your AI assistant with appropriate context.

**Using ChatGPT or Claude in Xcode:**

1. Open the Coding Assistant panel (View > Show Coding Assistant or `Cmd+Shift+A`)
2. Start a new conversation focused on networking
3. Provide initial context with a prompt like:

```
I'm working on a Swift 6 project targeting iOS 15+. I need to implement
networking using async/await patterns. Please ensure all suggestions:
- Use async/await (not completion handlers)
- Follow Swift 6 concurrency best practices
- Include proper error handling with typed throws
- Use actors for thread-safe state management
- Implement Sendable conformance where needed
```

**Using GitHub Copilot for Xcode:**

1. Install the GitHub Copilot for Xcode extension
2. Configure custom instructions in Settings:
   - Navigate to Copilot settings
   - Add custom instruction: "Always use async/await for asynchronous operations in Swift"
3. Enable inline suggestions for Swift files

### Step 2: Create a Network Service Protocol with AI Assistance

Define a protocol that your networking layer will implement. Ask your AI assistant:

**Prompt:**
```
Create a Swift protocol for a network service that:
1. Uses async/await for all methods
2. Supports generic Decodable responses
3. Handles different HTTP methods (GET, POST, PUT, DELETE)
4. Returns Result types for proper error handling
```

**Expected AI-Generated Code:**

```swift
import Foundation

protocol NetworkServiceProtocol {
    func request<T: Decodable>(
        endpoint: String,
        method: HTTPMethod,
        body: Encodable?
    ) async throws -> T
}

enum HTTPMethod: String {
    case get = "GET"
    case post = "POST"
    case put = "PUT"
    case delete = "DELETE"
}

enum NetworkError: Error {
    case invalidURL
    case invalidResponse
    case httpError(statusCode: Int)
    case decodingError(Error)
    case networkError(Error)
}
```

**Review the generated code for:**
- Proper use of `async throws` keywords
- Generic constraints using `Decodable`
- Comprehensive error enum

### Step 3: Implement the Network Service Using AI

Now ask the AI to implement the actual network service class.

**Prompt:**
```
Implement a NetworkService class that conforms to NetworkServiceProtocol using:
- URLSession with async/await
- Actor isolation for thread safety
- Proper status code validation (200-299 range)
- JSONDecoder for response parsing
- Comprehensive error handling
```

**Expected AI-Generated Implementation:**

```swift
actor NetworkService: NetworkServiceProtocol {
    private let session: URLSession
    private let baseURL: String

    init(baseURL: String, session: URLSession = .shared) {
        self.baseURL = baseURL
        self.session = session
    }

    func request<T: Decodable>(
        endpoint: String,
        method: HTTPMethod,
        body: Encodable? = nil
    ) async throws -> T {
        // Construct URL
        guard let url = URL(string: baseURL + endpoint) else {
            throw NetworkError.invalidURL
        }

        // Configure request
        var request = URLRequest(url: url)
        request.httpMethod = method.rawValue
        request.setValue("application/json", forHTTPHeaderField: "Content-Type")

        // Add body if present
        if let body = body {
            request.httpBody = try? JSONEncoder().encode(body)
        }

        // Perform request
        do {
            let (data, response) = try await session.data(for: request)

            // Validate response
            guard let httpResponse = response as? HTTPURLResponse else {
                throw NetworkError.invalidResponse
            }

            guard (200...299).contains(httpResponse.statusCode) else {
                throw NetworkError.httpError(statusCode: httpResponse.statusCode)
            }

            // Decode response
            do {
                let decoder = JSONDecoder()
                decoder.keyDecodingStrategy = .convertFromSnakeCase
                return try decoder.decode(T.self, from: data)
            } catch {
                throw NetworkError.decodingError(error)
            }

        } catch let error as NetworkError {
            throw error
        } catch {
            throw NetworkError.networkError(error)
        }
    }
}
```

**Key AI-Generated Features to Verify:**
- `actor` keyword for automatic synchronization
- `async throws` in method signature
- Native URLSession async/await: `try await session.data(for:)`
- Proper error propagation and type preservation
- Status code validation
- Snake case to camel case conversion in decoder

### Step 4: Generate Model Structures with AI

Ask the AI to create Codable models for your API responses.

**Prompt:**
```
Create Codable Swift structs for this JSON response:
{
  "id": 123,
  "user_name": "john_doe",
  "email": "john@example.com",
  "created_at": "2025-11-17T10:00:00Z"
}

Make them Sendable for Swift 6 concurrency safety.
```

**Expected AI Response:**

```swift
struct User: Codable, Sendable {
    let id: Int
    let userName: String
    let email: String
    let createdAt: Date

    enum CodingKeys: String, CodingKey {
        case id
        case userName = "user_name"
        case email
        case createdAt = "created_at"
    }
}
```

### Step 5: Implement ViewModel with Async Methods Using AI

Create a ViewModel that uses your network service with proper concurrency.

**Prompt:**
```
Create a SwiftUI ViewModel using @Observable macro that:
1. Uses the NetworkService actor
2. Fetches user data on initialization
3. Handles loading states
4. Updates UI on MainActor
5. Follows Swift 6 concurrency best practices
```

**Expected AI-Generated ViewModel:**

```swift
import SwiftUI

@Observable
@MainActor
class UserViewModel {
    private let networkService: NetworkService

    var user: User?
    var isLoading = false
    var errorMessage: String?

    init(networkService: NetworkService) {
        self.networkService = networkService
    }

    func fetchUser(id: Int) async {
        isLoading = true
        errorMessage = nil

        do {
            let fetchedUser: User = try await networkService.request(
                endpoint: "/users/\(id)",
                method: .get,
                body: nil as String?
            )
            user = fetchedUser
        } catch let error as NetworkError {
            errorMessage = handleNetworkError(error)
        } catch {
            errorMessage = "An unexpected error occurred: \(error.localizedDescription)"
        }

        isLoading = false
    }

    private func handleNetworkError(_ error: NetworkError) -> String {
        switch error {
        case .invalidURL:
            return "Invalid URL configuration"
        case .invalidResponse:
            return "Invalid server response"
        case .httpError(let statusCode):
            return "HTTP error: \(statusCode)"
        case .decodingError(let error):
            return "Failed to parse response: \(error.localizedDescription)"
        case .networkError(let error):
            return "Network error: \(error.localizedDescription)"
        }
    }
}
```

**Verify AI Generated:**
- `@MainActor` attribute for UI updates
- `@Observable` macro for SwiftUI state management
- Actor isolation when calling `networkService`
- Proper error handling with user-friendly messages

### Step 6: Create SwiftUI View with Task Modifiers

Ask AI to generate a view that properly integrates with async operations.

**Prompt:**
```
Create a SwiftUI view that:
1. Uses the UserViewModel
2. Calls fetchUser in a .task modifier
3. Shows loading, error, and success states
4. Handles task cancellation properly
```

**Expected AI-Generated View:**

```swift
import SwiftUI

struct UserProfileView: View {
    @State private var viewModel: UserViewModel
    let userId: Int

    init(userId: Int, networkService: NetworkService) {
        self.userId = userId
        self._viewModel = State(wrappedValue: UserViewModel(networkService: networkService))
    }

    var body: some View {
        Group {
            if viewModel.isLoading {
                ProgressView("Loading user...")
            } else if let errorMessage = viewModel.errorMessage {
                VStack(spacing: 16) {
                    Image(systemName: "exclamationmark.triangle")
                        .font(.largeTitle)
                        .foregroundStyle(.red)
                    Text(errorMessage)
                        .multilineTextAlignment(.center)
                    Button("Retry") {
                        Task {
                            await viewModel.fetchUser(id: userId)
                        }
                    }
                    .buttonStyle(.bordered)
                }
                .padding()
            } else if let user = viewModel.user {
                VStack(alignment: .leading, spacing: 12) {
                    Text(user.userName)
                        .font(.title)
                    Text(user.email)
                        .font(.subheadline)
                        .foregroundStyle(.secondary)
                    Text("Member since: \(user.createdAt.formatted(date: .abbreviated, time: .omitted))")
                        .font(.caption)
                }
                .padding()
            }
        }
        .task {
            await viewModel.fetchUser(id: userId)
        }
    }
}
```

**Key features to verify:**
- `.task` modifier for automatic cancellation
- Proper state handling (loading, error, success)
- MainActor isolation for UI updates

### Step 7: Test Concurrent Network Calls with AI Guidance

Ask AI to help you implement parallel network requests efficiently.

**Prompt:**
```
Show me how to fetch multiple users concurrently using async let and task groups.
Include error handling for partial failures.
```

**Expected AI-Generated Code:**

```swift
extension UserViewModel {
    // Using async let for fixed number of concurrent requests
    func fetchMultipleUsersAsync(ids: [Int]) async {
        guard ids.count >= 2 else { return }

        isLoading = true

        async let user1: User = networkService.request(
            endpoint: "/users/\(ids[0])",
            method: .get,
            body: nil as String?
        )

        async let user2: User = networkService.request(
            endpoint: "/users/\(ids[1])",
            method: .get,
            body: nil as String?
        )

        do {
            let (fetchedUser1, fetchedUser2) = try await (user1, user2)
            // Process both users
            print("Fetched \(fetchedUser1.userName) and \(fetchedUser2.userName)")
        } catch {
            errorMessage = "Failed to fetch users: \(error.localizedDescription)"
        }

        isLoading = false
    }

    // Using task group for dynamic number of concurrent requests
    func fetchAllUsers(ids: [Int]) async -> [User] {
        await withTaskGroup(of: User?.self) { group in
            for id in ids {
                group.addTask {
                    try? await self.networkService.request(
                        endpoint: "/users/\(id)",
                        method: .get,
                        body: nil as String?
                    )
                }
            }

            var users: [User] = []
            for await user in group {
                if let user = user {
                    users.append(user)
                }
            }
            return users
        }
    }
}
```

### Step 8: Add Request Cancellation and Timeout Handling

Enhance your network layer with AI assistance for cancellation support.

**Prompt:**
```
Add timeout handling and proper task cancellation to the NetworkService.
Include a way to configure timeout per request.
```

**Expected Enhancement:**

```swift
extension NetworkService {
    func requestWithTimeout<T: Decodable>(
        endpoint: String,
        method: HTTPMethod,
        body: Encodable? = nil,
        timeout: TimeInterval = 30
    ) async throws -> T {
        try await withTimeout(seconds: timeout) {
            try await self.request(endpoint: endpoint, method: method, body: body)
        }
    }
}

func withTimeout<T>(
    seconds: TimeInterval,
    operation: @escaping () async throws -> T
) async throws -> T {
    try await withThrowingTaskGroup(of: T.self) { group in
        group.addTask {
            try await operation()
        }

        group.addTask {
            try await Task.sleep(for: .seconds(seconds))
            throw NetworkError.networkError(NSError(
                domain: "Timeout",
                code: -1001,
                userInfo: [NSLocalizedDescriptionKey: "Request timed out"]
            ))
        }

        let result = try await group.next()!
        group.cancelAll()
        return result
    }
}
```

## Expected Results

After completing these steps, you should have:

1. **A working network layer** that uses modern async/await syntax instead of completion handlers
2. **Thread-safe code** using actors for concurrent access protection
3. **Type-safe networking** with generic Decodable responses
4. **Proper error handling** with custom error types and user-friendly messages
5. **SwiftUI integration** that properly manages async tasks and cancellation
6. **Concurrent request capabilities** using async let and task groups
7. **Clean, maintainable code** generated with AI assistance that follows Swift 6 best practices

**Visual indicators of success:**
- No data race warnings when building with Swift 6
- UI updates happen smoothly on the main thread
- Network requests cancel properly when views disappear
- Loading states transition correctly (loading → success/error)
- Multiple concurrent requests complete faster than sequential ones

## Troubleshooting

### Common Issue 1: "Expression is 'async' but is not marked with 'await'"

**Problem:** Forgetting to use `await` when calling async functions
**Solution:**
```swift
// ❌ Wrong
let user: User = viewModel.fetchUser(id: 1)

// ✅ Correct
let user: User = await viewModel.fetchUser(id: 1)
```

AI Assistance Tip: Ask your AI assistant to "review my async/await usage and identify missing await keywords"

### Common Issue 2: "Publishing changes from background threads is not allowed"

**Problem:** Updating `@Published` or `@Observable` properties from non-MainActor context
**Solution:** Mark your ViewModel class with `@MainActor`:
```swift
@Observable
@MainActor  // Add this annotation
class UserViewModel {
    // ...
}
```

Or wrap specific updates:
```swift
await MainActor.run {
    self.user = fetchedUser
}
```

AI Assistance Tip: Prompt "Check my ViewModel for MainActor isolation issues"

### Common Issue 3: "Type 'MyModel' does not conform to the 'Sendable' protocol"

**Problem:** Swift 6 strict concurrency checking requires types passed between actors to be Sendable
**Solution:** Add `Sendable` conformance to your models:
```swift
struct User: Codable, Sendable {  // Add Sendable
    // ...
}
```

For classes, use `@unchecked Sendable` only if you're certain about thread safety:
```swift
final class UserCache: @unchecked Sendable {
    private let lock = NSLock()
    private var cache: [Int: User] = [:]
    // Implement thread-safe access
}
```

AI Assistance Tip: Ask "Make my data models Sendable-compliant for Swift 6"

### Common Issue 4: Task Cancellation Not Working

**Problem:** Network requests continue after view disappears
**Solution:** Ensure you're using `.task` modifier, not `.onAppear`:
```swift
// ❌ Wrong - doesn't auto-cancel
.onAppear {
    Task {
        await viewModel.fetchUser(id: userId)
    }
}

// ✅ Correct - auto-cancels when view disappears
.task {
    await viewModel.fetchUser(id: userId)
}
```

AI Assistance Tip: Prompt "Review my SwiftUI lifecycle methods for proper task cancellation"

### Common Issue 5: AI Generates Deprecated Completion Handler Pattern

**Problem:** AI suggests old-style completion handler code
**Solution:** Explicitly instruct the AI in your prompt:
```
Please use async/await, NOT completion handlers or @escaping closures.
Target Swift 6 and iOS 15+. Show modern URLSession async methods only.
```

If AI still generates old patterns, provide a correction:
```
That code uses completion handlers. Please rewrite using async/await instead.
```

## Additional Tips

- **Leverage AI for migration**: If you have existing completion handler code, ask AI to "convert this completion handler code to async/await"
- **Use inline AI suggestions**: GitHub Copilot in Xcode can auto-complete async/await patterns as you type
- **Ask for explanations**: If AI generates unfamiliar concurrency code, ask "explain how this actor isolation works"
- **Request alternatives**: Try prompting "show me 3 different ways to structure this network call with async/await"
- **Verify with documentation**: Cross-reference AI suggestions with official Apple documentation
- **Build incrementally**: Start with simple GET requests before tackling complex concurrent operations
- **Enable strict concurrency checking**: Add `-strict-concurrency=complete` to build settings to catch issues early
- **Use type inference wisely**: AI-generated code might include unnecessary type annotations; Swift can often infer types in async contexts
- **Consider retry logic**: Ask AI to add exponential backoff retry mechanisms for failed requests
- **Mock for testing**: Request AI to generate mock NetworkService implementations for unit tests

## Related Articles

- KB-031: Access Coding Assistant in Xcode 26 (ChatGPT)
- KB-041: Create Anthropic API Account
- KB-043: Add Claude as Model Provider in Xcode
- KB-051: Use AI to Generate Unit Tests for Existing Code
- KB-055: Have AI Explain Error Messages and Suggest Fixes
- KB-057: Generate Codable Models from JSON Using AI
- KB-058: Ask AI to Add Comprehensive Error Handling to Functions

## Sources

- Async await in Swift explained with code examples - SwiftLee - https://www.avanderlee.com/swift/async-await/ (Accessed: November 17, 2025)
- How to Use URLSession with Async/Await for Network Requests in Swift - SwiftLee - https://www.avanderlee.com/concurrency/urlsession-async-await-network-requests-in-swift/ (Accessed: November 17, 2025)
- Approachable Concurrency in Swift 6.2: A Clear Guide - SwiftLee - https://www.avanderlee.com/concurrency/approachable-concurrency-in-swift-6-2-a-clear-guide/ (Accessed: November 17, 2025)
- Use async/await with URLSession - WWDC21 - Apple Developer - https://developer.apple.com/videos/play/wwdc2021/10095/ (Accessed: November 17, 2025)
- Async/Await Patterns for Clean Networking in Swift - Medium - https://medium.com/@rashadsh/async-await-patterns-for-clean-networking-in-swift-a28dd099d060 (Accessed: November 17, 2025)
- Understanding Concurrency in Swift 6 with Sendable protocol, MainActor, and async-await - Medium - https://medium.com/@egzonpllana/understanding-concurrency-in-swift-6-with-sendable-protocol-mainactor-and-async-await-5ccfdc0ca2b6 (Accessed: November 17, 2025)
- Developing in Xcode with AI: Comparing Copilot, Claude, MCP, Cursor, and More - Medium - https://medium.com/@avilevin23/developing-in-xcode-with-ai-comparing-copilot-claude-mcp-cursor-and-more-97a42254506c (Accessed: November 17, 2025)
- GitHub Copilot for Xcode: AI-Powered Code Suggestions and Chat Assistant - Bright Coding - https://www.blog.brightcoding.dev/2025/09/30/github-copilot-for-xcode-ai-powered-code-suggestions-and-chat-assistant-right-inside-your-ide/ (Accessed: November 17, 2025)
- AI features in Xcode 16: is it good? - Swift with Vincent - https://www.swiftwithvincent.com/blog/ai-features-in-xcode-16-is-it-good (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
