# How to Handle Button Actions and User Interactions

**Article ID:** KB-023
**Difficulty Level:** Beginner
**Topic:** Xcode v26.1+
**Last Updated:** November 17, 2025
**Applies To:** Xcode 26.1+, Swift 6.0+, iOS 26+

## Overview

Handling button actions and user interactions is fundamental to creating responsive iOS applications. With Xcode 26.1 and iOS 26, Apple has introduced the Liquid Glass design system alongside enhanced SwiftUI button APIs and improved UIKit integration. This article demonstrates modern approaches to implementing button actions in both SwiftUI and UIKit, including the new Liquid Glass button styles, async/await support, haptic feedback, and gesture handling.

**What You'll Learn:**
- Creating SwiftUI buttons with Liquid Glass design system
- Implementing button actions with closures and async/await
- Using UIKit button actions with target-action and UIAction patterns
- Adding haptic feedback for enhanced user experience
- Handling complex user interactions with gestures
- Best practices for button accessibility and user experience

## Prerequisites

Before following this guide, ensure you have:

- **Xcode 26.1 or later** installed
- **macOS** compatible with Xcode 26.1+
- **Swift 6.0+** language mode enabled
- Basic understanding of SwiftUI or UIKit
- **iOS 26.0+** deployment target for Liquid Glass features
- Basic knowledge of closures and Swift concurrency (for async examples)

## Detailed Steps

### Part 1: SwiftUI Button Actions

#### Step 1.1: Create a Basic Button with Action Closure

SwiftUI buttons consist of two components: a label and an action closure that executes when the button is tapped.

**Basic Button Syntax:**

```swift
import SwiftUI

struct ContentView: View {
    @State private var tapCount = 0

    var body: some View {
        VStack(spacing: 20) {
            Text("Tapped \(tapCount) times")
                .font(.title2)

            // Basic button with text label
            Button("Tap Me") {
                tapCount += 1
            }

            // Button with custom label view
            Button {
                tapCount += 1
            } label: {
                Label("Increment", systemImage: "plus.circle.fill")
                    .font(.headline)
            }
        }
        .padding()
    }
}
```

**Key Points:**
- The action closure executes synchronously on the main thread
- State changes within the action automatically update the view
- Buttons automatically adapt to their context (toolbar, navigation bar, etc.)

#### Step 1.2: Apply Liquid Glass Button Styles (iOS 26+)

iOS 26 introduces the Liquid Glass design system with new button styles that provide dynamic, glass-like depth.

```swift
import SwiftUI

struct LiquidGlassButtonsView: View {
    var body: some View {
        VStack(spacing: 30) {
            // Standard glass button (translucent)
            Button("Glass Button") {
                print("Glass button tapped")
            }
            .buttonStyle(.glass)

            // Prominent glass button (opaque, for primary actions)
            Button("Primary Action") {
                performPrimaryAction()
            }
            .buttonStyle(.glassProminent)

            // Tinted glass button
            Button("Custom Tint") {
                print("Tinted button tapped")
            }
            .buttonStyle(.glass)
            .tint(.purple)

            // Glass button with custom content
            Button {
                submitForm()
            } label: {
                HStack {
                    Image(systemName: "checkmark.circle.fill")
                    Text("Submit")
                        .fontWeight(.semibold)
                }
                .padding(.horizontal, 20)
                .padding(.vertical, 12)
            }
            .buttonStyle(.glassProminent)
            .tint(.green)
        }
        .padding()
    }

    func performPrimaryAction() {
        // Primary action logic
    }

    func submitForm() {
        // Form submission logic
    }
}
```

**Liquid Glass Button Style Options:**
- `.glass` - Translucent, clear-like effect that shows background through
- `.glassProminent` - Opaque variant designed for primary actions
- Both styles react with animations on press and adapt to light/dark modes

#### Step 1.3: Implement Async Button Actions

With Swift 6.0 and Xcode 26, you can safely handle asynchronous operations in button actions using async/await.

```swift
import SwiftUI

struct AsyncButtonView: View {
    @State private var isLoading = false
    @State private var userData: UserData?
    @State private var errorMessage: String?

    var body: some View {
        VStack(spacing: 20) {
            if let user = userData {
                Text("Welcome, \(user.name)!")
                    .font(.title2)
            }

            Button {
                Task {
                    await fetchUserData()
                }
            } label: {
                HStack {
                    if isLoading {
                        ProgressView()
                            .tint(.white)
                    }
                    Text(isLoading ? "Loading..." : "Fetch Data")
                }
                .frame(maxWidth: .infinity)
                .padding()
            }
            .buttonStyle(.glassProminent)
            .disabled(isLoading)

            if let error = errorMessage {
                Text(error)
                    .foregroundStyle(.red)
                    .font(.caption)
            }
        }
        .padding()
    }

    @MainActor
    func fetchUserData() async {
        isLoading = true
        errorMessage = nil

        do {
            // Simulate network request
            try await Task.sleep(for: .seconds(2))

            // In a real app, fetch from network
            userData = UserData(name: "John Doe", email: "john@example.com")
        } catch {
            errorMessage = "Failed to load data: \(error.localizedDescription)"
        }

        isLoading = false
    }
}

struct UserData {
    let name: String
    let email: String
}
```

**Best Practices for Async Actions:**
- Always wrap async calls in a `Task { }` block
- Use `@MainActor` for functions that update UI state
- Provide visual feedback during loading states
- Disable buttons during async operations to prevent multiple taps
- Handle errors gracefully with user-friendly messages

#### Step 1.4: Add Haptic Feedback to Buttons

Enhance the user experience by adding haptic feedback that synchronizes with button interactions.

```swift
import SwiftUI

struct HapticButtonView: View {
    @State private var likeCount = 0

    var body: some View {
        VStack(spacing: 30) {
            Text("\(likeCount) Likes")
                .font(.title)

            // Button with light haptic feedback
            Button {
                provideLightHaptic()
                likeCount += 1
            } label: {
                Image(systemName: likeCount > 0 ? "heart.fill" : "heart")
                    .font(.system(size: 40))
                    .foregroundStyle(likeCount > 0 ? .red : .gray)
            }
            .sensoryFeedback(.impact(weight: .light), trigger: likeCount)

            // Button with custom haptic feedback
            Button("Delete Item") {
                provideWarningHaptic()
                deleteItem()
            }
            .buttonStyle(.glassProminent)
            .tint(.red)
            .sensoryFeedback(.warning, trigger: likeCount)
        }
        .padding()
    }

    func provideLightHaptic() {
        let generator = UIImpactFeedbackGenerator(style: .light)
        generator.impactOccurred()
    }

    func provideWarningHaptic() {
        let generator = UINotificationFeedbackGenerator()
        generator.notificationOccurred(.warning)
    }

    func deleteItem() {
        // Delete logic
    }
}
```

**Haptic Feedback Types:**
- `.light` - Minor UI interactions (selections, toggles)
- `.medium` - Standard button taps
- `.heavy` - Important actions (confirmations, deletions)
- `.success`, `.warning`, `.error` - Notification-style feedback

**Haptic Best Practices:**
- Synchronize haptics precisely with visual animations
- Match haptic intensity to action importance
- Don't overuse - haptics should enhance, not distract
- Respect system haptic settings

### Part 2: UIKit Button Actions

#### Step 2.1: Create Buttons with UIAction Closures (Modern Approach)

UIAction (iOS 14+) provides a modern closure-based approach, avoiding the traditional target-action pattern.

```swift
import UIKit

class ModernButtonViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        setupButtons()
    }

    func setupButtons() {
        // Button with UIAction closure
        let primaryAction = UIAction(title: "Primary") { action in
            self.handlePrimaryAction()
        }

        let primaryButton = UIButton(type: .system, primaryAction: primaryAction)
        primaryButton.frame = CGRect(x: 100, y: 100, width: 200, height: 50)
        primaryButton.configuration = .filled()
        view.addSubview(primaryButton)

        // Button with inline UIAction
        let secondaryButton = UIButton(frame: CGRect(x: 100, y: 170, width: 200, height: 50))
        secondaryButton.configuration = .tinted()
        secondaryButton.setTitle("Secondary", for: .normal)

        secondaryButton.addAction(UIAction { [weak self] _ in
            self?.handleSecondaryAction()
        }, for: .touchUpInside)

        view.addSubview(secondaryButton)

        // Button with state-based action
        let toggleButton = UIButton(frame: CGRect(x: 100, y: 240, width: 200, height: 50))
        toggleButton.configuration = .gray()

        let toggleAction = UIAction { action in
            guard let button = action.sender as? UIButton else { return }
            button.isSelected.toggle()
            button.configuration?.title = button.isSelected ? "ON" : "OFF"
        }

        toggleButton.addAction(toggleAction, for: .touchUpInside)
        toggleButton.configuration?.title = "OFF"
        view.addSubview(toggleButton)
    }

    func handlePrimaryAction() {
        print("Primary action executed")
        // Provide haptic feedback
        let haptic = UIImpactFeedbackGenerator(style: .medium)
        haptic.impactOccurred()
    }

    func handleSecondaryAction() {
        print("Secondary action executed")
    }
}
```

**Advantages of UIAction:**
- No need for `@objc` selectors
- Closure captures context naturally
- Cleaner, more Swift-like syntax
- Supports weak self references to prevent retain cycles

#### Step 2.2: Traditional Target-Action Pattern

The classic target-action pattern remains fully supported for backward compatibility and specific use cases.

```swift
import UIKit

class TraditionalButtonViewController: UIViewController {

    private var counter = 0
    private var counterLabel: UILabel!

    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
    }

    func setupUI() {
        // Label to display counter
        counterLabel = UILabel(frame: CGRect(x: 100, y: 100, width: 200, height: 40))
        counterLabel.text = "Count: 0"
        counterLabel.textAlignment = .center
        counterLabel.font = .systemFont(ofSize: 24, weight: .bold)
        view.addSubview(counterLabel)

        // Increment button with target-action
        let incrementButton = UIButton(frame: CGRect(x: 100, y: 160, width: 200, height: 50))
        incrementButton.configuration = .filled()
        incrementButton.setTitle("Increment", for: .normal)
        incrementButton.addTarget(
            self,
            action: #selector(incrementCounter),
            for: .touchUpInside
        )
        view.addSubview(incrementButton)

        // Reset button
        let resetButton = UIButton(frame: CGRect(x: 100, y: 230, width: 200, height: 50))
        resetButton.configuration = .gray()
        resetButton.setTitle("Reset", for: .normal)
        resetButton.addTarget(
            self,
            action: #selector(resetCounter),
            for: .touchUpInside
        )
        view.addSubview(resetButton)
    }

    @objc func incrementCounter() {
        counter += 1
        updateCounterLabel()

        // Provide haptic feedback
        let haptic = UIImpactFeedbackGenerator(style: .light)
        haptic.impactOccurred()
    }

    @objc func resetCounter() {
        counter = 0
        updateCounterLabel()

        // Stronger haptic for reset action
        let haptic = UIImpactFeedbackGenerator(style: .medium)
        haptic.impactOccurred()
    }

    func updateCounterLabel() {
        counterLabel.text = "Count: \(counter)"
    }
}
```

**When to Use Target-Action:**
- Working with legacy code
- Interfacing with Objective-C
- Using UIControl subclasses that don't support UIAction
- Specific performance requirements

#### Step 2.3: Async Operations in UIKit Buttons

Handle asynchronous operations in UIKit button actions using Swift concurrency.

```swift
import UIKit

class AsyncUIKitButtonViewController: UIViewController {

    private var statusLabel: UILabel!
    private var loadButton: UIButton!

    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
    }

    func setupUI() {
        statusLabel = UILabel(frame: CGRect(x: 50, y: 100, width: 300, height: 60))
        statusLabel.text = "Ready to load data"
        statusLabel.textAlignment = .center
        statusLabel.numberOfLines = 0
        view.addSubview(statusLabel)

        // Async action button
        let loadAction = UIAction(title: "Load Data") { [weak self] _ in
            Task {
                await self?.loadDataAsync()
            }
        }

        loadButton = UIButton(type: .system, primaryAction: loadAction)
        loadButton.frame = CGRect(x: 100, y: 180, width: 200, height: 50)
        loadButton.configuration = .filled()
        view.addSubview(loadButton)
    }

    @MainActor
    func loadDataAsync() async {
        // Disable button and show loading state
        loadButton.isEnabled = false
        loadButton.configuration?.showsActivityIndicator = true
        statusLabel.text = "Loading..."

        do {
            // Simulate network request
            try await Task.sleep(for: .seconds(2))

            // Simulate successful data fetch
            let data = "Data loaded successfully!"
            statusLabel.text = data

            // Success haptic
            let haptic = UINotificationFeedbackGenerator()
            haptic.notificationOccurred(.success)

        } catch {
            statusLabel.text = "Error: \(error.localizedDescription)"

            // Error haptic
            let haptic = UINotificationFeedbackGenerator()
            haptic.notificationOccurred(.error)
        }

        // Re-enable button
        loadButton.isEnabled = true
        loadButton.configuration?.showsActivityIndicator = false
    }
}
```

### Part 3: Gesture-Based Interactions

#### Step 3.1: Using TapGesture vs Button

Understanding when to use `onTapGesture` versus `Button` is important for proper accessibility and user experience.

```swift
import SwiftUI

struct GestureVsButtonView: View {
    @State private var buttonTaps = 0
    @State private var gestureTaps = 0
    @State private var doubleTaps = 0

    var body: some View {
        VStack(spacing: 30) {
            // Preferred: Use Button for standard interactions
            // Buttons automatically provide accessibility support
            Button {
                buttonTaps += 1
            } label: {
                VStack {
                    Image(systemName: "hand.tap.fill")
                        .font(.largeTitle)
                    Text("Button Taps: \(buttonTaps)")
                }
                .frame(width: 200, height: 100)
            }
            .buttonStyle(.glass)

            // Use onTapGesture when you need custom tap behavior
            // or when applying to non-button views
            RoundedRectangle(cornerRadius: 12)
                .fill(.blue.opacity(0.2))
                .frame(width: 200, height: 100)
                .overlay {
                    Text("Gesture Taps: \(gestureTaps)")
                }
                .onTapGesture {
                    gestureTaps += 1
                }

            // Double-tap gesture (not possible with standard Button)
            Circle()
                .fill(.green.opacity(0.2))
                .frame(width: 120, height: 120)
                .overlay {
                    Text("Double Taps: \(doubleTaps)")
                        .multilineTextAlignment(.center)
                }
                .onTapGesture(count: 2) {
                    doubleTaps += 1
                }
        }
        .padding()
    }
}
```

**Button vs TapGesture Guidelines:**
- **Use Button when:**
  - Implementing standard UI controls
  - Accessibility is important (VoiceOver support)
  - You need automatic styling and state management
  - The interaction represents a semantic action

- **Use onTapGesture when:**
  - Applying tap behavior to custom views
  - Requiring multiple tap counts (double-tap, triple-tap)
  - Building custom interactive elements
  - Combining with other gestures

#### Step 3.2: Complex Gesture Handling

Combine multiple gestures for rich interactive experiences.

```swift
import SwiftUI

struct ComplexGestureView: View {
    @State private var scale: CGFloat = 1.0
    @State private var offset: CGSize = .zero
    @State private var lastAction = "None"

    var body: some View {
        VStack(spacing: 30) {
            Text("Last Action: \(lastAction)")
                .font(.headline)

            RoundedRectangle(cornerRadius: 20)
                .fill(
                    LinearGradient(
                        colors: [.blue, .purple],
                        startPoint: .topLeading,
                        endPoint: .bottomTrailing
                    )
                )
                .frame(width: 200, height: 200)
                .scaleEffect(scale)
                .offset(offset)
                .gesture(
                    // Simultaneous gestures
                    SimultaneousGesture(
                        MagnificationGesture()
                            .onChanged { value in
                                scale = value
                            }
                            .onEnded { _ in
                                lastAction = "Pinch"
                                withAnimation(.spring()) {
                                    scale = 1.0
                                }
                            },
                        DragGesture()
                            .onChanged { value in
                                offset = value.translation
                            }
                            .onEnded { _ in
                                lastAction = "Drag"
                                withAnimation(.spring()) {
                                    offset = .zero
                                }
                            }
                    )
                )
                .onTapGesture {
                    lastAction = "Tap"
                    let haptic = UIImpactFeedbackGenerator(style: .medium)
                    haptic.impactOccurred()
                }
                .onLongPressGesture(minimumDuration: 0.5) {
                    lastAction = "Long Press"
                    let haptic = UIImpactFeedbackGenerator(style: .heavy)
                    haptic.impactOccurred()
                }

            Button("Reset") {
                withAnimation(.spring()) {
                    scale = 1.0
                    offset = .zero
                    lastAction = "Reset"
                }
            }
            .buttonStyle(.glassProminent)
        }
        .padding()
    }
}
```

### Part 4: Advanced Button Patterns

#### Step 4.1: Custom Button Styles

Create reusable custom button styles that work with the Liquid Glass design system.

```swift
import SwiftUI

// Custom button style with loading state
struct LoadingButtonStyle: ButtonStyle {
    var isLoading: Bool

    func makeBody(configuration: Configuration) -> some View {
        HStack {
            if isLoading {
                ProgressView()
                    .tint(.white)
            }
            configuration.label
        }
        .padding()
        .frame(maxWidth: .infinity)
        .background(
            RoundedRectangle(cornerRadius: 12)
                .fill(configuration.isPressed ? Color.blue.opacity(0.8) : Color.blue)
        )
        .foregroundStyle(.white)
        .scaleEffect(configuration.isPressed ? 0.95 : 1.0)
        .animation(.spring(response: 0.3), value: configuration.isPressed)
        .opacity(isLoading ? 0.6 : 1.0)
    }
}

// Glass effect button with custom styling
struct CustomGlassButtonStyle: ButtonStyle {
    var color: Color

    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .padding()
            .background {
                RoundedRectangle(cornerRadius: 16)
                    .fill(.ultraThinMaterial)
                    .overlay {
                        RoundedRectangle(cornerRadius: 16)
                            .stroke(color.opacity(0.3), lineWidth: 1)
                    }
            }
            .foregroundStyle(color)
            .scaleEffect(configuration.isPressed ? 0.96 : 1.0)
            .animation(.spring(response: 0.2, dampingFraction: 0.7), value: configuration.isPressed)
    }
}

struct CustomButtonStylesView: View {
    @State private var isLoading = false

    var body: some View {
        VStack(spacing: 30) {
            Button("Custom Loading Button") {
                performAsyncOperation()
            }
            .buttonStyle(LoadingButtonStyle(isLoading: isLoading))
            .disabled(isLoading)

            Button("Custom Glass Style") {
                print("Tapped")
            }
            .buttonStyle(CustomGlassButtonStyle(color: .purple))

            Button("Bordered Prominent") {
                print("Tapped")
            }
            .buttonStyle(.borderedProminent)
            .controlSize(.large)
        }
        .padding()
    }

    func performAsyncOperation() {
        isLoading = true
        Task {
            try? await Task.sleep(for: .seconds(2))
            isLoading = false
        }
    }
}
```

#### Step 4.2: Accessible Button Implementation

Ensure buttons are fully accessible to all users.

```swift
import SwiftUI

struct AccessibleButtonView: View {
    @State private var isBookmarked = false
    @State private var volume: Double = 50

    var body: some View {
        VStack(spacing: 30) {
            // Icon-only button with accessibility label
            Button {
                isBookmarked.toggle()
            } label: {
                Image(systemName: isBookmarked ? "bookmark.fill" : "bookmark")
                    .font(.title)
            }
            .accessibilityLabel(isBookmarked ? "Remove bookmark" : "Add bookmark")
            .accessibilityHint("Double-tap to toggle bookmark")
            .buttonStyle(.glass)

            // Button with custom accessibility value
            Button {
                volume = min(100, volume + 10)
            } label: {
                Label("Increase Volume", systemImage: "speaker.wave.2.fill")
            }
            .accessibilityLabel("Volume")
            .accessibilityValue("\(Int(volume)) percent")
            .accessibilityHint("Increases volume by 10 percent")
            .buttonStyle(.glassProminent)

            // Destructive button with clear accessibility
            Button(role: .destructive) {
                deleteAccount()
            } label: {
                Label("Delete Account", systemImage: "trash.fill")
            }
            .accessibilityLabel("Delete account")
            .accessibilityHint("This action cannot be undone")
            .buttonStyle(.glassProminent)
        }
        .padding()
    }

    func deleteAccount() {
        // Deletion logic with confirmation
    }
}
```

**Accessibility Best Practices:**
- Provide clear labels for icon-only buttons
- Use semantic button roles (`.destructive`, `.cancel`)
- Add accessibility hints for complex actions
- Ensure sufficient tap target size (minimum 44x44 points)
- Test with VoiceOver enabled
- Support Dynamic Type for text scaling

## Expected Results

After implementing button actions and user interactions:

**SwiftUI Applications:**
- Buttons respond immediately to taps with visual feedback
- Liquid Glass buttons display translucent/opaque effects with smooth animations
- Async operations show loading states without blocking the UI
- Haptic feedback synchronizes with button presses
- All interactions feel responsive and native

**UIKit Applications:**
- UIAction closures execute without memory leaks
- Target-action patterns work reliably
- Button configurations update properly with state changes
- Async operations run on appropriate actors
- Activity indicators appear during loading states

**User Experience:**
- Buttons provide clear visual, tactile, and auditory feedback
- Loading states prevent accidental double-taps
- Gesture recognizers handle complex interactions smoothly
- Accessibility features work correctly with VoiceOver
- Animations feel fluid and natural

## Troubleshooting

### Issue 1: Button Action Not Firing

**Symptoms:**
- Button appears but doesn't respond to taps
- No visual feedback when pressing button

**Solutions:**

```swift
// Check for overlapping views blocking touches
.zIndex(1) // Bring button to front

// Ensure button isn't disabled
.disabled(false)

// Check for conflicting gestures
.simultaneousGesture(TapGesture()) // Allow simultaneous gestures

// Verify frame/size is sufficient
.frame(minWidth: 44, minHeight: 44) // Minimum tap target
```

### Issue 2: Async Actions Causing Data Race Warnings

**Symptoms:**
- Purple runtime warnings in Xcode 26
- Crashes when updating UI from background thread

**Solutions:**

```swift
// ✓ Correct: Use @MainActor
@MainActor
func updateUI() async {
    // UI updates are safe here
    isLoading = false
}

// ✓ Correct: Explicitly dispatch to main actor
func performAction() async {
    let data = await fetchData() // Background work

    await MainActor.run {
        self.displayData(data) // UI update on main thread
    }
}

// ✗ Incorrect: Direct UI update from Task
Task {
    let data = await fetchData()
    self.data = data // May cause data race
}
```

### Issue 3: Liquid Glass Buttons Not Appearing

**Symptoms:**
- `.glass` or `.glassProminent` styles don't render
- Buttons appear with default styling

**Solutions:**

```swift
// Ensure iOS 26+ deployment target in Xcode project settings
// Build Settings > Deployment > iOS Deployment Target: 26.0

// Check availability
if #available(iOS 26.0, *) {
    Button("New Style") { }
        .buttonStyle(.glassProminent)
} else {
    Button("Fallback Style") { }
        .buttonStyle(.borderedProminent)
}

// Verify device/simulator is running iOS 26+
```

### Issue 4: Haptic Feedback Not Working

**Symptoms:**
- No tactile feedback when buttons are pressed
- Silent operation despite haptic code

**Solutions:**

```swift
// Check device capabilities (simulators don't support haptics)
// Test on physical device

// Ensure proper generator preparation
let haptic = UIImpactFeedbackGenerator(style: .medium)
haptic.prepare() // Prepare before use
haptic.impactOccurred()

// Verify system settings allow haptics
// Settings > Sounds & Haptics > System Haptics
```

### Issue 5: Memory Leaks with Button Closures

**Symptoms:**
- Memory usage grows over time
- View controllers not deallocating

**Solutions:**

```swift
// ✓ Use weak self in closures
button.addAction(UIAction { [weak self] _ in
    self?.handleAction()
}, for: .touchUpInside)

// ✓ Use unowned for guaranteed lifetime
Task { [unowned self] in
    await self.fetchData()
}

// ✗ Avoid strong capture cycles
button.addAction(UIAction { _ in
    self.handleAction() // Strong capture
}, for: .touchUpInside)
```

## Additional Tips

### Performance Optimization

1. **Debounce Rapid Button Taps:**

```swift
class DebouncedButton: ObservableObject {
    @Published var isProcessing = false
    private var debounceTimer: Timer?

    func handleAction(action: @escaping () async -> Void) {
        debounceTimer?.invalidate()
        debounceTimer = Timer.scheduledTimer(withTimeInterval: 0.3, repeats: false) { _ in
            Task {
                await action()
            }
        }
    }
}
```

2. **Optimize Button Rendering:**

```swift
// Use equatable to prevent unnecessary button redraws
struct OptimizedButton: View, Equatable {
    let title: String
    let action: () -> Void

    static func == (lhs: OptimizedButton, rhs: OptimizedButton) -> Bool {
        lhs.title == rhs.title
    }

    var body: some View {
        Button(title, action: action)
            .buttonStyle(.glassProminent)
    }
}
```

### Design Best Practices

1. **Button Hierarchy:**
   - Primary actions: `.glassProminent` or `.borderedProminent`
   - Secondary actions: `.glass` or `.bordered`
   - Tertiary actions: `.plain` or text-only buttons

2. **Button Spacing:**
   - Minimum 8-12 points between adjacent buttons
   - Group related buttons together
   - Use consistent padding within button groups

3. **Loading States:**

```swift
struct LoadingButtonExample: View {
    @State private var isLoading = false

    var body: some View {
        Button {
            Task { await submit() }
        } label: {
            HStack {
                if isLoading {
                    ProgressView()
                        .tint(.white)
                }
                Text(isLoading ? "Submitting..." : "Submit")
            }
            .frame(maxWidth: .infinity)
        }
        .buttonStyle(.glassProminent)
        .disabled(isLoading)
    }

    func submit() async {
        isLoading = true
        defer { isLoading = false }
        // Perform submission
        try? await Task.sleep(for: .seconds(2))
    }
}
```

### Testing Button Interactions

1. **SwiftUI Preview Testing:**

```swift
#Preview("Button States") {
    VStack {
        Button("Normal", action: {})
            .buttonStyle(.glassProminent)

        Button("Disabled", action: {})
            .buttonStyle(.glassProminent)
            .disabled(true)

        Button("Loading", action: {})
            .buttonStyle(.glassProminent)
            .overlay {
                ProgressView()
            }
    }
    .padding()
}
```

2. **UI Testing:**

```swift
import XCTest

class ButtonInteractionTests: XCTestCase {
    func testButtonTap() {
        let app = XCUIApplication()
        app.launch()

        let button = app.buttons["Submit"]
        XCTAssertTrue(button.exists)
        XCTAssertTrue(button.isEnabled)

        button.tap()

        // Verify expected result
        XCTAssertTrue(app.staticTexts["Success"].exists)
    }
}
```

### Integration with iOS 26 Features

1. **Liquid Glass Toolbar Buttons:**

```swift
struct ToolbarGlassButtonsView: View {
    var body: some View {
        NavigationStack {
            ContentView()
                .navigationTitle("My App")
                .toolbar {
                    ToolbarItem(placement: .topBarTrailing) {
                        Button {
                            // Action
                        } label: {
                            Image(systemName: "plus")
                        }
                        .buttonStyle(.glass)
                    }

                    ToolbarItem(placement: .topBarLeading) {
                        Button("Edit") {
                            // Action
                        }
                        .buttonStyle(.glass)
                    }
                }
        }
    }
}
```

2. **Button with Context Menus:**

```swift
struct ContextMenuButtonView: View {
    var body: some View {
        Button("Options") {
            // Primary action
        }
        .buttonStyle(.glassProminent)
        .contextMenu {
            Button("Edit", systemImage: "pencil") {
                // Edit action
            }

            Button("Share", systemImage: "square.and.arrow.up") {
                // Share action
            }

            Divider()

            Button("Delete", systemImage: "trash", role: .destructive) {
                // Delete action
            }
        }
    }
}
```

## Related Articles

- KB-022: How to design adaptive user interfaces for different screen sizes
- KB-024: How to implement forms and input validation
- KB-025: How to use Swift concurrency (async/await) effectively
- KB-026: How to create custom SwiftUI modifiers and components
- KB-027: How to implement navigation and routing patterns
- KB-030: How to handle accessibility and VoiceOver support

## Sources

1. **SwiftUI Buttons Simplified: A Step-by-Step Guide** - Bugfender
   URL: https://bugfender.com/blog/swiftui-buttons/

2. **What's New in Xcode 26: Smarter, Faster, More Powerful — WWDC 2025 Highlights** - 200OK Solutions
   URL: https://200oksolutions.com/blog/whats-new-in-xcode-26-smarter-faster-more-powerful-wwdc-2025-highlights/

3. **SwiftUI in iOS 26: New Features, Improvements & WWDC 2025 Highlights** - Medium
   URL: https://medium.com/@himalimarasinghe/swiftui-in-ios-26-whats-new-from-wwdc-2025-be6b4864ce05

4. **Understanding SwiftUI's liquid glass button styles** - Tanaschita
   URL: https://tanaschita.com/swiftui-glass-button-style/

5. **Designing custom UI with Liquid Glass on iOS 26** - Donny Wals
   URL: https://www.donnywals.com/designing-custom-ui-with-liquid-glass-on-ios-26/

6. **GlassButtonStyle | Apple Developer Documentation**
   URL: https://developer.apple.com/documentation/swiftui/glassbuttonstyle

7. **UIKit & SwiftUI: Bridging the Gap in iOS 26** - Medium
   URL: https://medium.com/@pankajtalreja/uikit-swiftui-bridging-the-gap-in-ios-26-828e9d8b50f8

8. **Responding to control-based events using target-action | Apple Developer Documentation**
   URL: https://developer.apple.com/documentation/uikit/views_and_controls/responding_to_control-based_events_using_target-action

9. **2025 Guide to Haptics: Enhancing Mobile UX with Tactile Feedback** - Medium
   URL: https://saropa-contacts.medium.com/2025-guide-to-haptics-enhancing-mobile-ux-with-tactile-feedback-676dd5937774

10. **Handling tap gestures | Apple Developer Documentation**
    URL: https://developer.apple.com/documentation/uikit/touches_presses_and_gestures/handling_uikit_gestures/handling_tap_gestures

11. **Default Actor Isolation in Swift 6.2** - SwiftLee
    URL: https://www.avanderlee.com/concurrency/default-actor-isolation-in-swift-6-2/

---

**Document History:**
- **v1.0** (November 17, 2025): Initial publication covering Xcode 26.1+, Swift 6.0+, and iOS 26 Liquid Glass design system
