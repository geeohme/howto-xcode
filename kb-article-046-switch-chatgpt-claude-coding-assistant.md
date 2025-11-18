# How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant

**Article ID:** KB-046
**Difficulty:** Beginner-Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 2-5 minutes

## Overview
Xcode 26.1+ allows you to use multiple AI model providers in the Coding Assistant, including both ChatGPT (built-in) and Claude Sonnet 4 (via Anthropic). Switching between models lets you leverage the unique strengths of each AI assistant—ChatGPT for general coding tasks and Claude for complex refactoring, deep analysis, and nuanced problem-solving. This guide shows you how to switch between these models when starting conversations.

## Prerequisites
- Xcode 26.1 or later installed
- macOS 15 (Sequoia) or later
- ChatGPT configured (enabled by default in Xcode 26.1+)
- Claude configured as a model provider (see KB-043: How to add Claude as a model provider in Xcode Intelligence settings)
- Active internet connection
- Valid API keys for Anthropic (if using Claude)

## Steps

### Step 1: Open the Coding Assistant Panel
Press **Command+0** (⌘+0) to open the Coding Assistant panel. The panel appears on the right side of your Xcode window.

```
Keyboard shortcut: ⌘+0
```

Alternatively, you can access it via the menu:
- Navigate to **Window > Coding Assistant**
- Or click the **Coding Assistant** button in the toolbar (if visible)

### Step 2: Start a New Conversation
Click the **"New Conversation"** button at the top of the Coding Assistant panel. This is essential because model selection happens when you create a new conversation—you cannot switch models mid-conversation.

**Important:** Each conversation is tied to a single model provider. To use a different model, you must start a new conversation.

### Step 3: Select Your Preferred Model Provider
When starting a new conversation, a model selector dropdown appears near the top of the Coding Assistant panel. This dropdown displays all configured model providers.

Click the dropdown menu to see available options:
- **ChatGPT** - OpenAI's models (GPT-4.1, GPT-5, GPT-5 Reasoning)
- **Anthropic** - Claude models (Claude Sonnet 4, Claude Opus, etc.)
- Any other custom providers you've configured

### Step 4: Choose the Specific Model
After selecting a provider (ChatGPT or Anthropic), you'll see a list of available models from that provider:

**For ChatGPT:**
- GPT-4.1 - Standard model for most coding tasks
- GPT-5 - Latest model optimized for quick responses
- GPT-5 (Reasoning) - Advanced model for complex problem-solving

**For Anthropic (Claude):**
- Claude Sonnet 4 - Balanced model for most tasks (recommended)
- Claude Opus - Most capable model for complex reasoning
- Other Claude models tied to your API key

Select your desired model by clicking on it.

### Step 5: Mark Favorites for Quick Access (Optional)
To streamline future model selection, you can mark frequently-used models as favorites:

1. In the model dropdown, hover over your preferred model
2. Click the **star icon** or **favorite button** next to the model name
3. Favorited models appear at the top of the list for quicker access

This is particularly useful if you regularly switch between ChatGPT and Claude Sonnet 4.

### Step 6: Start Your Conversation
Once you've selected your model, the conversation interface activates. Type your prompt in the text field and press Enter. The selected AI model (ChatGPT or Claude) will respond based on your query.

The model indicator typically shows which AI is currently active in the conversation header.

### Step 7: Switch Models by Starting a New Conversation
To switch from ChatGPT to Claude (or vice versa) during your work session:

1. Click **"New Conversation"** again
2. Select the different model provider from the dropdown
3. Choose the specific model you want
4. Continue with your new conversation

Your previous conversations remain accessible in the conversation history sidebar.

## Expected Results
After following these steps, you should be able to:

- Successfully switch between ChatGPT and Claude Sonnet 4 when starting new conversations
- See the active model name displayed in the Coding Assistant panel header
- Receive responses from the selected AI model (ChatGPT or Claude)
- Access conversation history showing which model was used for each conversation
- Quickly access favorited models at the top of the model selector dropdown
- Experience different AI personalities and capabilities depending on which model you select

## Troubleshooting

### Common Issue 1: Claude (Anthropic) Not Appearing in Model List
**Problem:** When you click the model dropdown, only ChatGPT appears—no Anthropic or Claude options.
**Solution:**
- Verify you've added Anthropic as a model provider in **Xcode > Settings > Intelligence > Add a Model Provider**
- Check that your Anthropic API key is correctly configured with the URL `https://api.anthropic.com/` and header `x-api-key`
- Restart Xcode after adding the provider—sometimes new providers require a restart to appear
- Ensure your API key is valid and has access to Claude models by testing it at console.anthropic.com

### Common Issue 2: Cannot Switch Models Mid-Conversation
**Problem:** You want to switch from ChatGPT to Claude while in an active conversation, but there's no way to change models.
**Solution:** This is expected behavior. Model selection is locked once a conversation starts. To switch models, click **"New Conversation"** and select a different model provider. Your previous conversation remains accessible in the history.

### Common Issue 3: Model Performance or Response Issues
**Problem:** After switching to Claude, responses are slow or you encounter errors.
**Solution:**
- Check your internet connection stability
- Verify your Anthropic API key has remaining quota at console.anthropic.com/settings/limits
- Update to Xcode 26.1.1 or later, which includes important Coding Assistant performance improvements
- Try selecting a different Claude model (e.g., switch from Opus to Sonnet 4)
- Clear the conversation and start fresh if responses become inconsistent

### Common Issue 4: Favorites Not Saving
**Problem:** You mark models as favorites, but they don't appear at the top of the list after restarting Xcode.
**Solution:**
- Ensure you're running Xcode 26.1.1 or later, which has improved favorites persistence
- Try re-favoriting the models after verifying your Xcode version
- Check that Xcode preferences are being saved correctly (not running with restricted permissions)

## Additional Tips

### When to Use ChatGPT vs Claude
**Use ChatGPT when:**
- You need quick code generation for standard tasks
- Working with Apple-specific frameworks (iOS, macOS APIs)
- Generating unit tests or documentation
- Debugging common Swift errors
- Performing straightforward refactoring

**Use Claude Sonnet 4 when:**
- Tackling complex architectural decisions
- Performing large-scale refactoring across multiple files
- Analyzing nuanced code logic or edge cases
- Working on algorithm optimization
- Explaining complex codebases or legacy code
- Handling tasks requiring deep contextual understanding

### Optimize Your Workflow
- **Keep both models active:** Start one conversation with ChatGPT and another with Claude to compare responses for the same coding task
- **Use @-mentions:** Reference specific files using `@filename` syntax to provide better context to either model
- **Experiment with models:** Different tasks may benefit from different models—try both to see which produces better results for your use case
- **Monitor API usage:** If using paid API keys for Claude, monitor your usage at console.anthropic.com to avoid unexpected costs

### Performance Considerations
- Very large projects with extensive git histories may experience slower model switching or response times
- Claude models may have different rate limits than ChatGPT depending on your API tier
- Xcode 26.1.1 includes memory usage improvements for the Coding Assistant, especially with large repositories

## Related Articles
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-044: How to obtain and configure an Anthropic API key for Claude
- KB-045: How to verify Claude is working correctly in Xcode
- KB-047: How to use Claude for complex refactoring tasks
- KB-050: How to compare responses between ChatGPT and Claude for the same coding task
- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT
- KB-032: How to start a new conversation with ChatGPT in Xcode

## Sources
- Apple Developer Documentation - Writing code with intelligence in Xcode - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- 9to5Mac - "Xcode 26 will support multiple AI models, like Claude" - https://9to5mac.com/2025/06/10/beyond-chatgpt-xcode-26-will-support-multiple-ai-models-like-claude/ (Accessed: November 17, 2025)
- 9to5Mac - "Apple releases Xcode 26.1.1 with coding intelligence improvements" - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- TechCrunch - "Apple brings ChatGPT and other AI models to Xcode" - https://techcrunch.com/2025/06/09/apple-brings-chatgpt-and-other-ai-models-to-xcode/ (Accessed: November 17, 2025)
- Simon B. Stöckli - "Using Claude with Coding Assistant in Xcode 26" - https://simonbs.dev/posts/using-claude-with-coding-assistant-in-xcode-26/ (Accessed: November 17, 2025)
- Wendy Liga - "Use Custom Models in the New Xcode 26 Intelligence" - https://wendyliga.com/blog/xcode-26-custom-model/ (Accessed: November 17, 2025)
- Ronnie Rocha - "Beyond ChatGPT: How to Use Claude and Gemini in Xcode Coding Intelligence" - https://ronnierocha.dev/blog/beyond-chatgpt-how-to-use-claude-and-gemini-in-xcode-coding-intelligence/ (Accessed: November 17, 2025)
- OpenRouter Documentation - "Xcode Integration" - https://openrouter.ai/docs/community/xcode (Accessed: November 17, 2025)
- MacTrast - "Xcode 26.1.1 Update Includes Coding Intelligence Improvements" - https://www.mactrast.com/2025/11/xcode-26-1-1-update-includes-coding-intelligence-improvements/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
