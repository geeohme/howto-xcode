# How to start a new conversation with ChatGPT in Xcode

**Article ID:** KB-032
**Difficulty:** Beginner-Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 2-5 minutes

## Overview
Xcode 26.1+ includes integrated ChatGPT support through the Coding Assistant panel, allowing you to have AI-powered conversations directly within your development environment. This guide walks you through starting a new conversation, enabling ChatGPT, and configuring your preferences for optimal coding assistance.

## Prerequisites
- Xcode 26.1 or later installed on your Mac
- macOS 15 (Sequoia) or later
- (Optional) An OpenAI account or ChatGPT Plus subscription for increased usage limits
- An active internet connection

## Steps

### Step 1: Open the Coding Assistant Panel
Press **Command+0** (⌘+0) on your keyboard to open the Coding Assistant panel. This panel typically appears on the right side of your Xcode window. If you prefer using the menu, you can also navigate to **Window > Coding Assistant** to access it.

```
Keyboard shortcut: ⌘+0
```

If the panel doesn't appear, verify that Coding Intelligence is enabled in your Xcode preferences (see Step 4).

### Step 2: Click "New Conversation"
Once the Coding Assistant panel is open, look for the **"New Conversation"** button or text field at the top of the panel. Click this button to create a fresh conversation session with ChatGPT. Each conversation is isolated and independent—conversations started in Xcode are not shared with the ChatGPT web interface or the ChatGPT macOS app.

### Step 3: Select ChatGPT as Your Model Provider
A dropdown menu or model selector will appear showing available AI providers. By default, ChatGPT is the primary option. Select **ChatGPT** from the available models. If you have multiple models configured, you can switch between them before starting a new conversation.

For more advanced options:
- **GPT-4.1** - Standard model for most coding tasks
- **GPT-5** - Latest model optimized for quick, high-quality results
- **GPT-5 (Reasoning)** - Advanced model for complex problems requiring deeper analysis

### Step 4: Start Typing Your Question
Begin typing your question or prompt in the text field. You can ask ChatGPT to:
- Explain code concepts
- Generate new code
- Debug existing code
- Create unit tests
- Write documentation
- Refactor code

You can include selected code from your editor as context, and Xcode will automatically provide relevant file context to ChatGPT.

### Step 5: Configure ChatGPT Account Settings (Optional)
To increase your daily request limits, connect your ChatGPT account:

1. Open **Xcode > Preferences** (or **Xcode > Settings** on newer versions)
2. Navigate to the **Intelligence** tab
3. Locate the ChatGPT configuration section
4. Click the login option or account settings
5. Enter your OpenAI credentials or ChatGPT Plus account information
6. Authorize Xcode to access your account

You can use Xcode with ChatGPT without creating an account, but you'll have a daily request cap. ChatGPT Plus subscribers enjoy higher limits.

## Expected Results
After starting a new conversation:

- The ChatGPT conversation interface appears in the Coding Assistant panel
- You can type questions and receive AI-powered responses within seconds
- ChatGPT will analyze your code context and provide relevant suggestions
- Previous conversation messages appear in the conversation history
- You can continue adding messages to build on the conversation
- The conversation thread remains active until you start a new conversation or close the panel

## Troubleshooting

### Common Issue 1: Coding Assistant Panel Not Opening
**Problem:** Pressing ⌘+0 doesn't open the Coding Assistant panel, or the panel appears empty.
**Solution:** First, verify that Coding Intelligence is enabled by going to **Xcode > Preferences > Intelligence** and checking that the intelligence features are turned on. Ensure you're running Xcode 26.1 or later and macOS 15+. If still not working, try restarting Xcode or your Mac.

### Common Issue 2: ChatGPT Model Not Available
**Problem:** ChatGPT doesn't appear in the model selector dropdown.
**Solution:** ChatGPT should be available by default in Xcode 26.1+. If missing, go to **Xcode > Preferences > Intelligence** and verify that ChatGPT is listed. You may need to update Xcode to the latest version. Check that your internet connection is active.

### Common Issue 3: Daily Request Limit Reached
**Problem:** You see a message indicating you've exceeded your daily request limit.
**Solution:** If you're using Xcode without a ChatGPT account, you have a limited number of free requests per day. To increase your limit, log in with your OpenAI account or ChatGPT Plus subscription through **Preferences > Intelligence**. Alternatively, wait until the next day for your free limit to reset.

### Common Issue 4: Conversation Not Saving or Disappearing
**Problem:** Your conversation history is not persisting between sessions.
**Solution:** This is expected behavior. Xcode conversations are not synchronized with the ChatGPT web app or saved across Xcode sessions by default. If you need to preserve important conversations, copy and paste the content to a file or document before closing the conversation.

## Additional Tips
- **Use Code Context:** Select code in your editor before opening the Coding Assistant to provide ChatGPT with relevant context for better responses
- **Follow Up Questions:** Continue the conversation by typing follow-up questions in the same conversation thread rather than creating new conversations each time
- **Hidden System Prompt:** Xcode injects a hidden system prompt before each ChatGPT conversation that includes guidelines to assume Swift unless specified otherwise, and to use official Apple platform names (iOS, iPadOS, macOS, watchOS, visionOS)
- **Multiple Models:** You can add other AI providers like Claude or Gemini through **Preferences > Intelligence** and toggle between them when starting new conversations
- **Agent Mode:** If enabled in your Xcode version, Agent Mode allows ChatGPT to interact with your codebase more directly (creating, editing, or removing files) under your control

## Related Articles
- How to enable and configure Apple Intelligence in Xcode (Coming Soon)
- How to add Claude or other AI models to Xcode (Coming Soon)
- How to use ChatGPT for code generation in Xcode (Coming Soon)

## Sources
- Apple Developer Documentation - Writing code with intelligence in Xcode - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- Apple Developer Forums - Xcode Intelligence and ChatGPT discussions - https://developer.apple.com/forums/ (Accessed: November 17, 2025)
- 9to5Mac - "Apple releases Xcode 26.1.1 with coding intelligence improvements" - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- Swift with Vincent - "ChatGPT in Xcode 26: there's a hidden prompt!" - https://www.swiftwithvincent.com/blog/chatgpt-in-xcode-26-theres-a-hidden-prompt (Accessed: November 17, 2025)
- TechCrunch - "Apple brings ChatGPT and other AI models to Xcode" - https://techcrunch.com/2025/06/09/apple-brings-chatgpt-and-other-ai-models-to-xcode/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
