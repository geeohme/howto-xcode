# How to create and use Swift variables and constants

**Article ID:** KB-012
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 15-20 minutes

## Overview

This guide teaches you how to create and use variables and constants in Swift 6, the fundamental building blocks for storing and managing data in your iOS applications. You'll learn the difference between variables (declared with `var`) and constants (declared with `let`), when to use each, and best practices for writing clean, efficient Swift code in Xcode 26.1+.

## Prerequisites

Before starting this tutorial, you should have:

- **Xcode 26.1+** installed on your Mac
- **Basic familiarity** with opening Xcode and creating a project
- Understanding of what data is in programming (numbers, text, etc.)

**Related Articles:**
- KB-001: How to download and install Xcode 26.1+ from the Mac App Store
- KB-008: How to create your first iOS app project using the App template
- KB-011: How to write your first "Hello World" SwiftUI view

## Steps

### Step 1: Understand Variables vs Constants

In Swift, you store values in two types of containers:

- **Variables (`var`)**: Values that can change over time
- **Constants (`let`)**: Values that remain fixed once set

**Key Principle:** Swift encourages you to use constants (`let`) whenever possible. This makes your code safer, easier to understand, and allows the compiler to optimize performance.

```swift
// Variable - value can change
var score = 0
score = 10  // ✅ Allowed - variables can be reassigned
score = 25  // ✅ Also allowed

// Constant - value cannot change
let maximumScore = 100
// maximumScore = 200  // ❌ Error: Cannot assign to value: 'maximumScore' is a 'let' constant
```

**When to use `var`:**
- Counters that increment
- User input that changes
- Data that updates over time
- State that needs to be modified

**When to use `let`:**
- Configuration values
- Identifiers that don't change
- Data fetched once and displayed
- Anything that shouldn't be modified

### Step 2: Create Your First Variables and Constants

Open Xcode and create a new Swift Playground or add code to any Swift file:

```swift
// Creating variables
var playerName = "Alex"
var currentLevel = 1
var hasCompletedTutorial = false
var accountBalance = 99.99

// Creating constants
let appName = "My Awesome App"
let maximumPlayers = 4
let isPremiumFeatureEnabled = true
let taxRate = 0.08
```

**What's happening:**
- Swift automatically determines the type of each variable/constant from its initial value
- `playerName` is a `String`
- `currentLevel` is an `Int`
- `hasCompletedTutorial` is a `Bool`
- `accountBalance` is a `Double`

### Step 3: Work with Type Inference

Swift's **type inference** means you don't have to explicitly specify types—Swift figures it out from the value you assign.

```swift
// Swift infers these types automatically
let message = "Hello, World!"  // Inferred as String
let count = 42                 // Inferred as Int
let price = 19.99              // Inferred as Double
let isActive = true            // Inferred as Bool
```

**Benefits of type inference:**
- Less code to write
- Cleaner, more readable syntax
- Still fully type-safe
- Compiler catches type mismatches

### Step 4: Use Explicit Type Annotations

Sometimes you want to specify the type explicitly using **type annotations**. This is useful for clarity, when the initial value might be ambiguous, or when declaring without an initial value.

**Syntax:** `let name: Type = value`

```swift
// Explicit type annotations
let userName: String = "Jordan"
var itemCount: Int = 0
var temperature: Double = 72.5
let isLoggedIn: Bool = false

// Type annotation without initial value
var userInput: String
// userInput must be assigned before use

// Specifying Double instead of Int
let distance: Double = 100  // 100.0, not 100

// Specifying Float instead of Double
let ratio: Float = 0.5
```

**When to use type annotations:**
- API declarations for clarity
- When declaring variables without initial values
- When you need a specific numeric type (Float vs Double, Int vs Int64)
- For better documentation in complex codebases
- When type inference is ambiguous

### Step 5: Modify Variables (Not Constants)

Variables can be changed after creation; constants cannot.

```swift
var lives = 3
print(lives)  // Output: 3

lives = lives - 1
print(lives)  // Output: 2

lives = 5
print(lives)  // Output: 5

// You can also use compound assignment operators
lives += 2   // lives is now 7
lives -= 1   // lives is now 6
lives *= 2   // lives is now 12
lives /= 3   // lives is now 4
```

**Constants are immutable:**

```swift
let companyName = "Acme Corp"
// companyName = "New Corp"  // ❌ Error: Cannot assign to value: 'companyName' is a 'let' constant
```

### Step 6: Follow Swift Naming Conventions

Swift uses **camelCase** for variable and constant names:

```swift
// ✅ Good naming (camelCase)
let firstName = "Taylor"
var itemCount = 10
let maxLoginAttempts = 3
var isUserLoggedIn = false

// ❌ Bad naming (avoid these)
let FirstName = "Taylor"      // Don't use PascalCase for variables
var item_count = 10           // Don't use snake_case
let MAX_LOGIN_ATTEMPTS = 3    // Don't use SCREAMING_SNAKE_CASE
```

**Naming best practices:**
- Use descriptive names: `userAge` not `ua`
- Start with lowercase letter
- Use camelCase for multiple words
- Boolean names often start with `is`, `has`, or `should`
- Make names meaningful and self-documenting

```swift
// ✅ Clear, descriptive names
let userAge = 25
var hasSubscription = true
let shouldShowWelcomeScreen = false
var numberOfItemsInCart = 0

// ❌ Unclear abbreviations
let ua = 25
var sub = true
let swc = false
var n = 0
```

### Step 7: Work with Multiple Variables

You can declare multiple variables or constants on a single line (though it's often clearer to use separate lines):

```swift
// Multiple declarations (same type)
var x = 0, y = 0, z = 0

// Better for readability - separate lines
var x = 0
var y = 0
var z = 0

// Multiple constants with type annotation
let red: Double = 1.0, green: Double = 0.5, blue: Double = 0.0
```

### Step 8: Use Variables and Constants in SwiftUI

Here's how variables and constants work in a real SwiftUI app context:

```swift
import SwiftUI

struct ContentView: View {
    // @State variable - can change and update the UI
    @State private var counter = 0

    // Constant - doesn't change
    let title = "Counter App"
    let buttonColor = Color.blue

    var body: some View {
        VStack(spacing: 20) {
            Text(title)
                .font(.largeTitle)

            Text("Count: \(counter)")
                .font(.title)

            Button("Increment") {
                counter += 1  // Modifying the variable
            }
            .buttonStyle(.borderedProminent)
            .tint(buttonColor)
        }
        .padding()
    }
}
```

**Important:** In SwiftUI, use `@State` for variables that should update the UI when they change. Regular `var` and `let` work differently in the context of SwiftUI views.

### Step 9: Test Your Understanding in a Playground

Create a new playground in Xcode to practice:

1. **Open Xcode**
2. **File** → **New** → **Playground**
3. Choose **Blank** template
4. Try this example:

```swift
import Foundation

// Practice with constants
let appVersion = "1.0.0"
let developerName = "Your Name"

print("App: \(appVersion) by \(developerName)")

// Practice with variables
var score = 0
print("Starting score: \(score)")

score = 10
print("After first level: \(score)")

score += 25
print("After bonus: \(score)")

// Practice with type annotations
var userAge: Int = 25
var userName: String = "Alex"
var accountBalance: Double = 100.50

print("\(userName) is \(userAge) years old with balance: $\(accountBalance)")

// Try to modify a constant (this will cause an error - uncomment to see)
// let maxScore = 100
// maxScore = 200  // ❌ Error!
```

### Step 10: Use AI Coding Assistant for Help

In Xcode 26.1+, you can use the built-in Coding Intelligence to learn more:

1. **Select** any variable or constant in your code
2. **Right-click** → **Explain** (or press **Cmd + Shift + A**)
3. Ask ChatGPT or Claude: "Explain the difference between var and let in Swift"
4. Try: "Generate examples of when to use var vs let"

The AI assistant can help you understand variable scope, type inference, and best practices.

## Expected Results

After completing this tutorial, you should be able to:

✅ **Create variables** using the `var` keyword
✅ **Create constants** using the `let` keyword
✅ **Understand** when to use each type
✅ **Let Swift infer types** automatically
✅ **Use explicit type annotations** when needed
✅ **Follow naming conventions** (camelCase)
✅ **Modify variables** but not constants
✅ **Print values** to see changes
✅ **Apply these concepts** in SwiftUI views

**Example of successful understanding:**

```swift
// You should be comfortable writing code like this:
let gameTitle = "Space Adventure"      // Constant - won't change
var playerHealth: Double = 100.0       // Variable - will change
var currentLevel = 1                   // Type inferred as Int

playerHealth -= 15.5                   // Modify variable
currentLevel += 1                      // Increment level

print("\(gameTitle): Level \(currentLevel), Health: \(playerHealth)")
// Output: Space Adventure: Level 2, Health: 84.5
```

## Troubleshooting

### Common Issue 1: Cannot Assign to Constant

**Problem:** Error message: "Cannot assign to value: 'name' is a 'let' constant"

```swift
let score = 100
score = 200  // ❌ Error!
```

**Solution:**
- If the value needs to change, use `var` instead of `let`
- If it shouldn't change, keep it as `let` and don't try to modify it

```swift
var score = 100  // ✅ Changed to var
score = 200      // ✅ Now it works
```

### Common Issue 2: Type Mismatch

**Problem:** Error message: "Cannot assign value of type 'String' to type 'Int'"

```swift
var age = 25        // Inferred as Int
age = "twenty-five"  // ❌ Error: Can't assign String to Int
```

**Solution:**
- Once a variable's type is set (either by inference or annotation), you cannot change its type
- Create a new variable if you need to store a different type

```swift
var age = 25           // Int
var ageText = "25"     // String - different variable
```

### Common Issue 3: Using Variables Before Declaration

**Problem:** Error message: "Use of unresolved identifier"

```swift
print(userName)  // ❌ Error: userName doesn't exist yet
var userName = "Alex"
```

**Solution:**
- Declare variables before using them
- Swift requires variables to be declared before first use

```swift
var userName = "Alex"
print(userName)  // ✅ Correct order
```

### Common Issue 4: Using Uninitialized Variables

**Problem:** Error message: "Variable 'score' used before being initialized"

```swift
var score: Int
print(score)  // ❌ Error: score has no value yet
```

**Solution:**
- Either provide an initial value when declaring
- Or assign a value before using

```swift
// Option 1: Initialize immediately
var score: Int = 0
print(score)  // ✅ Works

// Option 2: Assign before use
var score: Int
score = 0
print(score)  // ✅ Works
```

### Common Issue 5: Implicit Type Conversion

**Problem:** Swift doesn't automatically convert between numeric types

```swift
let wholeNumber = 42
let decimalNumber = 3.14
let result = wholeNumber + decimalNumber  // ❌ Error: Cannot add Int to Double
```

**Solution:**
- Explicitly convert types when needed

```swift
let wholeNumber = 42
let decimalNumber = 3.14
let result = Double(wholeNumber) + decimalNumber  // ✅ Works: 45.14
```

## Additional Tips

- **Prefer `let` over `var`**: Start with `let` and only change to `var` when you know the value needs to change. Xcode will warn you if you use `var` for something that never changes.

- **Use meaningful names**: Your code should be self-documenting. `numberOfStudents` is better than `n`.

- **Type annotations for API clarity**: When creating functions or properties that others will use, explicit type annotations make your API clearer.

- **Faster builds with type annotations**: In complex expressions, adding type annotations can significantly speed up compile times.

- **SwiftLint integration**: Consider using SwiftLint to enforce consistent variable naming and usage patterns across your codebase.

- **Xcode warnings**: Pay attention to Xcode's warnings. If it suggests changing `var` to `let`, that's good advice!

- **String interpolation**: Use `\(variable)` to insert variables into strings:
  ```swift
  let name = "Taylor"
  let age = 30
  print("Hello, \(name)! You are \(age) years old.")
  ```

- **Constants in global scope**: Constants declared outside of functions are evaluated when first accessed (lazy), which is efficient.

- **Prefer value types**: In Swift, variables and constants work best with value types (structs) rather than reference types (classes) for predictable behavior.

## Related Articles

- KB-013: How to work with basic Swift data types (String, Int, Bool, Double)
- KB-014: How to create functions in Swift with parameters and return values
- KB-015: How to use Swift optionals and safely unwrap values
- KB-016: How to create and use Swift arrays and dictionaries
- KB-019: How to create and use custom Swift structs
- KB-022: How to use @State property wrapper for managing view state
- KB-025: How to use the #Playground macro to test code snippets anywhere

## Sources

- The Swift Programming Language - The Basics - https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html (Accessed: November 17, 2025)
- Hacking with Swift - Variables and Constants Tutorial - https://www.hackingwithswift.com/read/0/2/variables-and-constants (Accessed: November 17, 2025)
- Swift.org Documentation - Type Inference - https://docs.swift.org/swift-book/documentation/the-swift-programming-language/types/ (Accessed: November 17, 2025)
- Medium - Swift Basics: Variables, Constants, and Data Types Explained - https://medium.com/@sagarkaleforapple/swift-basics-variables-constants-and-data-types-explained-eaf5a1aad927 (Accessed: November 17, 2025)
- Microsoft Swift Guide - Type Inference Best Practices - https://microsoft.github.io/swift-guide/TypeInference.html (Accessed: November 17, 2025)
- Stack Overflow - Difference between let and var in Swift - https://stackoverflow.com/questions/24002092/what-is-the-difference-between-let-and-var-in-swift (Accessed: November 17, 2025)

---
*This article is part of the Claude Code Web knowledge base*
