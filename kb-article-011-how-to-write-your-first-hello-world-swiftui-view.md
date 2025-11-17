# How to write your first "Hello World" SwiftUI view

**Article ID:** KB-011
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 10-15 minutes

## Overview

This tutorial walks you through creating your first SwiftUI view—a simple "Hello World" app. You'll learn the fundamental building blocks of SwiftUI, including the View protocol, the ContentView structure, and how to display text on screen. This is the essential first step in learning SwiftUI development and understanding how modern iOS apps are built in Xcode 26.1+.

## Prerequisites

- Xcode 26.1 or later installed on your Mac
- Basic understanding of Swift programming (variables, structs)
- An iOS app project created using the SwiftUI App template
- M1 Mac or later (Apple Silicon required)
- macOS Sequoia 15.5 or later

**Related Prerequisites:**
- KB-002: How to verify Xcode installation and check the version number
- KB-008: How to create your first iOS app project using the App template
- KB-009: How to choose between SwiftUI and UIKit for your project

## Steps

### Step 1: Understand the SwiftUI View Protocol

In SwiftUI, everything you display on screen is a **View**. A view is any type that conforms to the `View` protocol. The View protocol has one required property:

```swift
var body: some View
```

The `body` property defines what content your view displays. It returns `some View`, which means it can return any type that conforms to the View protocol.

**Key Concepts:**
- **Declarative syntax:** You describe *what* you want to display, not *how* to display it
- **Structs, not classes:** SwiftUI views are lightweight structs for better performance
- **Composition:** Complex views are built by combining smaller views

### Step 2: Create Your First ContentView

When you create a new SwiftUI project in Xcode 26.1, you'll find a file called `ContentView.swift`. This is your app's main view. Here's the basic structure:

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        Text("Hello, World!")
    }
}
```

**Breaking down this code:**

1. **`import SwiftUI`** - Imports the SwiftUI framework, giving you access to all SwiftUI components
2. **`struct ContentView: View`** - Declares a new struct named ContentView that conforms to the View protocol
3. **`var body: some View`** - The required computed property that returns the view's content
4. **`Text("Hello, World!")`** - A Text view that displays the string "Hello, World!" on screen

### Step 3: Customize Your Hello World Text

You can customize the Text view with various modifiers. Try adding these to make your "Hello World" more interesting:

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        Text("Hello, World!")
            .font(.largeTitle)
            .fontWeight(.bold)
            .foregroundColor(.blue)
    }
}
```

**Common Text modifiers:**
- `.font(.largeTitle)` - Changes the text size to large title style
- `.fontWeight(.bold)` - Makes the text bold
- `.foregroundColor(.blue)` - Changes the text color to blue
- `.padding()` - Adds spacing around the text
- `.multilineTextAlignment(.center)` - Centers multi-line text

### Step 4: Use Xcode Previews to See Your View

Xcode 26.1+ includes a powerful preview system that lets you see your SwiftUI views in real-time without running the full simulator.

Look for the preview code at the bottom of `ContentView.swift`:

```swift
#Preview {
    ContentView()
}
```

**To use the preview:**
1. Open `ContentView.swift` in Xcode
2. Look for the **Canvas** on the right side of the editor (if hidden, press `Cmd + Option + Enter`)
3. Click the **Resume** button (▶) if the preview isn't showing
4. Your "Hello, World!" text should appear in the preview

**Preview benefits:**
- See changes instantly as you type
- Test different device sizes and orientations
- No need to run the full simulator for quick iterations
- Works with AI Coding Assistant for rapid prototyping

### Step 5: Add Multiple Text Views with VStack

To display multiple text elements, you need a container. The most common is `VStack` (vertical stack):

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            Text("Hello, World!")
                .font(.largeTitle)
                .fontWeight(.bold)

            Text("Welcome to SwiftUI")
                .font(.title2)
                .foregroundColor(.gray)

            Text("Built with Xcode 26.1")
                .font(.caption)
        }
    }
}
```

**Understanding VStack:**
- `VStack` arranges views vertically (top to bottom)
- You can add spacing: `VStack(spacing: 20) { ... }`
- You can align content: `VStack(alignment: .leading) { ... }`
- Alternative containers: `HStack` (horizontal), `ZStack` (overlapping/layered)

### Step 6: Run Your App in the Simulator

To see your Hello World app running on a device:

1. Select a simulator from the device menu (top toolbar): **iPhone 16 Pro**
2. Click the **Play button** (▶) or press `Cmd + R`
3. Wait for the simulator to launch and the app to build
4. Your "Hello, World!" text will appear on the simulated iPhone screen

**Simulator tips:**
- First build may take 1-2 minutes; subsequent builds are faster
- Use `Cmd + K` to toggle the software keyboard
- Use `Cmd + Shift + H` to go to the home screen
- Rotate device with `Cmd + Left/Right Arrow`

### Step 7: Experiment with AI Coding Assistant (Optional)

Xcode 26.1 includes built-in AI coding assistance. You can use it to experiment with your Hello World view:

1. Open the **Coding Assistant** panel (View > Show Coding Assistant, or `Cmd + Shift + A`)
2. Select your Text view code
3. Try these prompts:
   - "Add a background color and rounded corners to this text"
   - "Create a gradient background behind this text"
   - "Add a welcome button below the text"

4. Review the AI's suggested code
5. Accept changes or modify as needed

**AI Assistant benefits for beginners:**
- Learn SwiftUI modifiers through experimentation
- Get instant explanations of unfamiliar code
- Discover new ways to style your views

## Expected Results

After completing these steps, you should have:

✓ A working SwiftUI view that displays "Hello, World!" on screen
✓ Understanding of the View protocol and body property
✓ A ContentView struct that you can modify and customize
✓ Ability to preview your view in Xcode's Canvas
✓ A running app in the iOS Simulator showing your text

**What you should see:**
- In the **Canvas preview:** Your text styled exactly as specified in code
- In the **Simulator:** A full iPhone screen with your "Hello, World!" message
- **Instant updates:** Changes in code immediately reflect in the preview

**Expected code structure:**
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

## Troubleshooting

### Common Issue 1: "Type 'ContentView' does not conform to protocol 'View'"

**Problem:** You see a compiler error stating ContentView doesn't conform to the View protocol.
**Solution:** Ensure your ContentView struct has a `body` property that returns `some View`.

```swift
// ❌ Wrong - missing body property
struct ContentView: View {
    // Error: Type 'ContentView' does not conform to protocol 'View'
}

// ✅ Correct - includes body property
struct ContentView: View {
    var body: some View {
        Text("Hello, World!")
    }
}
```

### Common Issue 2: Canvas/Preview Not Showing

**Problem:** The preview canvas on the right side of Xcode doesn't display your view.
**Solution:** Try these steps in order:

1. **Show the Canvas:** Press `Cmd + Option + Enter` or go to **Editor > Canvas**
2. **Resume the preview:** Click the **Resume** button in the canvas
3. **Rebuild preview:** Press `Cmd + Option + P`
4. **Check for errors:** Fix any red compiler errors in your code
5. **Restart Xcode:** If previews still don't work, quit and reopen Xcode

### Common Issue 3: "Extra argument in call" Error

**Problem:** You get an error when trying to add multiple views without a container.
**Solution:** SwiftUI's `body` property can only return a single view. Wrap multiple views in a container like VStack:

```swift
// ❌ Wrong - can't return multiple views directly
var body: some View {
    Text("Hello")
    Text("World")  // Error: Extra argument in call
}

// ✅ Correct - use VStack to combine views
var body: some View {
    VStack {
        Text("Hello")
        Text("World")
    }
}
```

### Common Issue 4: Preview Shows Old Code

**Problem:** You change your code but the preview still shows the old version.
**Solution:** The preview might need to refresh:

1. Click **Resume** in the preview canvas
2. Press `Cmd + Option + P` to refresh the preview
3. Check that you saved the file (`Cmd + S`)
4. If still stuck, clean the build folder: **Product > Clean Build Folder** (`Cmd + Shift + K`)

## Additional Tips

- **Start simple:** Begin with basic Text views before adding complex modifiers and layouts

- **Use preview shortcuts:** The `#Preview` macro (new in Xcode 26.1) makes previews cleaner and easier to write compared to older `PreviewProvider` syntax

- **Chain modifiers:** You can add multiple modifiers to a single view:
  ```swift
  Text("Hello")
      .font(.title)
      .foregroundColor(.blue)
      .padding()
      .background(.yellow)
      .cornerRadius(10)
  ```
  Modifiers are applied in order from top to bottom

- **Explore built-in views:** Beyond Text, SwiftUI provides Image, Button, Label, and many more views. Start experimenting once you're comfortable with Text

- **Use SF Symbols:** SwiftUI works seamlessly with Apple's SF Symbols icon library:
  ```swift
  Image(systemName: "star.fill")
      .foregroundColor(.yellow)
  ```

- **Learn by modification:** Take the basic "Hello World" and modify one thing at a time to see what each modifier does

- **Leverage Coding Intelligence:** Use Xcode's AI assistant to explain any SwiftUI code you don't understand—just select the code and ask "What does this code do?"

- **Save your experiments:** Create a new SwiftUI view file (File > New > File > SwiftUI View) to experiment without affecting your main ContentView

- **Read compiler errors carefully:** SwiftUI compiler errors can be verbose, but they usually point you to the exact problem. Take time to read and understand them

## Related Articles

- KB-008: How to create your first iOS app project using the App template
- KB-009: How to choose between SwiftUI and UIKit for your project
- KB-021: How to add UI elements to your SwiftUI view (Text, Image, Button)
- KB-022: How to use @State property wrapper for managing view state
- KB-024: How to preview your SwiftUI views using Xcode Previews
- KB-025: How to use the #Playground macro to test code snippets anywhere

## Sources

- Hacking with Swift - Understanding the basic structure of a SwiftUI app - https://www.hackingwithswift.com/books/ios-swiftui/understanding-the-basic-structure-of-a-swiftui-app (Accessed: November 17, 2025)
- Medium - Introduction to SwiftUI in 2025 - https://medium.com/@felix.anderson1504/introduction-to-swiftui-in-2025-226382063070 (Accessed: November 17, 2025)
- Medium - What is ContentView in Swift - https://medium.com/antaeus-ar/what-is-contentview-in-swift-a79845f0a409 (Accessed: November 17, 2025)
- Medium - Mastering SwiftUI: View Protocol - https://medium.com/@viralswift/mastering-swiftui-view-protocol-everything-in-swiftui-is-a-view-fee9791b3b1a (Accessed: November 17, 2025)
- Code With Chris - Getting Started with SwiftUI - https://codewithchris.com/swiftui-tutorial/ (Accessed: November 17, 2025)
- Google Search Suggestion: "SwiftUI View protocol body property tutorial"
- Google Search Suggestion: "Xcode SwiftUI preview #Preview macro 2025"
- General knowledge: SwiftUI fundamentals, View protocol, Text view modifiers, and Xcode preview functionality

---
*This article is part of the Claude Code Web knowledge base*
