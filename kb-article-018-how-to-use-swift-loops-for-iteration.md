# How to use Swift loops (for-in, while) for iteration

**Article ID:** KB-018
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 20-25 minutes

## Overview
Swift loops allow you to execute code repeatedly, making them essential for iterating over collections, processing data, and implementing control flow. This guide covers Swift's three primary loop types: for-in loops (for iterating over sequences), while loops (for condition-based repetition), and repeat-while loops (for guaranteed first execution). You'll learn how to use ranges, stride functions, enumeration, and control transfer statements in Swift 6.2.1 (Xcode 26.1+).

## Prerequisites
- Xcode 26.1 or later installed
- Basic understanding of Swift variables and constants
- Familiarity with Swift data types (arrays, ranges, strings)
- A Swift project or Playground open in Xcode

## Steps

### Step 1: Understanding For-In Loops with Ranges

The for-in loop is Swift's most common loop type, used to iterate over sequences like ranges, arrays, and collections. It executes a block of code once for each item in the sequence.

**Closed range operator (`...`):**

```swift
// Iterates from 1 to 5, including both endpoints
for number in 1...5 {
    print("Count: \(number)")
}
// Output: Count: 1, Count: 2, Count: 3, Count: 4, Count: 5
```

**Half-open range operator (`..<`):**

```swift
// Iterates from 0 to 4, excluding the upper bound
for index in 0..<5 {
    print("Index: \(index)")
}
// Output: Index: 0, Index: 1, Index: 2, Index: 3, Index: 4
```

**Ignoring the loop variable with underscore:**

```swift
// Use _ when you don't need the loop variable
for _ in 1...3 {
    print("Hello!")
}
// Output: Hello! Hello! Hello!
```

### Step 2: Iterating Over Arrays with For-In Loops

For-in loops are perfect for processing each element in an array or collection.

**Basic array iteration:**

```swift
let fruits = ["Apple", "Banana", "Cherry", "Date"]

for fruit in fruits {
    print("I like \(fruit)")
}
// Output:
// I like Apple
// I like Banana
// I like Cherry
// I like Date
```

**Using enumerated() to get both index and value:**

```swift
let colors = ["Red", "Green", "Blue"]

for (index, color) in colors.enumerated() {
    print("Color \(index): \(color)")
}
// Output:
// Color 0: Red
// Color 1: Green
// Color 2: Blue
```

**Iterating over a dictionary:**

```swift
let scores = ["Alice": 95, "Bob": 87, "Charlie": 92]

for (name, score) in scores {
    print("\(name) scored \(score) points")
}
// Output (order may vary):
// Alice scored 95 points
// Bob scored 87 points
// Charlie scored 92 points
```

### Step 3: Using Stride for Custom Increments

The `stride()` function allows you to iterate with custom step values, including negative steps for counting backwards.

**stride(from:to:by:) - excludes the upper bound:**

```swift
// Count by 2s from 0 to 8 (excluding 10)
for number in stride(from: 0, to: 10, by: 2) {
    print(number)
}
// Output: 0, 2, 4, 6, 8
```

**stride(from:through:by:) - includes the upper bound:**

```swift
// Count by 2s from 0 to 10 (including 10)
for number in stride(from: 0, through: 10, by: 2) {
    print(number)
}
// Output: 0, 2, 4, 6, 8, 10
```

**Counting backwards:**

```swift
// Countdown from 10 to 1
for countdown in stride(from: 10, through: 1, by: -1) {
    print("\(countdown)...")
}
print("Liftoff!")
// Output: 10... 9... 8... 7... 6... 5... 4... 3... 2... 1... Liftoff!
```

**Iterating through array indices with stride:**

```swift
let items = ["First", "Second", "Third", "Fourth", "Fifth"]

// Process every other item starting at index 0
for index in stride(from: 0, to: items.count, by: 2) {
    print("\(index): \(items[index])")
}
// Output:
// 0: First
// 2: Third
// 4: Fifth
```

### Step 4: Filtering Iterations with Where Clauses

The `where` clause lets you add conditions to filter which iterations execute, making loops more concise and readable.

**Basic where clause:**

```swift
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

for number in numbers where number % 2 == 0 {
    print("\(number) is even")
}
// Output: 2 is even, 4 is even, 6 is even, 8 is even, 10 is even
```

**Where clause with enumerated():**

```swift
let words = ["Swift", "Objective-C", "Python", "JavaScript", "Go"]

for (index, word) in words.enumerated() where word.count > 4 {
    print("[\(index)]: '\(word)' has more than 4 characters")
}
// Output:
// [0]: 'Swift' has more than 4 characters
// [1]: 'Objective-C' has more than 4 characters
// [2]: 'Python' has more than 4 characters
// [3]: 'JavaScript' has more than 4 characters
```

**Multiple conditions in where clause:**

```swift
let values = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

for value in values where value > 3 && value < 8 {
    print(value)
}
// Output: 4, 5, 6, 7
```

### Step 5: Using While Loops for Condition-Based Iteration

While loops execute code repeatedly as long as a condition remains true. They're ideal when you don't know the number of iterations in advance.

**Basic while loop:**

```swift
var countdown = 5

while countdown > 0 {
    print("T-minus \(countdown)")
    countdown -= 1
}
print("Blast off!")
// Output: T-minus 5, T-minus 4, T-minus 3, T-minus 2, T-minus 1, Blast off!
```

**While loop with input validation pattern:**

```swift
var attempts = 0
var password = ""
let correctPassword = "swift123"

while password != correctPassword && attempts < 3 {
    attempts += 1
    print("Attempt \(attempts): Enter password")
    // In real code, you'd get user input here
    password = (attempts == 2) ? "swift123" : "wrong"
}

if password == correctPassword {
    print("Access granted!")
} else {
    print("Access denied. Too many attempts.")
}
```

**Processing array elements with while:**

```swift
var numbers = [10, 20, 30, 40, 50]
var sum = 0
var index = 0

while index < numbers.count {
    sum += numbers[index]
    index += 1
}

print("Total sum: \(sum)")  // Output: Total sum: 150
```

### Step 6: Using Repeat-While Loops for Guaranteed Execution

The repeat-while loop (similar to do-while in other languages) executes its code block at least once before checking the condition. This is useful when you need guaranteed first execution.

**Basic repeat-while loop:**

```swift
var number = 5

repeat {
    print("Number: \(number)")
    number += 1
} while number < 5

// Output: Number: 5
// The loop runs once even though 5 is not less than 5
```

**Compare with while loop (no execution):**

```swift
var value = 5

while value < 5 {
    print("This never prints")
    value += 1
}
// No output - condition is false from the start
```

**Menu-driven program pattern:**

```swift
var choice = 0

repeat {
    print("""

    Menu:
    1. View Profile
    2. Edit Settings
    3. Logout
    Enter choice (1-3):
    """)

    // Simulate user choice
    choice = 3  // In real code, get user input

    switch choice {
    case 1:
        print("Viewing profile...")
    case 2:
        print("Editing settings...")
    case 3:
        print("Logging out...")
    default:
        print("Invalid choice")
    }

} while choice != 3

print("Goodbye!")
```

**Retry logic with repeat-while:**

```swift
var retries = 0
let maxRetries = 3
var success = false

repeat {
    retries += 1
    print("Attempt \(retries): Trying to connect...")

    // Simulate connection attempt
    let randomSuccess = (retries == 2)  // Succeeds on 2nd try

    if randomSuccess {
        success = true
        print("Connected successfully!")
    } else {
        print("Connection failed")
    }

} while !success && retries < maxRetries

if !success {
    print("Failed after \(maxRetries) attempts")
}
```

### Step 7: Control Transfer Statements (Break and Continue)

Control transfer statements modify the normal flow of loop execution, allowing you to skip iterations or exit loops early.

**Continue - Skip to next iteration:**

```swift
// Skip odd numbers
for number in 1...10 {
    if number % 2 != 0 {
        continue  // Skip odd numbers
    }
    print("\(number) is even")
}
// Output: 2 is even, 4 is even, 6 is even, 8 is even, 10 is even
```

**Break - Exit loop immediately:**

```swift
// Find first number divisible by 7
for number in 1...100 {
    if number % 7 == 0 {
        print("First multiple of 7 is: \(number)")
        break  // Exit loop immediately
    }
}
// Output: First multiple of 7 is: 7
```

**Combining continue and break:**

```swift
var searchValue = 42
var attempts = 0

for number in 1...100 {
    attempts += 1

    // Skip numbers less than 20
    if number < 20 {
        continue
    }

    // Found the value - exit
    if number == searchValue {
        print("Found \(searchValue) after \(attempts) iterations")
        break
    }
}
// Output: Found 42 after 42 iterations
```

**Nested loop control with labeled statements:**

```swift
outerLoop: for i in 1...3 {
    for j in 1...3 {
        if i == 2 && j == 2 {
            print("Breaking outer loop at i=\(i), j=\(j)")
            break outerLoop  // Breaks the outer loop, not just inner
        }
        print("i=\(i), j=\(j)")
    }
}
// Output:
// i=1, j=1
// i=1, j=2
// i=1, j=3
// i=2, j=1
// Breaking outer loop at i=2, j=2
```

### Step 8: Practical Example - Data Processing Pipeline

Here's a complete example combining multiple loop techniques to process and analyze data:

```swift
struct Product {
    let name: String
    let price: Double
    let inStock: Bool
    let rating: Double
}

let products = [
    Product(name: "iPhone", price: 999.99, inStock: true, rating: 4.8),
    Product(name: "iPad", price: 599.99, inStock: false, rating: 4.6),
    Product(name: "MacBook", price: 1299.99, inStock: true, rating: 4.9),
    Product(name: "Apple Watch", price: 399.99, inStock: true, rating: 4.7),
    Product(name: "AirPods", price: 199.99, inStock: false, rating: 4.5),
    Product(name: "Mac Mini", price: 699.99, inStock: true, rating: 4.4)
]

// Task 1: Find all in-stock products with rating above 4.5
print("=== High-Rated In-Stock Products ===")
for (index, product) in products.enumerated() where product.inStock && product.rating > 4.5 {
    print("\(index + 1). \(product.name) - $\(product.price) ⭐️\(product.rating)")
}

// Task 2: Calculate total inventory value of in-stock items
print("\n=== Inventory Value Calculation ===")
var totalValue = 0.0
var stockCount = 0

for product in products {
    if !product.inStock {
        continue  // Skip out-of-stock items
    }

    totalValue += product.price
    stockCount += 1
    print("Added \(product.name): $\(product.price)")
}

print("Total inventory value: $\(String(format: "%.2f", totalValue))")
print("Items in stock: \(stockCount)")

// Task 3: Find first product under $500
print("\n=== Budget Product Search ===")
var foundBudgetItem = false

for product in products {
    if product.price < 500 && product.inStock {
        print("Budget recommendation: \(product.name) at $\(product.price)")
        foundBudgetItem = true
        break  // Found first match, exit
    }
}

if !foundBudgetItem {
    print("No budget items found in stock")
}

// Task 4: Generate price tiers report
print("\n=== Price Tiers ===")
let priceTiers = [
    ("Budget", 0.0...300.0),
    ("Mid-Range", 300.01...700.0),
    ("Premium", 700.01...2000.0)
]

for (tierName, priceRange) in priceTiers {
    var tierProducts: [String] = []

    for product in products {
        if priceRange.contains(product.price) {
            tierProducts.append(product.name)
        }
    }

    print("\(tierName): \(tierProducts.joined(separator: ", "))")
}

// Task 5: Simulate inventory check with retry logic
print("\n=== Inventory System Check ===")
var systemOnline = false
var checkAttempt = 0
let maxAttempts = 3

repeat {
    checkAttempt += 1
    print("Checking inventory system... (Attempt \(checkAttempt))")

    // Simulate system check (succeeds on attempt 2)
    systemOnline = (checkAttempt == 2)

    if systemOnline {
        print("✓ Inventory system is online")

        // Process urgent restock alerts
        for product in products where !product.inStock && product.rating > 4.5 {
            print("⚠️ RESTOCK ALERT: \(product.name) (High demand, rating: \(product.rating))")
        }
    } else {
        print("✗ System unavailable, retrying...")
    }

} while !systemOnline && checkAttempt < maxAttempts

if !systemOnline {
    print("⚠️ Unable to connect to inventory system after \(maxAttempts) attempts")
}
```

## Expected Results

When you run the code examples:

1. **For-in with ranges**: Prints sequential numbers according to the range specification
2. **Array iteration**: Processes each element in the collection once
3. **enumerated()**: Provides both the index (starting at 0) and the value for each element
4. **Dictionary iteration**: Processes key-value pairs (order may vary as dictionaries are unordered)
5. **stride()**: Creates custom iteration patterns with specified step values
6. **Where clauses**: Only executes loop body for items matching the condition
7. **While loops**: Continues executing while condition is true, may not execute at all if initially false
8. **Repeat-while loops**: Always executes at least once, then checks condition
9. **Break**: Immediately exits the current loop
10. **Continue**: Skips remaining code in current iteration and moves to next iteration
11. **Product processing example**: Produces formatted output showing:
    ```
    === High-Rated In-Stock Products ===
    1. iPhone - $999.99 ⭐️4.8
    3. MacBook - $1299.99 ⭐️4.9
    4. Apple Watch - $399.99 ⭐️4.7

    === Inventory Value Calculation ===
    Added iPhone: $999.99
    Added MacBook: $1299.99
    Added Apple Watch: $399.99
    Added Mac Mini: $699.99
    Total inventory value: $3399.96
    Items in stock: 4

    === Budget Product Search ===
    Budget recommendation: Apple Watch at $399.99

    === Price Tiers ===
    Budget: AirPods
    Mid-Range: iPad, Apple Watch, Mac Mini
    Premium: iPhone, MacBook

    === Inventory System Check ===
    Checking inventory system... (Attempt 1)
    ✗ System unavailable, retrying...
    Checking inventory system... (Attempt 2)
    ✓ Inventory system is online
    ⚠️ RESTOCK ALERT: iPad (High demand, rating: 4.6)
    ```

## Troubleshooting

### Common Issue 1: "Cannot convert value of type 'Int' to expected range type"
**Problem:** Using the wrong range operator for your use case
**Solution:** Use `...` for closed ranges (includes both endpoints) and `..<` for half-open ranges (excludes upper bound)

```swift
// Problem - trying to use .< (doesn't exist)
for i in 0.< 5 {  // Error!
    print(i)
}

// Solution 1: Half-open range
for i in 0..<5 {  // 0, 1, 2, 3, 4
    print(i)
}

// Solution 2: Closed range
for i in 0...4 {  // 0, 1, 2, 3, 4
    print(i)
}
```

### Common Issue 2: Infinite loop - program never stops
**Problem:** Loop condition never becomes false, causing endless execution
**Solution:** Ensure the loop variable or condition changes within the loop body

```swift
// Problem - infinite loop
var count = 0
while count < 5 {
    print(count)
    // Forgot to increment count!
}

// Solution
var count = 0
while count < 5 {
    print(count)
    count += 1  // Must modify the condition variable
}
```

### Common Issue 3: "Index out of range" error in while loop
**Problem:** Array index exceeds the array bounds during iteration
**Solution:** Always check that index is less than `array.count`

```swift
// Problem
let items = ["A", "B", "C"]
var i = 0
while i <= items.count {  // Wrong! Should be <
    print(items[i])  // Crashes when i == 3
    i += 1
}

// Solution
let items = ["A", "B", "C"]
var i = 0
while i < items.count {  // Correct: < not <=
    print(items[i])
    i += 1
}
```

### Common Issue 4: Confusion between `continue` and `break`
**Problem:** Using the wrong control statement for the desired behavior
**Solution:** Remember: `continue` skips to next iteration, `break` exits the loop entirely

```swift
// Example: Find first even number greater than 5
for number in 1...20 {
    if number <= 5 {
        continue  // Skip numbers 1-5
    }

    if number % 2 == 0 {
        print("Found: \(number)")
        break  // Exit loop after finding first match
    }
}
// Output: Found: 6
```

### Common Issue 5: Modifying array while iterating
**Problem:** Changing an array's contents while looping through it can cause crashes or unexpected behavior
**Solution:** Iterate over a copy or collect changes to apply after the loop

```swift
// Problem - dangerous!
var numbers = [1, 2, 3, 4, 5]
for number in numbers {
    if number % 2 == 0 {
        numbers.removeAll { $0 == number }  // Modifying during iteration!
    }
}

// Solution 1: Filter to create new array
let numbers = [1, 2, 3, 4, 5]
let oddNumbers = numbers.filter { $0 % 2 != 0 }
print(oddNumbers)  // [1, 3, 5]

// Solution 2: Iterate backwards by index (safe for removal)
var numbers = [1, 2, 3, 4, 5]
for index in stride(from: numbers.count - 1, through: 0, by: -1) {
    if numbers[index] % 2 == 0 {
        numbers.remove(at: index)
    }
}
print(numbers)  // [1, 3, 5]
```

## Additional Tips

- **Choose the right loop type**: Use `for-in` for sequences, `while` for unknown iteration counts, `repeat-while` for guaranteed first execution
- **Prefer for-in over while for arrays**: For-in loops are safer and more concise for iterating collections
- **Use enumerated() for index access**: Instead of manual index tracking, use `for (index, value) in array.enumerated()`
- **Leverage where clauses**: Filter iterations inline rather than using if statements inside the loop
- **Use stride() for custom increments**: More flexible than manual counter manipulation
- **Remember closed vs half-open ranges**: `1...5` includes 5, `1..<5` excludes 5 (stops at 4)
- **Break nested loops with labels**: Use labeled statements like `outerLoop:` to break out of outer loops
- **Avoid modifying collections during iteration**: Create new arrays with `filter()` or `map()` instead
- **Watch for infinite loops**: Always ensure loop conditions can become false
- **Use `_` for unused loop variables**: When you don't need the iteration value, use underscore: `for _ in 1...3`
- **Combine with Swift collections methods**: Modern Swift offers `forEach()`, `map()`, `filter()`, and `reduce()` as functional alternatives
- **Profile performance**: For large datasets, measure whether traditional loops or functional methods are faster
- **Consider async iteration**: In Swift 6, use `for await` with AsyncSequence for asynchronous data streams

## Related Articles
- KB-012: How to create and use Swift variables and constants
- KB-013: How to work with basic Swift data types (String, Int, Bool, Double)
- KB-016: How to create and use Swift arrays and dictionaries
- KB-017: How to implement basic control flow (if/else, switch statements)
- KB-019: How to create and use custom Swift structs
- KB-014: How to create functions in Swift with parameters and return values (if available)

## Sources
- Swift.org - Control Flow Documentation - https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow/ (Accessed: November 17, 2025)
- Swift.org Language Guide - Control Flow - https://docs.swift.org/swift-book/LanguageGuide/ControlFlow.html (Accessed: November 17, 2025)
- Programiz - Swift for Loop Examples - https://www.programiz.com/swift-programming/for-in-loop (Accessed: November 17, 2025)
- Programiz - Swift while and repeat-while Loop - https://www.programiz.com/swift-programming/repeat-while-loop (Accessed: November 17, 2025)
- HackingWithSwift - Using stride to loop over a range - https://www.hackingwithswift.com/example-code/language/using-stride-to-loop-over-a-range-of-numbers (Accessed: November 17, 2025)
- LogRocket Blog - for-in loops in Swift tutorial - https://blog.logrocket.com/for-in-loops-swift-tutorial/ (Accessed: November 17, 2025)
- Medium - Mastering Loops in Swift: A Complete Guide - https://medium.com/@iamCoder/mastering-loops-in-swift-a-complete-guide-ef8f18912c4e (Accessed: November 17, 2025)
- Stack Overflow - Swift Repeat While Loop Best Practices - https://stackoverflow.com/questions/65463991/swift-repeat-while-loop-best-practices (Accessed: November 17, 2025)
- Google Search Suggestion: "Swift 6.2 control flow loops official documentation"

---
*This article is part of the Claude Code Web knowledge base*
