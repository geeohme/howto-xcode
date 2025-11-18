# How to ask ChatGPT to explain selected code in your project

**Article ID:** KB-038
**Difficulty:** Beginner-Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 5-10 minutes

## Overview

Xcode 26.1+ includes built-in integration with ChatGPT and other AI models, allowing you to quickly ask for explanations of code directly within your project. This feature helps you understand complex code snippets, debugging errors, and unfamiliar patterns without leaving your IDE.

## Prerequisites

- Xcode version 26.1 or later installed on your Mac
- macOS 15 or later (macOS 26 Tahoe recommended for full Coding Intelligence features)
- A valid OpenAI account (free or ChatGPT Plus subscription supported)
- Active internet connection for AI requests

## Steps

### Step 1: Enable Coding Intelligence in Xcode Settings

First, ensure that Xcode's AI features are enabled:

1. Open **Xcode**
2. Navigate to **Xcode > Settings** (or press `Cmd + ,`)
3. Select the **Accounts** tab
4. If prompted, sign in with your Apple ID
5. Ensure that **Coding Intelligence** is toggled on
6. If you have a ChatGPT account, click **Connect ChatGPT Account** to link your OpenAI credentials for higher rate limits

### Step 2: Select the Code You Want Explained

Open the file containing the code you want explained:

1. Navigate to the code section in your editor
2. Click and drag to select the code block you want explained, or
3. Triple-click to select an entire line/function, or
4. Use `Cmd + A` to select all code in the file (if explaining the entire file)

Example code to select:

```swift
func calculateTotal(items: [Double]) -> Double {
    return items.reduce(0) { $0 + $1 }
}
```

### Step 3: Open the Coding Tools Panel

With your code selected, trigger the Coding Tools:

1. Look for the **AI sparkles icon** (✨) in the editor gutter next to your selection, or
2. Right-click on the selected code to open the context menu, or
3. Press **Cmd + Shift + A** to open the AI Assistant shortcut

This will display the Coding Tools panel on the right side of your editor.

### Step 4: Click "Explain" in the Coding Tools

In the Coding Tools panel that appears:

1. Look for the **"Explain"** button or action
2. Click on it to send your selected code to ChatGPT
3. Wait a moment for ChatGPT to process your request

### Step 5: Review the Explanation

ChatGPT will generate an explanation of your selected code:

1. The explanation will appear in the Coding Tools panel
2. Read through the explanation to understand what the code does
3. You can highlight different sections of code and click **Explain** again for additional context

## Expected Results

After clicking "Explain," you should see:

- A clear, human-readable explanation of what the selected code does
- Breaking down of individual lines or code sections (depending on complexity)
- Explanation of any functions, methods, or logic used
- Information about the purpose and output of the code
- Suggestions for improvements or alternative approaches (sometimes)

Example: If you explain the `calculateTotal` function above, you might receive: "This function takes an array of numbers and uses the `reduce` method to sum them all together, returning the total. It starts with an initial value of 0 and adds each item to the running total."

## Troubleshooting

### Issue 1: "Coding Tools panel doesn't appear"

**Problem:** When you select code, the AI sparkles icon doesn't appear or the Coding Tools panel won't open.

**Solution:**
- Verify that Coding Intelligence is enabled in Xcode Settings
- Check your macOS version (requires macOS 15 or later)
- Ensure your ChatGPT account is properly connected in Xcode Settings
- Restart Xcode and try again
- Make sure you have an active internet connection

### Issue 2: "ChatGPT rate limit exceeded"

**Problem:** You receive a message that your ChatGPT request quota has been exceeded.

**Solution:**
- If using the free ChatGPT tier, switch to ChatGPT Plus for higher rate limits
- Connect your ChatGPT Plus account in Xcode Settings > Accounts
- Wait and retry after some time has passed
- Use the built-in Xcode Code Completion instead of ChatGPT explanation temporarily

### Issue 3: "Xcode says 'No network connection'"

**Problem:** The Coding Tools indicate you don't have internet access.

**Solution:**
- Check your Wi-Fi or network connection
- Temporarily disable VPN if one is active
- Check your Mac's firewall settings to ensure Xcode can access the internet
- Restart Xcode

## Additional Tips

- **Be Specific:** Select only the code you want explained; selecting too much code may result in a lengthy or overwhelming explanation
- **Use for Unfamiliar Code:** This feature is especially helpful when working with code from other developers or learning new frameworks
- **Ask Follow-up Questions:** After the initial explanation, you can ask follow-up questions using the Chat feature to dive deeper
- **Explain Complex Algorithms:** Use this feature to understand sorting algorithms, recursion, or other complex logic patterns
- **Learning Tool:** This is an excellent way to learn and understand new programming concepts without needing external documentation
- **Review Before Trusting:** While ChatGPT explanations are usually accurate, always review the explanation and verify against official documentation for critical code

## Related Articles

- [How to use ChatGPT to generate code in Xcode](#) (forthcoming)
- [How to ask ChatGPT to fix code errors](#) (forthcoming)
- [Understanding Xcode 26.1+ Coding Intelligence Features](#) (forthcoming)

## Sources

- Apple Developer Documentation - Writing code with intelligence in Xcode - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- TechCrunch - Apple brings ChatGPT and other AI models to Xcode - https://techcrunch.com/2025/06/09/apple-brings-chatgpt-and-other-ai-models-to-xcode/ (Accessed: November 17, 2025)
- 9to5Mac - Apple releases Xcode 26.1.1 with coding intelligence improvements - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- Swift with Vincent - Discover 5 new AI features of Xcode 26 - https://www.swiftwithvincent.com/blog/discover-5-new-ai-features-of-xcode-26 (Accessed: November 17, 2025)
- Backtosoftwaredevelopment - Using ChatGPT alongside Xcode - https://backtosoftwaredevelopment.substack.com/p/using-chatgpt-alongside-xcode (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
