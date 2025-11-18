# How to handle data races using Swift 6 compile-time safety with AI help

**Article ID:** KB-088
**Difficulty:** Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 45-60 minutes

## Overview

Swift 6 introduces groundbreaking compile-time data race safety that detects potential concurrency issues before your code runs, eliminating an entire class of difficult-to-debug crashes and data corruption bugs. This guide demonstrates how to leverage Swift 6's actor isolation, Sendable protocol, and strict concurrency checking—combined with Xcode 26.1+'s AI coding assistant—to systematically identify and resolve data races in your codebase.

## Prerequisites

- Xcode 26.1 or later installed with Swift 6 language mode enabled
- Understanding of async/await concurrency model (see KB-082)
- Familiarity with actors and modern Swift concurrency patterns
- Related Knowledge: KB-086 (Swift Testing suites), KB-087 (Protocol-oriented architecture)
- An AI assistant configured in Xcode (ChatGPT, Claude, or compatible API)
- Active project with concurrency-related code to migrate

## Steps

### Step 1: Enable Swift 6 Language Mode and Strict Concurrency Checking

Before diagnosing data races, enable Swift 6's compile-time safety checks:

1. Open your Xcode project
2. Select your target in the project navigator
3. Navigate to **Build Settings**
4. Search for **"Swift Language Version"**
5. Set to **Swift 6** (or latest available)
6. Search for **"Strict Concurrency Checking"**
7. Set to **Complete** to enable full data race detection

**Alternative: Gradual Migration Approach**

For existing projects, start with warnings instead of errors:

1. Keep Swift Language Version at **Swift 5**
2. Set **Strict Concurrency Checking** to **Complete**
3. Build your project: **⌘B**
4. Review warnings in the Issue Navigator

This allows you to assess the scope of data race issues before committing to Swift 6 mode.

**Command Line Option (for package projects)**:

```bash
swift build -Xswiftc -strict-concurrency=complete
```

### Step 2: Understand Common Data Race Patterns Swift 6 Detects

Swift 6's compiler identifies several critical data race scenarios. Understanding these helps you interpret compiler diagnostics:

**Pattern 1: Shared Mutable State Across Actors**

```swift
// ❌ Problematic code - data race detected
class UserSession {
    var currentUser: User?  // Mutable property on reference type
    var isAuthenticated = false
}

actor AuthenticationManager {
    let session = UserSession()  // ⚠️ Shared mutable state

    func login(user: User) {
        session.currentUser = user  // Data race: multiple actors can access
        session.isAuthenticated = true
    }
}

// Different actor could access session simultaneously
@MainActor
class ProfileViewController {
    func updateProfile(_ manager: AuthenticationManager) async {
        // Potential data race when accessing manager.session
    }
}
```

**Swift 6 Error:**
```
error: stored property 'session' of 'Sendable'-conforming actor-isolated
class 'AuthenticationManager' cannot be of non-'Sendable' type 'UserSession'
```

**Pattern 2: Non-Sendable Types Crossing Actor Boundaries**

```swift
// ❌ Non-Sendable type
class DataCache {
    var items: [String: Data] = [:]  // Mutable dictionary
}

actor NetworkService {
    func fetchData() async -> DataCache {
        let cache = DataCache()
        // Populate cache...
        return cache  // ⚠️ Returns non-Sendable type across actor boundary
    }
}

@MainActor
func displayData() async {
    let service = NetworkService()
    let cache = await service.fetchData()  // Data race risk
    // Multiple contexts could now mutate cache
}
```

**Swift 6 Error:**
```
error: sending 'cache' risks causing data races
note: task-isolated 'cache' is passed as a 'Sendable' argument to
      main actor-isolated function
```

**Pattern 3: Main Actor Isolation Violations**

```swift
// ❌ Accessing main-actor state from non-isolated context
@MainActor
class ViewModel: ObservableObject {
    @Published var title: String = ""
    @Published var isLoading = false
}

actor DataProcessor {
    func process(viewModel: ViewModel) {
        viewModel.title = "Processing..."  // ⚠️ Main actor violation
        // Accessing @MainActor property from actor context
    }
}
```

**Swift 6 Error:**
```
error: main actor-isolated property 'title' can not be mutated from
       a non-isolated context
```

### Step 3: Use AI to Identify Data Race Risks in Existing Code

Leverage Xcode's AI assistant to audit your codebase for concurrency issues:

1. Select a class or actor that handles shared state
2. Right-click → **Ask Coding Intelligence** (or **⌃⌘?**)
3. Use this specific prompt:

```
Analyze this code for potential data races when Swift 6 strict concurrency
checking is enabled. Identify:

1. Properties that should be marked Sendable but aren't
2. Mutable state that could be accessed from multiple actors
3. Non-isolated functions accessing actor-isolated state
4. Types crossing actor boundaries unsafely
5. Missing @MainActor annotations on UI-related code

For each issue, explain why it's a data race risk and suggest a fix
compatible with Swift 6 compile-time safety.
```

**Example AI Response Analysis:**

For the `UserSession` example above, the AI might respond:

```
Data Race Risks Identified:

1. UserSession is a class with mutable properties (currentUser, isAuthenticated)
   Risk: Can be shared across actors and mutated concurrently
   Fix: Convert to struct (value type) or make it an actor

2. session property in AuthenticationManager
   Risk: Non-Sendable type stored in actor
   Fix: Make UserSession conform to Sendable

3. Direct property mutation from multiple contexts
   Risk: Race condition when reading/writing authentication state
   Fix: Encapsulate mutations within actor methods

Recommended approach:
- Convert UserSession to a struct (Sendable by default)
- Or make UserSession an actor with synchronized access
- Ensure all mutations go through actor-isolated methods
```

### Step 4: Fix Data Races Using Sendable Protocol

The `Sendable` protocol marks types safe for concurrent access. Apply it strategically:

**Solution 1: Convert Classes to Value Types (Structs)**

```swift
// ✅ Fixed: Value type is automatically Sendable
struct UserSession: Sendable {
    let currentUser: User?  // Immutable properties
    let isAuthenticated: Bool
}

// User must also be Sendable
struct User: Sendable {
    let id: UUID
    let username: String
    let email: String
}

actor AuthenticationManager {
    private(set) var session: UserSession?  // Now safe

    func login(user: User) {
        session = UserSession(currentUser: user, isAuthenticated: true)
    }

    func logout() {
        session = nil
    }
}
```

**Why this works:** Value types are copied when passed between actors, preventing shared mutable state.

**Solution 2: Use @unchecked Sendable for Thread-Safe Classes**

For classes with internal synchronization:

```swift
// ✅ Thread-safe class with explicit Sendable conformance
final class UserSession: @unchecked Sendable {
    private let lock = NSLock()
    private var _currentUser: User?
    private var _isAuthenticated = false

    var currentUser: User? {
        get {
            lock.lock()
            defer { lock.unlock() }
            return _currentUser
        }
    }

    var isAuthenticated: Bool {
        get {
            lock.lock()
            defer { lock.unlock() }
            return _isAuthenticated
        }
    }

    func update(user: User?, authenticated: Bool) {
        lock.lock()
        defer { lock.unlock() }
        _currentUser = user
        _isAuthenticated = authenticated
    }
}
```

**⚠️ Warning:** `@unchecked Sendable` tells the compiler "trust me, I've made this thread-safe." You're responsible for ensuring actual safety with locks, serial queues, or other synchronization.

**Ask AI to Convert:**

Select your class and prompt:
```
Convert this class to be Sendable-compliant for Swift 6. If possible, make it
a struct with immutable properties. If it must remain a class, add proper
synchronization using NSLock and conform to @unchecked Sendable with thread-safe
property access. Explain which approach you chose and why.
```

### Step 5: Implement Actor Isolation for Shared Mutable State

When state must be mutable, encapsulate it in actors:

**Before: Data Race Risk**

```swift
// ❌ Shared mutable state
class DataCache {
    var items: [String: Data] = [:]
    var lastUpdate: Date?

    func store(_ data: Data, forKey key: String) {
        items[key] = data
        lastUpdate = Date()
    }

    func retrieve(key: String) -> Data? {
        return items[key]
    }
}

// Multiple tasks can call these methods concurrently = data race
```

**After: Actor-Protected State**

```swift
// ✅ Actor ensures serialized access
actor DataCache {
    private var items: [String: Data] = [:]
    private var lastUpdate: Date?

    func store(_ data: Data, forKey key: String) {
        items[key] = data
        lastUpdate = Date()
    }

    func retrieve(key: String) -> Data? {
        return items[key]
    }

    func clear() {
        items.removeAll()
        lastUpdate = nil
    }
}

// Usage requires await (suspends until actor can execute)
let cache = DataCache()
await cache.store(myData, forKey: "user-profile")
let data = await cache.retrieve(key: "user-profile")
```

**How actors prevent data races:**
- Only one task executes code inside the actor at a time
- All property access is serialized automatically
- Mutable state never escapes the actor boundary

**AI Prompt for Actor Conversion:**

```
Convert this class to an actor to prevent data races. Ensure:
1. All mutable properties are private or internal
2. Public methods provide controlled access
3. Return types are Sendable (value types or other actors)
4. Add a clear() or reset() method for cleanup
5. Document thread-safety guarantees

Explain what data race risks this actor eliminates.
```

### Step 6: Apply @MainActor to UI Code

UIKit and SwiftUI components must run on the main thread. Use `@MainActor` to enforce this at compile time:

**Problem: Main Thread Violations**

```swift
// ❌ Compiler can't verify main thread safety
class ProfileViewModel: ObservableObject {
    @Published var username: String = ""
    @Published var isLoading = false

    func loadProfile() async {
        isLoading = true  // ⚠️ May not be on main thread
        // Load data...
        username = fetchedName  // ⚠️ @Published mutation off main thread
    }
}
```

**Solution: @MainActor Isolation**

```swift
// ✅ Compiler enforces main thread execution
@MainActor
class ProfileViewModel: ObservableObject {
    @Published var username: String = ""
    @Published var isLoading = false

    func loadProfile() async {
        isLoading = true  // ✓ Guaranteed on main thread

        // Explicitly switch to background for work
        let fetchedName = await Task.detached {
            // Network call on background thread
            return try await NetworkService.shared.fetchUsername()
        }.value

        username = fetchedName  // ✓ Back on main thread automatically
    }
}
```

**Global Actor Isolation for SwiftUI:**

```swift
// ✅ SwiftUI views are implicitly @MainActor in Swift 6
struct ProfileView: View {
    @StateObject var viewModel = ProfileViewModel()

    var body: some View {
        // All UI updates guaranteed on main thread
        VStack {
            Text(viewModel.username)
            if viewModel.isLoading {
                ProgressView()
            }
        }
        .task {
            await viewModel.loadProfile()
        }
    }
}
```

**Swift 6.2 Default Main Actor Isolation:**

Swift 6.2 introduced automatic `@MainActor` inference for SwiftUI view code. You can enable this for entire modules:

Add to your Swift file or module:
```swift
@MainActor import SwiftUI
```

**AI Prompt for MainActor Application:**

```
Review this ViewModel/ViewController and apply @MainActor where needed for
Swift 6 safety. Ensure:
1. The class is marked @MainActor if it updates UI
2. Background work uses Task.detached or Task { }
3. @Published properties are only mutated on main actor
4. Async methods properly await when calling non-isolated code
5. Explain why each @MainActor annotation is necessary

Also identify any properties that should be nonisolated.
```

### Step 7: Fix "Sendable Closure" Warnings

A common Swift 6 error involves closures capturing non-Sendable state:

**Problem:**

```swift
actor ImageProcessor {
    func process(_ images: [UIImage]) async {
        var processedCount = 0  // ⚠️ Mutable capture

        await withTaskGroup(of: Void.self) { group in
            for image in images {
                group.addTask {
                    // Process image...
                    processedCount += 1  // ❌ Data race: concurrent mutation
                }
            }
        }
    }
}
```

**Swift 6 Error:**
```
error: mutation of captured var 'processedCount' in concurrently-executing code
```

**Solution 1: Use Actor-Isolated State**

```swift
actor ImageProcessor {
    private var processedCount = 0  // Actor-protected state

    func process(_ images: [UIImage]) async {
        await withTaskGroup(of: Void.self) { group in
            for image in images {
                group.addTask {
                    // Process image...
                    await self.incrementCount()
                }
            }
        }
    }

    private func incrementCount() {
        processedCount += 1
    }
}
```

**Solution 2: Return Values Instead of Mutating**

```swift
actor ImageProcessor {
    func process(_ images: [UIImage]) async -> Int {
        await withTaskGroup(of: Int.self) { group in
            for image in images {
                group.addTask {
                    // Process image...
                    return 1  // Return count instead of mutating
                }
            }

            var total = 0
            for await count in group {
                total += count
            }
            return total
        }
    }
}
```

**AI Prompt for Closure Fixes:**

```
This code has a "Sendable closure captures mutable variable" error in Swift 6.
Fix it by either:
1. Moving the mutable state into actor-isolated methods
2. Redesigning to return values instead of capturing/mutating
3. Using atomic operations if appropriate

Choose the most idiomatic Swift 6 approach and explain the data race risk
that existed.
```

### Step 8: Transfer Sendable Values Across Actor Boundaries

When passing data between actors, ensure transferred types are `Sendable`:

**Non-Sendable Transfer (Error):**

```swift
class NetworkResponse {  // ❌ Not Sendable
    var data: Data
    var headers: [String: String]
}

actor APIClient {
    func fetch() async -> NetworkResponse {
        return NetworkResponse()  // ❌ Can't send across actor boundary
    }
}
```

**Sendable Value Transfer (Correct):**

```swift
struct NetworkResponse: Sendable {  // ✅ Sendable struct
    let data: Data
    let headers: [String: String]
    let statusCode: Int
}

actor APIClient {
    func fetch() async -> NetworkResponse {
        // Build response...
        return NetworkResponse(
            data: responseData,
            headers: responseHeaders,
            statusCode: 200
        )  // ✅ Safe to send - value type with Sendable properties
    }
}

@MainActor
class ViewModel {
    func loadData() async {
        let client = APIClient()
        let response = await client.fetch()  // ✅ Safe transfer
        // response is a copy, no shared mutable state
        processResponse(response)
    }
}
```

**Working with Non-Sendable Properties:**

If a type contains non-Sendable properties, you have options:

**Option 1: Make Properties Sendable**

```swift
// ❌ Before
struct UserProfile {
    let user: UserData  // UserData is a class, not Sendable
    let preferences: [String: Any]  // Any is not Sendable
}

// ✅ After
struct UserProfile: Sendable {
    let user: UserData  // Convert UserData to struct or actor
    let preferences: [String: String]  // Use concrete Sendable type
}
```

**Option 2: Isolate Non-Sendable Properties**

```swift
struct UserProfile: Sendable {
    let id: UUID
    let username: String
    // Don't include non-Sendable properties
}

actor UserManager {
    private var userMetadata: [UUID: NonSendableMetadata] = [:]

    func getProfile(id: UUID) -> UserProfile {
        return UserProfile(id: id, username: lookupUsername(id))
    }
}
```

**AI Analysis Prompt:**

```
This type needs to cross actor boundaries but contains non-Sendable properties.
Options:
1. Identify which properties are non-Sendable and why
2. Suggest converting non-Sendable types to Sendable equivalents
3. Redesign to separate Sendable data from non-Sendable implementation details
4. Show before/after code with Sendable conformance

Explain the safest approach for this specific case.
```

### Step 9: Handle Legacy Code with Incremental Migration

For large codebases, migrate module-by-module:

**Strategy 1: Per-Module Migration**

1. Enable Swift 6 for one module at a time
2. Keep dependencies in Swift 5 mode initially
3. Add `Sendable` conformance to public API types first
4. Fix internal data races second

**Strategy 2: Warnings First, Errors Later**

```swift
// In specific files, enable strict checking as warnings
// Add to top of Swift file:
// swift-tools-version: 5.9

import Foundation

// Code here gets warnings, not errors, for concurrency issues
```

**Track Migration Progress:**

Create a simple test to measure progress:

```swift
import Testing

@Suite("Swift 6 Concurrency Compliance")
struct ConcurrencyComplianceTests {
    @Test("All public types are Sendable where appropriate")
    func publicTypesSendable() {
        // This test failing = work to do
        #expect(MyPublicClass.self is Sendable.Type)
        #expect(MyPublicStruct.self is Sendable.Type)
    }
}
```

**AI-Assisted Migration Prompt:**

```
Create a migration plan for this module to Swift 6 data race safety:

1. List all classes that should become actors or structs
2. Identify types that need Sendable conformance
3. Find @MainActor candidates (view models, view controllers)
4. Detect shared mutable state that needs actor isolation
5. Prioritize changes by impact (start with public API)
6. Estimate effort for each change

Present as a checklist I can work through incrementally.
```

### Step 10: Verify Data Race Safety with Testing

After applying fixes, validate with tests:

**Concurrency Stress Test:**

```swift
import Testing

@Suite("Data Race Safety Tests")
struct ConcurrencySafetyTests {

    @Test("DataCache handles concurrent access safely")
    func dataCacheThreadSafety() async throws {
        let cache = DataCache()

        // Simulate 100 concurrent writes
        await withTaskGroup(of: Void.self) { group in
            for i in 0..<100 {
                group.addTask {
                    let data = "Test \(i)".data(using: .utf8)!
                    await cache.store(data, forKey: "key-\(i)")
                }
            }
        }

        // Verify all writes succeeded
        for i in 0..<100 {
            let retrieved = await cache.retrieve(key: "key-\(i)")
            #expect(retrieved != nil, "Key \(i) should exist")
        }
    }

    @Test("ViewModel updates UI on main thread")
    func viewModelMainThreadSafety() async {
        let viewModel = ProfileViewModel()

        await viewModel.loadProfile()

        // This would fail if not on main thread in Swift 6
        #expect(Thread.isMainThread, "ViewModel should operate on main thread")
    }

    @Test("Actor prevents concurrent modification")
    func actorSerializesAccess() async throws {
        actor Counter {
            private var value = 0

            func increment() {
                value += 1
            }

            func getValue() -> Int {
                return value
            }
        }

        let counter = Counter()

        // 1000 concurrent increments
        await withTaskGroup(of: Void.self) { group in
            for _ in 0..<1000 {
                group.addTask {
                    await counter.increment()
                }
            }
        }

        let final = await counter.getValue()
        #expect(final == 1000, "Actor should prevent race conditions")
    }
}
```

**Runtime Data Race Detection:**

Enable **Thread Sanitizer** during testing to catch runtime races:

1. Select your scheme: **Product → Scheme → Edit Scheme...**
2. Select **Test** in the sidebar
3. Go to **Diagnostics** tab
4. Enable **Thread Sanitizer**
5. Run tests: **⌘U**

Thread Sanitizer will detect actual data races that might slip through compile-time checks.

### Step 11: Document Concurrency Guarantees

Use AI to generate documentation explaining your concurrency model:

**AI Documentation Prompt:**

```
Add documentation comments to this actor/class explaining:
1. What data it protects from races
2. Thread-safety guarantees it provides
3. Whether callers need to use await
4. If it's @MainActor isolated and why
5. Example usage showing safe concurrent access

Use Swift documentation format with /// comments and code examples.
```

**Result:**

```swift
/// Manages user authentication state with thread-safe access.
///
/// `AuthenticationManager` is an actor that protects authentication state
/// from data races. All methods are async and automatically serialized,
/// ensuring only one operation modifies state at a time.
///
/// ## Thread Safety
///
/// All properties and methods are actor-isolated, preventing concurrent
/// access to authentication state. The manager is safe to use from any
/// concurrency context.
///
/// ## Usage
///
/// ```swift
/// let manager = AuthenticationManager()
///
/// // Login (serialized access)
/// await manager.login(user: currentUser)
///
/// // Check status (may suspend if another operation is in progress)
/// let isLoggedIn = await manager.isAuthenticated
/// ```
///
/// ## Data Race Prevention
///
/// This actor eliminates the risk of:
/// - Concurrent mutations to user session data
/// - Reading authentication state while it's being updated
/// - Race conditions between login/logout operations
///
actor AuthenticationManager {
    // Implementation...
}
```

## Expected Results

After completing these steps, you should have:

1. **Compile-Time Safety**:
   - Zero data race warnings/errors in Swift 6 mode
   - All types crossing actor boundaries are Sendable
   - Proper isolation with actors and @MainActor
   - Clean build with strict concurrency checking enabled

2. **Robust Concurrency Model**:
   - Shared mutable state protected by actors
   - Value types for transferred data
   - Main thread enforcement for UI code
   - No @unchecked Sendable unless truly necessary

3. **Maintainable Code**:
   - Clear documentation of thread-safety guarantees
   - Tests verifying concurrency safety
   - Consistent patterns throughout codebase
   - Incremental migration path for legacy code

4. **Eliminated Bugs**:
   - No data corruption from race conditions
   - No crashes from concurrent mutations
   - No "purple warnings" in Thread Sanitizer
   - Predictable behavior under concurrent load

## Troubleshooting

### Compiler error: "Type 'X' does not conform to the 'Sendable' protocol"

**Problem:** You're passing a class across actor boundaries, but it's not marked Sendable.

**Solution:**
- Convert the class to a struct if possible (automatic Sendable conformance)
- If it must remain a class, make it `final` and ensure all properties are Sendable, then add explicit conformance: `final class X: Sendable`
- For thread-safe classes with internal synchronization, use `@unchecked Sendable` and document why it's safe
- Ask AI: "Why isn't this type Sendable? Suggest the safest way to make it Sendable for Swift 6."

### Compiler error: "Main actor-isolated property cannot be referenced from a non-isolated context"

**Problem:** You're accessing a `@MainActor` property from an actor, Task, or non-isolated context.

**Solution:**
- Mark the calling function as `@MainActor` if it should run on main thread
- Use `await MainActor.run { }` to temporarily switch to main actor
- Pass the value as a parameter instead of accessing the property directly
- Ask AI: "This function accesses @MainActor state but isn't isolated. Should this function be @MainActor, or should I pass the value differently?"

**Example Fix:**

```swift
// ❌ Before
actor DataProcessor {
    func process(viewModel: MyViewModel) {
        let title = viewModel.title  // Error: @MainActor property
    }
}

// ✅ After - Option 1: Pass value
actor DataProcessor {
    func process(title: String) {
        // Use title parameter
    }
}

@MainActor
func caller() async {
    let vm = MyViewModel()
    await processor.process(title: vm.title)
}

// ✅ After - Option 2: Switch to main actor
actor DataProcessor {
    func process(viewModel: MyViewModel) async {
        let title = await MainActor.run { viewModel.title }
        // Use title
    }
}
```

### Warning: "Capture of 'self' with non-sendable type in a @Sendable closure"

**Problem:** Your class/actor is being captured in a concurrent closure but isn't Sendable.

**Solution:**
- Make the type `Sendable` (actors are automatically Sendable)
- Use `[weak self]` capture and handle the optional
- Redesign to pass specific values instead of capturing self
- Ask AI: "This closure captures self in a concurrent context. What's the safest fix?"

**Example:**

```swift
// ❌ Problem
class DataLoader {  // Not Sendable
    func load() {
        Task {
            // Capture of non-Sendable self
            await self.process()
        }
    }
}

// ✅ Fix: Convert to actor
actor DataLoader {  // Actors are Sendable
    func load() {
        Task {
            await self.process()  // Now safe
        }
    }
}
```

### Too many concurrency errors to fix at once

**Problem:** Enabling strict concurrency checking in a large codebase produces hundreds of errors.

**Solution:**
- Start with Swift 5 mode + strict concurrency warnings (not errors)
- Fix one module at a time, enabling Swift 6 per-module
- Use `@preconcurrency import` for dependencies not yet Swift 6 ready
- Create a tracking spreadsheet of modules and their migration status
- Ask AI: "Create a prioritized migration plan for these 50 concurrency warnings. Which should I fix first and why?"

**Gradual Migration Flag:**

```swift
// In module that's not ready yet
@preconcurrency import LegacyFramework

// Suppresses Sendable warnings from LegacyFramework
```

### "Expression is 'async' but is not marked with 'await'"

**Problem:** After adding actor isolation, you need `await` for calls that didn't require it before.

**Solution:**
- Add `await` keyword before the call
- Make the calling function `async` if it isn't already
- Understand that `await` indicates a potential suspension point, not necessarily slow code
- Ask AI: "Show me all the places in this function that now need await after converting to an actor"

### AI generates code that doesn't compile with strict concurrency

**Problem:** AI-generated code works in Swift 5 but fails Swift 6 data race checks.

**Solution:**
- Be explicit in prompts: "Generate code compatible with Swift 6 strict concurrency checking"
- Add to prompt: "Ensure all types crossing actor boundaries are Sendable"
- Ask for explanation: "Why would this code fail Swift 6 data race safety?"
- Iterate: "Fix the data race issues in this code for Swift 6"

## Additional Tips

- **Start with public API types**: Make your public-facing types Sendable first. This creates a safe boundary for clients and makes internal refactoring easier.

- **Actors for mutable state, structs for data**: If it changes, use an actor. If it's just data being passed around, use a struct. This simple rule eliminates most data races.

- **Don't fight the compiler**: Swift 6's diagnostics are sophisticated. If it says there's a data race risk, there probably is. Trust the static analysis.

- **Use Task.detached sparingly**: Detached tasks are unstructured concurrency and don't inherit actor context. Prefer structured concurrency with async/await and task groups.

- **Leverage AI for pattern recognition**: Once you fix a few data races, ask AI to find similar patterns: "Search the codebase for other classes that have the same data race pattern as the one I just fixed."

- **Value semantic types are your friend**: `String`, `Int`, `Array`, `Dictionary`, and custom structs are Sendable by default. Prefer them over classes for transferred data.

- **Test under concurrent load**: Concurrency bugs are often probabilistic. Write tests that stress your code with many concurrent operations to surface hidden races.

- **Enable Thread Sanitizer in CI**: Add Thread Sanitizer to your continuous integration tests to catch data races before they reach production.

- **Document your concurrency architecture**: Ask AI to generate architecture documentation explaining your actor hierarchy and data flow. This helps new team members understand the concurrency model.

- **Use Swift 6.2's default @MainActor isolation**: If most of your code is UI-related, Swift 6.2's default main actor isolation can simplify code. Enable it per-module with `@MainActor import SwiftUI`.

- **Create Sendable wrapper types**: For third-party non-Sendable types you can't modify, create Sendable wrapper structs that extract the data you need.

- **Pair program with AI**: For complex actor refactorings, ask AI to explain the approach first, then generate code step-by-step. This helps you learn the patterns.

- **Save successful prompts**: When you find an AI prompt that generates correct Swift 6 concurrent code, save it as a code snippet in Xcode for reuse.

## Related Articles

- KB-082: How to implement async/await for network calls with AI assistance
- KB-083: How to build AI-assisted error handling for API requests
- KB-084: How to create authentication flows with AI-generated code
- KB-085: How to ask AI to create mock data services for testing
- KB-086: How to write effective Swift Testing suites with @Suite organization
- KB-087: Protocol-oriented architecture for testable Swift applications

## Sources

- Swift.org - Data Race Safety (Swift 6 Concurrency Migration Guide) - https://www.swift.org/migration/documentation/swift-6-concurrency-migration-guide/dataracesafety/ (Accessed: November 17, 2025)
- Apple Developer Documentation - Adopting strict concurrency in Swift 6 apps - https://developer.apple.com/documentation/swift/adoptingswift6 (Accessed: November 17, 2025)
- Apple Developer Documentation - Sendable Protocol - https://developer.apple.com/documentation/swift/sendable (Accessed: November 17, 2025)
- Apple Developer Documentation - Data races in Xcode - https://developer.apple.com/documentation/xcode/data-races (Accessed: November 17, 2025)
- Apple WWDC24 - Migrate your app to Swift 6 (Session 10169) - https://developer.apple.com/videos/play/wwdc2024/10169/ (Accessed: November 17, 2025)
- Swift.org - Plotting a Path to a Package Ecosystem without Data Race Errors - https://www.swift.org/blog/ready-for-swift-6/ (Accessed: November 17, 2025)
- Swift.org - Enabling Complete Concurrency Checking - https://www.swift.org/documentation/concurrency/ (Accessed: November 17, 2025)
- Swift.org - Swift Concurrency Adoption Guidelines - https://www.swift.org/documentation/server/guides/libraries/concurrency-adoption-guidelines.html (Accessed: November 17, 2025)
- SwiftLee - Default Actor Isolation in Swift 6.2 - https://www.avanderlee.com/concurrency/default-actor-isolation-in-swift-6-2/ (Accessed: November 17, 2025)
- SwiftLee - Approachable Concurrency in Swift 6.2: A Clear Guide - https://www.avanderlee.com/concurrency/approachable-concurrency-in-swift-6-2-a-clear-guide/ (Accessed: November 17, 2025)
- Donny Wals - Solving "Main actor-isolated property can not be referenced from a Sendable closure" - https://www.donnywals.com/solving-main-actor-isolated-property-can-not-be-referenced-from-a-sendable-closure-in-swift/ (Accessed: November 17, 2025)
- Quality Coding - A Conversation With Swift 6 About Data Race Safety - https://qualitycoding.org/conversation-swift6-data-race-safety/ (Accessed: November 17, 2025)
- BrightDigit - Swift 6 Incomplete Migration Guide for Dummies - https://brightdigit.com/tutorials/swift-6-async-await-actors-fixes/ (Accessed: November 17, 2025)
- Medium - Practical Swift Concurrency: Actors, isolation, sendability - https://medium.com/@petrachkovsergey/practical-swift-concurrency-actors-isolation-sendability-a51343c2e4db (Accessed: November 17, 2025)
- InfoWorld - Swift language achieves data-race safety - https://www.infoworld.com/article/2336437/swift-achieves-data-race-safety-2.html (Accessed: November 17, 2025)
- SD Times - Development tool updates from WWDC: Xcode 26, Swift 6.2, and more - https://sdtimes.com/softwaredev/development-tool-updates-from-wwdc-foundation-models-framework-xcode-26-swift-6-2-and-more/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
