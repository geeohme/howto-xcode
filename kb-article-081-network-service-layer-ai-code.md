# How to create a network service layer with AI-generated code

**Article ID:** KB-081
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 35-40 minutes

## Overview

This guide demonstrates how to leverage Xcode 26's AI Assistant to build a production-ready, thread-safe network service layer using Swift 6 concurrency features. You'll learn to combine AI code generation with modern URLSession patterns to create a scalable, maintainable networking architecture that meets Swift 6's strict data race checking requirements.

## Prerequisites

- Xcode 26.1 or later installed with AI Assistant enabled
- macOS 26 Tahoe (required for AI Assistant features)
- Basic understanding of Swift async/await and protocols
- An active AI model provider (ChatGPT, Claude, or local model)
- Related articles: KB-031 (Access Coding Assistant), KB-046 (Switch between AI providers)

## Steps

### Step 1: Set up your networking layer structure with AI guidance

Open your Xcode project and create a new Swift file named `NetworkService.swift`. Before writing code manually, you'll use the AI Assistant to generate the foundational structure.

Type the following comment at the top of the file:

```swift
// Create a thread-safe network service protocol with async methods for:
// - Generic GET requests that decode JSON responses
// - POST requests with encodable bodies
// - Error handling with custom NetworkError enum
// - Request and response logging
// All code must be Swift 6 compliant with Sendable conformance
```

Press **Cmd + Shift + A** to invoke the AI Assistant. The assistant will generate a complete protocol structure similar to this:

```swift
import Foundation

enum NetworkError: Error, Sendable {
    case invalidURL
    case invalidResponse
    case httpError(statusCode: Int)
    case decodingError(Error)
    case encodingError(Error)
    case networkError(Error)
}

protocol NetworkServiceProtocol: Sendable {
    func get<T: Decodable & Sendable>(
        from endpoint: String,
        parameters: [String: String]?
    ) async throws -> T

    func post<T: Decodable & Sendable, U: Encodable & Sendable>(
        to endpoint: String,
        body: U
    ) async throws -> T
}
```

**Key Points:**
- The AI understands Swift 6's `Sendable` requirement for thread safety
- All generic types include `Sendable` conformance to prevent data races
- Error types are marked `Sendable` for safe cross-concurrency-domain usage

### Step 2: Generate the URLSession-based implementation

Below your protocol definition, add this comment describing the implementation:

```swift
// Implement NetworkServiceProtocol with a class that:
// - Uses URLSession with custom configuration
// - Includes a base URL property
// - Implements proper timeout and cache policies
// - Logs all requests and responses
// - Handles HTTP status codes correctly
// - Uses Swift 6 actor isolation for thread safety
```

Press **Cmd + Shift + A** again. The AI will generate an actor-based implementation:

```swift
actor NetworkService: NetworkServiceProtocol {
    private let session: URLSession
    private let baseURL: String

    init(baseURL: String, configuration: URLSessionConfiguration = .default) {
        self.baseURL = baseURL

        // Configure URLSession for optimal performance
        configuration.timeoutIntervalForRequest = 30
        configuration.timeoutIntervalForResource = 300
        configuration.requestCachePolicy = .returnCacheDataElseLoad
        configuration.httpMaximumConnectionsPerHost = 5

        self.session = URLSession(configuration: configuration)
    }

    func get<T: Decodable & Sendable>(
        from endpoint: String,
        parameters: [String: String]? = nil
    ) async throws -> T {
        let url = try buildURL(endpoint: endpoint, parameters: parameters)

        print("🌐 GET Request: \(url.absoluteString)")

        let (data, response) = try await session.data(from: url)

        try validateResponse(response)

        print("✅ Response received: \(data.count) bytes")

        return try decodeResponse(data: data)
    }

    func post<T: Decodable & Sendable, U: Encodable & Sendable>(
        to endpoint: String,
        body: U
    ) async throws -> T {
        let url = try buildURL(endpoint: endpoint, parameters: nil)
        var request = URLRequest(url: url)
        request.httpMethod = "POST"
        request.setValue("application/json", forHTTPHeaderField: "Content-Type")

        do {
            request.httpBody = try JSONEncoder().encode(body)
        } catch {
            throw NetworkError.encodingError(error)
        }

        print("🌐 POST Request: \(url.absoluteString)")

        let (data, response) = try await session.data(for: request)

        try validateResponse(response)

        print("✅ Response received: \(data.count) bytes")

        return try decodeResponse(data: data)
    }

    // MARK: - Private Helper Methods

    private func buildURL(
        endpoint: String,
        parameters: [String: String]?
    ) throws -> URL {
        guard var components = URLComponents(string: baseURL + endpoint) else {
            throw NetworkError.invalidURL
        }

        if let parameters = parameters {
            components.queryItems = parameters.map {
                URLQueryItem(name: $0.key, value: $0.value)
            }
        }

        guard let url = components.url else {
            throw NetworkError.invalidURL
        }

        return url
    }

    private func validateResponse(_ response: URLResponse) throws {
        guard let httpResponse = response as? HTTPURLResponse else {
            throw NetworkError.invalidResponse
        }

        guard (200...299).contains(httpResponse.statusCode) else {
            throw NetworkError.httpError(statusCode: httpResponse.statusCode)
        }
    }

    private func decodeResponse<T: Decodable>(data: Data) throws -> T {
        do {
            let decoder = JSONDecoder()
            decoder.keyDecodingStrategy = .convertFromSnakeCase
            return try decoder.decode(T.self, from: data)
        } catch {
            throw NetworkError.decodingError(error)
        }
    }
}
```

**Why use an actor?**
- Actors provide automatic thread safety in Swift 6
- All properties are isolated, preventing data races
- Methods are called asynchronously, ensuring serial execution
- This satisfies Swift 6's strict concurrency checking

### Step 3: Create endpoint definitions with AI assistance

Create a new file `APIEndpoint.swift`. Add this prompt:

```swift
// Create a protocol-based endpoint system where each endpoint defines:
// - HTTP method
// - Path
// - Query parameters
// - Request headers
// All types must be Sendable for Swift 6 compatibility
```

Press **Cmd + Shift + A**:

```swift
import Foundation

protocol APIEndpoint: Sendable {
    var path: String { get }
    var method: HTTPMethod { get }
    var parameters: [String: String]? { get }
    var headers: [String: String]? { get }
}

enum HTTPMethod: String, Sendable {
    case get = "GET"
    case post = "POST"
    case put = "PUT"
    case delete = "DELETE"
    case patch = "PATCH"
}

// Example endpoint implementation
enum UserEndpoint: APIEndpoint {
    case getUser(id: String)
    case createUser
    case updateUser(id: String)

    var path: String {
        switch self {
        case .getUser(let id):
            return "/users/\(id)"
        case .createUser:
            return "/users"
        case .updateUser(let id):
            return "/users/\(id)"
        }
    }

    var method: HTTPMethod {
        switch self {
        case .getUser:
            return .get
        case .createUser:
            return .post
        case .updateUser:
            return .put
        }
    }

    var parameters: [String: String]? {
        return nil
    }

    var headers: [String: String]? {
        return ["Authorization": "Bearer YOUR_TOKEN"]
    }
}
```

### Step 4: Generate model types with Sendable conformance

Create `NetworkModels.swift` and use this AI prompt:

```swift
// Generate example API models for a user management system:
// - User model with id, name, email, createdAt
// - UserCreateRequest for POST requests
// - UserResponse wrapper with success/error fields
// All models must be Codable and Sendable for Swift 6
```

Press **Cmd + Shift + A**:

```swift
import Foundation

struct User: Codable, Sendable {
    let id: String
    let name: String
    let email: String
    let createdAt: Date
}

struct UserCreateRequest: Codable, Sendable {
    let name: String
    let email: String
}

struct APIResponse<T: Codable & Sendable>: Codable, Sendable {
    let success: Bool
    let data: T?
    let error: String?
}
```

### Step 5: Implement a service wrapper with AI-generated convenience methods

Create `UserService.swift` with this prompt:

```swift
// Create a UserService class that:
// - Wraps NetworkService for user-specific operations
// - Implements fetchUser, createUser, updateUser methods
// - Handles API response unwrapping
// - Uses MainActor for UI-bound properties
// - Includes loading states and error handling
```

Press **Cmd + Shift + A**:

```swift
import Foundation

@MainActor
final class UserService: ObservableObject {
    @Published var isLoading = false
    @Published var lastError: NetworkError?

    private let networkService: NetworkService

    init(networkService: NetworkService) {
        self.networkService = networkService
    }

    func fetchUser(id: String) async -> User? {
        isLoading = true
        lastError = nil

        defer { isLoading = false }

        do {
            let response: APIResponse<User> = try await networkService.get(
                from: "/users/\(id)",
                parameters: nil
            )

            guard response.success, let user = response.data else {
                lastError = .invalidResponse
                return nil
            }

            return user
        } catch let error as NetworkError {
            lastError = error
            return nil
        } catch {
            lastError = .networkError(error)
            return nil
        }
    }

    func createUser(name: String, email: String) async -> User? {
        isLoading = true
        lastError = nil

        defer { isLoading = false }

        do {
            let request = UserCreateRequest(name: name, email: email)
            let response: APIResponse<User> = try await networkService.post(
                to: "/users",
                body: request
            )

            guard response.success, let user = response.data else {
                lastError = .invalidResponse
                return nil
            }

            return user
        } catch let error as NetworkError {
            lastError = error
            return nil
        } catch {
            lastError = .networkError(error)
            return nil
        }
    }
}
```

### Step 6: Generate comprehensive unit tests with AI

Create `NetworkServiceTests.swift` in your test target. Use this prompt:

```swift
// Generate XCTest unit tests for NetworkService that:
// - Test successful GET and POST requests
// - Test error handling for invalid URLs, HTTP errors, decoding errors
// - Use URLProtocol mocking for network responses
// - Include async test methods
// - Test thread safety with multiple concurrent requests
```

Press **Cmd + Shift + A** to generate complete test coverage:

```swift
import XCTest
@testable import YourApp

final class NetworkServiceTests: XCTestCase {
    var networkService: NetworkService!

    override func setUp() async throws {
        let config = URLSessionConfiguration.ephemeral
        config.protocolClasses = [MockURLProtocol.self]
        networkService = NetworkService(
            baseURL: "https://api.example.com",
            configuration: config
        )
    }

    func testSuccessfulGETRequest() async throws {
        // Mock response
        let user = User(
            id: "123",
            name: "John Doe",
            email: "john@example.com",
            createdAt: Date()
        )
        let response = APIResponse(success: true, data: user, error: nil)
        MockURLProtocol.mockData = try JSONEncoder().encode(response)
        MockURLProtocol.mockResponse = HTTPURLResponse(
            url: URL(string: "https://api.example.com/users/123")!,
            statusCode: 200,
            httpVersion: nil,
            headerFields: nil
        )

        // Execute request
        let result: APIResponse<User> = try await networkService.get(
            from: "/users/123",
            parameters: nil
        )

        XCTAssertTrue(result.success)
        XCTAssertEqual(result.data?.id, "123")
        XCTAssertEqual(result.data?.name, "John Doe")
    }

    func testHTTPErrorHandling() async {
        MockURLProtocol.mockResponse = HTTPURLResponse(
            url: URL(string: "https://api.example.com/users/123")!,
            statusCode: 404,
            httpVersion: nil,
            headerFields: nil
        )

        do {
            let _: APIResponse<User> = try await networkService.get(
                from: "/users/123",
                parameters: nil
            )
            XCTFail("Expected error to be thrown")
        } catch let error as NetworkError {
            if case .httpError(let statusCode) = error {
                XCTAssertEqual(statusCode, 404)
            } else {
                XCTFail("Expected httpError, got \(error)")
            }
        } catch {
            XCTFail("Expected NetworkError, got \(error)")
        }
    }

    func testConcurrentRequests() async throws {
        MockURLProtocol.mockData = try JSONEncoder().encode(
            APIResponse(success: true, data: User(
                id: "123",
                name: "Test",
                email: "test@example.com",
                createdAt: Date()
            ), error: nil)
        )
        MockURLProtocol.mockResponse = HTTPURLResponse(
            url: URL(string: "https://api.example.com/users/123")!,
            statusCode: 200,
            httpVersion: nil,
            headerFields: nil
        )

        // Execute 10 concurrent requests
        try await withThrowingTaskGroup(of: APIResponse<User>.self) { group in
            for i in 1...10 {
                group.addTask {
                    try await self.networkService.get(
                        from: "/users/\(i)",
                        parameters: nil
                    )
                }
            }

            var results = [APIResponse<User>]()
            for try await result in group {
                results.append(result)
            }

            XCTAssertEqual(results.count, 10)
            XCTAssertTrue(results.allSatisfy { $0.success })
        }
    }
}

// MARK: - Mock URLProtocol

class MockURLProtocol: URLProtocol {
    static var mockData: Data?
    static var mockResponse: HTTPURLResponse?
    static var mockError: Error?

    override class func canInit(with request: URLRequest) -> Bool {
        return true
    }

    override class func canonicalRequest(for request: URLRequest) -> URLRequest {
        return request
    }

    override func startLoading() {
        if let error = MockURLProtocol.mockError {
            client?.urlProtocol(self, didFailWithError: error)
            return
        }

        if let response = MockURLProtocol.mockResponse {
            client?.urlProtocol(self, didReceive: response, cacheStoragePolicy: .notAllowed)
        }

        if let data = MockURLProtocol.mockData {
            client?.urlProtocol(self, didLoad: data)
        }

        client?.urlProtocolDidFinishLoading(self)
    }

    override func stopLoading() {}
}
```

### Step 7: Integrate into a SwiftUI view

Create an example view that demonstrates the network layer in action:

```swift
// Generate a SwiftUI view that:
// - Uses UserService as a StateObject
// - Displays user information
// - Shows loading states
// - Handles and displays errors
// - Includes a refresh button
```

Press **Cmd + Shift + A**:

```swift
import SwiftUI

struct UserDetailView: View {
    @StateObject private var userService: UserService
    @State private var user: User?
    let userId: String

    init(userId: String, networkService: NetworkService) {
        self.userId = userId
        _userService = StateObject(wrappedValue: UserService(networkService: networkService))
    }

    var body: some View {
        Group {
            if userService.isLoading {
                ProgressView("Loading user...")
            } else if let error = userService.lastError {
                ErrorView(error: error) {
                    Task {
                        await loadUser()
                    }
                }
            } else if let user = user {
                UserInfoView(user: user)
            } else {
                ContentUnavailableView(
                    "No User Data",
                    systemImage: "person.slash",
                    description: Text("Unable to load user information")
                )
            }
        }
        .navigationTitle("User Details")
        .toolbar {
            ToolbarItem(placement: .primaryAction) {
                Button("Refresh") {
                    Task {
                        await loadUser()
                    }
                }
            }
        }
        .task {
            await loadUser()
        }
    }

    private func loadUser() async {
        user = await userService.fetchUser(id: userId)
    }
}

struct UserInfoView: View {
    let user: User

    var body: some View {
        List {
            Section("Basic Information") {
                LabeledContent("Name", value: user.name)
                LabeledContent("Email", value: user.email)
                LabeledContent("ID", value: user.id)
            }
        }
    }
}

struct ErrorView: View {
    let error: NetworkError
    let retryAction: () -> Void

    var body: some View {
        ContentUnavailableView {
            Label("Error", systemImage: "exclamationmark.triangle")
        } description: {
            Text(errorMessage)
        } actions: {
            Button("Retry", action: retryAction)
        }
    }

    private var errorMessage: String {
        switch error {
        case .invalidURL:
            return "The request URL is invalid"
        case .invalidResponse:
            return "The server response is invalid"
        case .httpError(let code):
            return "HTTP error: \(code)"
        case .decodingError:
            return "Failed to decode the response"
        case .encodingError:
            return "Failed to encode the request"
        case .networkError(let error):
            return "Network error: \(error.localizedDescription)"
        }
    }
}
```

## Expected Results

After completing these steps, you should have:

1. **Thread-safe networking layer**: An actor-based `NetworkService` that prevents data races and conforms to Swift 6's strict concurrency checking
2. **Type-safe endpoints**: Protocol-based endpoint definitions that ensure compile-time correctness
3. **Reusable models**: Codable and Sendable model types ready for JSON serialization
4. **Service wrappers**: High-level service classes with MainActor isolation for UI updates
5. **Comprehensive tests**: Unit tests covering success cases, error handling, and concurrent requests
6. **Working UI integration**: SwiftUI views that properly handle loading states, errors, and data display

When you run your app and navigate to the UserDetailView:
- Loading spinner appears immediately
- User data loads and displays in the list
- Error handling shows user-friendly messages with retry options
- All network operations run without blocking the UI thread

## Troubleshooting

### Common Issue 1: "Sendable" conformance errors
**Problem:** Compiler errors about "Type 'X' does not conform to the 'Sendable' protocol"
**Solution:** Ensure all types used across actor boundaries are marked `Sendable`. For custom types, add explicit conformance: `struct MyType: Sendable { }`. For generic types, add `Sendable` constraints: `<T: Decodable & Sendable>`.

### Common Issue 2: AI generates Swift 5.5 code instead of Swift 6
**Problem:** Generated code uses `@escaping` closures or lacks `Sendable` conformance
**Solution:** Be explicit in your prompt: "Generate Swift 6 compliant code with full Sendable conformance and actor isolation." You can also specify "Do not use completion handlers, use async/await only."

### Common Issue 3: URLSession.data(from:) not suspending properly
**Problem:** Network requests block the main thread or cause UI freezes
**Solution:** Ensure your `NetworkService` is an `actor` or uses proper task isolation. When calling from `@MainActor` contexts, use `Task { await networkService.get(...) }` to move work off the main actor.

### Common Issue 4: Decoding errors with snake_case API responses
**Problem:** JSON decoding fails when API uses snake_case (e.g., "created_at")
**Solution:** Configure JSONDecoder with `keyDecodingStrategy = .convertFromSnakeCase` as shown in Step 2. The AI should include this, but verify it's present in the `decodeResponse` method.

### Common Issue 5: AI Assistant not available (Cmd+Shift+A does nothing)
**Problem:** Keyboard shortcut doesn't invoke the AI Assistant
**Solution:** Verify you're running macOS 26 Tahoe (required for AI features). Check Xcode → Settings → Intelligence to ensure an AI provider is configured. Refer to KB-031 for setup instructions.

## Additional Tips

- **Customize AI generation levels**: In Xcode → Settings → Intelligence, you can set code generation to "minimal," "balanced," or "comprehensive" to control how much code the AI produces
- **Iterative refinement**: If the AI-generated code isn't exactly what you need, add more specific comments and invoke Cmd+Shift+A again. For example: "Add request retry logic with exponential backoff for failed requests"
- **Combine AI with manual review**: Always review AI-generated code for security concerns, especially around API key handling and data validation
- **Use AI for documentation**: After generating your network layer, select the code and use AI to generate comprehensive documentation comments
- **Protocol-first design**: Define protocols before implementations to help the AI understand your architectural intent
- **Cache configuration**: For production apps, customize `URLSessionConfiguration` based on your needs (e.g., `.ephemeral` for sensitive data)
- **Model providers**: Claude tends to generate more detailed error handling, while GPT-5 Reasoning is better for complex architectural decisions (see KB-050)

## Related Articles

- KB-031: How to access the coding assistant in Xcode 26 (ChatGPT)
- KB-046: How to switch between ChatGPT and Claude in the coding assistant
- KB-050: How to compare ChatGPT and Claude responses for the same coding task
- KB-051: How to use AI to generate unit tests for existing code
- KB-053: How to ask AI to implement MVVM architecture for a feature
- KB-055: How to have AI explain error messages and suggest fixes
- KB-057: How to generate Codable models from JSON using AI

## Sources

- Writing code with intelligence in Xcode - Apple Developer Documentation (https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode) (Accessed: November 17, 2025)
- URLSession - Apple Developer Documentation (https://developer.apple.com/documentation/foundation/urlsession) (Accessed: November 17, 2025)
- Use async/await with URLSession - WWDC21 - Apple Developer (https://developer.apple.com/videos/play/wwdc2021/10095/) (Accessed: November 17, 2025)
- Meet async/await in Swift - WWDC21 - Apple Developer (https://developer.apple.com/videos/play/wwdc2021/10132/) (Accessed: November 17, 2025)
- Building a generic, thread-safe Networking Layer in Swift 6 - Medium (https://medium.com/@egzonpllana/building-a-generic-thread-safe-networking-layer-in-swift-6-927fa1d0cce8) (Accessed: November 17, 2025)
- Code with Intelligence — xCode 26 - Medium (https://medium.com/@sachinsiwal/code-with-intelligence-xcode-26-9203a8ce665e) (Accessed: November 17, 2025)
- What's New in Xcode 26: WWDC 2025 Highlights - 200OK Solutions (https://200oksolutions.com/blog/whats-new-in-xcode-26-smarter-faster-more-powerful-wwdc-2025-highlights/) (Accessed: November 17, 2025)
- NetworkLayerSwift6 - GitHub (https://github.com/egzonpllana/NetworkLayerSwift6) (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
