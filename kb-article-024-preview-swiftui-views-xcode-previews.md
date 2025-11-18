# How to preview your SwiftUI views using Xcode Previews

**Article ID:** KB-024
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 15-20 minutes

## Overview

Xcode Previews is a powerful real-time design tool that allows you to see your SwiftUI views instantly as you write code, without needing to build and run your app in the simulator. This tutorial covers everything you need to know about using Xcode Previews in Xcode 26.1+, including the modern `#Preview` macro, the `@Previewable` macro for interactive state, preview traits for different configurations, and troubleshooting common issues. Mastering Xcode Previews dramatically accelerates your development workflow and makes SwiftUI development more enjoyable and productive.

## Prerequisites

- Xcode 26.1 or later installed on your Mac
- Basic understanding of SwiftUI views and the View protocol
- An iOS app project with SwiftUI views
- M1 Mac or later (Apple Silicon required)
- macOS Sequoia 15.5 or later

**Related Prerequisites:**
- KB-011: How to write your first "Hello World" SwiftUI view
- KB-008: How to create your first iOS app project using the App template
- KB-009: How to choose between SwiftUI and UIKit for your project

## Steps

### Step 1: Understand Xcode Previews and the #Preview Macro

Xcode Previews provides a live, interactive canvas that displays your SwiftUI views as you code. In Xcode 26.1+, you use the modern `#Preview` macro to create previews.

**Key benefits of Xcode Previews:**
- **Instant feedback:** See your UI changes in real-time without building the app
- **Multiple configurations:** Test different device sizes, orientations, and color schemes simultaneously
- **Interactive mode:** Interact with your views directly in the canvas
- **Faster iteration:** No need to wait for simulator launches or app builds

**The #Preview macro** (introduced in Xcode 15, refined in 26.1+) replaces the older `PreviewProvider` protocol with a cleaner, simpler syntax:

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        Text("Hello, World!")
            .font(.largeTitle)
            .foregroundColor(.blue)
    }
}

#Preview {
    ContentView()
}
```

**Advantages over PreviewProvider:**
- Less boilerplate code (no extra struct needed)
- Clearer, more concise syntax
- Works with SwiftUI, UIKit, and AppKit views
- Better integration with Xcode's preview system

### Step 2: Show the Preview Canvas

The Preview Canvas displays your SwiftUI views in real-time. Here's how to access and control it:

**To show/hide the Canvas:**
1. Open a SwiftUI view file (e.g., `ContentView.swift`)
2. Press `Cmd + Option + Enter` to toggle the Canvas
3. Alternatively, go to **Editor > Canvas** in the menu bar

**Canvas location:**
- The Canvas appears on the **right side** of the Xcode editor
- You can resize it by dragging the divider between the editor and Canvas
- Click and drag the preview device to reposition it within the Canvas

**If the Canvas is blank:**
1. Look for the **Resume** button (▶) in the top-left of the Canvas
2. Click **Resume** to start the preview
3. Xcode will compile your code and display the preview (may take a few seconds)

### Step 3: Create Named Previews for Different Scenarios

You can create multiple previews with descriptive names to test different states or configurations of your view:

```swift
import SwiftUI

struct ProfileView: View {
    var username: String
    var isVerified: Bool

    var body: some View {
        HStack {
            Text(username)
                .font(.headline)
            if isVerified {
                Image(systemName: "checkmark.seal.fill")
                    .foregroundColor(.blue)
            }
        }
        .padding()
    }
}

#Preview("Regular User") {
    ProfileView(username: "John Doe", isVerified: false)
}

#Preview("Verified User") {
    ProfileView(username: "Jane Smith", isVerified: true)
}

#Preview("Long Username") {
    ProfileView(username: "Christopher Alexander Anderson", isVerified: true)
}
```

**Benefits of multiple previews:**
- Test edge cases (empty data, long text, etc.)
- Compare different states side by side
- Document different usage scenarios
- Share examples with your team

### Step 4: Use Preview Traits to Configure Device Settings

Preview traits allow you to configure how your view appears—including device orientation, layout mode, and sizing. Use the `traits` parameter in the `#Preview` macro:

**Orientation Traits:**

```swift
#Preview("Portrait", traits: .portrait) {
    ContentView()
}

#Preview("Landscape", traits: .landscapeLeft) {
    ContentView()
}

#Preview("Upside Down", traits: .portraitUpsideDown) {
    ContentView()
}
```

Available orientation traits:
- `.portrait` - Standard portrait orientation
- `.portraitUpsideDown` - Upside-down portrait
- `.landscapeLeft` - Landscape with home button on left
- `.landscapeRight` - Landscape with home button on right

**Layout Traits:**

```swift
#Preview("Size That Fits", traits: .sizeThatFitsLayout) {
    ContentView()
}

#Preview("Fixed Size", traits: .fixedLayout(width: 300, height: 400)) {
    ContentView()
}
```

Layout trait options:
- `.sizeThatFitsLayout` - Preview without device frame (shows only the view's natural size)
- `.fixedLayout(width:height:)` - Preview at a specific size in points

**Combining Multiple Traits:**

```swift
#Preview("Small Landscape", traits: .fixedLayout(width: 320, height: 200).landscapeLeft) {
    ContentView()
}
```

### Step 5: Use @Previewable for Interactive State and Bindings

The `@Previewable` macro (introduced in Xcode 16, enhanced in 26.1+) allows you to use SwiftUI property wrappers like `@State`, `@Binding`, and `@FocusState` directly in previews, making them interactive and dynamic.

**Basic @Previewable with @State:**

```swift
import SwiftUI

struct CounterView: View {
    @Binding var count: Int

    var body: some View {
        VStack(spacing: 20) {
            Text("Count: \(count)")
                .font(.largeTitle)

            HStack {
                Button("Decrease") {
                    count -= 1
                }
                .buttonStyle(.bordered)

                Button("Increase") {
                    count += 1
                }
                .buttonStyle(.borderedProminent)
            }
        }
        .padding()
    }
}

#Preview {
    @Previewable @State var count = 0
    CounterView(count: $count)
}
```

**How @Previewable works:**
- Declare property wrappers directly in the `#Preview` block
- Mark them with `@Previewable` before the property wrapper (e.g., `@Previewable @State`)
- The preview becomes **interactive**—you can tap buttons, enter text, etc.
- Changes persist during the preview session

**@Previewable with Text Input:**

```swift
struct GreetingView: View {
    @Binding var name: String

    var body: some View {
        VStack {
            TextField("Enter your name", text: $name)
                .textFieldStyle(.roundedBorder)
                .padding()

            Text("Hello, \(name)!")
                .font(.title)
        }
    }
}

#Preview {
    @Previewable @State var name = "World"
    GreetingView(name: $name)
}
```

**@Previewable with FocusState:**

```swift
struct FocusableForm: View {
    @FocusState var isEmailFocused: Bool
    @State var email: String = ""

    var body: some View {
        VStack {
            TextField("Email", text: $email)
                .focused($isEmailFocused)
                .textFieldStyle(.roundedBorder)

            Button("Focus Email Field") {
                isEmailFocused = true
            }
        }
        .padding()
    }
}

#Preview {
    @Previewable @FocusState var isFocused: Bool
    @Previewable @State var email = "user@example.com"

    FocusableForm()
}
```

**Important notes:**
- `@Previewable` only works inside `#Preview` blocks
- You can use it with any SwiftUI property wrapper (`@State`, `@Binding`, `@Environment`, `@Query`, etc.)
- This feature requires Xcode 16 or later (included in 26.1+)

### Step 6: Use Canvas Controls for Interactive Testing

The preview Canvas has controls that let you interact with your previews in different modes:

**Canvas Control Buttons (bottom-left corner):**

1. **Live/Interactive Mode** (default):
   - Your preview is fully interactive
   - You can tap buttons, enter text, scroll, etc.
   - Animations play in real-time
   - Asynchronous code executes
   - **Use case:** Testing user interactions and app behavior

2. **Selectable Mode**:
   - Click the **Select** icon to enter this mode
   - You can select individual views in the preview
   - Selected views are highlighted with blue outlines
   - View inspector shows the selected view's modifiers
   - **Use case:** Inspecting view hierarchy and debugging layout

**Preview Control Buttons (top of Canvas):**

- **Resume button (▶):** Start/restart the preview if paused
- **Pause button (⏸):** Pause preview updates temporarily
- **Variants button (⚙):** Configure color scheme (light/dark), orientation, and dynamic type settings

**Keyboard Shortcuts:**
- `Cmd + Option + P` - Resume/refresh preview
- `Cmd + Option + Enter` - Toggle Canvas visibility
- `Cmd + Option + R` - Run preview on device/simulator

**Testing Dark Mode in Preview:**
1. Click the **Variants button (⚙)** in the Canvas
2. Select **Appearance > Dark** from the dropdown
3. Your preview switches to dark mode instantly

Alternatively, set it programmatically:
```swift
#Preview {
    ContentView()
        .preferredColorScheme(.dark)
}
```

### Step 7: Use PreviewModifier for Reusable Preview Environments

The `PreviewModifier` protocol (Xcode 16+) allows you to create reusable preview configurations that can be shared across multiple previews. This is especially useful for complex environments like Core Data contexts, environment objects, or mock dependencies.

**Creating a PreviewModifier:**

```swift
import SwiftUI

struct MockDataPreviewModifier: PreviewModifier {
    static func makeSharedContext() async throws -> MockDataContext {
        // This is called ONCE and cached across all previews
        let context = MockDataContext()
        await context.loadSampleData()
        return context
    }

    func body(content: Content, context: MockDataContext) -> some View {
        content
            .environment(\.dataContext, context)
            .modelContainer(context.container)
    }
}

// Use the modifier in previews
#Preview(traits: .modifier(MockDataPreviewModifier())) {
    ContentView()
}
```

**Key features:**
- **`makeSharedContext()`:** Called once and cached for all previews using this modifier
- **`body(content:context:)`:** Applies the context to each preview
- **Performance:** Shared context significantly improves preview performance

**Real-world example with Core Data:**

```swift
struct CoreDataPreviewModifier: PreviewModifier {
    static func makeSharedContext() async throws -> ModelContainer {
        let schema = Schema([Item.self])
        let configuration = ModelConfiguration(isStoredInMemoryOnly: true)
        let container = try ModelContainer(for: schema, configurations: [configuration])

        // Populate with sample data
        let context = container.mainContext
        for i in 1...10 {
            let item = Item(name: "Item \(i)", timestamp: Date())
            context.insert(item)
        }

        return container
    }

    func body(content: Content, context: ModelContainer) -> some View {
        content
            .modelContainer(context)
    }
}

#Preview("With Sample Data", traits: .modifier(CoreDataPreviewModifier())) {
    ItemListView()
}
```

### Step 8: Preview Multiple Devices Simultaneously

You can create separate previews for different device configurations to see how your view adapts:

```swift
#Preview("iPhone 16 Pro") {
    ContentView()
}

#Preview("iPhone SE") {
    ContentView()
}

#Preview("iPad Pro") {
    ContentView()
}
```

**For more explicit device configuration:**

```swift
#Preview("iPad Landscape", traits: .landscapeLeft) {
    ContentView()
        .previewDevice(PreviewDevice(rawValue: "iPad Pro (12.9-inch)"))
}

#Preview("iPhone Small", traits: .portrait) {
    ContentView()
        .previewDevice(PreviewDevice(rawValue: "iPhone SE (3rd generation)"))
}
```

**Test accessibility settings:**
```swift
#Preview("Large Text") {
    ContentView()
        .environment(\.dynamicTypeSize, .xxxLarge)
}

#Preview("Extra Large Accessibility") {
    ContentView()
        .environment(\.dynamicTypeSize, .accessibility5)
}
```

### Step 9: Debug Previews with Timeline View

For views with animations or time-based updates, you can use the **Timeline** feature in the Canvas:

1. Run your preview in **Interactive Mode**
2. Look for the **Timeline** button in the Canvas controls
3. Click to expand the timeline
4. Scrub through the timeline to see your view at different points in time

**Example with animation:**
```swift
struct AnimatedView: View {
    @State private var isExpanded = false

    var body: some View {
        VStack {
            Rectangle()
                .fill(.blue)
                .frame(width: isExpanded ? 300 : 100, height: 100)
                .animation(.spring(response: 0.5), value: isExpanded)

            Button("Toggle") {
                isExpanded.toggle()
            }
        }
    }
}

#Preview {
    @Previewable @State var expanded = false
    AnimatedView()
}
```

The timeline lets you:
- Pause animations mid-way
- Step through animation frames
- Debug animation timing issues
- Capture screenshots at specific moments

### Step 10: Preview UIKit and AppKit Views (Advanced)

Unlike the old `PreviewProvider`, the `#Preview` macro works with **UIKit** and **AppKit** views, not just SwiftUI:

**UIKit View Preview:**
```swift
import SwiftUI
import UIKit

#Preview {
    let controller = UIViewController()
    controller.view.backgroundColor = .systemBlue

    let label = UILabel()
    label.text = "Hello from UIKit"
    label.textColor = .white
    label.font = .systemFont(ofSize: 24, weight: .bold)
    label.translatesAutoresizingMaskIntoConstraints = false

    controller.view.addSubview(label)
    NSLayoutConstraint.activate([
        label.centerXAnchor.constraint(equalTo: controller.view.centerXAnchor),
        label.centerYAnchor.constraint(equalTo: controller.view.centerYAnchor)
    ])

    return controller
}
```

**This is valuable for:**
- Legacy UIKit projects transitioning to SwiftUI
- Testing UIKit components in isolation
- Maintaining older codebases with preview support

## Expected Results

After completing these steps, you should have:

✓ Understanding of the `#Preview` macro and how to create previews
✓ Ability to create multiple named previews for different scenarios
✓ Knowledge of preview traits for orientations and layout configurations
✓ Skill using `@Previewable` for interactive state in previews
✓ Familiarity with Canvas controls (interactive vs. selectable mode)
✓ Ability to create reusable `PreviewModifier` environments
✓ Knowledge of previewing multiple devices and configurations
✓ Understanding of debugging animations with timeline view

**What you should see:**
- **In the Canvas:** Your SwiftUI views rendered in real-time
- **Multiple preview cards:** If you created multiple `#Preview` blocks
- **Interactive elements:** Buttons, text fields, and controls that respond to clicks
- **Instant updates:** Code changes reflected immediately in the Canvas

**Example comprehensive preview setup:**
```swift
import SwiftUI

struct TaskRow: View {
    var title: String
    var isCompleted: Bool

    var body: some View {
        HStack {
            Image(systemName: isCompleted ? "checkmark.circle.fill" : "circle")
                .foregroundColor(isCompleted ? .green : .gray)
            Text(title)
                .strikethrough(isCompleted)
            Spacer()
        }
        .padding()
        .background(Color(.systemBackground))
    }
}

#Preview("Completed Task") {
    TaskRow(title: "Write documentation", isCompleted: true)
}

#Preview("Incomplete Task") {
    TaskRow(title: "Review pull request", isCompleted: false)
}

#Preview("Long Title", traits: .fixedLayout(width: 250, height: 60)) {
    TaskRow(title: "This is a very long task title that might wrap to multiple lines", isCompleted: false)
}

#Preview("Dark Mode") {
    TaskRow(title: "Check email", isCompleted: true)
        .preferredColorScheme(.dark)
}
```

## Troubleshooting

### Common Issue 1: "Automatic preview updating paused"

**Problem:** You see "Automatic preview updating paused" in the Canvas, and your preview doesn't update.

**Solution:** Try these steps in order:

1. **Resume the preview:**
   - Press `Cmd + Option + P` (quickest solution)
   - Or click the **Resume** button (▶) in the Canvas

2. **Build your project first:**
   - Press `Cmd + B` to build the project
   - Then resume the preview

3. **Clear build folder:**
   - Go to **Product > Clean Build Folder** (`Cmd + Shift + K`)
   - Wait for it to complete, then try previewing again

4. **Restart Xcode:**
   - Quit Xcode completely
   - Relaunch and reopen your project

### Common Issue 2: "Cannot preview in this file" / Build failures

**Problem:** Preview fails with errors like "Cannot preview in this file" or "Failed to build for preview."

**Solution:**

1. **Check for compilation errors:**
   - Fix any red errors in your code first
   - Previews cannot run if your code doesn't compile

2. **Ensure preview code is correct:**
   ```swift
   // ❌ Wrong - missing return or single expression
   #Preview {
       let view = ContentView()
       view  // Missing return
   }

   // ✅ Correct - single expression automatically returned
   #Preview {
       ContentView()
   }

   // ✅ Also correct - explicit closure with multiple statements
   #Preview {
       let view = ContentView()
       return view
   }
   ```

3. **Check dependencies:**
   - If using external packages, ensure they're properly integrated
   - Previews have limited support for some package types
   - Try moving preview-specific code to `#if DEBUG` blocks

4. **Disable automatic canvas refresh:**
   - Go to **Editor > Canvas** and uncheck **Automatically Refresh Canvas**
   - Manually refresh when needed with `Cmd + Option + P`

### Common Issue 3: Preview shows outdated content

**Problem:** You make code changes but the preview still shows the old UI.

**Solution:**

1. **Manually refresh:**
   - Press `Cmd + Option + P` to force refresh
   - Make sure you saved the file (`Cmd + S`)

2. **Check file is selected:**
   - The preview shows content for the currently selected file
   - Ensure you're viewing the correct file in the editor

3. **Clear derived data:**
   - Go to **Xcode > Settings > Locations**
   - Click the arrow next to **Derived Data** path
   - Delete the derived data folder for your project
   - Restart Xcode and try again

### Common Issue 4: "Timed out waiting for thunk to build"

**Problem:** Preview times out after 30+ seconds with an error about waiting for a "thunk to build."

**Solution:**

1. **Simplify your preview:**
   - Complex view hierarchies can slow preview compilation
   - Extract complex logic into separate methods
   - Use mock data instead of real network calls

2. **Remove expensive operations:**
   ```swift
   // ❌ Bad - expensive in preview
   #Preview {
       ContentView()
           .onAppear {
               // Don't fetch real data in previews
               fetchDataFromAPI()
           }
   }

   // ✅ Good - use mock data
   #Preview {
       ContentView(data: MockData.sampleItems)
   }
   ```

3. **Use PreviewModifier for shared contexts:**
   - Cache expensive setup in `makeSharedContext()`
   - See Step 7 for details

4. **Increase timeout (temporary workaround):**
   - This is a sign of deeper issues
   - Focus on simplifying your preview setup

### Common Issue 5: @Previewable not working

**Problem:** Using `@Previewable` causes errors or doesn't make previews interactive.

**Solution:**

1. **Check Xcode version:**
   - `@Previewable` requires Xcode 16 or later
   - Verify you're using Xcode 26.1+

2. **Correct syntax:**
   ```swift
   // ❌ Wrong - @Previewable must come before @State
   #Preview {
       @State @Previewable var count = 0
       CounterView(count: $count)
   }

   // ✅ Correct - @Previewable before @State
   #Preview {
       @Previewable @State var count = 0
       CounterView(count: $count)
   }
   ```

3. **Only use inside #Preview:**
   - `@Previewable` only works in preview contexts
   - Don't use it in your actual view code

## Additional Tips

- **Use descriptive preview names:** Name your previews clearly so you can quickly identify what each one tests (e.g., "Empty State", "Loading", "Error State")

- **Preview edge cases:** Always create previews for empty states, loading states, error states, and boundary conditions (very long text, many items, etc.)

- **Leverage #Preview for documentation:** Multiple previews serve as living documentation showing how your view should be used

- **Pin frequently used previews:** You can pin specific preview configurations in Xcode to keep them visible while working on other files

- **Combine with AI Coding Assistant:** Use Xcode's AI assistant to generate preview configurations quickly by describing what you want to test

- **Create preview extensions:**
  ```swift
  extension PreviewTrait where T == Preview.ViewTraits {
      static var iPhone16Pro: PreviewTrait<T> {
          .portrait
      }

      static var iPadLandscape: PreviewTrait<T> {
          .landscapeLeft
              .fixedLayout(width: 1024, height: 768)
      }
  }

  #Preview(traits: .iPhone16Pro) {
      ContentView()
  }
  ```

- **Use preview providers for sample data:**
  ```swift
  struct SampleData {
      static let users = [
          User(name: "Alice", age: 28),
          User(name: "Bob", age: 35),
          User(name: "Charlie", age: 42)
      ]
  }

  #Preview {
      UserListView(users: SampleData.users)
  }
  ```

- **Test localization in previews:**
  ```swift
  #Preview("Spanish") {
      ContentView()
          .environment(\.locale, Locale(identifier: "es"))
  }

  #Preview("Arabic (RTL)") {
      ContentView()
          .environment(\.layoutDirection, .rightToLeft)
  }
  ```

- **Performance tip:** If you have many previews, Xcode only builds the visible ones. Scroll through the Canvas to see all previews, or use the preview selector dropdown.

- **Keyboard shortcuts to memorize:**
  - `Cmd + Option + Enter` - Toggle Canvas
  - `Cmd + Option + P` - Resume/refresh preview
  - `Cmd + Option + R` - Run preview on device

- **Canvas size adjustment:** Drag the divider between your code editor and Canvas to give more space to previews when needed

## Related Articles

- KB-011: How to write your first "Hello World" SwiftUI view
- KB-021: How to add UI elements to your SwiftUI view (Text, Image, Button)
- KB-022: How to use @State property wrapper for managing view state
- KB-025: How to use the #Playground macro to test code snippets anywhere
- KB-026: How to use Swift Playgrounds for interactive development and prototyping

## Sources

- Apple Developer Documentation - Previews in Xcode - https://developer.apple.com/documentation/swiftui/previews-in-xcode (Accessed: November 17, 2025)
- Apple Developer Documentation - Preview Macro - https://developer.apple.com/documentation/swiftui/preview(_:traits:body:cameras:) (Accessed: November 17, 2025)
- Apple Developer Documentation - PreviewModifier - https://developer.apple.com/documentation/SwiftUI/PreviewModifier (Accessed: November 17, 2025)
- Apple Developer - WWDC23: Build programmatic UI with Xcode Previews - https://developer.apple.com/videos/play/wwdc2023/10252/ (Accessed: November 17, 2025)
- Apple Developer - WWDC24: What's new in Xcode 16 - https://developer.apple.com/videos/play/wwdc2024/10135/ (Accessed: November 17, 2025)
- SwiftLee - #Preview SwiftUI Views using Macros - https://www.avanderlee.com/xcode/preview-swiftui-uikit-appkit-views/ (Accessed: November 17, 2025)
- SwiftLee - @Previewable: Dynamic SwiftUI Previews Made Easy - https://www.avanderlee.com/swiftui/previewable-macro-usage-in-previews/ (Accessed: November 17, 2025)
- Swift with Majid - Mastering Preview macro in Swift - https://swiftwithmajid.com/2023/10/17/mastering-preview-macro-in-swift/ (Accessed: November 17, 2025)
- Swift with Majid - The power of previews in Xcode - https://swiftwithmajid.com/2024/11/26/the-power-of-previews-in-xcode/ (Accessed: November 17, 2025)
- Hacking with Swift - How to use @State inside SwiftUI previews using @Previewable - https://www.hackingwithswift.com/quick-start/swiftui/how-to-use-state-inside-swiftui-previews-using-previewable (Accessed: November 17, 2025)
- Donny Wals - Using PreviewModifier to build a previewing environment - https://www.donnywals.com/using-previewmodifier-to-build-a-previewing-environment/ (Accessed: November 17, 2025)
- nilcoalescing.com - Preview SwiftUI views with bindings using @Previewable - https://nilcoalescing.com/blog/PreviewSwiftUIViewsWithBindings/ (Accessed: November 17, 2025)
- Swift UI Recipes - Troubleshooting Common SwiftUI Preview Issues - https://swiftuirecipes.com/blog/troubleshooting-common-swiftui-preview-issues (Accessed: November 17, 2025)
- 200OK Solutions - What's New in Xcode 26: WWDC 2025 Highlights - https://200oksolutions.com/blog/whats-new-in-xcode-26-smarter-faster-more-powerful-wwdc-2025-highlights/ (Accessed: November 17, 2025)
- DEV Community - Essential updates in Xcode 26.1.1 with Swift 6.2.1 - https://dev.to/arshtechpro/essential-updates-in-xcode-2611-with-swift-621-2e3i (Accessed: November 17, 2025)
- General knowledge: SwiftUI Previews functionality, #Preview macro, @Previewable macro, and Xcode preview system architecture

---
*This article is part of the Claude Code Web knowledge base*
