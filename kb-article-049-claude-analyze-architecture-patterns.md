# How to ask Claude to analyze architecture patterns in your codebase

**Article ID:** KB-049
**Difficulty:** Beginner-Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 30-60 minutes

## Overview

Xcode 26.1+ includes native integration with Claude (Anthropic's AI assistant), allowing you to analyze and understand your codebase's architectural patterns directly within your IDE. Claude's 200,000 token context window enables comprehensive analysis of entire project architectures, identifying patterns like MVVM, Clean Architecture, or custom approaches, and providing actionable recommendations for improvement.

## Prerequisites

- Xcode 26.1 or later installed on your Mac
- macOS 15 or later (macOS 26 Tahoe recommended for full Coding Intelligence features)
- Claude configured and working in Coding Assistant (see KB-043, KB-044, KB-045, KB-046)
- Existing codebase with multiple files (at least 5-10 files recommended)
- Basic understanding of software architecture concepts (MVC, MVVM, etc.)
- Active Claude subscription (Pro, Max, or Team/Enterprise plan for Claude Sonnet 4 access)
- Active internet connection for AI requests

## Steps

### Step 1: Enable Claude in Xcode Settings

First, ensure Claude is properly configured as your AI provider:

1. Open **Xcode**
2. Navigate to **Xcode > Settings** (or press `Cmd + ,`)
3. Select the **Intelligence** tab
4. Click **"Add a Model Provider"** or select from the provider dropdown
5. Choose **Claude in Xcode** from the list
6. Click **"Sign In"** and authenticate with your Claude account credentials
7. Verify that **Claude Sonnet 4** appears as an available model
8. Ensure **Coding Intelligence** is toggled on

Alternatively, you can use an API key:
- Visit **console.anthropic.com/settings/keys** to generate a Claude API key
- In Xcode Settings > Intelligence, select **"Internet Hosted"** as the provider type
- Enter your Claude API key
- Click **OK** to save

### Step 2: Prepare Your Codebase for Analysis

Before asking Claude to analyze your architecture, prepare your project:

1. **Create a `.claude-context/` directory** in your project root (optional but recommended):
   ```bash
   mkdir .claude-context
   ```

2. **Document your current understanding** (if any) in a `CLAUDE.md` file:
   ```markdown
   # Project Architecture Notes

   - Current pattern: Unknown/MVVM/Clean Architecture/etc.
   - Known issues: [List any architectural concerns]
   - Goals: [What you want to improve]
   ```

3. **Ensure your codebase is organized**:
   - Remove unnecessary build artifacts
   - Ensure key files are accessible (not hidden in complex folder structures)
   - Have a clear project structure in Xcode's navigator

### Step 3: Select Files for Architectural Review

Choose which parts of your codebase to analyze:

**Option A: Start with a Representative Sample**
1. Select 3-5 key files that represent different layers of your app:
   - A View file (SwiftUI View or UIViewController)
   - A ViewModel or Presenter
   - A Model or Entity
   - A networking/data layer file
   - An app coordinator or entry point

**Option B: Analyze by Feature Slice**
1. Choose all files related to one complete feature
2. This helps Claude understand your architecture in context

**Option C: Full Project Analysis**
1. For smaller projects (<50 files), you can request analysis of the entire codebase
2. Claude's 200,000 token context window can handle substantial code volumes

### Step 4: Open the Coding Assistant

With your approach decided, open the Coding Assistant:

1. Press **Cmd + Shift + A** to open the Coding Assistant panel, or
2. Select **Editor > Coding Tools > Open Assistant** from the menu
3. The Assistant panel will appear at the bottom of your Xcode window

### Step 5: Craft Effective Architecture Analysis Prompts

Use these proven prompt patterns for architecture analysis:

#### Pattern 1: Discovery Prompt (When You're Unsure of Current Architecture)

```
I need you to analyze the architecture of my iOS project. Please:

1. Examine the following files: [list 5-10 key files or say "the entire project"]
2. Identify the architectural pattern(s) being used (e.g., MVC, MVVM, VIPER, Clean Architecture)
3. Describe how data flows through the application
4. Identify any mixing of patterns or architectural inconsistencies
5. Rate the current architecture's adherence to best practices (1-10 scale)

Focus on SwiftUI/UIKit patterns and modern iOS development practices.
```

#### Pattern 2: Specific Pattern Analysis

```
Please analyze my codebase to determine if it follows MVVM (Model-View-ViewModel) architecture:

- Identify which files serve as Views, ViewModels, and Models
- Check if ViewModels properly separate business logic from Views
- Verify that Views only handle UI concerns
- Look for any violations of MVVM principles
- Suggest improvements to better align with MVVM best practices

Files to examine: [list specific files or directories]
```

#### Pattern 3: Clean Architecture Assessment

```
Analyze my iOS project for Clean Architecture principles:

1. Identify the separation of concerns across layers:
   - Presentation Layer (Views, ViewModels)
   - Domain Layer (Use Cases, Entities, Repository Interfaces)
   - Data Layer (Repository Implementations, Network, Persistence)

2. Check dependency directions (should flow from outer to inner layers)
3. Verify that the Domain layer has no dependencies on UIKit/SwiftUI
4. Identify any violations of the Dependency Inversion Principle
5. Recommend refactoring priorities

Context: This is a [describe app type] with approximately [number] of files.
```

#### Pattern 4: Comparison and Migration Prompt

```
My project currently uses [current architecture, e.g., MVC]. I'm considering
migrating to [target architecture, e.g., MVVM with Clean Architecture].

Please:
1. Analyze the current architecture implementation
2. Identify which components would map to the new architecture
3. Suggest a migration strategy with specific steps
4. Highlight potential challenges in the migration
5. Recommend which files to refactor first

Focus on: [specific area of concern or feature]
```

#### Pattern 5: SwiftUI-Specific Modern Patterns (2025)

```
Analyze my SwiftUI codebase for modern 2025 architectural patterns:

1. Check usage of @Observable, @Bindable, and Swift macros
2. Identify if the project uses "Classic MVVM" (@Published) or modern patterns
3. Evaluate state management approach (SwiftData, Combine, async/await)
4. Assess whether the architecture leverages SwiftUI's reactive nature
5. Recommend updates to align with SwiftUI best practices in 2025

Note: I'm using Xcode 26.1 with Swift 6.2.1 and iOS 26.1 SDK.
```

### Step 6: Provide Context Files to Claude

After submitting your prompt:

1. Claude may ask for specific files to analyze
2. You can provide files by:
   - Opening them in the editor (Claude can see open tabs)
   - Copying relevant code into the chat
   - Referencing file paths directly: "Please analyze `Views/HomeView.swift`"

3. For best results, provide:
   - The project's main entry point (AppDelegate or App struct)
   - Representative view files
   - ViewModel/Presenter files
   - Data model files
   - Any custom architecture components

### Step 7: Review Claude's Analysis

Claude will provide a comprehensive architectural analysis including:

1. **Architecture Pattern Identification**: What pattern(s) your code follows
2. **Structural Breakdown**: How your code is organized across layers
3. **Data Flow Diagram**: How information moves through your app (in text form)
4. **Best Practice Compliance**: Where you're following or deviating from standards
5. **Specific Recommendations**: Concrete steps to improve your architecture

**Example Output:**
```
Your project follows a hybrid architecture combining MVVM with some Clean
Architecture principles:

PRESENTATION LAYER:
- Views: HomeView.swift, ProfileView.swift (SwiftUI)
- ViewModels: HomeViewModel.swift, ProfileViewModel.swift
  ✓ Good: ViewModels use @Observable (modern pattern)
  ⚠️  Issue: ViewModels directly import networking code (should use repository)

DOMAIN LAYER:
- Partially implemented
- Missing: Use Cases and Repository Interfaces

DATA LAYER:
- NetworkService.swift handles API calls
- UserDefaultsManager.swift for persistence
  ⚠️  Issue: NetworkService is accessed directly by ViewModels

RECOMMENDATIONS:
1. Introduce a Repository pattern to abstract data sources
2. Create Use Cases for complex business logic
3. Move networking interfaces to the domain layer
...
```

### Step 8: Ask Follow-Up Questions

Continue the conversation to dive deeper:

```
Examples:
- "Can you show me how to refactor HomeViewModel to use a repository pattern?"
- "What would the repository interface look like for this feature?"
- "How should I handle navigation in this architecture?"
- "Show me an example of a Use Case for the login feature"
- "Which files violate the single responsibility principle?"
```

### Step 9: Request Architectural Recommendations with Priority

```
Based on your analysis, please prioritize architectural improvements:

1. List issues from most critical to least critical
2. For each issue, explain:
   - Why it matters
   - What problems it could cause
   - Estimated refactoring effort (small/medium/large)
3. Suggest a phased approach to improvements
4. Identify "quick wins" vs. "long-term refactorings"
```

### Step 10: Generate Architecture Documentation

Once you understand your architecture:

```
Please create architecture documentation for my project including:

1. A high-level architecture overview
2. Layer responsibilities and boundaries
3. Data flow diagrams (in text/markdown format)
4. File organization conventions
5. Dependency rules
6. Code examples for each layer

Format this as markdown that I can save to ARCHITECTURE.md
```

## Expected Results

After completing these steps, you should have:

- **Clear understanding** of your current architectural pattern(s)
- **Identification** of architectural strengths and weaknesses
- **Specific recommendations** for improvements prioritized by impact
- **Code examples** showing how to implement recommended patterns
- **Migration strategy** if moving to a different architecture
- **Documentation** you can reference and share with your team
- **Confidence** in making architectural decisions going forward

Claude's analysis should provide:
- 90%+ accuracy in pattern identification for well-structured code
- Actionable recommendations aligned with iOS/Swift best practices
- Context-aware suggestions based on your specific codebase
- Adherence to 2025 modern practices (SwiftUI, async/await, Swift 6)

## Troubleshooting

### Issue 1: "Claude doesn't seem to understand my architecture"

**Problem:** Claude's analysis seems off-target or misidentifies your patterns.

**Solution:**
- Provide more context upfront: "This project uses MVVM with coordinators for navigation"
- Share more representative files from different layers
- Ask Claude to explain its reasoning: "Why did you identify this as MVC instead of MVVM?"
- Explicitly state what you disagree with and ask for re-evaluation
- Ensure you're analyzing files that actually implement your architecture (not test files or utilities)

### Issue 2: "Analysis is too surface-level"

**Problem:** Claude provides generic advice rather than specific recommendations.

**Solution:**
- Request deeper analysis: "Please think deeply about the architectural trade-offs here"
- Use extended thinking mode: "I need you to think through the implications of this architecture"
- Provide specific concerns: "I'm worried about testability and scalability"
- Ask for code examples: "Show me exactly how to implement this recommendation"
- Reference specific files: "Focus your analysis on how HomeViewModel.swift interacts with NetworkService.swift"

### Issue 3: "Claude suggests outdated patterns"

**Problem:** Recommendations reference older iOS patterns (e.g., @Published instead of @Observable).

**Solution:**
- Specify your environment: "I'm using Xcode 26.1, Swift 6.2.1, and iOS 26.1 SDK"
- Request modern patterns: "Please recommend 2025 best practices for SwiftUI architecture"
- Mention specific technologies: "This project uses @Observable, async/await, and SwiftData"
- Correct Claude if needed: "Actually, in 2025 we use @Observable instead of @Published"
- Ask for version-specific advice: "What's the modern SwiftUI approach to this pattern?"

### Issue 4: "Cannot access enough files for full analysis"

**Problem:** Claude seems limited in how many files it can analyze at once.

**Solution:**
- Analyze in phases: Start with one feature, then expand
- Focus on representative samples rather than entire codebase
- Use the "Vertical Slice Architecture" approach: Analyze one complete feature at a time
- Create a summary document with code snippets from multiple files
- Prioritize the most important architectural components first

## Additional Tips

- **Use .claude-context/ for consistency**: Store architectural guidelines, naming conventions, and patterns in `.claude-context/architecture.md` so Claude knows your preferences
- **Start broad, then narrow**: First get a high-level architectural overview, then dive deep into specific layers or features
- **Leverage Claude's context window**: Unlike ChatGPT, Claude can handle significantly larger codebases in a single analysis (200,000 tokens)
- **Be specific about iOS patterns**: Mention whether you're using SwiftUI or UIKit, as architectural patterns differ significantly
- **Request comparisons**: Ask Claude to compare your architecture to industry-standard examples (e.g., "How does this compare to Apple's sample apps?")
- **Iterate on the analysis**: Architecture analysis is a conversation, not a one-shot question. Refine your understanding through multiple exchanges
- **Document decisions**: Save Claude's recommendations to `ARCHITECTURE.md` or `.claude-context/` for future reference
- **Consider token efficiency**: Structure your code for AI-friendliness—clear separation of concerns helps Claude provide better analysis
- **Use multi-agent thinking**: For complex architectural decisions, ask Claude to "think deeply" or "evaluate trade-offs" to engage extended reasoning
- **Validate recommendations**: While Claude is highly capable, always verify architectural recommendations against official Apple documentation and current iOS best practices
- **Export and share**: Copy Claude's analysis to share with your team for architectural discussions and decision-making

## Related Articles

- KB-047: How to use Claude for complex refactoring tasks (forthcoming)
- KB-048: How to leverage Claude's context window for large code reviews (forthcoming)
- KB-050: How to compare responses between ChatGPT and Claude for the same coding task (forthcoming)
- KB-038: How to ask ChatGPT to explain selected code in your project
- KB-040: How to generate documentation comments with ChatGPT

## Sources

- Anthropic - Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 17, 2025)
- 9to5Mac - Xcode 26 will support multiple AI models, like Claude - https://9to5mac.com/2025/06/10/beyond-chatgpt-xcode-26-will-support-multiple-ai-models-like-claude/ (Accessed: November 17, 2025)
- 9to5Mac - Apple preps native Claude integration on Xcode - https://9to5mac.com/2025/08/18/apple-preps-native-claude-integration-on-xcode/ (Accessed: November 17, 2025)
- CISIN - How to Integrate ChatGPT and Claude in Xcode 26 - https://www.cisin.com/coffee-break/how-to-integrate-chatgpt-and-claude-in-xcode-26.html (Accessed: November 17, 2025)
- Simon B. Støvring - Using Claude with Coding Assistant in Xcode 26 - https://simonbs.dev/posts/using-claude-with-coding-assistant-in-xcode-26/ (Accessed: November 17, 2025)
- Medium - From Red Builds to Release: How I Ship iOS Features 2× Faster with Claude in Xcode 26 - https://alirezarezvani.medium.com/from-red-builds-to-release-how-i-ship-ios-features-2-faster-with-claude-in-xcode-26-204265c7919c (Accessed: November 17, 2025)
- Medium - Keep the AI Vibe: Optimizing Codebase Architecture for AI Coding Tools - https://medium.com/@richardhightower/ai-optimizing-codebase-architecture-for-ai-coding-tools-ff6bb6fdc497 (Accessed: November 17, 2025)
- Medium - Modern MVVM in SwiftUI 2025: The Clean Architecture You've Been Waiting For - https://medium.com/@minalkewat/modern-mvvm-in-swiftui-2025-the-clean-architecture-youve-been-waiting-for-72a7d576648e (Accessed: November 17, 2025)
- Alexey Naumov - Clean Architecture for SwiftUI - https://nalexn.github.io/clean-architecture-swiftui/ (Accessed: November 17, 2025)
- Netguru - SwiftUI Clean Architecture: Swift iOS Architectural Design - https://www.netguru.com/blog/clean-swift-with-swiftui-ios (Accessed: November 17, 2025)
- Medium - Building an iOS App with MVVM and Clean Architecture - https://medium.com/@krishnaprakashn/building-an-ios-app-with-mvvm-and-clean-architecture-a-practical-guide-16c0fd74ca44 (Accessed: November 17, 2025)
- eesel AI - How to navigate any codebase with Claude Code in 2025 - https://www.eesel.ai/blog/navigate-codebase-claude-code (Accessed: November 17, 2025)
- GitHub - claude-code-infrastructure-showcase - https://github.com/diet103/claude-code-infrastructure-showcase/blob/main/.claude/agents/code-architecture-reviewer.md (Accessed: November 17, 2025)
- arXiv - Exploring Prompt Patterns in AI-Assisted Code Generation - https://arxiv.org/html/2506.01604v1 (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
