# How to create functions in Swift with parameters and return values

**Article ID:** KB-014
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 20-25 minutes

## Overview

Functions are fundamental building blocks in Swift programming that encapsulate reusable code. This guide demonstrates how to create functions with parameters (input values) and return values (output) in Swift 6.2.1, which is included with Xcode 26.1+. You'll learn basic syntax, parameter variations, return types, and best practices for writing clean, maintainable Swift functions.

## Prerequisites

- Xcode 26.1+ installed with Swift 6.2.1 or later
- Basic understanding of Swift syntax and data types
- An Xcode Playground or Swift project to practice with
- Related: KB-002 (How to verify Xcode installation and check the version number)

## Steps

### Step 1: Understanding Basic Function Syntax

In Swift, functions are defined using the `func` keyword. The basic structure includes the function name, parameters in parentheses, a return type after `->`, and the function body in curly braces.

**Basic function without parameters or return value:**

```swift
func greet() {
    print("Hello, Swift!")
}

// Call the function
greet()  // Output: Hello, Swift!
```

This is the simplest form—no input, no output, just executes code.

### Step 2: Creating Functions with Single Parameters

Add parameters to pass data into your function. Each parameter has a name and a type.

```swift
func greet(name: String) {
    print("Hello, \(name)!")
}

// Call with an argument
greet(name: "Alice")  // Output: Hello, Alice!
```

**Key points:**
- `name` is the parameter name
- `String` is the parameter type
- When calling, you use the parameter name as an argument label

### Step 3: Adding Multiple Parameters

Functions can accept multiple parameters, separated by commas.

```swift
func add(a: Int, b: Int) {
    let sum = a + b
    print("The sum is \(sum)")
}

// Call with multiple arguments
add(a: 5, b: 10)  // Output: The sum is 15
```

Each parameter requires its type specification, and argument labels make function calls more readable.

### Step 4: Creating Functions with Return Values

Use the `->` arrow syntax to specify a return type, then use the `return` keyword to send a value back to the caller.

```swift
func multiply(x: Int, y: Int) -> Int {
    return x * y
}

// Store the returned value
let result = multiply(x: 4, y: 7)
print(result)  // Output: 28
```

**Important:** The return type must match the actual value returned. Swift's type system enforces this at compile time.

### Step 5: Using Argument Labels and Parameter Names

Swift supports two names for parameters: an external argument label (used when calling) and an internal parameter name (used inside the function).

```swift
func calculatePrice(for quantity: Int, at unitPrice: Double) -> Double {
    return Double(quantity) * unitPrice
}

// Call with readable argument labels
let total = calculatePrice(for: 5, at: 19.99)
print(total)  // Output: 99.95
```

**Syntax:** `func name(argumentLabel parameterName: Type) -> ReturnType`

- `for` and `at` are argument labels (external)
- `quantity` and `unitPrice` are parameter names (internal)

### Step 6: Omitting Argument Labels with Underscore

Use an underscore `_` to omit the argument label, making function calls more concise.

```swift
func power(_ base: Int, _ exponent: Int) -> Int {
    var result = 1
    for _ in 0..<exponent {
        result *= base
    }
    return result
}

// Call without argument labels
let value = power(2, 3)  // Output: 8 (2^3)
print(value)
```

This is useful for simple operations where labels don't add clarity.

### Step 7: Implementing Default Parameter Values

Provide default values for parameters using the `=` operator. This makes parameters optional when calling the function.

```swift
func greetUser(name: String, greeting: String = "Hello") {
    print("\(greeting), \(name)!")
}

// Call with and without the optional parameter
greetUser(name: "Bob")                      // Output: Hello, Bob!
greetUser(name: "Carol", greeting: "Hi")    // Output: Hi, Carol!
```

**Best practice:** Place parameters with default values at the end of the parameter list.

### Step 8: Using Variadic Parameters

Variadic parameters accept zero or more values of the same type, denoted by three dots `...` after the type.

```swift
func calculateAverage(_ numbers: Double...) -> Double {
    guard !numbers.isEmpty else { return 0.0 }

    let sum = numbers.reduce(0, +)
    return sum / Double(numbers.count)
}

// Call with varying number of arguments
let avg1 = calculateAverage(10, 20, 30)           // Output: 20.0
let avg2 = calculateAverage(5.5, 10.5, 15.5, 20.5) // Output: 13.0
print(avg1)
print(avg2)
```

**Note:** Inside the function, the variadic parameter is treated as an array.

### Step 9: Returning Multiple Values with Tuples

Return multiple related values by wrapping them in a tuple. You can provide labels for clarity.

```swift
func getMinMax(numbers: [Int]) -> (min: Int, max: Int)? {
    guard !numbers.isEmpty else { return nil }

    var currentMin = numbers[0]
    var currentMax = numbers[0]

    for number in numbers {
        if number < currentMin {
            currentMin = number
        }
        if number > currentMax {
            currentMax = number
        }
    }

    return (min: currentMin, max: currentMax)
}

// Access tuple values with labels
if let result = getMinMax(numbers: [3, 1, 4, 1, 5, 9, 2]) {
    print("Min: \(result.min), Max: \(result.max)")
    // Output: Min: 1, Max: 9
}
```

**Key features:**
- Return type is `(min: Int, max: Int)?` (optional tuple)
- Access values using dot notation: `result.min`, `result.max`
- Can also destructure: `let (minimum, maximum) = result`

### Step 10: Implementing Optional Return Types

Use optional return types when a function might not be able to return a value.

```swift
func findUser(by id: Int, in users: [String: String]) -> String? {
    return users[String(id)]
}

let users = ["1": "Alice", "2": "Bob", "3": "Carol"]

if let user = findUser(by: 2, in: users) {
    print("Found: \(user)")  // Output: Found: Bob
} else {
    print("User not found")
}
```

The `?` after `String` indicates the function may return `nil`.

### Step 11: Using Implicit Returns

In Swift 6.2, functions with a single expression can omit the `return` keyword.

```swift
func square(_ number: Int) -> Int {
    number * number  // Implicit return
}

let squared = square(7)
print(squared)  // Output: 49
```

This works for any single-expression function body, making code more concise.

### Step 12: Combining Techniques - Complete Example

Here's a comprehensive example combining multiple concepts:

```swift
func processOrder(
    _ itemName: String,
    quantity: Int = 1,
    pricePerUnit: Double,
    taxRate: Double = 0.08
) -> (subtotal: Double, tax: Double, total: Double) {
    let subtotal = Double(quantity) * pricePerUnit
    let tax = subtotal * taxRate
    let total = subtotal + tax

    return (subtotal, tax, total)
}

// Call with different parameter combinations
let order1 = processOrder("Laptop", quantity: 2, pricePerUnit: 999.99)
print("Subtotal: $\(order1.subtotal), Tax: $\(order1.tax), Total: $\(order1.total)")
// Output: Subtotal: $1999.98, Tax: $159.9984, Total: $2159.9784

let order2 = processOrder("Mouse", pricePerUnit: 29.99, taxRate: 0.10)
print("Total: $\(order2.total)")
// Output: Total: $32.989
```

This function demonstrates:
- Omitted argument label for `itemName`
- Default parameters for `quantity` and `taxRate`
- Multiple parameters with different types
- Tuple return value with labeled components

## Expected Results

After completing this guide, you should be able to:

✓ **Define functions** using proper Swift syntax with the `func` keyword
✓ **Add parameters** with appropriate types and argument labels
✓ **Specify return types** using the `->` arrow notation
✓ **Return values** using the `return` keyword or implicit returns
✓ **Use advanced features** like default parameters, variadic parameters, and tuple returns
✓ **Write readable code** with clear argument labels and parameter names
✓ **Handle optional returns** when functions may not always produce a value

**Your functions should:**
- Compile without errors in Xcode 26.1+ with Swift 6.2.1
- Display autocomplete suggestions for parameters when calling
- Show function documentation in Quick Help (if you add documentation comments)
- Work in Xcode Playgrounds with immediate output in the results sidebar

## Troubleshooting

### Common Issue 1: "Missing return in a function expected to return"

**Problem:** Compiler error when a function declares a return type but doesn't return a value in all code paths.

**Solution:** Ensure every possible execution path returns a value of the correct type.

```swift
// ❌ Incorrect - missing return for else case
func categorize(age: Int) -> String {
    if age < 18 {
        return "Minor"
    }
    // Error: Missing return
}

// ✅ Correct - all paths return
func categorize(age: Int) -> String {
    if age < 18 {
        return "Minor"
    } else {
        return "Adult"
    }
}
```

### Common Issue 2: "Cannot convert return expression of type 'X' to return type 'Y'"

**Problem:** The returned value type doesn't match the declared return type.

**Solution:** Convert the value to the correct type or change the return type declaration.

```swift
// ❌ Incorrect - returning Int when Double expected
func divide(a: Int, b: Int) -> Double {
    return a / b  // Error: Int / Int produces Int
}

// ✅ Correct - convert to Double
func divide(a: Int, b: Int) -> Double {
    return Double(a) / Double(b)
}
```

### Common Issue 3: "Argument labels do not match any available overloads"

**Problem:** Calling a function with incorrect or missing argument labels.

**Solution:** Use the exact argument labels defined in the function signature.

```swift
func calculateTax(on amount: Double, rate: Double) -> Double {
    return amount * rate
}

// ❌ Incorrect
calculateTax(amount: 100, rate: 0.08)  // Missing 'on' label

// ✅ Correct
calculateTax(on: 100, rate: 0.08)
```

**Tip:** Use Xcode's autocomplete by typing the function name and pressing Tab to fill in the correct argument labels.

### Common Issue 4: Variadic Parameter Confusion

**Problem:** Trying to use multiple variadic parameters or placing them incorrectly.

**Solution:** Only one variadic parameter is allowed per function, and it should typically be the last parameter.

```swift
// ❌ Incorrect - multiple variadic parameters not allowed
func process(_ strings: String..., numbers: Int...) { }

// ✅ Correct - single variadic parameter
func process(_ strings: String...) { }

// ✅ Also correct - variadic at end with other parameters
func process(prefix: String, _ strings: String...) { }
```

## Additional Tips

- **Naming conventions:** Use descriptive function names with verbs (e.g., `calculateTotal`, `fetchUserData`, `validateEmail`). Follow Swift's camelCase convention.

- **Documentation comments:** Add documentation to functions using triple-slash `///` comments for better code maintainability:
  ```swift
  /// Calculates the area of a rectangle.
  /// - Parameters:
  ///   - width: The width of the rectangle
  ///   - height: The height of the rectangle
  /// - Returns: The calculated area as a Double
  func calculateArea(width: Double, height: Double) -> Double {
      return width * height
  }
  ```

- **Function signatures:** Keep function signatures simple. If you have more than 3-4 parameters, consider using a struct or class to group related data.

- **Pure functions:** When possible, write pure functions that don't modify external state—they're easier to test and reason about.

- **Guard statements:** Use `guard` for early exits and input validation:
  ```swift
  func process(value: Int?) -> Int {
      guard let value = value, value > 0 else {
          return 0
      }
      return value * 2
  }
  ```

- **Type inference:** While Swift can infer types, being explicit with return types improves code clarity and compiler error messages.

- **Testing in Playgrounds:** Xcode Playgrounds are perfect for experimenting with functions. Create a new Playground (File > New > Playground) to test these examples interactively.

- **Performance:** For performance-critical code, avoid unnecessary computations in frequently called functions. Consider using `@inlinable` for small functions that should be optimized.

## Related Articles

- KB-002: How to verify Xcode installation and check the version number
- KB-009: How to choose between SwiftUI and UIKit for your project
- KB-015: How to use closures in Swift (when available)
- KB-016: How to work with optionals and optional chaining in Swift (when available)

## Sources

- Swift.org - Official Swift Documentation - https://www.swift.org/documentation/ (Accessed: November 17, 2025)
- Swift.org - Swift 6.2 Released - https://www.swift.org/blog/swift-6.2-released/ (Accessed: November 17, 2025)
- Apple Developer Documentation - Swift Programming Language - https://developer.apple.com/documentation/swift/ (Accessed: November 17, 2025)
- Google Search: "Swift 6.2 functions parameters return values syntax examples" (November 17, 2025)
- General knowledge: Swift function syntax and best practices (stable across Swift 5.x - 6.x versions)

---
*This article is part of the Claude Code Web knowledge base*
