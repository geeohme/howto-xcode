# How to add Claude as a model provider in Xcode Intelligence settings

**Article ID:** KB-043
**Difficulty:** Beginner-Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 10-15 minutes

## Overview
Xcode 26.1+ supports custom AI model providers beyond the default ChatGPT integration, allowing you to connect Anthropic's Claude models to power your Coding Assistant and Intelligence features. This guide walks you through adding Claude as a model provider using an Anthropic API key, enabling you to leverage Claude Sonnet 4's powerful code generation and refactoring capabilities directly within Xcode.

## Prerequisites
- Xcode 26.1 or later installed (version 26.1.1+ recommended)
- macOS Sequoia 15.5+ or later
- An Anthropic API account with an active API key (see KB-042: How to generate an API key for Claude in the Anthropic console)
- Internet connection
- Note: This requires an Anthropic API key (paid service), which is separate from Claude Pro/Max subscription plans

## Steps

### Step 1: Generate Your Anthropic API Key
Before configuring Xcode, obtain your API key from Anthropic:

1. Navigate to [https://console.anthropic.com/settings/keys](https://console.anthropic.com/settings/keys) in your web browser
2. Sign in with your Anthropic account credentials
3. Click **"Create Key"** or **"+ Create Key"** button
4. Give your key a descriptive name (optional, e.g., "Xcode Development")
5. Click **"Create"** to generate the key
6. **Immediately copy** the API key to your clipboard and store it securely (you'll only see it once)

**Important:** Keep your API key confidential—treat it like a password and never commit it to version control.

### Step 2: Open Xcode Intelligence Settings
Access the Intelligence configuration panel:

1. Launch **Xcode 26.1** or later
2. Click **Xcode** in the menu bar (top-left corner)
3. Select **Settings** (or **Preferences** on older macOS versions)
4. Click the **Intelligence** tab at the top of the Settings window

You should see the Intelligence settings panel with existing model providers (ChatGPT is typically listed by default).

### Step 3: Add Anthropic as a Model Provider
Configure Claude/Anthropic as a new model provider:

1. In the Intelligence settings panel, click the **"+"** button or **"Add a Model Provider"** option
2. Select **"Internet Hosted"** when prompted for provider type
3. You'll see a configuration form with the following fields:

   **Provider Name/Label:** Anthropic (or Claude)

   **API Endpoint URL:**
   ```
   https://api.anthropic.com/
   ```

   **API Key Header:**
   ```
   x-api-key
   ```

   **API Key:**
   Paste the API key you copied from the Anthropic console in Step 1

4. Click **"Add"** or **"Continue"** to save the configuration

**Note:** The API key header `x-api-key` is critical—this is Anthropic's specific authentication format and differs from OpenAI's authorization method.

### Step 4: Restart Xcode (If Necessary)
To ensure the new model provider is recognized:

1. **Save all your work** in Xcode
2. Quit Xcode completely (Xcode > Quit Xcode or ⌘Q)
3. Relaunch Xcode from your Applications folder or Dock

**Important:** You may need to restart Xcode before the Anthropic model provider shows up in your available models list. This is a known behavior when adding new custom providers.

### Step 5: Verify Claude Models Are Available
Confirm that Anthropic's Claude models are now accessible:

1. Open the **Coding Assistant** panel in Xcode (press **⌘+0** or go to **View** > **Panels** > **Coding Assistant**)
2. Click **"New Conversation"** or the **+** button to start a new chat
3. Look for the model selector dropdown (typically at the top of the Coding Assistant panel)
4. Click the dropdown to view available models
5. You should now see **"Anthropic"** as an option with available Claude models, including:
   - **Claude Sonnet 4** (recommended for most coding tasks)
   - **Claude Opus** (if available with your API tier)
   - Other Claude model variants based on your API access

### Step 6: Select and Test Claude
Test your Claude integration:

1. From the model selector dropdown, choose **Anthropic > Claude Sonnet 4** (or your preferred Claude model)
2. In the Coding Assistant text field, type a test prompt such as:
   ```
   Create a simple SwiftUI view with a button that increments a counter
   ```
3. Press **Enter** or click the send button
4. Wait for Claude to generate a response
5. Review the generated code—Claude should provide clear, well-structured SwiftUI code

If you receive a response from Claude, your integration is successful!

## Expected Results
After completing these steps, you should:

- See "Anthropic" listed as an available model provider in your Intelligence settings
- Be able to select Claude models (Claude Sonnet 4, Opus, etc.) from the Coding Assistant's model dropdown
- Receive AI-generated code suggestions and assistance powered by Claude
- Experience Claude's strengths in code generation, refactoring, and handling long context windows
- Have access to Claude's context-aware code edits and conservative instruction-following
- Be able to switch between ChatGPT and Claude models as needed for different tasks

## Troubleshooting

### Issue 1: Model Provider Not Showing After Adding
**Problem:** You added the Anthropic configuration but don't see it in the model list.

**Solution:**
- Restart Xcode completely (this is often required for new providers)
- Verify the API endpoint URL is exactly `https://api.anthropic.com/` (include the trailing slash)
- Check that the API key header is `x-api-key` (case-sensitive, all lowercase)
- Remove and re-add the provider if the issue persists
- Ensure you're running Xcode 26.1 or later (check Xcode > About Xcode)

### Issue 2: Authentication or API Key Errors
**Problem:** You see "Invalid API Key" or authentication failed errors when trying to use Claude.

**Solution:**
- Verify you copied the entire API key correctly from console.anthropic.com
- Check that your Anthropic account has active API credits or billing set up at [https://console.anthropic.com/settings/billing](https://console.anthropic.com/settings/billing)
- Ensure you're using an API key (not a Claude Pro/Max subscription login)
- Try regenerating a new API key from the Anthropic console and updating Xcode
- Confirm the API key header is set to `x-api-key` (not "Authorization" or other headers)

### Issue 3: "Could Not Connect to Model Provider" Error
**Problem:** Xcode can't establish a connection to Anthropic's API.

**Solution:**
- Check your internet connection
- Verify the API endpoint URL is correct: `https://api.anthropic.com/`
- Check if there are any firewall or VPN settings blocking the connection
- Visit [https://status.anthropic.com/](https://status.anthropic.com/) to check if Anthropic's API service is operational
- Try accessing console.anthropic.com in your browser to verify connectivity

### Issue 4: Models Not Appearing in Dropdown
**Problem:** Anthropic provider is added but no Claude models show in the model selector.

**Solution:**
- Restart Xcode (this is the most common fix)
- Verify your API key is valid by testing it in the Anthropic console or using a test API call
- Check your Anthropic account tier—some models may require specific API access levels
- Remove the provider and add it again with fresh credentials
- Clear Xcode's cache: Quit Xcode, delete `~/Library/Caches/com.apple.dt.Xcode`, and relaunch

## Additional Tips
- **Cost Awareness:** Claude models use API credits based on token usage. Monitor your consumption at [https://console.anthropic.com/settings/billing](https://console.anthropic.com/settings/billing) to avoid unexpected costs.
- **Model Strengths:** Claude Sonnet 4 excels at code generation, refactoring, and handling long context windows. Claude Opus provides even more sophisticated reasoning but at higher cost.
- **Context Windows:** Claude models support very large context windows, making them excellent for understanding complex codebases and multi-file refactoring tasks.
- **Switching Models:** You can freely switch between ChatGPT and Claude models within the same Xcode session—use the model dropdown in the Coding Assistant to select your preferred AI.
- **Rate Limits:** API-based access has rate limits. If you hit limits, wait a moment or consider upgrading your Anthropic API tier.
- **Security Best Practice:** Xcode stores your API key securely in the system keychain, but never hardcode API keys in your application source code.
- **Multiple Providers:** You can add multiple model providers (OpenAI, Anthropic, Google Gemini, etc.) and switch between them as needed for different tasks.
- **Performance Optimization:** Update to Xcode 26.1.1 or later for improved Coding Assistant memory usage and performance, especially with large git repositories.

## Related Articles
- KB-042: How to generate an API key for Claude in the Anthropic console
- KB-044: How to configure the Claude API endpoint (https://api.anthropic.com/)
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT
- KB-007: How to access the new Intelligence settings tab in Xcode preferences

## Sources
- Anthropic Official Announcement: "Claude is now generally available in Xcode" - https://www.anthropic.com/news/claude-in-xcode (Accessed: November 17, 2025)
- Simon B. Støvring: "Using Claude with Coding Assistant in Xcode 26" - https://simonbs.dev/posts/using-claude-with-coding-assistant-in-xcode-26/ (Accessed: November 17, 2025)
- Wendy Liga: "Use Custom Models in the New Xcode 26 Intelligence" - https://wendyliga.com/blog/xcode-26-custom-model/ (Accessed: November 17, 2025)
- Ronnie Rocha: "Beyond ChatGPT: How to Use Claude and Gemini in Xcode Coding Intelligence" - https://ronnierocha.dev/blog/beyond-chatgpt-how-to-use-claude-and-gemini-in-xcode-coding-intelligence/ (Accessed: November 17, 2025)
- Apple Developer Documentation: "Xcode 26.1.1 Release Notes" - https://developer.apple.com/documentation/xcode-release-notes/xcode-26_1-release-notes (Accessed: November 17, 2025)
- 9to5Mac: "Apple releases Xcode 26.1.1 with coding intelligence improvements" - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- TechCrunch: "Apple brings ChatGPT and other AI models to Xcode" - https://techcrunch.com/2025/06/09/apple-brings-chatgpt-and-other-ai-models-to-xcode/ (Accessed: November 17, 2025)
- MacRumors: "Apple Releases Xcode 26 Beta 7 With GPT-5 Support and Claude Integration" - https://www.macrumors.com/2025/08/28/xcode-gpt-5-claude-integration/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
