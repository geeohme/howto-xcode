# How to use AI to generate complex custom view modifiers

**Article ID:** KB-073
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 20-25 minutes

## Overview

Custom view modifiers are powerful SwiftUI tools that encapsulate reusable styling and behavior logic. This guide demonstrates how to leverage Xcode 26.1+'s AI Coding Intelligence (ChatGPT and Claude integration) to generate complex, production-ready custom view modifiers that would take significant time to write manually. You'll learn effective prompting strategies, best practices for refining AI-generated code, and techniques for creating sophisticated modifiers that enhance code reusability across your SwiftUI projects.

## Prerequisites

- Xcode 26.1 or later installed on macOS
- Basic understanding of SwiftUI views and built-in modifiers
- AI provider configured (ChatGPT or Claude) - see [KB-031](kb-article-031-access-coding-assistant-xcode-26-chatgpt.md) or [KB-043](kb-article-043-add-claude-model-provider-xcode.md)
- Familiarity with Swift structs and protocols
- Understanding of the ViewModifier protocol concept

## Steps

### Step 1: Understand Custom View Modifier Requirements

Before prompting AI, clarify what your custom modifier should accomplish. Custom view modifiers conform to the `ViewModifier` protocol and must implement a `body(content:)` method that transforms the input view.

Open your SwiftUI project in Xcode 26.1+ and navigate to the file where you want to add custom modifiers (typically a separate `ViewModifiers.swift` file for organization).

**Key considerations for complex modifiers:**
- What visual styling or behavior do you need?
- Will it require parameters (e.g., colors, sizes, animations)?
- Does it need @State or @Binding properties?
- Should it work with specific view types or be generic?

### Step 2: Access the AI Coding Assistant

Open the Coding Assistant panel using one of these methods:
- Press **Cmd + Shift + A** (default keyboard shortcut)
- Go to **Editor > Coding Assistant** in the menu bar
- Click the AI assistant icon in the Xcode toolbar

The assistant panel will appear on the right side of your Xcode window, ready to receive prompts.

### Step 3: Craft an Effective Prompt for Basic Custom Modifier

Start with a clear, specific prompt that describes your modifier's purpose. For a basic example, let's create a card-style modifier.

**Example Prompt:**
```
Create a SwiftUI custom view modifier called CardStyle that:
- Adds rounded corners with 12pt radius
- Applies a white background
- Adds a shadow with radius 5 and opacity 0.2
- Includes 16pt padding
- Make it conform to ViewModifier protocol
- Add a View extension for easy usage
```

Press **Enter** or click **Send** to submit your prompt. The AI will generate code similar to:

```swift
struct CardStyle: ViewModifier {
    func body(content: Content) -> some View {
        content
            .padding(16)
            .background(Color.white)
            .cornerRadius(12)
            .shadow(radius: 5, x: 0, y: 2)
            .opacity(0.2)
    }
}

extension View {
    func cardStyle() -> some View {
        modifier(CardStyle())
    }
}
```

**Review the output:** Check if the AI correctly understood your requirements. In this case, you might notice the shadow opacity was applied to the entire view instead of just the shadow—this is where refinement comes in.

### Step 4: Refine the AI-Generated Code with Follow-up Prompts

Use conversational follow-ups to fix issues or add features. The AI maintains context from previous messages in the conversation.

**Example Follow-up:**
```
The shadow opacity should only apply to the shadow, not the entire view.
Fix this and also make the background color customizable with a parameter.
```

The AI will regenerate the code with corrections:

```swift
struct CardStyle: ViewModifier {
    let backgroundColor: Color

    init(backgroundColor: Color = .white) {
        self.backgroundColor = backgroundColor
    }

    func body(content: Content) -> some View {
        content
            .padding(16)
            .background(backgroundColor)
            .cornerRadius(12)
            .shadow(color: Color.black.opacity(0.2), radius: 5, x: 0, y: 2)
    }
}

extension View {
    func cardStyle(backgroundColor: Color = .white) -> some View {
        modifier(CardStyle(backgroundColor: backgroundColor))
    }
}
```

### Step 5: Generate Complex Modifiers with Multiple Features

For more sophisticated modifiers, provide detailed requirements in a single comprehensive prompt. Let's create an animated shimmer loading effect modifier.

**Example Advanced Prompt:**
```
Create a SwiftUI custom view modifier called ShimmerEffect that:
- Creates an animated shimmer/skeleton loading effect
- Uses a linear gradient that moves from left to right repeatedly
- The gradient should have three colors: light gray, white, light gray
- Animation should be continuous with 2-second duration
- Use .easeInOut animation curve
- Add a parameter to control if the effect is active (Bool)
- Include proper GeometryReader for responsive sizing
- Add View extension for easy application
- Make it work on any View type
```

The AI will generate a comprehensive implementation:

```swift
struct ShimmerEffect: ViewModifier {
    let isActive: Bool
    @State private var phase: CGFloat = 0

    func body(content: Content) -> some View {
        content
            .overlay(
                GeometryReader { geometry in
                    if isActive {
                        LinearGradient(
                            gradient: Gradient(colors: [
                                Color.gray.opacity(0.3),
                                Color.white.opacity(0.8),
                                Color.gray.opacity(0.3)
                            ]),
                            startPoint: .leading,
                            endPoint: .trailing
                        )
                        .frame(width: geometry.size.width * 2)
                        .offset(x: -geometry.size.width + (phase * geometry.size.width * 2))
                        .onAppear {
                            withAnimation(
                                Animation.easeInOut(duration: 2)
                                    .repeatForever(autoreverses: false)
                            ) {
                                phase = 1
                            }
                        }
                    }
                }
            )
            .mask(content)
    }
}

extension View {
    func shimmer(isActive: Bool = true) -> some View {
        modifier(ShimmerEffect(isActive: isActive))
    }
}
```

### Step 6: Request Variations and Alternative Implementations

Ask the AI to provide alternative approaches or variations for different use cases.

**Example Prompt:**
```
Now create a variation of the shimmer effect that:
- Goes vertically instead of horizontally
- Has customizable colors as parameters
- Includes an option to control animation speed
```

This helps you explore different design patterns and learn various implementation techniques.

### Step 7: Generate Modifiers with State Management

For modifiers that need to track and manage state, be explicit about state requirements.

**Example Prompt:**
```
Create a PressableScale view modifier that:
- Scales the view down to 0.95 when pressed
- Scales back to 1.0 when released
- Uses spring animation with response 0.3 and dampingFraction 0.6
- Tracks press state internally
- Works with any tappable view
- Include haptic feedback on press
```

AI-generated result:

```swift
struct PressableScale: ViewModifier {
    @State private var isPressed = false
    let hapticFeedback = UIImpactFeedbackGenerator(style: .light)

    func body(content: Content) -> some View {
        content
            .scaleEffect(isPressed ? 0.95 : 1.0)
            .animation(
                .spring(response: 0.3, dampingFraction: 0.6),
                value: isPressed
            )
            .gesture(
                DragGesture(minimumDistance: 0)
                    .onChanged { _ in
                        if !isPressed {
                            isPressed = true
                            hapticFeedback.impactOccurred()
                        }
                    }
                    .onEnded { _ in
                        isPressed = false
                    }
            )
    }
}

extension View {
    func pressableScale() -> some View {
        modifier(PressableScale())
    }
}
```

### Step 8: Combine Multiple Modifiers into a Compound Modifier

Request AI to create sophisticated modifiers that combine multiple effects.

**Example Prompt:**
```
Create a GlassmorphicCard view modifier that combines:
- Frosted glass blur effect (Material.ultraThinMaterial)
- Subtle gradient border (2pt width)
- Corner radius (20pt)
- Custom padding (20pt)
- Shadow with color parameter
- Border gradient from top-left light color to bottom-right darker color
- Make all parameters customizable with sensible defaults
```

The AI will generate a comprehensive compound modifier with multiple visual effects working together.

### Step 9: Test and Validate AI-Generated Modifiers

Copy the AI-generated code into your Xcode project and test it thoroughly:

1. Create a test SwiftUI preview to verify the modifier works:

```swift
struct ModifierPreview: View {
    var body: some View {
        VStack(spacing: 20) {
            Text("Card Example")
                .cardStyle()

            Rectangle()
                .frame(height: 100)
                .shimmer(isActive: true)

            Text("Press Me")
                .pressableScale()
        }
        .padding()
    }
}

#Preview {
    ModifierPreview()
}
```

2. Build the project (**Cmd + B**) to check for compilation errors
3. Run in the iOS Simulator to test visual appearance and animations
4. Test with different parameter values
5. Verify the modifier works on different view types

### Step 10: Optimize and Refine with AI Assistance

If you notice performance issues or want improvements, ask the AI to optimize:

**Example Optimization Prompt:**
```
The ShimmerEffect might be performance-intensive.
Optimize it for better performance by:
- Using drawingGroup() where appropriate
- Minimizing state updates
- Add comments explaining performance considerations
```

Or request best practices:

```
Review this CardStyle modifier and suggest improvements for:
- iOS 18.2+ best practices
- Accessibility support
- Dark mode compatibility
- Dynamic Type support
```

### Step 11: Generate Documentation with AI

Once your modifier is finalized, ask AI to generate comprehensive documentation:

**Example Prompt:**
```
Add detailed Swift documentation comments to the CardStyle modifier including:
- Summary description
- Parameter documentation
- Usage example in the comments
- Availability information for iOS 18.2+
```

The AI will add proper documentation:

```swift
/// A view modifier that applies a card-style appearance to any SwiftUI view.
///
/// The card style includes rounded corners, background color, padding, and a subtle shadow
/// for a modern, elevated appearance commonly used in iOS applications.
///
/// - Parameters:
///   - backgroundColor: The background color of the card. Defaults to white.
///
/// # Example
/// ```swift
/// Text("Hello, World!")
///     .cardStyle(backgroundColor: .white)
/// ```
///
/// - Important: This modifier is optimized for iOS 18.2+ and supports dark mode.
/// - Version: iOS 18.2+
@available(iOS 18.2, *)
struct CardStyle: ViewModifier {
    let backgroundColor: Color

    init(backgroundColor: Color = .white) {
        self.backgroundColor = backgroundColor
    }

    func body(content: Content) -> some View {
        content
            .padding(16)
            .background(backgroundColor)
            .cornerRadius(12)
            .shadow(color: Color.black.opacity(0.2), radius: 5, x: 0, y: 2)
    }
}
```

## Expected Results

After following this guide, you should have:

- **Functional Custom Modifiers**: AI-generated ViewModifier structs that compile without errors and work as intended
- **View Extensions**: Convenient extension methods that make your modifiers feel native to SwiftUI
- **Working Examples**: Test code demonstrating the modifiers in action
- **Documented Code**: Proper Swift documentation comments explaining usage and parameters
- **Reusable Components**: Modifiers saved in your project that can be applied throughout your codebase

**Visual Results:**
- Cards with consistent styling across your app
- Smooth animations and interactive effects
- Loading states with shimmer effects
- Professional-looking UI components with minimal code duplication

**Code Quality:**
- Modifiers follow SwiftUI best practices
- Clean, readable code structure
- Proper separation of concerns
- Type-safe implementations with Swift's strong typing

## Troubleshooting

### AI Generates Incorrect ViewModifier Syntax

**Problem:** The AI generates code that doesn't conform properly to ViewModifier protocol or has compilation errors.

**Solution:**
- Be more explicit in your prompt: "Ensure it conforms to the ViewModifier protocol from SwiftUI"
- Provide a code template in your prompt showing the basic structure you expect
- Try switching AI models (e.g., from GPT-5 to GPT-5 Reasoning or Claude Sonnet 4)
- Ask the AI to explain any compilation errors: "Why is this code giving a 'Type X does not conform to protocol ViewModifier' error?"

### Generated Modifier Doesn't Work with All View Types

**Problem:** The modifier works with some views but fails with others, or causes unexpected layout issues.

**Solution:**
- Specify in your prompt: "Make it work with any View type using generics"
- Ask the AI: "Why doesn't this modifier work when applied to a Button?"
- Request: "Modify this to work with views that already have gestures attached"
- Use `.contentShape()` if the modifier affects touch targets

### Animation Performance Issues

**Problem:** AI-generated animations are choppy or cause performance degradation.

**Solution:**
- Ask the AI: "Optimize this animation modifier for 60fps performance"
- Request addition of `.drawingGroup()` for complex animations
- Prompt: "Use Animatable protocol instead of repeated state updates"
- Reduce animation complexity: "Simplify the gradient animation to use fewer color stops"

### Modifier Parameters Not Updating

**Problem:** Changing modifier parameters in parent view doesn't update the modifier's appearance.

**Solution:**
- Ensure the modifier doesn't capture values incorrectly
- Ask AI: "Make all parameters reactive to parent view updates"
- Check that `@State` variables are used correctly within the modifier
- For external state, request `@Binding` parameters: "Add a @Binding parameter to allow parent view control"

### Dark Mode or Accessibility Issues

**Problem:** The generated modifier doesn't adapt to dark mode or lacks accessibility support.

**Solution:**
- Add to your prompt: "Support both light and dark mode using Color.adaptive or semantic colors"
- Request: "Add accessibility labels and hints to this modifier"
- Ask: "Make this modifier support Dynamic Type for text scaling"
- Prompt: "Use system colors instead of hardcoded RGB values for better dark mode support"

## Additional Tips

- **Start Simple, Iterate**: Begin with basic modifier functionality, then add complexity through follow-up prompts. This gives you better control and understanding of each feature.

- **Use Semantic Naming**: When prompting AI, use clear, descriptive names for modifiers (e.g., "GlassmorphicCard" rather than "Modifier1"). The AI will generate better documentation and examples.

- **Request Multiple Options**: Ask "Give me three different approaches to implement this modifier" to explore various design patterns and choose the best fit.

- **Specify iOS Version**: Always mention the target iOS version (e.g., "iOS 18.2+") to ensure AI uses appropriate APIs and doesn't suggest deprecated methods.

- **Combine with Existing Code**: When adding AI-generated modifiers to existing projects, provide context: "I already have a Theme system with these colors: [list]. Create a modifier that uses these."

- **Save Conversation History**: Keep your AI conversation open while iterating. The context helps the AI understand your project's patterns and maintain consistency.

- **Test Edge Cases**: Ask the AI to generate test scenarios: "What edge cases should I test for this modifier? Generate test code for each."

- **Version Control**: Commit working AI-generated code before requesting major changes, so you can easily revert if needed.

- **Learn from AI Code**: Review the generated code to understand the techniques used. Ask: "Explain why you used GeometryReader here instead of frame()?"

- **Create Modifier Libraries**: Organize related modifiers in dedicated files (e.g., `CardModifiers.swift`, `AnimationModifiers.swift`) for better project structure.

- **Compare AI Models**: For complex modifiers, try both ChatGPT and Claude. GPT-5 Reasoning is excellent for complex logic, while Claude Sonnet 4 often provides more elegant SwiftUI code.

- **Request Unit Tests**: Advanced users can ask: "Generate XCTest unit tests for this ViewModifier to verify behavior."

## Related Articles

- [KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT](kb-article-031-access-coding-assistant-xcode-26-chatgpt.md)
- [KB-037: How to generate SwiftUI views with ChatGPT](kb-article-037-generate-swiftui-views-chatgpt.md)
- [KB-043: How to add Claude as a model provider in Xcode](kb-article-043-add-claude-model-provider-xcode.md)
- [KB-046: How to switch between ChatGPT and Claude in Coding Assistant](kb-article-046-switch-chatgpt-claude-coding-assistant.md)
- [KB-052: How to prompt AI assistants to create SwiftUI preview code](kb-article-052-prompt-ai-assistants-create-swiftui-preview-code.md)
- [KB-069: How to reverse engineer and customize Coding Intelligence prompts](kb-article-069-reverse-engineer-customize-coding-intelligence-prompts.md)

## Sources

- Writing code with intelligence in Xcode - Apple Developer Documentation - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- ViewModifier - Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/viewmodifier (Accessed: November 17, 2025)
- Xcode 26 will support multiple AI models, like Claude - 9to5Mac - https://9to5mac.com/2025/06/10/beyond-chatgpt-xcode-26-will-support-multiple-ai-models-like-claude/ (Accessed: November 17, 2025)
- Apple Releases Xcode 26 Beta 7 With GPT-5 Support and Claude Integration - MacRumors - https://www.macrumors.com/2025/08/28/xcode-gpt-5-claude-integration/ (Accessed: November 17, 2025)
- Custom modifiers - Hacking with iOS: SwiftUI Edition - https://www.hackingwithswift.com/books/ios-swiftui/custom-modifiers (Accessed: November 17, 2025)
- SwiftUI Custom View Modifiers - Use Your Loaf - https://useyourloaf.com/blog/swiftui-custom-view-modifiers/ (Accessed: November 17, 2025)
- SwiftUI View Modifiers Tutorial for iOS - Kodeco - https://www.kodeco.com/34699757-swiftui-view-modifiers-tutorial-for-ios (Accessed: November 17, 2025)
- Xcode 26 is here, with Code Intelligence - Medium - https://dimillian.medium.com/xcode-26-is-here-with-code-intelligence-93563624da81 (Accessed: November 17, 2025)
- Reverse-Engineering Xcode's Coding Intelligence prompt - Peter Friese - https://peterfriese.dev/blog/2025/reveng-xcode-coding-intelligence/ (Accessed: November 17, 2025)
- Code with Intelligence — xCode 26 - Medium - https://medium.com/@sachinsiwal/code-with-intelligence-xcode-26-9203a8ce665e (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
