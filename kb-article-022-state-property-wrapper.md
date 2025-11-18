# KB-022: How to Use @State Property Wrapper for Managing View State

**Difficulty Level:** Beginner
**Article ID:** KB-022
**Topic:** Xcode v26.1+ / SwiftUI
**Last Updated:** November 17, 2025
**Xcode Version:** 26.1+
**Swift Version:** 6.0+

---

## Overview

The `@State` property wrapper is one of the fundamental building blocks of SwiftUI's reactive state management system. It allows you to create mutable state within a SwiftUI view, which would otherwise be impossible since SwiftUI views are structs (value types). When a `@State` property changes, SwiftUI automatically invalidates the view and recomputes its body, ensuring your UI stays in sync with your data.

`@State` is specifically designed for managing simple, view-local state that is owned by a single view. It should be used for storing small amounts of value type data such as strings, integers, booleans, and simple structs.

### What You'll Learn

- Understanding what @State is and how it works
- When to use @State vs other property wrappers
- Best practices for declaring and using @State properties
- How SwiftUI manages @State storage
- Common patterns and use cases
- Binding state to child views

---

## Prerequisites

Before you begin, ensure you have:

- **Xcode 26.1 or later** installed
- **Basic understanding of SwiftUI** and view creation
- **Familiarity with Swift structs** and value types
- **Understanding of Swift property wrappers** (helpful but not required)

---

## Understanding @State

### How @State Works

When you declare a property with the `@State` wrapper, SwiftUI:

1. **Moves storage outside the struct**: The property's value is stored in managed memory controlled by SwiftUI, not within your view struct
2. **Watches for changes**: SwiftUI monitors the property for any modifications
3. **Triggers view updates**: When the value changes, SwiftUI automatically invalidates and redraws the view
4. **Synthesizes additional properties**: Creates `$propertyName` (binding) and `_propertyName` (internal storage)

### When to Use @State

Use `@State` for:

- ✅ Simple value types (Int, String, Bool, Double, etc.)
- ✅ Local UI state (toggle switches, text fields, counters)
- ✅ Temporary view state that doesn't need to persist
- ✅ State owned exclusively by a single view
- ✅ Simple structs and enums

Do NOT use `@State` for:

- ❌ Shared data between multiple views (use `@StateObject` or `@ObservedObject`)
- ❌ Complex model objects (use `@StateObject`)
- ❌ Data passed from parent views (use `@Binding`)
- ❌ Large data structures (performance considerations)
- ❌ Data that needs to persist beyond view lifecycle

---

## Step-by-Step Guide

### Step 1: Basic @State Declaration

The most basic use of `@State` is to create a simple property that can be modified within a view.

```swift
import SwiftUI

struct CounterView: View {
    // Declare @State property - always mark as private
    @State private var count = 0

    var body: some View {
        VStack(spacing: 20) {
            Text("Count: \(count)")
                .font(.largeTitle)

            Button("Increment") {
                count += 1  // Modifying state triggers view update
            }
        }
        .padding()
    }
}

#Preview {
    CounterView()
}
```

**Key Points:**
- Always declare `@State` properties as `private` to emphasize they are owned by the view
- Initialize with a default value
- Modifying the property automatically updates the UI

### Step 2: Using @State with Text Input

One of the most common use cases is managing text field input.

```swift
import SwiftUI

struct TextInputView: View {
    @State private var username = ""
    @State private var email = ""

    var body: some View {
        Form {
            Section("User Information") {
                TextField("Username", text: $username)
                TextField("Email", text: $email)
            }

            Section("Preview") {
                if !username.isEmpty {
                    Text("Hello, \(username)!")
                }
                if !email.isEmpty {
                    Text("Your email: \(email)")
                }
            }
        }
    }
}

#Preview {
    TextInputView()
}
```

**Key Points:**
- Use the `$` prefix to create a binding: `$username`
- Bindings allow two-way data flow between the state and the control
- The view automatically updates as the user types

### Step 3: Using @State with Toggle and Picker

Manage boolean states and selections with `@State`.

```swift
import SwiftUI

struct SettingsView: View {
    @State private var isNotificationsEnabled = true
    @State private var selectedTheme = "Light"

    let themes = ["Light", "Dark", "Auto"]

    var body: some View {
        Form {
            Section("Preferences") {
                Toggle("Enable Notifications", isOn: $isNotificationsEnabled)

                Picker("Theme", selection: $selectedTheme) {
                    ForEach(themes, id: \.self) { theme in
                        Text(theme)
                    }
                }
            }

            Section("Current Settings") {
                HStack {
                    Text("Notifications:")
                    Spacer()
                    Text(isNotificationsEnabled ? "On" : "Off")
                        .foregroundStyle(.secondary)
                }

                HStack {
                    Text("Theme:")
                    Spacer()
                    Text(selectedTheme)
                        .foregroundStyle(.secondary)
                }
            }
        }
    }
}

#Preview {
    SettingsView()
}
```

### Step 4: Using @State with Custom Structs

You can use `@State` with custom value types as well.

```swift
import SwiftUI

struct Task: Identifiable {
    let id = UUID()
    var title: String
    var isCompleted: Bool
}

struct TaskListView: View {
    @State private var tasks = [
        Task(title: "Learn SwiftUI", isCompleted: false),
        Task(title: "Build an app", isCompleted: false),
        Task(title: "Deploy to App Store", isCompleted: false)
    ]

    @State private var newTaskTitle = ""

    var body: some View {
        NavigationStack {
            List {
                ForEach($tasks) { $task in
                    HStack {
                        Image(systemName: task.isCompleted ? "checkmark.circle.fill" : "circle")
                            .foregroundStyle(task.isCompleted ? .green : .gray)
                            .onTapGesture {
                                task.isCompleted.toggle()
                            }

                        Text(task.title)
                            .strikethrough(task.isCompleted)
                    }
                }
                .onDelete { indexSet in
                    tasks.remove(atOffsets: indexSet)
                }
            }
            .navigationTitle("Tasks")
            .toolbar {
                ToolbarItem(placement: .bottomBar) {
                    HStack {
                        TextField("New task", text: $newTaskTitle)
                            .textFieldStyle(.roundedBorder)

                        Button("Add") {
                            guard !newTaskTitle.isEmpty else { return }
                            tasks.append(Task(title: newTaskTitle, isCompleted: false))
                            newTaskTitle = ""
                        }
                        .buttonStyle(.borderedProminent)
                    }
                }
            }
        }
    }
}

#Preview {
    TaskListView()
}
```

**Key Points:**
- Use `$tasks` in `ForEach` to get bindings to individual elements
- Each element can be modified directly: `$task.isCompleted.toggle()`
- Arrays of structs work seamlessly with `@State`

### Step 5: Passing State to Child Views with @Binding

When you need to share state with child views, pass a binding.

```swift
import SwiftUI

// Parent View
struct VolumeControlView: View {
    @State private var volume: Double = 50

    var body: some View {
        VStack(spacing: 30) {
            Text("Volume Control")
                .font(.title)

            VolumeSlider(volume: $volume)

            VolumeDisplay(volume: volume)

            Button("Reset") {
                volume = 50
            }
            .buttonStyle(.borderedProminent)
        }
        .padding()
    }
}

// Child View with @Binding
struct VolumeSlider: View {
    @Binding var volume: Double

    var body: some View {
        VStack {
            Slider(value: $volume, in: 0...100)
            Text("Slider Value: \(Int(volume))")
                .font(.caption)
        }
    }
}

// Child View that only reads
struct VolumeDisplay: View {
    let volume: Double

    var body: some View {
        HStack {
            Image(systemName: volumeIcon)
                .font(.title2)

            ProgressView(value: volume, total: 100)
                .tint(volumeColor)
        }
        .padding()
        .background(Color.gray.opacity(0.1))
        .clipShape(RoundedRectangle(cornerRadius: 10))
    }

    private var volumeIcon: String {
        switch volume {
        case 0: "speaker.slash.fill"
        case 1...33: "speaker.wave.1.fill"
        case 34...66: "speaker.wave.2.fill"
        default: "speaker.wave.3.fill"
        }
    }

    private var volumeColor: Color {
        volume > 75 ? .red : .blue
    }
}

#Preview {
    VolumeControlView()
}
```

**Key Points:**
- Parent owns the `@State`
- Pass `$volume` (binding) to children that need to modify it
- Pass `volume` (value) to children that only read it
- Child views use `@Binding` to receive the binding

### Step 6: Multiple Related State Properties

Manage multiple related states in a single view.

```swift
import SwiftUI

struct LoginView: View {
    @State private var username = ""
    @State private var password = ""
    @State private var rememberMe = false
    @State private var showingAlert = false
    @State private var isLoading = false

    var body: some View {
        NavigationStack {
            Form {
                Section("Credentials") {
                    TextField("Username", text: $username)
                        .textContentType(.username)
                        .autocapitalization(.none)

                    SecureField("Password", text: $password)
                        .textContentType(.password)
                }

                Section {
                    Toggle("Remember Me", isOn: $rememberMe)
                }

                Section {
                    Button("Login") {
                        performLogin()
                    }
                    .disabled(!isFormValid)
                    .frame(maxWidth: .infinity)
                }
            }
            .navigationTitle("Login")
            .alert("Login Failed", isPresented: $showingAlert) {
                Button("OK") { }
            } message: {
                Text("Please check your credentials and try again.")
            }
            .overlay {
                if isLoading {
                    ProgressView()
                        .scaleEffect(1.5)
                        .frame(maxWidth: .infinity, maxHeight: .infinity)
                        .background(Color.black.opacity(0.2))
                }
            }
        }
    }

    private var isFormValid: Bool {
        !username.isEmpty && !password.isEmpty && !isLoading
    }

    private func performLogin() {
        isLoading = true

        // Simulate network request
        DispatchQueue.main.asyncAfter(deadline: .now() + 2) {
            isLoading = false
            showingAlert = true
        }
    }
}

#Preview {
    LoginView()
}
```

### Step 7: Using @State with Animations

State changes can be animated automatically.

```swift
import SwiftUI

struct AnimatedShapeView: View {
    @State private var isExpanded = false
    @State private var rotation: Double = 0
    @State private var color: Color = .blue

    var body: some View {
        VStack(spacing: 40) {
            RoundedRectangle(cornerRadius: isExpanded ? 50 : 10)
                .fill(color)
                .frame(
                    width: isExpanded ? 300 : 100,
                    height: isExpanded ? 300 : 100
                )
                .rotationEffect(.degrees(rotation))
                .animation(.spring(response: 0.6, dampingFraction: 0.7), value: isExpanded)
                .animation(.linear(duration: 1), value: rotation)
                .animation(.easeInOut, value: color)

            VStack(spacing: 15) {
                Button("Toggle Size") {
                    isExpanded.toggle()
                }
                .buttonStyle(.borderedProminent)

                Button("Rotate") {
                    rotation += 90
                }
                .buttonStyle(.bordered)

                Button("Change Color") {
                    color = color == .blue ? .purple : .blue
                }
                .buttonStyle(.bordered)
            }
        }
        .padding()
    }
}

#Preview {
    AnimatedShapeView()
}
```

**Key Points:**
- State changes automatically trigger animations when wrapped with `.animation()`
- Different animations can be applied to different state properties
- Use `value:` parameter to specify which state changes trigger the animation

---

## Expected Results

After implementing `@State` correctly, you should see:

1. **Immediate UI Updates**: When state changes, the view updates automatically
2. **No Console Warnings**: No warnings about struct mutability
3. **Proper Data Flow**: Parent state changes reflect in child views with bindings
4. **Smooth Animations**: State-driven animations work seamlessly
5. **Interactive Controls**: Text fields, toggles, and sliders respond correctly

### Testing Your Implementation

Create this test view to verify `@State` is working:

```swift
import SwiftUI

struct StateTestView: View {
    @State private var counter = 0
    @State private var text = "Hello"
    @State private var isOn = false

    var body: some View {
        VStack(spacing: 20) {
            // Counter test
            Text("Counter: \(counter)")
            Button("Increment") { counter += 1 }

            Divider()

            // Text binding test
            TextField("Enter text", text: $text)
                .textFieldStyle(.roundedBorder)
            Text("You typed: \(text)")

            Divider()

            // Toggle test
            Toggle("Switch", isOn: $isOn)
            Text("Toggle is: \(isOn ? "ON" : "OFF")")
        }
        .padding()
    }
}
```

**Expected Behavior:**
- Clicking "Increment" increases the counter
- Typing in the text field updates the text display immediately
- Toggling the switch changes the status text

---

## Troubleshooting

### Issue 1: "Cannot assign to property: 'self' is immutable"

**Problem:** Trying to modify a property without `@State`.

```swift
// ❌ Wrong
struct BrokenView: View {
    var count = 0  // Missing @State

    var body: some View {
        Button("Increment") {
            count += 1  // Error: Cannot assign
        }
    }
}
```

**Solution:** Add `@State` wrapper.

```swift
// ✅ Correct
struct FixedView: View {
    @State private var count = 0

    var body: some View {
        Button("Increment") {
            count += 1  // Works!
        }
    }
}
```

### Issue 2: State Not Updating Child Views

**Problem:** Passing value instead of binding to child.

```swift
// ❌ Wrong
struct ParentView: View {
    @State private var value = 10

    var body: some View {
        ChildView(value: value)  // Passing value, not binding
    }
}

struct ChildView: View {
    @Binding var value: Int

    var body: some View {
        Button("Change") {
            value += 1  // Won't affect parent
        }
    }
}
```

**Solution:** Pass binding with `$` prefix.

```swift
// ✅ Correct
struct ParentView: View {
    @State private var value = 10

    var body: some View {
        ChildView(value: $value)  // Passing binding
    }
}
```

### Issue 3: Using @State for Complex Objects

**Problem:** Using `@State` with reference types or observable objects.

```swift
// ❌ Wrong approach
class UserData {
    var name: String = ""
}

struct WrongView: View {
    @State private var user = UserData()  // Should use @StateObject

    var body: some View {
        TextField("Name", text: $user.name)  // Won't update properly
    }
}
```

**Solution:** Use `@StateObject` for classes that conform to `ObservableObject`.

```swift
// ✅ Correct
@Observable
class UserData {
    var name: String = ""
}

struct CorrectView: View {
    @State private var user = UserData()  // Works with @Observable in Swift 6

    var body: some View {
        TextField("Name", text: $user.name)
    }
}

// Or use @StateObject for ObservableObject
class UserDataLegacy: ObservableObject {
    @Published var name: String = ""
}

struct LegacyView: View {
    @StateObject private var user = UserDataLegacy()

    var body: some View {
        TextField("Name", text: $user.name)
    }
}
```

### Issue 4: State Initialization Issues

**Problem:** Trying to initialize `@State` from another property.

```swift
// ❌ Won't work as expected
struct ConfigView: View {
    let initialValue: Int
    @State private var value = initialValue  // Error: Cannot use instance member

    var body: some View {
        Text("\(value)")
    }
}
```

**Solution:** Use `init` to set initial state value.

```swift
// ✅ Correct
struct ConfigView: View {
    @State private var value: Int

    init(initialValue: Int) {
        _value = State(initialValue: initialValue)
    }

    var body: some View {
        Text("\(value)")
    }
}
```

### Issue 5: Performance with Large State Objects

**Problem:** Storing large amounts of data in `@State` causes performance issues.

**Solution:**
- Use `@State` only for simple, small value types
- For larger data structures, use `@StateObject` with an `ObservableObject`
- Consider splitting large state into smaller, focused pieces
- Use computed properties to derive data instead of storing it

---

## Additional Tips

### Best Practices

1. **Always Use `private`**
   ```swift
   @State private var count = 0  // ✅ Good
   @State var count = 0           // ❌ Avoid
   ```

2. **Initialize with Sensible Defaults**
   ```swift
   @State private var searchText = ""     // ✅ Good
   @State private var isLoading = false   // ✅ Good
   @State private var count = 0           // ✅ Good
   ```

3. **Use Descriptive Names**
   ```swift
   @State private var isShowingAlert = false      // ✅ Good
   @State private var selectedTabIndex = 0        // ✅ Good
   @State private var flag = false                // ❌ Not descriptive
   ```

4. **Group Related State**
   ```swift
   // Instead of multiple scattered @State properties
   struct UserForm {
       var firstName = ""
       var lastName = ""
       var email = ""
   }

   @State private var form = UserForm()
   ```

5. **Don't Overuse @State**
   - Use constants for values that don't change
   - Use computed properties for derived values
   - Use `@Environment` for app-wide settings

### Understanding the Dollar Sign ($)

The `$` prefix creates a `Binding` from your `@State` property:

```swift
@State private var name = "John"

// Three ways to use the property:
name           // String value: "John"
$name          // Binding<String>: Two-way connection
_name          // State<String>: Internal storage (rarely used directly)
```

### Memory Management

- `@State` properties persist for the lifetime of the view
- When a view is destroyed, its `@State` is deallocated
- State is NOT shared between different instances of the same view
- Each view instance has its own independent state

### Swift 6.0 Enhancements

With Swift 6.0 and the `@Observable` macro, you can now use `@State` with observable reference types:

```swift
import SwiftUI
import Observation

@Observable
class GameState {
    var score = 0
    var level = 1
    var playerName = ""
}

struct GameView: View {
    @State private var gameState = GameState()

    var body: some View {
        VStack {
            Text("Player: \(gameState.playerName)")
            Text("Score: \(gameState.score)")
            Text("Level: \(gameState.level)")

            Button("Score Point") {
                gameState.score += 10
            }
        }
    }
}
```

This is a modern alternative to `@StateObject` and `ObservableObject`.

---

## Related Articles

- **KB-023**: How to use @Binding to share state between views
- **KB-024**: How to use @StateObject for managing reference types
- **KB-025**: How to use @ObservedObject for shared data
- **KB-026**: How to use @EnvironmentObject for app-wide state
- **KB-027**: Understanding SwiftUI's state management hierarchy
- **KB-028**: How to use the @Observable macro in Swift 6
- **KB-029**: Best practices for SwiftUI state management
- **KB-030**: How to debug state changes in SwiftUI

---

## Sources

1. **Apple Developer Documentation - State**
   - URL: https://developer.apple.com/documentation/swiftui/state
   - Official Apple documentation for the @State property wrapper

2. **Hacking with Swift - What is the @State property wrapper?**
   - URL: https://www.hackingwithswift.com/quick-start/swiftui/what-is-the-state-property-wrapper
   - Comprehensive tutorial on @State usage and best practices

3. **Kodeco - Best Practices for State Management in SwiftUI**
   - URL: https://www.kodeco.com/books/swiftui-cookbook/v1.0/chapters/8-best-practices-for-state-management-in-swiftui
   - In-depth guide on SwiftUI state management patterns

4. **Swift by Sundell - SwiftUI State Management Guide**
   - URL: https://www.swiftbysundell.com/articles/swiftui-state-management-guide/
   - Comprehensive overview of SwiftUI's state management system

5. **FatBobman - Exploring Key Property Wrappers in SwiftUI**
   - URL: https://fatbobman.com/en/posts/exploring-key-property-wrappers-in-swiftui/
   - Detailed comparison of @State, @Binding, @StateObject, and other wrappers

6. **Medium - Inside SwiftUI @State property wrapper**
   - URL: https://medium.com/@myshkinasasha/inside-swiftui-state-property-wrapper-3f3f1d1d9e33
   - Technical deep-dive into how @State works internally

---

**Document Version:** 1.0
**Created:** November 17, 2025
**Category:** SwiftUI / State Management
**Tags:** #swiftui #state #property-wrapper #state-management #beginner #xcode26 #swift6

---

*This article is part of the Xcode 26.1+ Knowledge Base series. For questions, updates, or corrections, please refer to the repository documentation.*
