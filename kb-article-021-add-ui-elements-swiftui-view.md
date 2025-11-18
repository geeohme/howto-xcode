# How to add UI elements to your SwiftUI view (Text, Image, Button)

**Article ID:** KB-021
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 15-20 minutes

## Overview

This guide teaches you how to add the three fundamental UI elements to your SwiftUI views: Text, Image, and Button. These are the building blocks of nearly every iOS app interface. You'll learn how to create each element, customize their appearance, and combine them to build functional user interfaces in Xcode 26.1+.

## Prerequisites

Before adding UI elements to your SwiftUI views, ensure you have:

- **Xcode 26.1+:** Installed and verified on your Mac
- **Basic SwiftUI Knowledge:** Understanding of ContentView and the View protocol
- **iOS App Project:** A SwiftUI app project created and ready to edit
- **macOS:** Sequoia 15.6 or later

**Related Prerequisites:**
- KB-008: How to create your first iOS app project using the App template
- KB-011: How to write your first "Hello World" SwiftUI view

## Steps

### Step 1: Understanding SwiftUI's Basic UI Elements

SwiftUI provides built-in views for displaying content and handling user interaction. The three most fundamental elements are:

**Text** - Displays static or dynamic text content
**Image** - Displays images from assets, system symbols, or external sources
**Button** - Creates tappable controls that trigger actions

All three conform to the `View` protocol and can be customized with modifiers.

```swift
// Basic structure for any SwiftUI view element
struct ContentView: View {
    var body: some View {
        // UI elements go here
    }
}
```

### Step 2: Adding Text Elements

The `Text` view displays one or more lines of read-only text. It's the simplest and most commonly used UI element in SwiftUI.

#### Basic Text Usage

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        Text("Welcome to SwiftUI")
    }
}
```

#### Customizing Text with Modifiers

SwiftUI provides numerous modifiers to customize text appearance:

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 20) {
            // Basic text
            Text("Simple Text")

            // Large title with bold weight
            Text("Large Title")
                .font(.largeTitle)
                .fontWeight(.bold)

            // Colored text
            Text("Blue Text")
                .foregroundColor(.blue)

            // Multiple modifiers combined
            Text("Styled Text")
                .font(.title2)
                .fontWeight(.semibold)
                .foregroundColor(.purple)
                .italic()

            // Custom font size
            Text("Custom Size")
                .font(.system(size: 24))
        }
        .padding()
    }
}
```

#### Common Text Modifiers

| Modifier | Purpose | Example |
|----------|---------|---------|
| `.font()` | Sets text size and style | `.font(.title)` |
| `.fontWeight()` | Adjusts text weight | `.fontWeight(.bold)` |
| `.foregroundColor()` | Changes text color | `.foregroundColor(.red)` |
| `.italic()` | Makes text italic | `.italic()` |
| `.underline()` | Adds underline | `.underline()` |
| `.strikethrough()` | Adds strikethrough | `.strikethrough()` |
| `.multilineTextAlignment()` | Aligns multi-line text | `.multilineTextAlignment(.center)` |

#### Advanced Text Features

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 15) {
            // Multi-line text
            Text("This is a longer text that will automatically wrap to multiple lines when needed")
                .multilineTextAlignment(.center)
                .padding()

            // Text with background
            Text("Background Color")
                .padding()
                .background(Color.yellow)
                .cornerRadius(8)

            // Text with custom styling
            Text("Fancy Text")
                .font(.title)
                .fontWeight(.heavy)
                .foregroundColor(.white)
                .padding()
                .background(
                    LinearGradient(
                        colors: [.blue, .purple],
                        startPoint: .leading,
                        endPoint: .trailing
                    )
                )
                .cornerRadius(12)
        }
        .padding()
    }
}
```

### Step 3: Adding Image Elements

The `Image` view displays images in your SwiftUI interface. Images can come from your asset catalog, SF Symbols, or external sources.

#### Three Ways to Create Images

**1. Images from Assets**

First, add an image to your asset catalog (Assets.xcassets):
1. Open Assets.xcassets in Xcode
2. Click the **+** button at the bottom
3. Select **Image Set**
4. Drag your image file into the image well

```swift
struct ContentView: View {
    var body: some View {
        // Display image from Assets.xcassets
        Image("myImage")
            .resizable()
            .scaledToFit()
            .frame(width: 200, height: 200)
    }
}
```

**2. SF Symbols (System Icons)**

SF Symbols is Apple's comprehensive icon library with thousands of built-in symbols:

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 20) {
            // Basic SF Symbol
            Image(systemName: "star.fill")

            // Colored SF Symbol
            Image(systemName: "heart.fill")
                .foregroundColor(.red)

            // Sized SF Symbol
            Image(systemName: "cloud.sun.fill")
                .font(.system(size: 50))

            // Scaled SF Symbol
            Image(systemName: "figure.walk")
                .imageScale(.large)
                .foregroundColor(.blue)
        }
    }
}
```

**3. Decorative Images**

For images that don't convey important information to VoiceOver users:

```swift
Image(decorative: "backgroundPattern")
    .resizable()
    .scaledToFill()
```

#### Common Image Modifiers

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 20) {
            // Resizable image that fits
            Image(systemName: "photo")
                .resizable()
                .scaledToFit()
                .frame(width: 100, height: 100)

            // Resizable image that fills
            Image(systemName: "photo")
                .resizable()
                .scaledToFill()
                .frame(width: 100, height: 100)
                .clipped()

            // Circular image with border
            Image(systemName: "person.circle.fill")
                .resizable()
                .frame(width: 80, height: 80)
                .foregroundColor(.blue)
                .clipShape(Circle())
                .overlay(Circle().stroke(Color.white, lineWidth: 4))
                .shadow(radius: 10)

            // Image with aspect ratio
            Image(systemName: "rectangle.fill")
                .resizable()
                .aspectRatio(contentMode: .fit)
                .frame(height: 100)
                .foregroundColor(.green)
        }
        .padding()
    }
}
```

#### Important Image Modifiers

| Modifier | Purpose | Example |
|----------|---------|---------|
| `.resizable()` | Allows image to be resized | `.resizable()` |
| `.scaledToFit()` | Fits image within frame | `.scaledToFit()` |
| `.scaledToFill()` | Fills frame with image | `.scaledToFill()` |
| `.frame()` | Sets image dimensions | `.frame(width: 100, height: 100)` |
| `.aspectRatio()` | Maintains aspect ratio | `.aspectRatio(contentMode: .fit)` |
| `.clipShape()` | Clips to shape | `.clipShape(Circle())` |

**Pro Tip:** Use the SF Symbols app (free from Apple) to browse all available system symbols.

### Step 4: Adding Button Elements

Buttons are interactive UI elements that trigger actions when tapped. Every button has two components: an action and a label.

#### Basic Button Usage

```swift
struct ContentView: View {
    var body: some View {
        Button("Tap Me") {
            print("Button was tapped!")
        }
    }
}
```

#### Button with Action State

```swift
struct ContentView: View {
    @State private var tapCount = 0

    var body: some View {
        VStack(spacing: 20) {
            Text("Taps: \(tapCount)")
                .font(.largeTitle)

            Button("Tap Me") {
                tapCount += 1
            }
        }
    }
}
```

**Note:** The `@State` property wrapper enables the button to modify the view's state. Learn more in KB-022: How to use @State property wrapper for managing view state.

#### Button with Custom Label

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 20) {
            // Button with text label
            Button(action: {
                print("Simple button tapped")
            }) {
                Text("Simple Button")
            }

            // Button with image
            Button(action: {
                print("Icon button tapped")
            }) {
                Image(systemName: "plus.circle.fill")
                    .font(.system(size: 40))
            }

            // Button with text and image
            Button(action: {
                print("Combined button tapped")
            }) {
                HStack {
                    Image(systemName: "heart.fill")
                    Text("Like")
                }
            }

            // Button with Label (recommended for icon + text)
            Button(action: {
                print("Label button tapped")
            }) {
                Label("Share", systemImage: "square.and.arrow.up")
            }
        }
    }
}
```

#### Styling Buttons

SwiftUI provides several ways to style buttons:

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 20) {
            // Default button
            Button("Default Button") {
                print("Tapped")
            }

            // Bordered button
            Button("Bordered Button") {
                print("Tapped")
            }
            .buttonStyle(.bordered)

            // Bordered prominent button
            Button("Prominent Button") {
                print("Tapped")
            }
            .buttonStyle(.borderedProminent)

            // Custom styled button
            Button("Custom Styled Button") {
                print("Tapped")
            }
            .font(.headline)
            .foregroundColor(.white)
            .padding()
            .background(Color.blue)
            .cornerRadius(10)

            // Pill-shaped button
            Button("Pill Button") {
                print("Tapped")
            }
            .font(.title3)
            .fontWeight(.semibold)
            .foregroundColor(.white)
            .padding(.horizontal, 30)
            .padding(.vertical, 15)
            .background(
                LinearGradient(
                    colors: [.purple, .pink],
                    startPoint: .leading,
                    endPoint: .trailing
                )
            )
            .cornerRadius(25)

            // Button with shadow
            Button("Shadow Button") {
                print("Tapped")
            }
            .padding()
            .background(Color.green)
            .foregroundColor(.white)
            .cornerRadius(8)
            .shadow(color: .green.opacity(0.5), radius: 10, x: 0, y: 5)
        }
        .padding()
    }
}
```

#### Button Roles (iOS 15+)

SwiftUI provides semantic button roles for common actions:

```swift
struct ContentView: View {
    @State private var showingAlert = false

    var body: some View {
        VStack(spacing: 20) {
            // Destructive button (red)
            Button("Delete", role: .destructive) {
                print("Delete action")
            }
            .buttonStyle(.bordered)

            // Cancel button
            Button("Cancel", role: .cancel) {
                print("Cancel action")
            }
            .buttonStyle(.bordered)

            // Normal button
            Button("Save") {
                print("Save action")
            }
            .buttonStyle(.borderedProminent)
        }
    }
}
```

### Step 5: Combining Text, Image, and Button

Real apps combine these elements to create rich interfaces. Here's a practical example:

```swift
struct ContentView: View {
    @State private var likeCount = 42
    @State private var isLiked = false

    var body: some View {
        VStack(spacing: 25) {
            // Header with icon and title
            HStack {
                Image(systemName: "photo.artframe")
                    .font(.title)
                    .foregroundColor(.blue)

                Text("Photo Gallery")
                    .font(.largeTitle)
                    .fontWeight(.bold)
            }

            // Main image
            Image(systemName: "photo.fill")
                .resizable()
                .scaledToFit()
                .frame(height: 250)
                .foregroundColor(.gray)
                .background(Color.gray.opacity(0.1))
                .cornerRadius(15)

            // Description text
            Text("Beautiful sunset over the mountains")
                .font(.headline)
                .foregroundColor(.secondary)
                .multilineTextAlignment(.center)

            // Action buttons
            HStack(spacing: 30) {
                // Like button
                Button(action: {
                    isLiked.toggle()
                    if isLiked {
                        likeCount += 1
                    } else {
                        likeCount -= 1
                    }
                }) {
                    VStack {
                        Image(systemName: isLiked ? "heart.fill" : "heart")
                            .font(.title2)
                            .foregroundColor(isLiked ? .red : .gray)

                        Text("\(likeCount)")
                            .font(.caption)
                            .foregroundColor(.gray)
                    }
                }

                // Comment button
                Button(action: {
                    print("Comment tapped")
                }) {
                    VStack {
                        Image(systemName: "bubble.right")
                            .font(.title2)
                            .foregroundColor(.gray)

                        Text("24")
                            .font(.caption)
                            .foregroundColor(.gray)
                    }
                }

                // Share button
                Button(action: {
                    print("Share tapped")
                }) {
                    VStack {
                        Image(systemName: "square.and.arrow.up")
                            .font(.title2)
                            .foregroundColor(.gray)

                        Text("Share")
                            .font(.caption)
                            .foregroundColor(.gray)
                    }
                }
            }

            // Download button
            Button(action: {
                print("Download tapped")
            }) {
                Label("Download Photo", systemImage: "arrow.down.circle.fill")
                    .font(.headline)
                    .foregroundColor(.white)
                    .padding()
                    .frame(maxWidth: .infinity)
                    .background(Color.blue)
                    .cornerRadius(12)
            }
        }
        .padding()
    }
}
```

### Step 6: Using Live Preview to Test Your UI

Xcode 26.1's live preview lets you see changes instantly:

1. Open your ContentView.swift file
2. Ensure the `#Preview` macro is at the bottom:
   ```swift
   #Preview {
       ContentView()
   }
   ```
3. Press `Cmd + Option + Enter` to show the Canvas
4. Click **Resume** if the preview is paused
5. Make changes to your code and watch them appear instantly

**Preview Tips:**
- Test different device sizes in preview
- Preview updates automatically as you type
- Faster than rebuilding the entire app
- Great for experimenting with UI elements

### Step 7: Building a Complete Example

Let's create a practical login screen using all three elements:

```swift
struct LoginView: View {
    @State private var username = ""
    @State private var password = ""

    var body: some View {
        VStack(spacing: 30) {
            // App logo and title
            VStack(spacing: 15) {
                Image(systemName: "lock.shield.fill")
                    .resizable()
                    .scaledToFit()
                    .frame(width: 80, height: 80)
                    .foregroundColor(.blue)

                Text("Welcome Back")
                    .font(.largeTitle)
                    .fontWeight(.bold)

                Text("Sign in to continue")
                    .font(.subheadline)
                    .foregroundColor(.secondary)
            }
            .padding(.top, 50)

            Spacer()

            // Login button
            Button(action: {
                print("Login tapped with username: \(username)")
            }) {
                HStack {
                    Image(systemName: "arrow.right.circle.fill")
                    Text("Sign In")
                        .fontWeight(.semibold)
                }
                .font(.title3)
                .foregroundColor(.white)
                .frame(maxWidth: .infinity)
                .padding()
                .background(Color.blue)
                .cornerRadius(12)
            }

            // Secondary action buttons
            HStack(spacing: 20) {
                Button(action: {
                    print("Forgot password tapped")
                }) {
                    Text("Forgot Password?")
                        .font(.caption)
                        .foregroundColor(.blue)
                }

                Text("•")
                    .foregroundColor(.gray)

                Button(action: {
                    print("Create account tapped")
                }) {
                    Text("Create Account")
                        .font(.caption)
                        .foregroundColor(.blue)
                }
            }

            Spacer()

            // Footer
            Text("By signing in, you agree to our Terms & Privacy Policy")
                .font(.caption2)
                .foregroundColor(.secondary)
                .multilineTextAlignment(.center)
                .padding(.horizontal)
        }
        .padding()
    }
}

#Preview {
    LoginView()
}
```

**Note:** This example uses `@State` for interactivity. TextField and SecureField components are covered in future articles.

## Expected Results

After completing this tutorial, you should have:

✓ Understanding of SwiftUI's three fundamental UI elements
✓ Ability to create and customize Text views with modifiers
✓ Knowledge of three ways to display images (assets, SF Symbols, decorative)
✓ Skills to create interactive buttons with actions and custom styling
✓ Experience combining Text, Image, and Button to build real interfaces
✓ Working code examples you can modify and experiment with

**What you should see:**
- **In Canvas Preview:** Your UI elements displayed with all customizations applied
- **In Simulator:** Interactive buttons that respond to taps and update the interface
- **In Console:** Print statements from button actions (View > Debug Area > Activate Console)

## Troubleshooting

### Common Issue 1: Image Not Displaying from Assets

**Problem:** `Image("myImage")` doesn't show anything in the preview or simulator.
**Solution:**
1. Verify the image is in Assets.xcassets
2. Check the image name matches exactly (case-sensitive)
3. Ensure the image is added to the correct target
4. Try rebuilding: `Cmd + Shift + K` (Clean), then `Cmd + B` (Build)

```swift
// ❌ Wrong - typo in name
Image("MyIamge")  // Misspelled

// ✅ Correct - exact name from Assets
Image("MyImage")
```

### Common Issue 2: SF Symbol Not Found

**Problem:** `Image(systemName: "...")` displays a question mark or nothing.
**Solution:**
1. Verify the symbol name is correct using the SF Symbols app
2. Check that the symbol is available in your deployment target (some symbols are iOS 16+)
3. Ensure you're using `systemName:` parameter, not the default initializer

```swift
// ❌ Wrong - using default initializer for system symbol
Image("star.fill")  // Looks in Assets, not SF Symbols

// ✅ Correct - using systemName parameter
Image(systemName: "star.fill")
```

### Common Issue 3: Button Action Not Firing

**Problem:** Tapping a button doesn't trigger the action.
**Solution:**
1. Check that you're testing in Simulator (not just Preview for some interactions)
2. Ensure the button action closure is properly formatted
3. Add a print statement to verify the action is called
4. Check that no other view is blocking tap gestures

```swift
// ❌ Wrong - missing action braces
Button("Tap Me")
    print("This won't work")

// ✅ Correct - action in closure
Button("Tap Me") {
    print("This works!")
}
```

### Common Issue 4: Image Not Resizing

**Problem:** Image doesn't resize when you set a frame.
**Solution:** Add the `.resizable()` modifier before setting frame or aspect ratio.

```swift
// ❌ Wrong - missing resizable()
Image(systemName: "star")
    .frame(width: 100, height: 100)  // Won't resize

// ✅ Correct - includes resizable()
Image(systemName: "star")
    .resizable()
    .frame(width: 100, height: 100)
```

### Common Issue 5: Text Not Wrapping to Multiple Lines

**Problem:** Long text gets truncated with "..." instead of wrapping.
**Solution:** Set a frame width or use `.fixedSize()` with multiline alignment.

```swift
// ❌ Wrong - text truncates by default
Text("This is a very long text that should wrap to multiple lines")

// ✅ Correct - allows multiple lines
Text("This is a very long text that should wrap to multiple lines")
    .frame(maxWidth: .infinity)
    .multilineTextAlignment(.leading)

// ✅ Also correct - fixed size for wrapping
Text("This is a very long text that should wrap to multiple lines")
    .fixedSize(horizontal: false, vertical: true)
```

### Common Issue 6: Modifier Order Matters

**Problem:** Modifiers applied in wrong order produce unexpected results.
**Solution:** Remember that modifiers are applied from top to bottom. Background, padding, and border order affects the final appearance.

```swift
// ❌ Wrong order - padding outside background
Text("Hello")
    .background(Color.blue)
    .padding()  // Padding is blue, not background

// ✅ Correct order - padding inside background
Text("Hello")
    .padding()
    .background(Color.blue)  // Background includes padding
```

## Additional Tips

- **Start Simple:** Begin with basic Text, Image, and Button elements before adding complex styling

- **Use Modifiers Gradually:** Add one modifier at a time to see its effect

- **SF Symbols App:** Download the free SF Symbols app from Apple to browse all available icons and see their names

- **Color Options:** SwiftUI provides semantic colors like `.primary`, `.secondary`, `.accentColor` that adapt to light/dark mode automatically

- **Accessibility:** Use `Label` for buttons with icons and text to improve VoiceOver support:
  ```swift
  Button(action: {}) {
      Label("Save", systemImage: "square.and.arrow.down")
  }
  ```

- **Button States:** Buttons automatically show pressed states, but you can customize with `.buttonStyle()` or create custom ButtonStyle

- **Preview Multiple Variants:** Test different states in preview:
  ```swift
  #Preview("Light Mode") {
      ContentView()
  }

  #Preview("Dark Mode") {
      ContentView()
          .preferredColorScheme(.dark)
  }
  ```

- **Reusable Components:** Extract repeated UI patterns into separate views:
  ```swift
  struct CustomButton: View {
      let title: String
      let action: () -> Void

      var body: some View {
          Button(action: action) {
              Text(title)
                  .padding()
                  .background(Color.blue)
                  .foregroundColor(.white)
                  .cornerRadius(8)
          }
      }
  }
  ```

- **Experiment Safely:** Create a new SwiftUI View file (File > New > File > SwiftUI View) to test ideas without modifying your main code

- **Learn by Doing:** The best way to master these elements is to recreate screens from your favorite apps

## Related Articles

- KB-008: How to create your first iOS app project using the App template
- KB-011: How to write your first "Hello World" SwiftUI view
- KB-022: How to use @State property wrapper for managing view state
- KB-024: How to preview your SwiftUI views using Xcode Previews
- KB-025: How to use the #Playground macro to test code snippets anywhere

## Sources

- Text | Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/text (Accessed: November 17, 2025)
- Image | Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/image (Accessed: November 17, 2025)
- Button | Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/button (Accessed: November 17, 2025)
- Images | Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/images (Accessed: November 17, 2025)
- Text and symbol modifiers | Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/view-text-and-symbols (Accessed: November 17, 2025)
- ButtonStyle | Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/buttonstyle (Accessed: November 17, 2025)
- Controls and indicators | Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/controls-and-indicators (Accessed: November 17, 2025)
- Hacking with Swift - Buttons and images - https://www.hackingwithswift.com/books/ios-swiftui/buttons-and-images (Accessed: November 17, 2025)
- Code With Chris - SwiftUI Button Guide - https://codewithchris.com/swiftui-button/ (Accessed: November 17, 2025)
- Simple Swift Guide - How to create a Button in SwiftUI - https://simpleswiftguide.com/how-to-create-button-in-swiftui/ (Accessed: November 17, 2025)
- Medium - SwiftUI in iOS 26: New Features from WWDC 2025 - https://medium.com/@himalimarasinghe/swiftui-in-ios-26-whats-new-from-wwdc-2025-be6b4864ce05 (Accessed: November 17, 2025)
- Google Search Suggestion: "SwiftUI Text Image Button modifiers tutorial 2025"
- Google Search Suggestion: "SF Symbols SwiftUI Xcode 26 comprehensive guide"

---
*This article is part of the Claude Code Web knowledge base*
