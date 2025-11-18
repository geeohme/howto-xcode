# How to use AI to convert UIKit code to SwiftUI

**Article ID:** KB-054
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 30-60 minutes

## Overview

Xcode 26.1+ integrates AI coding assistants (ChatGPT and Claude) that can intelligently convert UIKit code to SwiftUI, dramatically accelerating migration efforts. These AI assistants understand both frameworks and can translate view controllers, views, constraints, and UIKit patterns into their SwiftUI equivalents while maintaining functionality. This guide teaches you how to effectively prompt AI assistants to migrate UIKit components, review conversions for correctness, and incrementally modernize your codebase without rewriting everything at once.

## Prerequisites

- Xcode 26.1 or later installed
- Apple Intelligence enabled in macOS System Settings (see KB-003)
- Either ChatGPT or Claude configured in Xcode Intelligence settings (see KB-031, KB-041, KB-044)
- Active subscription to ChatGPT (paid tier recommended) or Claude (Pro/Max recommended)
- Basic understanding of both UIKit and SwiftUI frameworks
- Existing UIKit code to convert (view controllers, views, or custom components)
- Tests in place (highly recommended) to verify behavior before and after migration
- Working knowledge of UIHostingController and representable protocols for hybrid apps

## Steps

### Step 1: Assess Your UIKit Code for Conversion

Before engaging the AI assistant, analyze which UIKit components to migrate:

1. **Identify conversion candidates:**
   - Start with simpler, self-contained views (custom UIView subclasses)
   - Progress to view controllers with minimal dependencies
   - Save complex navigation hierarchies and coordinators for later
   - Prioritize new features or screens with less integration overhead

2. **Check for SwiftUI compatibility:**
   - Views using standard UIKit controls (UILabel, UIButton, UITextField) convert easily
   - Custom drawing code (Core Graphics) requires UIViewRepresentable wrappers
   - Third-party UIKit libraries may need representable bridges
   - MapKit, AVKit, and other Apple frameworks have SwiftUI equivalents

3. **Document current behavior:**
   - Take screenshots of the current UI state, including different screen sizes
   - Note interactive behaviors (animations, gestures, transitions)
   - List data flow patterns (delegates, closures, NotificationCenter)
   - Identify state management approaches (properties, UserDefaults, Core Data)

**Conversion Strategy Recommendation:**

- **Incremental migration:** Convert one screen at a time, using UIHostingController to embed SwiftUI in existing UIKit navigation
- **Hybrid approach:** Keep UIKit shell with SwiftUI content views
- **Feature-based:** Build new features in SwiftUI while maintaining legacy UIKit code

### Step 2: Select and Prepare UIKit Code

Properly scope the code you want to convert:

1. **Open your UIKit file** in Xcode 26.1+
2. **Select the code to convert:**
   - For a UIView subclass: Select the entire class definition (including private methods and properties)
   - For a UIViewController: Select the class and associated view code
   - For Auto Layout constraints: Include constraint creation and activation code
   - For smaller conversions: Select individual methods or view setup code

3. **Gather context files:**
   - Keep model files open in adjacent tabs
   - Reference any delegates, data sources, or protocols the code depends on
   - Note any custom colors, fonts, or design system constants

**Best Practice:** Start with 100-200 lines of code. Converting massive view controllers (1000+ lines) in one prompt produces lower-quality results. Break large conversions into logical components.

### Step 3: Open Your AI Coding Assistant

Choose either ChatGPT or Claude based on your needs:

1. **Access the Coding Assistant:**
   - Press **⌃⌘ I** (Control-Command-I) to open the Coding Assistant panel
   - Or select **Editor → Coding Assistant** from the menu bar
   - The assistant panel appears on the right side of Xcode

2. **Choose your AI model:**
   - **Claude Sonnet 4:** Best for complex multi-file migrations, architectural refactoring, and detailed explanations (200K token context window)
   - **ChatGPT GPT-5:** Good for quick, straightforward conversions of individual views or simple view controllers
   - Switch models using the dropdown in the assistant panel (see KB-046)

3. **The AI gathers context automatically:**
   - Your selected code is sent to the AI
   - Related files and project structure are analyzed
   - Previous conversation history provides continuity

### Step 4: Write Effective UIKit-to-SwiftUI Conversion Prompts

The quality of your conversion depends heavily on prompt specificity. Use these proven patterns:

#### Pattern 1: Simple View Conversion

For custom UIView subclasses:

```
Convert this UIView subclass to a SwiftUI View. Translate all UI elements
to their SwiftUI equivalents (UILabel → Text, UIButton → Button, etc.).
Preserve the layout structure and styling (colors, fonts, spacing). Replace
Auto Layout constraints with SwiftUI layout containers (VStack, HStack,
ZStack, Spacer, padding).
```

#### Pattern 2: View Controller Conversion

For UIViewController with logic and state:

```
Convert this UIViewController to SwiftUI. Create a SwiftUI View with an
@Observable view model class for state management. Translate the viewDidLoad
setup into SwiftUI view modifiers. Convert delegate methods and @IBAction
functions to SwiftUI button actions and onChange modifiers. Preserve all
business logic and validation rules.
```

#### Pattern 3: Auto Layout to SwiftUI Layout

For constraint-based layouts:

```
Convert these Auto Layout constraints to equivalent SwiftUI layout code.
Use VStack/HStack for vertical/horizontal arrangements, Spacer for flexible
space, padding for margins, and frame modifiers for fixed dimensions. Match
the constraint priorities using layout priorities if needed.
```

#### Pattern 4: Incremental Hybrid Approach

For embedding SwiftUI in existing UIKit:

```
Convert this UIViewController's view hierarchy to SwiftUI, but wrap it in
a way that allows embedding in the existing UIKit navigation stack. Create
a SwiftUI View for the content, then show how to use UIHostingController
to present it from the parent UIViewController. Maintain existing navigation
bar buttons and appearance.
```

#### Pattern 5: Data-Driven Components

For views with data sources:

```
Convert this UITableViewController to a SwiftUI List view. Transform the
UITableViewDataSource methods into a ForEach loop over the model array.
Replace didSelectRowAt with NavigationLink or onTapGesture. Convert any
custom UITableViewCell classes into separate SwiftUI view components.
Include swipe actions if present.
```

**Key Prompt Principles:**

- **Be explicit about equivalencies:** State which UIKit controls map to which SwiftUI views
- **Mention state management:** Request @State, @Binding, or @Observable for managing mutable data
- **Specify layout approach:** Ask for specific SwiftUI containers (VStack, HStack, GeometryReader)
- **Request compatibility code:** Ask for UIViewRepresentable wrappers if needed for unavailable components
- **Include styling:** Request matching fonts, colors, corner radius, shadows, and other visual attributes

### Step 5: Review the AI-Generated SwiftUI Code

After the AI generates converted code, perform thorough review:

1. **Read the AI's explanation:**
   - The AI typically explains what changed and why
   - Note any caveats or manual steps required
   - Check for mentions of unsupported features requiring workarounds

2. **Verify structural equivalence:**
   - Compare the visual hierarchy (UIKit nested views → SwiftUI nested stacks)
   - Check that all UI elements were translated (no missing buttons, labels, images)
   - Ensure layout matches the original (spacing, alignment, sizing)

3. **Examine state management:**
   - **@State:** Used for local view state (replaces UIViewController properties)
   - **@Binding:** Used for passing mutable state between views (replaces delegates with single values)
   - **@Observable:** Used for view models with complex state (replaces MVVM view models)
   - **@Environment:** Used for app-wide dependencies (replaces singletons or injected dependencies)

4. **Check data flow patterns:**
   - **UIKit delegates → SwiftUI closures or @Binding:** Ensure callbacks are properly converted
   - **NotificationCenter → Environment or @Observable:** Verify global state access is modernized
   - **KVO → @Published or Observation:** Check reactive patterns are updated

5. **Validate UI customization:**
   - Colors: Verify UIColor → Color conversions match (consider semantic colors)
   - Fonts: Check UIFont → Font translations (system fonts, custom fonts, dynamic type)
   - Images: Ensure UIImage → Image conversions work (SF Symbols, asset catalogs, remote images)

6. **Review lifecycle and behavior:**
   - **viewDidLoad → onAppear:** Check initialization logic is called at the right time
   - **viewWillAppear → onAppear:** Verify refresh logic works correctly
   - **viewWillDisappear → onDisappear:** Ensure cleanup code executes
   - **Animations:** Confirm UIView.animate → withAnimation conversions are smooth

### Step 6: Test the Converted SwiftUI Code

Never trust AI-generated code without testing. Follow this verification process:

1. **Copy the code into Xcode:**
   - Create a new Swift file or replace the old UIKit file
   - Include all supporting code (view models, models, extensions)
   - Ensure imports are correct (SwiftUI, Combine if needed)

2. **Build the project:**
   - Press **⌘B** to compile
   - Fix any immediate compilation errors
   - If errors occur, copy them back to the AI for fixes

3. **Preview in Canvas:**
   - Use Xcode's SwiftUI Canvas to preview the view
   - Check layout in different device sizes (iPhone SE, iPhone 15 Pro Max, iPad)
   - Test light and dark mode appearance
   - Verify Dynamic Type scaling (small and accessibility sizes)

4. **Run in simulator:**
   - Launch the app and navigate to the converted screen
   - Test all interactive elements (buttons, text fields, gestures)
   - Verify animations and transitions
   - Check navigation flow (push, present, dismiss)

5. **Compare with original:**
   - Place screenshots side-by-side (UIKit vs SwiftUI versions)
   - Look for visual differences (spacing, alignment, colors, fonts)
   - Test edge cases (empty states, error states, loading states)
   - Verify accessibility (VoiceOver, button labels, traits)

### Step 7: Handle UIKit Components Without SwiftUI Equivalents

Some UIKit components don't have direct SwiftUI equivalents. The AI can help wrap them:

#### Wrapping UIKit Views with UIViewRepresentable

If the AI identifies components that need wrapping, request:

```
Create a UIViewRepresentable wrapper for this UIKit component. Implement
makeUIView to create the UIKit view, updateUIView to handle state changes,
and makeCoordinator if delegate methods need bridging. Add SwiftUI-style
@Binding parameters for any mutable state.
```

**Common components requiring wrappers:**
- Custom UIKit views with complex Core Graphics drawing
- Third-party UIKit libraries (charts, image pickers, video players)
- MKMapView (though MapKit has SwiftUI Map, advanced features may need wrapping)
- WKWebView (though better to use Safari View Controller or async WebKit APIs)
- PKCanvasView for PencilKit drawing

#### Wrapping UIViewController with UIViewControllerRepresentable

For view controllers that must remain in UIKit:

```
Create a UIViewControllerRepresentable wrapper for this UIViewController.
Implement makeUIViewController, updateUIViewController, and makeCoordinator
to bridge delegate callbacks to SwiftUI @Binding parameters. Show how to
present this from SwiftUI using .sheet or NavigationLink.
```

### Step 8: Integrate Converted SwiftUI Views

Once conversion is verified, integrate the SwiftUI view into your app:

#### Option A: Embed in Existing UIKit App (Hybrid)

Use UIHostingController to present SwiftUI from UIKit:

```swift
// In your UIKit view controller
import SwiftUI

let swiftUIView = ConvertedSwiftUIView(data: myData)
let hostingController = UIHostingController(rootView: swiftUIView)

// Present modally
present(hostingController, animated: true)

// Or push onto navigation stack
navigationController?.pushViewController(hostingController, animated: true)

// Or embed as child view controller
addChild(hostingController)
view.addSubview(hostingController.view)
hostingController.didMove(toParent: self)
```

Ask the AI to generate this integration code:

```
Show me how to use UIHostingController to present this SwiftUI view from
my existing UIKit navigation controller. Include proper lifecycle methods
for adding as a child view controller.
```

#### Option B: Full SwiftUI Navigation

If migrating to pure SwiftUI:

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            NavigationStack {
                MainView()
            }
        }
    }
}
```

Gradually replace UIKit screens with SwiftUI as you convert them.

### Step 9: Refine and Optimize with Follow-Up Prompts

Migration is iterative. Use conversational prompts to improve:

1. **Fix layout issues:**
   ```
   The spacing between elements is too tight. Add more padding and increase
   the vertical spacing in the VStack from 8 to 16 points.
   ```

2. **Improve state management:**
   ```
   Extract the business logic into a separate view model class using the
   @Observable macro. Move all @State variables that handle data and logic
   into the view model, keeping only UI-specific state in the view.
   ```

3. **Add missing features:**
   ```
   Add pull-to-refresh functionality using the refreshable modifier. When
   the user pulls down, call the fetchData() method and show a loading
   indicator.
   ```

4. **Enhance animations:**
   ```
   Add smooth spring animations when items appear and disappear from the
   list. Use .transition(.scale) with .animation(.spring()) to match the
   feel of the original UIKit implementation.
   ```

5. **Optimize performance:**
   ```
   This list is slow with 1000+ items. Add lazy loading using LazyVStack,
   implement onAppear pagination triggers, and cache image downloads.
   ```

### Step 10: Verify Complete Functionality

After completing the conversion:

1. **Run full test suite:**
   - Execute unit tests: **⌘U**
   - Update tests that referenced UIKit view controllers
   - Write new SwiftUI-specific tests using ViewInspector or snapshot testing
   - Ensure all business logic tests still pass

2. **Perform manual QA:**
   - Test all user flows through the converted screen
   - Verify data persistence (UserDefaults, Core Data, file system)
   - Check network requests and response handling
   - Test error states and edge cases
   - Validate form validation and input handling

3. **Accessibility audit:**
   - Run Accessibility Inspector
   - Test with VoiceOver enabled
   - Verify button labels, hints, and traits
   - Check Dynamic Type support (Text should scale, not be fixed size)
   - Ensure sufficient color contrast

4. **Performance testing:**
   - Profile with Instruments (Time Profiler, SwiftUI view body profiler)
   - Check for unnecessary view redraws
   - Verify list scrolling is smooth
   - Measure launch time impact
   - Test on older devices (iPhone SE, older iPads)

5. **Visual regression testing:**
   - Compare screenshots across iOS versions (iOS 17, 18, 19)
   - Test on different device sizes and orientations
   - Verify appearance in light and dark mode
   - Check localization in RTL languages (Arabic, Hebrew)

## Expected Results

After successfully using AI to convert UIKit to SwiftUI, you should achieve:

1. ✅ **Functionally Equivalent SwiftUI Views:**
   - All UI elements present and correctly styled
   - Interactive elements work identically to UIKit version
   - Layout adapts to different screen sizes and orientations
   - Animations and transitions match the original feel

2. ✅ **Modern SwiftUI Patterns:**
   - Declarative view hierarchy (no imperative setup code)
   - State management using @State, @Binding, @Observable
   - Reactive data flow (changes automatically update UI)
   - Composition over inheritance (reusable view components)

3. ✅ **Clean, Maintainable Code:**
   - Reduced code volume (SwiftUI typically uses 30-50% less code than UIKit)
   - Improved readability (what you see is what you get)
   - Better testability (pure views with injected dependencies)
   - Type-safe styling and modifiers

4. ✅ **Successful Integration:**
   - SwiftUI view works in existing app architecture
   - Navigation flows correctly between UIKit and SwiftUI screens
   - Data passes correctly between old and new code
   - No crashes or undefined behavior

5. ✅ **Passing Tests:**
   - All unit tests green (business logic unchanged)
   - UI tests updated for SwiftUI view hierarchy
   - Manual QA passes with no regressions
   - Accessibility tests confirm VoiceOver compatibility

## Troubleshooting

### Issue 1: AI Converts Code That Doesn't Compile

**Symptoms:** Generated SwiftUI code has syntax errors, missing imports, or uses deprecated APIs

**Solutions:**
- Copy the exact compiler error and paste it into the AI chat: "Fix this error: [paste error]"
- Specify the iOS version target: "Convert to SwiftUI for iOS 17+ using the latest APIs"
- Ask for step-by-step fixes: "The build fails with 3 errors. Fix each one individually and explain"
- Check that all custom types (models, enums) were included in the conversion
- Verify imports are correct (SwiftUI, Combine, your app's module name)
- Request the AI explain the code: "Explain this conversion and why you chose these APIs"

### Issue 2: Layout Doesn't Match Original UIKit Version

**Symptoms:** SwiftUI version has different spacing, alignment, or sizing compared to UIKit original

**Solutions:**
- Provide specific feedback: "The spacing between the title and subtitle should be 12 points, not 8"
- Share a screenshot: Describe the visual difference in detail
- Request constraint equivalents: "These Auto Layout constraints need exact SwiftUI equivalents: [paste constraints]"
- Ask for GeometryReader: "Use GeometryReader to match the exact proportional sizing from the UIKit version"
- Specify layout priorities: "The description text should expand to fill available space while the image stays fixed at 60x60"
- Use overlay comparisons: "Put the old and new versions side-by-side and identify the visual differences"

### Issue 3: State Management Causes Incorrect Behavior

**Symptoms:** UI doesn't update when data changes, or updates cause unexpected redraws/performance issues

**Solutions:**
- Verify state ownership: "Move the @State variable to the parent view since it needs to persist across navigation"
- Check binding direction: "This should be @Binding, not @State, since the parent view owns this data"
- Use @Observable correctly: "Convert this class to use @Observable instead of ObservableObject and @Published"
- Debug view updates: "Add print statements in the view body to track when it redraws"
- Fix shared state: "Use @Environment to share this state across multiple views without prop drilling"
- Ask the AI: "Explain the data flow in this converted view and identify where state should live"

### Issue 4: Animations Don't Match UIKit Feel

**Symptoms:** SwiftUI animations are too fast, too slow, or have the wrong easing curve

**Solutions:**
- Request specific animation curves: "Use .spring(response: 0.5, dampingFraction: 0.7) to match the bouncy UIKit animation"
- Match timing: "The original UIKit animation uses 0.3 seconds with ease-in-out. Use Animation.easeInOut(duration: 0.3)"
- Add explicit animations: "Wrap the state change in withAnimation { } to animate the layout change"
- Remove unwanted animations: "Use .animation(nil) on this view to prevent automatic animation"
- Debug animation: "Show before and after states clearly so I can see what's animating"
- Profile performance: "This animation is janky. Optimize by using .drawingGroup() or lazy containers"

### Issue 5: Navigation Integration Breaks

**Symptoms:** UIHostingController doesn't appear correctly, navigation bar is wrong, or back button is missing

**Solutions:**
- Fix navigation bar: "Set navigationBarHidden(false) and add .navigationTitle() modifier to show the navigation bar"
- Ensure hosting controller setup: "Show the complete UIHostingController setup including navigation bar button items"
- Match UIKit navigation: "The SwiftUI view should use the same navigation bar appearance as the rest of the UIKit app"
- Handle back button: "Add a custom back button using .navigationBarBackButtonHidden() and a custom action"
- Test navigation: "Verify that pushing and popping this hosting controller works in the existing navigation stack"
- Use hybrid navigation: "Create a SwiftUI NavigationStack inside the hosting controller instead of relying on UINavigationController"

### Issue 6: SwiftUI Preview Crashes or Shows Blank Screen

**Symptoms:** Canvas preview shows error, blank screen, or crashes when trying to render

**Solutions:**
- Add preview data: "Create a #Preview with sample data so the view renders without dependencies"
- Fix dependencies: "The preview crashes because of this singleton. Pass a mock instance for preview mode"
- Check @Observable: "Use @State in the preview to create an observable object instance"
- Simplify preview: "Show just the view in isolation without navigation or environment setup"
- Use PreviewProvider: "Convert to PreviewProvider with multiple previews showing different states"
- Debug the error: "The preview error is: [paste error]. What's causing this and how do I fix it?"

## Additional Tips

### Choose the Right AI Model for UIKit Conversion

**Prefer Claude Sonnet 4 for:**
- **Large view controller conversions** with complex state and logic (200K context window handles extensive code)
- **Multi-file migrations** where the AI needs to understand relationships across many files
- **Detailed explanations** of why specific SwiftUI patterns were chosen
- **Architectural refactoring** combined with UIKit-to-SwiftUI conversion
- **Legacy Objective-C to Swift + SwiftUI** double migrations

**Prefer ChatGPT GPT-5 for:**
- **Quick single-view conversions** (simple UIView subclass to SwiftUI view)
- **Exploratory questions** about conversion approaches before implementing
- **General SwiftUI patterns** not specific to your codebase

See KB-046 for switching between models and KB-050 for comparing their responses.

### Incremental Migration Strategy

**Never try to convert an entire app at once.** Use this phased approach:

**Phase 1: New Features (Weeks 1-2)**
- Build all new features in SwiftUI going forward
- Use UIHostingController to integrate into existing UIKit navigation
- Gain team experience with SwiftUI patterns

**Phase 2: Leaf Screens (Weeks 3-6)**
- Convert detail views, settings screens, and other "leaf" screens
- These have minimal navigation dependencies
- Focus on views without complex UIKit-specific code

**Phase 3: List and Collection Views (Weeks 7-10)**
- Convert UITableViewController to SwiftUI List
- Convert UICollectionViewController to SwiftUI LazyVGrid
- Migrate custom cells to SwiftUI view components

**Phase 4: Core Screens (Weeks 11-16)**
- Convert main screens and tab bar controllers
- Migrate complex view controllers with business logic
- Update navigation flow to SwiftUI NavigationStack

**Phase 5: Custom Controls (Weeks 17+)**
- Convert custom UIView subclasses with drawing code
- Wrap unavoidable UIKit components in UIViewRepresentable
- Polish animations and transitions

This surgical, incremental approach minimizes risk and allows for rollback if issues arise.

### Create a UIKit-to-SwiftUI Mapping Guide

Save this in `.claude/UIKIT_SWIFTUI_MAPPING.md` for consistent conversions:

```markdown
# UIKit to SwiftUI Conversion Guide

## View Components
- UILabel → Text
- UIButton → Button
- UIImageView → Image (or AsyncImage for remote images)
- UITextField → TextField
- UITextView → TextEditor
- UISwitch → Toggle
- UISlider → Slider
- UIProgressView → ProgressView
- UIActivityIndicatorView → ProgressView(style: .circular)
- UIStackView → VStack, HStack, ZStack
- UIScrollView → ScrollView
- UITableView → List
- UICollectionView → LazyVGrid or LazyHGrid

## Layout
- Auto Layout constraints → VStack/HStack + padding/frame modifiers
- NSLayoutConstraint.activate → Implicit in SwiftUI hierarchy
- UIEdgeInsets → EdgeInsets or .padding()
- CGFloat spacing → VStack(spacing: ) or HStack(spacing: )

## State Management
- UIViewController properties → @State in View
- Delegates → @Binding or closures
- NotificationCenter → @Environment or custom EnvironmentKey
- Singletons → @Environment or @EnvironmentObject
- Target-action → Button action closures

## Lifecycle
- viewDidLoad → .onAppear or init
- viewWillAppear → .onAppear(perform: )
- viewWillDisappear → .onDisappear(perform: )
- viewDidLayoutSubviews → GeometryReader if needed

## Styling
- UIColor → Color (use Color(.systemBlue) for UIKit colors)
- UIFont → Font (use Font(UIFont) for exact UIFont)
- layer.cornerRadius → .cornerRadius()
- layer.shadowRadius → .shadow()
```

The AI will reference this guide for consistent conversions.

### Test Both Light and Dark Mode

SwiftUI makes it easy to preview multiple appearances:

```swift
#Preview("Light Mode") {
    ConvertedView()
        .preferredColorScheme(.light)
}

#Preview("Dark Mode") {
    ConvertedView()
        .preferredColorScheme(.dark)
}
```

Ask the AI: "Generate previews for light mode, dark mode, and different device sizes."

### Use Version Control Extensively

**Commit frequently during conversion:**

```bash
git commit -m "Convert LoginView from UIKit to SwiftUI"
git commit -m "Add UIHostingController integration for LoginView"
git commit -m "Fix layout spacing in converted LoginView"
git commit -m "Add SwiftUI preview with sample data"
```

This creates rollback points if the conversion introduces regressions.

### Leverage AI for Representable Wrappers

For UIKit components that must stay in UIKit:

```
I need to use this UIKit component [describe component] in SwiftUI. Create
a UIViewRepresentable wrapper with proper update handling. If the component
has delegate methods, bridge them to SwiftUI @Binding parameters using
makeCoordinator.
```

The AI will generate the full wrapper boilerplate, saving significant time.

### Combine Conversion with Modernization

While converting, improve the code:

```
Convert this UIViewController to SwiftUI. While converting, also:
1. Replace completion handlers with async/await
2. Extract business logic into a view model using @Observable
3. Add proper error handling for network failures
4. Create comprehensive previews for different states
```

This "conversion + modernization" approach maximizes the value of the migration effort.

### Profile Before and After

Measure objectively whether SwiftUI improves performance:

1. **Baseline UIKit version:**
   - Measure launch time, scroll performance, memory usage
   - Use Instruments to profile

2. **Compare SwiftUI version:**
   - Re-run same measurements
   - SwiftUI should have similar or better performance for most cases
   - If worse, ask AI to optimize

```
The SwiftUI version scrolls slower than UIKit. Profile and optimize the
List view. Consider using LazyVStack, .id() for view identity, and
.equatable() to prevent unnecessary redraws.
```

### Handle Image Assets Correctly

UIKit and SwiftUI load images differently:

**UIKit:**
```swift
let image = UIImage(named: "logo")
imageView.image = image
```

**SwiftUI:**
```swift
Image("logo") // Asset catalog
Image(systemName: "star.fill") // SF Symbols
AsyncImage(url: URL(string: "https://...")) // Remote images
```

Ask the AI to handle this: "Convert all UIImage instances to SwiftUI Image, using AsyncImage for remote URLs."

### Preserve Accessibility

Ensure the conversion maintains accessibility:

```
Convert this view to SwiftUI and preserve all accessibility features:
- VoiceOver labels for all interactive elements
- Accessibility hints for complex gestures
- Accessibility traits (button, header, etc.)
- Dynamic Type support for all text
- Semantic content attributes for RTL languages
```

The AI will add proper `.accessibilityLabel()`, `.accessibilityHint()`, and `.accessibilityAddTraits()` modifiers.

## Related Articles

- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT integration
- KB-037: How to generate SwiftUI views from natural language descriptions using ChatGPT
- KB-038: How to ask ChatGPT to explain complex code sections in plain English
- KB-039: How to use ChatGPT to fix errors and debug code
- KB-043: How to add Claude as a custom model provider in Xcode Intelligence settings
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-047: How to use Claude for complex refactoring tasks
- KB-009: How to choose between SwiftUI and UIKit for your project

## Sources

1. **Apple Developer - Xcode 26 Release Notes**
   https://developer.apple.com/documentation/xcode-release-notes/xcode-26-release-notes
   Accessed: November 17, 2025

2. **Medium - From Red Builds to Release: How I Ship iOS Features 2× Faster with Claude in Xcode 26**
   https://alirezarezvani.medium.com/from-red-builds-to-release-how-i-ship-ios-features-2-faster-with-claude-in-xcode-26-204265c7919c
   Accessed: November 17, 2025

3. **Medium - UIKit to SwiftUI: The Migration Guide Every iOS Dev Needs**
   https://thgdvr.medium.com/uikit-to-swiftui-the-migration-guide-every-ios-dev-needs-2545c54382e6
   Accessed: November 17, 2025

4. **Medium - Moving from UIKit to SwiftUI — A Complete Guide for iOS Developers**
   https://medium.com/@mauryaankit1308/moving-from-uikit-to-swiftui-a-complete-guide-for-ios-developers-05f00d84407a
   Accessed: November 17, 2025

5. **Hacking with Swift - Migrating from UIKit to SwiftUI**
   https://www.hackingwithswift.com/quick-start/swiftui/migrating-from-uikit-to-swiftui
   Accessed: November 17, 2025

6. **SitePoint - AI-Assisted Coding for iOS Development: CursorAI and Upcoming Swift Assist**
   https://www.sitepoint.com/ai-assisted-coding-for-ios-development/
   Accessed: November 17, 2025

7. **Medium - Prompting for iOS Development — Less Sloppy Code from AI**
   https://medium.com/@mireabot/prompting-for-ios-development-less-sloppy-code-from-ai-902ae44c228d
   Accessed: November 17, 2025

8. **Finotes Blog - Best Practices for Migrating from UIKit to SwiftUI**
   https://www.blog.finotes.com/post/best-practices-for-migrating-from-uikit-to-swiftui
   Accessed: November 17, 2025

9. **DEV Community - Xcode 26 Features & Updates from WWDC 2025: AI Tools, Swift 6, and Faster Builds**
   https://medium.com/@himalimarasinghe/xcode-26-everything-ios-developers-need-to-know-from-wwdc-2025-f92e3edfb07b
   Accessed: November 17, 2025

10. **Index.dev - iOS 26 Explained: Apple's Biggest Update for Developers**
    https://www.index.dev/blog/ios-26-developer-guide
    Accessed: November 17, 2025

11. **Stack Overflow - Migrating from UIKit to SwiftUI**
    https://stackoverflow.com/questions/75729373/migrating-from-uikit-to-swiftui
    Accessed: November 17, 2025

12. **Swift with Vincent - Discover 5 new AI features of Xcode 26**
    https://www.swiftwithvincent.com/blog/discover-5-new-ai-features-of-xcode-26
    Accessed: November 17, 2025

---
*This article is part of the Xcode v26.1+ knowledge base*
