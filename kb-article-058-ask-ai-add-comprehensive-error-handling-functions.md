# How to ask AI to add comprehensive error handling to functions

**Article ID:** KB-058
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 20-40 minutes

## Overview

Xcode 26.1+ integrates powerful AI assistants (ChatGPT and Claude Sonnet 4) that can analyze your Swift functions and add comprehensive error handling with modern patterns. Instead of manually implementing do-catch blocks, typed throws, and async error propagation, you can prompt the AI to generate robust error handling that covers edge cases, network failures, validation errors, and data corruption scenarios. This guide teaches you how to effectively prompt AI to add production-ready error handling to your Swift 6 functions using typed throws, async/await patterns, and comprehensive error coverage.

## Prerequisites

- Xcode 26.1 or later installed (26.1.1 recommended for improved AI performance)
- macOS 15.0 or later (macOS 26 Tahoe recommended for full Coding Intelligence features)
- Swift 6.0+ project with concurrency support enabled
- ChatGPT or Claude configured in Xcode Coding Assistant (see KB-031 for ChatGPT, KB-043-046 for Claude)
- At least one function that needs error handling improvements
- Basic understanding of Swift error handling (do-catch, try, throw)
- Active internet connection for AI service access
- Recommended: Existing tests to verify error handling behavior

## Steps

### Step 1: Identify Functions Needing Error Handling

Before prompting the AI, assess which functions require comprehensive error handling:

1. **Open your project** in Xcode 26.1+
2. **Review your codebase** for functions with these warning signs:
   - Functions that silently ignore errors with `try?` or `try!`
   - Network calls without timeout or connection error handling
   - Data parsing without validation or corruption checks
   - File I/O operations without permission or disk space checks
   - User input processing without validation
   - Async functions that don't properly propagate errors
   - Functions marked with `throws` but only throw generic `Error` types

3. **Prioritize critical paths:**
   - Authentication and authorization flows
   - Payment and financial transactions
   - Data persistence and synchronization
   - Network requests for critical business logic
   - User-facing operations that could fail unexpectedly

**Example candidates for improvement:**

```swift
// ❌ Poor error handling - uses try? and silently fails
func fetchUserProfile(id: String) async -> User? {
    guard let url = URL(string: "https://api.example.com/users/\(id)") else { return nil }
    let data = try? await URLSession.shared.data(from: url).0
    return try? JSONDecoder().decode(User.self, from: data ?? Data())
}

// ❌ No error handling - crashes on failure
func saveToFile(data: Data, filename: String) {
    let url = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]
    try! data.write(to: url.appendingPathComponent(filename))
}

// ❌ Generic throws - caller doesn't know what errors to expect
func processPayment(amount: Decimal) throws -> Receipt {
    // Implementation that could fail in many ways
}
```

### Step 2: Open the Coding Assistant with Your Function

1. **Navigate to the file** containing the function you want to improve
2. **Select the entire function:**
   - Click at the start of the function signature
   - Drag to select through the closing brace `}`
   - For complex functions, select 50-150 lines of context including the function and related types

3. **Open the Coding Assistant:**
   - Press **⌃⌘ I** (Control-Command-I)
   - Or select **Editor > Coding Assistant** from the menu bar
   - The assistant panel appears on the right side

4. **Choose your AI model:**
   - **Claude Sonnet 4:** Better for complex error scenarios, comprehensive coverage, and detailed explanations (recommended for production code)
   - **ChatGPT (GPT-5):** Faster responses, good for straightforward error handling (recommended for rapid iteration)
   - See KB-050 for detailed model comparison

### Step 3: Write an Effective Error Handling Prompt

The quality of generated error handling depends heavily on prompt specificity. Use these proven patterns:

#### Pattern 1: Comprehensive Error Handling for Networking

For functions that make network requests:

```
Add comprehensive error handling to this async function. Use Swift 6 typed throws
to define a specific error enum covering:
- Network connectivity failures (no internet, timeout)
- HTTP status codes (4xx client errors, 5xx server errors)
- Invalid or malformed responses
- JSON decoding failures with detailed messages
- Authentication/authorization failures

Include proper async error propagation and informative error messages for debugging.
```

#### Pattern 2: Validation Error Handling

For functions processing user input or data validation:

```
Refactor this function to use typed throws with comprehensive validation error handling.
Create a ValidationError enum that covers:
- Empty or nil input values
- Format validation (email, phone, date formats)
- Range validation (min/max values, string length)
- Business rule violations
- Cross-field validation errors

Each error should include the field name and expected format for user-friendly messages.
```

#### Pattern 3: File I/O Error Handling

For functions performing file operations:

```
Add robust error handling for file operations using typed throws. Cover these scenarios:
- File not found or path invalid
- Permission denied (read/write access)
- Insufficient disk space
- File already exists (for creation operations)
- Corrupted or unreadable data
- Concurrent access conflicts

Use Swift 6 typed throws and include recovery suggestions in error descriptions.
```

#### Pattern 4: Data Parsing Error Handling

For functions decoding JSON, parsing data formats, or transforming data:

```
Enhance this parsing function with comprehensive error handling using Swift 6 typed throws.
Define a ParseError enum covering:
- Missing required fields
- Type mismatches (expected string, got number)
- Invalid date/time formats
- Null or undefined values in required fields
- Nested object parsing failures
- Schema version mismatches

Include the JSON path to the failing field in each error for debugging.
```

#### Pattern 5: Converting try? to Proper Error Handling

For existing functions using `try?` or `try!`:

```
Replace all try? and try! with proper error handling. Convert this function to use
typed throws with a custom error enum. Ensure each failure point has a specific error
case with contextual information. Add do-catch blocks where errors should be caught
and handled locally versus propagated to the caller.
```

**Key Prompt Principles:**

- **Be specific about error categories:** List the types of failures the function should handle
- **Specify Swift 6 features:** Mention "typed throws" to get `throws(MyError)` syntax instead of generic `throws`
- **Request informative errors:** Ask for error messages that include context (field names, expected values, etc.)
- **Define recovery expectations:** Specify whether errors should be propagated, caught and logged, or retry automatically
- **Include testing requirements:** Ask for example error cases to test

### Step 4: Review the Generated Error Handling Code

After the AI generates the improved function, carefully review the changes:

1. **Examine the error type definition:**
   ```swift
   // ✅ Good: Specific error cases with associated values
   enum NetworkError: Error {
       case noConnection
       case timeout(duration: TimeInterval)
       case httpError(statusCode: Int, message: String)
       case invalidResponse(reason: String)
       case decodingFailed(error: DecodingError, data: Data)
   }

   // ❌ Avoid: Too generic or missing context
   enum NetworkError: Error {
       case failed
       case error
   }
   ```

2. **Verify typed throws usage (Swift 6):**
   ```swift
   // ✅ Good: Typed throws with specific error type
   func fetchUser(id: String) async throws(NetworkError) -> User {
       // Implementation
   }

   // ❌ Old style: Generic throws
   func fetchUser(id: String) async throws -> User {
       // Implementation
   }
   ```

3. **Check error coverage completeness:**
   - Does every `try` statement have a corresponding catch or throw?
   - Are all edge cases covered (nil values, empty arrays, invalid input)?
   - Does the function handle both immediate errors and async propagated errors?
   - Are resource cleanup operations (file handles, network connections) in `defer` blocks?

4. **Validate error messages:**
   - Are messages informative enough for debugging?
   - Do messages avoid exposing sensitive information (passwords, tokens)?
   - Can error messages be displayed to users or are they developer-only?

5. **Review async error handling:**
   ```swift
   // ✅ Good: Proper async error propagation with typed throws
   func fetchAndProcess() async throws(DataError) -> ProcessedData {
       do throws(NetworkError) {
           let rawData = try await networkService.fetch()
           return try process(rawData)
       } catch {
           throw DataError.networkFailure(underlyingError: error)
       }
   }

   // ❌ Problematic: Swallows errors or loses type information
   func fetchAndProcess() async -> ProcessedData? {
       guard let data = try? await networkService.fetch() else { return nil }
       return try? process(data)
   }
   ```

### Step 5: Request Refinements and Improvements

If the initial error handling needs adjustment, use follow-up prompts:

#### Add Missing Error Cases

```
The error handling looks good, but also add cases for:
- Rate limiting (HTTP 429 Too Many Requests)
- Maintenance mode (HTTP 503 Service Unavailable)
- Client timeout (request took longer than 30 seconds)

Update the error enum and catch blocks accordingly.
```

#### Improve Error Messages

```
Enhance the error messages to be more user-friendly. For validation errors,
include the field name, the invalid value (sanitized), and the expected format.
For example: "Email address 'user@' is invalid. Expected format: name@domain.com"
```

#### Add Error Recovery Logic

```
Add retry logic for transient network errors. Retry up to 3 times with exponential
backoff (1s, 2s, 4s) for connection timeouts and 5xx server errors. Don't retry for
4xx client errors or authentication failures.
```

#### Request Logging Integration

```
Add structured logging for all error paths using os_log. Include:
- Error type and message
- Function name and line number
- Relevant context (user ID, request parameters)
- Timestamp and correlation ID

Use appropriate log levels (error for failures, warning for retries).
```

#### Add Error Context

```
Wrap lower-level errors with higher-level context. For example, if JSON decoding fails,
wrap the DecodingError in a custom error that includes the API endpoint, response status
code, and a snippet of the response body for debugging.
```

### Step 6: Test the Error Handling

After implementing the AI-generated error handling, thoroughly test all error paths:

1. **Create unit tests for each error case:**

   ```swift
   func testNetworkTimeout() async throws {
       let service = MockNetworkService(behavior: .timeout)

       do throws(NetworkError) {
           _ = try await service.fetchUser(id: "123")
           XCTFail("Should have thrown timeout error")
       } catch NetworkError.timeout(let duration) {
           XCTAssertGreaterThan(duration, 0)
       } catch {
           XCTFail("Wrong error type: \(error)")
       }
   }
   ```

2. **Test error message quality:**

   ```swift
   func testValidationErrorMessage() throws {
       let input = "invalid-email"

       do {
           _ = try validateEmail(input)
           XCTFail("Should have thrown validation error")
       } catch let error as ValidationError {
           let message = error.localizedDescription
           XCTAssertTrue(message.contains("invalid-email"))
           XCTAssertTrue(message.contains("Expected format"))
       }
   }
   ```

3. **Verify async error propagation:**

   ```swift
   func testAsyncErrorPropagation() async throws {
       // Ensure errors thrown in async contexts propagate correctly
       await XCTAssertThrowsAsyncError(
           try await fetchAndProcessData()
       ) { error in
           XCTAssertTrue(error is DataError)
       }
   }
   ```

4. **Test edge cases:**
   - Empty input (empty strings, empty arrays, nil values)
   - Boundary values (maximum string length, minimum numbers)
   - Concurrent access (multiple threads accessing shared resources)
   - Resource exhaustion (out of memory, disk full)

5. **Manual testing:**
   - Disconnect network and test connectivity error handling
   - Provide malformed JSON and verify parsing error messages
   - Test with invalid authentication tokens
   - Simulate server errors (500, 503) using network mocking tools

### Step 7: Ask AI to Generate Error Handling Documentation

Request documentation for the error handling:

```
Generate comprehensive documentation for this error handling implementation.
Include:
1. Doc comments on the error enum explaining when each case is thrown
2. Function-level documentation listing all possible errors
3. Usage examples showing how to handle each error case
4. Recovery strategies for transient vs permanent errors
5. A decision tree for which errors should be retried vs surfaced to users
```

Example generated documentation:

```swift
/// Fetches a user profile from the remote API.
///
/// - Parameter id: The unique identifier for the user
/// - Returns: The user's profile data
/// - Throws: `NetworkError` with the following cases:
///   - `.noConnection`: Network is unavailable, suggest retry after connection restored
///   - `.timeout`: Request exceeded 30s timeout, safe to retry
///   - `.httpError(4xx)`: Client error, check request parameters (do not retry)
///   - `.httpError(5xx)`: Server error, safe to retry after delay
///   - `.invalidResponse`: Server returned unexpected format, log for investigation
///   - `.decodingFailed`: JSON structure doesn't match expected schema
///
/// - Example:
/// ```swift
/// do {
///     let user = try await fetchUserProfile(id: "12345")
///     print("Loaded user: \(user.name)")
/// } catch NetworkError.noConnection {
///     // Show offline UI, retry when connection restored
/// } catch NetworkError.timeout {
///     // Retry with exponential backoff
/// } catch NetworkError.httpError(let code, let message) where code >= 500 {
///     // Server error - retry after delay
/// } catch {
///     // Permanent failure - log and show error to user
/// }
/// ```
func fetchUserProfile(id: String) async throws(NetworkError) -> User {
    // Implementation
}
```

### Step 8: Integrate Error Handling with Your App Architecture

Ask the AI to integrate error handling with your app's architecture:

#### For MVVM Architecture

```
Adapt this error handling to work with MVVM. The ViewModel should catch errors
from the service layer, convert them to user-friendly messages, and expose them
via @Published properties for the View to observe. Add a LoadingState enum with
cases: idle, loading, success(data), failure(error).
```

#### For Repository Pattern

```
Wrap the repository's error types with domain-specific errors. Map NetworkError
to DomainError cases that make sense for the business logic layer. For example,
NetworkError.httpError(404) becomes DomainError.userNotFound.
```

#### For Coordinator Pattern

```
Add error handling to the coordinator flow. When the service throws an error,
the coordinator should decide whether to show an alert, navigate to an error screen,
or retry the operation. Include navigation logic for recoverable vs fatal errors.
```

## Expected Results

After successfully adding comprehensive error handling with AI assistance, you should have:

1. ✅ **Typed Error Definitions (Swift 6):**
   - Custom error enums using `throws(SpecificError)` syntax
   - Specific error cases for each failure scenario
   - Associated values providing context (error codes, field names, underlying errors)
   - Conformance to `Error` and optionally `LocalizedError` for user-facing messages

2. ✅ **Complete Error Coverage:**
   - All `try` statements wrapped in `do-catch` blocks or propagated via `throws`
   - No use of `try?` or `try!` that silently hide errors
   - Edge cases handled (nil values, empty inputs, boundary conditions)
   - Resource cleanup in `defer` blocks for files, connections, locks

3. ✅ **Async/Await Error Handling:**
   - Proper error propagation in async functions using `async throws(ErrorType)`
   - Correct error handling in `Task` closures with explicit `throws(ErrorType)` in `do` blocks
   - No lost errors in fire-and-forget async tasks
   - Structured concurrency errors properly propagated to parent tasks

4. ✅ **Informative Error Messages:**
   - Contextual information (field names, expected formats, error codes)
   - Developer-friendly debugging details (file paths, response bodies, correlation IDs)
   - User-friendly descriptions via `LocalizedError` implementation
   - No exposure of sensitive data in error messages

5. ✅ **Comprehensive Tests:**
   - Unit tests for each error case with proper `throws(ErrorType)` checking
   - Tests verifying error message content and quality
   - Integration tests for error propagation through layers
   - Edge case coverage in test suite

6. ✅ **Documentation:**
   - Function-level doc comments listing all possible errors
   - Usage examples showing error handling patterns
   - Recovery strategies documented for each error type
   - Decision trees for retry vs surface to user

## Troubleshooting

### Issue 1: AI Generates Generic Errors Instead of Typed Throws

**Problem:** The AI generates `throws` instead of `throws(SpecificError)`, or creates vague error types.

**Solutions:**
- **Explicitly mention Swift 6:** Start your prompt with "Using Swift 6 typed throws syntax..."
- **Provide an example:** Include an example of typed throws in your prompt:
  ```
  Use typed throws like this example:
  func fetch() async throws(NetworkError) -> Data { ... }
  ```
- **Specify error categories:** List the exact error categories you want as enum cases
- **Follow up:** If the AI uses generic `throws`, reply: "Convert this to use Swift 6 typed throws with a specific error enum"
- **Use Claude for Swift 6:** Claude Sonnet 4 tends to generate more modern Swift 6 patterns than ChatGPT

### Issue 2: Error Handling Is Too Verbose or Complex

**Problem:** The AI generates overly complex error handling with too many layers of wrapping or excessive cases.

**Solutions:**
- **Request simplification:** "Simplify this error handling. Combine similar error cases and reduce nesting."
- **Specify desired complexity level:** "Use error handling appropriate for a production app, not over-engineered."
- **Focus on practical cases:** "Only handle errors that can actually occur in this function, not theoretical edge cases."
- **Set maximum enum cases:** "Limit the error enum to 5-7 most important cases."
- **Use ChatGPT for simplicity:** ChatGPT tends to generate more concise solutions than Claude

### Issue 3: AI Doesn't Handle Async Errors Correctly in Task Closures

**Problem:** Generated code loses error types when using `Task { }` or doesn't compile due to type inference issues.

**Solutions:**
- **Explicitly request Task error handling:** "When using Task closures, use explicit 'do throws(ErrorType)' blocks."
- **Provide the pattern:** Include this in your prompt:
  ```swift
  Task {
      do throws(MyError) {
          try await someFunction()
      } catch {
          // Handle error
      }
  }
  ```
- **Ask for structured concurrency:** "Use async/await with proper error propagation instead of unstructured Task closures."
- **Reference Swift forums:** Share the Stack Overflow link about Swift 6 typed throws in tasks with the AI for context

### Issue 4: Error Messages Expose Sensitive Information

**Problem:** Generated error messages include passwords, API keys, tokens, or personal data.

**Solutions:**
- **Specify security requirements:** "Ensure error messages never include passwords, tokens, credit card numbers, or PII."
- **Request sanitization:** "Add a function to sanitize error messages by redacting sensitive fields before logging or displaying."
- **Separate developer vs user errors:** "Create two message types: detailed developer messages for logging, sanitized user messages for UI."
- **Review and test:** Always manually review generated error messages and test with production-like data

### Issue 5: Tests Don't Cover All Error Cases

**Problem:** AI-generated tests only cover happy path or miss important error scenarios.

**Solutions:**
- **Request comprehensive test coverage:** "Generate unit tests for ALL error cases in the enum, including edge cases."
- **Specify test framework patterns:** "Use XCTest with async/await test methods and XCTAssertThrowsAsyncError."
- **Ask for test matrix:** "Create a test for each error case: [list all enum cases]. Each test should verify the error is thrown in the expected scenario."
- **Request mocking infrastructure:** "Create mock objects that can simulate each error condition for testing."

## Additional Tips

### When to Use ChatGPT vs Claude for Error Handling

**Prefer ChatGPT (GPT-5) for:**
- **Quick error handling additions** to simple functions (validation, parsing)
- **Rapid iteration** when prototyping error handling strategies
- **Straightforward scenarios** (basic networking, file I/O, common patterns)
- **Concise implementations** without over-engineering

**ChatGPT Strengths:**
- Faster response times (15-20 seconds vs 30-60 seconds for Claude)
- More concise error handling code (fewer lines, simpler structure)
- Better for learning and exploring error handling patterns quickly

**Prefer Claude Sonnet 4 for:**
- **Complex error scenarios** requiring multiple layers of error transformation
- **Large codebases** where errors need to propagate through many layers (repository, service, view model)
- **Production-ready implementations** with comprehensive coverage and documentation
- **Swift 6 advanced features** (typed throws, actor isolation errors, concurrency)

**Claude Strengths:**
- More thorough error coverage (identifies edge cases you might miss)
- Better integration with existing architecture patterns
- More detailed documentation and usage examples
- Superior understanding of Swift 6 concurrency and typed throws

See KB-050 for a complete comparison of ChatGPT vs Claude.

### Common Error Handling Patterns

#### Pattern 1: Network Request Error Handling

```swift
enum NetworkError: Error {
    case noConnection
    case timeout(TimeInterval)
    case httpError(statusCode: Int, message: String)
    case invalidResponse(reason: String)
    case decodingFailed(error: DecodingError)
}

func fetchData<T: Decodable>(from url: URL) async throws(NetworkError) -> T {
    let (data, response) = try await URLSession.shared.data(from: url)

    guard let httpResponse = response as? HTTPURLResponse else {
        throw NetworkError.invalidResponse(reason: "Not an HTTP response")
    }

    guard (200...299).contains(httpResponse.statusCode) else {
        throw NetworkError.httpError(
            statusCode: httpResponse.statusCode,
            message: HTTPURLResponse.localizedString(forStatusCode: httpResponse.statusCode)
        )
    }

    do {
        return try JSONDecoder().decode(T.self, from: data)
    } catch let decodingError as DecodingError {
        throw NetworkError.decodingFailed(error: decodingError)
    }
}
```

#### Pattern 2: Validation Error Handling

```swift
enum ValidationError: Error, LocalizedError {
    case emptyField(fieldName: String)
    case invalidFormat(fieldName: String, expected: String, actual: String)
    case outOfRange(fieldName: String, min: Double, max: Double, actual: Double)

    var errorDescription: String? {
        switch self {
        case .emptyField(let field):
            return "\(field) cannot be empty"
        case .invalidFormat(let field, let expected, let actual):
            return "\(field) has invalid format '\(actual)'. Expected: \(expected)"
        case .outOfRange(let field, let min, let max, let actual):
            return "\(field) value \(actual) is out of range [\(min)-\(max)]"
        }
    }
}

func validateUserInput(email: String, age: Int) throws(ValidationError) {
    guard !email.isEmpty else {
        throw ValidationError.emptyField(fieldName: "Email")
    }

    let emailRegex = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"
    guard email.range(of: emailRegex, options: .regularExpression) != nil else {
        throw ValidationError.invalidFormat(
            fieldName: "Email",
            expected: "name@domain.com",
            actual: email
        )
    }

    guard (13...120).contains(age) else {
        throw ValidationError.outOfRange(
            fieldName: "Age",
            min: 13,
            max: 120,
            actual: Double(age)
        )
    }
}
```

#### Pattern 3: Layered Error Transformation

```swift
// Low-level error (networking layer)
enum NetworkError: Error {
    case httpError(statusCode: Int)
    case decodingFailed(DecodingError)
}

// Mid-level error (repository layer)
enum RepositoryError: Error {
    case networkFailure(NetworkError)
    case notFound
    case unauthorized
}

// High-level error (domain layer)
enum UserServiceError: Error, LocalizedError {
    case userNotFound
    case sessionExpired
    case unknownError

    var errorDescription: String? {
        switch self {
        case .userNotFound:
            return "The requested user could not be found"
        case .sessionExpired:
            return "Your session has expired. Please log in again"
        case .unknownError:
            return "An unexpected error occurred. Please try again"
        }
    }
}

// Transform errors through layers
func fetchUser(id: String) async throws(UserServiceError) -> User {
    do throws(RepositoryError) {
        return try await userRepository.fetch(id: id)
    } catch RepositoryError.notFound {
        throw UserServiceError.userNotFound
    } catch RepositoryError.unauthorized {
        throw UserServiceError.sessionExpired
    } catch {
        throw UserServiceError.unknownError
    }
}
```

### Error Handling Checklist

Before considering error handling complete, verify:

- [ ] All `try` statements have corresponding error handling (no naked `try?` or `try!`)
- [ ] Error enum cases cover all realistic failure scenarios for the function
- [ ] Associated values provide enough context for debugging and recovery
- [ ] Error messages are informative but don't expose sensitive data
- [ ] Async functions use typed throws: `async throws(ErrorType)`
- [ ] Task closures use explicit `do throws(ErrorType)` blocks
- [ ] Resources (files, connections) are cleaned up in `defer` blocks
- [ ] Unit tests exist for each error case
- [ ] Documentation lists all possible errors and recovery strategies
- [ ] User-facing errors implement `LocalizedError` for display
- [ ] Logging is added for all error paths with appropriate context

### Prompting Strategies for Different Function Types

**For View Models:**
```
Add error handling that exposes errors to SwiftUI views. Use @Published properties
for error states. Convert service layer errors to user-friendly messages. Include
a LoadingState enum with idle, loading, success(data), and failure(error) cases.
```

**For Repository Functions:**
```
Add comprehensive error handling for data access. Handle database errors, network
errors, caching failures, and data consistency issues. Use typed throws to define
a RepositoryError enum. Include retry logic for transient failures.
```

**For Parsing/Decoding Functions:**
```
Add detailed error handling for JSON decoding. When decoding fails, capture the
JSON key path, expected type, and actual value. Create a DecodingError wrapper
that includes the raw JSON for debugging.
```

**For Authentication Functions:**
```
Add security-focused error handling. Cover invalid credentials, expired tokens,
rate limiting, account locked, and two-factor authentication required. Never
log passwords or tokens in error messages.
```

### Swift 6 Concurrency Error Handling

When working with actors and structured concurrency:

```
Add error handling for Swift 6 actor concurrency. Handle actor isolation violations,
task cancellation, and timeout scenarios. Ensure all Task groups properly propagate
errors. Use typed throws for actor methods.
```

Example:

```swift
actor DataProcessor {
    enum ProcessingError: Error {
        case cancelled
        case timeout
        case processingFailed(reason: String)
    }

    func processItems(_ items: [Item]) async throws(ProcessingError) -> [Result] {
        try Task.checkCancellation()

        return try await withThrowingTaskGroup(of: Result.self) { group in
            for item in items {
                group.addTask {
                    try await self.processItem(item)
                }
            }

            var results: [Result] = []
            for try await result in group {
                results.append(result)
            }
            return results
        }
    }
}
```

### Performance Considerations

Error handling can impact performance:

- **Avoid throwing in tight loops:** If a function is called thousands of times, throwing errors can be expensive
- **Use Result types for expected failures:** For operations that commonly fail (parsing user input), consider `Result<Success, Failure>` instead of `throws`
- **Cache error messages:** Don't construct complex error messages in hot paths; use static strings
- **Measure impact:** Use Instruments to profile error handling overhead

Ask AI to optimize:
```
Optimize this error handling for performance. This function is called 10,000 times
per second in a tight loop. Use Result type instead of throws for common validation
failures, and only throw for exceptional cases.
```

## Related Articles

- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT
- KB-032: How to start a conversation with ChatGPT in Xcode
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-047: How to use Claude for complex refactoring tasks
- KB-050: How to compare responses between ChatGPT and Claude for the same coding task

## Sources

1. **Type-safe and user-friendly error handling in Swift 6 - The.Swift.Dev.**
   https://theswiftdev.com/2025/type-safe-and-user-friendly-error-handling-in-swift-6/
   Accessed: November 17, 2025

2. **Swift Typed-Throws - Medium (Oudy)**
   https://medium.com/@oudy1st/swift-typed-throws-825d6c028930
   Accessed: November 17, 2025

3. **Typed throws in Swift explained with code examples - SwiftLee**
   https://www.avanderlee.com/swift/typed-throws/
   Accessed: November 17, 2025

4. **Stack Overflow - Swift 6 - How to use Typed Throws inside a Task**
   https://stackoverflow.com/questions/79019378/swift-6-how-to-use-typed-throws-inside-a-task
   Accessed: November 17, 2025

5. **Revolutionizing Error Handling with Typed Throws in Swift 6 - Medium (Syed Qamar Abbas)**
   https://medium.com/@syedqamar.a1/revolutionizing-error-handling-with-typed-throws-in-swift-6-6f31849c56d6
   Accessed: November 17, 2025

6. **Swift Error handling best practices - Stack Overflow**
   https://stackoverflow.com/questions/49628288/swift-error-handling-best-practices
   Accessed: November 17, 2025

7. **Error handling with async throws - Using Swift - Swift Forums**
   https://forums.swift.org/t/error-handling-with-async-throws/63769
   Accessed: November 17, 2025

8. **Swift 6.0: New Features, Compile-Time Warnings, and Fixes - Medium (Amit Gupta)**
   https://medium.com/@gupta.amit6700/swift-6-0-new-features-compile-time-warnings-and-fixes-e45f7775921c
   Accessed: November 17, 2025

9. **Essential updates in Xcode 26.1.1 with Swift 6.2.1 - DEV Community**
   https://dev.to/arshtechpro/essential-updates-in-xcode-2611-with-swift-621-2e3i
   Accessed: November 17, 2025

10. **Mastering Error Handling in Swift: Best Practices and Detailed Examples - Medium (Anas Poovalloor)**
    https://anasaman-p.medium.com/mastering-error-handling-in-swift-best-practices-and-detailed-examples-76ea86d4f25c
    Accessed: November 17, 2025

11. **Claude Skills - API Error Handling**
    https://claude-plugins.dev/skills/@aj-geddes/useful-ai-prompts/api-error-handling
    Accessed: November 17, 2025

12. **Developer's Guide to AI Coding Tools: Claude vs. ChatGPT - Descope**
    https://www.descope.com/blog/post/claude-vs-chatgpt
    Accessed: November 17, 2025

---
*This article is part of the Xcode v26.1+ knowledge base*
