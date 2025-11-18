# How to use Claude for complex refactoring tasks

**Article ID:** KB-047
**Difficulty:** Beginner-Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 15-30 minutes

## Overview

Claude Sonnet 4's integration with Xcode 26.1+ provides powerful capabilities for complex refactoring tasks, from modernizing legacy Objective-C code to migrating Swift projects to async/await patterns. Unlike simple code completion tools, Claude understands project context, maintains conversation history, and provides inline code modifications with reviewable diffs. This guide teaches you how to effectively prompt Claude for refactoring tasks, review changes safely, and apply incremental improvements to your codebase using AI-assisted development.

## Prerequisites

- Xcode 26.1 or later installed
- Apple Intelligence enabled in macOS System Settings (see KB-003)
- Claude account configured in Xcode Intelligence settings (see KB-041, KB-044)
- Active Claude subscription (Pro, Max, or premium Team/Enterprise seats with Claude Code access)
- Basic understanding of refactoring concepts and Swift/SwiftUI development
- Code that needs refactoring (existing project or sample code)
- Tests in place (recommended) to verify behavior before and after refactoring

## Steps

### Step 1: Select Code to Refactor

Before prompting Claude, identify and select the specific code you want to refactor:

1. **Open your project** in Xcode 26.1+
2. **Navigate to the file** containing code that needs refactoring
3. **Highlight the target code:**
   - For single functions: Click and drag to select the entire function
   - For classes/structs: Select the entire type definition
   - For inline refactoring: Select specific lines or code blocks
   - For multi-file refactors: Keep the main file open and reference others in your prompt

**Best Practice:** Start with smaller, focused selections (50-100 lines) rather than entire files. This improves Claude's accuracy and makes changes easier to review.

### Step 2: Open Claude in the Coding Assistant

1. **Access the Coding Assistant:**
   - Press **⌃⌘ I** (Control-Command-I) to open the Coding Assistant panel
   - Or click **Editor → Coding Assistant** from the menu bar
   - The assistant panel appears on the right side of the Xcode window

2. **Verify Claude is selected:**
   - Check the model selector dropdown in the Coding Assistant
   - Ensure **"Claude Sonnet 4"** is displayed
   - If ChatGPT is selected, click the dropdown and switch to Claude (see KB-046)

3. **Claude gathers context automatically:**
   - Claude reads your selected code
   - It analyzes your project structure and relevant files
   - Context includes conversation history from your current session

### Step 3: Write an Effective Refactoring Prompt

The quality of your refactoring depends heavily on prompt specificity. Use these proven prompt patterns:

#### Pattern 1: Function-Level Refactoring

For refactoring individual functions or methods:

```
Refactor this function to use async/await instead of completion handlers.
Keep the function signature compatible with existing call sites, and
add error handling for network failures.
```

#### Pattern 2: Architectural Refactoring

For larger structural changes:

```
Refactor this view controller to use modern async/await patterns for
all networking calls. Extract the networking logic into a separate
service layer following dependency injection. Add unit tests stubbing
URLSessionProtocol.
```

#### Pattern 3: SwiftUI Migration

For converting UIKit to SwiftUI or modernizing SwiftUI views:

```
Extract this SwiftUI component into a separate reusable view. Move
@State variables into an @Observable view model class. Create previews
with mock states for happy path, empty state, and error conditions.
```

#### Pattern 4: Performance Optimization

For performance-focused refactoring:

```
Refactor this view model to improve performance. Cache expensive
calculations, use lazy initialization where appropriate, and ensure
all property updates happen on the main thread.
```

**Key Prompt Principles:**

- **Be specific about the goal:** State what you want to achieve (async/await, dependency injection, performance, etc.)
- **Include constraints:** Mention backward compatibility, API surface requirements, or architectural patterns
- **Request tests:** Ask Claude to include unit tests to lock in behavior
- **Specify review format:** Request "minimal diffs" or "inline changes" for easier review

### Step 4: Use Advanced Prompting Techniques

For complex refactoring tasks, employ these advanced strategies:

#### Test-First Refactoring

Lock in behavior before making changes:

```
First, write XCTests to pin the current behavior of PriceFormatter
under DE and US locales with various edge cases. Don't modify
production code yet. Once tests pass, refactor the formatter to
use modern Swift patterns while keeping all tests green.
```

#### Staged Refactoring with Planning

Request Claude to plan before implementing:

```
Think step-by-step: Analyze this authentication system and propose
a refactoring plan to implement proper dependency injection. List
all classes that need changes, explain the migration path, then
implement the changes incrementally with tests after each stage.
```

#### Minimal Diff Requests

For surgical changes in large files:

```
Propose a minimal diff to make UserSessionStore conform to Sendable.
Only modify what's necessary to satisfy Swift 6 concurrency checking.
```

### Step 5: Review Claude's Proposed Changes

After Claude generates the refactored code, carefully review the changes:

1. **Read the explanation:**
   - Claude provides context about what changed and why
   - Review the reasoning to ensure it aligns with your goals
   - Ask follow-up questions if anything is unclear

2. **Examine inline diffs:**
   - For inline edits, Claude shows **before/after diffs** directly in your editor
   - Red highlighting shows removed code
   - Green highlighting shows added code
   - Review each change individually

3. **Verify the approach:**
   - Check that the refactoring maintains existing functionality
   - Ensure new patterns follow Swift/SwiftUI best practices
   - Confirm error handling is appropriate
   - Verify thread safety for concurrent code

4. **Test compatibility:**
   - If Claude claims "call sites remain compatible," verify this manually
   - Check that public APIs haven't changed unexpectedly
   - Review any new dependencies or imports

### Step 6: Apply Changes Incrementally

Never apply large refactorings all at once. Use this incremental approach:

1. **Apply one change at a time:**
   - For inline edits, click **"Accept"** to apply the suggestion
   - For chat-provided code, copy and paste one section at a time
   - For multi-file changes, implement file-by-file

2. **Build after each change:**
   - Press **⌘B** to build your project
   - Resolve any compiler errors before proceeding
   - Claude can usually fix build errors in 1-2 iterations if they occur

3. **Run tests frequently:**
   - Press **⌘U** to run your test suite
   - Verify all tests pass before moving to the next change
   - If tests fail, ask Claude to investigate and fix the issue

4. **Commit between stages:**
   - Use Git to commit after each successful refactoring stage
   - Example: `git commit -m "Refactor: Convert AuthManager to async/await"`
   - This creates rollback points if later changes cause issues

### Step 7: Iterate and Refine

Refactoring is an iterative process. Use Claude conversationally:

1. **Request improvements:**
   ```
   The code works, but make it more efficient. Cache the parsed result
   and avoid re-parsing on every call.
   ```

2. **Add missing pieces:**
   ```
   Add comprehensive error handling for network timeouts, invalid JSON,
   and unauthorized access scenarios.
   ```

3. **Optimize further:**
   ```
   This is good, but the view model is doing too much. Extract the
   validation logic into a separate validator class.
   ```

4. **Generate documentation:**
   ```
   Add doc comments in Apple style. Include parameter descriptions,
   return values, complexity notes, and thread-safety guarantees.
   ```

### Step 8: Verify Final Results

After completing the refactoring:

1. **Full build verification:**
   - Clean build folder: **⌘⇧K** (Command-Shift-K)
   - Build: **⌘B**
   - Ensure zero warnings and errors

2. **Complete test run:**
   - Run all tests: **⌘U**
   - Check test coverage to ensure refactored code is tested
   - Add new tests for edge cases if needed

3. **Runtime testing:**
   - Run the app in the simulator
   - Test the refactored functionality manually
   - Verify UI updates, network calls, and state management work correctly

4. **Code review:**
   - Review the final diff in Git
   - Ensure code style matches your project conventions
   - Verify all TODOs and placeholder comments are resolved

## Expected Results

After successfully using Claude for refactoring, you should see:

1. ✅ **Improved Code Quality:**
   - More readable and maintainable code structure
   - Modern Swift patterns (async/await, @Observable, etc.)
   - Better separation of concerns
   - Reduced code duplication

2. ✅ **Working Functionality:**
   - All tests passing (green test results)
   - Clean builds with zero errors
   - Runtime behavior identical to pre-refactoring state
   - No regressions in existing features

3. ✅ **Better Architecture:**
   - Cleaner separation between layers (UI, business logic, data)
   - Proper dependency injection where appropriate
   - Thread-safe concurrent code
   - Type-safe error handling

4. ✅ **Documentation and Tests:**
   - Comprehensive doc comments on public APIs
   - Unit tests covering refactored code paths
   - Example usage in previews or playground code

5. ✅ **Incremental Git History:**
   - Multiple logical commits showing refactoring progression
   - Clear commit messages describing each change
   - Easy rollback points if issues arise

## Troubleshooting

### Issue 1: Claude Suggests Too Many Changes at Once

**Symptoms:** Claude proposes refactoring an entire file or multiple files simultaneously, making review difficult

**Solutions:**
- Break your request into smaller chunks: "First, refactor just the networking layer. We'll handle the UI later."
- Use the phrase "minimal diff" or "surgical change" in your prompt
- Select smaller code sections (50 lines or less) before prompting
- Ask Claude to "propose changes incrementally, one function at a time"
- Use staged refactoring: "Step 1 only: extract the validation logic. Don't change anything else yet."

### Issue 2: Refactored Code Doesn't Build

**Symptoms:** After applying Claude's suggestions, Xcode shows compiler errors

**Solutions:**
- Share the exact compiler error with Claude: "The build fails with error: 'Cannot convert value of type X to Y'. Fix this."
- Claude can usually resolve build errors in 1-2 iterations
- Verify you copied all required imports and dependencies
- Check that you applied all parts of multi-step refactoring
- Ask Claude: "Walk me through the build errors and provide fixes for each one"
- If stuck, revert the change with Git and try a smaller refactoring scope

### Issue 3: Tests Fail After Refactoring

**Symptoms:** Build succeeds, but unit tests fail with assertion errors or unexpected behavior

**Solutions:**
- Share the failing test output with Claude: "These tests are failing: [paste test output]. What needs to be fixed?"
- Ask Claude to analyze the test failures: "Explain why this test fails and propose a fix that doesn't change the test's intent"
- Verify mock objects and test data still match the refactored signatures
- Check for threading issues (accessing UI from background threads or vice versa)
- Use test-first refactoring next time: Write/verify tests before refactoring production code

### Issue 4: Claude's Refactoring Doesn't Follow Project Conventions

**Symptoms:** Generated code uses different patterns, naming conventions, or architectural styles than your existing codebase

**Solutions:**
- Create a `.claude/CLAUDE.md` file in your project root documenting your code style, patterns, and conventions
- Include specific conventions in your prompt: "Follow our project's convention of using protocol-based dependency injection with factory methods"
- Reference existing code as examples: "Refactor this to match the pattern used in UserManager.swift"
- Provide feedback iteratively: "This works, but we use ViewState enums instead of Bool flags. Refactor to use that pattern."
- Ask Claude to review existing code first: "Study how we handle errors in PaymentService.swift, then refactor this class to match that pattern"

## Additional Tips

### When to Use Claude vs. ChatGPT for Refactoring

**Prefer Claude (Sonnet 4) for:**
- **Large refactoring tasks** with multi-file changes and complex context (Claude's 200K token context window excels here)
- **Swift 6 and modern Apple frameworks** (Claude tends to propose "Apple-native" solutions for SwiftUI, Combine, async/await)
- **Architectural changes** requiring long, coherent outputs and tight adherence to constraints
- **Legacy code modernization** (Objective-C to Swift, completion handlers to async/await)

**Prefer ChatGPT (GPT-5) for:**
- **Quick, simple refactorings** of single functions
- **General programming patterns** not specific to Apple platforms
- **Exploratory questions** about refactoring approaches before implementing

See KB-046 for instructions on switching between models.

### Breaking Down Large Refactoring Tasks

**Don't try to refactor massive files in one go.** Use this phased approach:

1. **Phase 1: Extract and isolate**
   - Extract 50-100 lines into separate functions or types
   - Run tests after each extraction
   - Commit each successful extraction

2. **Phase 2: Modernize patterns**
   - Convert completion handlers to async/await
   - Replace manual state management with @Observable
   - Migrate to newer API versions

3. **Phase 3: Optimize and clean**
   - Remove code duplication
   - Improve performance bottlenecks
   - Add comprehensive documentation

**This surgical approach is safer and easier to review than massive one-shot refactors.**

### Context Management for Long Sessions

For extended refactoring sessions:

- **Clear context between major tasks:** If you switch to a different refactoring area, the conversation history can become polluted with irrelevant context
- **Reference specific files:** Mention files explicitly in prompts to help Claude locate relevant code
- **Provide fresh context:** When starting a new refactoring area, give Claude a clear summary of what you've changed so far

### Leverage Claude's Extended Thinking

For particularly complex refactoring decisions:

- Use prompts like **"Think step-by-step"** or **"Think hard about the best approach"**
- These trigger Claude's extended reasoning mode, allocating more computational resources to analyze alternatives
- Useful for architectural decisions, performance optimizations, or migration strategies

### Create Project-Specific Guidelines

Save time and improve consistency by creating `.claude/CLAUDE.md` in your repository:

```markdown
# Project Refactoring Guidelines

## Code Style
- Use protocol-oriented design for all service layers
- Prefer composition over inheritance
- All networking uses async/await with structured concurrency

## Testing Requirements
- Minimum 80% code coverage for business logic
- All public APIs must have doc comments
- Use test doubles (protocols) instead of mocks

## Architectural Patterns
- MVVM for all SwiftUI views
- Coordinator pattern for navigation
- Repository pattern for data access
```

Claude reads this context and follows your project's conventions automatically.

### Review Changes Visually

For UI refactoring:

1. **Use SwiftUI Previews:** Ask Claude to generate comprehensive previews showing before/after states
2. **Take screenshots:** Compare visual output before and after refactoring
3. **Test all edge cases:** Empty states, error states, loading states, dark mode, Dynamic Type, RTL languages

### Performance Verification

After performance-related refactoring:

- **Profile with Instruments:** Use Time Profiler to verify optimizations actually improved performance
- **Measure objectively:** Don't trust "feels faster"—use `XCTMetric` for benchmarks
- **Ask Claude for benchmarks:** "Add microbenchmarks for this cache implementation with assertions for p95 latency under 5ms"

### Safe Async/Await Migration

When converting completion handlers to async/await:

1. **Keep compatibility:** Ask Claude to maintain the original API alongside the new async version temporarily
2. **Migrate incrementally:** Convert call sites one at a time
3. **Test thoroughly:** Async bugs can be subtle—test race conditions, cancellation, and error propagation
4. **Use Sendable properly:** Ensure types crossing concurrency boundaries conform to Sendable (Swift 6)

## Related Articles

- KB-041: How to create an Anthropic API account at console.anthropic.com
- KB-044: How to configure Claude API endpoint in Xcode Intelligence settings
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-048: How to leverage Claude's context window for large code reviews
- KB-049: How to ask Claude to analyze architecture patterns in your codebase

## Sources

1. **Anthropic Official Announcement - Claude in Xcode**
   https://www.anthropic.com/news/claude-in-xcode
   Accessed: November 17, 2025

2. **Anthropic Engineering - Claude Code Best Practices**
   https://www.anthropic.com/engineering/claude-code-best-practices
   Accessed: November 17, 2025

3. **Medium - From Red Builds to Release: How I Ship iOS Features 2× Faster with Claude in Xcode 26**
   https://alirezarezvani.medium.com/from-red-builds-to-release-how-i-ship-ios-features-2-faster-with-claude-in-xcode-26-204265c7919c
   Accessed: November 17, 2025

4. **Claude AI Dev - Claude Now Available in Xcode**
   https://claudeai.dev/blog/claude-xcode-integration/
   Accessed: November 17, 2025

5. **AI Tool Insight - Claude Now Available in Xcode 26 – AI Coding Assistant for Apple Developers**
   https://aitoolinsight.com/claude-in-xcode-ai-for-apple-developers/
   Accessed: November 17, 2025

6. **TestingCatalog - Apple integrates Anthropic Claude Sonnet 4 in Xcode 26**
   https://www.testingcatalog.com/apple-integrates-anthropic-claude-sonnet-4-in-xcode-26/
   Accessed: November 17, 2025

7. **MacRumors - Apple Releases Xcode 26 Beta 7 With GPT-5 Support and Claude Integration**
   https://www.macrumors.com/2025/08/28/xcode-gpt-5-claude-integration/
   Accessed: November 17, 2025

8. **9to5Mac - New Xcode beta now available with GPT-5 and Claude support**
   https://9to5mac.com/2025/08/28/new-xcode-beta-now-available-with-gpt-5-and-claude-support/
   Accessed: November 17, 2025

9. **Medium - Refactoring with Claude: A concrete and relatable example**
   https://medium.com/@jbelis/refactoring-with-claude-b690a364d2f0
   Accessed: November 17, 2025

10. **Authentic Jobs - 3 Creative Uses of Claude for Debugging and Refactoring Code**
    https://authenticjobs.com/3-creative-uses-of-claude-for-debugging-and-refactoring-code/
    Accessed: November 17, 2025

11. **TwoCentStudios - Rewriting a 12 Year Old Objective-C iOS App with Claude Code**
    https://twocentstudios.com/2025/06/22/vinylogue-swift-rewrite/
    Accessed: November 17, 2025

12. **DEV Community - Essential updates in Xcode 26.1.1 with Swift 6.2.1**
    https://dev.to/arshtechpro/essential-updates-in-xcode-2611-with-swift-621-2e3i
    Accessed: November 17, 2025

---
*This article is part of the Xcode v26.1+ knowledge base*
