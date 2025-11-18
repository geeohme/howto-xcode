# How to implement Swift 6 concurrency with actors using AI guidance

**Article ID:** KB-086
**Difficulty:** Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 45-60 minutes

## Overview

Swift 6 introduces complete concurrency checking with compile-time data-race safety, making concurrent programming significantly safer. This guide demonstrates how to leverage AI coding assistants in Xcode 26.1+ to implement actors for thread-safe state management, understand @MainActor isolation, and adopt Sendable protocols. You'll learn to prompt AI effectively to generate production-ready concurrent code that eliminates data races while following Swift 6.2's latest concurrency improvements.

## Prerequisites

- Xcode 26.1+ installed with Swift 6 support
- Understanding of async/await patterns (see KB-082)
- Familiarity with Swift classes and structs
- At least one AI provider configured (ChatGPT or Claude - see KB-031, KB-043)
- iOS 15+ deployment target for full concurrency support
- Basic understanding of multithreading concepts

## Steps

### Step 1: Enable Swift 6 Language Mode and Strict Concurrency Checking

Before generating actor code, configure your project for complete concurrency checking.

1. Open your project in Xcode 26.1+
2. Select your target in Project Navigator
3. Navigate to **Build Settings**
4. Search for "Swift Language Version"
5. Set to **Swift 6**
6. Search for "Strict Concurrency Checking"
7. Set to **Complete**

This ensures the compiler identifies all potential data races at compile time rather than runtime.

**Alternative: Enable as warnings first** (recommended for existing projects):
- Set Swift Language Version to **Swift 5**
- Set Strict Concurrency Checking to **Complete**
- This shows concurrency issues as warnings, allowing incremental migration

### Step 2: Understand Actors with AI Assistance

Before implementing, ask your AI assistant to explain actors in the context of your project.

**Prompt for AI:**
```
Explain Swift 6 actors and how they differ from classes. Include:
1. How actors prevent data races
2. When to use actors vs classes
3. The role of await when accessing actor properties
4. How actor isolation works in Swift 6
Provide a simple example comparing a thread-unsafe class to a thread-safe actor.
```

**Expected AI Explanation:**

The AI will explain that actors are reference types (like classes) but with automatic synchronization. Key points:

- **Automatic serialization**: Only one task can access actor state at a time
- **Isolation**: Actor properties/methods are isolated from external concurrent access
- **await requirement**: Accessing actor members from outside requires `await`
- **No data races**: Compiler guarantees thread safety

**Example AI should provide:**

```swift
// ❌ Thread-unsafe class - can have data races
class UnsafeBankAccount {
    var balance: Double = 0

    func deposit(amount: Double) {
        balance += amount  // Race condition!
    }
}

// ✅ Thread-safe actor - Swift 6 prevents data races
actor SafeBankAccount {
    var balance: Double = 0

    func deposit(amount: Double) {
        balance += amount  // Automatically synchronized
    }
}

// Usage
Task {
    let account = SafeBankAccount()
    await account.deposit(amount: 100)  // 'await' required
    let currentBalance = await account.balance
}
```

### Step 3: Generate a Data Manager Actor with AI

Create a practical actor for managing shared application state.

**Prompt for AI:**
```
Create an ImageCacheActor for Swift 6 that:
1. Manages a dictionary of cached images
2. Provides thread-safe get/set methods
3. Implements cache size limits (max 100 images)
4. Includes a method to clear cache
5. Uses proper actor isolation
6. Makes all types Sendable-compliant
```

**Expected AI-Generated Code:**

```swift
import UIKit

actor ImageCacheActor {
    private var cache: [String: UIImage] = [:]
    private let maxCacheSize = 100

    // Get cached image
    func image(forKey key: String) -> UIImage? {
        return cache[key]
    }

    // Store image in cache
    func setImage(_ image: UIImage, forKey key: String) {
        // Remove oldest entry if cache is full
        if cache.count >= maxCacheSize, cache[key] == nil {
            if let firstKey = cache.keys.first {
                cache.removeValue(forKey: firstKey)
            }
        }
        cache[key] = image
    }

    // Remove specific image
    func removeImage(forKey key: String) {
        cache.removeValue(forKey: key)
    }

    // Clear entire cache
    func clearCache() {
        cache.removeAll()
    }

    // Get cache statistics
    func cacheInfo() -> (count: Int, keys: [String]) {
        return (cache.count, Array(cache.keys))
    }
}
```

**Verification points:**
- `actor` keyword instead of `class`
- No explicit locking mechanisms needed
- All methods can safely mutate `cache` dictionary
- External access requires `await`

### Step 4: Implement @MainActor for UI Updates

Learn to use @MainActor to ensure UI updates happen on the main thread.

**Prompt for AI:**
```
Create a Swift 6 ImageLoaderViewModel using @Observable that:
1. Uses the ImageCacheActor from previous step
2. Loads images from URLs asynchronously
3. Updates UI state on @MainActor
4. Handles loading and error states
5. Follows Swift 6.2 concurrency best practices
```

**Expected AI-Generated ViewModel:**

```swift
import SwiftUI

@Observable
@MainActor
class ImageLoaderViewModel {
    private let cacheActor: ImageCacheActor

    var currentImage: UIImage?
    var isLoading = false
    var errorMessage: String?

    init(cacheActor: ImageCacheActor) {
        self.cacheActor = cacheActor
    }

    func loadImage(from urlString: String) async {
        isLoading = true
        errorMessage = nil

        // Check cache first
        if let cachedImage = await cacheActor.image(forKey: urlString) {
            currentImage = cachedImage
            isLoading = false
            return
        }

        // Download image
        guard let url = URL(string: urlString) else {
            errorMessage = "Invalid URL"
            isLoading = false
            return
        }

        do {
            let (data, _) = try await URLSession.shared.data(from: url)

            guard let image = UIImage(data: data) else {
                errorMessage = "Invalid image data"
                isLoading = false
                return
            }

            // Cache the image
            await cacheActor.setImage(image, forKey: urlString)

            // Update UI (already on MainActor)
            currentImage = image

        } catch {
            errorMessage = "Failed to load: \(error.localizedDescription)"
        }

        isLoading = false
    }

    func clearCurrentImage() async {
        if let image = currentImage,
           let urlString = await findURLForImage(image) {
            await cacheActor.removeImage(forKey: urlString)
        }
        currentImage = nil
    }

    private func findURLForImage(_ image: UIImage) async -> String? {
        // Implementation to find URL for cached image
        let info = await cacheActor.cacheInfo()
        // Simplified - in production, maintain bidirectional mapping
        return info.keys.first
    }
}
```

**Key features to verify:**
- `@MainActor` annotation on class ensures all properties/methods run on main thread
- Can safely update `@Observable` properties (currentImage, isLoading, errorMessage)
- Uses `await` when accessing `cacheActor` from different actor context
- No manual `DispatchQueue.main.async` calls needed

### Step 5: Understand and Implement Sendable Protocol

Make custom types safe for concurrent contexts.

**Prompt for AI:**
```
Explain the Sendable protocol in Swift 6 and show me how to:
1. Make a struct Sendable
2. Make a class Sendable
3. Handle cases where a type can't automatically conform
4. Use @unchecked Sendable when necessary
Include examples of ImageMetadata struct and ImageProcessor class.
```

**Expected AI Response with Examples:**

```swift
// Structs with all Sendable properties are automatically Sendable
struct ImageMetadata: Sendable {
    let url: String
    let width: Int
    let height: Int
    let fileSize: Int
    let downloadedAt: Date
}

// Classes must meet strict requirements to be Sendable
final class ImageProcessor: Sendable {
    // ✅ All properties must be immutable (let) and Sendable
    let processorName: String
    let maxSize: CGSize

    init(name: String, maxSize: CGSize) {
        self.processorName = name
        self.maxSize = maxSize
    }

    func process(_ image: UIImage) -> UIImage {
        // Image processing logic
        return image
    }
}

// For thread-safe classes with internal synchronization
final class ThreadSafeCounter: @unchecked Sendable {
    private let lock = NSLock()
    private var value: Int = 0

    func increment() {
        lock.lock()
        defer { lock.unlock() }
        value += 1
    }

    func getValue() -> Int {
        lock.lock()
        defer { lock.unlock() }
        return value
    }
}

// Using Sendable types with actors
actor ImageProcessingActor {
    func processWithMetadata(
        image: UIImage,
        metadata: ImageMetadata  // Sendable - can cross actor boundary
    ) async -> UIImage {
        let processor = ImageProcessor(
            name: "Thumbnail",
            maxSize: CGSize(width: 200, height: 200)
        )
        return processor.process(image)
    }
}
```

**AI should explain:**
- Value types (struct, enum) are automatically Sendable if all properties are Sendable
- Classes need to be `final`, have no mutable state, and only Sendable properties
- `@unchecked Sendable` opts out of compiler checks - use only when you guarantee thread safety
- Sendable is required for types crossing actor boundaries

### Step 6: Create a Complex Multi-Actor System

Build a realistic feature using multiple actors working together.

**Prompt for AI:**
```
Design a download manager system for Swift 6 using actors that:
1. DownloadActor: manages active downloads with progress tracking
2. FileStorageActor: handles saving files to disk
3. DownloadCoordinator: @MainActor ViewModel coordinating the system
4. Proper error handling and cancellation support
5. All types Sendable-compliant
Show how these actors communicate safely.
```

**Expected AI-Generated System:**

```swift
import Foundation
import SwiftUI

// MARK: - Sendable Models

struct Download: Sendable, Identifiable {
    let id: UUID
    let url: URL
    let fileName: String
    var progress: Double
    var state: DownloadState

    enum DownloadState: Sendable {
        case pending
        case downloading
        case completed
        case failed(String)
    }
}

// MARK: - Download Actor

actor DownloadActor {
    private var activeDownloads: [UUID: URLSessionDownloadTask] = [:]
    private var downloadProgress: [UUID: Double] = [:]

    func startDownload(id: UUID, url: URL) async throws -> URL {
        // Create download task
        let session = URLSession.shared

        return try await withCheckedThrowingContinuation { continuation in
            let task = session.downloadTask(with: url) { localURL, response, error in
                if let error = error {
                    continuation.resume(throwing: error)
                    return
                }

                guard let localURL = localURL else {
                    continuation.resume(throwing: DownloadError.invalidResponse)
                    return
                }

                continuation.resume(returning: localURL)
            }

            activeDownloads[id] = task
            task.resume()
        }
    }

    func cancelDownload(id: UUID) {
        activeDownloads[id]?.cancel()
        activeDownloads.removeValue(forKey: id)
        downloadProgress.removeValue(forKey: id)
    }

    func updateProgress(id: UUID, progress: Double) {
        downloadProgress[id] = progress
    }

    func getProgress(id: UUID) -> Double {
        return downloadProgress[id] ?? 0.0
    }
}

enum DownloadError: Error, LocalizedError {
    case invalidResponse
    case saveFailed
    case fileNotFound

    var errorDescription: String? {
        switch self {
        case .invalidResponse: return "Invalid server response"
        case .saveFailed: return "Failed to save file"
        case .fileNotFound: return "Downloaded file not found"
        }
    }
}

// MARK: - File Storage Actor

actor FileStorageActor {
    private let fileManager = FileManager.default
    private var documentsDirectory: URL {
        fileManager.urls(for: .documentDirectory, in: .userDomainMask)[0]
    }

    func saveFile(from temporaryURL: URL, withName fileName: String) throws -> URL {
        let destinationURL = documentsDirectory.appendingPathComponent(fileName)

        // Remove existing file if present
        if fileManager.fileExists(atPath: destinationURL.path) {
            try fileManager.removeItem(at: destinationURL)
        }

        try fileManager.copyItem(at: temporaryURL, to: destinationURL)
        return destinationURL
    }

    func deleteFile(at url: URL) throws {
        try fileManager.removeItem(at: url)
    }

    func fileExists(named fileName: String) -> Bool {
        let url = documentsDirectory.appendingPathComponent(fileName)
        return fileManager.fileExists(atPath: url.path)
    }

    func listDownloadedFiles() -> [String] {
        guard let contents = try? fileManager.contentsOfDirectory(
            at: documentsDirectory,
            includingPropertiesForKeys: nil
        ) else {
            return []
        }
        return contents.map { $0.lastPathComponent }
    }
}

// MARK: - Main Actor Coordinator

@Observable
@MainActor
class DownloadCoordinator {
    private let downloadActor: DownloadActor
    private let storageActor: FileStorageActor

    var downloads: [Download] = []
    var errorMessage: String?

    init(
        downloadActor: DownloadActor = DownloadActor(),
        storageActor: FileStorageActor = FileStorageActor()
    ) {
        self.downloadActor = downloadActor
        self.storageActor = storageActor
    }

    func addDownload(url: URL, fileName: String) {
        let download = Download(
            id: UUID(),
            url: url,
            fileName: fileName,
            progress: 0,
            state: .pending
        )
        downloads.append(download)

        Task {
            await performDownload(download)
        }
    }

    private func performDownload(_ download: Download) async {
        // Update state to downloading
        if let index = downloads.firstIndex(where: { $0.id == download.id }) {
            downloads[index].state = .downloading
        }

        do {
            // Start download using DownloadActor
            let temporaryURL = try await downloadActor.startDownload(
                id: download.id,
                url: download.url
            )

            // Save file using FileStorageActor
            let savedURL = try await storageActor.saveFile(
                from: temporaryURL,
                withName: download.fileName
            )

            // Update state to completed
            if let index = downloads.firstIndex(where: { $0.id == download.id }) {
                downloads[index].state = .completed
                downloads[index].progress = 1.0
            }

        } catch {
            // Update state to failed
            if let index = downloads.firstIndex(where: { $0.id == download.id }) {
                downloads[index].state = .failed(error.localizedDescription)
            }
            errorMessage = error.localizedDescription
        }
    }

    func cancelDownload(_ download: Download) async {
        await downloadActor.cancelDownload(id: download.id)

        if let index = downloads.firstIndex(where: { $0.id == download.id }) {
            downloads.remove(at: index)
        }
    }

    func loadSavedFiles() async {
        let files = await storageActor.listDownloadedFiles()
        print("Downloaded files: \(files)")
    }
}
```

**Verify the system:**
- Three separate actors with distinct responsibilities
- Sendable types (Download, DownloadState) can cross actor boundaries
- @MainActor coordinator updates UI-bound state
- Actor isolation prevents data races between concurrent downloads

### Step 7: Handle Actor Isolation Boundaries

Learn to work with nonisolated and custom isolation.

**Prompt for AI:**
```
Show me how to:
1. Use 'nonisolated' to opt out of actor isolation for specific methods
2. Create computed properties in actors
3. Implement protocols that require non-isolated methods
4. Handle callbacks and closures in actor contexts
Provide examples with a NetworkActor that implements a protocol.
```

**Expected AI Code:**

```swift
import Foundation

// Protocol with non-isolated requirement
protocol NetworkReporting {
    var reportingID: String { get }
    func logRequest(_ request: URLRequest)
}

actor NetworkActor: NetworkReporting {
    private var requestCount = 0
    private var activeRequests: Set<UUID> = []

    // Stored property - isolated by default
    private let actorID = UUID()

    // nonisolated: can be accessed without 'await'
    nonisolated let reportingID: String = "NetworkActor-001"

    // nonisolated method: no access to isolated state
    nonisolated func logRequest(_ request: URLRequest) {
        print("Request to: \(request.url?.absoluteString ?? "unknown")")
        // Cannot access requestCount here - it's isolated
    }

    // Regular isolated method
    func performRequest(id: UUID, url: URL) async throws -> Data {
        activeRequests.insert(id)
        requestCount += 1

        let (data, _) = try await URLSession.shared.data(from: url)

        activeRequests.remove(id)
        return data
    }

    // Computed property - isolated
    var statistics: String {
        "Total requests: \(requestCount), Active: \(activeRequests.count)"
    }

    // nonisolated computed property accessing stored nonisolated property
    nonisolated var actorDescription: String {
        "NetworkActor with ID: \(reportingID)"
    }
}

// Using the actor
Task {
    let network = NetworkActor()

    // No await needed for nonisolated members
    print(network.reportingID)
    print(network.actorDescription)

    // await required for isolated members
    let stats = await network.statistics
    print(stats)
}
```

**AI should explain:**
- `nonisolated` removes actor isolation from specific members
- Nonisolated members cannot access isolated state
- Useful for protocol conformance and simple utility methods
- Computed properties in actors are isolated unless marked nonisolated

### Step 8: Test Actor Code with AI-Generated Tests

Create comprehensive tests for concurrent code.

**Prompt for AI:**
```
Generate XCTest unit tests for the ImageCacheActor that:
1. Test concurrent access from multiple tasks
2. Verify thread safety with Task.detached
3. Test cache size limits
4. Validate proper isolation
5. Use async/await testing patterns in Swift 6
Include tests that would fail with a non-actor implementation.
```

**Expected AI-Generated Tests:**

```swift
import XCTest
@testable import YourApp

final class ImageCacheActorTests: XCTestCase {
    var sut: ImageCacheActor!

    override func setUp() async throws {
        sut = ImageCacheActor()
    }

    override func tearDown() async throws {
        await sut.clearCache()
        sut = nil
    }

    // Test basic storage and retrieval
    func testImageStorageAndRetrieval() async throws {
        let testImage = UIImage(systemName: "star.fill")!
        let key = "test-key"

        await sut.setImage(testImage, forKey: key)
        let retrieved = await sut.image(forKey: key)

        XCTAssertNotNil(retrieved)
    }

    // Test concurrent access - would fail without actor isolation
    func testConcurrentAccess() async throws {
        let iterations = 100

        await withTaskGroup(of: Void.self) { group in
            // Launch multiple concurrent write tasks
            for i in 0..<iterations {
                group.addTask {
                    let image = UIImage(systemName: "star.fill")!
                    await self.sut.setImage(image, forKey: "key-\(i)")
                }
            }
        }

        // Verify all images were stored
        let info = await sut.cacheInfo()
        XCTAssertEqual(info.count, 100)
    }

    // Test cache size limit
    func testCacheSizeLimit() async throws {
        // Add 150 images (limit is 100)
        for i in 0..<150 {
            let image = UIImage(systemName: "star.fill")!
            await sut.setImage(image, forKey: "key-\(i)")
        }

        let info = await sut.cacheInfo()
        XCTAssertLessThanOrEqual(info.count, 100)
    }

    // Test removal
    func testImageRemoval() async throws {
        let image = UIImage(systemName: "star.fill")!
        await sut.setImage(image, forKey: "remove-test")

        var retrieved = await sut.image(forKey: "remove-test")
        XCTAssertNotNil(retrieved)

        await sut.removeImage(forKey: "remove-test")
        retrieved = await sut.image(forKey: "remove-test")
        XCTAssertNil(retrieved)
    }

    // Test clear cache
    func testClearCache() async throws {
        // Add multiple images
        for i in 0..<10 {
            let image = UIImage(systemName: "star.fill")!
            await sut.setImage(image, forKey: "key-\(i)")
        }

        await sut.clearCache()
        let info = await sut.cacheInfo()
        XCTAssertEqual(info.count, 0)
    }

    // Test actor isolation prevents data races
    func testActorIsolationPreventsRaces() async throws {
        let image = UIImage(systemName: "star.fill")!
        let raceKey = "race-test"

        // This would cause race conditions without actor isolation
        await withTaskGroup(of: Void.self) { group in
            for _ in 0..<50 {
                group.addTask {
                    await self.sut.setImage(image, forKey: raceKey)
                }
                group.addTask {
                    _ = await self.sut.image(forKey: raceKey)
                }
                group.addTask {
                    await self.sut.removeImage(forKey: raceKey)
                }
            }
        }

        // Test passes if no crashes occur - actor prevents races
        XCTAssertTrue(true, "Actor prevented data races")
    }
}
```

### Step 9: Migrate Existing Classes to Actors with AI

Convert legacy non-thread-safe code to actors.

**Prompt for AI:**
```
I have this legacy thread-unsafe UserSessionManager class:

[paste your old class]

Convert it to a Swift 6 actor with:
1. Proper actor isolation
2. @MainActor for properties that update UI
3. Sendable types throughout
4. Maintain the same public interface
5. Add migration comments explaining changes
```

**Example transformation AI provides:**

```swift
// BEFORE: Thread-unsafe class
class UserSessionManager {
    var currentUser: User?
    var isLoggedIn: Bool = false

    func login(user: User) {
        self.currentUser = user
        self.isLoggedIn = true
    }
}

// AFTER: Thread-safe actor
actor UserSessionManager {
    // Isolated state - automatically thread-safe
    private var currentUser: User?
    private var isLoggedIn: Bool = false

    // Migration note: Callers must now use 'await'
    func login(user: User) async {
        self.currentUser = user
        self.isLoggedIn = true
    }

    // Migration note: Use this for UI updates
    @MainActor
    func getLoginStatus() -> Bool {
        // Note: Can't directly access isolated state from @MainActor
        // Need to refactor
        return false
    }

    // Better approach: Keep state checks in actor
    func checkLoginStatus() -> Bool {
        return isLoggedIn
    }
}

// Updated usage
Task {
    let manager = UserSessionManager()
    await manager.login(user: someUser)
    let status = await manager.checkLoginStatus()
}
```

### Step 10: Debug Common Actor Issues with AI

Use AI to troubleshoot concurrency problems.

**Prompt for AI when you encounter errors:**

```
I'm getting this Swift 6 concurrency error:
"Actor-isolated property 'balance' can not be referenced from a non-isolated context"

Here's my code:
[paste your code]

Explain:
1. Why this error occurs
2. How actor isolation works here
3. How to fix it
4. Best practices to avoid this
```

**AI will provide analysis and fixes:**

Common issues AI helps solve:
- Missing `await` when accessing actor members
- Trying to access isolated state from wrong context
- Sendable conformance errors
- MainActor isolation for UI updates
- Task executor mismatch warnings

## Expected Results

After completing these steps, you should have:

1. **Thread-safe actors** managing shared state without explicit locks or dispatch queues
2. **@MainActor isolated ViewModels** ensuring UI updates on main thread
3. **Sendable-compliant types** that safely cross actor boundaries
4. **No data-race warnings** when building with Swift 6 complete concurrency checking
5. **Multi-actor systems** with proper isolation boundaries and communication patterns
6. **Comprehensive tests** validating concurrent behavior
7. **Clean migration path** from legacy code to Swift 6 concurrency

**Visual indicators of success:**
- Build succeeds with zero concurrency warnings/errors in Swift 6 mode
- Instruments Thread Sanitizer shows no data races
- UI remains responsive during concurrent operations
- Xcode shows purple async/await indicators correctly placed
- Code completion suggests proper isolation boundaries

## Troubleshooting

### Common Issue 1: "Expression is 'async' but is not marked with 'await'"

**Problem:** Forgetting to use `await` when calling actor methods from outside the actor
**Solution:**
```swift
// ❌ Wrong
let image = cacheActor.image(forKey: "test")

// ✅ Correct
let image = await cacheActor.image(forKey: "test")
```

**AI Assistance Tip:** Ask "Review my actor usage and identify missing await keywords"

### Common Issue 2: "Actor-isolated property cannot be referenced from non-isolated context"

**Problem:** Trying to access actor state directly without using actor methods
**Solution:**
```swift
actor DataStore {
    var items: [String] = []  // Isolated

    // ✅ Provide isolated methods for access
    func getItems() -> [String] {
        return items
    }

    func addItem(_ item: String) {
        items.append(item)
    }
}

// Usage
let store = DataStore()
// ❌ let items = store.items
// ✅ let items = await store.getItems()
```

**AI Assistance Tip:** Prompt "Refactor this actor to provide proper accessor methods"

### Common Issue 3: "Type 'MyClass' does not conform to the 'Sendable' protocol"

**Problem:** Passing non-Sendable types between actors
**Solution:**

```swift
// ❌ Non-Sendable class
class UserData {
    var name: String
    init(name: String) { self.name = name }
}

// ✅ Option 1: Make it a Sendable struct
struct UserData: Sendable {
    let name: String
}

// ✅ Option 2: Make class Sendable (if immutable)
final class UserData: Sendable {
    let name: String
    init(name: String) { self.name = name }
}

// ✅ Option 3: Use @unchecked Sendable with internal safety
final class UserData: @unchecked Sendable {
    private let lock = NSLock()
    private var _name: String

    var name: String {
        lock.lock()
        defer { lock.unlock() }
        return _name
    }

    init(name: String) { self._name = name }
}
```

**AI Assistance Tip:** Ask "Make this type Sendable-compliant for Swift 6"

### Common Issue 4: "Publishing changes from background threads is not allowed"

**Problem:** Updating @Observable or @Published properties from non-MainActor context
**Solution:**

```swift
// ❌ Wrong - updates from background actor
@Observable
class ViewModel {
    var data: String = ""

    func loadData() async {
        // This runs on background - crash!
        data = await someActor.getData()
    }
}

// ✅ Correct - isolate to MainActor
@Observable
@MainActor
class ViewModel {
    var data: String = ""

    func loadData() async {
        // Automatically on MainActor - safe!
        data = await someActor.getData()
    }
}
```

**AI Assistance Tip:** Prompt "Fix MainActor isolation issues in this ViewModel"

### Common Issue 5: AI Generates Non-Sendable Closures

**Problem:** AI creates closures that capture non-Sendable state
**Solution:**

```swift
// ❌ AI might generate this
actor TaskManager {
    func performTask(completion: () -> Void) {  // Not Sendable
        completion()
    }
}

// ✅ Request Sendable closure
actor TaskManager {
    func performTask(completion: @Sendable () -> Void) {
        completion()
    }
}
```

**Prompt correction:**
```
That closure needs to be @Sendable for Swift 6. Please update the
signature to use @Sendable closures and ensure captured values are
also Sendable.
```

## Additional Tips

- **Model selection**: Claude Sonnet 4.5+ excels at explaining complex concurrency concepts; GPT-4 Turbo is faster for boilerplate actor code
- **Incremental adoption**: Enable strict concurrency as warnings first, then migrate module-by-module
- **Actor granularity**: Create actors around logical resource boundaries (database, network, cache), not per-object
- **Avoid actor over-use**: Not everything needs to be an actor - use structs for simple value types
- **Performance monitoring**: Use Instruments > System Trace to verify actor queuing isn't causing bottlenecks
- **Swift 6.2 features**: If using Swift 6.2+, leverage "approachable concurrency" with implicit @MainActor
- **Documentation**: Ask AI to generate inline documentation explaining isolation boundaries
- **Code review**: Request AI to review for "Sendable violations" and "potential data races"
- **Task groups**: For parallel operations, ask AI for "task group examples with proper cancellation"
- **Global actors**: Create custom global actors for domain-specific isolation beyond @MainActor
- **Testing strategy**: Test actors with high concurrency (100+ simultaneous tasks) to catch race conditions
- **Migration patterns**: Ask AI for before/after examples when converting callbacks to async/await with actors

## Related Articles

- KB-031: How to access the coding assistant in Xcode 26 (ChatGPT)
- KB-043: How to add Claude as a model provider in Xcode
- KB-082: How to implement async/await for network calls using AI assistance
- KB-051: How to use AI to generate unit tests for existing code
- KB-055: How to have AI explain error messages and suggest fixes
- KB-079: How to use the @Observable macro for state management
- KB-080: How to implement SwiftData for modern persistence
- KB-081: How to build a network service layer with AI-generated code

## Sources

- Swift 6.2 Released | Swift.org - https://www.swift.org/blog/swift-6.2-released/ (Accessed: November 17, 2025)
- Adopting strict concurrency in Swift 6 apps | Apple Developer Documentation - https://developer.apple.com/documentation/swift/adoptingswift6 (Accessed: November 17, 2025)
- Enabling Complete Concurrency Checking | Swift.org - https://www.swift.org/documentation/concurrency/ (Accessed: November 17, 2025)
- Complete concurrency enabled by default – available from Swift 6.0 - Hacking with Swift - https://www.hackingwithswift.com/swift/6.0/concurrency (Accessed: November 17, 2025)
- Approachable Concurrency in Swift 6.2: A Clear Guide - SwiftLee - https://www.avanderlee.com/concurrency/approachable-concurrency-in-swift-6-2-a-clear-guide/ (Accessed: November 17, 2025)
- MainActor usage in Swift explained to dispatch to the main thread - SwiftLee - https://www.avanderlee.com/swift/mainactor-dispatch-main-thread/ (Accessed: November 17, 2025)
- Understanding Concurrency in Swift 6 with Sendable protocol, MainActor, and async-await | Medium - https://medium.com/@egzonpllana/understanding-concurrency-in-swift-6-with-sendable-protocol-mainactor-and-async-await-5ccfdc0ca2b6 (Accessed: November 17, 2025)
- Sendable and @Sendable closures explained with code examples - SwiftLee - https://www.avanderlee.com/swift/sendable-protocol-closures/ (Accessed: November 17, 2025)
- Migrate your app to Swift 6 - WWDC24 - Apple Developer - https://developer.apple.com/videos/play/wwdc2024/10169/ (Accessed: November 17, 2025)
- Eliminate data races using Swift Concurrency - WWDC22 - Apple Developer - https://developer.apple.com/videos/play/wwdc2022/110351/ (Accessed: November 17, 2025)
- Swift 6 Incomplete Migration Guide for Dummies | BrightDigit - https://brightdigit.com/tutorials/swift-6-async-await-actors-fixes/ (Accessed: November 17, 2025)
- Practical Swift Concurrency: Actors, isolation, sendability | Medium - https://medium.com/@petrachkovsergey/practical-swift-concurrency-actors-isolation-sendability-a51343c2e4db (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
