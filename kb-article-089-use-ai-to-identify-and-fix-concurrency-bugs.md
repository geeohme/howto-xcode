# How to use AI to identify and fix concurrency bugs

**Article ID:** KB-089
**Difficulty:** Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 30-45 minutes

## Overview

Swift 6 introduces strict concurrency checking to eliminate data races at compile time, but understanding and fixing concurrency warnings can be challenging. Xcode 26.1+ integrates powerful AI assistants (ChatGPT and Claude) directly into the Coding Assistant panel, enabling you to identify subtle concurrency bugs, understand complex actor isolation issues, and generate fixes for Sendable conformance errors. This guide demonstrates how to effectively use AI to diagnose and resolve concurrency problems in Swift 6 projects.

## Prerequisites

- Xcode 26.1 or later with Swift 6 language mode enabled
- macOS 15.0 (Sequoia) or later
- Understanding of Swift 6 concurrency basics (actors, async/await, Sendable)
- AI Coding Assistant configured (see KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT)
- Claude configured as a secondary AI provider (see KB-046: How to switch between ChatGPT and Claude in the Coding Assistant)
- Familiarity with Swift 6 concurrency features:
  - KB-086: Introduction to Swift 6 strict concurrency checking (assumed prerequisite)
  - KB-087: Understanding actors and actor isolation (assumed prerequisite)
  - KB-088: Working with Sendable types and protocol conformance (assumed prerequisite)
- Active internet connection
- A Swift 6 project with concurrency warnings or errors

## Steps

### Step 1: Enable Swift 6 Strict Concurrency Checking

Before using AI to fix concurrency bugs, ensure your project has strict concurrency checking enabled to surface all potential data races.

1. Open your Xcode project
2. Select your target in the Project Navigator
3. Go to **Build Settings**
4. Search for "Strict Concurrency Checking"
5. Set the value to **Complete** (or use **Targeted** for gradual migration)

Alternatively, add this to your Swift compiler flags:
```
-strict-concurrency=complete
```

6. Build your project (⌘+B) to see all concurrency warnings and errors

### Step 2: Identify the Type of Concurrency Bug

Review the compiler diagnostics to categorize your concurrency issues. Common types include:

- **Data Race Warnings:** "Capture of 'variable' with non-sendable type in a @Sendable closure"
- **Actor Isolation Errors:** "Expression is 'async' but is not marked with 'await'"
- **Main Actor Violations:** "Call to main actor-isolated method in nonisolated context"
- **Sendable Conformance Issues:** "Type 'MyClass' does not conform to the 'Sendable' protocol"
- **Task Priority Inversions:** Incorrect use of Task priorities causing performance issues
- **Concurrent Mutations:** Accessing mutable state from multiple tasks without proper isolation

### Step 3: Open the AI Coding Assistant and Select the Right Model

1. Press **Command+0** (⌘+0) to open the Coding Assistant panel
2. Click **"New Conversation"** to start fresh
3. Select your preferred AI model:
   - **ChatGPT (GPT-5):** Good for straightforward concurrency fixes and general advice
   - **Claude Sonnet 4:** Recommended for complex concurrency analysis and architectural guidance
   - **GPT-5 (Reasoning):** Best for analyzing subtle race conditions and timing issues

For this guide, we'll use **Claude Sonnet 4** due to its superior reasoning capabilities for concurrency debugging.

### Step 4: Provide Context Using Effective Prompts

Use structured prompts to give the AI maximum context about your concurrency issue. Here are proven prompt patterns:

#### Prompt Pattern 1: Diagnosing Data Race Warnings

```
I'm getting the following Swift 6 concurrency warning:

[Paste exact compiler warning here]

Here's the relevant code:

```swift
[Paste your code snippet]
```

This code is part of a [describe architecture: MVVM/MVC/etc.] architecture. Can you:
1. Explain why this warning appears
2. Identify the potential data race
3. Suggest 2-3 different solutions with their trade-offs
4. Recommend which solution is best for my use case
```

#### Prompt Pattern 2: Fixing Actor Isolation Errors

```
I have an actor isolation error in Swift 6:

Error: [Paste error message]

Code context:

```swift
[Paste code with error]
```

I need to:
- Maintain thread safety
- Keep the API ergonomic for callers
- Avoid unnecessary async/await overhead where possible

Please suggest how to fix this with proper actor isolation.
```

#### Prompt Pattern 3: Converting to Sendable Types

```
My custom type doesn't conform to Sendable in Swift 6:

```swift
[Paste your type definition]
```

This type is used in [describe usage: networking layer/view models/etc.].

Can you:
1. Analyze why it's not Sendable
2. Show me how to make it Sendable while preserving functionality
3. Explain if I should use @unchecked Sendable and when that's appropriate
```

#### Prompt Pattern 4: Analyzing Complex Race Conditions

```
I suspect a race condition in this code that only appears intermittently in production:

```swift
[Paste suspicious code]
```

The symptoms are: [describe behavior]

Can you:
1. Identify potential race conditions
2. Explain the execution scenarios that could cause issues
3. Suggest refactoring to eliminate the race using Swift 6 concurrency primitives
4. Add appropriate assertions or logging to catch this early
```

### Step 5: Review and Apply AI-Suggested Fixes

When the AI responds with suggestions:

1. **Read the Explanation First:** Understand *why* the concurrency bug exists before applying fixes
2. **Evaluate Multiple Solutions:** If the AI provides options, consider the trade-offs
3. **Test Each Fix Incrementally:** Don't apply all changes at once
4. **Use @-mentions for Context:** Reference specific files with `@filename` to help the AI understand your project structure

Example interaction:

**You:**
```
@NetworkManager.swift I'm getting "Sendable" warnings on this class. Can you help fix it?
```

**AI Response Structure (Expected):**
- Explanation of why NetworkManager isn't Sendable
- Analysis of which properties cause the issue
- Suggested refactoring (e.g., using actors, making properties immutable)
- Updated code with proper isolation
- Migration notes for existing call sites

### Step 6: Verify Fixes with Thread Sanitizer (TSan)

After applying AI-suggested fixes, verify there are no runtime data races:

1. Go to **Product > Scheme > Edit Scheme**
2. Select the **Run** action
3. Go to **Diagnostics** tab
4. Enable **Thread Sanitizer**
5. Run your app and exercise the concurrent code paths
6. Check for TSan warnings in the console

If TSan reports issues, return to the AI with:

```
I applied your suggested fix, but Thread Sanitizer reports:

[Paste TSan output]

Can you help me understand what's still wrong?
```

### Step 7: Ask for Code Review and Best Practices

After fixing immediate issues, use AI to review your concurrency architecture:

```
Here's my updated code after fixing concurrency warnings:

```swift
[Paste refactored code]
```

Can you review this for:
1. Adherence to Swift 6 concurrency best practices
2. Potential performance bottlenecks (over-serialization, etc.)
3. Edge cases I might have missed
4. Suggestions for improved ergonomics while maintaining safety
```

### Step 8: Document Concurrency Decisions

Ask the AI to help document complex concurrency patterns:

```
Can you generate documentation comments for this actor explaining:
- Which operations are thread-safe and why
- Performance characteristics of concurrent access
- Any gotchas or non-obvious behaviors

```swift
[Paste your actor code]
```
```

The AI will generate comprehensive documentation that helps future maintainers understand your concurrency model.

## Expected Results

After following these steps with AI assistance, you should:

- **Eliminate all Swift 6 concurrency warnings and errors** from your project
- **Understand the root causes** of data races and actor isolation issues
- **Have well-structured concurrent code** using actors, async/await, and Sendable types appropriately
- **Pass Thread Sanitizer testing** with no runtime data race detections
- **Improve code maintainability** with clear concurrency boundaries and documentation
- **Build confidence** in using Swift 6's strict concurrency model effectively
- **Reduce debugging time** by catching concurrency issues at compile time rather than in production

## Troubleshooting

### Common Issue 1: AI Suggests Overly Complex Solutions

**Problem:** The AI recommends wrapping everything in actors or adding excessive async/await, making your code harder to use.

**Solution:**
- Provide more context about your architecture and performance requirements
- Ask for simpler alternatives: "Can you suggest a solution that doesn't require making every property async?"
- Use the prompt: "Show me the minimal changes needed to fix this concurrency warning"
- Consider using Swift 6.2's "Approachable Concurrency" features for simpler main-actor isolation
- If using Claude, ask it to compare multiple approaches and recommend the most pragmatic one

### Common Issue 2: Suggested Fixes Don't Compile

**Problem:** The AI-generated code has syntax errors or uses APIs incorrectly.

**Solution:**
- Verify you're using Swift 6 language mode (the AI might generate Swift 5.x patterns)
- Paste the compilation error back to the AI: "This code produces the error: [paste error]"
- Provide more context: Include imports, surrounding class/struct definitions, and property declarations
- Ask for complete code: "Can you show the entire class/actor definition, not just the method?"
- Use GPT-5 Reasoning model for more accurate syntax (at the cost of slower responses)
- Reference the official Swift 6 documentation and ask the AI to align with current best practices

### Common Issue 3: Performance Regression After Applying Fixes

**Problem:** After fixing concurrency warnings, your app is noticeably slower due to over-synchronization.

**Solution:**
- Profile with Instruments before and after changes
- Ask the AI: "My code is now safe but slower. How can I reduce synchronization overhead while maintaining safety?"
- Consider these patterns:
  - Use `nonisolated` for computed properties that don't need isolation
  - Batch operations to reduce actor boundary crossings
  - Use `TaskLocal` for thread-safe access to context without passing parameters
- Share profiling results with the AI to identify bottlenecks
- Ask: "Can you refactor this to reduce the number of async/await suspension points?"

### Common Issue 4: AI Recommends @unchecked Sendable Too Freely

**Problem:** The AI suggests marking types as `@unchecked Sendable` without proper justification, which disables compiler safety checks.

**Solution:**
- Question the recommendation: "Why is @unchecked Sendable appropriate here? Are there alternatives?"
- Ask for proper Sendable conformance first: "Show me how to make this properly Sendable without @unchecked"
- Only use `@unchecked Sendable` when:
  - You're wrapping thread-safe types from Objective-C or C
  - You've implemented manual synchronization (locks, serial queues) that the compiler can't verify
  - You've thoroughly verified thread safety through testing and reasoning
- Request documentation: "Can you add comments explaining why @unchecked Sendable is safe here?"

## Additional Tips

### Effective Prompts for Concurrency Debugging

**Be Specific About Your Goals:**
- ❌ "Fix my concurrency warnings"
- ✅ "I have 15 Sendable warnings in my networking layer. I need to maintain backward compatibility with iOS 16. Show me how to conditionally adopt Swift 6 concurrency."

**Provide Execution Context:**
- "This function is called from both UI code (main thread) and background processing (Task)"
- "This property is accessed by multiple concurrent downloads"
- "This actor needs to coordinate with UIKit which requires main thread access"

**Ask for Comparisons:**
- "Compare using an actor vs. OSAllocatedUnfairLock for this use case"
- "What are the trade-offs between making this class Sendable vs. wrapping it in an actor?"

**Request Migration Strategies:**
- "I have 100 files to migrate. Can you suggest a phased approach starting with the most critical?"
- "Show me how to migrate this UIKit view controller to use async/await while maintaining UIKit compatibility"

### Leverage Swift 6.2 Features (November 2025)

If you're using Xcode 26.1 with Swift 6.2, mention these in your prompts:

- **Approachable Concurrency:** "Use Swift 6.2's Approachable Concurrency to simplify this"
- **Default Main Actor Isolation:** "Can I use the new default main actor isolation instead of adding @MainActor everywhere?"
- **Improved Debugging:** "Show me how to use named tasks for better debugging"
- **Async Stepping:** Take advantage of LLDB's improved async debugging to step through concurrent code

### Use the Right AI Model for the Task

**ChatGPT GPT-5:**
- Quick fixes for straightforward warnings
- Generating boilerplate Sendable conformances
- Explaining basic concurrency concepts
- Adding @MainActor annotations

**Claude Sonnet 4:**
- Complex architectural refactoring
- Analyzing potential race conditions
- Reviewing large codebases for concurrency issues
- Understanding subtle timing bugs
- Migration strategies for large projects

**GPT-5 Reasoning:**
- Debugging intermittent race conditions
- Analyzing complex actor interaction patterns
- Understanding performance implications
- Reasoning about correctness proofs

### Combine AI with Traditional Tools

Don't rely solely on AI—use it alongside Swift's built-in tools:

1. **Compiler Warnings:** Let the compiler guide your fixes first
2. **Thread Sanitizer (TSan):** Verify AI fixes don't introduce runtime races
3. **Instruments:** Profile performance after concurrency changes
4. **LLDB Async Debugging:** Step through concurrent code to understand behavior
5. **AI Assistant:** Use for explanation, suggestions, and code generation

### Keep Learning

After the AI helps fix issues, ask follow-up questions:

- "Can you explain the Swift 6 rules that caused these warnings?"
- "What are the common patterns for this type of concurrency problem?"
- "Are there any WWDC sessions or documentation I should read to understand this better?"

This helps you learn the underlying concepts so you can avoid similar issues in the future.

## Related Articles

- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT
- KB-046: How to switch between ChatGPT and Claude in the Coding Assistant
- KB-086: Introduction to Swift 6 strict concurrency checking (assumed prerequisite)
- KB-087: Understanding actors and actor isolation (assumed prerequisite)
- KB-088: Working with Sendable types and protocol conformance (assumed prerequisite)
- KB-039: How to fix errors and bugs with ChatGPT assistance
- KB-047: How to use Claude for complex refactoring tasks
- KB-055: How to have AI explain error messages and suggest fixes

## Sources

- Apple Developer Documentation - "Writing code with intelligence in Xcode" - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- Apple Developer Documentation - "Adopting strict concurrency in Swift 6 apps" - https://developer.apple.com/documentation/swift/adoptingswift6 (Accessed: November 17, 2025)
- Swift.org - "Enabling Complete Concurrency Checking" - https://www.swift.org/documentation/concurrency/ (Accessed: November 17, 2025)
- Swift.org - "Swift 6.2 Released" - https://www.swift.org/blog/swift-6.2-released/ (Accessed: November 17, 2025)
- 9to5Mac - "Apple releases Xcode 26.1.1 with coding intelligence improvements" - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- Medium - "From Red Builds to Release: How I Ship iOS Features 2× Faster with Claude in Xcode 26" by Reza Rezvani - https://alirezarezvani.medium.com/from-red-builds-to-release-how-i-ship-ios-features-2-faster-with-claude-in-xcode-26-204265c7919c (Accessed: November 17, 2025)
- AWS Blog - "Introducing Claude 4 in Amazon Bedrock" - https://aws.amazon.com/blogs/aws/claude-opus-4-anthropics-most-powerful-model-for-coding-is-now-in-amazon-bedrock/ (Accessed: November 17, 2025)
- Antoine van der Lee - "Swift 6: What's New and How to Migrate" - https://www.avanderlee.com/concurrency/swift-6-migrating-xcode-projects-packages/ (Accessed: November 17, 2025)
- Donny Wals - "Exploring concurrency changes in Swift 6.2" - https://www.donnywals.com/exploring-concurrency-changes-in-swift-6-2/ (Accessed: November 17, 2025)
- Donny Wals - "What is Approachable Concurrency in Xcode 26?" - https://www.donnywals.com/what-is-approachable-concurrency-in-xcode-26/ (Accessed: November 17, 2025)
- Hacking with Swift - "What's new in Swift 6.2?" - https://www.hackingwithswift.com/articles/277/whats-new-in-swift-6-2 (Accessed: November 17, 2025)
- Quality Coding - "A Conversation With Swift 6 About Data Race Safety" - https://qualitycoding.org/conversation-swift6-data-race-safety/ (Accessed: November 17, 2025)
- Quality Coding - "XCTest Meets @MainActor: How to Fix Strict Concurrency Warnings" - https://qualitycoding.org/xctest-mainactor/ (Accessed: November 17, 2025)
- Simon B. Stöckli - "Using Claude with Coding Assistant in Xcode 26" - https://simonbs.dev/posts/using-claude-with-coding-assistant-in-xcode-26/ (Accessed: November 17, 2025)
- swiftyplace - "Debugging Swift Concurrency: 'Am I on the Main Actor?' (Not the Main Thread)" - https://www.swiftyplace.com/blog/debugging-swift-concurrency (Accessed: November 17, 2025)
- Stack Overflow - "Complete Concurrency check enabled and how to resolve warnings" - https://stackoverflow.com/questions/78282542/complete-concurrency-check-enabled-and-how-to-resolve-warnings (Accessed: November 17, 2025)
- SD Times - "Swift 6 now available with strict concurrency checking" - https://sdtimes.com/softwaredev/swift-6-now-available-with-strict-concurrency-checking/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
