# How to ask AI to create mock data services for testing

**Article ID:** KB-085
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 30-35 minutes

## Overview

This guide demonstrates how to effectively use Xcode 26.1+'s AI coding assistant to generate mock data services for testing purposes. You'll learn to craft specific prompts that produce protocol-based mock implementations compatible with Swift Testing framework, enabling isolated, reliable unit tests without external dependencies.

## Prerequisites

- Xcode 26.1 or later installed with Coding Intelligence enabled
- Basic understanding of Swift protocols and dependency injection
- Familiarity with Swift Testing framework (@Test and @Suite macros)
- An AI assistant account configured (ChatGPT, Claude, or compatible API)
- Active internet connection for cloud-based AI models (or local model for offline work)

## Steps

### Step 1: Enable and Configure Coding Intelligence

First, ensure Xcode's AI assistant is properly configured:

1. Open Xcode and navigate to **Xcode → Settings → Intelligence**
2. Enable the **Coding Intelligence** option
3. Select your preferred AI provider (ChatGPT, Claude, or custom API endpoint)
4. Set code generation level to **Balanced** or **Comprehensive** for test generation
5. Verify connection by checking the status indicator in the toolbar

**Note**: For sensitive projects, consider using a local model on Apple silicon Macs to keep code private.

### Step 2: Design Your Service Protocol

Before asking AI to create mocks, define a clear protocol that represents your service interface. Open your source file and create a protocol:

```swift
import Foundation

protocol WeatherServiceProtocol {
    func fetchCurrentWeather(for city: String) async throws -> Weather
    func fetchForecast(for city: String, days: Int) async throws -> [Weather]
}

struct Weather: Codable, Equatable {
    let temperature: Double
    let conditions: String
    let humidity: Int
    let city: String
}
```

**Why this matters**: Protocols enable dependency injection and make your code testable. The AI assistant works best when it understands the interface contract.

### Step 3: Craft an Effective Prompt for Mock Generation

Select your protocol code and invoke the AI assistant (Editor → Coding Intelligence → Ask About Selection, or right-click → Ask AI). Use this prompt structure:

```
Create a mock implementation of this WeatherServiceProtocol for use with Swift Testing framework. The mock should:

1. Track all method calls with their parameters
2. Allow configuring return values or thrown errors
3. Use actor isolation for thread-safe test execution
4. Follow Swift 6 concurrency best practices
5. Include clear documentation for test usage

The mock should be named MockWeatherService and be suitable for dependency injection in tests.
```

**Key prompt elements**:
- **Specific name**: Prevents generic naming
- **Feature list**: Defines exactly what the mock should do
- **Framework reference**: Ensures compatibility with Swift Testing
- **Concurrency requirements**: Specifies thread-safety needs
- **Usage context**: Clarifies this is for testing, not production

### Step 4: Review and Refine the Generated Mock

The AI assistant will generate a mock service. Here's an example of what you should expect:

```swift
import Foundation
import Testing

actor MockWeatherService: WeatherServiceProtocol {
    // Configuration for return values
    var weatherToReturn: Weather?
    var forecastToReturn: [Weather] = []
    var errorToThrow: Error?

    // Call tracking
    private(set) var fetchCurrentWeatherCallCount = 0
    private(set) var fetchCurrentWeatherCalledWith: [String] = []

    private(set) var fetchForecastCallCount = 0
    private(set) var fetchForecastCalledWith: [(city: String, days: Int)] = []

    func reset() {
        weatherToReturn = nil
        forecastToReturn = []
        errorToThrow = nil
        fetchCurrentWeatherCallCount = 0
        fetchCurrentWeatherCalledWith = []
        fetchForecastCallCount = 0
        fetchForecastCalledWith = []
    }

    func fetchCurrentWeather(for city: String) async throws -> Weather {
        fetchCurrentWeatherCallCount += 1
        fetchCurrentWeatherCalledWith.append(city)

        if let error = errorToThrow {
            throw error
        }

        guard let weather = weatherToReturn else {
            fatalError("MockWeatherService: weatherToReturn not configured")
        }

        return weather
    }

    func fetchForecast(for city: String, days: Int) async throws -> [Weather] {
        fetchForecastCallCount += 1
        fetchForecastCalledWith.append((city, days))

        if let error = errorToThrow {
            throw error
        }

        return forecastToReturn
    }
}
```

**Verify the mock includes**:
- ✓ Actor isolation for thread safety
- ✓ Configurable return values
- ✓ Error simulation capability
- ✓ Call tracking for verification
- ✓ Reset method for test isolation

If elements are missing, refine your prompt: "Add a reset() method to clear all state between tests" or "Make this an actor for thread-safe access."

### Step 5: Generate Test Suite Setup

Now ask the AI to create a test suite that uses your mock. In your test file, use this prompt:

```
Create a Swift Testing test suite for a WeatherViewModel that depends on WeatherServiceProtocol. The suite should:

1. Use the MockWeatherService for dependency injection
2. Include setup that creates a fresh mock for each test
3. Use @Test macro with descriptive test names
4. Test both success and error scenarios
5. Verify the view model correctly calls the service
6. Use #expect for assertions with clear failure messages

Include at least 3 test cases covering different scenarios.
```

The AI will generate a test suite structure:

```swift
import Testing
@testable import YourApp

@Suite("Weather ViewModel Tests")
struct WeatherViewModelTests {

    @Test("Fetches current weather successfully")
    func fetchCurrentWeatherSuccess() async throws {
        // Given
        let mockService = MockWeatherService()
        let expectedWeather = Weather(
            temperature: 72.5,
            conditions: "Sunny",
            humidity: 45,
            city: "San Francisco"
        )
        await mockService.setWeatherToReturn(expectedWeather)

        let viewModel = WeatherViewModel(service: mockService)

        // When
        await viewModel.fetchWeather(for: "San Francisco")

        // Then
        let callCount = await mockService.fetchCurrentWeatherCallCount
        #expect(callCount == 1, "Service should be called exactly once")

        let calledWith = await mockService.fetchCurrentWeatherCalledWith
        #expect(calledWith == ["San Francisco"], "Should request correct city")

        #expect(viewModel.currentWeather == expectedWeather,
                "ViewModel should store fetched weather")
    }

    @Test("Handles service errors gracefully")
    func fetchCurrentWeatherError() async throws {
        // Given
        let mockService = MockWeatherService()
        struct NetworkError: Error {}
        await mockService.setErrorToThrow(NetworkError())

        let viewModel = WeatherViewModel(service: mockService)

        // When
        await viewModel.fetchWeather(for: "San Francisco")

        // Then
        #expect(viewModel.errorMessage != nil,
                "ViewModel should handle and display error")
        #expect(viewModel.currentWeather == nil,
                "Weather should remain nil on error")
    }

    @Test("Fetches multi-day forecast", arguments: [3, 5, 7])
    func fetchForecastForDays(days: Int) async throws {
        // Given
        let mockService = MockWeatherService()
        let forecast = (0..<days).map { day in
            Weather(
                temperature: 70.0 + Double(day),
                conditions: "Sunny",
                humidity: 50,
                city: "Seattle"
            )
        }
        await mockService.setForecastToReturn(forecast)

        let viewModel = WeatherViewModel(service: mockService)

        // When
        await viewModel.fetchForecast(for: "Seattle", days: days)

        // Then
        let calledWith = await mockService.fetchForecastCalledWith
        #expect(calledWith.count == 1)
        #expect(calledWith[0].city == "Seattle")
        #expect(calledWith[0].days == days)
        #expect(viewModel.forecast.count == days,
                "Should store correct number of forecast days")
    }
}
```

### Step 6: Request Mock Factory Methods (Optional Enhancement)

For complex scenarios, ask the AI to create factory methods for common test data:

```
Add factory methods to MockWeatherService that create pre-configured instances for common test scenarios:
- sunny weather
- stormy weather
- error conditions
- empty forecast

Make these static methods for easy reuse across tests.
```

The AI will add convenience methods:

```swift
extension MockWeatherService {
    static func withSunnyWeather(for city: String = "Test City") -> MockWeatherService {
        let mock = MockWeatherService()
        Task {
            await mock.setWeatherToReturn(Weather(
                temperature: 75.0,
                conditions: "Sunny",
                humidity: 40,
                city: city
            ))
        }
        return mock
    }

    static func withNetworkError() -> MockWeatherService {
        let mock = MockWeatherService()
        Task {
            struct NetworkError: Error {}
            await mock.setErrorToThrow(NetworkError())
        }
        return mock
    }

    static func withForecast(days: Int, for city: String = "Test City") -> MockWeatherService {
        let mock = MockWeatherService()
        let forecast = (0..<days).map { day in
            Weather(
                temperature: 70.0 + Double(day),
                conditions: "Partly Cloudy",
                humidity: 50 + day,
                city: city
            )
        }
        Task {
            await mock.setForecastToReturn(forecast)
        }
        return mock
    }
}
```

### Step 7: Validate Generated Code

Run your tests to ensure the AI-generated mock works correctly:

1. Build your test target: **⌘U** (Product → Test)
2. Review test results in the Test Navigator
3. Check for any compilation errors or warnings
4. Verify all tests pass with expected behavior

If issues arise, use targeted prompts to fix specific problems:
- "The actor methods need await keywords - please add them"
- "Convert this to use Swift 6 strict concurrency checking"
- "Add documentation comments explaining each property"

### Step 8: Organize and Document

Ask the AI to add comprehensive documentation:

```
Add documentation comments to MockWeatherService explaining:
- Its purpose and usage in tests
- How to configure return values
- How to verify method calls
- Thread-safety guarantees as an actor
- Example usage in a test

Use proper Swift documentation format with /// comments.
```

Result:

```swift
/// A mock implementation of `WeatherServiceProtocol` for use in unit tests.
///
/// `MockWeatherService` is an actor that provides thread-safe mock behavior
/// for weather service operations. It tracks method calls and allows
/// configuring return values or errors for controlled testing.
///
/// ## Configuring Return Values
///
/// ```swift
/// let mock = MockWeatherService()
/// await mock.setWeatherToReturn(Weather(...))
/// ```
///
/// ## Verifying Method Calls
///
/// ```swift
/// let callCount = await mock.fetchCurrentWeatherCallCount
/// #expect(callCount == 1)
/// ```
///
/// ## Thread Safety
///
/// As an actor, all property access and method calls are automatically
/// serialized, making it safe to use in concurrent test scenarios.
///
/// ## Example Usage
///
/// ```swift
/// @Test func testWeatherFetch() async throws {
///     let mock = MockWeatherService()
///     await mock.setWeatherToReturn(Weather(...))
///     let viewModel = WeatherViewModel(service: mock)
///     await viewModel.fetchWeather(for: "Seattle")
///     #expect(await mock.fetchCurrentWeatherCallCount == 1)
/// }
/// ```
actor MockWeatherService: WeatherServiceProtocol {
    // ... implementation ...
}
```

## Expected Results

After completing these steps, you should have:

1. **A robust mock service** with:
   - Full protocol conformance
   - Call tracking for verification
   - Configurable return values and errors
   - Thread-safe actor implementation
   - Clear documentation

2. **Working test suite** that:
   - Runs successfully in Xcode Test Navigator
   - Uses Swift Testing framework with @Test macros
   - Tests both success and error paths
   - Demonstrates dependency injection
   - Provides clear test coverage

3. **Maintainable test code** that:
   - Other developers can understand and extend
   - Follows Swift 6 concurrency best practices
   - Uses modern Swift Testing features
   - Isolates tests from external dependencies

## Troubleshooting

### AI generates code with deprecated XCTest syntax

**Problem:** The generated test uses `XCTestCase`, `func testExample()` naming, or `XCTAssert` instead of Swift Testing.
**Solution:** Be explicit in your prompt: "Use Swift Testing framework with @Test macro, NOT XCTest. Use #expect for assertions." If the AI persists, add: "This is for Xcode 26.1+ only."

### Mock doesn't track method calls correctly

**Problem:** Call counters stay at zero or parameters aren't captured.
**Solution:** Ask the AI: "Add call tracking that increments counters and stores parameters for every method call in the protocol." Review the generated code to ensure tracking occurs before the return statement.

### Concurrency errors with actor access

**Problem:** Compiler errors about "actor-isolated property cannot be accessed" or missing `await` keywords.
**Solution:** Ensure all mock property access in tests uses `await`. Ask the AI: "Add await keywords for all actor property access in the test suite." Also verify your test functions are marked `async`.

### Mock configuration is cumbersome

**Problem:** Setting up mocks requires too much boilerplate in each test.
**Solution:** Request factory methods: "Create static factory methods on the mock that return pre-configured instances for common test scenarios." This makes tests more readable and reduces duplication.

### Generated tests don't verify important behavior

**Problem:** Tests pass but don't actually verify the correct behavior.
**Solution:** Be specific about assertions: "Add #expect assertions that verify: 1) the service was called with correct parameters, 2) the result was stored in the view model, 3) UI state was updated appropriately." Review each test to ensure it checks what matters.

### AI-generated code doesn't follow project conventions

**Problem:** Naming, formatting, or organization differs from your codebase style.
**Solution:** Add style guidance to your prompt: "Follow the naming conventions in this project where classes use PascalCase and methods use camelCase. Place mocks in a /Mocks subdirectory." Consider creating a project-specific prompt template.

## Additional Tips

- **Start with simple protocols**: Begin by asking AI to mock simple protocols with 1-2 methods. Once you verify the quality, scale up to complex protocols.

- **Provide context in prompts**: Include relevant code snippets in your selection when asking for mocks. The AI generates better results when it sees related types and patterns.

- **Iterate incrementally**: Don't expect perfect code in one prompt. Generate the basic mock first, then refine with follow-up requests: "Add reset method", "Make this an actor", "Add documentation".

- **Use parameterized tests**: Leverage Swift Testing's arguments feature for testing multiple scenarios without duplicating test code. Ask AI: "Convert this to a parameterized test with arguments for different input values."

- **Create a mocks directory**: Organize all generated mocks in a dedicated folder (e.g., `Tests/Mocks/`) for easy discovery and maintenance.

- **Review before committing**: Always review AI-generated code for correctness, security, and compliance with your team's standards. The AI is a productivity tool, not a replacement for code review.

- **Save effective prompts**: When you discover prompts that generate high-quality mocks, save them as code snippets in Xcode or document them in your project's testing guide.

- **Combine with existing patterns**: Ask the AI to match your project's existing mock patterns: "Generate a mock similar to the existing MockDatabaseService in this project."

- **Test the tests**: Temporarily break your production code to verify tests actually fail when they should. This confirms your mocks and assertions work correctly.

- **Use local models for sensitive code**: If working with proprietary or sensitive code, configure a local model on Apple silicon Macs to keep code on-device.

## Related Articles

- KB-086: How to write effective Swift Testing suites with @Suite organization
- KB-087: Protocol-oriented architecture for testable Swift applications
- KB-088: Dependency injection patterns in Swift 6
- KB-089: Advanced Swift Testing with custom traits and parameterization

## Sources

- Apple Developer Documentation - Writing code with intelligence in Xcode - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- Apple Developer Documentation - Swift Testing - https://developer.apple.com/documentation/testing (Accessed: November 17, 2025)
- Apple Developer - Swift Testing Framework - https://developer.apple.com/xcode/swift-testing (Accessed: November 17, 2025)
- 9to5Mac - Apple releases Xcode 26.1.1 with coding intelligence improvements - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- Swift by Sundell - Mocking in Swift - https://www.swiftbysundell.com/articles/mocking-in-swift/ (Accessed: November 17, 2025)
- Quality Coding - How to Use Protocols for Swift Mocking and Stubbing - https://qualitycoding.org/swift-mocking/ (Accessed: November 17, 2025)
- GitHub - swiftlang/swift-testing - https://github.com/swiftlang/swift-testing (Accessed: November 17, 2025)
- Antoine van der Lee - Swift Testing: Writing a Modern Unit Test - https://www.avanderlee.com/swift-testing/modern-unit-test/ (Accessed: November 17, 2025)
- Peter Friese - Reverse-Engineering Xcode's Coding Intelligence prompt - https://peterfriese.dev/blog/2025/reveng-xcode-coding-intelligence/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
