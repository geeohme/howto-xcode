# How to create adaptive layouts that work across iPhone and iPad with AI help

**Article ID:** KB-076
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 30-35 minutes

## Overview

Creating adaptive layouts in SwiftUI that seamlessly work across iPhone and iPad requires understanding size classes, responsive layout techniques, and device-specific design patterns. Xcode 26.1+ integrates AI coding assistants (ChatGPT and Claude) that can generate sophisticated adaptive layouts using ViewThatFits, size class detection, GeometryReader, and conditional view hierarchies. This guide teaches you how to leverage AI to build truly universal interfaces that automatically adjust between compact (iPhone) and regular (iPad) size classes, handle orientation changes gracefully, and provide optimal user experiences on any screen size.

## Prerequisites

- Xcode 26.1 or later installed
- macOS 15 (Sequoia) or later on Apple Silicon Mac (M1 or newer)
- Apple Intelligence enabled in system settings
- ChatGPT or Claude configured in Xcode Intelligence settings (see KB-031, KB-043)
- Understanding of basic SwiftUI views and modifiers
- Familiarity with VStack, HStack, and ZStack layout containers
- Knowledge of SwiftUI environment values and @Environment property wrapper
- iOS 18.2+ deployment target for your project

**Related Prerequisites:**
- KB-011: How to write your first Hello World SwiftUI view
- KB-021: How to add UI elements to a SwiftUI view
- KB-031: How to access the Coding Assistant in Xcode 26 (ChatGPT)
- KB-037: How to generate SwiftUI views from natural language descriptions using ChatGPT
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings

## Steps

### Step 1: Understand Size Classes and Adaptive Design

Before using AI to generate adaptive layouts, understand the foundational concepts:

**What are size classes?**

Size classes are Apple's trait-based system for describing the available screen space. SwiftUI provides two environment values:
- **horizontalSizeClass:** Describes horizontal space (.compact or .regular)
- **verticalSizeClass:** Describes vertical space (.compact or .regular)

**Size class patterns by device:**

**iPhone:**
- Portrait: compact width, regular height
- Landscape (standard models): compact width, compact height
- Landscape (Plus/Pro Max models): regular width, compact height

**iPad:**
- All orientations (full screen): regular width, regular height
- Split View (1/3 screen): compact width, regular height
- Split View (1/2 or 2/3 screen): regular width, regular height

**Why adaptive layouts matter:**

1. **Single codebase:** Build one app that works beautifully on all devices
2. **iPad optimization:** Leverage larger screens with side-by-side layouts, additional detail panes
3. **User experience:** Provide the best interface for each device size
4. **Future-proofing:** Automatically adapt to new devices and screen sizes
5. **Accessibility:** Support landscape orientation, external displays, and Stage Manager on iPad

**Modern SwiftUI adaptive techniques (iOS 18.2+):**
- **Size class detection:** Using @Environment(\.horizontalSizeClass) and @Environment(\.verticalSizeClass)
- **ViewThatFits:** Automatically selects the first view that fits in available space
- **GeometryReader:** Measures available space and creates proportional layouts
- **Conditional view hierarchies:** Switch between HStack and VStack based on size classes
- **NavigationSplitView:** Three-column layouts that automatically collapse on smaller screens

### Step 2: Access the AI Coding Assistant

Open your AI assistant to help generate adaptive layouts:

1. **Launch Xcode 26.1+** and open your project
2. **Create or open a SwiftUI view file** where you want adaptive layout
3. **Open the Coding Assistant:**
   - Press `Cmd + I` (inline assistant)
   - Or press `Cmd + Shift + I` (side panel chat)
   - Or navigate to **Editor → Coding Assistant**

4. **Choose the appropriate AI model:**
   - **Claude Sonnet 4:** Best for complex multi-column layouts, architectural discussions, and detailed explanations
   - **ChatGPT GPT-5:** Good for quick adaptive layout implementations and standard patterns
   - **Switch models** using the dropdown if needed (see KB-046)

The AI will have context about your project structure and can reference your existing views.

### Step 3: Generate Basic Size Class-Aware Layouts

Start by asking AI to create layouts that respond to size classes:

#### Prompt Pattern 1: Horizontal vs Vertical Stack

```
Create a SwiftUI view that displays a profile card with an image and details.
Use @Environment(\.horizontalSizeClass) to detect the size class. On iPhone
(compact width), arrange elements vertically using VStack. On iPad (regular
width), arrange them horizontally using HStack with the image on the left
and details on the right. Include smooth transitions when size class changes.
```

**Expected AI-generated output:**

```swift
import SwiftUI

struct AdaptiveProfileCard: View {
    @Environment(\.horizontalSizeClass) var horizontalSizeClass

    let imageName: String
    let name: String
    let title: String
    let bio: String

    var body: some View {
        Group {
            if horizontalSizeClass == .compact {
                // iPhone portrait layout - vertical
                VStack(spacing: 16) {
                    profileImage
                    profileDetails
                }
            } else {
                // iPad or iPhone landscape layout - horizontal
                HStack(spacing: 24) {
                    profileImage
                    profileDetails
                }
            }
        }
        .padding()
        .background(Color(.systemBackground))
        .cornerRadius(12)
        .shadow(radius: 2)
        .animation(.spring(response: 0.3), value: horizontalSizeClass)
    }

    var profileImage: some View {
        Image(imageName)
            .resizable()
            .scaledToFill()
            .frame(width: 120, height: 120)
            .clipShape(Circle())
    }

    var profileDetails: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text(name)
                .font(.title2)
                .fontWeight(.bold)

            Text(title)
                .font(.subheadline)
                .foregroundColor(.secondary)

            Text(bio)
                .font(.body)
                .foregroundColor(.secondary)
        }
        .frame(maxWidth: .infinity, alignment: .leading)
    }
}

#Preview("iPhone") {
    AdaptiveProfileCard(
        imageName: "person.circle.fill",
        name: "Sarah Chen",
        title: "iOS Developer",
        bio: "Building beautiful adaptive interfaces for iPhone and iPad"
    )
    .environment(\.horizontalSizeClass, .compact)
}

#Preview("iPad") {
    AdaptiveProfileCard(
        imageName: "person.circle.fill",
        name: "Sarah Chen",
        title: "iOS Developer",
        bio: "Building beautiful adaptive interfaces for iPhone and iPad"
    )
    .environment(\.horizontalSizeClass, .regular)
    .frame(width: 600)
}
```

#### Prompt Pattern 2: Multi-Device Optimized Grid

```
Create an adaptive photo grid view for an image gallery. On iPhone, show
2 columns. On iPad portrait, show 3 columns. On iPad landscape, show
4 columns. Use LazyVGrid with adaptive columns based on size class detection.
Include proper spacing and aspect ratio for photos.
```

**Key concepts the AI implements:**
- Detecting both horizontal and vertical size classes
- Creating different GridItem configurations per device
- Using LazyVGrid for performance with many items
- Applying appropriate spacing and sizing

### Step 4: Use ViewThatFits for Automatic Layout Selection

ViewThatFits (introduced in iOS 16, enhanced in iOS 18) automatically picks the first view that fits in available space:

#### Prompt for ViewThatFits:

```
Create a toolbar with 5 action buttons using ViewThatFits. If there's enough
horizontal space, show all buttons in an HStack. If space is limited, show
a menu button that reveals the actions in a dropdown. The ViewThatFits should
automatically choose the layout based on available width.
```

**Expected AI-generated output:**

```swift
import SwiftUI

struct AdaptiveToolbar: View {
    var body: some View {
        ViewThatFits {
            // First choice: All buttons in a row (if space permits)
            HStack(spacing: 20) {
                toolbarButtons
            }

            // Fallback: Compact menu button (if space is tight)
            Menu {
                toolbarMenuItems
            } label: {
                Label("Actions", systemImage: "ellipsis.circle")
            }
        }
        .padding()
    }

    @ViewBuilder
    var toolbarButtons: some View {
        Button(action: { /* share */ }) {
            Label("Share", systemImage: "square.and.arrow.up")
        }

        Button(action: { /* edit */ }) {
            Label("Edit", systemImage: "pencil")
        }

        Button(action: { /* favorite */ }) {
            Label("Favorite", systemImage: "star")
        }

        Button(action: { /* delete */ }) {
            Label("Delete", systemImage: "trash")
                .foregroundColor(.red)
        }

        Button(action: { /* settings */ }) {
            Label("Settings", systemImage: "gear")
        }
    }

    @ViewBuilder
    var toolbarMenuItems: some View {
        Button(action: { /* share */ }) {
            Label("Share", systemImage: "square.and.arrow.up")
        }

        Button(action: { /* edit */ }) {
            Label("Edit", systemImage: "pencil")
        }

        Button(action: { /* favorite */ }) {
            Label("Favorite", systemImage: "star")
        }

        Button(action: { /* delete */ }) {
            Label("Delete", systemImage: "trash")
        }

        Button(action: { /* settings */ }) {
            Label("Settings", systemImage: "gear")
        }
    }
}

#Preview("Wide Layout") {
    AdaptiveToolbar()
        .frame(width: 600)
}

#Preview("Compact Layout") {
    AdaptiveToolbar()
        .frame(width: 300)
}
```

**Benefits of ViewThatFits:**
- No manual size class detection needed
- Automatically adapts to available space
- Works with rotation, split-screen, and window resizing
- Cleaner code than conditional logic

### Step 5: Create Navigation Layouts with NavigationSplitView

For iPad-optimized interfaces with sidebars and detail panes, use NavigationSplitView:

#### Prompt for NavigationSplitView:

```
Create a master-detail layout using NavigationSplitView for a notes app.
Show a sidebar with note categories, a list of notes, and a detail view
of the selected note. On iPhone, this should collapse to a single-column
navigation stack. On iPad, show all three columns. Include realistic
sample data for categories and notes.
```

**Expected AI-generated output:**

```swift
import SwiftUI

struct AdaptiveNotesApp: View {
    @State private var selectedCategory: Category?
    @State private var selectedNote: Note?

    var body: some View {
        NavigationSplitView {
            // Sidebar: Categories
            List(Category.sampleCategories, selection: $selectedCategory) { category in
                NavigationLink(value: category) {
                    Label(category.name, systemImage: category.icon)
                }
            }
            .navigationTitle("Categories")
        } content: {
            // Middle column: Notes list
            if let category = selectedCategory {
                List(category.notes, selection: $selectedNote) { note in
                    NavigationLink(value: note) {
                        VStack(alignment: .leading, spacing: 4) {
                            Text(note.title)
                                .font(.headline)
                            Text(note.preview)
                                .font(.subheadline)
                                .foregroundColor(.secondary)
                                .lineLimit(2)
                            Text(note.date, style: .date)
                                .font(.caption)
                                .foregroundColor(.secondary)
                        }
                    }
                }
                .navigationTitle(category.name)
            } else {
                ContentUnavailableView(
                    "Select a Category",
                    systemImage: "folder",
                    description: Text("Choose a category to view notes")
                )
            }
        } detail: {
            // Detail: Note content
            if let note = selectedNote {
                NoteDetailView(note: note)
            } else {
                ContentUnavailableView(
                    "Select a Note",
                    systemImage: "note.text",
                    description: Text("Choose a note to view its content")
                )
            }
        }
        .navigationSplitViewStyle(.balanced)
    }
}

struct NoteDetailView: View {
    let note: Note

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 16) {
                Text(note.title)
                    .font(.largeTitle)
                    .fontWeight(.bold)

                Text(note.date, style: .date)
                    .font(.subheadline)
                    .foregroundColor(.secondary)

                Divider()

                Text(note.content)
                    .font(.body)
            }
            .padding()
        }
        .navigationTitle(note.title)
        .navigationBarTitleDisplayMode(.inline)
    }
}

// MARK: - Data Models

struct Category: Identifiable, Hashable {
    let id = UUID()
    let name: String
    let icon: String
    var notes: [Note]

    static let sampleCategories = [
        Category(name: "Work", icon: "briefcase", notes: [
            Note(title: "Project Proposal", preview: "Draft for Q1 initiative", content: "Complete project proposal with timeline and budget..."),
            Note(title: "Meeting Notes", preview: "Weekly team sync", content: "Discussed sprint progress and upcoming milestones...")
        ]),
        Category(name: "Personal", icon: "house", notes: [
            Note(title: "Grocery List", preview: "Weekly shopping items", content: "Milk, eggs, bread, vegetables..."),
            Note(title: "Travel Plans", preview: "Summer vacation ideas", content: "Researching destinations in Europe...")
        ]),
        Category(name: "Ideas", icon: "lightbulb", notes: [
            Note(title: "App Concept", preview: "Fitness tracking idea", content: "Build an app that gamifies workouts..."),
            Note(title: "Book Outline", preview: "Fiction novel structure", content: "Main character embarks on a journey...")
        ])
    ]
}

struct Note: Identifiable, Hashable {
    let id = UUID()
    let title: String
    let preview: String
    let content: String
    let date: Date = Date()
}

#Preview("iPad") {
    AdaptiveNotesApp()
        .previewDevice("iPad Pro (12.9-inch) (6th generation)")
}

#Preview("iPhone") {
    AdaptiveNotesApp()
        .previewDevice("iPhone 16 Pro")
}
```

**NavigationSplitView benefits:**
- Automatically collapses to single column on iPhone
- Three-column layout on iPad with proper sidebar behavior
- Built-in handling for split-screen multitasking
- State restoration and persistence

### Step 6: Use GeometryReader for Proportional Layouts

For layouts that need precise control based on available space:

#### Prompt for GeometryReader:

```
Create an adaptive card layout that shows content differently based on
available width. Use GeometryReader to measure the container size. If width
is less than 400, show a compact vertical layout. If width is 400-700, show
a medium layout with side-by-side elements. If width is over 700, show an
expanded layout with additional details. Include smooth transitions.
```

**Expected AI-generated output:**

```swift
import SwiftUI

struct AdaptiveContentCard: View {
    let product: Product

    var body: some View {
        GeometryReader { geometry in
            let width = geometry.size.width

            Group {
                if width < 400 {
                    compactLayout
                } else if width < 700 {
                    mediumLayout
                } else {
                    expandedLayout
                }
            }
            .animation(.spring(response: 0.35, dampingFraction: 0.8), value: width)
        }
        .frame(height: heightForLayout)
    }

    // Compact: iPhone portrait
    var compactLayout: some View {
        VStack(alignment: .leading, spacing: 12) {
            productImage
                .frame(height: 200)

            VStack(alignment: .leading, spacing: 8) {
                Text(product.name)
                    .font(.headline)

                Text(product.price)
                    .font(.title3)
                    .fontWeight(.bold)
                    .foregroundColor(.blue)

                Text(product.description)
                    .font(.subheadline)
                    .foregroundColor(.secondary)
                    .lineLimit(3)
            }
            .padding(.horizontal)
        }
    }

    // Medium: iPhone landscape or iPad 1/3 split
    var mediumLayout: some View {
        HStack(spacing: 16) {
            productImage
                .frame(width: 200)

            VStack(alignment: .leading, spacing: 12) {
                Text(product.name)
                    .font(.title2)
                    .fontWeight(.semibold)

                Text(product.price)
                    .font(.title2)
                    .fontWeight(.bold)
                    .foregroundColor(.blue)

                Text(product.description)
                    .font(.body)
                    .foregroundColor(.secondary)
                    .lineLimit(4)

                Spacer()
            }
            .padding(.vertical)
        }
        .padding()
    }

    // Expanded: iPad or Mac
    var expandedLayout: some View {
        HStack(spacing: 24) {
            productImage
                .frame(width: 300)

            VStack(alignment: .leading, spacing: 16) {
                VStack(alignment: .leading, spacing: 8) {
                    Text(product.category)
                        .font(.caption)
                        .foregroundColor(.secondary)
                        .textCase(.uppercase)

                    Text(product.name)
                        .font(.largeTitle)
                        .fontWeight(.bold)

                    HStack {
                        ForEach(0..<5) { index in
                            Image(systemName: index < product.rating ? "star.fill" : "star")
                                .foregroundColor(.yellow)
                        }
                        Text("\(product.rating).0")
                            .foregroundColor(.secondary)
                    }
                }

                Text(product.description)
                    .font(.body)
                    .foregroundColor(.secondary)

                Text(product.price)
                    .font(.title)
                    .fontWeight(.bold)
                    .foregroundColor(.blue)

                Spacer()

                HStack {
                    Button("Add to Cart") {
                        // Add to cart action
                    }
                    .buttonStyle(.borderedProminent)

                    Button("Save for Later") {
                        // Save action
                    }
                    .buttonStyle(.bordered)
                }
            }
            .padding(.vertical)
        }
        .padding(24)
    }

    var productImage: some View {
        RoundedRectangle(cornerRadius: 12)
            .fill(Color.gray.opacity(0.2))
            .overlay {
                Image(systemName: product.imageName)
                    .font(.system(size: 60))
                    .foregroundColor(.gray)
            }
    }

    var heightForLayout: CGFloat {
        // Adjust card height based on layout
        return 300 // Can be dynamic based on geometry
    }
}

struct Product {
    let name: String
    let category: String
    let price: String
    let description: String
    let rating: Int
    let imageName: String

    static let sample = Product(
        name: "Wireless Headphones Pro",
        category: "Audio",
        price: "$299.99",
        description: "Premium wireless headphones with active noise cancellation, 30-hour battery life, and studio-quality sound.",
        rating: 5,
        imageName: "headphones"
    )
}

#Preview("Compact") {
    AdaptiveContentCard(product: .sample)
        .frame(width: 350)
}

#Preview("Medium") {
    AdaptiveContentCard(product: .sample)
        .frame(width: 550)
}

#Preview("Expanded") {
    AdaptiveContentCard(product: .sample)
        .frame(width: 800)
}
```

### Step 7: Generate Comprehensive Device Previews

Ask AI to create previews for all target devices:

#### Prompt for comprehensive previews:

```
Generate comprehensive SwiftUI previews for this adaptive layout showing how
it appears on:
1. iPhone SE (compact, smallest screen)
2. iPhone 16 Pro (standard)
3. iPhone 16 Pro Max in landscape
4. iPad Pro in portrait
5. iPad Pro in landscape
6. iPad in 1/3 split screen (compact width)

Use appropriate preview device identifiers and traits.
```

**The AI will generate previews like:**

```swift
#Preview("iPhone SE - Compact") {
    ContentView()
        .previewDevice(PreviewDevice(rawValue: "iPhone SE (3rd generation)"))
        .previewDisplayName("iPhone SE")
}

#Preview("iPhone 16 Pro - Standard") {
    ContentView()
        .previewDevice(PreviewDevice(rawValue: "iPhone 16 Pro"))
}

#Preview("iPhone 16 Pro Max - Landscape", traits: .landscapeLeft) {
    ContentView()
        .previewDevice(PreviewDevice(rawValue: "iPhone 16 Pro Max"))
}

#Preview("iPad Pro - Portrait", traits: .portrait) {
    ContentView()
        .previewDevice(PreviewDevice(rawValue: "iPad Pro (12.9-inch) (6th generation)"))
}

#Preview("iPad Pro - Landscape", traits: .landscapeLeft) {
    ContentView()
        .previewDevice(PreviewDevice(rawValue: "iPad Pro (12.9-inch) (6th generation)"))
}

#Preview("iPad Split Screen - Compact") {
    ContentView()
        .previewDevice(PreviewDevice(rawValue: "iPad Pro (12.9-inch) (6th generation)"))
        .environment(\.horizontalSizeClass, .compact)
        .previewDisplayName("iPad 1/3 Split")
}
```

### Step 8: Optimize Adaptive Layouts with AI Refinement

Use iterative prompts to improve your adaptive layouts:

#### Refinement prompt examples:

**Improve spacing:**
```
The compact layout looks too cramped on iPhone SE. Increase padding to 16
and add more vertical spacing between elements. Ensure content doesn't feel
squeezed.
```

**Add orientation handling:**
```
Add support for iPhone landscape orientation. When the device rotates to
landscape, switch from VStack to HStack for better use of horizontal space.
Detect orientation changes using verticalSizeClass.
```

**Performance optimization:**
```
This adaptive grid with 500 images is slow. Optimize using LazyVGrid instead
of regular Grid, and add .id() to improve SwiftUI's diffing performance.
Consider using AsyncImage with caching.
```

**Accessibility improvements:**
```
Ensure this adaptive layout works with accessibility text sizes (xxxLarge
and accessibility5). Text should wrap properly and layouts should adjust
to accommodate larger text without clipping.
```

**iPad multitasking:**
```
Test this layout in iPad split-screen scenarios. Ensure it gracefully
handles: full screen (regular/regular), 2/3 split (regular/regular),
1/2 split (compact/regular), and 1/3 split (compact/regular).
```

### Step 9: Combine Multiple Adaptive Techniques

For complex interfaces, combine size classes, ViewThatFits, and GeometryReader:

#### Advanced prompt:

```
Create a dashboard view that combines multiple adaptive techniques:
1. Use NavigationSplitView for sidebar on iPad, tab bar on iPhone
2. Use ViewThatFits for adaptive toolbar (full buttons vs menu)
3. Use size class detection for grid columns (2 on iPhone, 3-4 on iPad)
4. Use GeometryReader for proportional card sizing
5. Include smooth animations when transitioning between layouts
6. Add comprehensive previews for all device sizes

The dashboard shows: stats cards, recent activity list, and quick actions.
```

The AI will generate a sophisticated, production-ready adaptive interface combining best practices.

### Step 10: Test Adaptive Layouts Across Devices

After generating adaptive layouts:

1. **Preview in Canvas:**
   - Open Canvas with `Cmd + Option + Enter`
   - Cycle through all device previews
   - Check layout correctness at each size

2. **Test in Simulator:**
   - Run on iPhone SE (smallest): `Cmd + R`
   - Run on iPad Pro (largest): Switch device in scheme
   - Test rotation: `Cmd + Left/Right Arrow`
   - Test split-screen on iPad

3. **Verify size class transitions:**
   - Watch layout changes when rotating
   - Ensure animations are smooth
   - Check that no content is clipped

4. **Accessibility testing:**
   - Test with largest text size (Settings → Accessibility → Display & Text Size)
   - Verify VoiceOver navigation
   - Check color contrast in both light and dark mode

5. **Ask AI to fix issues:**
   ```
   On iPhone SE in landscape, the HStack is too wide and content is cut off.
   Fix by adding a ScrollView horizontally, or switch to VStack for this
   specific size class combination (compact width, compact height).
   ```

## Expected Results

After completing this tutorial, you should have:

1. **Truly Universal Layouts:**
   - Single SwiftUI view that adapts seamlessly from iPhone SE to iPad Pro
   - Appropriate layout for each device size (vertical on iPhone, horizontal on iPad)
   - Smooth transitions when rotating or resizing
   - No content clipping, awkward spacing, or layout issues

2. **Multiple Adaptive Techniques:**
   - Size class detection using @Environment(\.horizontalSizeClass) and @Environment(\.verticalSizeClass)
   - ViewThatFits automatically selecting best layout for available space
   - GeometryReader for precise proportional layouts
   - NavigationSplitView for master-detail iPad interfaces

3. **Comprehensive Device Previews:**
   - Previews for iPhone SE, standard iPhone, iPhone Max, and iPad
   - Both portrait and landscape orientations
   - iPad split-screen scenarios
   - Easy visual comparison of layouts

4. **Production-Ready Code:**
   - Clean, maintainable SwiftUI code
   - Proper state management across layout changes
   - Smooth animations between size class transitions
   - Accessibility support with Dynamic Type

5. **Time Savings:**
   - 20-30 minutes saved per adaptive view using AI generation
   - Reduced trial-and-error in layout design
   - Automatic generation of comprehensive preview code
   - Quick iterations with AI refinement prompts

**Visual results in Xcode Canvas:**
- iPhone SE preview shows compact vertical layout
- iPhone 16 Pro shows optimized standard layout
- iPad Pro shows expanded multi-column layout with additional details
- All layouts render correctly without layout constraint conflicts

## Troubleshooting

### Common Issue 1: Layout Doesn't Change Between iPhone and iPad

**Problem:** The same layout appears on both iPhone and iPad despite size class detection code.

**Solution:**
- Verify you're using `@Environment(\.horizontalSizeClass)` not `@State`
- Check the conditional logic: `if horizontalSizeClass == .compact` (iPhone) vs `== .regular` (iPad)
- Ensure the environment value is being read in the view body, not in init
- Test in actual simulators, not just Canvas (Canvas may default to one size class)
- Add print statement: `print("Size class: \(horizontalSizeClass)")` to debug
- Ask AI: "The size class detection isn't working. Debug why both iPhone and iPad show the same layout."

**Follow-up prompt:**
```
Add debug logging to show the current horizontal and vertical size classes.
Print them in the view body so I can verify they're changing correctly.
```

### Common Issue 2: ViewThatFits Picks Wrong Layout

**Problem:** ViewThatFits always shows the fallback layout, never the preferred layout.

**Solution:**
- ViewThatFits measures ideal size, not frame size—ensure views report their ideal size correctly
- Check that first view's ideal size isn't unrealistically large
- Specify axes: `.viewThatFits(in: .horizontal)` to only consider horizontal space
- Remove fixed frame modifiers from views inside ViewThatFits
- Order matters—place most desirable layout first, fallback last
- Ask AI: "ViewThatFits always picks the menu, never the HStack. Fix the measurement issue."

**Follow-up prompt:**
```
The ViewThatFits is selecting the compact layout even when there's plenty
of space. Ensure the HStack reports its ideal size correctly and doesn't
have unnecessary frame constraints.
```

### Common Issue 3: GeometryReader Causes Layout Issues

**Problem:** Using GeometryReader breaks layout, causes infinite size, or creates visual glitches.

**Solution:**
- GeometryReader proposes infinite size to its child—wrap content in a frame or container
- Don't use GeometryReader at the root of a view—embed it where needed
- Cache geometry values in @State to prevent layout loops: `@State private var width: CGFloat = 0`
- Use `.frame(height: fixedHeight)` on GeometryReader to prevent expanding vertically
- Consider alternatives: size classes or ViewThatFits may be simpler
- Ask AI: "GeometryReader is causing layout issues. Refactor to use it properly or replace with ViewThatFits."

**Follow-up prompt:**
```
The GeometryReader is expanding to infinite height. Constrain it to only
measure width and set a fixed height of 400 points.
```

### Common Issue 4: iPad Split-Screen Layouts Break

**Problem:** Layout works in full-screen iPad but breaks in split-screen (1/3 or 1/2 screen).

**Solution:**
- iPad split-screen can have compact width (1/3 and sometimes 1/2)—handle this case
- Test with environment override: `.environment(\.horizontalSizeClass, .compact)`
- NavigationSplitView automatically handles this—use it for sidebar layouts
- Use ViewThatFits to gracefully degrade complex layouts
- Don't assume iPad = regular width—it can be compact in multitasking
- Ask AI: "This layout breaks in iPad 1/3 split-screen. Handle compact width on iPad."

**Follow-up prompt:**
```
Add a preview simulating iPad in 1/3 split screen (compact width, regular
height). Ensure the layout works correctly by showing a single-column view.
```

### Common Issue 5: Animations Are Janky During Size Class Changes

**Problem:** Layout transitions when rotating or resizing are choppy, slow, or have visual glitches.

**Solution:**
- Add explicit animation: `.animation(.spring(), value: horizontalSizeClass)`
- Use consistent ID across layout changes: `.id("content")` on both VStack and HStack
- Avoid animating too many properties—animate only critical changes
- Use matchedGeometryEffect for smooth shared element transitions
- Consider .transition() modifiers for appearing/disappearing views
- Profile with Instruments to identify performance bottlenecks
- Ask AI: "Optimize the size class transition animation. Use spring animation and matched geometry effect."

**Follow-up prompt:**
```
The transition between layouts is abrupt. Add a smooth spring animation
when horizontalSizeClass changes, and use matchedGeometryEffect to animate
the profile image between the VStack and HStack layouts.
```

### Common Issue 6: Text Truncates or Clips on Small Screens

**Problem:** Text gets cut off on iPhone SE or in compact layouts.

**Solution:**
- Use `.lineLimit(nil)` or `.lineLimit(2...5)` for flexible wrapping
- Add `.fixedSize(horizontal: false, vertical: true)` to allow vertical growth
- Replace fixed `.frame(height: 100)` with `.frame(minHeight: 100)` to allow expansion
- Use `.scaledToFit()` font modifier for automatic scaling
- Test with accessibility text sizes (xxxLarge)
- Wrap text containers in ScrollView if content may overflow
- Ask AI: "Text is clipping on iPhone SE. Make the layout responsive with flexible text wrapping."

**Follow-up prompt:**
```
On iPhone SE with accessibility size xxxLarge, the description text is
cut off. Allow the text to wrap to multiple lines and expand the card
height dynamically.
```

## Additional Tips

- **Start with size classes** - They're the foundational adaptive technique; master them before ViewThatFits or GeometryReader

- **Test early and often** - Run on actual simulators (iPhone SE and iPad Pro) frequently to catch layout issues

- **Use Canvas previews extensively** - Create previews for every device size you support; visual comparison catches issues

- **Prefer ViewThatFits over manual logic** - It's cleaner, more maintainable, and handles edge cases automatically

- **NavigationSplitView for iPad** - If building iPad-optimized apps, use NavigationSplitView for proper sidebar/detail patterns

- **Combine techniques strategically** - Use size classes for major layout decisions, ViewThatFits for component-level adaptation, GeometryReader sparingly

- **Animate size class changes** - Add `.animation(.spring(), value: horizontalSizeClass)` for smooth transitions

- **Consider iPad multitasking** - iPad in split-screen can have compact width—always test this scenario

- **Use relative sizing** - Prefer percentages and flexible layouts over fixed pixel sizes

- **Test with Dynamic Type** - Ensure layouts work with accessibility text sizes (Settings → Accessibility → Display & Text Size)

- **Watch for over-engineering** - Not every view needs complex adaptive logic; simple VStacks often work fine

- **Create reusable adaptive components** - Extract common adaptive patterns (AdaptiveStack, AdaptiveCard) for consistency

- **Document size class behavior** - Add comments explaining when each layout variant appears

- **Use AI for boilerplate** - AI excels at generating comprehensive device previews and size class scaffolding

- **Iterate with AI** - Start simple, then refine with follow-up prompts ("add iPad landscape support", "improve spacing")

- **Profile performance** - Large adaptive views may need LazyVStack or LazyVGrid for smooth scrolling

- **Learn from AI-generated code** - Study the adaptive patterns AI uses to improve your own SwiftUI skills

**Example prompt library for adaptive layouts:**

```
// Template 1: Basic size class adaptation
Create a [ViewName] that uses size class detection. Show VStack layout on
iPhone (compact width) and HStack on iPad (regular width). Include smooth
animation when size class changes.

// Template 2: ViewThatFits toolbar
Create a toolbar using ViewThatFits. Show all [N] buttons in HStack when
space permits. Fall back to a Menu when space is limited.

// Template 3: NavigationSplitView
Create a three-column NavigationSplitView for [app type]. Sidebar shows
[categories], middle column shows [list], detail shows [content]. Collapses
to navigation stack on iPhone.

// Template 4: GeometryReader proportional layout
Create a [ViewName] using GeometryReader. If width < 400, show [compact
layout]. If 400-700, show [medium layout]. If > 700, show [expanded layout].

// Template 5: Comprehensive previews
Generate previews for [ViewName] on: iPhone SE, iPhone 16 Pro, iPhone 16
Pro Max landscape, iPad Pro portrait, iPad Pro landscape, iPad 1/3 split.
```

## Related Articles

- KB-011: How to write your first Hello World SwiftUI view
- KB-021: How to add UI elements to a SwiftUI view
- KB-024: How to preview your SwiftUI views using Xcode Previews
- KB-031: How to access the Coding Assistant in Xcode 26 (ChatGPT)
- KB-037: How to generate SwiftUI views from natural language descriptions using ChatGPT
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-046: How to switch between ChatGPT and Claude in the Coding Assistant
- KB-052: How to prompt AI assistants to create SwiftUI preview code

## Sources

- Apple Developer Documentation - Maintaining the adaptable sizes of built-in views - https://developer.apple.com/tutorials/swiftui-concepts/maintaining-the-adaptable-sizes-of-built-in-views (Accessed: November 17, 2025)
- Apple Developer Documentation - horizontalSizeClass - https://developer.apple.com/documentation/swiftui/environmentvalues/horizontalsizeclass (Accessed: November 17, 2025)
- Apple Developer Documentation - verticalSizeClass - https://developer.apple.com/documentation/swiftui/environmentvalues/verticalsizeclass (Accessed: November 17, 2025)
- Apple Developer Documentation - ViewThatFits - https://developer.apple.com/documentation/swiftui/viewthatfits (Accessed: November 17, 2025)
- Apple Developer Documentation - NavigationSplitView - https://developer.apple.com/documentation/swiftui/navigationsplitview (Accessed: November 17, 2025)
- Hacking with Swift - How to create an adaptive layout with ViewThatFits - https://www.hackingwithswift.com/quick-start/swiftui/how-to-create-an-adaptive-layout-with-viewthatfits (Accessed: November 17, 2025)
- Hacking with Swift - Changing a view's layout in response to size classes - https://www.hackingwithswift.com/books/ios-swiftui/changing-a-views-layout-in-response-to-size-classes (Accessed: November 17, 2025)
- Sarunw - Responsive layout in SwiftUI with ViewThatFits - https://sarunw.com/posts/swiftui-viewthatfits/ (Accessed: November 17, 2025)
- Nilcoalescing - Adaptive layouts with ViewThatFits - https://nilcoalescing.com/blog/AdaptiveLayoutsWithViewThatFits/ (Accessed: November 17, 2025)
- Holy Swift - Adaptative Views That Fit Anywhere in SwiftUI - https://holyswift.app/adaptative-views-that-fit-anywhere-in-swiftui/ (Accessed: November 17, 2025)
- Five Stars - How to make SwiftUI views adaptive - https://www.fivestars.blog/articles/adaptive-swiftui-views/ (Accessed: November 17, 2025)
- Bitcot - Size Classes in Swift: The Complete Guide to Adaptive iOS Layouts (2025) - https://www.bitcot.com/designing-for-diversity-with-size-classes-layout-swift/ (Accessed: November 17, 2025)
- Medium - SwiftUI Responsive Design: Adaptive Layouts for iPhone, iPad & Mac — Complete Guide 2025 - https://ravi6997.medium.com/responsive-design-in-swiftui-stop-hardcoding-layout-for-iphone-only-build-apps-that-truly-adapt-69e5c975e112 (Accessed: November 17, 2025)
- Namit Gupta - 7 Essential Techniques for Crafting Adaptive Layouts in SwiftUI - https://namitgupta.com/7-essential-techniques-for-crafting-adaptive-layouts-in-swiftui (Accessed: November 17, 2025)
- Medium - How to adapt to size classes in SwiftUI - https://michaziobro-21492.medium.com/how-to-solve-size-classes-in-swiftui-6143571d39e2 (Accessed: November 17, 2025)
- GitHub - SwiftUI-with-Size-Classes - https://github.com/renaudjenny/SwiftUI-with-Size-Classes (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
