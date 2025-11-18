# How to Use the #Playground Macro to Test Code Snippets Anywhere

**Article ID:** KB-025
**Difficulty Level:** Beginner
**Xcode Version:** 26.1+
**Swift Version:** 6.2+
**Last Updated:** November 17, 2025

---

## Overview

The `#Playground` macro is a powerful new feature introduced in Xcode 26 that allows you to run and test code snippets directly within any Swift file in your project. Unlike traditional Swift Playgrounds that require separate `.playground` files, the `#Playground` macro lets you execute code inline and see immediate results in Xcode's Canvas. This makes it easier to experiment with code, test algorithms, debug logic, and preview SwiftUI views without leaving your project files.

### Key Benefits

- **No Separate Files Required**: Test code directly in your project files without creating separate playground files
- **Access to Private Code**: Playground blocks can access private entities within the same file
- **Live Results in Canvas**: See output and previews immediately in Xcode's Canvas
- **Multiple Playgrounds**: Create multiple named playground blocks in a single file
- **SwiftUI Integration**: Preview SwiftUI views without running the full app
- **Quick Experimentation**: Rapidly test ideas and algorithms within your existing codebase

---

## Prerequisites

- **Xcode 26.1 or later** installed on your Mac
- **Swift 6.2 or later** (included with Xcode 26.1+)
- Basic understanding of Swift programming
- A Swift project open in Xcode

---

## Step-by-Step Instructions

### Step 1: Import the Playgrounds Framework

At the top of any Swift file where you want to use the `#Playground` macro, import the Playgrounds framework:

```swift
import Playgrounds
```

**Note**: If you're working in a SwiftUI file, you'll also need to keep your existing imports:

```swift
import SwiftUI
import Playgrounds
```

### Step 2: Create a Basic Playground Block

Add a `#Playground` macro anywhere in your Swift file. The simplest syntax is:

```swift
import Playgrounds

#Playground {
    let message = "Hello, Xcode 26 Playground!"
    print(message)
}
```

**Where to Place It**:
- In SwiftUI projects, place the `#Playground` macro **outside** of View struct declarations
- Can be placed at the file level, after your type definitions
- Multiple playground blocks can exist in the same file

### Step 3: View Results in Canvas

1. Ensure the **Canvas** is visible in Xcode (if not, click the Editor button in the top-right toolbar and select "Canvas")
2. The Canvas will automatically display the output from your playground block
3. Any `print()` statements will appear in the Canvas output area

### Step 4: Create Named Playground Blocks

When using multiple playground blocks in a single file, add names to identify them:

```swift
import Playgrounds

#Playground("Basic Math") {
    let result = 10 + 20
    print("Sum: \(result)")
}

#Playground("Array Operations") {
    let numbers = [1, 2, 3, 4, 5]
    let doubled = numbers.map { $0 * 2 }
    print("Doubled: \(doubled)")
}
```

**Navigation**: Use the tabs at the top of the Canvas to switch between different playground blocks, similar to SwiftUI previews.

### Step 5: Test Functions and Algorithms

The `#Playground` macro can access any functions defined in the same file, including private ones:

```swift
import Foundation
import Playgrounds

private func fibonacci(_ n: Int) -> Int {
    if n <= 1 { return n }
    return fibonacci(n - 1) + fibonacci(n - 2)
}

#Playground("Fibonacci Sequence") {
    print("Fibonacci sequence:")
    for n in 0..<10 {
        print("fibonacci(\(n)) = \(fibonacci(n))")
    }
}
```

### Step 6: Preview SwiftUI Views

The `#Playground` macro supports SwiftUI views, enabling live previews in Canvas:

```swift
import SwiftUI
import Playgrounds

struct DemoView: View {
    var body: some View {
        VStack(spacing: 20) {
            Text("Hello, Playground!")
                .font(.title)

            Button("Tap Me") {
                print("Button tapped")
            }
            .buttonStyle(.borderedProminent)
        }
        .padding()
        .background(.ultraThinMaterial)
        .cornerRadius(12)
    }
}

#Playground("SwiftUI Preview") {
    DemoView()
}
```

**Important**: When working with SwiftUI, the playground block should return or reference the view you want to preview.

### Step 7: Test with Structs and Classes

You can create and test custom types within playground blocks:

```swift
import Playgrounds

struct Person {
    let firstName: String
    let lastName: String

    var fullName: String {
        "\(firstName) \(lastName)"
    }
}

#Playground("Person Test") {
    let person = Person(firstName: "Jane", lastName: "Smith")
    print("Full name: \(person.fullName)")

    let people = [
        Person(firstName: "Alice", lastName: "Johnson"),
        Person(firstName: "Bob", lastName: "Williams")
    ]

    people.forEach { person in
        print(person.fullName)
    }
}
```

### Step 8: Debugging with Print Statements

Use playground blocks to quickly debug and understand data transformations:

```swift
import Playgrounds

#Playground("Data Transformation") {
    let rawData = ["apple", "banana", "cherry", "date"]

    print("Original data: \(rawData)")

    let uppercased = rawData.map { $0.uppercased() }
    print("Uppercased: \(uppercased)")

    let filtered = rawData.filter { $0.count > 5 }
    print("Filtered (length > 5): \(filtered)")

    let sorted = rawData.sorted()
    print("Sorted: \(sorted)")
}
```

---

## Expected Results

When you implement the `#Playground` macro correctly:

1. **Canvas Activation**: The Canvas panel in Xcode will automatically activate and show your playground output
2. **Immediate Execution**: Code runs immediately when you save the file or when Xcode auto-refreshes
3. **Console Output**: All `print()` statements appear in the Canvas output area
4. **SwiftUI Previews**: SwiftUI views render as interactive previews in the Canvas
5. **Multiple Tabs**: Named playground blocks appear as separate tabs in the Canvas
6. **Real-time Updates**: Changes to your playground code update the Canvas in real-time

**Example Output** for the Fibonacci example:
```
Fibonacci sequence:
fibonacci(0) = 0
fibonacci(1) = 1
fibonacci(2) = 1
fibonacci(3) = 2
fibonacci(4) = 3
fibonacci(5) = 5
fibonacci(6) = 8
fibonacci(7) = 13
fibonacci(8) = 21
fibonacci(9) = 34
```

---

## Troubleshooting

### Canvas Not Showing Playground Output

**Problem**: The Canvas is visible but doesn't show playground results.

**Solutions**:
1. Ensure you've imported the `Playgrounds` framework at the top of the file
2. Verify you're using Xcode 26.1 or later
3. Try clicking the refresh button in the Canvas panel
4. In SwiftUI projects, make sure the `#Playground` macro is placed outside of View declarations
5. Check that your playground block has valid Swift code with no syntax errors

### "No such module 'Playgrounds'" Error

**Problem**: Xcode can't find the Playgrounds module.

**Solutions**:
1. Verify you're running Xcode 26.1 or later (not Xcode 15 or 16)
2. Check your Swift version: Go to Xcode → Settings → Locations and ensure Command Line Tools is set to Xcode 26.1+
3. Clean the build folder: Product → Clean Build Folder (Cmd+Shift+K)
4. Restart Xcode

### Playground Block Not Executing

**Problem**: The playground code doesn't run or update.

**Solutions**:
1. Save the file (Cmd+S) to trigger execution
2. Click the "Resume" button in the Canvas if it's paused
3. Check for compilation errors in your code
4. Ensure the Canvas is in "Live" mode, not "Paused"
5. Try closing and reopening the Canvas panel

### SwiftUI Preview Not Rendering

**Problem**: SwiftUI views don't appear in the playground preview.

**Solutions**:
1. Ensure you've imported both `SwiftUI` and `Playgrounds`
2. Verify the playground block returns or references the view
3. Make sure the playground is placed outside the View struct definition
4. Check that your view code is valid and compiles without errors
5. Try using a simpler view first to test the setup

### Multiple Playgrounds Interfering

**Problem**: Multiple playground blocks in the same file cause conflicts or confusion.

**Solutions**:
1. Give each playground block a unique, descriptive name
2. Use the tabs in the Canvas to switch between different playgrounds
3. Comment out playground blocks you're not currently using
4. Consider moving unrelated playgrounds to separate files

### Performance Issues

**Problem**: Playground execution is slow or causes Xcode to lag.

**Solutions**:
1. Avoid infinite loops or very long-running computations
2. Limit the amount of data processing in playground blocks
3. Use smaller datasets for testing
4. Close other intensive apps to free up system resources
5. Consider using traditional debugging tools for performance-critical code

---

## Additional Tips

### Best Practices

1. **Use Descriptive Names**: When creating multiple playgrounds, use clear, descriptive names that indicate what each one tests
   ```swift
   #Playground("Edge Cases - Empty Array") { }
   #Playground("Edge Cases - Negative Numbers") { }
   ```

2. **Keep Playgrounds Focused**: Each playground block should test one specific concept or feature for clarity

3. **Clean Up Before Committing**: Remove or comment out playground blocks before committing code to version control, as they're primarily for development and testing

4. **Leverage Private Access**: Take advantage of the ability to test private functions without making them public

5. **Combine with Unit Tests**: Use playgrounds for quick experimentation, but write proper unit tests for production code validation

### Advanced Techniques

1. **Testing Async Code**:
   ```swift
   import Playgrounds
   import Foundation

   #Playground("Async Operations") {
       Task {
           try await Task.sleep(nanoseconds: 1_000_000_000)
           print("Async operation completed")
       }
   }
   ```

2. **Debugging Data Models**:
   ```swift
   #Playground("Model Validation") {
       let user = User(id: 1, name: "Test User", email: "test@example.com")
       dump(user)  // Use dump() for detailed object inspection
   }
   ```

3. **Testing Extensions**:
   ```swift
   extension String {
       func reversedWords() -> String {
           self.split(separator: " ").reversed().joined(separator: " ")
       }
   }

   #Playground("String Extension Test") {
       let phrase = "Hello World from Xcode"
       print(phrase.reversedWords())
   }
   ```

4. **Quick UI Experiments**:
   ```swift
   #Playground("Color Schemes") {
       VStack(spacing: 20) {
           ForEach(["red", "blue", "green", "purple"], id: \.self) { color in
               Text(color.capitalized)
                   .foregroundColor(Color(color))
                   .padding()
                   .background(Color(color).opacity(0.2))
                   .cornerRadius(8)
           }
       }
       .padding()
   }
   ```

### Workflow Integration

1. **Rapid Prototyping**: Use playgrounds to quickly test new features before implementing them in your main codebase

2. **Algorithm Development**: Develop and test complex algorithms in isolation before integrating them

3. **Learning New APIs**: Experiment with unfamiliar frameworks and APIs in a low-stakes environment

4. **Code Reviews**: Add temporary playgrounds to demonstrate how new code works during code reviews

5. **Documentation**: Use playgrounds as executable examples in your codebase for team members

### Keyboard Shortcuts

- **Show/Hide Canvas**: Option+Cmd+Return
- **Refresh Canvas**: Cmd+Option+P (when Canvas is focused)
- **Run Playground**: Save the file with Cmd+S

---

## Related Articles

- KB-024: How to use Swift Previews for rapid UI development
- KB-026: How to debug SwiftUI views with the Canvas inspector
- KB-015: How to use Xcode's Live Preview feature
- KB-033: How to create and manage Swift Playgrounds in Xcode
- KB-041: How to test Swift code with Quick Look in Xcode

---

## Sources

1. **Apple Developer Documentation** - Running code snippets using the playground macro
   [https://developer.apple.com/documentation/Xcode/running-code-snippets-using-the-playground-macro](https://developer.apple.com/documentation/Xcode/running-code-snippets-using-the-playground-macro)

2. **Apple WWDC25** - What's new in Xcode 26
   [https://developer.apple.com/videos/play/wwdc2025/247/](https://developer.apple.com/videos/play/wwdc2025/247/)

3. **SwiftLee** - #Playground Macro: Running Code Snippets in Xcode's canvas
   [https://www.avanderlee.com/swift/playground-macro-running-code-snippets-in-xcodes-canvas/](https://www.avanderlee.com/swift/playground-macro-running-code-snippets-in-xcodes-canvas/)

4. **Swift Forums** - #Playground macro and "swift play" idea for code exploration in Swift
   [https://forums.swift.org/t/playground-macro-and-swift-play-idea-for-code-exploration-in-swift/79435](https://forums.swift.org/t/playground-macro-and-swift-play-idea-for-code-exploration-in-swift/79435)

5. **Medium (Lokeswaran)** - Xcode 26 Playgrounds
   [https://medium.com/@lokeswaran.dev/xcode-26-playgrounds-6f6367a4a2f3](https://medium.com/@lokeswaran.dev/xcode-26-playgrounds-6f6367a4a2f3)

---

**Document Version:** 1.0
**Created:** November 17, 2025
**Author:** Technical Documentation Team
