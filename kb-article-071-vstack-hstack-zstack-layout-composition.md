# How to use VStack, HStack, and ZStack for layout composition

**Article ID:** KB-071
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 20-25 minutes

## Overview

This guide teaches you how to master SwiftUI's three fundamental layout containers: VStack (vertical stack), HStack (horizontal stack), and ZStack (depth stack). These containers are the building blocks of complex layouts in SwiftUI, allowing you to compose sophisticated user interfaces by combining simple views. You'll learn how to control spacing, alignment, and depth to create professional iOS app layouts in Xcode 26.1+.

## Prerequisites

Before working with layout stacks, ensure you have:

- **Xcode 26.1+:** Installed and verified on your Mac
- **Basic SwiftUI Knowledge:** Understanding of views and modifiers
- **iOS App Project:** A SwiftUI app project created and ready to edit
- **macOS:** Sequoia 15.6 or later

**Related Prerequisites:**
- KB-008: How to create your first iOS app project using the App template
- KB-011: How to write your first "Hello World" SwiftUI view
- KB-021: How to add UI elements to your SwiftUI view

## Steps

### Step 1: Understanding Stack Layout Containers

SwiftUI provides three container views that arrange their child views in different dimensions:

**VStack** - Arranges views vertically (top to bottom)
**HStack** - Arranges views horizontally (left to right)
**ZStack** - Overlays views along the z-axis (front to back)

Each stack accepts three key parameters:
- `alignment` - Controls how child views align within the stack
- `spacing` - Sets the distance between child views
- `content` - A ViewBuilder closure containing the child views

```swift
// Basic structure of each stack type
VStack(alignment: .leading, spacing: 10) {
    // Views arranged vertically
}

HStack(alignment: .center, spacing: 15) {
    // Views arranged horizontally
}

ZStack(alignment: .topLeading) {
    // Views layered on top of each other
}
```

### Step 2: Working with VStack (Vertical Stack)

VStack arranges views in a vertical column from top to bottom.

#### Basic VStack Usage

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            Text("First Item")
            Text("Second Item")
            Text("Third Item")
        }
    }
}
```

#### VStack Alignment Options

VStack supports horizontal alignment for its children:

```swift
struct ContentView: View {
    var body: some View {
        HStack(spacing: 40) {
            // Leading alignment (left)
            VStack(alignment: .leading, spacing: 8) {
                Text("Name:")
                    .fontWeight(.bold)
                Text("John Smith")
                Text("Location:")
                    .fontWeight(.bold)
                Text("San Francisco, CA")
            }
            .frame(width: 150)
            .padding()
            .background(Color.blue.opacity(0.1))
            .cornerRadius(8)

            // Center alignment (default)
            VStack(alignment: .center, spacing: 8) {
                Text("Name:")
                    .fontWeight(.bold)
                Text("John Smith")
                Text("Location:")
                    .fontWeight(.bold)
                Text("San Francisco, CA")
            }
            .frame(width: 150)
            .padding()
            .background(Color.green.opacity(0.1))
            .cornerRadius(8)

            // Trailing alignment (right)
            VStack(alignment: .trailing, spacing: 8) {
                Text("Name:")
                    .fontWeight(.bold)
                Text("John Smith")
                Text("Location:")
                    .fontWeight(.bold)
                Text("San Francisco, CA")
            }
            .frame(width: 150)
            .padding()
            .background(Color.orange.opacity(0.1))
            .cornerRadius(8)
        }
    }
}
```

**Available VStack Alignments:**
- `.leading` - Left alignment
- `.center` - Center alignment (default)
- `.trailing` - Right alignment

#### Controlling VStack Spacing

```swift
struct ContentView: View {
    var body: some View {
        HStack(spacing: 30) {
            // No spacing
            VStack(spacing: 0) {
                Rectangle().fill(Color.blue).frame(height: 40)
                Rectangle().fill(Color.green).frame(height: 40)
                Rectangle().fill(Color.orange).frame(height: 40)
            }
            .frame(width: 80)

            // Default spacing (8-10 points)
            VStack {
                Rectangle().fill(Color.blue).frame(height: 40)
                Rectangle().fill(Color.green).frame(height: 40)
                Rectangle().fill(Color.orange).frame(height: 40)
            }
            .frame(width: 80)

            // Custom spacing (20 points)
            VStack(spacing: 20) {
                Rectangle().fill(Color.blue).frame(height: 40)
                Rectangle().fill(Color.green).frame(height: 40)
                Rectangle().fill(Color.orange).frame(height: 40)
            }
            .frame(width: 80)
        }
    }
}
```

#### Dynamic Spacing with Spacer

```swift
struct ContentView: View {
    var body: some View {
        VStack {
            Text("Header")
                .font(.largeTitle)
                .fontWeight(.bold)

            Spacer()  // Pushes content to top and bottom

            Text("Middle Content")
                .font(.headline)

            Spacer()

            Text("Footer")
                .font(.caption)
                .foregroundColor(.secondary)
        }
        .padding()
        .frame(height: 400)
        .background(Color.gray.opacity(0.1))
    }
}
```

### Step 3: Working with HStack (Horizontal Stack)

HStack arranges views in a horizontal row from left to right.

#### Basic HStack Usage

```swift
struct ContentView: View {
    var body: some View {
        HStack {
            Image(systemName: "person.circle.fill")
                .font(.title)
            Text("Profile")
            Image(systemName: "chevron.right")
                .font(.caption)
        }
    }
}
```

#### HStack Alignment Options

HStack supports vertical alignment for its children:

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 30) {
            // Top alignment
            HStack(alignment: .top, spacing: 15) {
                Image(systemName: "star.fill")
                    .font(.system(size: 50))
                    .foregroundColor(.yellow)

                VStack(alignment: .leading) {
                    Text("Top Aligned")
                        .font(.headline)
                    Text("The icon aligns with the top of the tallest view")
                        .font(.caption)
                        .foregroundColor(.secondary)
                }
            }
            .frame(height: 80)
            .padding()
            .background(Color.blue.opacity(0.1))
            .cornerRadius(8)

            // Center alignment (default)
            HStack(alignment: .center, spacing: 15) {
                Image(systemName: "heart.fill")
                    .font(.system(size: 50))
                    .foregroundColor(.red)

                VStack(alignment: .leading) {
                    Text("Center Aligned")
                        .font(.headline)
                    Text("The icon centers vertically with other views")
                        .font(.caption)
                        .foregroundColor(.secondary)
                }
            }
            .frame(height: 80)
            .padding()
            .background(Color.green.opacity(0.1))
            .cornerRadius(8)

            // Bottom alignment
            HStack(alignment: .bottom, spacing: 15) {
                Image(systemName: "bolt.fill")
                    .font(.system(size: 50))
                    .foregroundColor(.orange)

                VStack(alignment: .leading) {
                    Text("Bottom Aligned")
                        .font(.headline)
                    Text("The icon aligns with the bottom edge")
                        .font(.caption)
                        .foregroundColor(.secondary)
                }
            }
            .frame(height: 80)
            .padding()
            .background(Color.orange.opacity(0.1))
            .cornerRadius(8)
        }
        .padding()
    }
}
```

**Available HStack Alignments:**
- `.top` - Top alignment
- `.center` - Center alignment (default)
- `.bottom` - Bottom alignment
- `.firstTextBaseline` - Aligns to first text baseline
- `.lastTextBaseline` - Aligns to last text baseline

#### Text Baseline Alignment

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 30) {
            // First text baseline alignment
            HStack(alignment: .firstTextBaseline, spacing: 10) {
                Text("Large")
                    .font(.system(size: 40))
                Text("Medium")
                    .font(.system(size: 24))
                Text("Small")
                    .font(.system(size: 16))
            }
            .padding()
            .background(Color.blue.opacity(0.1))

            // Last text baseline alignment
            HStack(alignment: .lastTextBaseline, spacing: 10) {
                VStack(alignment: .leading) {
                    Text("First line")
                    Text("Second line")
                }
                .font(.caption)

                Text("Aligns with last baseline")
                    .font(.title2)
            }
            .padding()
            .background(Color.green.opacity(0.1))
        }
    }
}
```

#### Distributing Space with Spacer

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 20) {
            // Spacer pushes items apart
            HStack {
                Text("Left")
                Spacer()
                Text("Right")
            }
            .padding()
            .background(Color.blue.opacity(0.1))

            // Multiple spacers distribute evenly
            HStack {
                Text("Left")
                Spacer()
                Text("Center")
                Spacer()
                Text("Right")
            }
            .padding()
            .background(Color.green.opacity(0.1))

            // Spacer with minimum length
            HStack(spacing: 0) {
                Text("Item")
                Spacer(minLength: 50)
                Text("Item")
                Spacer(minLength: 50)
                Text("Item")
            }
            .padding()
            .background(Color.orange.opacity(0.1))
        }
        .padding()
    }
}
```

### Step 4: Working with ZStack (Depth Stack)

ZStack layers views on top of each other along the z-axis, with later views appearing in front.

#### Basic ZStack Usage

```swift
struct ContentView: View {
    var body: some View {
        ZStack {
            // Background layer
            Rectangle()
                .fill(Color.blue)
                .frame(width: 200, height: 200)

            // Middle layer
            Circle()
                .fill(Color.green)
                .frame(width: 150, height: 150)

            // Foreground layer
            Text("Front")
                .font(.title)
                .fontWeight(.bold)
                .foregroundColor(.white)
        }
    }
}
```

#### ZStack Alignment Options

ZStack supports both horizontal and vertical alignment:

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 20) {
            // Top leading alignment
            ZStack(alignment: .topLeading) {
                Rectangle()
                    .fill(Color.blue.opacity(0.3))
                    .frame(width: 150, height: 150)

                Text("Top Leading")
                    .padding(8)
                    .background(Color.white)
                    .cornerRadius(4)
            }

            // Bottom trailing alignment
            ZStack(alignment: .bottomTrailing) {
                Rectangle()
                    .fill(Color.green.opacity(0.3))
                    .frame(width: 150, height: 150)

                Text("Bottom Trailing")
                    .padding(8)
                    .background(Color.white)
                    .cornerRadius(4)
            }

            // Center alignment (default)
            ZStack(alignment: .center) {
                Rectangle()
                    .fill(Color.orange.opacity(0.3))
                    .frame(width: 150, height: 150)

                Text("Center")
                    .padding(8)
                    .background(Color.white)
                    .cornerRadius(4)
            }
        }
    }
}
```

**Available ZStack Alignments:**
- `.topLeading`, `.top`, `.topTrailing`
- `.leading`, `.center` (default), `.trailing`
- `.bottomLeading`, `.bottom`, `.bottomTrailing`

#### Creating Overlays and Badges

```swift
struct ContentView: View {
    var body: some View {
        HStack(spacing: 30) {
            // Notification badge
            ZStack(alignment: .topTrailing) {
                Image(systemName: "bell.fill")
                    .font(.system(size: 40))
                    .foregroundColor(.blue)

                Circle()
                    .fill(Color.red)
                    .frame(width: 20, height: 20)
                    .overlay(
                        Text("3")
                            .font(.system(size: 12))
                            .fontWeight(.bold)
                            .foregroundColor(.white)
                    )
                    .offset(x: 8, y: -8)
            }

            // Image with text overlay
            ZStack(alignment: .bottom) {
                Image(systemName: "photo.fill")
                    .resizable()
                    .scaledToFit()
                    .frame(width: 120, height: 120)
                    .foregroundColor(.gray)

                Text("Sunset")
                    .font(.caption)
                    .fontWeight(.semibold)
                    .foregroundColor(.white)
                    .padding(.horizontal, 12)
                    .padding(.vertical, 6)
                    .background(Color.black.opacity(0.7))
                    .cornerRadius(4)
                    .padding(8)
            }

            // Profile with status indicator
            ZStack(alignment: .bottomTrailing) {
                Circle()
                    .fill(Color.gray.opacity(0.3))
                    .frame(width: 80, height: 80)
                    .overlay(
                        Image(systemName: "person.fill")
                            .font(.system(size: 40))
                            .foregroundColor(.gray)
                    )

                Circle()
                    .fill(Color.green)
                    .frame(width: 20, height: 20)
                    .overlay(
                        Circle()
                            .stroke(Color.white, lineWidth: 2)
                    )
                    .offset(x: 2, y: 2)
            }
        }
        .padding()
    }
}
```

### Step 5: Nesting Stacks for Complex Layouts

Real-world layouts combine multiple stacks to create sophisticated interfaces.

#### Card Layout with Nested Stacks

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 20) {
            // Profile card combining all three stack types
            ZStack(alignment: .topTrailing) {
                // Card background
                RoundedRectangle(cornerRadius: 16)
                    .fill(Color.white)
                    .shadow(color: .gray.opacity(0.2), radius: 10, x: 0, y: 5)

                // Card content
                VStack(alignment: .leading, spacing: 16) {
                    // Header with avatar and info
                    HStack(alignment: .center, spacing: 15) {
                        // Avatar with status
                        ZStack(alignment: .bottomTrailing) {
                            Circle()
                                .fill(
                                    LinearGradient(
                                        colors: [.blue, .purple],
                                        startPoint: .topLeading,
                                        endPoint: .bottomTrailing
                                    )
                                )
                                .frame(width: 60, height: 60)
                                .overlay(
                                    Image(systemName: "person.fill")
                                        .foregroundColor(.white)
                                        .font(.title2)
                                )

                            Circle()
                                .fill(Color.green)
                                .frame(width: 16, height: 16)
                                .overlay(
                                    Circle()
                                        .stroke(Color.white, lineWidth: 2)
                                )
                        }

                        // User info
                        VStack(alignment: .leading, spacing: 4) {
                            Text("Sarah Johnson")
                                .font(.headline)
                                .fontWeight(.bold)

                            Text("iOS Developer")
                                .font(.subheadline)
                                .foregroundColor(.secondary)

                            HStack(spacing: 4) {
                                Image(systemName: "mappin.circle.fill")
                                    .font(.caption)
                                    .foregroundColor(.blue)
                                Text("San Francisco, CA")
                                    .font(.caption)
                                    .foregroundColor(.secondary)
                            }
                        }

                        Spacer()
                    }

                    Divider()

                    // Stats section
                    HStack(spacing: 0) {
                        // Projects stat
                        VStack(spacing: 4) {
                            Text("24")
                                .font(.title2)
                                .fontWeight(.bold)
                            Text("Projects")
                                .font(.caption)
                                .foregroundColor(.secondary)
                        }
                        .frame(maxWidth: .infinity)

                        Divider()
                            .frame(height: 40)

                        // Followers stat
                        VStack(spacing: 4) {
                            Text("1.2K")
                                .font(.title2)
                                .fontWeight(.bold)
                            Text("Followers")
                                .font(.caption)
                                .foregroundColor(.secondary)
                        }
                        .frame(maxWidth: .infinity)

                        Divider()
                            .frame(height: 40)

                        // Following stat
                        VStack(spacing: 4) {
                            Text("843")
                                .font(.title2)
                                .fontWeight(.bold)
                            Text("Following")
                                .font(.caption)
                                .foregroundColor(.secondary)
                        }
                        .frame(maxWidth: .infinity)
                    }

                    // Action buttons
                    HStack(spacing: 12) {
                        Button(action: {}) {
                            HStack {
                                Image(systemName: "person.badge.plus")
                                Text("Follow")
                            }
                            .frame(maxWidth: .infinity)
                            .padding(.vertical, 12)
                            .background(Color.blue)
                            .foregroundColor(.white)
                            .cornerRadius(8)
                        }

                        Button(action: {}) {
                            HStack {
                                Image(systemName: "message")
                                Text("Message")
                            }
                            .frame(maxWidth: .infinity)
                            .padding(.vertical, 12)
                            .background(Color.gray.opacity(0.1))
                            .foregroundColor(.blue)
                            .cornerRadius(8)
                        }
                    }
                }
                .padding()

                // Menu button overlay
                Button(action: {}) {
                    Image(systemName: "ellipsis")
                        .font(.title3)
                        .foregroundColor(.gray)
                        .padding(8)
                }
            }
            .frame(maxWidth: 400)
            .padding()
        }
    }
}
```

#### Grid-like Layout with Stacks

```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 20) {
            Text("Photo Gallery")
                .font(.largeTitle)
                .fontWeight(.bold)

            // 2x2 grid using nested HStacks and VStacks
            VStack(spacing: 15) {
                HStack(spacing: 15) {
                    PhotoCell(icon: "photo", title: "Sunset")
                    PhotoCell(icon: "photo", title: "Mountains")
                }

                HStack(spacing: 15) {
                    PhotoCell(icon: "photo", title: "Ocean")
                    PhotoCell(icon: "photo", title: "Forest")
                }
            }
            .padding()
        }
    }
}

struct PhotoCell: View {
    let icon: String
    let title: String

    var body: some View {
        ZStack(alignment: .bottom) {
            Rectangle()
                .fill(Color.gray.opacity(0.2))
                .frame(width: 150, height: 150)
                .cornerRadius(12)
                .overlay(
                    Image(systemName: icon)
                        .font(.system(size: 40))
                        .foregroundColor(.gray)
                )

            Text(title)
                .font(.caption)
                .fontWeight(.semibold)
                .foregroundColor(.white)
                .frame(maxWidth: .infinity)
                .padding(.vertical, 8)
                .background(Color.black.opacity(0.6))
                .cornerRadius(12, corners: [.bottomLeft, .bottomRight])
        }
    }
}

// Extension for selective corner radius
extension View {
    func cornerRadius(_ radius: CGFloat, corners: UIRectCorner) -> some View {
        clipShape(RoundedCorner(radius: radius, corners: corners))
    }
}

struct RoundedCorner: Shape {
    var radius: CGFloat = .infinity
    var corners: UIRectCorner = .allCorners

    func path(in rect: CGRect) -> Path {
        let path = UIBezierPath(
            roundedRect: rect,
            byRoundingCorners: corners,
            cornerRadii: CGSize(width: radius, height: radius)
        )
        return Path(path.cgPath)
    }
}
```

### Step 6: Advanced Layout Techniques

#### Custom Spacing Between Specific Views

In iOS 16+, you can control spacing between individual views:

```swift
struct ContentView: View {
    var body: some View {
        VStack {
            Text("Title")
                .font(.largeTitle)
                .fontWeight(.bold)

            Text("Subtitle")
                .font(.headline)
                .padding(.top, 5)  // Custom spacing from title

            Text("Body text goes here. This is a longer description.")
                .font(.body)
                .padding(.top, 20)  // Larger spacing from subtitle

            Button("Action") {}
                .buttonStyle(.borderedProminent)
                .padding(.top, 30)  // Even larger spacing
        }
        .padding()
    }
}
```

#### Using GeometryReader with Stacks

Create responsive layouts that adapt to available space:

```swift
struct ContentView: View {
    var body: some View {
        GeometryReader { geometry in
            VStack(spacing: 0) {
                // Header - 20% of height
                ZStack {
                    Rectangle()
                        .fill(Color.blue)
                    Text("Header")
                        .font(.title)
                        .foregroundColor(.white)
                }
                .frame(height: geometry.size.height * 0.2)

                // Content - 60% of height
                HStack(spacing: 0) {
                    // Left sidebar - 30% of width
                    ZStack {
                        Rectangle()
                            .fill(Color.gray.opacity(0.2))
                        Text("Sidebar")
                    }
                    .frame(width: geometry.size.width * 0.3)

                    // Main content - 70% of width
                    ZStack {
                        Rectangle()
                            .fill(Color.white)
                        Text("Main Content")
                    }
                    .frame(width: geometry.size.width * 0.7)
                }
                .frame(height: geometry.size.height * 0.6)

                // Footer - 20% of height
                ZStack {
                    Rectangle()
                        .fill(Color.gray)
                    Text("Footer")
                        .foregroundColor(.white)
                }
                .frame(height: geometry.size.height * 0.2)
            }
        }
        .ignoresSafeArea()
    }
}
```

#### Stack Layout Priority

Control how views compete for space using `layoutPriority()`:

```swift
struct ContentView: View {
    var body: some View {
        HStack {
            Text("Short")
                .padding()
                .background(Color.blue.opacity(0.2))

            Text("This is a very long text that would normally get truncated")
                .layoutPriority(1)  // Higher priority gets more space
                .padding()
                .background(Color.green.opacity(0.2))

            Text("Medium length text")
                .padding()
                .background(Color.orange.opacity(0.2))
        }
        .lineLimit(1)
        .frame(width: 350)
    }
}
```

### Step 7: Performance Considerations and Best Practices

#### Limiting Stack Children

Stacks can hold up to 10 direct children. For more views, use `Group` or extract to separate views:

```swift
struct ContentView: View {
    var body: some View {
        VStack {
            // First group of views
            Group {
                Text("View 1")
                Text("View 2")
                Text("View 3")
                Text("View 4")
                Text("View 5")
            }

            // Second group of views
            Group {
                Text("View 6")
                Text("View 7")
                Text("View 8")
                Text("View 9")
                Text("View 10")
            }

            // More views in additional groups...
        }
    }
}
```

**Better approach:** Extract repeated patterns to custom views:

```swift
struct ContentView: View {
    let items = ["Item 1", "Item 2", "Item 3", "Item 4", "Item 5",
                 "Item 6", "Item 7", "Item 8", "Item 9", "Item 10"]

    var body: some View {
        VStack {
            ForEach(items, id: \.self) { item in
                ItemRow(title: item)
            }
        }
    }
}

struct ItemRow: View {
    let title: String

    var body: some View {
        HStack {
            Image(systemName: "circle.fill")
            Text(title)
            Spacer()
        }
        .padding()
    }
}
```

#### Avoiding Over-nesting

Deeply nested stacks can impact performance and readability:

```swift
// ❌ Bad: Too much nesting
VStack {
    HStack {
        VStack {
            HStack {
                VStack {
                    // Content...
                }
            }
        }
    }
}

// ✅ Good: Extract to separate views
VStack {
    HeaderView()
    ContentView()
    FooterView()
}
```

## Expected Results

After completing this tutorial, you should have:

✓ Comprehensive understanding of VStack, HStack, and ZStack
✓ Ability to control alignment and spacing in all three stack types
✓ Skills to nest stacks for complex layouts
✓ Knowledge of advanced techniques like layout priority and GeometryReader
✓ Best practices for performant and maintainable stack-based layouts
✓ Working examples of real-world layouts (cards, grids, overlays)

**What you should see:**
- **In Canvas Preview:** Complex layouts with properly aligned and spaced elements
- **In Simulator:** Responsive layouts that adapt to different screen sizes
- **In Code:** Clean, readable layout code using stack composition

## Troubleshooting

### Common Issue 1: Views Not Aligning as Expected

**Problem:** Child views don't align properly within a stack.
**Solution:**
1. Check the stack's alignment parameter matches your intent
2. Verify child views don't have conflicting frame modifiers
3. Remember alignment affects the perpendicular axis (horizontal for VStack, vertical for HStack)

```swift
// ❌ Wrong: Alignment doesn't match intention
VStack(alignment: .center) {
    Text("Left aligned?")  // Will be centered, not left
}

// ✅ Correct: Proper alignment
VStack(alignment: .leading) {
    Text("Left aligned")  // Now properly left-aligned
}
```

### Common Issue 2: Spacing Inconsistencies

**Problem:** Spacing between views appears inconsistent or wrong.
**Solution:**
1. Check for implicit padding from parent views
2. Verify spacing parameter is set correctly
3. Look for individual view modifiers that add padding
4. Use spacing: 0 and add explicit padding for precise control

```swift
// ❌ Wrong: Mixed spacing approaches
VStack(spacing: 10) {
    Text("Item 1")
        .padding(.bottom, 20)  // Conflicts with stack spacing
    Text("Item 2")
}

// ✅ Correct: Consistent spacing
VStack(spacing: 20) {
    Text("Item 1")
    Text("Item 2")
}
```

### Common Issue 3: ZStack Content Hidden

**Problem:** Views in ZStack are not visible or appear behind others.
**Solution:**
1. Remember that later views in ZStack appear in front
2. Check for overlapping frames that completely cover earlier views
3. Use `.zIndex()` modifier to explicitly control depth ordering
4. Verify colors and opacity aren't hiding content

```swift
// ❌ Wrong: Background covers everything
ZStack {
    Text("I'm hidden!")
    Rectangle()
        .fill(Color.blue)
        .frame(width: 300, height: 300)
}

// ✅ Correct: Background first
ZStack {
    Rectangle()
        .fill(Color.blue)
        .frame(width: 300, height: 300)
    Text("Now visible!")
        .foregroundColor(.white)
}

// ✅ Also correct: Using zIndex
ZStack {
    Text("Visible with zIndex")
        .zIndex(1)  // Brings to front
    Rectangle()
        .fill(Color.blue)
        .frame(width: 300, height: 300)
}
```

### Common Issue 4: Stack Taking Too Much or Too Little Space

**Problem:** Stack doesn't fill available space or takes more than needed.
**Solution:**
1. Stacks size themselves to fit their content by default
2. Use `.frame(maxWidth: .infinity)` or `.frame(maxHeight: .infinity)` to expand
3. Use `Spacer()` within stacks to push content and fill space
4. Apply background colors temporarily to visualize actual sizes

```swift
// ❌ Wrong: Stack doesn't fill width
HStack {
    Text("Left")
    Text("Right")
}

// ✅ Correct: Stack fills width with spacer
HStack {
    Text("Left")
    Spacer()
    Text("Right")
}
.frame(maxWidth: .infinity)

// ✅ Also correct: Using frame
HStack {
    Text("Left")
    Spacer()
    Text("Right")
}
.frame(maxWidth: .infinity)
```

### Common Issue 5: "Extra argument in call" Error

**Problem:** Compiler error when creating stacks with many children.
**Solution:** Stacks support maximum 10 direct children. Use `Group`, `ForEach`, or extract to custom views.

```swift
// ❌ Wrong: More than 10 children
VStack {
    Text("1")
    Text("2")
    // ... 11+ text views
}  // Compiler error

// ✅ Correct: Using Group
VStack {
    Group {
        Text("1")
        Text("2")
        // ... up to 10
    }
    Group {
        Text("11")
        // ... more
    }
}

// ✅ Better: Using ForEach
VStack {
    ForEach(1...20, id: \.self) { number in
        Text("\(number)")
    }
}
```

## Additional Tips

- **Use Divider:** Add visual separation between stack elements with `Divider()`:
  ```swift
  VStack {
      Text("Section 1")
      Divider()
      Text("Section 2")
  }
  ```

- **Spacer Flexibility:** `Spacer()` with `minLength` ensures minimum spacing:
  ```swift
  HStack {
      Text("Left")
      Spacer(minLength: 50)  // At least 50 points
      Text("Right")
  }
  ```

- **Preview Different Sizes:** Test your layouts on multiple devices:
  ```swift
  #Preview("iPhone 15 Pro") {
      ContentView()
  }

  #Preview("iPad") {
      ContentView()
          .previewDevice("iPad Pro (12.9-inch)")
  }
  ```

- **Accessibility:** Stacks automatically support Dynamic Type. Test with larger text sizes:
  ```swift
  #Preview("Large Text") {
      ContentView()
          .environment(\.sizeCategory, .accessibilityExtraExtraLarge)
  }
  ```

- **Background Trick:** Temporarily add backgrounds to understand view bounds:
  ```swift
  VStack {
      Text("Debug")
          .background(Color.red.opacity(0.3))
  }
  .background(Color.blue.opacity(0.3))
  ```

- **Fixed vs Flexible:** Use `.fixedSize()` to prevent views from expanding:
  ```swift
  Text("Won't expand")
      .fixedSize()
  ```

- **Combine with ScrollView:** For long content, wrap stacks in ScrollView:
  ```swift
  ScrollView {
      VStack(spacing: 20) {
          ForEach(1...50, id: \.self) { item in
              Text("Item \(item)")
          }
      }
  }
  ```

## Related Articles

- KB-008: How to create your first iOS app project using the App template
- KB-011: How to write your first "Hello World" SwiftUI view
- KB-021: How to add UI elements to your SwiftUI view (Text, Image, Button)
- KB-022: How to use @State property wrapper for managing view state
- KB-024: How to preview your SwiftUI views using Xcode Previews

## Sources

- VStack | Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/vstack (Accessed: November 17, 2025)
- HStack | Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/hstack (Accessed: November 17, 2025)
- ZStack | Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/zstack (Accessed: November 17, 2025)
- Building layouts with stack views | Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/building-layouts-with-stack-views (Accessed: November 17, 2025)
- Organizing and aligning content with stacks | Apple Developer Documentation - https://developer.apple.com/tutorials/swiftui-concepts/organizing-and-aligning-content-with-stacks (Accessed: November 17, 2025)
- Using stacks to arrange views | Apple Developer Documentation - https://developer.apple.com/tutorials/app-dev-training/using-stacks-to-arrange-views (Accessed: November 17, 2025)
- SwiftUI Layout System - WWDC Sessions - https://developer.apple.com/videos/ (Accessed: November 17, 2025)
- Hacking with Swift - SwiftUI Stacks Tutorial - https://www.hackingwithswift.com/books/ios-swiftui/using-stacks-to-arrange-views (Accessed: November 17, 2025)
- Google Search Suggestion: "SwiftUI VStack HStack ZStack layout composition iOS 18 best practices"
- Google Search Suggestion: "SwiftUI stack alignment spacing Xcode 26 comprehensive guide"

---
*This article is part of the Xcode v26.1+ knowledge base*
