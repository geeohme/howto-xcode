# How to create and use custom Swift structs

**Article ID:** KB-019
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 20-25 minutes

## Overview

Structs (structures) are fundamental building blocks in Swift that allow you to create custom data types by grouping related properties and methods together. Unlike simple data types, structs enable you to model real-world concepts with multiple attributes and behaviors. This guide covers creating custom structs, adding properties and methods, understanding value semantics, and leveraging Swift 6.2 struct features in Xcode 26.1+.

## Prerequisites

- Xcode 26.1 or later installed
- Basic understanding of Swift syntax
- Familiarity with variables, constants, and basic data types
- Understanding of functions (helpful but not required)

**Related Prerequisites:**
- KB-012: How to create and use Swift variables and constants
- KB-013: How to work with basic Swift data types
- KB-014: How to create functions in Swift with parameters and return values

## Steps

### Step 1: Understanding What Structs Are

Structs are value types that let you define custom data structures with their own properties (data) and methods (functionality). They're perfect for modeling things like coordinates, user profiles, or game characters.

**Key characteristics of structs:**
- **Value types**: When assigned or passed, structs are copied (not referenced)
- **Automatic initializers**: Swift provides a memberwise initializer automatically
- **Mutability control**: Use `var` for mutable instances, `let` for immutable ones
- **No inheritance**: Unlike classes, structs cannot inherit from other types

### Step 2: Creating Your First Struct

Let's start with a simple struct definition. The basic syntax uses the `struct` keyword followed by the name and a body containing properties.

```swift
// Basic struct with two properties
struct Person {
    var name: String
    var age: Int
}

// Create an instance using the automatic memberwise initializer
var person1 = Person(name: "Alice", age: 28)
var person2 = Person(name: "Bob", age: 32)

// Access properties using dot notation
print(person1.name)  // Output: Alice
print(person2.age)   // Output: 32

// Modify properties (only works because person1 is var)
person1.age = 29
print(person1.age)   // Output: 29
```

**Important**: Swift automatically creates an initializer that accepts parameters for all properties. You don't need to write it yourself unless you want custom initialization logic.

### Step 3: Adding Computed Properties

Beyond stored properties, structs can have computed properties that calculate values on-the-fly rather than storing them.

```swift
struct Rectangle {
    var width: Double
    var height: Double

    // Computed property - calculates area dynamically
    var area: Double {
        return width * height
    }

    // Computed property with getter and setter
    var perimeter: Double {
        get {
            return 2 * (width + height)
        }
        set {
            // Set width and height equally to achieve new perimeter
            let side = newValue / 4
            width = side
            height = side
        }
    }
}

let rect = Rectangle(width: 10, height: 5)
print(rect.area)       // Output: 50.0
print(rect.perimeter)  // Output: 30.0
```

**Computed properties:**
- Don't store values directly - they're calculated when accessed
- Must specify a type explicitly
- Can have getters only (read-only) or both getters and setters

### Step 4: Adding Methods to Structs

Methods are functions that belong to a struct and can operate on its properties.

```swift
struct BankAccount {
    var accountHolder: String
    var balance: Double

    // Method that reads properties
    func displayBalance() {
        print("\(accountHolder) has $\(balance)")
    }

    // Method that checks a condition
    func canWithdraw(amount: Double) -> Bool {
        return amount <= balance
    }
}

let account = BankAccount(accountHolder: "Sarah", balance: 1000.0)
account.displayBalance()  // Output: Sarah has $1000.0

if account.canWithdraw(amount: 500) {
    print("Withdrawal approved")
}
```

### Step 5: Using Mutating Methods

By default, methods cannot modify struct properties because structs are value types. To change properties within a method, mark it as `mutating`.

```swift
struct Counter {
    var count: Int = 0

    // Mutating method can modify properties
    mutating func increment() {
        count += 1
    }

    mutating func increment(by amount: Int) {
        count += amount
    }

    mutating func reset() {
        count = 0
    }
}

var gameScore = Counter()
gameScore.increment()           // count is now 1
gameScore.increment(by: 5)      // count is now 6
print(gameScore.count)          // Output: 6
gameScore.reset()               // count is now 0
```

**Critical rule**: You cannot call mutating methods on struct instances declared with `let`:

```swift
let fixedCounter = Counter()
// fixedCounter.increment()  // Error! Cannot use mutating method on immutable value
```

### Step 6: Creating Custom Initializers

While Swift provides automatic memberwise initializers, you can create custom ones for more complex initialization logic.

```swift
struct Temperature {
    var celsius: Double

    // Custom initializer from Celsius
    init(celsius: Double) {
        self.celsius = celsius
    }

    // Custom initializer from Fahrenheit
    init(fahrenheit: Double) {
        self.celsius = (fahrenheit - 32) / 1.8
    }

    // Custom initializer from Kelvin
    init(kelvin: Double) {
        self.celsius = kelvin - 273.15
    }
}

let temp1 = Temperature(celsius: 25.0)
let temp2 = Temperature(fahrenheit: 77.0)
let temp3 = Temperature(kelvin: 298.15)

print(temp1.celsius)  // Output: 25.0
print(temp2.celsius)  // Output: 25.0 (approximately)
print(temp3.celsius)  // Output: 25.0 (approximately)
```

**Note**: When you provide a custom initializer, Swift no longer provides the automatic memberwise initializer unless you define initializers in an extension.

### Step 7: Understanding Value Semantics

Structs are value types, meaning they're copied when assigned to variables or passed to functions. This is different from classes (reference types).

```swift
struct Point {
    var x: Int
    var y: Int
}

var point1 = Point(x: 10, y: 20)
var point2 = point1  // Creates a copy

point2.x = 30  // Modifies only point2

print(point1.x)  // Output: 10 (unchanged)
print(point2.x)  // Output: 30 (changed)
```

This copy-on-assignment behavior prevents unexpected side effects and makes code easier to reason about.

### Step 8: Working with Nested Structs

Structs can contain other structs, allowing you to build complex data models.

```swift
struct Address {
    var street: String
    var city: String
    var zipCode: String
}

struct Employee {
    var name: String
    var employeeID: Int
    var address: Address

    func displayInfo() {
        print("\(name) (ID: \(employeeID))")
        print("\(address.street), \(address.city) \(address.zipCode)")
    }
}

let workAddress = Address(
    street: "123 Main St",
    city: "San Francisco",
    zipCode: "94102"
)

let employee = Employee(
    name: "Jordan Lee",
    employeeID: 12345,
    address: workAddress
)

employee.displayInfo()
// Output:
// Jordan Lee (ID: 12345)
// 123 Main St, San Francisco 94102
```

### Step 9: Using Static Properties and Methods

Static properties and methods belong to the type itself, not to instances of the type.

```swift
struct MathHelper {
    static let pi = 3.14159

    static func degreesToRadians(_ degrees: Double) -> Double {
        return degrees * pi / 180
    }

    static func radiansToDegrees(_ radians: Double) -> Double {
        return radians * 180 / pi
    }
}

// Access static members without creating an instance
print(MathHelper.pi)                           // Output: 3.14159
let radians = MathHelper.degreesToRadians(90)  // Output: 1.5708
print(radians)
```

### Step 10: Leveraging Swift 6.2 InlineArray Feature (Advanced)

Swift 6.2 introduces `InlineArray` for structs, allowing fixed-size arrays with inline storage for better memory efficiency.

```swift
// New in Swift 6.2: InlineArray with fixed size
struct GameBoard {
    var cells: [9 of Int]  // Fixed-size array of 9 integers

    init(defaultValue: Int) {
        cells = .init(repeating: defaultValue)
    }

    mutating func setCell(at index: Int, value: Int) {
        cells[index] = value
    }
}

var board = GameBoard(defaultValue: 0)
board.setCell(at: 4, value: 1)  // Set center cell
```

**Benefits of InlineArray:**
- Fixed-size arrays stored directly in the struct (no heap allocation)
- Improved performance for small, fixed collections
- Type-safe size enforcement at compile time

### Step 11: Property Observers in Structs

Add observers to track when property values change.

```swift
struct StepCounter {
    var totalSteps: Int = 0 {
        willSet {
            print("About to set totalSteps to \(newValue)")
        }
        didSet {
            if totalSteps > oldValue {
                print("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}

var steps = StepCounter()
steps.totalSteps = 100
// Output: About to set totalSteps to 100
// Output: Added 100 steps
steps.totalSteps = 150
// Output: About to set totalSteps to 150
// Output: Added 50 steps
```

## Expected Results

After completing these steps, you should be able to:

- Define custom structs with stored and computed properties
- Create instances using memberwise and custom initializers
- Add methods and mutating methods to structs
- Understand value semantics and how struct copying works
- Build complex data models using nested structs
- Use static properties and methods for type-level functionality
- Implement property observers to track changes
- Leverage Swift 6.2 features like InlineArray for advanced use cases

**Sample Complete Struct:**

```swift
struct Product {
    // Stored properties
    var name: String
    var price: Double
    var quantity: Int

    // Computed property
    var totalValue: Double {
        return price * Double(quantity)
    }

    // Static property
    static var taxRate: Double = 0.08

    // Initializer
    init(name: String, price: Double, quantity: Int) {
        self.name = name
        self.price = price
        self.quantity = quantity
    }

    // Instance method
    func displayInfo() {
        print("\(name): $\(price) x \(quantity) = $\(totalValue)")
    }

    // Mutating method
    mutating func restock(additional: Int) {
        quantity += additional
    }

    // Static method
    static func calculateTax(on amount: Double) -> Double {
        return amount * taxRate
    }
}

var laptop = Product(name: "MacBook Pro", price: 2499.99, quantity: 5)
laptop.displayInfo()  // MacBook Pro: $2499.99 x 5 = $12499.95
laptop.restock(additional: 3)
print(laptop.quantity)  // Output: 8

let tax = Product.calculateTax(on: laptop.totalValue)
print("Tax: $\(tax)")  // Tax: $999.996
```

## Troubleshooting

### Common Issue 1: Cannot Modify Properties in Methods
**Problem:** "Cannot assign to property: 'self' is immutable" error when trying to change a property in a method
**Solution:** Mark the method as `mutating` to allow it to modify struct properties.

```swift
// Wrong
struct Counter {
    var count = 0
    func increment() {  // Error: cannot modify count
        count += 1
    }
}

// Correct
struct Counter {
    var count = 0
    mutating func increment() {
        count += 1
    }
}
```

### Common Issue 2: Mutating Methods on Immutable Instances
**Problem:** "Cannot use mutating member on immutable value" when calling a mutating method on a let constant
**Solution:** Declare the instance with `var` instead of `let` if you need to call mutating methods.

```swift
let counter = Counter()  // Immutable
// counter.increment()  // Error!

var counter = Counter()  // Mutable
counter.increment()      // Works!
```

### Common Issue 3: Lost Automatic Memberwise Initializer
**Problem:** After adding a custom initializer, the automatic memberwise initializer no longer works
**Solution:** Define custom initializers in an extension to keep the memberwise initializer, or explicitly define both.

```swift
struct Person {
    var name: String
    var age: Int

    // This removes the automatic memberwise initializer
    init(name: String) {
        self.name = name
        self.age = 0
    }
}

// Better approach: use extension
struct Person {
    var name: String
    var age: Int
}

extension Person {
    init(name: String) {
        self.name = name
        self.age = 0
    }
}

// Now both work:
let person1 = Person(name: "Alice", age: 30)  // Memberwise
let person2 = Person(name: "Bob")             // Custom
```

### Common Issue 4: Unexpected Copying Behavior
**Problem:** Modifying a struct doesn't affect other references to it
**Solution:** This is expected behavior for value types. If you need shared references, consider using a class instead, or be mindful of when copies are made.

```swift
var original = Point(x: 10, y: 20)
var copy = original  // Creates a copy
copy.x = 30
// original.x is still 10, not 30
```

## Additional Tips

- **Prefer structs over classes**: Unless you specifically need reference semantics or inheritance, use structs. They're safer and more efficient in many cases.
- **Use descriptive names**: Struct names should be nouns and use UpperCamelCase (e.g., `BankAccount`, not `bankAccount`).
- **Keep structs focused**: Each struct should represent a single, cohesive concept. If your struct is getting too large, consider breaking it into smaller structs.
- **Leverage value semantics**: Structs make it easier to write predictable code since changes don't affect copies.
- **Protocol conformance**: Structs can conform to protocols, making them very flexible for abstraction.
- **Memory efficiency**: For small structs, Swift's copy-on-write optimization means copies are very cheap until modified.
- **Swift 6.2 concurrency**: Structs are inherently thread-safe when immutable, making them ideal for concurrent programming.
- **Documentation**: Use documentation comments (`///`) above properties and methods to explain their purpose.

## Related Articles

- KB-012: How to create and use Swift variables and constants
- KB-013: How to work with basic Swift data types
- KB-014: How to create functions in Swift with parameters and return values
- KB-015: How to use Swift optionals and safely unwrap values
- KB-016: How to create and use Swift arrays and dictionaries
- KB-020: How to work with Swift classes (if available)
- KB-021: How to use Swift enums for type-safe code (if available)

## Sources

- Swift.org - Structures and Classes Documentation - https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures/ (Accessed: November 17, 2025)
- Swift.org - Swift 6.2 Released - https://www.swift.org/blog/swift-6.2-released/ (Accessed: November 17, 2025)
- Swift.org - Value and Reference Types - https://www.swift.org/documentation/articles/value-and-reference-types.html (Accessed: November 17, 2025)
- Xcode 26 Release Notes - Apple Developer Documentation - https://developer.apple.com/documentation/xcode-release-notes/xcode-26-release-notes (Accessed: November 17, 2025)
- DEV Community - What's new in Xcode 26 - https://dev.to/arshtechpro/wwdc-2025-whats-new-in-xcode-26-3oo3 (Accessed: November 17, 2025)
- Swift Forums - Why does Apple recommend to use structs by default? - https://forums.swift.org/t/why-does-apple-recommend-to-use-structs-by-default/62066 (Accessed: November 17, 2025)

---
*This article is part of the Claude Code Web knowledge base*
