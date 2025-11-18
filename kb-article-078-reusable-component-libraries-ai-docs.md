# How to build reusable component libraries with AI-generated documentation

**Article ID:** KB-078
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 35-40 minutes

## Overview

This guide demonstrates how to create production-ready, reusable SwiftUI component libraries using Swift Package Manager (SPM) while leveraging Xcode 26's AI Coding Intelligence to generate comprehensive documentation. You'll learn to structure components for maximum reusability, use AI assistants to create DocC documentation, and publish your package for team or public use.

## Prerequisites

- Xcode 26.1+ installed with AI Coding Intelligence enabled
- macOS with Apple Intelligence enabled (see KB-003)
- Basic understanding of SwiftUI and Swift Package Manager
- Familiarity with Git and version control
- AI assistant configured in Xcode (ChatGPT, Claude, or similar - see KB-031 to KB-046)
- Understanding of Swift structs and protocols (see KB-019)

## Steps

### Step 1: Create a New Swift Package for Your Component Library

Open Xcode and create a new Swift Package project:

1. Select **File > New > Package** from the menu bar
2. Choose **Library** as the package type
3. Name your package using a descriptive identifier (e.g., "DesignSystemKit" or "UIComponents")
4. Choose a location and ensure "Create Git repository on my Mac" is checked
5. Click **Create**

Xcode will generate a package structure with the following key files:
- `Package.swift` - Package manifest defining targets, products, and dependencies
- `Sources/[PackageName]/` - Directory for your source code
- `Tests/[PackageName]Tests/` - Directory for unit tests

**Expected structure:**
```
DesignSystemKit/
├── Package.swift
├── Sources/
│   └── DesignSystemKit/
│       └── DesignSystemKit.swift
└── Tests/
    └── DesignSystemKitTests/
        └── DesignSystemKitTests.swift
```

### Step 2: Configure Package.swift with DocC Plugin

Modify your `Package.swift` file to include the Swift-DocC plugin for documentation generation:

```swift
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "DesignSystemKit",
    platforms: [
        .iOS(.v17),
        .macOS(.v14)
    ],
    products: [
        .library(
            name: "DesignSystemKit",
            targets: ["DesignSystemKit"]
        ),
    ],
    dependencies: [
        // Add DocC plugin dependency
        .package(
            url: "https://github.com/swiftlang/swift-docc-plugin",
            from: "1.1.0"
        )
    ],
    targets: [
        .target(
            name: "DesignSystemKit",
            dependencies: []
        ),
        .testTarget(
            name: "DesignSystemKitTests",
            dependencies: ["DesignSystemKit"]
        ),
    ]
)
```

**Important:** The `platforms` array specifies minimum OS versions. Adjust based on your deployment targets.

### Step 3: Design Your Component Architecture

Create a well-organized directory structure for scalability:

1. In the `Sources/DesignSystemKit/` directory, create subdirectories:
   - `Components/` - Reusable UI components
   - `Styles/` - Colors, fonts, spacing tokens
   - `Modifiers/` - Custom view modifiers
   - `Utilities/` - Helper functions and extensions

2. Create a design system foundation file:

```swift
// Sources/DesignSystemKit/Styles/DesignTokens.swift

import SwiftUI

/// Design tokens for consistent styling across the component library.
public enum DesignTokens {

    /// Color palette for the design system
    public enum Colors {
        public static let primary = Color(hex: "#007AFF")
        public static let secondary = Color(hex: "#5856D6")
        public static let success = Color(hex: "#34C759")
        public static let warning = Color(hex: "#FF9500")
        public static let error = Color(hex: "#FF3B30")
        public static let background = Color(hex: "#F2F2F7")
        public static let surface = Color.white
        public static let textPrimary = Color.primary
        public static let textSecondary = Color.secondary
    }

    /// Spacing values for consistent layout
    public enum Spacing {
        public static let xs: CGFloat = 4
        public static let sm: CGFloat = 8
        public static let md: CGFloat = 16
        public static let lg: CGFloat = 24
        public static let xl: CGFloat = 32
        public static let xxl: CGFloat = 48
    }

    /// Corner radius values
    public enum CornerRadius {
        public static let sm: CGFloat = 4
        public static let md: CGFloat = 8
        public static let lg: CGFloat = 12
        public static let xl: CGFloat = 16
    }

    /// Typography system
    public enum Typography {
        public static let largeTitle = Font.largeTitle.weight(.bold)
        public static let title = Font.title.weight(.semibold)
        public static let headline = Font.headline.weight(.semibold)
        public static let body = Font.body
        public static let caption = Font.caption
    }
}

// Helper extension for hex colors
extension Color {
    init(hex: String) {
        let hex = hex.trimmingCharacters(in: CharacterSet.alphanumerics.inverted)
        var int: UInt64 = 0
        Scanner(string: hex).scanHexInt64(&int)
        let a, r, g, b: UInt64
        switch hex.count {
        case 6: // RGB (no alpha)
            (a, r, g, b) = (255, int >> 16, int >> 8 & 0xFF, int & 0xFF)
        case 8: // ARGB
            (a, r, g, b) = (int >> 24, int >> 16 & 0xFF, int >> 8 & 0xFF, int & 0xFF)
        default:
            (a, r, g, b) = (255, 0, 0, 0)
        }
        self.init(
            .sRGB,
            red: Double(r) / 255,
            green: Double(g) / 255,
            blue:  Double(b) / 255,
            opacity: Double(a) / 255
        )
    }
}
```

### Step 4: Create Reusable Components with Protocol-Oriented Design

Implement components using protocols for maximum flexibility:

```swift
// Sources/DesignSystemKit/Components/DSButton.swift

import SwiftUI

/// A customizable button component following the design system guidelines.
///
/// `DSButton` provides a consistent button interface with multiple style variants
/// that automatically adapt to the design system's color palette and spacing.
///
/// ## Usage
///
/// ```swift
/// DSButton("Submit", style: .primary) {
///     submitForm()
/// }
/// ```
///
/// ## Topics
///
/// ### Creating a Button
/// - ``init(_:style:action:)``
/// - ``init(_:style:systemImage:action:)``
///
/// ### Button Styles
/// - ``DSButtonStyle``
public struct DSButton: View {

    /// The button's display text
    private let title: String

    /// The visual style of the button
    private let style: DSButtonStyle

    /// Optional SF Symbol icon name
    private let systemImage: String?

    /// The action to perform when the button is tapped
    private let action: () -> Void

    /// Creates a button with text and a style.
    ///
    /// - Parameters:
    ///   - title: The button's label text
    ///   - style: The visual style variant (default: `.primary`)
    ///   - action: The action to perform on tap
    public init(
        _ title: String,
        style: DSButtonStyle = .primary,
        action: @escaping () -> Void
    ) {
        self.title = title
        self.style = style
        self.systemImage = nil
        self.action = action
    }

    /// Creates a button with text, an icon, and a style.
    ///
    /// - Parameters:
    ///   - title: The button's label text
    ///   - style: The visual style variant (default: `.primary`)
    ///   - systemImage: SF Symbol icon name
    ///   - action: The action to perform on tap
    public init(
        _ title: String,
        style: DSButtonStyle = .primary,
        systemImage: String,
        action: @escaping () -> Void
    ) {
        self.title = title
        self.style = style
        self.systemImage = systemImage
        self.action = action
    }

    public var body: some View {
        Button(action: action) {
            HStack(spacing: DesignTokens.Spacing.sm) {
                if let systemImage = systemImage {
                    Image(systemName: systemImage)
                }
                Text(title)
                    .font(DesignTokens.Typography.headline)
            }
            .padding(.horizontal, DesignTokens.Spacing.lg)
            .padding(.vertical, DesignTokens.Spacing.md)
            .frame(maxWidth: .infinity)
            .background(style.backgroundColor)
            .foregroundColor(style.foregroundColor)
            .cornerRadius(DesignTokens.CornerRadius.md)
        }
    }
}

/// Visual style variants for ``DSButton``.
///
/// Each style provides appropriate color combinations for different
/// button purposes and hierarchies.
public enum DSButtonStyle {
    /// Primary action button with prominent styling
    case primary
    /// Secondary action button with subtle styling
    case secondary
    /// Success state button (confirmations, completions)
    case success
    /// Warning state button (potentially destructive actions)
    case warning
    /// Error or destructive action button
    case destructive
    /// Text-only button with minimal styling
    case text

    var backgroundColor: Color {
        switch self {
        case .primary:
            return DesignTokens.Colors.primary
        case .secondary:
            return DesignTokens.Colors.secondary
        case .success:
            return DesignTokens.Colors.success
        case .warning:
            return DesignTokens.Colors.warning
        case .destructive:
            return DesignTokens.Colors.error
        case .text:
            return Color.clear
        }
    }

    var foregroundColor: Color {
        switch self {
        case .text:
            return DesignTokens.Colors.primary
        default:
            return .white
        }
    }
}

// Preview for development and documentation
#Preview("Button Styles") {
    VStack(spacing: DesignTokens.Spacing.md) {
        DSButton("Primary Button", style: .primary) { }
        DSButton("Secondary Button", style: .secondary) { }
        DSButton("Success Button", style: .success) { }
        DSButton("Warning Button", style: .warning) { }
        DSButton("Destructive Button", style: .destructive) { }
        DSButton("Text Button", style: .text) { }
        DSButton("With Icon", style: .primary, systemImage: "star.fill") { }
    }
    .padding()
}
```

### Step 5: Use AI to Generate Documentation Comments

Leverage Xcode 26's AI Coding Intelligence to quickly document your components:

1. **Select the entire component or function** you want to document (e.g., the entire `DSButton` struct)

2. **Invoke the AI Assistant:**
   - Press **Cmd + Shift + A**, or
   - Right-click and select **Ask Assistant**, or
   - Click the AI icon in the Xcode toolbar

3. **Use a documentation-specific prompt:**
   ```
   Generate comprehensive DocC documentation for this SwiftUI component. Include:
   - A summary describing the component's purpose
   - Parameter documentation with descriptions
   - Usage examples in code blocks
   - Topics section organizing the API
   - Related components references where applicable
   Follow Apple's documentation style guide.
   ```

4. **Review and refine the AI output:**
   - Check that all public members are documented
   - Verify code examples compile and are accurate
   - Ensure DocC markdown syntax is correct (triple slashes `///`)
   - Add ``backticks`` around symbol references for linking

5. **Repeat for all public APIs** in your package

**Pro tip:** Create a custom AI prompt template and save it for consistency across all components.

### Step 6: Create Advanced Components with AI Assistance

Build more complex components with AI-generated code and documentation:

```swift
// Sources/DesignSystemKit/Components/DSCard.swift

import SwiftUI

/// A versatile card container component with multiple style variants.
///
/// `DSCard` provides a flexible container for grouping related content with
/// consistent styling, shadows, and optional header/footer sections.
///
/// ## Usage
///
/// ```swift
/// DSCard {
///     VStack(alignment: .leading, spacing: 8) {
///         Text("Card Title")
///             .font(.headline)
///         Text("Card content goes here")
///             .font(.body)
///     }
/// }
/// ```
///
/// ## Topics
///
/// ### Creating a Card
/// - ``init(style:content:)``
/// - ``init(header:content:)``
/// - ``init(header:footer:content:)``
///
/// ### Card Styles
/// - ``DSCardStyle``
public struct DSCard<Content: View>: View {

    private let content: Content
    private let style: DSCardStyle
    private let header: AnyView?
    private let footer: AnyView?

    /// Creates a basic card with custom content.
    ///
    /// - Parameters:
    ///   - style: The visual style of the card (default: `.elevated`)
    ///   - content: A view builder for the card's content
    public init(
        style: DSCardStyle = .elevated,
        @ViewBuilder content: () -> Content
    ) {
        self.style = style
        self.header = nil
        self.footer = nil
        self.content = content()
    }

    /// Creates a card with a header section.
    ///
    /// - Parameters:
    ///   - style: The visual style of the card (default: `.elevated`)
    ///   - header: A view builder for the card's header
    ///   - content: A view builder for the card's main content
    public init<Header: View>(
        style: DSCardStyle = .elevated,
        @ViewBuilder header: () -> Header,
        @ViewBuilder content: () -> Content
    ) {
        self.style = style
        self.header = AnyView(header())
        self.footer = nil
        self.content = content()
    }

    /// Creates a card with header and footer sections.
    ///
    /// - Parameters:
    ///   - style: The visual style of the card (default: `.elevated`)
    ///   - header: A view builder for the card's header
    ///   - footer: A view builder for the card's footer
    ///   - content: A view builder for the card's main content
    public init<Header: View, Footer: View>(
        style: DSCardStyle = .elevated,
        @ViewBuilder header: () -> Header,
        @ViewBuilder footer: () -> Footer,
        @ViewBuilder content: () -> Content
    ) {
        self.style = style
        self.header = AnyView(header())
        self.footer = AnyView(footer())
        self.content = content()
    }

    public var body: some View {
        VStack(spacing: 0) {
            if let header = header {
                header
                    .padding(DesignTokens.Spacing.md)
                Divider()
            }

            content
                .padding(DesignTokens.Spacing.md)

            if let footer = footer {
                Divider()
                footer
                    .padding(DesignTokens.Spacing.md)
            }
        }
        .background(DesignTokens.Colors.surface)
        .cornerRadius(DesignTokens.CornerRadius.lg)
        .shadow(
            color: style.shadowColor,
            radius: style.shadowRadius,
            x: 0,
            y: style.shadowY
        )
    }
}

/// Visual style variants for ``DSCard``.
public enum DSCardStyle {
    /// Card with elevated shadow effect
    case elevated
    /// Card with subtle outline
    case outlined
    /// Card with no border or shadow
    case flat

    var shadowColor: Color {
        switch self {
        case .elevated:
            return Color.black.opacity(0.1)
        case .outlined, .flat:
            return Color.clear
        }
    }

    var shadowRadius: CGFloat {
        switch self {
        case .elevated: return 8
        case .outlined, .flat: return 0
        }
    }

    var shadowY: CGFloat {
        switch self {
        case .elevated: return 4
        case .outlined, .flat: return 0
        }
    }
}

#Preview("Card Variants") {
    ScrollView {
        VStack(spacing: DesignTokens.Spacing.lg) {
            DSCard {
                Text("Simple elevated card")
            }

            DSCard(style: .outlined) {
                Text("Outlined card style")
            }

            DSCard(
                header: {
                    Text("Header")
                        .font(DesignTokens.Typography.headline)
                },
                content: {
                    Text("Card with header section")
                }
            )

            DSCard(
                header: {
                    Text("Complete Card")
                        .font(DesignTokens.Typography.headline)
                },
                footer: {
                    DSButton("Action", style: .primary) { }
                },
                content: {
                    Text("Card with header, content, and footer")
                }
            )
        }
        .padding()
    }
}
```

**AI Prompt for this component:**
```
Create a versatile SwiftUI card component for a design system library. The card should:
1. Support optional header and footer sections
2. Have three style variants: elevated, outlined, and flat
3. Use generic content with ViewBuilder
4. Follow the DesignTokens system for spacing and styling
5. Include comprehensive DocC documentation
6. Provide preview examples showing all variants
```

### Step 7: Generate DocC Documentation Catalog

Create a documentation catalog file to provide an overview of your library:

1. In your package root, create a `Sources/DesignSystemKit/Documentation.docc` directory

2. Add a catalog file (you can use AI to help generate this):

```markdown
# ``DesignSystemKit``

A comprehensive SwiftUI component library with consistent design tokens and reusable UI components.

## Overview

DesignSystemKit provides production-ready, accessible SwiftUI components designed for rapid app development. All components follow a unified design language with customizable tokens for colors, spacing, typography, and more.

## Topics

### Design Foundation

- ``DesignTokens``
- ``DesignTokens/Colors``
- ``DesignTokens/Spacing``
- ``DesignTokens/Typography``
- ``DesignTokens/CornerRadius``

### Interactive Components

- ``DSButton``
- ``DSButtonStyle``
- ``DSCard``
- ``DSCardStyle``

### Getting Started

To use DesignSystemKit in your project, add it as a Swift Package dependency:

```swift
dependencies: [
    .package(url: "https://github.com/yourusername/DesignSystemKit", from: "1.0.0")
]
```

Then import the library in your SwiftUI views:

```swift
import DesignSystemKit

struct ContentView: View {
    var body: some View {
        VStack(spacing: DesignTokens.Spacing.md) {
            DSButton("Get Started", style: .primary) {
                // Your action
            }
        }
    }
}
```

### Design Principles

DesignSystemKit follows these core principles:

1. **Consistency** - Unified design tokens across all components
2. **Accessibility** - Full support for Dynamic Type and VoiceOver
3. **Flexibility** - Highly customizable while maintaining defaults
4. **Performance** - Lightweight components optimized for SwiftUI
5. **Documentation** - Comprehensive API documentation with examples
```

Save this file as `Sources/DesignSystemKit/Documentation.docc/DesignSystemKit.md`

### Step 8: Build and Preview Documentation Locally

Use the DocC plugin to generate and preview your documentation:

1. **Open Terminal** and navigate to your package directory:
   ```bash
   cd /path/to/DesignSystemKit
   ```

2. **Build documentation:**
   ```bash
   swift package generate-documentation --target DesignSystemKit
   ```

3. **Preview documentation with live server:**
   ```bash
   swift package --disable-sandbox preview-documentation --target DesignSystemKit
   ```

4. **Open the provided URL** (typically `http://localhost:8000/documentation/designsystemkit`) in your web browser

5. **Review the generated documentation:**
   - Verify all components appear in the sidebar
   - Check that code examples render correctly
   - Test symbol linking (clicking ``ComponentName`` should navigate)
   - Ensure preview images display properly

**Troubleshooting:** If documentation doesn't build, check that:
- All public types have at least a summary comment (`///`)
- DocC markdown syntax is valid (no missing backticks)
- The Documentation.docc folder is in the correct location

### Step 9: Use AI to Maintain Documentation Consistency

As your library grows, use AI to ensure documentation remains consistent:

1. **Create a documentation style guide** (use AI to generate):
   ```
   Prompt: "Create a documentation style guide for our SwiftUI component library.
   Include guidelines for:
   - Summary format and length
   - Parameter documentation style
   - Code example structure
   - When to use Topics sections
   - Symbol linking conventions
   - Markdown formatting standards"
   ```

2. **Use AI for documentation audits:**
   - Select multiple component files
   - Ask AI: "Review these component documentations for consistency. Check that all public APIs are documented, examples are provided, and the style matches our guidelines."

3. **Generate migration documentation:**
   - When updating component APIs, use AI to generate migration guides
   - Prompt: "Generate a migration guide for updating from DSButton v1 to v2, explaining what changed and how to update existing code."

4. **Create usage examples:**
   - Prompt: "Generate 5 real-world usage examples for the DSCard component, showing different scenarios like profile cards, content cards, settings cards, etc."

### Step 10: Export Static Documentation for GitHub Pages

Generate static documentation that can be hosted on GitHub Pages or other static hosts:

1. **Generate static documentation:**
   ```bash
   swift package --allow-writing-to-directory ./docs \
     generate-documentation --target DesignSystemKit \
     --output-path ./docs \
     --transform-for-static-hosting \
     --hosting-base-path DesignSystemKit
   ```

2. **Create a `.nojekyll` file** in the docs directory:
   ```bash
   touch docs/.nojekyll
   ```

3. **Commit the documentation:**
   ```bash
   git add docs/
   git commit -m "Add generated documentation"
   git push
   ```

4. **Configure GitHub Pages:**
   - Go to your repository settings on GitHub
   - Navigate to **Pages** section
   - Set source to **Deploy from branch**
   - Select **main** branch and **/docs** folder
   - Click **Save**

5. **Access your documentation** at `https://yourusername.github.io/DesignSystemKit/`

### Step 11: Create Comprehensive Unit Tests with AI

Use AI to generate test coverage for your components:

```swift
// Tests/DesignSystemKitTests/DSButtonTests.swift

import XCTest
@testable import DesignSystemKit
import SwiftUI

final class DSButtonTests: XCTestCase {

    func testButtonInitialization() {
        // Test that button initializes with correct title
        let button = DSButton("Test Button") { }

        // Verify button was created (basic smoke test)
        XCTAssertNotNil(button)
    }

    func testButtonStyles() {
        // Test all button style cases exist
        let styles: [DSButtonStyle] = [
            .primary,
            .secondary,
            .success,
            .warning,
            .destructive,
            .text
        ]

        // Verify each style has valid color configurations
        for style in styles {
            XCTAssertNotNil(style.backgroundColor)
            XCTAssertNotNil(style.foregroundColor)
        }
    }

    func testButtonWithIcon() {
        // Test button initialization with system image
        let button = DSButton(
            "Icon Button",
            style: .primary,
            systemImage: "star.fill"
        ) { }

        XCTAssertNotNil(button)
    }

    func testButtonAction() {
        // Test button action is called
        var actionCalled = false
        let button = DSButton("Action Test") {
            actionCalled = true
        }

        // Note: Full interaction testing requires ViewInspector or similar library
        XCTAssertNotNil(button)
    }
}
```

**AI Prompt for test generation:**
```
Generate comprehensive unit tests for the DSButton component. Include tests for:
1. Initialization with different parameters
2. All style variants
3. Button with and without icons
4. Color and styling properties
5. Edge cases and error conditions
Follow XCTest best practices and include descriptive test names.
```

### Step 12: Tag and Release Your Package

Create semantic versioned releases for your package:

1. **Ensure all tests pass:**
   ```bash
   swift test
   ```

2. **Create a git tag:**
   ```bash
   git tag -a 1.0.0 -m "Release version 1.0.0 - Initial release with DSButton and DSCard components"
   git push origin 1.0.0
   ```

3. **Create a GitHub Release:**
   - Go to your repository on GitHub
   - Click **Releases** > **Create a new release**
   - Select the tag you just created
   - Use AI to generate release notes:
     ```
     Prompt: "Generate release notes for version 1.0.0 of DesignSystemKit.
     Include: Overview, new features (DSButton, DSCard, DesignTokens),
     installation instructions, breaking changes (none for initial release),
     and what's next."
     ```
   - Publish the release

### Step 13: Create a README with AI Assistance

Generate a comprehensive README for your package:

**AI Prompt:**
```
Create a comprehensive README.md for a Swift Package Manager library called DesignSystemKit. Include:
1. Project title and description
2. Features list
3. Requirements (iOS 17+, Swift 5.9+)
4. Installation instructions for SPM
5. Quick start guide with code examples
6. Documentation link
7. Component showcase with screenshots
8. Contributing guidelines
9. License information (MIT)
10. Badges for Swift version, platform, and license
Use proper markdown formatting with code blocks, emoji, and clear sections.
```

Sample structure:
```markdown
# DesignSystemKit

A production-ready SwiftUI component library with AI-generated documentation.

[![Swift Version](https://img.shields.io/badge/Swift-5.9+-orange.svg)](https://swift.org)
[![Platform](https://img.shields.io/badge/platform-iOS%2017+-blue.svg)](https://www.apple.com/ios)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

## Features

- 🎨 Comprehensive design token system
- 🧩 Reusable SwiftUI components
- 📚 Full DocC documentation
- ♿️ Accessibility-first design
- 🎯 Type-safe API
- ⚡️ Lightweight and performant

## Installation

### Swift Package Manager

Add DesignSystemKit to your `Package.swift`:

```swift
dependencies: [
    .package(url: "https://github.com/yourusername/DesignSystemKit", from: "1.0.0")
]
```

## Quick Start

```swift
import SwiftUI
import DesignSystemKit

struct ContentView: View {
    var body: some View {
        VStack(spacing: DesignTokens.Spacing.md) {
            DSButton("Primary Action", style: .primary) {
                print("Button tapped!")
            }

            DSCard {
                Text("Card Content")
            }
        }
        .padding()
    }
}
```

## Documentation

Full documentation is available at [https://yourusername.github.io/DesignSystemKit](https://yourusername.github.io/DesignSystemKit)

## License

MIT License - see [LICENSE](LICENSE) file for details.
```

## Expected Results

After completing all steps, you should have:

1. **A fully functional Swift Package** with reusable SwiftUI components
2. **Comprehensive DocC documentation** for all public APIs with AI-generated content
3. **Design token system** providing consistent styling across components
4. **Unit tests** validating component behavior
5. **Static documentation website** hosted on GitHub Pages or similar
6. **Semantic versioning** with proper git tags and releases
7. **Professional README** with installation and usage instructions
8. **Working previews** for all components in Xcode

**Validation checklist:**
- ✅ Package builds without errors: `swift build`
- ✅ All tests pass: `swift test`
- ✅ Documentation generates successfully
- ✅ Components render correctly in Xcode previews
- ✅ Other projects can add your package as a dependency
- ✅ Documentation is accessible online
- ✅ All public APIs have documentation comments
- ✅ Code examples compile and run

## Troubleshooting

### Issue 1: Documentation Not Generating

**Problem:** Running `swift package generate-documentation` fails with errors.

**Solutions:**
- Ensure Swift 5.9+ is installed: `swift --version`
- Verify the DocC plugin is properly added to `Package.swift`
- Check that all documentation comments use triple slashes (`///`)
- Validate DocC markdown syntax (symbol links need double backticks: ``SymbolName``)
- Clear build artifacts: `swift package clean` and try again
- Check for typos in Documentation.docc file paths

### Issue 2: AI-Generated Documentation Missing Context

**Problem:** AI generates generic documentation that doesn't explain component-specific behavior.

**Solutions:**
- Provide more context in your prompts (include the component code)
- Specify the design system's purpose and usage patterns
- Request specific examples relevant to your use case
- Review and manually enhance AI output with domain knowledge
- Use multi-turn conversations with the AI to refine documentation
- Reference your style guide in the prompt

### Issue 3: Package Not Resolving in Other Projects

**Problem:** When adding the package to another project, SPM fails to resolve it.

**Solutions:**
- Ensure you've pushed all commits and tags to the remote repository
- Verify the repository URL is correct and accessible
- Check that the package has at least one semantic version tag (e.g., `1.0.0`)
- Validate `Package.swift` syntax: `swift package dump-package`
- Make sure the minimum platform versions are compatible
- Clear SPM cache: Delete `~/Library/Caches/org.swift.swiftpm/`

### Issue 4: Xcode Can't Find AI Assistant

**Problem:** Pressing Cmd+Shift+A doesn't invoke the AI assistant.

**Solutions:**
- Verify Apple Intelligence is enabled in System Settings (see KB-003)
- Check Intelligence preferences in Xcode: **Xcode > Settings > Intelligence**
- Ensure you're using Xcode 26.1+ : **Xcode > About Xcode**
- Verify an AI provider is configured (ChatGPT, Claude, etc.)
- Check network connectivity for cloud-based AI providers
- Restart Xcode to refresh AI service connections

### Issue 5: GitHub Pages Shows 404 Error

**Problem:** Documentation URL returns "404 Not Found" after configuring GitHub Pages.

**Solutions:**
- Verify `.nojekyll` file exists in the docs directory
- Check GitHub Pages settings: branch should be `main`, folder should be `/docs`
- Wait 5-10 minutes for GitHub Pages to deploy (initial deployment takes time)
- Ensure the docs folder is committed and pushed to the main branch
- Verify the URL format: `https://username.github.io/RepositoryName/`
- Check repository visibility (public repositories work best with GitHub Pages)

## Additional Tips

### Best Practices for Component Design

- **Keep components focused:** Each component should have a single, well-defined purpose
- **Use composition over complexity:** Build complex UIs from simple, composable components
- **Provide sensible defaults:** Make common use cases simple while supporting customization
- **Follow SwiftUI conventions:** Use ViewBuilder, support modifiers, embrace declarative syntax
- **Consider accessibility:** Support Dynamic Type, VoiceOver, high contrast modes
- **Test on multiple devices:** Verify layout and behavior on different screen sizes

### AI Documentation Workflow

1. **Write the component code first** - Implement functionality before documentation
2. **Use AI for first draft** - Generate initial documentation with comprehensive prompts
3. **Review and refine** - Enhance AI output with specific details and edge cases
4. **Maintain consistency** - Use the same prompts and style guide across all components
5. **Update regularly** - Regenerate documentation when APIs change
6. **Validate examples** - Always test that code examples compile and work correctly

### Versioning Strategy

Follow semantic versioning (SemVer):
- **Major version (1.0.0 → 2.0.0):** Breaking API changes
- **Minor version (1.0.0 → 1.1.0):** New features, backward compatible
- **Patch version (1.0.0 → 1.0.1):** Bug fixes, backward compatible

### Performance Optimization

- **Minimize view updates:** Use `@State`, `@Binding`, and `@ObservedObject` appropriately
- **Avoid unnecessary recomputations:** Use `let` for computed properties when possible
- **Profile with Instruments:** Identify performance bottlenecks in complex components
- **Lazy loading:** Use `LazyVStack`/`LazyHStack` for large scrollable lists
- **Asset optimization:** Use SF Symbols instead of custom images when possible

### Documentation Enhancement Ideas

- **Add tutorials:** Create step-by-step guides for common implementation patterns
- **Include video demos:** Screen recordings showing component usage
- **Provide design guidelines:** Explain when to use each component variant
- **Show real-world examples:** Full app screenshots using your components
- **Document design decisions:** Explain why certain APIs were chosen
- **Create migration guides:** Help users upgrade between major versions

### Expanding Your Component Library

Consider adding these components next:
- Input fields (text fields, secure fields, search bars)
- Selection controls (toggles, checkboxes, radio buttons, segmented controls)
- Navigation components (tabs, sidebars, navigation bars)
- Feedback components (alerts, toasts, loading indicators, progress bars)
- Layout containers (grids, stacks with predefined spacing)
- Data display (lists, tables, charts, badges, tags)
- Overlays (modals, sheets, popovers, tooltips)

### Community and Contribution

- **Accept contributions:** Set up contribution guidelines and issue templates
- **Use GitHub Discussions:** Create a community space for questions and ideas
- **Provide examples repository:** Separate repo with sample apps using your library
- **Maintain a changelog:** Document all changes between versions
- **Respond to issues:** Engage with users and fix bugs promptly
- **Host documentation:** Keep online documentation up-to-date with each release

## Related Articles

- KB-019: How to create and use custom Swift structs
- KB-021: Add UI elements to a SwiftUI view
- KB-031: Access the Coding Assistant in Xcode 26 (ChatGPT)
- KB-040: Generate documentation with ChatGPT
- KB-043: Add Claude as a custom model provider in Xcode

## Sources

- What's New in Xcode 26: Smarter, Faster, More Powerful - https://200oksolutions.com/blog/whats-new-in-xcode-26-smarter-faster-more-powerful-wwdc-2025-highlights/ (Accessed: November 17, 2025)
- Writing code with intelligence in Xcode - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- Swift Package Manager Documentation - https://docs.swift.org/swiftpm/documentation/packagemanagerdocs/ (Accessed: November 17, 2025)
- Swift-DocC Plugin for SwiftPM - https://github.com/swiftlang/swift-docc-plugin (Accessed: November 17, 2025)
- Documenting a Swift Framework or Package - https://www.swift.org/documentation/docc/documenting-a-swift-framework-or-package (Accessed: November 17, 2025)
- Using the Swift Package Manager command plugin for Swift-DocC - https://www.createwithswift.com/using-the-swift-package-manager-command-plugin-for-swift-docc/ (Accessed: November 17, 2025)
- Documenting your code with DocC - Swift with Majid - https://swiftwithmajid.com/2025/04/01/documenting-your-code-with-docc/ (Accessed: November 17, 2025)
- SwiftUI Design System: A Complete Guide to Building Consistent UI Components (2025) - https://dev.to/swift_pal/swiftui-design-system-a-complete-guide-to-building-consistent-ui-components-2025-299k (Accessed: November 17, 2025)
- Build Reusable SwiftUI Component Libraries With the Swift Package Manager - https://medium.com/better-programming/build-reusable-swiftui-component-libraries-with-the-swift-package-manager-23343681a8f3 (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
