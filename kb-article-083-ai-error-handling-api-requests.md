# How to use AI to generate comprehensive error handling for API requests

**Article ID:** KB-083
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 30-35 minutes

## Overview

Learn to leverage Xcode 26.1's AI-powered Coding Intelligence to generate robust, production-ready error handling for network requests. This guide demonstrates how to use AI assistants (ChatGPT, Claude, or custom models) to create comprehensive error handling that covers network failures, HTTP status codes, decoding errors, and custom error types with Swift 6's modern async/await patterns.

## Prerequisites

- Xcode 26.1 or later installed on macOS
- Apple Intelligence enabled in System Settings ([KB-003](/kb-article-003-how-to-enable-apple-intelligence-in-macos-system-settings-for-coding-intelligence.md))
- Coding Intelligence configured in Xcode Preferences ([KB-007](/kb-article-007-how-to-access-the-new-intelligence-settings-tab-in-xcode-preferences.md))
- At least one AI model provider configured (ChatGPT, Claude, or custom)
- Basic understanding of Swift async/await and URLSession
- Familiarity with Swift's error handling (do-try-catch)

## Steps

### Step 1: Set Up Your Networking Layer Base Structure

Before using AI to generate error handling, create a basic networking structure that AI can enhance. Create a new Swift file named `NetworkService.swift`:

```swift
import Foundation

// Basic structure for AI to expand upon
class NetworkService {
    static let shared = NetworkService()
    private init() {}

    // Base method that needs comprehensive error handling
    func fetchData<T: Decodable>(from url: URL) async throws -> T {
        let (data, response) = try await URLSession.shared.data(from: url)
        let decodedData = try JSONDecoder().decode(T.self, from: data)
        return decodedData
    }
}
```

**Why this approach:** Starting with a basic structure gives the AI context about your existing code patterns, leading to more relevant and consistent suggestions.

### Step 2: Craft an Effective AI Prompt for Error Types

Position your cursor above the `NetworkService` class and invoke the AI assistant (Cmd + Shift + A). Use this comprehensive prompt:

```
Create a comprehensive error handling system for API requests with the following requirements:
1. Define a custom NetworkError enum conforming to Error and LocalizedError
2. Include cases for: network connectivity issues, HTTP status codes (client 4xx and server 5xx errors), invalid URLs, decoding failures, timeout errors, and unauthorized access
3. Each error case should provide user-friendly localized descriptions
4. Include associated values to capture detailed error information (status codes, underlying errors)
5. Use Swift 6 best practices and modern error handling patterns
```

**Prompt engineering tips:**
- Be specific about error categories you need
- Request Swift 6 compatibility explicitly
- Ask for LocalizedError conformance for user-facing messages
- Specify associated values for detailed error context

### Step 3: Review and Refine AI-Generated Error Types

The AI will generate a custom error enum. Review the output, which should resemble:

```swift
enum NetworkError: Error, LocalizedError {
    case invalidURL
    case noInternetConnection
    case requestTimeout
    case unauthorized
    case clientError(statusCode: Int)
    case serverError(statusCode: Int)
    case decodingError(Error)
    case unknownError(Error)

    var errorDescription: String? {
        switch self {
        case .invalidURL:
            return "The URL provided is invalid."
        case .noInternetConnection:
            return "No internet connection available. Please check your network settings."
        case .requestTimeout:
            return "The request timed out. Please try again."
        case .unauthorized:
            return "You are not authorized to access this resource."
        case .clientError(let statusCode):
            return "Client error occurred with status code: \(statusCode)."
        case .serverError(let statusCode):
            return "Server error occurred with status code: \(statusCode)."
        case .decodingError(let error):
            return "Failed to decode response: \(error.localizedDescription)"
        case .unknownError(let error):
            return "An unknown error occurred: \(error.localizedDescription)"
        }
    }

    var recoverySuggestion: String? {
        switch self {
        case .noInternetConnection:
            return "Please check your WiFi or cellular data connection and try again."
        case .requestTimeout:
            return "The server took too long to respond. Please try again later."
        case .unauthorized:
            return "Please sign in again to continue."
        case .serverError:
            return "The server is experiencing issues. Please try again later."
        default:
            return "Please try again or contact support if the problem persists."
        }
    }
}
```

**What to verify:**
- All necessary error cases are covered
- Associated values capture relevant error details
- User-facing messages are clear and actionable
- Recovery suggestions provide helpful guidance

### Step 4: Generate Enhanced Network Request Method

Select your basic `fetchData` method and use AI to enhance it with comprehensive error handling. Prompt:

```
Enhance this network request method with:
1. Comprehensive error handling for all NetworkError cases
2. HTTP status code validation (200-299 success range)
3. URLError mapping to custom NetworkError types
4. Proper async/await error propagation
5. Timeout handling
6. Response validation before decoding
```

The AI should generate an enhanced method:

```swift
func fetchData<T: Decodable>(
    from url: URL,
    timeout: TimeInterval = 30
) async throws -> T {
    // Validate URL scheme
    guard url.scheme == "https" || url.scheme == "http" else {
        throw NetworkError.invalidURL
    }

    // Configure request with timeout
    var request = URLRequest(url: url)
    request.timeoutInterval = timeout

    do {
        let (data, response) = try await URLSession.shared.data(for: request)

        // Validate HTTP response
        guard let httpResponse = response as? HTTPURLResponse else {
            throw NetworkError.unknownError(
                NSError(domain: "NetworkService", code: -1, userInfo: [
                    NSLocalizedDescriptionKey: "Invalid response type"
                ])
            )
        }

        // Handle HTTP status codes
        switch httpResponse.statusCode {
        case 200...299:
            // Success range - proceed with decoding
            break
        case 401:
            throw NetworkError.unauthorized
        case 400...499:
            throw NetworkError.clientError(statusCode: httpResponse.statusCode)
        case 500...599:
            throw NetworkError.serverError(statusCode: httpResponse.statusCode)
        default:
            throw NetworkError.unknownError(
                NSError(domain: "NetworkService", code: httpResponse.statusCode)
            )
        }

        // Attempt decoding with error handling
        do {
            let decodedData = try JSONDecoder().decode(T.self, from: data)
            return decodedData
        } catch {
            throw NetworkError.decodingError(error)
        }

    } catch let error as NetworkError {
        // Re-throw custom network errors
        throw error
    } catch let urlError as URLError {
        // Map URLError to NetworkError
        switch urlError.code {
        case .notConnectedToInternet, .networkConnectionLost:
            throw NetworkError.noInternetConnection
        case .timedOut:
            throw NetworkError.requestTimeout
        default:
            throw NetworkError.unknownError(urlError)
        }
    } catch {
        // Catch-all for unexpected errors
        throw NetworkError.unknownError(error)
    }
}
```

### Step 5: Generate Retry Logic with Exponential Backoff

For production-ready API handling, add retry logic. Prompt the AI:

```
Add a retry mechanism with exponential backoff to the network service:
1. Maximum 3 retry attempts for transient errors
2. Exponential backoff: 1s, 2s, 4s delays
3. Only retry for network errors and server errors (5xx), not client errors (4xx)
4. Use async/await pattern
```

AI-generated retry wrapper:

```swift
func fetchDataWithRetry<T: Decodable>(
    from url: URL,
    maxRetries: Int = 3,
    timeout: TimeInterval = 30
) async throws -> T {
    var lastError: Error?

    for attempt in 0..<maxRetries {
        do {
            return try await fetchData(from: url, timeout: timeout)
        } catch let error as NetworkError {
            lastError = error

            // Determine if error is retryable
            let shouldRetry: Bool = switch error {
            case .noInternetConnection, .requestTimeout, .serverError:
                true
            case .unauthorized, .clientError, .invalidURL, .decodingError:
                false
            case .unknownError:
                true
            }

            guard shouldRetry && attempt < maxRetries - 1 else {
                throw error
            }

            // Exponential backoff: 2^attempt seconds
            let delay = TimeInterval(pow(2.0, Double(attempt)))
            try await Task.sleep(nanoseconds: UInt64(delay * 1_000_000_000))

        } catch {
            throw error
        }
    }

    throw lastError ?? NetworkError.unknownError(
        NSError(domain: "NetworkService", code: -1)
    )
}
```

**Key features generated:**
- Swift 6 pattern matching in switch expressions
- Exponential backoff calculation
- Conditional retry logic based on error type
- Proper async sleep using Task.sleep

### Step 6: Add Request/Response Logging for Debugging

Ask AI to generate logging utilities for debugging:

```
Create a debug logging system for network requests that:
1. Logs request details (URL, method, headers)
2. Logs response status and data size
3. Logs errors with full details
4. Uses os.log for proper logging levels
5. Only logs in DEBUG builds
```

AI-generated logging:

```swift
import os.log

extension NetworkService {
    private static let logger = Logger(
        subsystem: Bundle.main.bundleIdentifier ?? "NetworkService",
        category: "Networking"
    )

    private func logRequest(url: URL, method: String = "GET") {
        #if DEBUG
        Self.logger.debug("🌐 Request: \(method) \(url.absoluteString)")
        #endif
    }

    private func logResponse(url: URL, statusCode: Int, dataSize: Int) {
        #if DEBUG
        Self.logger.info("✅ Response: \(statusCode) from \(url.absoluteString) (\(dataSize) bytes)")
        #endif
    }

    private func logError(url: URL, error: Error) {
        #if DEBUG
        Self.logger.error("❌ Error from \(url.absoluteString): \(error.localizedDescription)")
        #endif
    }
}
```

Integrate logging into your fetch method at appropriate points (before request, after response, in error handling).

### Step 7: Generate Usage Examples and Tests

Finally, ask AI to generate practical usage examples:

```
Create example usage code that demonstrates:
1. Successful API call with error handling
2. Handling specific error cases with user feedback
3. Using the retry mechanism
4. SwiftUI integration with error state management
```

AI-generated example:

```swift
// Example model
struct User: Codable {
    let id: Int
    let name: String
    let email: String
}

// Example usage in ViewModel
@MainActor
class UserViewModel: ObservableObject {
    @Published var user: User?
    @Published var errorMessage: String?
    @Published var isLoading = false

    func fetchUser(userId: Int) async {
        isLoading = true
        errorMessage = nil

        guard let url = URL(string: "https://api.example.com/users/\(userId)") else {
            errorMessage = "Invalid URL"
            isLoading = false
            return
        }

        do {
            let fetchedUser: User = try await NetworkService.shared
                .fetchDataWithRetry(from: url)
            user = fetchedUser
        } catch let error as NetworkError {
            // Handle specific error types
            switch error {
            case .noInternetConnection:
                errorMessage = "No internet connection. Please check your network."
            case .unauthorized:
                errorMessage = "Please log in again."
            case .serverError(let code):
                errorMessage = "Server error (\(code)). Please try again later."
            default:
                errorMessage = error.localizedDescription
            }
        } catch {
            errorMessage = "An unexpected error occurred."
        }

        isLoading = false
    }
}
```

### Step 8: Configure AI Code Generation Level

Optimize AI assistance for your workflow by adjusting settings:

1. Go to **Xcode → Settings → Intelligence**
2. Set **Code Generation Level** to **Comprehensive** for detailed error handling
3. Enable **Auto-apply suggestions** if you trust the AI output
4. Configure your preferred model (Claude recommended for code generation)

**Model recommendations:**
- **Claude (Sonnet/Opus):** Best for complex error handling logic and edge cases
- **GPT-5:** Good balance of speed and comprehensive coverage
- **GPT-5-reasoning:** Best for analyzing potential failure scenarios

## Expected Results

After completing these steps, you should have:

1. **Custom error types** that cover all API failure scenarios with user-friendly messages
2. **Enhanced network methods** with comprehensive error handling and validation
3. **Retry logic** that handles transient failures intelligently
4. **Debug logging** for troubleshooting network issues
5. **Production-ready code** that gracefully handles edge cases

When you make an API call, your app will:
- Properly catch and categorize all error types
- Provide meaningful error messages to users
- Automatically retry transient failures
- Log detailed information for debugging
- Maintain type safety with Swift 6 patterns

## Troubleshooting

### AI Generates Generic Error Handling

**Problem:** The AI produces basic do-try-catch without custom error types.
**Solution:** Be more specific in your prompt. Explicitly request "custom Error enum with associated values" and provide examples of error cases you need (timeout, 404, 500, etc.). Set Code Generation Level to "Comprehensive" in Intelligence settings.

### Generated Code Doesn't Compile with Swift 6

**Problem:** AI uses deprecated patterns or Swift 5 syntax.
**Solution:** Add "using Swift 6 strict concurrency" to your prompt. Explicitly mention async/await and modern error handling. If using Claude, it's generally more current with Swift 6 patterns. Update your prompt to include: "Ensure code compiles with Swift 6 strict concurrency enabled."

### Error Messages Aren't User-Friendly

**Problem:** Technical error messages appear that confuse users.
**Solution:** Request LocalizedError conformance explicitly. Ask AI to "provide user-friendly error messages and recovery suggestions for each error case." Review and customize the generated descriptions for your app's tone.

### Retry Logic Retries Non-Retryable Errors

**Problem:** The retry mechanism attempts to retry client errors (4xx) or decoding errors.
**Solution:** Review the retry logic's shouldRetry switch statement. Ensure only transient errors (network failures, timeouts, 5xx errors) return true. Ask AI to "only retry transient network errors, not client errors or decoding failures."

### AI Doesn't Consider App-Specific Requirements

**Problem:** Generated code is generic and doesn't match your API's specific error responses.
**Solution:** Provide context in your prompt about your API's behavior. Example: "Our API returns error details in a JSON field called 'message' in the response body. Parse this for 4xx/5xx responses." Include sample API response structures.

## Additional Tips

- **Iterative refinement:** Don't expect perfect code on the first AI generation. Use follow-up prompts to refine specific aspects: "Add timeout handling to this method" or "Include response validation for empty data."

- **Combine AI strengths:** Use ChatGPT for initial structure generation, then switch to Claude for refactoring complex error handling logic. Claude's larger context window helps when analyzing multiple error scenarios.

- **Code review AI output:** Always review AI-generated error handling for security issues, particularly around error messages that might leak sensitive information (API keys, internal paths, user data).

- **Test edge cases:** Ask AI to generate unit tests for error scenarios: "Create XCTest cases that verify NetworkError.unauthorized is thrown for 401 responses."

- **Type-safe errors:** For APIs with documented error responses, ask AI to generate Codable error models: "Create a Codable struct for API error responses with code, message, and details fields."

- **Swift 6 typed throws:** If you're on Swift 6, explore typed throws for even more type safety: `func fetch() async throws(NetworkError) -> Data`. Ask AI to use this pattern for stricter error handling.

- **Monitor token usage:** Comprehensive error handling prompts can be lengthy. If using paid API tiers, consider breaking complex prompts into smaller, focused requests.

## Related Articles

- [KB-058: How to ask AI to add comprehensive error handling to functions](/kb-article-058-ask-ai-add-comprehensive-error-handling-functions.md) - General error handling
- [KB-057: How to generate Codable models from JSON using AI](/kb-article-057-generate-codable-models-json-using-ai.md) - API response modeling
- [KB-051: How to use AI to generate unit tests for existing code](/kb-article-051-use-ai-generate-unit-tests-existing-code.md) - Testing error scenarios
- [KB-047: How to use Claude for complex code refactoring tasks](/kb-article-047-use-claude-complex-refactoring.md) - Refactoring with AI
- [KB-055: How to have AI explain error messages and suggest fixes](/kb-article-055-have-ai-explain-error-messages-suggest-fixes.md) - Debugging with AI

## Sources

- Writing code with intelligence in Xcode - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- Code with Intelligence — xCode 26 - https://medium.com/@sachinsiwal/code-with-intelligence-xcode-26-9203a8ce665e (Accessed: November 17, 2025)
- Type-safe and user-friendly error handling in Swift 6 - https://theswiftdev.com/2025/type-safe-and-user-friendly-error-handling-in-swift-6/ (Accessed: November 17, 2025)
- API calls using Swift async/await and error handling - https://medium.com/@gianlucaannina_34907/api-calls-using-swift-async-await-and-error-handling-c8efcb000e63 (Accessed: November 17, 2025)
- Building a generic, thread-safe Networking Layer in Swift 6 with Interceptors - https://medium.com/@egzonpllana/building-a-generic-thread-safe-networking-layer-in-swift-6-927fa1d0cce8 (Accessed: November 17, 2025)
- Reverse-Engineering Xcode's Coding Intelligence prompt - https://peterfriese.dev/blog/2025/reveng-xcode-coding-intelligence/ (Accessed: November 17, 2025)
- URLSession | Apple Developer Documentation - https://developer.apple.com/documentation/foundation/urlsession (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
