# How to use @Observable macro for state management (Swift 6)

**Article ID:** KB-079
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 25-30 minutes

## Overview

The `@Observable` macro, introduced with Swift's Observation framework, provides a modern, efficient approach to state management in SwiftUI applications. It replaces the older `ObservableObject` protocol with a cleaner syntax and superior performance, offering granular change tracking that only updates views when the specific properties they depend on change. This article demonstrates how to implement `@Observable` for state management in Swift 6 with Xcode 26.1+ and iOS 18.2+.

## Prerequisites

- Xcode 26.1 or later installed
- Swift 6 language mode enabled
- iOS 18.2+ deployment target (or macOS 14+, tvOS 17+, watchOS 10+)
- Basic understanding of SwiftUI views and state management concepts
- Familiarity with Swift classes and properties
- Related: KB-022 (State property wrapper basics)

## Steps

### Step 1: Import the Observation Framework

The Observation framework provides the `@Observable` macro. While it's automatically available in SwiftUI projects, you should explicitly import it when using it outside of SwiftUI contexts.

```swift
import SwiftUI
import Observation

// The Observation framework is now available
```

**Note:** For SwiftUI-only usage, the import is typically handled automatically, but explicit imports improve code clarity and are required for UIKit integration.

### Step 2: Create an Observable Class

Define a class and apply the `@Observable` macro. Unlike `ObservableObject`, you don't need to conform to any protocols or use `@Published` for individual properties.

```swift
@Observable
class UserProfileViewModel {
    var username: String = ""
    var email: String = ""
    var age: Int = 0
    var isLoggedIn: Bool = false
    var profileImageURL: String?

    // Computed properties are also tracked automatically
    var displayName: String {
        username.isEmpty ? "Guest" : username
    }

    // Methods can mutate state directly
    func login(username: String, email: String) {
        self.username = username
        self.email = email
        self.isLoggedIn = true
    }

    func logout() {
        self.username = ""
        self.email = ""
        self.isLoggedIn = false
        self.profileImageURL = nil
    }
}
```

**Key advantages:**
- All stored properties are automatically observable
- No need for `@Published` property wrapper
- Cleaner, more intuitive syntax
- Full Swift 6 concurrency support

### Step 3: Use Observable Objects in SwiftUI Views with @State

When creating an instance of an `@Observable` class in a SwiftUI view, use `@State` instead of `@StateObject`. This is a critical difference from the old `ObservableObject` approach.

```swift
struct ProfileView: View {
    @State private var viewModel = UserProfileViewModel()

    var body: some View {
        VStack(spacing: 20) {
            if viewModel.isLoggedIn {
                Text("Welcome, \(viewModel.displayName)!")
                    .font(.title)

                Text("Email: \(viewModel.email)")
                    .font(.subheadline)

                Button("Logout") {
                    viewModel.logout()
                }
            } else {
                Text("Please log in")
                LoginForm(viewModel: viewModel)
            }
        }
        .padding()
    }
}
```

**Important:** Only the view that creates the `@Observable` instance should use `@State`. Child views receive it as a regular parameter.

### Step 4: Pass Observable Objects to Child Views

Pass `@Observable` objects to child views as regular parameters without any property wrapper. The observation system automatically tracks changes.

```swift
struct LoginForm: View {
    var viewModel: UserProfileViewModel  // No property wrapper needed!
    @State private var usernameInput = ""
    @State private var emailInput = ""

    var body: some View {
        VStack(spacing: 15) {
            TextField("Username", text: $usernameInput)
                .textFieldStyle(.roundedBorder)

            TextField("Email", text: $emailInput)
                .textFieldStyle(.roundedBorder)
                .keyboardType(.emailAddress)

            Button("Sign In") {
                viewModel.login(username: usernameInput, email: emailInput)
            }
            .buttonStyle(.borderedProminent)
        }
        .padding()
    }
}
```

**Performance benefit:** This view will only re-render when the properties it actually reads from `viewModel` change, not when any property changes.

### Step 5: Use Environment for Dependency Injection

For sharing observable objects across multiple views in the hierarchy, use the `.environment()` modifier and `@Environment` property wrapper.

```swift
// In your app's root or parent view
@main
struct MyApp: App {
    @State private var appState = AppStateManager()

    var body: some Scene {
        WindowGroup {
            ContentView()
                .environment(appState)  // Inject into environment
        }
    }
}

// Observable app state manager
@Observable
class AppStateManager {
    var theme: ColorScheme = .light
    var isNetworkAvailable: Bool = true
    var currentUser: User?
}

// Access in any child view
struct SettingsView: View {
    @Environment(AppStateManager.self) private var appState

    var body: some View {
        VStack {
            Toggle("Dark Mode", isOn: Binding(
                get: { appState.theme == .dark },
                set: { appState.theme = $0 ? .dark : .light }
            ))

            if let user = appState.currentUser {
                Text("Current User: \(user.name)")
            }
        }
    }
}
```

**Note:** This replaces the old `.environmentObject()` modifier and `@EnvironmentObject` property wrapper.

### Step 6: Control Property Observation with @ObservationIgnored

Sometimes you need properties that shouldn't trigger view updates. Use `@ObservationIgnored` for these cases.

```swift
@Observable
class DataViewModel {
    var publicData: String = ""  // Observed by default
    var userName: String = ""     // Observed by default

    @ObservationIgnored
    var cache: [String: Any] = [:]  // Changes won't trigger updates

    @ObservationIgnored
    var privateAPIKey: String = ""  // Not observed

    func updateCache(key: String, value: Any) {
        // Modifying cache won't cause view re-renders
        cache[key] = value
    }
}
```

**Use cases for @ObservationIgnored:**
- Internal caches and temporary storage
- Performance optimization for frequently changing data
- Private implementation details
- Large data structures that don't affect UI

### Step 7: Observe Changes Outside SwiftUI with withObservationTracking

To track changes to `@Observable` objects outside of SwiftUI views (in UIKit, testing, or custom code), use `withObservationTracking`.

```swift
import Observation

@Observable
class TemperatureMonitor {
    var currentTemp: Double = 20.0
    var unit: String = "Celsius"
}

func monitorTemperatureChanges() {
    let monitor = TemperatureMonitor()

    func observe() {
        withObservationTracking {
            // This block tracks which properties are accessed
            print("Current temperature: \(monitor.currentTemp)°\(monitor.unit)")
        } onChange: {
            // This block is called when tracked properties change
            print("Temperature changed!")
            observe()  // Re-establish tracking
        }
    }

    observe()

    // Later, when temperature changes:
    monitor.currentTemp = 25.0  // Triggers the onChange block
}
```

**Important:** You must call the tracking function recursively in the `onChange` block to continue observing changes.

### Step 8: Use Observable with Swift Concurrency

`@Observable` classes work seamlessly with Swift's concurrency features. Mark your class with `@MainActor` when dealing with UI-related state.

```swift
@Observable
@MainActor
class AsyncDataLoader {
    var items: [Item] = []
    var isLoading: Bool = false
    var errorMessage: String?

    func loadData() async {
        isLoading = true
        errorMessage = nil

        do {
            // Simulate network request
            try await Task.sleep(for: .seconds(2))
            items = try await fetchItemsFromAPI()
            isLoading = false
        } catch {
            errorMessage = error.localizedDescription
            isLoading = false
        }
    }

    private func fetchItemsFromAPI() async throws -> [Item] {
        // API call implementation
        return []
    }
}

// Usage in SwiftUI
struct DataListView: View {
    @State private var loader = AsyncDataLoader()

    var body: some View {
        List(loader.items) { item in
            Text(item.name)
        }
        .overlay {
            if loader.isLoading {
                ProgressView("Loading...")
            }
        }
        .task {
            await loader.loadData()
        }
        .alert("Error", isPresented: .constant(loader.errorMessage != nil)) {
            Button("OK") {
                loader.errorMessage = nil
            }
        } message: {
            if let error = loader.errorMessage {
                Text(error)
            }
        }
    }
}

struct Item: Identifiable {
    let id = UUID()
    let name: String
}
```

### Step 9: Migrate from ObservableObject to @Observable

If you're migrating existing code, follow these conversion steps:

**Before (ObservableObject):**
```swift
class OldViewModel: ObservableObject {
    @Published var name: String = ""
    @Published var count: Int = 0
}

struct OldView: View {
    @StateObject private var viewModel = OldViewModel()
    @ObservedObject var sharedViewModel: OldViewModel

    var body: some View {
        Text(viewModel.name)
    }
}
```

**After (@Observable):**
```swift
@Observable
class NewViewModel {
    var name: String = ""  // No @Published needed
    var count: Int = 0
}

struct NewView: View {
    @State private var viewModel = NewViewModel()  // @State instead of @StateObject
    var sharedViewModel: NewViewModel  // No property wrapper for parameters

    var body: some View {
        Text(viewModel.name)
    }
}
```

**Migration checklist:**
- ✓ Remove `ObservableObject` conformance
- ✓ Add `@Observable` macro to class
- ✓ Remove all `@Published` wrappers
- ✓ Change `@StateObject` to `@State`
- ✓ Remove `@ObservedObject` from child view parameters
- ✓ Replace `.environmentObject()` with `.environment()`
- ✓ Replace `@EnvironmentObject` with `@Environment`

## Expected Results

After implementing `@Observable` for state management, you should experience:

1. **Improved Performance**: Views only update when the specific properties they read change, not when any property in the object changes
2. **Cleaner Code**: No need for `@Published` property wrappers or protocol conformance
3. **Better Type Safety**: Compiler catches more state management issues at compile time
4. **Granular Updates**: SwiftUI can track exactly which properties each view depends on
5. **Modern Swift Integration**: Full support for async/await, actors, and Swift 6 concurrency

**Testing your implementation:**
- Run your app in the Simulator or on a device
- Modify observable properties and verify views update correctly
- Use Xcode's View Debugger to confirm unnecessary view updates are eliminated
- Check the Instruments Time Profiler to measure performance improvements

## Troubleshooting

### Common Issue 1: View Not Updating When Property Changes

**Problem:** You're modifying an `@Observable` object's property, but the SwiftUI view isn't re-rendering.

**Solution:**
- Ensure the property is not marked with `@ObservationIgnored`
- Verify you're using `@State` (not `@StateObject`) for the view that creates the instance
- Check that you're modifying the property on the main thread (use `@MainActor` on the class if needed)
- Confirm you're actually accessing the property in the view's `body` (views only track properties they read)

```swift
// Incorrect - view won't update
@Observable
class ViewModel {
    @ObservationIgnored  // ❌ This prevents tracking
    var count: Int = 0
}

// Correct
@Observable
class ViewModel {
    var count: Int = 0  // ✓ Tracked automatically
}
```

### Common Issue 2: Compiler Error "Type does not conform to Observable"

**Problem:** Getting compilation errors about Observable conformance when passing objects to child views.

**Solution:**
- Make sure you've imported the `Observation` framework
- Verify your deployment target is iOS 17.0+ (or equivalent for other platforms)
- Ensure the `@Observable` macro is applied directly to the class definition
- Check that you're not trying to use `@Observable` with structs (it only works with classes)

```swift
// Incorrect - @Observable only works with classes
@Observable
struct MyModel {  // ❌ Error: structs can't use @Observable
    var name: String
}

// Correct
@Observable
class MyModel {  // ✓ Classes work with @Observable
    var name: String = ""
}
```

### Common Issue 3: Environment Object Not Found

**Problem:** Runtime crash with "Failed to find environment key" when using `@Environment` with an `@Observable` type.

**Solution:**
- Ensure you've injected the object using `.environment()` in a parent view
- Verify you're using `@Environment(YourType.self)` syntax (not the keyPath syntax)
- Check that the type matches exactly (including generics if applicable)
- Consider providing a default value for testing purposes

```swift
// Inject in parent view
ContentView()
    .environment(AppState())  // ✓ Must inject before accessing

// Access in child view
struct ChildView: View {
    @Environment(AppState.self) private var appState  // ✓ Correct syntax

    var body: some View {
        Text(appState.title)
    }
}
```

### Common Issue 4: Observation Not Working in UIKit (iOS 26+)

**Problem:** Using `@Observable` with UIKit's new `updateProperties()` method, but UI doesn't update.

**Solution:**
- Ensure you're targeting iOS 26+ where UIKit observation support was added
- Verify you're calling `updateProperties()` in your view controller
- Make sure you're accessing the observable properties inside the `updateProperties` closure
- Check that your observable object is retained properly (not deallocated)

```swift
// iOS 26+ UIKit integration
class ProfileViewController: UIViewController {
    let viewModel = UserProfileViewModel()
    let nameLabel = UILabel()

    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
        observeViewModel()
    }

    func observeViewModel() {
        updateProperties {
            // Access observable properties here
            nameLabel.text = viewModel.username
        }
    }
}
```

## Additional Tips

- **Start with @Observable for new projects**: If you're starting a new Swift 6 project, use `@Observable` from the beginning rather than `ObservableObject`. The performance and ergonomic benefits are significant.

- **Combine multiple related properties**: Group related state into a single `@Observable` class rather than having many individual `@State` properties. This improves code organization and makes state management more maintainable.

- **Use @MainActor for UI state**: Always mark UI-related observable classes with `@MainActor` to ensure thread safety and prevent concurrency issues when updating from async contexts.

- **Leverage computed properties**: Since all properties are tracked automatically, computed properties that depend on stored properties work seamlessly and trigger updates appropriately.

- **Profile before and after migration**: Use Instruments to measure the performance impact when migrating from `ObservableObject` to `@Observable`. You should see reduced view update counts and improved frame rates.

- **Avoid excessive granularity**: While `@Observable` provides fine-grained updates, don't create too many small observable objects. Balance between granularity and architectural simplicity.

- **Test observation behavior**: Write unit tests that verify your observable objects trigger the expected updates. Use `withObservationTracking` in your tests to validate behavior.

- **Document ignored properties**: When using `@ObservationIgnored`, add comments explaining why a property shouldn't trigger updates to help future maintainers understand your intent.

## Related Articles

- KB-022: How to use @State property wrapper
- KB-023: How to handle button actions and user interactions
- KB-054: How to convert UIKit code to SwiftUI
- KB-053: How to implement MVVM architecture with AI assistance

## Sources

- Migrating from the Observable Object protocol to the Observable macro - https://developer.apple.com/documentation/swiftui/migrating-from-the-observable-object-protocol-to-the-observable-macro (Accessed: November 17, 2025)
- Observable() Macro - Apple Developer Documentation - https://developer.apple.com/documentation/observation/observable() (Accessed: November 17, 2025)
- @Observable Macro performance increase over ObservableObject - https://www.avanderlee.com/swiftui/observable-macro-performance-increase-observableobject/ (Accessed: November 17, 2025)
- @Observable in SwiftUI explained - Donny Wals - https://www.donnywals.com/observable-in-swiftui-explained/ (Accessed: November 17, 2025)
- Using @Observable in SwiftUI views - https://nilcoalescing.com/blog/ObservableInSwiftUI/ (Accessed: November 17, 2025)
- Swift 6.2: Observations - Michael Tsai - https://mjtsai.com/blog/2025/10/31/swift-6-2-observations/ (Accessed: November 17, 2025)
- iOS 26 - UIKit Gets SwiftUI Superpowers: Observable and updateProperties - https://dev.to/arshtechpro/ios-26-uikit-gets-swiftui-superpowers-observable-and-updateproperties-3l26 (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
