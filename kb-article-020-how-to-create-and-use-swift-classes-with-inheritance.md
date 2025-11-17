# How to create and use Swift classes with inheritance

**Article ID:** KB-020
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 25-30 minutes

## Overview

This guide teaches you how to create classes with inheritance in Swift 6, enabling you to build hierarchical relationships between objects and reuse code effectively. You'll learn how to define base classes, create subclasses, override methods and properties, and use the `super` keyword to access parent functionality. Classes with inheritance are essential for modeling real-world relationships in iOS applications developed with Xcode 26.1+.

## Prerequisites

Before starting this tutorial, you should have:

- **Xcode 26.1+** installed on your Mac
- **Basic understanding** of Swift classes and structs
- **Familiarity** with creating Swift functions and properties
- Understanding of object-oriented programming concepts

**Related Articles:**
- KB-012: How to create and use Swift variables and constants
- KB-013: How to work with basic Swift data types
- KB-014: How to create functions in Swift with parameters and return values
- KB-019: How to create and use custom Swift structs

## Steps

### Step 1: Understand Classes vs Structs

Before diving into inheritance, understand the key difference:

- **Structs**: Value types, no inheritance, copied when passed around
- **Classes**: Reference types, support inheritance, shared when passed around

**Classes support inheritance; structs do not.** This makes classes ideal for modeling hierarchical relationships like:
- Animal → Dog → Labrador
- Vehicle → Car → ElectricCar
- UIViewController → CustomViewController

```swift
// Struct - NO inheritance support
struct Animal {
    var name: String
}
// struct Dog: Animal { }  // ❌ Error: Struct cannot inherit

// Class - SUPPORTS inheritance
class Animal {
    var name: String
    init(name: String) {
        self.name = name
    }
}
class Dog: Animal { }  // ✅ Works: Class can inherit
```

**When to use classes with inheritance:**
- Building UI component hierarchies
- Creating game entities with shared behaviors
- Modeling real-world object hierarchies
- Working with UIKit/AppKit (which use classes extensively)
- Need to override parent class behavior

### Step 2: Create Your First Base Class

A **base class** (also called parent or superclass) is a class that doesn't inherit from anything else.

Open Xcode and create a new Swift file or playground, then define a base class:

```swift
class Vehicle {
    var currentSpeed = 0.0
    var description: String {
        return "traveling at \(currentSpeed) miles per hour"
    }

    func makeNoise() {
        // Default implementation does nothing
        // Subclasses can override this
    }
}
```

**What's happening:**
- `Vehicle` is a base class (no `: ParentClass` after its name)
- Has a stored property `currentSpeed` with default value
- Has a computed property `description`
- Has a method `makeNoise()` that subclasses can customize

**Create an instance:**

```swift
let someVehicle = Vehicle()
print("Vehicle: \(someVehicle.description)")
// Output: Vehicle: traveling at 0.0 miles per hour
```

### Step 3: Create a Subclass (Inherit from Base Class)

A **subclass** inherits properties, methods, and other characteristics from its parent class.

**Syntax:** `class SubclassName: ParentClass { }`

```swift
class Bicycle: Vehicle {
    var hasBasket = false
}
```

**What you get from inheritance:**
- Bicycle automatically has `currentSpeed` property
- Bicycle automatically has `description` computed property
- Bicycle automatically has `makeNoise()` method
- Bicycle adds its own unique property `hasBasket`

**Using the subclass:**

```swift
let bicycle = Bicycle()
bicycle.hasBasket = true
bicycle.currentSpeed = 15.0

print("Bicycle: \(bicycle.description)")
// Output: Bicycle: traveling at 15.0 miles per hour

print("Has basket: \(bicycle.hasBasket)")
// Output: Has basket: true
```

### Step 4: Create Multi-Level Inheritance Hierarchies

Subclasses can themselves become base classes for further subclasses.

```swift
class Bicycle: Vehicle {
    var hasBasket = false
}

class Tandem: Bicycle {
    var currentNumberOfPassengers = 0
}
```

**Inheritance chain:** Vehicle → Bicycle → Tandem

A `Tandem` instance has:
- `currentSpeed` (from Vehicle)
- `description` (from Vehicle)
- `makeNoise()` (from Vehicle)
- `hasBasket` (from Bicycle)
- `currentNumberOfPassengers` (its own property)

```swift
let tandem = Tandem()
tandem.hasBasket = true
tandem.currentNumberOfPassengers = 2
tandem.currentSpeed = 22.0

print("Tandem: \(tandem.description)")
// Output: Tandem: traveling at 22.0 miles per hour
print("Passengers: \(tandem.currentNumberOfPassengers)")
// Output: Passengers: 2
```

### Step 5: Override Methods in Subclasses

Use the `override` keyword to provide a custom implementation of a method inherited from a parent class.

```swift
class Train: Vehicle {
    override func makeNoise() {
        print("Choo Choo")
    }
}
```

**The `override` keyword tells Swift:**
- You're intentionally replacing the parent's implementation
- Swift should verify the parent actually has this method
- Prevents accidental overrides

**Using the overridden method:**

```swift
let train = Train()
train.makeNoise()
// Output: Choo Choo

let vehicle = Vehicle()
vehicle.makeNoise()
// Output: (nothing - base class has empty implementation)
```

**What happens without `override`:**

```swift
class Car: Vehicle {
    func makeNoise() {  // ❌ Error: Must use 'override'
        print("Beep beep")
    }
}
```

**Error:** "Method does not override any method from its superclass"

**Fix:** Add the `override` keyword:

```swift
class Car: Vehicle {
    override func makeNoise() {  // ✅ Correct
        print("Beep beep")
    }
}
```

### Step 6: Override Properties

You can override inherited properties to provide custom getters and setters or add property observers.

**Override a computed property:**

```swift
class Car: Vehicle {
    var gear = 1

    override var description: String {
        return super.description + " in gear \(gear)"
    }
}
```

**Using `super.description`:**
- Calls the parent class's `description` property
- Builds upon the parent's implementation
- Adds additional information

```swift
let car = Car()
car.currentSpeed = 25.0
car.gear = 3
print("Car: \(car.description)")
// Output: Car: traveling at 25.0 miles per hour in gear 3
```

**Add property observers to inherited properties:**

```swift
class AutomaticCar: Car {
    override var currentSpeed: Double {
        didSet {
            gear = Int(currentSpeed / 10.0) + 1
        }
    }
}
```

**What's happening:**
- `AutomaticCar` watches for changes to `currentSpeed`
- Automatically adjusts `gear` based on speed
- `didSet` runs after the value changes

```swift
let automatic = AutomaticCar()
automatic.currentSpeed = 35.0
print("Automatic car: \(automatic.description)")
// Output: Automatic car: traveling at 35.0 miles per hour in gear 4
```

### Step 7: Use the `super` Keyword to Access Parent Class

The `super` keyword lets you access methods, properties, and subscripts from the parent class.

**Common uses of `super`:**
- `super.methodName()` - Call parent's method implementation
- `super.propertyName` - Access parent's property
- `super.init()` - Call parent's initializer

```swift
class Motorcycle: Vehicle {
    var hasSidecar = false

    override func makeNoise() {
        super.makeNoise()  // Call parent's implementation first
        print("Vroom vroom")
    }

    override var description: String {
        let baseDescription = super.description
        return baseDescription + (hasSidecar ? " with a sidecar" : "")
    }
}
```

**Using super to extend functionality:**

```swift
let motorcycle = Motorcycle()
motorcycle.currentSpeed = 45.0
motorcycle.hasSidecar = true

motorcycle.makeNoise()
// Output: Vroom vroom (super.makeNoise() was empty, so only this prints)

print(motorcycle.description)
// Output: traveling at 45.0 miles per hour with a sidecar
```

### Step 8: Require Initialization with Custom Initializers

When adding stored properties without default values, you must provide an initializer.

```swift
class Person {
    var name: String
    var age: Int

    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }

    func introduce() {
        print("Hi, I'm \(name) and I'm \(age) years old")
    }
}
```

**Create a subclass with additional properties:**

```swift
class Student: Person {
    var studentID: String

    init(name: String, age: Int, studentID: String) {
        // Initialize subclass properties first
        self.studentID = studentID

        // Then call parent's initializer
        super.init(name: name, age: age)
    }

    override func introduce() {
        super.introduce()
        print("My student ID is \(studentID)")
    }
}
```

**Initialization order:**
1. Initialize properties introduced by the subclass
2. Call `super.init()` to initialize parent class properties
3. Modify inherited properties if needed (after super.init())

```swift
let student = Student(name: "Alex", age: 20, studentID: "S12345")
student.introduce()
// Output:
// Hi, I'm Alex and I'm 20 years old
// My student ID is S12345
```

### Step 9: Prevent Overriding with the `final` Keyword

Use `final` to prevent a class, method, or property from being overridden or subclassed.

**Prevent method override:**

```swift
class Vehicle {
    final func startEngine() {
        print("Engine started")
    }
}

class Car: Vehicle {
    override func startEngine() {  // ❌ Error: Cannot override final method
        print("Car engine started")
    }
}
```

**Prevent property override:**

```swift
class Animal {
    final var legCount = 4
}

class Dog: Animal {
    override var legCount: Int {  // ❌ Error: Cannot override final property
        return 4
    }
}
```

**Prevent subclassing entirely:**

```swift
final class MathUtilities {
    static func add(_ a: Int, _ b: Int) -> Int {
        return a + b
    }
}

class AdvancedMath: MathUtilities {  // ❌ Error: Cannot inherit from final class
}
```

**When to use `final`:**
- Performance optimization (compiler can inline final methods)
- Prevent unintended modifications to critical functionality
- Document that a class/method is not designed for subclassing
- Enforce design constraints

### Step 10: Check Type and Cast with `is` and `as`

Use type checking and casting to work with class hierarchies.

**Type checking with `is`:**

```swift
class MediaItem {
    var name: String
    init(name: String) {
        self.name = name
    }
}

class Movie: MediaItem {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}

class Song: MediaItem {
    var artist: String
    init(name: String, artist: String) {
        self.artist = artist
        super.init(name: name)
    }
}
```

**Create mixed array:**

```swift
let library: [MediaItem] = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley"),
    Movie(name: "Citizen Kane", director: "Orson Welles"),
    Song(name: "The One And Only", artist: "Chesney Hawkes")
]
```

**Count types with `is`:**

```swift
var movieCount = 0
var songCount = 0

for item in library {
    if item is Movie {
        movieCount += 1
    } else if item is Song {
        songCount += 1
    }
}

print("Library contains \(movieCount) movies and \(songCount) songs")
// Output: Library contains 2 movies and 2 songs
```

**Type casting with `as?` and `as!`:**

```swift
for item in library {
    if let movie = item as? Movie {
        print("Movie: '\(movie.name)', dir. \(movie.director)")
    } else if let song = item as? Song {
        print("Song: '\(song.name)', by \(song.artist)")
    }
}
// Output:
// Movie: 'Casablanca', dir. Michael Curtiz
// Song: 'Blue Suede Shoes', by Elvis Presley
// Movie: 'Citizen Kane', dir. Orson Welles
// Song: 'The One And Only', by Chesney Hawkes
```

**Difference between `as?` and `as!`:**
- `as?` - Safe cast, returns optional (nil if cast fails)
- `as!` - Force cast, crashes if cast fails (use only when certain)

### Step 11: Build a Real iOS Example with UIKit

Here's how inheritance works in real iOS development with UIKit:

```swift
import UIKit

// Base class from UIKit - you inherit from it
class CustomViewController: UIViewController {

    let titleLabel = UILabel()

    override func viewDidLoad() {
        super.viewDidLoad()  // ALWAYS call super.viewDidLoad()

        // Your custom setup
        view.backgroundColor = .systemBackground
        setupTitleLabel()
    }

    private func setupTitleLabel() {
        titleLabel.text = "Welcome"
        titleLabel.font = .systemFont(ofSize: 24, weight: .bold)
        titleLabel.textAlignment = .center
        titleLabel.translatesAutoresizingMaskIntoConstraints = false

        view.addSubview(titleLabel)

        NSLayoutConstraint.activate([
            titleLabel.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            titleLabel.centerYAnchor.constraint(equalTo: view.centerYAnchor)
        ])
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        print("View will appear")
    }

    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        print("View did appear")
    }
}
```

**Key points about UIKit inheritance:**
- `UIViewController` provides base functionality for view controllers
- Override lifecycle methods (`viewDidLoad`, `viewWillAppear`, etc.)
- **Always call `super` in overridden lifecycle methods**
- Add your custom properties and methods
- Inherit all UIViewController functionality (navigation, presentation, etc.)

### Step 12: Understand Class vs Struct Trade-offs

**When to use classes with inheritance:**
- Modeling hierarchical relationships (Vehicle → Car → ElectricCar)
- Working with UIKit/AppKit (requires classes)
- Need identity (same instance shared across app)
- Need deinitializers
- Interoperating with Objective-C

**When to use structs instead:**
- Simple data containers
- No need for inheritance
- Prefer value semantics (copies)
- Better performance for small data
- Thread-safe by default
- SwiftUI prefers structs for views and models

```swift
// Good use of class with inheritance
class Animal {
    func makeSound() {
        print("Some sound")
    }
}

class Dog: Animal {
    override func makeSound() {
        print("Woof!")
    }
}

// Better as struct (no inheritance needed)
struct Point {
    var x: Double
    var y: Double
}

struct Size {
    var width: Double
    var height: Double
}
```

## Expected Results

After completing this tutorial, you should be able to:

✅ **Create base classes** that other classes can inherit from
✅ **Create subclasses** using the colon syntax (`: ParentClass`)
✅ **Override methods** using the `override` keyword
✅ **Override properties** with custom implementations
✅ **Use `super`** to access parent class functionality
✅ **Write initializers** for subclasses that call `super.init()`
✅ **Prevent overriding** with the `final` keyword
✅ **Check types** with `is` and cast with `as?`/`as!`
✅ **Build class hierarchies** for iOS apps
✅ **Understand when to use classes vs structs**

**Example of successful understanding:**

```swift
// You should be comfortable writing hierarchies like this:
class GameCharacter {
    var health: Int
    var name: String

    init(name: String, health: Int = 100) {
        self.name = name
        self.health = health
    }

    func attack() {
        print("\(name) attacks!")
    }
}

class Warrior: GameCharacter {
    var weaponType: String

    init(name: String, weaponType: String) {
        self.weaponType = weaponType
        super.init(name: name, health: 150)
    }

    override func attack() {
        super.attack()
        print("\(name) swings their \(weaponType)!")
    }
}

class Mage: GameCharacter {
    var mana: Int

    init(name: String, mana: Int = 100) {
        self.mana = mana
        super.init(name: name, health: 80)
    }

    override func attack() {
        if mana >= 10 {
            print("\(name) casts a spell!")
            mana -= 10
        } else {
            print("\(name) is out of mana!")
        }
    }
}

let warrior = Warrior(name: "Conan", weaponType: "sword")
let mage = Mage(name: "Gandalf")

warrior.attack()
// Output:
// Conan attacks!
// Conan swings their sword!

mage.attack()
// Output: Gandalf casts a spell!
```

## Troubleshooting

### Common Issue 1: Forgot `override` Keyword

**Problem:** Error message: "Overriding method must be marked with the 'override' keyword"

```swift
class Vehicle {
    func makeNoise() { }
}

class Car: Vehicle {
    func makeNoise() {  // ❌ Error: Missing 'override'
        print("Beep")
    }
}
```

**Solution:**
- Add `override` keyword when replacing a parent class method

```swift
class Car: Vehicle {
    override func makeNoise() {  // ✅ Correct
        print("Beep")
    }
}
```

### Common Issue 2: Unnecessary `override` Keyword

**Problem:** Error message: "Method does not override any method from its superclass"

```swift
class Vehicle {
    func start() { }
}

class Car: Vehicle {
    override func stop() {  // ❌ Error: Parent doesn't have 'stop()'
        print("Stopping")
    }
}
```

**Solution:**
- Remove `override` keyword for new methods that don't exist in parent
- Only use `override` for methods that exist in the parent class

```swift
class Car: Vehicle {
    func stop() {  // ✅ Correct: New method, no override needed
        print("Stopping")
    }
}
```

### Common Issue 3: Forgot to Call `super.init()`

**Problem:** Error message: "'super.init' isn't called on all paths before returning from initializer"

```swift
class Person {
    var name: String
    init(name: String) {
        self.name = name
    }
}

class Student: Person {
    var studentID: String

    init(name: String, studentID: String) {
        self.studentID = studentID
        // ❌ Error: Forgot super.init()
    }
}
```

**Solution:**
- Always call `super.init()` after initializing subclass properties

```swift
class Student: Person {
    var studentID: String

    init(name: String, studentID: String) {
        self.studentID = studentID
        super.init(name: name)  // ✅ Correct
    }
}
```

### Common Issue 4: Wrong Initialization Order

**Problem:** Error message: "Property 'self.studentID' not initialized at super.init call"

```swift
class Student: Person {
    var studentID: String

    init(name: String, studentID: String) {
        super.init(name: name)  // ❌ Error: Called super.init too early
        self.studentID = studentID
    }
}
```

**Solution:**
- Initialize subclass properties BEFORE calling `super.init()`

```swift
class Student: Person {
    var studentID: String

    init(name: String, studentID: String) {
        self.studentID = studentID  // ✅ First: Initialize subclass property
        super.init(name: name)       // ✅ Then: Call super.init()
    }
}
```

### Common Issue 5: Trying to Override `final` Members

**Problem:** Error message: "Instance method overrides a 'final' instance method"

```swift
class Vehicle {
    final func startEngine() {
        print("Engine started")
    }
}

class Car: Vehicle {
    override func startEngine() {  // ❌ Error: Can't override final method
        print("Car engine started")
    }
}
```

**Solution:**
- Don't try to override `final` methods
- If you need customization, the parent class design needs to change
- Use composition instead of inheritance if you can't modify the parent

```swift
class Car: Vehicle {
    func startCarEngine() {  // ✅ Different method, not an override
        startEngine()  // Call parent's final method
        print("Car is ready")
    }
}
```

## Additional Tips

- **Always call `super` in lifecycle methods**: When overriding UIKit methods like `viewDidLoad()`, `viewWillAppear()`, etc., always call the parent implementation with `super.methodName()`.

- **Use `final` for optimization**: Marking classes and methods as `final` enables compiler optimizations and makes code run faster.

- **Prefer composition over inheritance**: Don't create deep inheritance hierarchies. Consider using protocols and composition instead.
  ```swift
  // Instead of: ElectricVehicle → ElectricCar → ElectricSportsCar
  // Consider: Car with Engine protocol implementation
  ```

- **Document inheritance requirements**: If designing a class for subclassing, document which methods should be overridden and which should call `super`.

- **Use access control**: Mark methods as `private` if they shouldn't be overridden. Use `open` in frameworks to explicitly allow subclassing.

- **Type erasure with protocols**: For polymorphism without inheritance overhead, consider using protocols with associated types.

- **Memory management**: Classes are reference types. Be aware of retain cycles when using closures that capture `self`.
  ```swift
  // Use [weak self] or [unowned self] in closures
  someMethod { [weak self] in
      self?.doSomething()
  }
  ```

- **Testing**: Classes with inheritance can be harder to test. Consider dependency injection and protocols for better testability.

- **SwiftUI preference**: SwiftUI prefers structs over classes. Most SwiftUI views should be structs conforming to the `View` protocol, not classes with inheritance.

- **Xcode refactoring**: Use Xcode's refactoring tools (**Editor** → **Refactor**) to safely rename methods and properties in class hierarchies.

## Related Articles

- KB-012: How to create and use Swift variables and constants
- KB-013: How to work with basic Swift data types
- KB-014: How to create functions in Swift with parameters and return values
- KB-019: How to create and use custom Swift structs
- KB-021: How to work with Swift protocols and protocol-oriented programming
- KB-023: How to use Swift enums with associated values
- KB-024: How to understand value types vs reference types in Swift
- KB-028: How to manage memory and avoid retain cycles with classes

## Sources

- The Swift Programming Language - Inheritance - https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance/ (Accessed: November 17, 2025)
- The Swift Programming Language - Initialization - https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization/ (Accessed: November 17, 2025)
- Swift.org Documentation - Classes and Structures - https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures/ (Accessed: November 17, 2025)
- Hacking with Swift - Class Inheritance Tutorial - https://www.hackingwithswift.com/sixty/8/2/class-inheritance (Accessed: November 17, 2025)
- Mike Gopsill - Swift Classes & Inheritance - https://www.mikegopsill.com/posts/swift-classes-inheritance/ (Accessed: November 17, 2025)
- Kodeco - Swift Cookbook: Use Inheritance in Swift - https://www.kodeco.com/books/swift-cookbook/v1.0/chapters/7-use-inheritance-in-swift (Accessed: November 17, 2025)

---
*This article is part of the Claude Code Web knowledge base*
