# How to use AI to optimize slow-performing code

**Article ID:** KB-056
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 45-60 minutes

## Overview

Xcode 26.1+ combines powerful AI-driven Coding Intelligence with advanced Instruments profiling tools to help you identify and optimize slow-performing code. This integrated workflow allows you to profile your app using Instruments to pinpoint performance bottlenecks, then leverage ChatGPT or Claude Sonnet 4 in the Coding Assistant to analyze the problematic code and generate optimized solutions. This guide demonstrates a systematic approach to performance optimization that combines profiling data with AI-powered code analysis and refactoring.

## Prerequisites

- Xcode 26.1 or later installed (26.1.1 recommended for improved Coding Assistant performance)
- macOS 15.0 or later (macOS 26 Tahoe recommended for full Coding Intelligence features)
- AI Coding Assistant configured (ChatGPT or Claude - see KB-031 through KB-046)
- An iOS/macOS app project with performance issues or slow-running code
- Physical test device (recommended - simulator performance deviates by up to 30% from real hardware)
- Active internet connection for AI services
- Basic understanding of Swift performance concepts (algorithms, data structures, concurrency)

## Steps

### Step 1: Identify Performance Issues with User Testing

Before profiling, establish what "slow" means for your specific use case:

1. **Test your app manually** and identify areas where performance feels sluggish
2. **Document specific scenarios** where slowness occurs:
   - Slow UI updates or animations
   - Long loading times for data or images
   - Laggy scrolling in lists or collection views
   - Delayed responses to user interactions
   - High CPU usage causing device heat or battery drain
3. **Reproduce the issue consistently** - ensure you can reliably trigger the performance problem
4. **Set performance goals:** Define what "acceptable" performance looks like (e.g., "List should scroll at 60 FPS" or "Data load should complete in under 1 second")

**Example scenarios to document:**
```
Issue: Product list scrolls at ~20 FPS with visible stuttering
Scenario: Open app > Navigate to Products > Scroll through 100+ items
Expected: Smooth 60 FPS scrolling
Current: Noticeable frame drops every 2-3 seconds

Issue: Search results take 3-5 seconds to display
Scenario: Type query in search bar > Press search
Expected: Results in under 500ms
Current: 3-5 second delay with frozen UI
```

### Step 2: Profile with Instruments to Find Bottlenecks

Use Xcode's Instruments to identify exactly where your app is slow:

1. **Build for profiling:** In Xcode, select **Product > Profile** (or press `Cmd + I`)
   - This automatically builds your app in Release configuration for accurate measurements
   - Xcode will launch Instruments automatically
2. **Choose the appropriate instrument template:**
   - **Time Profiler**: For CPU-bound performance issues (slow algorithms, excessive computation)
   - **SwiftUI Instrument**: For SwiftUI view update performance problems
   - **Allocations/Leaks**: For memory-related slowness
   - **Power Profiler**: For battery drain and thermal issues
3. **Select your target device** (prefer physical device over simulator)
4. **Start recording** by clicking the red Record button
5. **Reproduce the slow scenario** you documented in Step 1
6. **Stop recording** after capturing the performance issue (typically 10-30 seconds of activity)

**For this guide, we'll focus on Time Profiler** as it's the most common starting point for optimization.

### Step 3: Analyze Time Profiler Results

Interpret the profiling data to find your performance bottlenecks:

1. **Review the timeline graph** at the top - look for CPU spikes that correspond to the slow behavior
2. **Examine the Call Tree** (bottom panel):
   - Click the "Call Tree" tab
   - Enable these display options in the bottom-left inspector:
     - ✓ Separate by Thread
     - ✓ Hide System Libraries (to focus on your code)
     - ✓ Show Obj-C Only (optional - reduces noise for Swift apps)
3. **Sort by "Weight" or "Self Weight"** to see which functions consume the most CPU time
   - **Weight**: Total time including called functions
   - **Self Weight**: Time spent in that specific function
4. **Look for functions with high percentages** (typically >5-10% of total time)
5. **Expand the call tree** to see the exact file and line numbers

**Identifying bottlenecks:**
```
Example Time Profiler results:

Function                                    Weight    Self Weight
---------------------------------------------------------------
ProductListView.body                        45.2%     2.1%
  └─ ForEach<Product>.init                  43.1%     1.2%
      └─ ProductRowView.init                42.0%     0.8%
          └─ ImageProcessor.loadImage()     38.5%     35.2%  ← BOTTLENECK
              └─ UIImage.resize()           32.1%     32.1%  ← BOTTLENECK
```

**What to look for:**
- Functions with >10% self weight are prime optimization candidates
- Repeated calls to expensive operations (database queries, image processing, JSON parsing)
- Synchronous operations blocking the main thread

### Step 4: Use SwiftUI Instrument for UI Performance Issues

If your issue involves slow SwiftUI view updates, use the specialized SwiftUI Instrument:

1. **Profile again** with **Product > Profile** (`Cmd + I`)
2. **Select the SwiftUI template** from the instrument picker
3. **Record** your slow UI scenario
4. **Examine the Update Groups lane** - this shows SwiftUI view update work
5. **Look for colored markers:**
   - **Orange**: Potentially problematic updates (10-16ms on main thread)
   - **Red**: Likely causing hitches or hangs (>16ms on main thread)
6. **Review the cause-and-effect graph** showing which state changes triggered which view updates
7. **Identify long view body updates** that should be optimized

**Common SwiftUI performance issues:**
- View bodies updating unnecessarily when state hasn't changed
- Expensive computations in view body closures
- Large view hierarchies being regenerated on every update
- Missing view identity causing complete view replacement

### Step 5: Navigate to the Slow Code in Xcode

Jump directly to the problematic code from Instruments:

1. **In Instruments**, double-click on the bottleneck function in the Call Tree
2. **Xcode will open** and navigate to the exact file and line number
3. **Select the entire slow function or code block**:
   - If it's a single function, select from the function signature to the closing brace
   - If it's a code block within a larger function, select just the problematic section
4. **Review the code** to understand what it's doing before asking AI for help

**Example - identifying a slow image processing function:**
```swift
// Xcode navigates to this function (38.5% weight in profiler):
func loadImage(from url: URL) -> UIImage? {
    guard let data = try? Data(contentsOf: url) else { return nil }
    guard let image = UIImage(data: data) else { return nil }

    // Bottleneck: Synchronous resize on main thread (32.1% weight)
    return image.resize(to: CGSize(width: 200, height: 200))
}
```

### Step 6: Open the AI Coding Assistant

Access Xcode's AI-powered Coding Assistant to analyze the slow code:

1. **Open the Coding Assistant panel:**
   - Press `Cmd + Shift + A`, or
   - Navigate to **Editor > Show Coding Assistant**, or
   - Click the Coding Assistant icon in the toolbar
2. **Choose your AI model:**
   - Click the model dropdown at the top of the Coding Assistant panel
   - Select **ChatGPT** (faster, 10-20 seconds) or **Claude Sonnet 4** (more thorough, 30-60 seconds)
   - **Recommendation for optimization:** Claude Sonnet 4 typically provides more comprehensive performance analysis
3. **Ensure you have selected the slow code** in your editor (the AI will use this as context)

### Step 7: Ask AI to Analyze Performance Issues

Craft an effective prompt that includes profiling context:

**Prompt template:**
```
I'm experiencing performance issues in this function. According to Instruments Time Profiler:
- This function takes [X]% of total CPU time
- The specific bottleneck is [specific line/operation] at [Y]% self weight
- The issue occurs when [scenario description]
- Current execution time: [time] (target: [target time])

Please analyze this code and explain:
1. Why is this code slow?
2. What are the specific performance bottlenecks?
3. How can I optimize this for better performance?

[Context if needed: This runs on the main thread / in a background task / etc.]
```

**Real example:**
```
I'm experiencing performance issues in the loadImage function below. According to Instruments:
- loadImage() takes 38.5% of total CPU time
- The UIImage.resize() call takes 32.1% self weight
- This occurs when scrolling through a list with 100+ product images
- Current: Each image takes ~150ms to load (target: <16ms for 60 FPS scrolling)

Please analyze this code and explain:
1. Why is this code slow?
2. What are the specific performance bottlenecks?
3. How can I optimize this for better performance?

Context: This code currently runs synchronously on the main thread when SwiftUI views are created.
```

### Step 8: Review AI's Performance Analysis

The AI will provide detailed analysis of the performance issues:

**Typical AI response structure:**
1. **Root cause identification:** Explains what's making the code slow
2. **Specific bottlenecks:** Points out expensive operations
3. **Impact assessment:** Describes why these bottlenecks matter
4. **Optimization opportunities:** Lists potential improvements

**Example AI analysis (shortened):**
```
The code has several critical performance issues:

1. SYNCHRONOUS NETWORK I/O ON MAIN THREAD
   - `Data(contentsOf: url)` is a blocking network call
   - This freezes the UI for 100-200ms per image
   - Should be async and off the main thread

2. IMMEDIATE IMAGE RESIZING
   - `UIImage.resize()` is computationally expensive
   - Resizing happens before caching, doing duplicate work
   - Should be done once and cached

3. MISSING CACHING
   - Every scroll causes the same images to reload
   - No memory or disk cache for processed images
   - Each image is downloaded and resized repeatedly

4. BLOCKING UI THREAD
   - All work happens synchronously when view appears
   - Causes frame drops and stuttering during scroll
```

### Step 9: Request Optimized Code from AI

Ask the AI to generate an optimized version:

**Follow-up prompt:**
```
Thank you for the analysis. Please provide an optimized version of this code that:
1. Uses async/await for non-blocking execution
2. Moves expensive operations off the main thread
3. Implements appropriate caching
4. Follows Swift 6 concurrency best practices
5. Maintains the same functionality while improving performance

Please include comments explaining the performance improvements.
```

**For SwiftUI-specific optimizations:**
```
Based on the SwiftUI Instrument data showing this view body updates taking 24ms
and updating unnecessarily, please optimize this SwiftUI view to:
1. Minimize view body recomputation
2. Use appropriate property wrappers (@State, @Observable, etc.)
3. Break down large view hierarchies
4. Add view identity where needed
5. Ensure it only updates when relevant state changes
```

### Step 10: Review and Understand the Optimized Code

Carefully examine the AI's proposed optimizations:

1. **Read through the entire optimized code** - don't just copy-paste
2. **Understand each optimization:**
   - Why is it faster than the original?
   - What trade-offs does it make (memory vs speed, complexity vs performance)?
   - Does it maintain the same behavior?
3. **Check for Swift 6 and modern practices:**
   - Uses async/await instead of completion handlers
   - Properly handles actors for thread safety
   - Uses @MainActor where appropriate for UI updates
4. **Look for potential issues:**
   - Does it handle errors properly?
   - Are there edge cases the optimization might miss?
   - Does it fit your app's architecture?

**Example optimized code (from AI):**
```swift
// Optimized version with async/await, caching, and background processing
actor ImageCache {
    private var cache: [URL: UIImage] = [:]

    func image(for url: URL) -> UIImage? {
        cache[url]
    }

    func store(_ image: UIImage, for url: URL) {
        cache[url] = image
    }
}

@MainActor
class ImageLoader: ObservableObject {
    @Published var image: UIImage?
    private let cache = ImageCache()

    // Async, cancellable, cached image loading
    func loadImage(from url: URL) async {
        // Check cache first (fast path)
        if let cached = await cache.image(for: url) {
            self.image = cached
            return
        }

        // Download and process off main thread
        do {
            let (data, _) = try await URLSession.shared.data(from: url)

            // Image processing on background queue
            let processed = await Task.detached {
                guard let image = UIImage(data: data) else { return nil }
                return image.resize(to: CGSize(width: 200, height: 200))
            }.value

            guard let processed else { return }

            // Cache the processed image
            await cache.store(processed, for: url)

            // Update UI on main thread (thanks to @MainActor)
            self.image = processed
        } catch {
            print("Image load failed: \\(error)")
        }
    }
}

// Performance improvements:
// 1. Async/await - non-blocking, doesn't freeze UI (eliminates 150ms main thread block)
// 2. Actor-based caching - thread-safe, prevents duplicate downloads
// 3. Task.detached - image processing on background thread (saves 32% CPU on main)
// 4. @MainActor - ensures UI updates happen on main thread safely
// Estimated improvement: 95% reduction in main thread blocking time
```

### Step 11: Implement the Optimized Code

Apply the optimizations to your project:

1. **Create a backup** or commit your current code first
2. **Replace the slow code** with the optimized version
3. **Resolve any integration issues:**
   - Update call sites if the function signature changed (e.g., synchronous to async)
   - Add necessary imports (e.g., `import SwiftUI` for property wrappers)
   - Adjust property wrappers if view code changed
4. **Build the project** (`Cmd + B`) and fix any compilation errors
5. **Test the functionality** to ensure it still works correctly:
   - Does it display the same results?
   - Are edge cases handled?
   - Do errors show appropriately?

**Common integration steps for async optimizations:**
```swift
// Before (synchronous):
let image = loadImage(from: url)
imageView.image = image

// After (async/await):
Task {
    await imageLoader.loadImage(from: url)
}

// Or in SwiftUI:
.task {
    await imageLoader.loadImage(from: url)
}
```

### Step 12: Verify Performance Improvement with Instruments

Measure the actual performance gains:

1. **Profile again** with **Product > Profile** (`Cmd + I`)
2. **Use the same Instrument** you used in Step 2 (e.g., Time Profiler)
3. **Record the exact same scenario** you profiled originally
4. **Stop recording** and examine the new results
5. **Compare the metrics:**
   - Find the same function in the Call Tree
   - Compare the Weight % before and after
   - Look for reduced CPU spikes in the timeline
   - Check if frame rate improved (for UI issues)

**Creating a comparison:**
```
BEFORE OPTIMIZATION (from Step 3):
Function                        Weight    Self Weight
--------------------------------------------------------
ImageProcessor.loadImage()      38.5%     35.2%
  └─ UIImage.resize()           32.1%     32.1%

AFTER OPTIMIZATION:
Function                        Weight    Self Weight
--------------------------------------------------------
ImageLoader.loadImage()         2.1%      0.3%
  └─ Task.detached              1.8%      0.1%
      └─ UIImage.resize()       1.5%      1.5%

IMPROVEMENT:
- Main thread CPU time: 38.5% → 2.1% (94.5% reduction)
- Blocking resize operations: Moved to background thread
- Frame rate: 20 FPS → 58 FPS (190% improvement)
- User-visible lag: Eliminated
```

### Step 13: Iterate on Remaining Bottlenecks

If performance is still not acceptable, continue the optimization cycle:

1. **Identify the next biggest bottleneck** in the new profiling results
2. **Repeat Steps 5-12** for this new bottleneck
3. **Use AI to analyze** why performance is still not optimal
4. **Ask for more advanced optimizations** if needed:

**Advanced optimization prompts:**
```
The performance improved from 38% to 2% CPU usage, but I still see frame drops.
The SwiftUI Instrument shows the view body is updating 30 times per second.
How can I reduce unnecessary view updates?

The algorithm is now async, but still takes 800ms for large datasets (need <100ms).
Can you suggest a more efficient algorithm or data structure?

Memory usage is now 450MB for this feature (target: <150MB).
How can I optimize memory footprint while maintaining performance?
```

### Step 14: Ask AI About Algorithmic Improvements

For deeper optimizations, consult AI about algorithm and architecture changes:

**Prompt for algorithmic analysis:**
```
This function is still slow despite basic optimizations. Current complexity is O(n²).
Can you:
1. Analyze the time complexity of this algorithm
2. Suggest a more efficient algorithm or data structure
3. Provide an optimized implementation
4. Explain the performance trade-offs

Context: Processing [N] items, currently takes [X]ms, target is [Y]ms
```

**Example - O(n²) to O(n) optimization:**
```swift
// AI might identify a nested loop problem and suggest:

// BEFORE - O(n²): Nested loops checking every pair
for user in users {
    for friend in friends {
        if user.id == friend.userId {
            matches.append((user, friend))
        }
    }
}
// Time for 1000 items: ~850ms

// AFTER - O(n): Dictionary lookup
let friendsByUserId = Dictionary(grouping: friends, by: \\.userId)
for user in users {
    if let userFriends = friendsByUserId[user.id] {
        matches.append(contentsOf: userFriends.map { (user, $0) })
    }
}
// Time for 1000 items: ~12ms (98.6% improvement)
```

### Step 15: Document Your Optimizations

Record what you learned for future reference:

1. **Document the original issue:**
   - What was slow and why
   - Profiling metrics before optimization
2. **Document the solution:**
   - What optimizations were applied
   - Why they work
   - Performance metrics after optimization
3. **Note lessons learned:**
   - Common patterns to avoid
   - Best practices discovered
   - When to apply these optimizations

**Example documentation:**
```swift
// PERFORMANCE NOTE (November 2025):
// Original implementation: Synchronous image loading on main thread
// Issue: 38.5% CPU usage, causing 20 FPS scrolling (target: 60 FPS)
// Solution: Async/await with Task.detached, actor-based caching
// Result: 2.1% CPU usage, 58 FPS scrolling (94.5% improvement)
//
// Lesson: NEVER perform network I/O or image processing on main thread
// Always use async/await and cache results
// Verified with Instruments Time Profiler (Xcode 26.1.1)
```

## Expected Results

After following this workflow, you should achieve:

**Performance Improvements:**
- **Measurable speedup:** 50-95% reduction in CPU time for bottleneck functions
- **Better user experience:** Smoother scrolling, faster load times, more responsive UI
- **Improved frame rates:** UI updates reaching 60 FPS (or 120 FPS on ProMotion displays)
- **Reduced resource usage:** Lower CPU, memory, and power consumption

**Knowledge Gains:**
- **Understanding bottlenecks:** Clear identification of what makes your code slow
- **Profiling skills:** Ability to use Instruments effectively to find performance issues
- **Optimization patterns:** Knowledge of common Swift/SwiftUI optimization techniques
- **AI-assisted workflow:** Efficient process for analyzing and optimizing code with AI

**Typical optimization outcomes:**
- **Synchronous → Async:** 80-95% reduction in main thread blocking time
- **O(n²) → O(n log n) or O(n):** 90-99% improvement for large datasets
- **Missing caching → Cached:** 95-99% improvement for repeated operations
- **Unnecessary view updates → Optimized SwiftUI:** 60-90% reduction in view body computations

## Troubleshooting

### Issue 1: Instruments Shows No Performance Problems

**Problem:** Time Profiler shows all functions at <5% weight, but the app still feels slow.

**Solution:**
- **Try different instruments:** Use SwiftUI Instrument for UI issues, or Hangs instrument for unresponsive UI
- **Check you're profiling the right scenario:** Ensure you're reproducing the exact slow behavior
- **Look at absolute time, not percentages:** Even 3% of 10 seconds is 300ms - still slow
- **Profile on a real device:** Simulator performance is not representative
- **Check the System Trace instrument:** May reveal issues with disk I/O, networking, or system contention
- **Consider power/thermal throttling:** Use Power Profiler to check if device is throttling due to heat

### Issue 2: AI Suggests Optimizations That Don't Compile

**Problem:** The AI's optimized code has compilation errors or doesn't match your Swift version.

**Solution:**
- **Specify your Swift version in the prompt:** "Using Swift 6 with strict concurrency checking enabled"
- **Provide more context:** Include relevant imports, property wrappers, and architectural constraints
- **Ask AI to fix the errors:** Copy the compiler error and ask "How do I fix this error in the optimized code?"
- **Verify the AI understood your code:** The optimization might be based on incorrect assumptions
- **Try a different AI model:** Claude Sonnet 4 typically handles Swift 6 concurrency better than ChatGPT

**Example clarification prompt:**
```
The optimized code has this error: "Actor-isolated property 'cache' cannot be referenced from non-isolated context"
I'm using Swift 6 with strict concurrency checking. How should I fix this?
```

### Issue 3: Optimized Code is Faster But Breaks Functionality

**Problem:** The performance improved, but the feature no longer works correctly.

**Solution:**
- **Identify what broke:** Test edge cases, error scenarios, and boundary conditions
- **Ask AI to fix while maintaining performance:** Describe what broke and ask for a corrected version
- **Review the AI's assumptions:** The optimization might have oversimplified your logic
- **Test incrementally:** Apply optimizations one at a time to identify which change caused the issue
- **Add tests:** Write unit tests before optimizing to catch regressions

**Example prompt:**
```
The optimized code improved performance, but it no longer handles the case when [scenario].
The original code did [expected behavior], but the optimized version does [actual behavior].
Please fix the optimized code to handle this case while maintaining the performance improvement.
```

### Issue 4: Performance Improved But Not Enough

**Problem:** You optimized the code, but it's still not fast enough to meet your goals.

**Solution:**
- **Profile again** to find the new bottleneck (optimization often reveals secondary bottlenecks)
- **Ask AI for more aggressive optimizations:**
  - "This improved from X to Y, but I need Z. What more aggressive optimizations are possible?"
  - Consider algorithmic changes, different data structures, or architectural redesign
- **Question requirements:** Is the performance target realistic? Ask AI "Is [target] achievable for [operation]?"
- **Consider parallelization:** Ask "Can this operation be parallelized across multiple threads/cores?"
- **Look beyond code:** Sometimes infrastructure is the bottleneck (database, network, disk I/O)

**Escalation prompt:**
```
After optimizations, execution time improved from 850ms to 320ms, but my target is <100ms.
Current approach: [describe algorithm/approach]
Dataset size: [N items]
Hardware: [device/platform]

What more aggressive optimizations are possible? Should I consider:
1. A different algorithm or data structure?
2. Parallelization across multiple cores?
3. Restructuring the architecture?
4. Is <100ms even realistic for this operation?
```

### Issue 5: AI Recommends Unsafe Concurrent Code

**Problem:** The AI suggests code that compiles but has data races or thread safety issues.

**Solution:**
- **Enable strict concurrency checking:** In Build Settings, set "Strict Concurrency Checking" to "Complete"
- **Ask AI to use actors:** "Please use Swift actors to ensure thread safety"
- **Verify with Thread Sanitizer:** Profile with the Thread Sanitizer instrument to detect race conditions
- **Request explicit explanations:** Ask "Explain how this code is thread-safe and why there are no data races"
- **Prefer Claude for concurrency:** Claude Sonnet 4 has better understanding of Swift 6 concurrency than ChatGPT

**Clarification prompt:**
```
This code might have thread safety issues. I'm using Swift 6 with strict concurrency checking.
Please rewrite using actors and @MainActor to ensure thread safety, and explain why it's safe.
```

### Issue 6: Can't Reproduce Performance Issues in Instruments

**Problem:** The app feels slow in normal use, but profiling doesn't show the issue.

**Solution:**
- **Profile a longer session:** Record for 30-60 seconds instead of 10 seconds
- **Use the release build:** Always use Product > Profile, not a debug build
- **Check if it's intermittent:** Performance might degrade over time (memory leaks, cache buildup)
- **Test on older/slower devices:** Issue might only appear on less powerful hardware
- **Try different Instruments:**
  - **Allocations:** Memory pressure causing slowdown
  - **Leaks:** Memory leaks leading to eventual slowdown
  - **Activity Monitor:** System-wide resource contention

## Additional Tips

### Profiling Best Practices

**Always profile on real devices:**
- Simulator performance can deviate by 30-50% from actual hardware
- Thermal throttling only happens on real devices
- GPU performance is completely different on simulator

**Use Release builds exclusively:**
- Debug builds include assertions, logging, and optimization barriers
- Release builds match production performance
- Always use **Product > Profile** (`Cmd + I`), never debug builds

**Profile realistic scenarios:**
- Use production-like data volumes (100s or 1000s of items, not 10)
- Test under realistic network conditions (slow 4G, not perfect WiFi)
- Include realistic user interactions (rapid scrolling, multiple taps)

**Establish baselines:**
- Profile before any optimization to have a comparison point
- Save Instruments traces for later comparison
- Document exact test scenarios for reproducibility

### When to Use Each Instrument

**Time Profiler:**
- CPU-bound performance issues (algorithms, computation)
- General-purpose starting point for optimization
- Identifying hot functions and call stacks

**SwiftUI Instrument:**
- Slow SwiftUI view updates or animations
- Identifying unnecessary view body recomputation
- Understanding state change propagation

**Allocations:**
- High memory usage or memory growth over time
- Identifying large object allocations
- Finding memory-related performance issues

**Leaks:**
- Memory leaks causing slowdown over time
- Retain cycles preventing deallocation
- Growing memory footprint

**Power Profiler:**
- Battery drain issues
- Thermal problems (device getting hot)
- Understanding system-level resource impact

**System Trace:**
- Complex multi-threaded performance issues
- File I/O and network bottlenecks
- System contention and thread scheduling

### AI Model Selection for Optimization

**Use ChatGPT when:**
- You need quick optimization suggestions (10-20 second responses)
- The bottleneck is straightforward (obvious O(n²) loop, missing cache, etc.)
- You want concise, actionable recommendations
- You're optimizing common patterns (list rendering, image loading, JSON parsing)

**Use Claude Sonnet 4 when:**
- You need deep algorithmic analysis and suggestions
- The optimization requires architectural changes
- You're working with complex concurrency or thread safety issues
- You want detailed explanations of trade-offs
- The codebase is large and needs extensive context (Claude has 200K token context)
- You need Swift 6 strict concurrency expertise

### Effective Optimization Prompts

**Include profiling metrics:**
```
Good: "This function takes 38.5% CPU time according to Time Profiler, with 32.1%
      self weight in the UIImage.resize() call. How can I optimize it?"

Poor: "This function is slow. Make it faster."
```

**Specify performance targets:**
```
Good: "Current: 850ms for 1000 items. Target: <100ms. How can I achieve this?"

Poor: "Make this faster."
```

**Provide context about constraints:**
```
Good: "This runs on the main thread in a SwiftUI view body and must stay under 16ms
      for 60 FPS. Current: 42ms causing frame drops."

Poor: "Optimize this view."
```

**Request explanations, not just code:**
```
Good: "Explain why this is slow, what the bottleneck is, and how your optimization
      improves performance."

Poor: "Give me faster code."
```

### Common Performance Patterns to Avoid

**Main thread blocking:**
- NEVER do network I/O on the main thread
- NEVER do file I/O on the main thread
- NEVER do heavy computation (image processing, JSON parsing, encryption) on main thread
- Use async/await and Task.detached for background work

**Algorithmic inefficiency:**
- Watch for O(n²) nested loops (especially in view bodies)
- Use dictionaries/sets for lookups instead of array.contains() in loops
- Consider lazy evaluation for expensive computations

**Unnecessary work:**
- Cache results of expensive operations
- Avoid recomputing the same values repeatedly
- Use lazy loading for resources not immediately needed

**SwiftUI-specific issues:**
- Expensive computations in view body closures
- Missing view identity causing complete re-creation
- Over-use of @Published triggering unnecessary updates
- Large view hierarchies without proper decomposition

### Optimization Priority Guidelines

**Optimize in this order:**
1. **Fix obvious mistakes first:** Main thread blocking, O(n²) where O(n) is trivial, missing caching
2. **Profile to find the biggest bottleneck:** Focus on functions with >10% weight
3. **Optimize the bottleneck:** One at a time, measure results
4. **Repeat until performance is acceptable:** Continue with next-biggest bottleneck
5. **Stop when good enough:** Don't over-optimize - "fast enough" is sufficient

**Don't optimize prematurely:**
- Profile first, optimize second (never guess at performance problems)
- Optimize only what profiling shows is slow
- Maintain code clarity unless performance demands otherwise

### Measuring Real-World Impact

**Beyond Instruments metrics:**
- **User-perceived performance:** Does the app feel faster to users?
- **Battery life:** Does optimization reduce power consumption?
- **Thermal performance:** Does the device stay cooler?
- **App Store ratings:** Do performance improvements impact reviews?

**Track metrics over time:**
- Create benchmarks for critical operations
- Run automated performance tests in CI/CD
- Monitor production performance with analytics

### Swift 6 Optimization Opportunities

**Leverage modern concurrency:**
- Use async/await for natural async code (replaces completion handlers)
- Use actors for thread-safe state management (eliminates locks/queues)
- Use @MainActor for UI code (compiler-enforced main thread safety)
- Use TaskGroup for parallel work (efficient multi-core utilization)

**Performance-focused Swift 6 features:**
- Strict concurrency checking catches data races at compile time
- Embedded Swift for ultra-low-overhead code
- Improved optimization for generic code
- Better inlining and whole-module optimization

### When AI Optimization Isn't Enough

**Some problems require human expertise:**
- **Architectural issues:** Fundamental design problems AI can't fix
- **Domain-specific algorithms:** Specialized techniques AI might not know
- **Hardware-specific optimization:** GPU shaders, Metal performance, SIMD
- **Distributed systems:** Backend/frontend co-optimization

**Consider consulting:**
- Apple's performance documentation and WWDC sessions
- Swift performance guides and best practices
- Domain experts for specialized algorithms
- Platform engineers for low-level optimization

## Related Articles

- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT
- KB-032: How to start a conversation with ChatGPT in Xcode
- KB-039: How to use ChatGPT to fix errors and bugs in your code
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-047: How to use Claude for complex refactoring tasks
- KB-048: How to leverage Claude's context window for large code reviews
- KB-050: How to compare responses between ChatGPT and Claude for the same coding task

## Sources

- 9to5Mac: Apple releases Xcode 26.1.1 with coding intelligence improvements - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- 200OK Solutions: What's New in Xcode 26: Smarter, Faster, More Powerful — WWDC 2025 Highlights - https://200oksolutions.com/blog/whats-new-in-xcode-26-smarter-faster-more-powerful-wwdc-2025-highlights/ (Accessed: November 17, 2025)
- Medium (Sachin Siwal): Code with Intelligence — xCode 26 - https://medium.com/@sachinsiwal/code-with-intelligence-xcode-26-9203a8ce665e (Accessed: November 17, 2025)
- Medium (Max): Xcode 26 Beta Arrives with a Focus on AI, Performance, and Enhanced Debugging - https://medium.com/@csmax/xcode-26-beta-arrives-with-a-focus-on-ai-performance-and-enhanced-debugging-d9ebb47ac232 (Accessed: November 17, 2025)
- Medium (Taoufiq El Moutaouakil): iOS 26 WWDC 2025: Complete Developer Guide to New Features, Performance Optimization & AI Integration - https://medium.com/@taoufiq.moutaouakil/ios-26-wwdc-2025-complete-developer-guide-to-new-features-performance-optimization-ai-5b0494b7543d (Accessed: November 17, 2025)
- Medium (Sree Charan): Xcode 26's Instruments Gets a Major Overhaul - https://medium.com/@sree.charan/xcode-26s-instruments-gets-a-major-overhaul-2e18c58f5513 (Accessed: November 17, 2025)
- DEV Community (AETechPro): WWDC 2025 - Optimize SwiftUI performance with Instruments - https://dev.to/softwaretechpro/wwdc-2025-optimize-swiftui-performance-with-instruments-4o4j (Accessed: November 17, 2025)
- DEV Community (ArshTechPro): WWDC 2025 - What's new in Xcode 26 - https://dev.to/arshtechpro/wwdc-2025-whats-new-in-xcode-26-3oo3 (Accessed: November 17, 2025)
- Medium (Aditi More): Xcode 26 Is Here: 10 Features That Will Redefine iOS Development - https://medium.com/@Aditi.iOS/xcode-26-is-here-10-features-that-will-redefine-ios-development-741195d9260e (Accessed: November 17, 2025)
- Medium (Gaurav Parmar): What's New in Xcode 26 — A Developer's Guide to Faster iOS Development - https://medium.com/@gauravios/whats-new-in-xcode-26-a-developer-s-guide-to-faster-ios-development-dd23d5c8563f (Accessed: November 17, 2025)
- Avanderlee: Xcode Instruments usage to improve app performance - https://www.avanderlee.com/debugging/xcode-instruments-time-profiler/ (Accessed: November 17, 2025)
- Velotio Engineering: Optimizing iOS Memory Usage with Instruments Xcode Tool - https://www.velotio.com/engineering-blog/optimizing-ios-memory-usage-with-instruments-xcode-tool (Accessed: November 17, 2025)
- One Click IT Solution: App Performance Optimization: Using Xcode Instruments for Memory & CPU Efficiency - https://www.oneclickitsolution.com/centerofexcellence/ios/app-performance-excellence-leveraging-instruments-for-memory-and-cpu-optimization (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
