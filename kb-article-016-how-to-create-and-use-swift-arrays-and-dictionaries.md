# How to create and use Swift arrays and dictionaries

**Article ID:** KB-016
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 25-30 minutes

## Overview

Swift arrays and dictionaries are fundamental collection types that allow you to store and organize multiple values in your code. Arrays maintain ordered lists of values, while dictionaries store key-value pairs for quick lookups. This guide covers creating, accessing, modifying, and iterating through these essential data structures in Swift 6.2.1 (Xcode 26.1+).

## Prerequisites

- Xcode 26.1 or later installed
- Basic understanding of Swift syntax and variables
- Familiarity with Swift data types (String, Int, etc.)

## Steps

### Step 1: Understanding Arrays

Arrays are ordered collections that store values of the same type. Each element has a specific position (index) starting from 0.

**Create an array using array literal syntax:**

```swift
// Type inference - Swift automatically determines the type
let fruits = ["Apple", "Banana", "Orange"]

// Explicit type annotation
var numbers: [Int] = [1, 2, 3, 4, 5]

// Empty array with type specification
var emptyArray: [String] = []
var anotherEmpty = [Double]()
```

**Key characteristics:**
- Arrays are zero-indexed (first element is at index 0)
- All elements must be the same type
- Use `var` for mutable arrays, `let` for immutable ones

### Step 2: Accessing and Modifying Array Elements

Once you have an array, you can access and modify its elements using subscript syntax.

```swift
var cities = ["New York", "London", "Tokyo"]

// Access elements by index
print(cities[0])  // Output: New York
print(cities[2])  // Output: Tokyo

// Modify an element
cities[1] = "Paris"
print(cities)  // Output: ["New York", "Paris", "Tokyo"]

// Access first and last elements safely
if let firstCity = cities.first {
    print("First city: \(firstCity)")
}

if let lastCity = cities.last {
    print("Last city: \(lastCity)")
}
```

**Important:** Accessing an index that doesn't exist will cause a runtime error. Always check array bounds or use optional properties like `first` and `last`.

### Step 3: Common Array Operations

Swift provides rich functionality for manipulating arrays.

```swift
var names = ["Alice", "Bob", "Charlie"]

// Add elements
names.append("David")  // Adds to the end
names.insert("Zoe", at: 0)  // Inserts at specific index

// Remove elements
names.remove(at: 1)  // Removes element at index 1
names.removeLast()  // Removes last element
names.removeAll()  // Removes all elements

// Array properties
var scores = [95, 87, 92, 88]
print(scores.count)  // Output: 4
print(scores.isEmpty)  // Output: false

// Check if array contains an element
if scores.contains(92) {
    print("Score found!")
}

// Combine arrays
let moreScores = [91, 85]
let allScores = scores + moreScores
```

### Step 4: Understanding Dictionaries

Dictionaries store unordered collections of key-value pairs. Each key must be unique and maps to exactly one value.

**Create dictionaries:**

```swift
// Type inference
var student = ["name": "Alice", "grade": "A", "age": "20"]

// Explicit type annotation
var scores: [String: Int] = ["math": 95, "english": 87, "science": 92]

// Empty dictionary
var emptyDict: [String: String] = [:]
var anotherEmptyDict = [Int: String]()

// Dictionary with different value types using type aliases
var userProfile: [String: Any] = [
    "username": "alice123",
    "age": 25,
    "verified": true
]
```

**Key characteristics:**
- Keys must be unique and hashable
- Values can be any type (but typically all the same type)
- Dictionaries are unordered (no guaranteed sequence)

### Step 5: Accessing and Modifying Dictionary Values

Dictionary access returns optional values since a key might not exist.

```swift
var prices = ["apple": 1.99, "banana": 0.99, "orange": 2.49]

// Access values (returns Optional)
if let applePrice = prices["apple"] {
    print("Apple costs $\(applePrice)")
}

// Add or update values
prices["grape"] = 3.99  // Adds new key-value pair
prices["banana"] = 1.29  // Updates existing value

// Remove a key-value pair
prices["orange"] = nil  // Sets value to nil, removing the key
prices.removeValue(forKey: "grape")  // Alternative removal method

// Dictionary properties
print(prices.count)  // Number of key-value pairs
print(prices.isEmpty)  // Check if empty
```

### Step 6: Iterating Over Collections

Both arrays and dictionaries support iteration with for-in loops.

**Iterating over arrays:**

```swift
let fruits = ["Apple", "Banana", "Orange", "Mango"]

// Iterate over values
for fruit in fruits {
    print(fruit)
}

// Iterate with index using enumerated()
for (index, fruit) in fruits.enumerated() {
    print("\(index): \(fruit)")
}
```

**Iterating over dictionaries:**

```swift
let studentGrades = ["Alice": 95, "Bob": 87, "Charlie": 92]

// Iterate over key-value pairs
for (name, grade) in studentGrades {
    print("\(name) scored \(grade)")
}

// Iterate over keys only
for name in studentGrades.keys {
    print("Student: \(name)")
}

// Iterate over values only
for grade in studentGrades.values {
    print("Grade: \(grade)")
}
```

### Step 7: Working with Arrays of Dictionaries

A common pattern is combining arrays and dictionaries for structured data.

```swift
// Array of dictionaries representing users
let users = [
    ["first": "Lucy", "last": "Johnson", "age": "28"],
    ["first": "John", "last": "Williams", "age": "32"],
    ["first": "Emma", "last": "Davis", "age": "25"]
]

// Access nested data
for user in users {
    if let firstName = user["first"], let lastName = user["last"] {
        print("Name: \(firstName) \(lastName)")
    }
}

// Dictionary with array values
var studentCourses: [String: [String]] = [
    "Alice": ["Math", "Physics", "Chemistry"],
    "Bob": ["English", "History", "Art"]
]

// Access array within dictionary
if let aliceCourses = studentCourses["Alice"] {
    print("Alice's courses: \(aliceCourses.joined(separator: ", "))")
}
```

### Step 8: Advanced Array Operations

Swift provides powerful methods for transforming and filtering collections.

```swift
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// Map - transform each element
let doubled = numbers.map { $0 * 2 }
print(doubled)  // [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

// Filter - select elements matching criteria
let evenNumbers = numbers.filter { $0 % 2 == 0 }
print(evenNumbers)  // [2, 4, 6, 8, 10]

// Reduce - combine elements into single value
let sum = numbers.reduce(0, +)
print(sum)  // 55

// Sort
var names = ["Charlie", "Alice", "Bob"]
names.sort()  // Sorts in place
let sortedNames = names.sorted()  // Returns new sorted array

// Reverse
let reversed = numbers.reversed()
```

## Expected Results

After completing these steps, you should be able to:

- Create and initialize both empty and populated arrays and dictionaries
- Access elements using subscript syntax and safe optional unwrapping
- Add, modify, and remove elements from collections
- Iterate through arrays and dictionaries using for-in loops
- Combine arrays and dictionaries for complex data structures
- Use higher-order functions like map, filter, and reduce for data transformation

**Sample Output:**

```
Apple costs $1.99
Alice scored 95
Bob scored 87
Charlie scored 92
Name: Lucy Johnson
Name: John Williams
Name: Emma Davis
Alice's courses: Math, Physics, Chemistry
```

## Troubleshooting

### Common Issue 1: Index Out of Range Error
**Problem:** Runtime crash with "Fatal error: Index out of range"
**Solution:** Always verify array bounds before accessing by index. Use `.indices` property or check `count`.

```swift
// Unsafe
let value = myArray[5]  // Crashes if array has < 6 elements

// Safe
if myArray.indices.contains(5) {
    let value = myArray[5]
}

// Or use optional access patterns
if let value = myArray.first {
    print(value)
}
```

### Common Issue 2: Force Unwrapping Dictionary Values
**Problem:** Unexpectedly found nil while unwrapping an Optional value
**Solution:** Dictionary subscript returns Optional. Use optional binding instead of force unwrapping.

```swift
// Unsafe
let price = prices["apple"]!  // Crashes if key doesn't exist

// Safe
if let price = prices["apple"] {
    print("Price: \(price)")
} else {
    print("Price not found")
}

// Or provide default value
let price = prices["apple"] ?? 0.0
```

### Common Issue 3: Mutating Immutable Collections
**Problem:** "Cannot use mutating member on immutable value"
**Solution:** Declare collection with `var` instead of `let` if you need to modify it.

```swift
// Immutable - cannot modify
let numbers = [1, 2, 3]
// numbers.append(4)  // Error!

// Mutable - can modify
var numbers = [1, 2, 3]
numbers.append(4)  // Works!
```

## Additional Tips

- **Choose the right collection**: Use arrays when order matters and you need index-based access. Use dictionaries when you need fast lookups by unique keys.
- **Type safety**: Swift's type system ensures all elements in an array are the same type, preventing runtime errors.
- **Performance**: Array access by index is O(1). Dictionary lookup by key is also O(1) average case.
- **Swift 6.2 features**: While basic arrays and dictionaries remain unchanged, Swift 6.2 introduces `InlineArray` for fixed-size arrays with inline storage.
- **Memory efficiency**: Arrays automatically grow as needed, but you can use `reserveCapacity()` if you know the final size.
- **Copy-on-write**: Swift collections use copy-on-write semantics, so passing collections is efficient until they're modified.
- **Use meaningful keys**: For dictionaries, choose descriptive key names that make your code self-documenting.

## Related Articles

- KB-001: How to create a new Swift project in Xcode (if available)
- KB-002: How to write and run basic Swift code (if available)
- Future articles on Sets, Tuples, and other Swift collection types

## Sources

- Apple Developer Documentation - Array | Swift - https://developer.apple.com/documentation/swift/array (Accessed: November 17, 2025)
- Swift.org - Collection Types Language Guide - https://docs.swift.org/swift-book/LanguageGuide/CollectionTypes.html (Accessed: November 17, 2025)
- Xcode 26.1.1 Release Notes - https://developer.apple.com/documentation/xcode-release-notes/xcode-26_1-release-notes (Accessed: November 17, 2025)
- DEV Community - Essential updates in Xcode 26.1.1 with Swift 6.2.1 - https://dev.to/arshtechpro/essential-updates-in-xcode-2611-with-swift-621-2e3i (Accessed: November 17, 2025)
- DEV Community - Integer Generic Parameters in Swift 6.2 - https://dev.to/arshtechpro/iinteger-generic-parameters-in-swift-62-a-new-era-for-type-safe-programming-495d (Accessed: November 17, 2025)

---
*This article is part of the Claude Code Web knowledge base*
