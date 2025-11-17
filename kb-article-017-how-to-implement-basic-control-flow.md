# How to implement basic control flow (if/else, switch statements)

**Article ID:** KB-017
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 20-25 minutes

## Overview

This guide teaches you how to implement basic control flow in Swift using if/else statements and switch statements within Xcode 26.1+. Control flow allows your code to make decisions and execute different code paths based on conditions, making your apps dynamic and responsive. You'll learn when to use each type of statement and how Xcode's Coding Intelligence features can help you write cleaner, more efficient conditional logic.

## Prerequisites

Before starting this tutorial, you should have:

- **Xcode 26.1+** installed on your Mac (see KB-001: How to download and install Xcode 26.1+)
- **Basic Swift syntax knowledge** including variables, constants, and data types
- **A Swift Playground or Xcode project** open and ready for experimentation
- **Optional:** Coding Intelligence enabled for AI-assisted code completion (see KB-003 and KB-007)

## Steps

### Step 1: Create a New Swift Playground for Practice

Before implementing control flow, set up a practice environment:

1. Open **Xcode 26.1+**
2. Select **File** > **New** > **Playground**
3. Choose **Blank** template
4. Name it "ControlFlowPractice"
5. Select a save location and click **Create**

```swift
// Your playground is now ready for testing control flow
import Foundation

// We'll add our control flow examples below
```

**Tip:** Swift Playgrounds execute code immediately, making them perfect for learning control flow concepts interactively.

### Step 2: Implement Basic If Statements

The `if` statement is the most fundamental control flow structure. It executes a block of code only when a condition is true.

**Basic If Statement Syntax:**

```swift
let temperature = 75

if temperature > 70 {
    print("It's a warm day!")
}
// Output: It's a warm day!
```

**Key Points:**
- The condition must evaluate to a Boolean value (`true` or `false`)
- Curly braces `{}` define the code block to execute
- Swift doesn't require parentheses around conditions (though they're allowed)
- The code block only runs if the condition is `true`

**Practical Example:**

```swift
let userAge = 18
let hasParentalConsent = true

if userAge >= 18 {
    print("Access granted: User is an adult")
}

if userAge < 18 && hasParentalConsent {
    print("Access granted: User has parental consent")
}
```

**Xcode Intelligence Tip:** As you type `if`, Xcode's code completion will suggest the full statement structure. Press **Tab** to accept and fill in the condition.

### Step 3: Add Else Clauses for Alternative Paths

The `else` clause provides an alternative code path when the `if` condition is false.

**If-Else Syntax:**

```swift
let isLoggedIn = false

if isLoggedIn {
    print("Welcome back!")
} else {
    print("Please log in to continue")
}
// Output: Please log in to continue
```

**Chaining Multiple Conditions with Else If:**

```swift
let score = 85

if score >= 90 {
    print("Grade: A - Excellent work!")
} else if score >= 80 {
    print("Grade: B - Good job!")
} else if score >= 70 {
    print("Grade: C - Satisfactory")
} else if score >= 60 {
    print("Grade: D - Needs improvement")
} else {
    print("Grade: F - Please see instructor")
}
// Output: Grade: B - Good job!
```

**Best Practices:**
- Order your conditions from most specific to least specific
- Always include a final `else` clause when all cases should be handled
- Keep conditions simple and readable

**Real-World Example - Feature Access Control:**

```swift
let subscriptionTier = "premium"
let accountActive = true

if !accountActive {
    print("Account suspended. Please contact support.")
} else if subscriptionTier == "premium" {
    print("Access granted: All premium features unlocked")
} else if subscriptionTier == "standard" {
    print("Access granted: Standard features available")
} else {
    print("Access granted: Free tier features only")
}
```

### Step 4: Use Logical Operators for Complex Conditions

Combine multiple conditions using logical operators:

```swift
let username = "john_doe"
let password = "secure123"
let isBiometricEnabled = false

// AND operator (&&) - Both conditions must be true
if username == "john_doe" && password == "secure123" {
    print("Login successful with credentials")
}

// OR operator (||) - At least one condition must be true
if password == "secure123" || isBiometricEnabled {
    print("Authentication method available")
}

// NOT operator (!) - Inverts the Boolean value
if !username.isEmpty {
    print("Username is provided")
}

// Complex combination
if (username == "john_doe" && password == "secure123") || isBiometricEnabled {
    print("User authenticated successfully")
}
```

**Common Logical Operators:**
- `&&` - Logical AND (both must be true)
- `||` - Logical OR (at least one must be true)
- `!` - Logical NOT (inverts the value)

### Step 5: Understand Switch Statement Basics

Switch statements provide a cleaner way to handle multiple possible values. Swift's switch statements are more powerful than in many other languages.

**Basic Switch Syntax:**

```swift
let dayOfWeek = "Monday"

switch dayOfWeek {
case "Monday":
    print("Start of the work week")
case "Tuesday", "Wednesday", "Thursday":
    print("Middle of the week")
case "Friday":
    print("Almost weekend!")
case "Saturday", "Sunday":
    print("Weekend time!")
default:
    print("Invalid day")
}
// Output: Start of the work week
```

**Key Swift Switch Features:**
- **No implicit fallthrough** - Each case automatically breaks (unlike C/C++)
- **Must be exhaustive** - All possible values must be handled
- **Multiple values per case** - Use commas to match multiple values
- **No break needed** - Cases don't fall through by default

### Step 6: Implement Switch with Different Data Types

Switch statements work with various data types in Swift:

**Switching on Integers:**

```swift
let httpStatusCode = 404

switch httpStatusCode {
case 200:
    print("Success: OK")
case 201:
    print("Success: Created")
case 400:
    print("Client Error: Bad Request")
case 401:
    print("Client Error: Unauthorized")
case 404:
    print("Client Error: Not Found")
case 500:
    print("Server Error: Internal Server Error")
default:
    print("Status code: \(httpStatusCode)")
}
// Output: Client Error: Not Found
```

**Switching on Enums (Most Powerful Use):**

```swift
enum NetworkStatus {
    case connected
    case disconnected
    case connecting
    case error
}

let currentStatus = NetworkStatus.connected

switch currentStatus {
case .connected:
    print("Online: All features available")
case .connecting:
    print("Connecting: Please wait...")
case .disconnected:
    print("Offline: Limited functionality")
case .error:
    print("Connection error: Please try again")
}
// Output: Online: All features available
```

**Note:** When switching on enums, if you handle all cases, the `default` clause is not required.

### Step 7: Use Range Matching in Switch Statements

Swift switch statements support range matching, perfect for numeric ranges:

```swift
let temperature = 68

switch temperature {
case ..<32:
    print("Freezing: Below 32°F")
case 32..<50:
    print("Cold: 32-49°F")
case 50..<70:
    print("Cool: 50-69°F")
case 70..<85:
    print("Comfortable: 70-84°F")
case 85...:
    print("Hot: 85°F and above")
default:
    print("Temperature reading unavailable")
}
// Output: Cool: 50-69°F
```

**Range Operators:**
- `x..<y` - Half-open range (x to y-1)
- `x...y` - Closed range (x to y inclusive)
- `x...` - One-sided range (x and above)
- `..<x` - One-sided range (below x)

**Practical Example - Age-Based Access:**

```swift
let age = 16

switch age {
case 0..<13:
    print("Access: Kids content only")
case 13..<18:
    print("Access: Teen content (parental controls enabled)")
case 18..<21:
    print("Access: Adult content (some restrictions)")
case 21...:
    print("Access: Full unrestricted access")
default:
    print("Invalid age")
}
// Output: Access: Teen content (parental controls enabled)
```

### Step 8: Implement Value Binding in Switch Cases

Swift allows you to bind values within switch cases for more complex pattern matching:

```swift
let coordinate = (x: 3, y: 0)

switch coordinate {
case (0, 0):
    print("At origin point")
case (let x, 0):
    print("On x-axis at position \(x)")
case (0, let y):
    print("On y-axis at position \(y)")
case (let x, let y) where x == y:
    print("On diagonal line at (\(x), \(y))")
case (let x, let y):
    print("At position (\(x), \(y))")
}
// Output: On x-axis at position 3
```

**Advanced Example with Where Clauses:**

```swift
let userScore = 95
let hasBonus = true

switch (userScore, hasBonus) {
case (90...100, true):
    print("Perfect score with bonus: Gold medal! 🏆")
case (90...100, false):
    print("Perfect score: Silver medal!")
case (let score, true) where score >= 80:
    print("Good score with bonus: Bronze medal!")
case (let score, _) where score >= 70:
    print("Passing score: \(score) points")
default:
    print("Keep practicing!")
}
// Output: Perfect score with bonus: Gold medal! 🏆
```

### Step 9: Choose Between If-Else and Switch

Understanding when to use each control structure improves code readability:

**Use If-Else When:**
- Checking Boolean conditions
- Comparing different variables or expressions
- Conditions involve complex logical operations
- You have only 2-3 possible paths

```swift
// Good use of if-else
let hasNetworkConnection = true
let hasValidCredentials = true

if hasNetworkConnection && hasValidCredentials {
    // Proceed with login
} else if !hasNetworkConnection {
    // Show offline message
} else {
    // Show invalid credentials error
}
```

**Use Switch When:**
- Matching against specific values of a single variable
- Working with enums
- Using range matching
- Handling many distinct cases (4+)
- Pattern matching with tuples

```swift
// Good use of switch
enum PaymentMethod {
    case creditCard
    case debitCard
    case paypal
    case applePay
    case cryptocurrency
}

let selectedMethod = PaymentMethod.applePay

switch selectedMethod {
case .creditCard, .debitCard:
    print("Processing card payment...")
case .paypal:
    print("Redirecting to PayPal...")
case .applePay:
    print("Authenticating with Face ID...")
case .cryptocurrency:
    print("Generating wallet address...")
}
```

### Step 10: Test Your Control Flow Implementation

Create a practical example that combines both if-else and switch statements:

```swift
// User authentication and authorization system
struct User {
    let username: String
    let age: Int
    let accountType: String
    let isVerified: Bool
}

let currentUser = User(
    username: "alice_dev",
    age: 25,
    accountType: "premium",
    isVerified: true
)

// Step 1: Check authentication (if-else)
if currentUser.username.isEmpty {
    print("Error: Username required")
} else if !currentUser.isVerified {
    print("Error: Account not verified. Check your email.")
} else {
    print("✓ User authenticated: \(currentUser.username)")

    // Step 2: Check authorization (switch)
    switch currentUser.accountType {
    case "premium":
        if currentUser.age >= 18 {
            print("✓ Access granted: All premium features")
        } else {
            print("✓ Access granted: Premium features with parental controls")
        }
    case "standard":
        print("✓ Access granted: Standard features")
    case "trial":
        print("✓ Access granted: 30-day trial features")
    default:
        print("✓ Access granted: Free tier features")
    }

    // Step 3: Age-based content filtering (switch with ranges)
    switch currentUser.age {
    case 0..<13:
        print("→ Content filter: G-rated only")
    case 13..<17:
        print("→ Content filter: PG-13 and below")
    case 17..<21:
        print("→ Content filter: R-rated allowed")
    case 21...:
        print("→ Content filter: All content available")
    default:
        print("→ Content filter: Unknown age")
    }
}
```

**Expected Output:**
```
✓ User authenticated: alice_dev
✓ Access granted: All premium features
→ Content filter: All content available
```

Run this code in your playground to see control flow in action!

## Expected Results

After completing this tutorial, you should be able to:

- **Write if statements** that execute code based on conditions
- **Use else and else-if clauses** to handle multiple scenarios
- **Combine conditions** using logical operators (`&&`, `||`, `!`)
- **Implement switch statements** for clean value matching
- **Use range matching** in switch cases for numeric comparisons
- **Apply value binding and where clauses** for advanced pattern matching
- **Choose the appropriate control flow structure** for different scenarios

**In Xcode 26.1+, you should see:**
- Code completion suggestions as you type control flow keywords
- Automatic bracket and brace matching
- Syntax highlighting for keywords (`if`, `else`, `switch`, `case`, `default`)
- Coding Intelligence suggestions for improving your conditional logic
- Real-time error detection for non-exhaustive switch statements

## Troubleshooting

### Common Issue 1: Non-Exhaustive Switch Statement

**Problem:** Error: "Switch must be exhaustive"

```swift
let value = 5

switch value {
case 1:
    print("One")
case 2:
    print("Two")
}
// Error: Switch must be exhaustive
```

**Solution:** Add a `default` case or cover all possible values:

```swift
switch value {
case 1:
    print("One")
case 2:
    print("Two")
default:
    print("Other value: \(value)")
}
```

### Common Issue 2: Type Mismatch in Conditions

**Problem:** Error: "Cannot convert value of type 'Int' to expected argument type 'Bool'"

```swift
let count = 5

if count {  // Error: Int is not a Bool
    print("Has items")
}
```

**Solution:** Use a proper Boolean comparison:

```swift
if count > 0 {
    print("Has items")
}
```

### Common Issue 3: Unreachable Code After Return/Break

**Problem:** Warning: "Code after 'return' will never be executed"

```swift
func checkValue(_ num: Int) -> String {
    if num > 0 {
        return "Positive"
        print("This line never runs")  // Warning!
    }
    return "Not positive"
}
```

**Solution:** Remove or relocate code that comes after return statements:

```swift
func checkValue(_ num: Int) -> String {
    if num > 0 {
        print("Checking positive value")  // Put before return
        return "Positive"
    }
    return "Not positive"
}
```

### Common Issue 4: Using Assignment (=) Instead of Comparison (==)

**Problem:** Error: "Cannot assign to value" or unexpected behavior

```swift
let status = "active"

if status = "active" {  // Error: Assignment not allowed
    print("Account is active")
}
```

**Solution:** Use comparison operator `==` not assignment operator `=`:

```swift
if status == "active" {
    print("Account is active")
}
```

### Common Issue 5: Forgot to Handle Optional Values

**Problem:** Using optionals directly in conditions without unwrapping

```swift
let username: String? = "john_doe"

if username == "john_doe" {  // Works but compares optional
    print("Welcome John")
}
```

**Solution:** Unwrap optionals properly using optional binding:

```swift
if let username = username, username == "john_doe" {
    print("Welcome John")
}

// Or use guard for early exit
guard let username = username else {
    print("Username not provided")
    return
}

if username == "john_doe" {
    print("Welcome John")
}
```

## Additional Tips

- **Coding Intelligence:** In Xcode 26.1+, enable Coding Intelligence (GPT-5 or Claude Sonnet 4) for AI-assisted suggestions that can refactor complex if-else chains into more readable switch statements
- **Performance:** Switch statements are generally faster than long if-else chains for value matching
- **Readability:** Use guard statements for early returns to reduce nested if-else blocks
- **Exhaustiveness:** When switching on enums, handle all cases without `default` to catch errors if new cases are added later
- **Pattern Matching:** Explore Swift's advanced pattern matching with tuples, ranges, and where clauses for powerful conditional logic
- **Ternary Operator:** For simple if-else assignments, consider the ternary operator: `let result = condition ? valueIfTrue : valueIfFalse`
- **Testing:** Test all code paths in your control flow structures to ensure proper behavior
- **Documentation:** Add comments to complex conditional logic explaining the business rules

**Xcode Shortcuts:**
- **⌘ + /** - Add documentation comment above functions with control flow
- **⌃ + I** - Re-indent selected code (helpful for nested if statements)
- **⌘ + /** - Comment/uncomment selected code for testing different paths

## Related Articles

- KB-008: How to create your first iOS app project using the App template
- KB-009: How to choose between SwiftUI and UIKit for your project
- KB-011: How to work with Swift variables, constants, and data types (coming soon)
- KB-018: How to implement loops and iterations in Swift (coming soon)
- KB-019: How to handle optional values safely with if-let and guard (coming soon)
- KB-020: How to use enumerations effectively in Swift (coming soon)

## Sources

- Swift Language Guide - Control Flow - https://docs.swift.org/swift-book/LanguageGuide/ControlFlow.html (Accessed: November 17, 2025)
- Apple Developer Documentation - Swift Programming Language - https://developer.apple.com/documentation/swift (Accessed: November 17, 2025)
- Swift.org - The Swift Programming Language - https://docs.swift.org/swift-book/ (Accessed: November 17, 2025)
- Google Search Suggestion: "Swift 6 control flow if else switch statements tutorial"
- Google Search Suggestion: "Xcode 26.1 Swift pattern matching switch cases"

---
*This article is part of the Claude Code Web knowledge base*
