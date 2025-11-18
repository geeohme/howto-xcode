# How to prompt AI assistants to create SwiftUI preview code

**Article ID:** KB-052
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 15-20 minutes

## Overview

Xcode 26.1+ integrates powerful AI coding assistants like ChatGPT and Claude directly into your development workflow, enabling you to generate comprehensive SwiftUI preview code through natural language prompts. This capability dramatically accelerates UI development by automating the creation of preview configurations for multiple device types, color schemes, accessibility settings, and edge cases. This tutorial teaches you how to craft effective prompts that generate high-quality, production-ready preview code with mock data, covering various states and scenarios without manual implementation.

## Prerequisites

- Xcode 26.1 or later installed
- macOS 15 (Sequoia) or later on Apple Silicon Mac (M1 or newer)
- Apple Intelligence enabled in system settings
- ChatGPT or Claude configured in Xcode Intelligence settings
- Basic understanding of SwiftUI previews and the #Preview macro
- Familiarity with SwiftUI view structure

**Related Prerequisites:**
- KB-024: How to preview your SwiftUI views using Xcode Previews
- KB-031: How to access the Coding Assistant in Xcode 26 (ChatGPT)
- KB-037: How to generate SwiftUI views from natural language descriptions using ChatGPT
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings

## Steps

### Step 1: Understand When to Use AI for Preview Generation

AI assistants excel at generating preview code because previews are development-only artifacts that don't ship in production apps, making them an ideal low-risk use case for AI-generated code. The AI can automatically create comprehensive preview configurations including mock data, multiple device types, and edge cases that would be tedious to write manually.

**Best use cases for AI-generated previews:**

1. **Multiple device configurations** - Generate previews for iPhone SE, iPhone 16 Pro, iPad Pro, and Apple Watch simultaneously
2. **Dark mode and light mode variants** - Create paired previews showing both color schemes
3. **Accessibility configurations** - Generate previews with different dynamic type sizes and localization settings
4. **Edge cases and states** - Automatically create previews for empty states, loading states, error states, and boundary conditions
5. **Complex mock data** - Generate realistic sample data structures to populate your views
6. **Preview modifiers and traits** - Apply appropriate preview traits for orientations, layouts, and device characteristics

**When AI saves the most time:**
- Creating comprehensive preview suites for new views (5-10 minutes saved per view)
- Testing responsive layouts across multiple devices (10+ minutes saved)
- Generating mock data models and sample instances (5-15 minutes saved)
- Documenting component variants for design systems

### Step 2: Access the AI Coding Assistant in Xcode

Before generating preview code, ensure your AI assistant is properly configured:

**Using ChatGPT (built-in):**
1. Open Xcode 26.1 or later
2. Navigate to **Xcode** → **Settings** → **Intelligence**
3. Verify ChatGPT is listed under **Model Providers**
4. Select your preferred model (GPT-5 Reasoning or GPT-5 recommended)

**Using Claude (requires API key):**
1. Navigate to **Xcode** → **Settings** → **Intelligence**
2. Click **Add a Model Provider**
3. Select **Claude** from the list
4. Enter your Anthropic API key
5. Choose Claude Sonnet 4 or later for best results

**Opening the assistant:**
- Press `Cmd + I` to open the inline coding assistant
- Or use the chat interface in the left sidebar for longer conversations
- Or right-click in your code and select **Ask Coding Assistant**

### Step 3: Write Effective Prompts for Preview Generation

The quality of generated preview code depends heavily on prompt clarity and specificity. Follow these guidelines for optimal results:

**Essential components of a good preview prompt:**

1. **Specify the view context** - Mention the view name and purpose
2. **Request explicit preview configurations** - List desired device types, color schemes, orientations
3. **Include mock data requirements** - Describe the sample data structure and content
4. **Mention edge cases** - Request previews for empty, loading, error, and boundary states
5. **Specify preview traits** - Ask for traits like `.portrait`, `.landscapeLeft`, `.sizeThatFitsLayout`
6. **Request @Previewable when needed** - For interactive state in previews

**Example: Basic preview generation prompt**

```
Generate comprehensive SwiftUI previews for my ProfileView. Include:
- Light and dark mode variants
- iPhone 16 Pro and iPad Pro configurations
- A preview with extra large dynamic type for accessibility testing
- Sample data with name "Sarah Chen", bio "iOS Developer", and 1,234 followers
```

**Example: Complex preview suite prompt**

```
Create a complete preview suite for TaskListView that displays to-do items. Generate previews for:
1. Empty state with no tasks
2. Normal state with 5 sample tasks (mix of completed and incomplete)
3. Long list with 20 tasks to test scrolling performance
4. Dark mode variant
5. iPad landscape orientation
6. Accessibility size xxxLarge
7. One preview showing a task with a very long title that wraps

Each task should have: id (UUID), title (String), isCompleted (Bool), dueDate (Date).
Use the #Preview macro and include descriptive names for each preview.
```

**Example: Interactive preview with @Previewable**

```
Generate an interactive preview for SettingsView using @Previewable. Include:
- @State variable for notification toggle (default: true)
- @State variable for username text field (default: "User123")
- Make the preview fully interactive so I can test toggling and typing
- Use the #Preview macro with @Previewable for state management
```

### Step 4: Generate Previews for Multiple Device Configurations

One of AI's most valuable capabilities is generating device-specific previews efficiently. Use prompts that specify exact device types and configurations:

**Multi-device prompt template:**

```
Create separate SwiftUI previews for [ViewName] targeting these devices:
1. iPhone SE (3rd generation) in portrait - compact layout
2. iPhone 16 Pro in portrait - standard layout
3. iPhone 16 Pro Max in landscape - expanded layout
4. iPad Pro (12.9-inch) in landscape - split view layout

Each preview should:
- Use descriptive names indicating the device
- Include appropriate preview traits for orientation
- Show how the view adapts to different screen sizes
- Use realistic mock data
```

**Example generated output you might receive:**

```swift
import SwiftUI

struct ArticleDetailView: View {
    let article: Article

    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 16) {
                Text(article.title)
                    .font(.title)
                    .bold()

                Text(article.author)
                    .font(.subheadline)
                    .foregroundColor(.secondary)

                Text(article.content)
                    .font(.body)
            }
            .padding()
        }
    }
}

#Preview("iPhone SE - Compact") {
    ArticleDetailView(article: Article(
        id: UUID(),
        title: "Getting Started with SwiftUI",
        author: "Jane Developer",
        content: "SwiftUI is Apple's modern framework for building user interfaces across all Apple platforms..."
    ))
    .previewDevice(PreviewDevice(rawValue: "iPhone SE (3rd generation)"))
}

#Preview("iPhone 16 Pro - Standard", traits: .portrait) {
    ArticleDetailView(article: Article(
        id: UUID(),
        title: "Advanced SwiftUI Techniques",
        author: "John Architect",
        content: "Learn advanced patterns for building complex SwiftUI applications with clean architecture..."
    ))
    .previewDevice(PreviewDevice(rawValue: "iPhone 16 Pro"))
}

#Preview("iPhone 16 Pro Max - Landscape", traits: .landscapeLeft) {
    ArticleDetailView(article: Article(
        id: UUID(),
        title: "Performance Optimization in SwiftUI",
        author: "Sarah Performance",
        content: "Discover techniques for optimizing SwiftUI performance in production applications..."
    ))
    .previewDevice(PreviewDevice(rawValue: "iPhone 16 Pro Max"))
}

#Preview("iPad Pro - Split View", traits: .landscapeLeft) {
    ArticleDetailView(article: Article(
        id: UUID(),
        title: "Building Universal Apps",
        author: "Mike Universal",
        content: "Create apps that work beautifully on iPhone, iPad, and Mac using SwiftUI..."
    ))
    .previewDevice(PreviewDevice(rawValue: "iPad Pro (12.9-inch) (6th generation)"))
}

struct Article: Identifiable {
    let id: UUID
    let title: String
    let author: String
    let content: String
}
```

### Step 5: Request Dark Mode and Accessibility Previews

Comprehensive preview suites include color scheme variants and accessibility configurations. Prompt the AI explicitly for these scenarios:

**Accessibility-focused prompt:**

```
Generate accessibility-focused previews for my UserProfileView. Include:
1. Standard preview with default settings
2. Dark mode preview using .preferredColorScheme(.dark)
3. Extra large text (xxxLarge) for readability testing
4. Accessibility5 dynamic type for maximum size
5. Right-to-left layout for Arabic/Hebrew localization
6. High contrast mode variant

Use realistic user data: name "Alex Johnson", email "alex@example.com", 542 followers.
Add descriptive preview names explaining what each tests.
```

**Color scheme prompt:**

```
Create side-by-side light and dark mode previews for NotificationCardView.
- One preview named "Light Mode" with light color scheme
- One preview named "Dark Mode" with dark color scheme
- Use identical mock data in both for easy comparison
- Include a notification with: title "New Message", body "You have 3 unread messages", timestamp "2 minutes ago"
```

**Expected AI-generated output:**

```swift
#Preview("Standard Configuration") {
    UserProfileView(user: User(
        name: "Alex Johnson",
        email: "alex@example.com",
        followerCount: 542
    ))
}

#Preview("Dark Mode") {
    UserProfileView(user: User(
        name: "Alex Johnson",
        email: "alex@example.com",
        followerCount: 542
    ))
    .preferredColorScheme(.dark)
}

#Preview("Extra Large Text") {
    UserProfileView(user: User(
        name: "Alex Johnson",
        email: "alex@example.com",
        followerCount: 542
    ))
    .environment(\.dynamicTypeSize, .xxxLarge)
}

#Preview("Maximum Accessibility") {
    UserProfileView(user: User(
        name: "Alex Johnson",
        email: "alex@example.com",
        followerCount: 542
    ))
    .environment(\.dynamicTypeSize, .accessibility5)
}

#Preview("Right-to-Left Layout") {
    UserProfileView(user: User(
        name: "Alex Johnson",
        email: "alex@example.com",
        followerCount: 542
    ))
    .environment(\.layoutDirection, .rightToLeft)
}
```

### Step 6: Generate Mock Data and Sample Models

AI assistants can automatically generate realistic mock data structures to populate your previews, saving significant time:

**Mock data prompt template:**

```
Generate preview code for [ViewName] including complete mock data structures. Create:
1. A data model with properties: [list properties and types]
2. A static SampleData struct containing 3-5 realistic examples
3. Previews using this sample data showing different scenarios
4. Use realistic, diverse names and values (not "Item 1", "Item 2")
```

**Example: E-commerce product preview**

```
Create previews for ProductCardView showing an e-commerce product. Generate:
- Product model with: id (UUID), name (String), price (Double), imageURL (String), rating (Double), reviewCount (Int), inStock (Bool)
- Sample data for: a popular in-stock item, an out-of-stock item, a discounted item
- Preview showing each product variant
- Use realistic product names, prices between $19.99-$299.99, and ratings between 3.5-5.0
```

**Expected AI-generated output:**

```swift
struct ProductCardView: View {
    let product: Product

    var body: some View {
        VStack(alignment: .leading) {
            // Product image placeholder
            RoundedRectangle(cornerRadius: 12)
                .fill(Color.gray.opacity(0.2))
                .frame(height: 200)
                .overlay {
                    Text("Product Image")
                        .foregroundColor(.secondary)
                }

            VStack(alignment: .leading, spacing: 8) {
                Text(product.name)
                    .font(.headline)
                    .lineLimit(2)

                HStack {
                    Image(systemName: "star.fill")
                        .foregroundColor(.yellow)
                    Text(String(format: "%.1f", product.rating))
                    Text("(\(product.reviewCount))")
                        .foregroundColor(.secondary)
                }
                .font(.caption)

                HStack {
                    Text("$\(String(format: "%.2f", product.price))")
                        .font(.title3)
                        .bold()

                    Spacer()

                    if product.inStock {
                        Text("In Stock")
                            .font(.caption)
                            .foregroundColor(.green)
                    } else {
                        Text("Out of Stock")
                            .font(.caption)
                            .foregroundColor(.red)
                    }
                }
            }
            .padding()
        }
        .background(Color(.systemBackground))
        .cornerRadius(12)
        .shadow(radius: 2)
    }
}

struct Product: Identifiable {
    let id: UUID
    let name: String
    let price: Double
    let imageURL: String
    let rating: Double
    let reviewCount: Int
    let inStock: Bool
}

struct SampleProducts {
    static let popularItem = Product(
        id: UUID(),
        name: "Wireless Noise-Canceling Headphones",
        price: 249.99,
        imageURL: "https://example.com/headphones.jpg",
        rating: 4.8,
        reviewCount: 1247,
        inStock: true
    )

    static let outOfStock = Product(
        id: UUID(),
        name: "Limited Edition Mechanical Keyboard",
        price: 179.99,
        imageURL: "https://example.com/keyboard.jpg",
        rating: 4.9,
        reviewCount: 856,
        inStock: false
    )

    static let discountedItem = Product(
        id: UUID(),
        name: "USB-C Hub with 7 Ports",
        price: 39.99,
        imageURL: "https://example.com/hub.jpg",
        rating: 4.2,
        reviewCount: 432,
        inStock: true
    )
}

#Preview("Popular Item") {
    ProductCardView(product: SampleProducts.popularItem)
        .padding()
}

#Preview("Out of Stock") {
    ProductCardView(product: SampleProducts.outOfStock)
        .padding()
}

#Preview("Discounted Item") {
    ProductCardView(product: SampleProducts.discountedItem)
        .padding()
}

#Preview("Dark Mode") {
    ProductCardView(product: SampleProducts.popularItem)
        .padding()
        .preferredColorScheme(.dark)
}
```

### Step 7: Generate Edge Case and State Previews

Comprehensive preview suites test boundary conditions and various view states. Prompt the AI to generate these critical scenarios:

**Edge case prompt:**

```
Generate edge case previews for MessageListView showing chat messages. Create previews for:
1. Empty state - no messages, show helpful placeholder
2. Single message - just one message in the list
3. Very long message - message with 300+ characters that wraps multiple lines
4. Loading state - show loading indicator while messages fetch
5. Error state - display error message "Failed to load messages"
6. Many messages - 50+ messages to test scroll performance

Use the Message model: id (UUID), sender (String), content (String), timestamp (Date).
Name each preview descriptively.
```

**State management prompt with @Previewable:**

```
Create interactive state previews for LoginFormView using @Previewable. Generate:
1. Empty form state - no text entered
2. Partially filled state - email entered, password empty
3. Validation error state - invalid email format shown
4. Loading state - login button disabled, spinner showing
5. Success state - show success message

Use @Previewable @State for interactive testing. Include email and password @State variables.
```

**Expected AI-generated output:**

```swift
#Preview("Empty State") {
    MessageListView(messages: [])
}

#Preview("Single Message") {
    MessageListView(messages: [
        Message(
            id: UUID(),
            sender: "Alice",
            content: "Hey, how are you?",
            timestamp: Date()
        )
    ])
}

#Preview("Very Long Message") {
    MessageListView(messages: [
        Message(
            id: UUID(),
            sender: "Bob",
            content: "This is an extremely long message that tests how the UI handles text wrapping across multiple lines. It contains detailed information that spans several sentences and should wrap gracefully in the message bubble without breaking the layout or causing any visual issues in the interface.",
            timestamp: Date()
        )
    ])
}

#Preview("Loading State") {
    MessageListView(messages: [], isLoading: true)
}

#Preview("Error State") {
    MessageListView(messages: [], error: "Failed to load messages")
}

#Preview("Many Messages") {
    MessageListView(messages: Array(repeating: Message(
        id: UUID(),
        sender: "User \(Int.random(in: 1...10))",
        content: "Sample message \(Int.random(in: 1...100))",
        timestamp: Date()
    ), count: 50))
}
```

### Step 8: Use Follow-Up Prompts to Refine Previews

AI assistants maintain conversation context, allowing you to iteratively improve generated preview code:

**Initial prompt:**
```
Generate basic previews for WeatherCardView showing current weather with temperature, condition, and location.
```

**Follow-up prompt 1:**
```
Add two more previews: one for extreme cold (-20°C) and one for extreme heat (45°C). Include appropriate weather conditions.
```

**Follow-up prompt 2:**
```
Now add a dark mode variant for the extreme heat preview and change the temperature display to show both Celsius and Fahrenheit.
```

**Follow-up prompt 3:**
```
Create a compact size preview using .sizeThatFitsLayout trait to show how it looks in a widget.
```

**Benefits of iterative refinement:**
- Build complexity gradually instead of overwhelming the AI with one massive prompt
- Test each addition before requesting the next
- Learn what works well and adjust your prompting strategy
- Maintain cleaner conversation history

### Step 9: Request Preview Documentation and Comments

Well-documented previews serve as living documentation for your team. Ask the AI to include explanatory comments:

**Documentation-focused prompt:**

```
Generate comprehensive previews for OnboardingScreenView with detailed comments. For each preview:
- Add a comment explaining what the preview tests
- Document the purpose of any mock data
- Explain why specific preview traits were chosen
- Note any edge cases being validated

Include previews for: first screen, middle screen, final screen, and dark mode variant.
```

**Expected AI-generated output:**

```swift
// MARK: - Onboarding Screen Previews

/// Tests the first onboarding screen in standard light mode
/// This preview validates the welcome message and primary call-to-action button
#Preview("First Screen") {
    OnboardingScreenView(
        step: 1,
        title: "Welcome to TaskMaster",
        description: "Organize your tasks and boost productivity",
        imageName: "onboarding-1"
    )
}

/// Tests the middle screen to ensure content flows properly between steps
/// Validates that users can understand the app's core features
#Preview("Middle Screen") {
    OnboardingScreenView(
        step: 2,
        title: "Track Your Progress",
        description: "Visualize completion rates and stay motivated",
        imageName: "onboarding-2"
    )
}

/// Tests the final screen with the "Get Started" button
/// Critical for validating the primary conversion point
#Preview("Final Screen") {
    OnboardingScreenView(
        step: 3,
        title: "Start Your Journey",
        description: "Create your first task and achieve your goals",
        imageName: "onboarding-3",
        isLastStep: true
    )
}

/// Dark mode variant of the first screen
/// Ensures readability and visual hierarchy in dark environments
/// Tests that images and colors have sufficient contrast
#Preview("First Screen - Dark Mode") {
    OnboardingScreenView(
        step: 1,
        title: "Welcome to TaskMaster",
        description: "Organize your tasks and boost productivity",
        imageName: "onboarding-1"
    )
    .preferredColorScheme(.dark)
}
```

### Step 10: Validate and Test Generated Preview Code

After receiving AI-generated preview code, validate it before integrating:

**Validation checklist:**

1. **Compile the code**
   - Copy preview code into your Swift file
   - Press `Cmd + B` to build
   - Fix any compilation errors (rare with Xcode 26.1+ AI)

2. **Check preview rendering**
   - Open the Canvas with `Cmd + Option + Enter`
   - Press `Cmd + Option + P` to refresh previews
   - Verify each preview renders correctly

3. **Test interactive previews**
   - For previews using `@Previewable`, interact with controls
   - Verify state changes work as expected
   - Test text input, button taps, toggles

4. **Verify mock data realism**
   - Ensure generated data is realistic and diverse
   - Check for placeholder values like "Item 1", "Test User"
   - Request improvements if data seems generic

5. **Review preview traits**
   - Verify orientations are correct (portrait/landscape)
   - Check device configurations match your targets
   - Ensure layout traits produce expected results

**If issues are found, use follow-up prompts:**
```
The preview for landscape iPad is showing portrait layout. Fix the preview to use .landscapeLeft trait correctly.
```

```
The mock data uses generic names like "User 1" and "User 2". Replace with diverse, realistic names representing different demographics.
```

## Expected Results

After following these steps, you should have:

1. **Comprehensive preview suites** - Multiple previews testing different configurations, states, and edge cases
2. **Realistic mock data** - Well-structured sample data with diverse, believable values
3. **Device-specific previews** - Configurations for iPhone SE, iPhone Pro, iPad, and other target devices
4. **Accessibility previews** - Previews testing dark mode, large text, and right-to-left layouts
5. **Interactive previews** - Using `@Previewable` for views requiring state management
6. **Well-documented code** - Comments explaining each preview's purpose and testing goals
7. **Time savings** - 10-20 minutes saved per view compared to manual preview creation

**What you should see in Xcode Canvas:**
- Multiple preview cards, each clearly labeled with descriptive names
- Previews rendering correctly for different device sizes and orientations
- Dark mode previews displaying with appropriate contrast
- Interactive elements responding to clicks in live mode
- Mock data displaying realistically in your views

**Example comprehensive preview suite:**

```swift
// Generated by AI with minimal manual adjustments
#Preview("Standard - Light Mode") {
    TaskDetailView(task: SampleTasks.standardTask)
}

#Preview("Standard - Dark Mode") {
    TaskDetailView(task: SampleTasks.standardTask)
        .preferredColorScheme(.dark)
}

#Preview("Overdue Task") {
    TaskDetailView(task: SampleTasks.overdueTask)
}

#Preview("Completed Task") {
    TaskDetailView(task: SampleTasks.completedTask)
}

#Preview("Task with Long Description") {
    TaskDetailView(task: SampleTasks.longDescriptionTask)
}

#Preview("iPad Landscape", traits: .landscapeLeft) {
    TaskDetailView(task: SampleTasks.standardTask)
        .previewDevice(PreviewDevice(rawValue: "iPad Pro (12.9-inch)"))
}

#Preview("Accessibility - Extra Large Text") {
    TaskDetailView(task: SampleTasks.standardTask)
        .environment(\.dynamicTypeSize, .xxxLarge)
}
```

## Troubleshooting

### Common Issue 1: Generated Previews Don't Compile

**Problem:** AI-generated preview code contains syntax errors or uses incorrect APIs.

**Solution:**
- Copy the error message and ask the AI: "Fix this compilation error: [paste error]"
- Request specific iOS version: "Generate previews using iOS 17+ APIs only"
- Specify Xcode version: "Use SwiftUI APIs compatible with Xcode 26.1+"
- Ask for explanation: "Explain why this preview code doesn't compile"
- Break into smaller requests: Generate data models first, then previews

**Example follow-up:**
```
The preview code uses PreviewDevice incorrectly. Fix it to use proper device identifiers for iOS 17+.
```

### Common Issue 2: Mock Data Is Too Generic or Unrealistic

**Problem:** Generated data uses placeholder values like "Item 1", "Test User", or unrealistic data.

**Solution:**
- Request diverse, realistic data explicitly: "Use diverse names representing different demographics and cultures"
- Provide examples: "Use names like 'Sofia Rodriguez', 'Jamal Washington', 'Yuki Tanaka' instead of 'User 1'"
- Specify value ranges: "Generate prices between $9.99 and $399.99, not round numbers"
- Ask for varied content: "Create sample descriptions with different lengths (short, medium, long)"

**Example follow-up:**
```
The mock data names are too generic. Replace with 10 diverse, realistic names representing different cultures and backgrounds. Use varied ages between 22-67.
```

### Common Issue 3: Previews Don't Cover Important Edge Cases

**Problem:** Generated previews miss critical scenarios like empty states, loading states, or error conditions.

**Solution:**
- Explicitly list edge cases in your prompt: "Include previews for: empty, loading, error, and success states"
- Request boundary testing: "Add previews testing: zero items, one item, maximum items (100+)"
- Ask for accessibility scenarios: "Include previews for: large text, right-to-left, voice-over considerations"
- Use follow-up prompts to add missing cases

**Example follow-up:**
```
Add three more previews:
1. Empty state with no data and a helpful message
2. Loading state showing a progress indicator
3. Error state displaying "Network connection failed"
```

### Common Issue 4: @Previewable Syntax Incorrect or Not Working

**Problem:** Generated code places `@Previewable` incorrectly or doesn't make previews interactive.

**Solution:**
- Verify correct syntax: `@Previewable` must come before the property wrapper (e.g., `@Previewable @State`)
- Ensure Xcode version: `@Previewable` requires Xcode 16+, fully supported in 26.1+
- Request correction: "Fix the @Previewable syntax—it should come before @State"
- Ask for interactive example: "Generate an interactive preview where I can type in the text field and see updates"

**Example follow-up:**
```
The @Previewable syntax is wrong. Fix it to: @Previewable @State var text = ""
```

### Common Issue 5: Too Many Previews Generated, Canvas Performance Slow

**Problem:** AI generates 15+ previews, causing slow Canvas performance and long build times.

**Solution:**
- Request fewer, focused previews: "Generate only 5 key previews: standard, dark mode, iPad, accessibility, error state"
- Split into multiple files: "Create previews for iPhone in one file and iPad previews in another"
- Use preview groups: "Group related previews together with descriptive section comments"
- Disable auto-refresh: Go to **Editor > Canvas** and uncheck **Automatically Refresh Canvas**

**Example follow-up:**
```
That's too many previews. Reduce to the 6 most important: standard, dark mode, iPad landscape, empty state, large text, and error state.
```

### Common Issue 6: AI Uses Outdated Preview Syntax

**Problem:** Generated code uses old `PreviewProvider` protocol instead of modern `#Preview` macro.

**Solution:**
- Specify modern syntax: "Use the #Preview macro, not PreviewProvider protocol"
- Request update: "Convert these PreviewProvider previews to use the #Preview macro"
- Mention Xcode version: "Generate previews using Xcode 26.1+ syntax with #Preview"

**Example follow-up:**
```
Don't use PreviewProvider. Rewrite using the modern #Preview macro introduced in Xcode 15.
```

## Additional Tips

- **Start with a simple prompt** - Generate basic previews first, then iterate with follow-ups to add complexity

- **Be specific about preview names** - Request descriptive names like "Empty State - No Messages" instead of generic "Preview 1"

- **Request organized code** - Ask for "// MARK:" comments to group related previews together

- **Specify data diversity** - Request "diverse, realistic data representing different demographics" for inclusive mock data

- **Combine with view generation** - When using AI to generate SwiftUI views (KB-037), include preview generation in the same prompt

- **Use ChatGPT for speed, Claude for complexity** - ChatGPT generates previews faster for simple cases; Claude handles complex multi-state scenarios better with its larger context window

- **Iterate incrementally** - Build preview suites gradually with follow-up prompts rather than one massive request

- **Save effective prompts** - Keep a library of prompts that generate good results for reuse across projects

- **Review and customize** - AI-generated previews are starting points; customize colors, spacing, and data to match your design system

- **Test on actual devices** - Use `Cmd + Option + R` to run previews on connected devices or simulators for real-world testing

- **Create preview templates** - Ask AI to generate a reusable `PreviewModifier` for shared environments (Core Data, environment objects)

- **Document your prompt patterns** - Note which prompt structures work best with your AI assistant for future reference

- **Use preview generation for learning** - Study AI-generated previews to learn preview best practices and SwiftUI patterns

**Example prompt library to save:**

```
// Template 1: Multi-device suite
Generate previews for [ViewName] for iPhone SE, iPhone 16 Pro, iPad Pro landscape, and Apple Watch. Use realistic mock data.

// Template 2: Accessibility suite
Create accessibility previews for [ViewName]: light/dark mode, xxxLarge text, accessibility5 size, and right-to-left layout.

// Template 3: State testing
Generate state previews for [ViewName]: empty, loading, error, success, and partial data states with appropriate UI.

// Template 4: Interactive preview
Create an interactive preview for [ViewName] using @Previewable with @State variables for [list state properties].
```

## Related Articles

- KB-024: How to preview your SwiftUI views using Xcode Previews
- KB-037: How to generate SwiftUI views from natural language descriptions using ChatGPT
- KB-031: How to access the Coding Assistant in Xcode 26 (ChatGPT)
- KB-047: How to use Claude for complex refactoring tasks in large codebases
- KB-048: How to leverage Claude's extended context window for comprehensive code reviews
- KB-050: How to compare ChatGPT and Claude responses to choose the best solution

## Sources

- Apple Developer Documentation - Previews in Xcode - https://developer.apple.com/documentation/swiftui/previews-in-xcode (Accessed: November 17, 2025)
- Apple Developer Documentation - Preview Macro - https://developer.apple.com/documentation/swiftui/preview(_:traits:body:cameras:) (Accessed: November 17, 2025)
- Apple Developer - Writing code with intelligence in Xcode - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- Apple Newsroom - Apple supercharges its tools and technologies for developers - https://www.apple.com/newsroom/2025/06/apple-supercharges-its-tools-and-technologies-for-developers/ (Accessed: November 17, 2025)
- TechCrunch - Apple brings ChatGPT and other AI models to Xcode - https://techcrunch.com/2025/06/09/apple-brings-chatgpt-and-other-ai-models-to-xcode/ (Accessed: November 17, 2025)
- 200OK Solutions - What's New in Xcode 26: Smarter, Faster, More Powerful — WWDC 2025 Highlights - https://200oksolutions.com/blog/whats-new-in-xcode-26-smarter-faster-more-powerful-wwdc-2025-highlights/ (Accessed: November 17, 2025)
- Swift with Vincent - Discover 5 new AI features of Xcode 26 - https://www.swiftwithvincent.com/blog/discover-5-new-ai-features-of-xcode-26 (Accessed: November 17, 2025)
- Swift with Vincent - ChatGPT in Xcode 26: there's a hidden prompt - https://www.swiftwithvincent.com/blog/chatgpt-in-xcode-26-theres-a-hidden-prompt (Accessed: November 17, 2025)
- CISIN - How to Integrate ChatGPT and Claude in Xcode 26 - https://www.cisin.com/coffee-break/how-to-integrate-chatgpt-and-claude-in-xcode-26.html (Accessed: November 17, 2025)
- Apidog - How To Use Claude with Xcode - https://apidog.com/blog/use-claude-in-xcode/ (Accessed: November 17, 2025)
- Peter Friese - Reverse-Engineering Xcode's Coding Intelligence prompt - https://peterfriese.dev/blog/2025/reveng-xcode-coding-intelligence/ (Accessed: November 17, 2025)
- Medium - Code with Intelligence — xCode 26 - https://medium.com/@sachinsiwal/code-with-intelligence-xcode-26-9203a8ce665e (Accessed: November 17, 2025)
- Natasha The Robot - A Swift Developer's Guide to Prompt Engineering with Apple's FoundationModels - https://www.natashatherobot.com/p/swift-prompt-engineering-apples-foundationmodels (Accessed: November 17, 2025)
- SwiftLee - ChatGPT for Swift: Top 5 code generation prompts - https://www.avanderlee.com/swift/chatgpt-code-generation-prompts/ (Accessed: November 17, 2025)
- Medium - Unlock SwiftUI Superpowers: Smarter Prompts, Faster Builds with Cursor AI - https://medium.com/@ashitranpura27/unlock-swiftui-superpowers-smarter-prompts-faster-builds-with-cursor-ai-58230728024d (Accessed: November 17, 2025)
- Medium - Prompting for iOS Development — Less Sloppy Code from AI - https://medium.com/@mireabot/prompting-for-ios-development-less-sloppy-code-from-ai-902ae44c228d (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
