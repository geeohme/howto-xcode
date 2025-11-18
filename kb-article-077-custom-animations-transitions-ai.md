# How to implement custom animations and transitions using AI suggestions

**Article ID:** KB-077
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 25-30 minutes

## Overview

Learn how to leverage Xcode 26.1's AI-powered coding assistant to design, implement, and refine custom SwiftUI animations and transitions for iOS 18.2+. This guide demonstrates how to use AI suggestions to create sophisticated animations, including keyframe-based animations, custom transitions, and complex timing effects without memorizing extensive API documentation.

## Prerequisites

- Xcode 26.1 or later installed
- macOS with Apple Intelligence enabled (see KB-003)
- Active AI model provider configured (ChatGPT, Claude, or local models)
- Basic understanding of SwiftUI views and modifiers
- Familiarity with SwiftUI state management (@State, @Binding)
- Knowledge of the Xcode AI coding assistant (see KB-031, KB-032)

## Steps

### Step 1: Access the AI Coding Assistant for Animation Planning

Before writing code, use the AI assistant to explore animation options and get implementation guidance.

1. Open your SwiftUI project in Xcode 26.1+
2. Press **Cmd + Shift + A** to open the AI coding assistant panel
3. In the assistant chat, describe your animation goal in natural language

**Example prompt:**
```
I need to create a custom card flip animation in SwiftUI for iOS 18.2.
The card should rotate 180 degrees on the Y-axis when tapped, revealing
content on the back. What's the best approach using modern SwiftUI APIs?
```

The AI will suggest appropriate animation techniques, such as using `.rotation3DEffect()`, `.animation()`, or the new `KeyframeAnimator` API from iOS 18.

**Tip:** Be specific about your iOS version (18.2+) to ensure the AI suggests the latest APIs rather than deprecated approaches.

### Step 2: Generate Basic Animation Code Structure

Ask the AI to generate the foundational code for your animation.

**Prompt example:**
```
Generate a SwiftUI view that implements a spring animation for a button
scale effect. Use iOS 18.2 best practices and include state management.
```

The AI will generate code similar to:

```swift
import SwiftUI

struct AnimatedButtonView: View {
    @State private var isPressed = false

    var body: some View {
        Button("Tap Me") {
            withAnimation(.spring(response: 0.3, dampingFraction: 0.6)) {
                isPressed.toggle()
            }
        }
        .scaleEffect(isPressed ? 1.2 : 1.0)
        .buttonStyle(.borderedProminent)
    }
}

#Preview {
    AnimatedButtonView()
}
```

**Note:** The AI automatically includes a SwiftUI preview for immediate testing, a feature enhanced in Xcode 26.

### Step 3: Request Custom Transition Implementation

For custom transitions, provide the AI with specific requirements about the transition behavior.

**Prompt example:**
```
Create a custom SwiftUI transition that slides in from the bottom with a
fade effect and slight scale change. Make it reusable as a ViewModifier.
```

The AI will generate a custom transition:

```swift
import SwiftUI

// Custom transition modifier
struct SlideAndFadeTransition: ViewModifier {
    let isActive: Bool

    func body(content: Content) -> some View {
        content
            .offset(y: isActive ? 0 : 50)
            .opacity(isActive ? 1 : 0)
            .scaleEffect(isActive ? 1.0 : 0.95)
    }
}

extension AnyTransition {
    static var slideAndFade: AnyTransition {
        .modifier(
            active: SlideAndFadeTransition(isActive: false),
            identity: SlideAndFadeTransition(isActive: true)
        )
    }
}

// Usage example
struct ContentView: View {
    @State private var showDetails = false

    var body: some View {
        VStack {
            Button("Toggle Details") {
                withAnimation(.easeInOut(duration: 0.4)) {
                    showDetails.toggle()
                }
            }

            if showDetails {
                Text("Details appear here")
                    .padding()
                    .background(.blue.opacity(0.2))
                    .cornerRadius(10)
                    .transition(.slideAndFade)
            }
        }
        .padding()
    }
}
```

### Step 4: Implement KeyframeAnimator for Complex Animations

For timeline-based animations with multiple stages, ask the AI to use iOS 18's `KeyframeAnimator`.

**Prompt example:**
```
Use KeyframeAnimator to create a celebration animation where a star icon
scales up, rotates 360 degrees, and changes color, all in a 2-second sequence.
```

Generated code:

```swift
import SwiftUI

struct CelebrationView: View {
    @State private var trigger = false

    var body: some View {
        VStack {
            Button("Celebrate!") {
                trigger.toggle()
            }

            KeyframeAnimator(
                initialValue: AnimationValues(),
                trigger: trigger
            ) { values in
                Image(systemName: "star.fill")
                    .font(.system(size: 60))
                    .foregroundStyle(values.color)
                    .scaleEffect(values.scale)
                    .rotationEffect(values.rotation)
            } keyframes: { _ in
                KeyframeTrack(\.scale) {
                    SpringKeyframe(1.5, duration: 0.4, spring: .bouncy)
                    SpringKeyframe(1.0, duration: 0.3)
                }

                KeyframeTrack(\.rotation) {
                    LinearKeyframe(.degrees(0), duration: 0.0)
                    LinearKeyframe(.degrees(360), duration: 0.7)
                }

                KeyframeTrack(\.color) {
                    LinearKeyframe(.yellow, duration: 0.3)
                    LinearKeyframe(.orange, duration: 0.2)
                    LinearKeyframe(.red, duration: 0.2)
                    LinearKeyframe(.yellow, duration: 0.3)
                }
            }
        }
    }
}

struct AnimationValues {
    var scale: CGFloat = 1.0
    var rotation: Angle = .degrees(0)
    var color: Color = .yellow
}

#Preview {
    CelebrationView()
}
```

### Step 5: Refine Animation Timing and Easing

Use the AI to experiment with different timing curves and spring parameters.

**Prompt example:**
```
The scale animation feels too abrupt. Adjust the spring parameters to make
it feel more natural and playful. Explain the different spring options.
```

The AI will provide explanations and suggest modifications:

```swift
// Original: .spring(response: 0.3, dampingFraction: 0.6)
// More bouncy: .spring(response: 0.4, dampingFraction: 0.5)
// Smoother: .spring(response: 0.6, dampingFraction: 0.8)

// iOS 18.2 also provides named spring presets:
.animation(.bouncy, value: isPressed)      // Playful bounce
.animation(.smooth, value: isPressed)      // Gentle, smooth
.animation(.snappy, value: isPressed)      // Quick, responsive
```

The AI can explain that lower damping creates more bounce, while higher response values slow down the animation.

### Step 6: Add SF Symbols Animations (iOS 18+)

Request AI assistance for integrating animated SF Symbols introduced in iOS 18.

**Prompt example:**
```
Add an animated SF Symbols icon that wiggles when a notification arrives.
Use iOS 18.2 symbol animation APIs.
```

```swift
import SwiftUI

struct NotificationBadgeView: View {
    @State private var hasNotification = false

    var body: some View {
        VStack {
            Image(systemName: "bell.fill")
                .font(.system(size: 50))
                .symbolEffect(.wiggle, value: hasNotification)
                .foregroundStyle(.blue)

            Button("Simulate Notification") {
                hasNotification.toggle()
            }
        }
    }
}

// Other symbol animations available:
// .symbolEffect(.bounce, value: trigger)
// .symbolEffect(.pulse, value: trigger)
// .symbolEffect(.breathe, value: trigger)
```

### Step 7: Create Navigation Transitions with Zoom Effect

Ask the AI to implement iOS 18's new zoom navigation transitions.

**Prompt example:**
```
Create a list-to-detail navigation that uses the new zoom transition
from iOS 18. When tapping a card, it should zoom from the list into
the detail view.
```

```swift
import SwiftUI

struct PhotoGalleryView: View {
    @Namespace private var namespace
    @State private var selectedPhoto: Photo?

    var body: some View {
        NavigationStack {
            ScrollView {
                LazyVGrid(columns: [GridItem(.adaptive(minimum: 150))]) {
                    ForEach(photos) { photo in
                        PhotoThumbnail(photo: photo)
                            .onTapGesture {
                                selectedPhoto = photo
                            }
                            .matchedTransitionSource(
                                id: photo.id,
                                in: namespace
                            )
                    }
                }
            }
            .navigationDestination(item: $selectedPhoto) { photo in
                PhotoDetailView(photo: photo)
                    .navigationTransition(.zoom(
                        sourceID: photo.id,
                        in: namespace
                    ))
            }
            .navigationTitle("Photos")
        }
    }
}

struct Photo: Identifiable {
    let id = UUID()
    let name: String
    let color: Color
}

let photos = [
    Photo(name: "Sunset", color: .orange),
    Photo(name: "Ocean", color: .blue),
    Photo(name: "Forest", color: .green)
]
```

### Step 8: Test and Debug Animations with AI Assistance

Use the AI to identify and fix animation performance issues.

**Prompt example:**
```
My animation is janky and dropping frames. Here's my code: [paste code]
What's causing the performance issue and how can I fix it?
```

The AI can identify common issues:
- Animations triggering too frequently
- Heavy computations inside animation blocks
- Missing `.drawingGroup()` for complex shapes
- Inefficient state updates

**Suggested fix example:**
```swift
// Before (causes jank)
.opacity(calculateComplexOpacity()) // Recalculated every frame

// After (smooth)
.opacity(cachedOpacity) // Computed once, cached in @State
.animation(.easeInOut, value: cachedOpacity)
```

### Step 9: Generate Preview Code for Multiple Animation States

Ask the AI to create comprehensive previews showing different animation states.

**Prompt example:**
```
Create SwiftUI previews that show my animation in its start, middle,
and end states side-by-side for design review.
```

```swift
#Preview("Animation States") {
    HStack(spacing: 20) {
        // Start state
        VStack {
            AnimatedCard(progress: 0.0)
            Text("Start")
                .font(.caption)
        }

        // Middle state
        VStack {
            AnimatedCard(progress: 0.5)
            Text("Middle")
                .font(.caption)
        }

        // End state
        VStack {
            AnimatedCard(progress: 1.0)
            Text("End")
                .font(.caption)
        }
    }
    .padding()
}
```

### Step 10: Document Complex Animations with AI

Request AI-generated documentation for your custom animations.

**Prompt example:**
```
Add comprehensive documentation comments to this animation code
explaining the timing, purpose, and customization options.
```

The AI will add clear documentation:

```swift
/// A custom transition that combines slide, fade, and scale effects.
///
/// This transition creates a subtle entry animation suitable for modal
/// presentations and detail views. The effect includes:
/// - Vertical slide from 50 points below
/// - Opacity fade from 0 to 1
/// - Slight scale from 0.95 to 1.0
///
/// - Parameters:
///   - duration: Animation duration in seconds (default: 0.4)
///   - edge: The edge from which to slide (default: .bottom)
///
/// Example usage:
/// ```swift
/// if showView {
///     DetailView()
///         .transition(.slideAndFade)
/// }
/// ```
struct SlideAndFadeTransition: ViewModifier {
    // Implementation...
}
```

## Expected Results

After completing these steps, you should have:

1. **AI-generated animation code** that follows iOS 18.2+ best practices
2. **Custom transitions** implemented as reusable ViewModifiers
3. **Keyframe animations** for complex, multi-stage effects
4. **SF Symbols animations** integrated with modern APIs
5. **Smooth, performant animations** optimized through AI suggestions
6. **Comprehensive previews** showing multiple animation states
7. **Well-documented code** with clear explanations of timing and behavior

When you run your app or preview, animations should:
- Execute smoothly at 60fps without dropped frames
- Feel natural with appropriate spring physics or easing curves
- Respond immediately to user interactions
- Match iOS design guidelines and conventions

## Troubleshooting

### Issue 1: AI Suggests Deprecated Animation APIs

**Problem:** The AI generates code using `.animation(_:)` without a value parameter, which is deprecated in iOS 18+.

**Solution:** Explicitly tell the AI to use iOS 18.2+ APIs in your prompt:
```
Update this animation to use iOS 18.2 best practices with the .animation(_:value:) modifier
```

The AI will correct to:
```swift
// Deprecated (avoid)
.animation(.spring())

// iOS 18.2+ (correct)
.animation(.spring(), value: isAnimating)
```

### Issue 2: KeyframeAnimator Code Doesn't Compile

**Problem:** AI-generated `KeyframeAnimator` code shows compilation errors about missing protocol conformances.

**Solution:** Ensure your animation values struct is mutable. Ask the AI:
```
Fix the compilation errors in this KeyframeAnimator code
```

The AI will add necessary conformances:
```swift
struct AnimationValues {
    var scale: CGFloat = 1.0
    var rotation: Angle = .degrees(0)
    // Must be var, not let, for keyframe mutations
}
```

### Issue 3: Custom Transitions Don't Animate

**Problem:** Custom transition appears/disappears instantly without animating.

**Solution:** Verify you're wrapping state changes in `withAnimation`. Ask the AI:
```
My custom transition isn't animating. Here's my toggle code: [paste code]
```

The AI will identify missing animation wrapper:
```swift
// Wrong - no animation
showView.toggle()

// Correct - wrapped in animation
withAnimation(.easeInOut(duration: 0.4)) {
    showView.toggle()
}
```

### Issue 4: AI Generated Code Uses Wrong SwiftUI Version

**Problem:** AI suggests `UIViewRepresentable` solutions instead of native SwiftUI for animations available in iOS 18.2.

**Solution:** Reinforce the version requirement:
```
Please use only native SwiftUI APIs available in iOS 18.2+.
Avoid UIKit bridges for this animation.
```

## Additional Tips

- **Iterate with the AI**: Start with a basic animation and ask the AI to enhance it incrementally rather than requesting everything at once
- **Use specific prompts**: Instead of "make it better," say "increase the bounce by 20% and make it faster"
- **Request explanations**: Ask "why did you choose these spring parameters?" to learn animation principles
- **Test on device**: Animations may feel different on physical devices vs. simulator; ask the AI for device-specific optimizations
- **Combine animations**: Request the AI to layer multiple animation effects (scale + rotation + opacity) for richer interactions
- **Ask for alternatives**: Request "show me 3 different timing curve options for this transition" to explore variations
- **Accessibility**: Prompt the AI to respect "Reduce Motion" settings with `.accessibilityReduceMotion` checks
- **Save successful prompts**: Keep a library of effective animation prompts for reuse across projects
- **Reference WWDC sessions**: Mention "use techniques from WWDC24 session on animations" for cutting-edge approaches
- **Preview-driven development**: Ask the AI to generate multiple preview variations for rapid design iteration

## Related Articles

- KB-031: How to access the coding assistant in Xcode 26 (ChatGPT)
- KB-032: How to start a conversation with ChatGPT in Xcode
- KB-037: How to generate SwiftUI views using ChatGPT
- KB-046: How to switch between ChatGPT and Claude as your coding assistant
- KB-051: How to use AI to generate unit tests for existing code
- KB-052: How to prompt AI assistants to create SwiftUI preview code
- KB-056: How to use AI to optimize slow-performing code

## Sources

- Swift with Vincent - Discover 5 new AI features of Xcode 26 - https://www.swiftwithvincent.com/blog/discover-5-new-ai-features-of-xcode-26 (Accessed: November 17, 2025)
- Medium - WWDC25 for Developers: What's New in SwiftUI, Xcode 26, On-Device AI, and More - https://medium.com/@navinkumar7582/wwdc25-for-developers-whats-new-in-swiftui-xcode-26-on-device-ai-and-more-03fa39a48a0f (Accessed: November 17, 2025)
- Medium - SwiftUI Animations in 2025: Master Implicit, Explicit & Matched Geometry Effects - https://ravi6997.medium.com/swiftui-animations-in-2025-master-the-art-of-fluid-motion-design-0bbaf7a1b9a1 (Accessed: November 17, 2025)
- DEV Community - Advanced SwiftUI Animations (2025 Guide) - https://dev.to/swift_pal/advanced-swiftui-animations-2025-guide-2ekd (Accessed: November 17, 2025)
- Apple Developer Documentation - Transition - https://developer.apple.com/documentation/swiftui/transition (Accessed: November 17, 2025)
- Apple Developer Documentation - Animating views and transitions - https://developer.apple.com/tutorials/swiftui/animating-views-and-transitions (Accessed: November 17, 2025)
- Apple Developer - Enhance your UI animations and transitions (WWDC24) - https://developer.apple.com/videos/play/wwdc2024/10145/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
