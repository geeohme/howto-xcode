# How to work with basic Swift data types (String, Int, Bool, Double)

**Article ID:** KB-013
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 20-25 minutes

## Overview

This guide introduces you to the four fundamental data types in Swift: String, Int, Bool, and Double. Understanding these basic types is essential for all Swift programming in Xcode 26.1+, whether you're building iOS apps with SwiftUI or UIKit. You'll learn how to declare variables and constants with these types, perform common operations, convert between types, and follow Swift 6.2's best practices for type safety and efficiency.

## Prerequisites

- Xcode 26.1 or later installed
- Basic familiarity with Xcode's interface
- An Xcode project (iOS App or Playground) to practice with
- Understanding of Swift variable declaration (var and let)

## Steps

### Step 1: Understanding and Working with Int (Integers)

Int represents whole numbers without decimal points. Swift's Int type is platform-optimized (32-bit on 32-bit platforms, 64-bit on 64-bit platforms).

**Declaring integers:**

```swift
// Declare a variable integer (can be changed)
var age: Int = 25
var temperature = -5  // Type inference automatically detects Int

// Declare a constant integer (cannot be changed)
let maximumScore: Int = 100
let currentYear = 2025  // Type inferred as Int
```

**Common integer operations:**

```swift
// Basic arithmetic
let sum = 10 + 5          // 15
let difference = 20 - 8    // 12
let product = 6 * 7        // 42
let quotient = 50 / 10     // 5 (integer division)
let remainder = 17 % 5     // 2 (modulo operator)

// Compound assignment operators
var score = 100
score += 25    // score is now 125
score -= 15    // score is now 110
score *= 2     // score is now 220
score /= 10    // score is now 22

// Increment and decrement (common pattern)
var counter = 0
counter += 1   // increment by 1 (counter is now 1)
counter -= 1   // decrement by 1 (counter is now 0)
```

**Integer ranges and bounds:**

```swift
// Check the range of Int on your platform
let minInt = Int.min  // Minimum value (e.g., -9223372036854775808 on 64-bit)
let maxInt = Int.max  // Maximum value (e.g., 9223372036854775807 on 64-bit)

// Specific-sized integers when needed
let smallNumber: Int8 = 127       // 8-bit integer (-128 to 127)
let mediumNumber: Int16 = 32767   // 16-bit integer
let largeNumber: Int64 = 9223372036854775807  // 64-bit integer
```

### Step 2: Understanding and Working with String (Text)

String represents sequences of characters (text). Strings in Swift are Unicode-compliant and support emoji, international characters, and special symbols.

**Declaring strings:**

```swift
// Declare a string variable
var greeting: String = "Hello, World!"
var name = "Alice"  // Type inference detects String

// Declare a string constant
let appName: String = "My App"
let welcomeMessage = "Welcome to Xcode 26"

// Multi-line strings using triple quotes
let multilineText = """
This is a string
that spans multiple
lines in Swift.
"""

// Empty strings
var emptyString1 = ""
var emptyString2 = String()
```

**Common string operations:**

```swift
// String concatenation
let firstName = "John"
let lastName = "Doe"
let fullName = firstName + " " + lastName  // "John Doe"

// String interpolation (preferred method)
let age = 30
let message = "My name is \(firstName) and I am \(age) years old."
// Result: "My name is John and I am 30 years old."

// String length
let text = "Hello"
let length = text.count  // 5

// Check if string is empty
let username = ""
if username.isEmpty {
    print("Username is empty")
}

// Case conversion
let original = "Hello World"
let uppercase = original.uppercased()  // "HELLO WORLD"
let lowercase = original.lowercased()  // "hello world"

// Check if string contains substring
let sentence = "Swift is awesome"
if sentence.contains("Swift") {
    print("Found Swift!")
}

// String comparison
let str1 = "apple"
let str2 = "Apple"
if str1 == str2 {
    print("Strings are equal")
} else {
    print("Strings are different")  // This prints (case-sensitive)
}

// Replace substring
let phrase = "Hello World"
let newPhrase = phrase.replacingOccurrences(of: "World", with: "Swift")
// Result: "Hello Swift"
```

### Step 3: Understanding and Working with Bool (Boolean)

Bool represents logical values with only two possible states: true or false. Booleans are essential for conditional logic and control flow.

**Declaring booleans:**

```swift
// Declare boolean variables
var isLoggedIn: Bool = false
var hasCompletedTutorial = true  // Type inference detects Bool

// Declare boolean constants
let isDarkModeEnabled: Bool = true
let isPremiumUser = false
```

**Common boolean operations:**

```swift
// Logical NOT operator (!)
var isActive = true
let isInactive = !isActive  // false

// Logical AND operator (&&)
let isAdult = true
let hasLicense = true
let canDrive = isAdult && hasLicense  // true (both must be true)

// Logical OR operator (||)
let isWeekend = false
let isHoliday = true
let isDayOff = isWeekend || isHoliday  // true (at least one must be true)

// Combining logical operators
let age = 25
let hasPermission = true
let canAccess = (age >= 18) && hasPermission  // true

// Toggle boolean value
var isMuted = false
isMuted.toggle()  // isMuted is now true
isMuted.toggle()  // isMuted is now false
```

**Using booleans in conditional statements:**

```swift
let temperature = 75
let isSunny = true

if isSunny && temperature > 70 {
    print("Perfect day for the beach!")
}

// Ternary operator with booleans
let score = 85
let result = score >= 60 ? "Pass" : "Fail"  // "Pass"

// Guard statements with booleans
func processData(isValid: Bool) {
    guard isValid else {
        print("Data is not valid")
        return
    }
    print("Processing valid data")
}
```

### Step 4: Understanding and Working with Double (Floating-Point Numbers)

Double represents numbers with decimal points. It's a 64-bit floating-point type with approximately 15 decimal digits of precision. Use Double when you need fractional numbers.

**Declaring doubles:**

```swift
// Declare double variables
var price: Double = 19.99
var temperature = 98.6  // Type inference detects Double for decimals

// Declare double constants
let pi: Double = 3.14159265359
let goldenRatio = 1.618033988749

// Doubles can represent whole numbers too
let wholeNumber: Double = 42.0
```

**Common double operations:**

```swift
// Basic arithmetic with doubles
let a = 10.5
let b = 3.2
let sum = a + b           // 13.7
let difference = a - b    // 7.3
let product = a * b       // 33.6
let quotient = a / b      // 3.28125

// Mixing integers and doubles requires explicit conversion
let intValue = 5
let doubleValue = 2.5
let result = Double(intValue) * doubleValue  // 12.5

// Compound assignment operators
var balance = 100.0
balance += 25.50   // 125.50
balance -= 10.00   // 115.50
balance *= 1.05    // 121.275 (5% increase)
balance /= 2       // 60.6375

// Rounding operations
let value = 3.14159
let rounded = value.rounded()           // 3.0 (rounds to nearest)
let roundedUp = value.rounded(.up)      // 4.0 (ceiling)
let roundedDown = value.rounded(.down)  // 3.0 (floor)

// Comparing doubles
let price1 = 19.99
let price2 = 29.99
if price1 < price2 {
    print("price1 is cheaper")
}
```

**Float vs Double:**

```swift
// Float is 32-bit (lower precision, ~6 decimal digits)
let floatNumber: Float = 3.14159

// Double is 64-bit (higher precision, ~15 decimal digits)
let doubleNumber: Double = 3.14159265359

// Swift defaults to Double for decimal literals
let defaultDecimal = 3.14  // This is a Double, not Float

// Use Double unless you have specific memory constraints
// Double is recommended for most use cases
```

### Step 5: Type Conversion Between Basic Types

Swift is a type-safe language and doesn't automatically convert between types. You must explicitly convert values when needed.

**Converting between numeric types:**

```swift
// Int to Double
let intNumber = 42
let doubleFromInt = Double(intNumber)  // 42.0

// Double to Int (truncates decimal part)
let doubleNumber = 42.99
let intFromDouble = Int(doubleNumber)  // 42 (not rounded, just truncated)

// String to Int
let numberString = "123"
if let intFromString = Int(numberString) {
    print("Converted: \(intFromString)")  // 123
} else {
    print("Conversion failed")
}

// Invalid string to Int returns nil
let invalidString = "abc"
let result = Int(invalidString)  // nil (not a valid number)

// String to Double
let priceString = "19.99"
if let doubleFromString = Double(priceString) {
    print("Price: $\(doubleFromString)")  // Price: $19.99
}

// Int to String
let count = 42
let countString = String(count)  // "42"

// Double to String
let temperature = 98.6
let tempString = String(temperature)  // "98.6"

// String formatting for Double with specific decimal places
let price = 19.99999
let formattedPrice = String(format: "%.2f", price)  // "20.00"

// Bool to String
let isActive = true
let statusString = String(isActive)  // "true"

// String to Bool
let boolString = "true"
if let boolFromString = Bool(boolString) {
    print("Boolean: \(boolFromString)")  // Boolean: true
}
```

**Safe conversion with optional binding:**

```swift
// Best practice: Use optional binding for safe conversions
func processUserInput(_ input: String) {
    // Try to convert string to integer
    if let age = Int(input) {
        print("Valid age: \(age)")

        // Convert to double for calculations
        let ageInDouble = Double(age)
        let futureAge = ageInDouble + 10.5
        print("Future age: \(futureAge)")
    } else {
        print("Invalid number format")
    }
}

processUserInput("25")    // Valid age: 25, Future age: 35.5
processUserInput("abc")   // Invalid number format
```

### Step 6: Type Inference and Type Annotations

Swift can automatically infer types, but you can also explicitly declare them for clarity.

**Understanding type inference:**

```swift
// Swift infers the type based on the value
let age = 25              // Inferred as Int
let name = "Alice"        // Inferred as String
let isActive = true       // Inferred as Bool
let price = 19.99         // Inferred as Double

// No type annotation needed for obvious cases
var score = 0             // Int
var message = "Hello"     // String
```

**When to use explicit type annotations:**

```swift
// When declaring without immediate initialization
var username: String
var itemCount: Int
var isEnabled: Bool

// Later initialization
username = "john_doe"
itemCount = 10
isEnabled = true

// When you want a different type than inferred
let wholeNumber: Double = 42     // Force Double instead of Int
let smallInteger: Int8 = 100     // Specify Int8 instead of default Int

// For clarity in complex scenarios
let taxRate: Double = 0.08       // Explicit annotation for documentation
let maxAttempts: Int = 3         // Makes intent clear

// Empty collections require type annotation
var scores: [Int] = []           // Empty integer array
var settings: [String: Bool] = [:] // Empty dictionary
```

**Checking types at runtime:**

```swift
// Use type(of:) to check the type
let value = 42
print(type(of: value))      // Int

let text = "Hello"
print(type(of: text))       // String

let decimal = 3.14
print(type(of: decimal))    // Double

let flag = true
print(type(of: flag))       // Bool
```

### Step 7: Practical Example - Building a Simple Calculator

Here's a complete example using all four basic data types in a practical scenario:

```swift
// Simple calculator function using all basic types
struct Calculator {
    // Properties using different data types
    var displayValue: Double = 0
    var previousValue: Double = 0
    var operation: String = ""
    var isNewCalculation: Bool = true

    // Perform calculation
    mutating func calculate(num1: Double, num2: Double, operation: String) -> Double {
        var result: Double = 0

        switch operation {
        case "+":
            result = num1 + num2
        case "-":
            result = num1 - num2
        case "*":
            result = num1 * num2
        case "/":
            if num2 != 0 {
                result = num1 / num2
            } else {
                print("Error: Division by zero")
                return 0
            }
        default:
            print("Unknown operation")
            return num1
        }

        return result
    }

    // Format result as string
    func formatResult(_ value: Double) -> String {
        // Check if value is a whole number
        if value.truncatingRemainder(dividingBy: 1) == 0 {
            return String(Int(value))  // Display as integer
        } else {
            return String(format: "%.2f", value)  // Display with 2 decimals
        }
    }
}

// Usage example
var calc = Calculator()

// Perform calculations
let result1 = calc.calculate(num1: 10.5, num2: 5.5, operation: "+")
print("10.5 + 5.5 = \(calc.formatResult(result1))")  // "16.00"

let result2 = calc.calculate(num1: 100, num2: 4, operation: "/")
print("100 / 4 = \(calc.formatResult(result2))")     // "25"

let result3 = calc.calculate(num1: 7.5, num2: 2, operation: "*")
print("7.5 * 2 = \(calc.formatResult(result3))")     // "15.00"

// Using in a SwiftUI view
struct CalculatorView: View {
    @State private var inputValue: String = ""
    @State private var result: Double = 0
    @State private var showingResult: Bool = false

    var body: some View {
        VStack {
            // String input
            TextField("Enter a number", text: $inputValue)
                .textFieldStyle(.roundedBorder)
                .keyboardType(.decimalPad)
                .padding()

            Button("Calculate Double") {
                // String to Double conversion
                if let number = Double(inputValue) {
                    result = number * 2
                    showingResult = true
                }
            }
            .padding()

            // Bool controlling display
            if showingResult {
                // Double displayed as String
                Text("Result: \(result, specifier: "%.2f")")
                    .font(.title)
                    .padding()
            }
        }
    }
}
```

## Expected Results

After completing this guide, you should be able to:

1. **Declare variables and constants** with String, Int, Bool, and Double types
2. **Perform operations** specific to each data type (arithmetic for numbers, concatenation for strings, logical operations for booleans)
3. **Convert between types** safely using type casting and optional binding
4. **Use type inference** effectively while knowing when explicit type annotations are necessary
5. **Build practical code** that combines all four basic data types

**Success indicators:**
- You can create variables of each type without compilation errors
- Type conversions work correctly without crashes
- You understand when to use each data type
- Your code compiles and runs in Xcode 26.1+ with Swift 6.2.1

## Troubleshooting

### Common Issue 1: Type Mismatch Errors

**Problem:** Error: "Cannot convert value of type 'Int' to expected argument type 'Double'"

```swift
let intValue = 5
let doubleValue = 2.5
let result = intValue * doubleValue  // ERROR!
```

**Solution:** Swift requires explicit type conversion for numeric operations between different types.

```swift
let intValue = 5
let doubleValue = 2.5
let result = Double(intValue) * doubleValue  // Correct: 12.5
```

### Common Issue 2: String to Number Conversion Failures

**Problem:** App crashes or unexpected nil values when converting strings to numbers

```swift
let userInput = "abc"
let number = Int(userInput)!  // CRASH! Force unwrapping nil
```

**Solution:** Always use optional binding or nil coalescing for safe conversion.

```swift
// Safe approach with optional binding
let userInput = "abc"
if let number = Int(userInput) {
    print("Valid number: \(number)")
} else {
    print("Invalid input - not a number")
}

// Or use nil coalescing with default value
let safeNumber = Int(userInput) ?? 0  // Uses 0 if conversion fails
```

### Common Issue 3: Integer Division Truncation

**Problem:** Integer division gives unexpected results by truncating decimals

```swift
let result = 7 / 2  // Result is 3, not 3.5!
```

**Solution:** Convert at least one operand to Double for decimal results.

```swift
let result1 = Double(7) / Double(2)  // 3.5
let result2 = 7.0 / 2.0              // 3.5
let result3 = Double(7) / 2          // 3.5 (one conversion is enough)
```

### Common Issue 4: Floating-Point Precision Issues

**Problem:** Decimal calculations don't produce exact results

```swift
let result = 0.1 + 0.2  // 0.30000000000000004 (not exactly 0.3!)
if result == 0.3 {
    print("Equal")  // This won't print!
}
```

**Solution:** Use epsilon comparison for floating-point equality checks, or round results.

```swift
// Approach 1: Round for display
let result = 0.1 + 0.2
let roundedResult = (result * 10).rounded() / 10  // 0.3

// Approach 2: Epsilon comparison
let epsilon = 0.00001
if abs(result - 0.3) < epsilon {
    print("Approximately equal")  // This will print
}

// Approach 3: Use Decimal for financial calculations
import Foundation
let decimal1 = Decimal(string: "0.1")!
let decimal2 = Decimal(string: "0.2")!
let decimalResult = decimal1 + decimal2  // Exactly 0.3
```

### Common Issue 5: Bool Comparison Confusion

**Problem:** Verbose or incorrect boolean comparisons

```swift
// Redundant comparison
if isActive == true {  // Unnecessarily verbose
    print("Active")
}

// Wrong comparison
if isActive = true {  // ERROR: Assignment instead of comparison
    print("Active")
}
```

**Solution:** Use booleans directly in conditions without comparing to true/false.

```swift
// Correct and concise
if isActive {
    print("Active")
}

// For false checks, use negation
if !isActive {
    print("Inactive")
}
```

## Additional Tips

- **Choose the right numeric type**: Use Int for countable quantities (people, items, indices), Double for measurements and calculations (prices, distances, temperatures).

- **String interpolation over concatenation**: Prefer `"Value: \(value)"` over `"Value: " + String(value)` for better readability and performance.

- **Constants by default**: Use `let` (constants) whenever possible and only use `var` (variables) when the value needs to change. This makes code safer and shows intent.

- **Type inference is your friend**: Let Swift infer types in obvious cases to reduce verbosity, but use explicit annotations for clarity when needed.

- **Avoid force unwrapping**: Never use `!` to force unwrap optional conversions without being certain of success. Always handle potential failures with optional binding or nil coalescing.

- **Use descriptive variable names**: `userAge: Int` is clearer than `a: Int`, and `isComplete: Bool` is better than `flag: Bool`.

- **Remember Bool's toggle()**: The `toggle()` method is cleaner than `isEnabled = !isEnabled` for flipping boolean values.

- **Format numbers for display**: Use String formatting (`String(format: "%.2f", value)`) when displaying currency or measurements to users.

- **Playground practice**: Use Xcode Playgrounds to experiment with data types and see results immediately without building a full app.

- **Swift 6.2 improvements**: Swift 6.2 in Xcode 26.1+ includes enhanced type inference and better error messages for type-related issues.

## Related Articles

- KB-009: How to choose between SwiftUI and UIKit for your project
- KB-014: How to create and use Swift arrays and dictionaries (when available)
- KB-015: How to use Swift optionals safely (when available)
- KB-016: How to create Swift functions with parameters and return types (when available)
- KB-020: How to debug type errors in Xcode (when available)

## Sources

- Apple Developer Documentation - Swift String - https://developer.apple.com/documentation/Swift/String (Accessed: November 17, 2025)
- Apple Developer Documentation - Swift Bool - https://developer.apple.com/documentation/Swift/Bool (Accessed: November 17, 2025)
- Apple Developer Documentation - Swift Double - https://developer.apple.com/documentation/swift/double (Accessed: November 17, 2025)
- Apple Developer Documentation - Swift - https://developer.apple.com/documentation/swift/ (Accessed: November 17, 2025)
- Swift.org - Swift 6.2 Released - https://www.swift.org/blog/swift-6.2-released/ (Accessed: November 17, 2025)
- Hacking with Swift - Types of Data - https://www.hackingwithswift.com/read/0/3/types-of-data (Accessed: November 17, 2025)
- Programiz - Swift Data Types (With Examples) - https://www.programiz.com/swift-programming/data-types (Accessed: November 17, 2025)
- GeeksforGeeks - Swift Data Types - https://www.geeksforgeeks.org/swift/swift-data-types/ (Accessed: November 17, 2025)
- Talent500 - Swift Data Types Explained: Int, Float, Double, String & More - https://talent500.com/blog/datatypes-in-swift/ (Accessed: November 17, 2025)

---
*This article is part of the Claude Code Web knowledge base*
