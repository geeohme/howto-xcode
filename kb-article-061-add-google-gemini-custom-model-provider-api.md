# How to add Google Gemini as a custom model provider via API

**Article ID:** KB-061
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 10-15 minutes

## Overview
Xcode 26.1+ supports adding Google's Gemini models as custom AI providers through the Intelligence settings, enabling you to leverage Google's powerful multimodal AI capabilities for code generation, debugging, and refactoring. This guide walks you through obtaining a Gemini API key from Google AI Studio and configuring it in Xcode to access models like Gemini 2.0 Flash, Gemini 2.0 Pro, and Gemini 2.5 Pro for your coding assistant.

## Prerequisites
- Xcode 26.1 or later installed (version 26.1.1+ recommended)
- macOS Sequoia 15.5+ or later (macOS Tahoe 26+ recommended for full Intelligence features)
- A Google account (free or paid)
- Internet connection
- Basic familiarity with Xcode Intelligence settings
- Note: Google offers a free tier with generous quotas, making this accessible without immediate payment

## Steps

### Step 1: Access Google AI Studio
Navigate to Google's AI Studio platform to create your API key:

1. Open your web browser and go to [https://aistudio.google.com](https://aistudio.google.com)
2. Click **"Sign in"** in the top-right corner
3. Sign in with your Google account credentials
4. If prompted, accept the Terms of Service and Privacy Policy
5. You'll be taken to the Google AI Studio dashboard

**Note:** Google AI Studio is a web-based platform that requires no installation and runs entirely in your browser.

### Step 2: Create a Gemini API Key
Generate your API key for accessing Gemini models:

1. In the Google AI Studio interface, locate the **"Get API key"** button in the left sidebar (it may appear as a key icon)
2. Click **"Get API key"** or **"Create API key"**
3. You'll see a dialog with API key options
4. Click **"Create API key in new project"** (recommended for first-time users)
   - Alternatively, select an existing Google Cloud project if you have one
5. Optionally, give your project a descriptive name (e.g., "Xcode Development")
6. Click **"Create"** to generate the API key
7. **Immediately copy the generated API key** to your clipboard
8. Store the key securely in a password manager or secure note (you can access it later, but it's best practice to save it immediately)

**Important:** Unlike some other services, Google AI Studio allows you to view your API keys again later, but it's still recommended to store them securely.

### Step 3: Understand Gemini API Pricing and Quotas
Before configuring Xcode, understand the cost structure:

**Free Tier (No Credit Card Required):**
- Available for all Google accounts
- Generous free quota for experimentation and development
- Rate limits apply (requests per minute/day)
- Suitable for individual developers and testing

**Paid Tier:**
- Higher rate limits and quotas
- Production-level reliability
- Pay-per-use pricing based on tokens
- Configure billing in Google Cloud Console if needed

**Recommendation:** Start with the free tier to test the integration before enabling billing.

### Step 4: Open Xcode Intelligence Settings
Access Xcode's Intelligence configuration panel:

1. Launch **Xcode 26.1** or later
2. Click **Xcode** in the menu bar (top-left corner)
3. Select **Settings** (or **Preferences** on older macOS versions, or press **Command+,**)
4. Click the **Intelligence** tab at the top of the Settings window

You should see the Intelligence settings panel with existing model providers (ChatGPT and any previously configured providers).

### Step 5: Add Google Gemini as a Model Provider
Configure Gemini as a new custom provider:

1. In the Intelligence settings panel, click the **"+"** button or **"Add a Model Provider"** option
2. Select **"Internet Hosted"** when prompted for provider type
3. You'll see a configuration form with several fields to complete

Fill in the following information exactly:

**Provider Name/Label:**
```
Google Gemini
```
(You can also use "Gemini" or "Google" - this name will appear in your model selector)

**API Endpoint URL:**
```
https://generativelanguage.googleapis.com/v1beta/openai
```

**API Key Header:**
```
x-goog-api-key
```

**API Key:**
Paste the API key you copied from Google AI Studio in Step 2

4. Click **"Add"**, **"Save"**, or **"Continue"** to save the configuration

**Critical Notes:**
- The endpoint URL `https://generativelanguage.googleapis.com/v1beta/openai` is Google's OpenAI-compatible API endpoint specifically designed for tools like Xcode
- The API key header `x-goog-api-key` is case-sensitive and must be lowercase
- Earlier versions of Xcode 26 beta required proxy workarounds for Gemini, but Xcode 26.1+ natively supports the v1beta/openai endpoint

### Step 6: Restart Xcode (Recommended)
To ensure the new model provider is properly recognized:

1. **Save all your work** in Xcode
2. Quit Xcode completely (Xcode > Quit Xcode or **Command+Q**)
3. Wait a few seconds
4. Relaunch Xcode from your Applications folder or Dock

**Note:** While not always required, restarting Xcode after adding a custom provider ensures all components recognize the new configuration.

### Step 7: Verify Gemini Models Are Available
Confirm that Google's Gemini models are now accessible:

1. Open the **Coding Assistant** panel in Xcode (press **Command+Shift+A** or go to **View** > **Panels** > **Coding Assistant**)
2. Click **"New Conversation"** or the **+** button to start a new chat
3. Look for the model selector dropdown (typically at the top of the Coding Assistant panel)
4. Click the dropdown to view available models
5. You should now see **"Google Gemini"** (or the name you chose) as an option with available models, including:
   - **Gemini 2.0 Flash** (recommended for most coding tasks - fast and efficient)
   - **Gemini 2.0 Pro** (experimental, best for complex coding problems)
   - **Gemini 2.5 Pro** (highest capability model if available with your account)
   - **Gemini 2.0 Flash-Lite** (most cost-efficient option)

### Step 8: Select and Test Gemini
Test your Gemini integration:

1. From the model selector dropdown, choose **Google Gemini > Gemini 2.0 Flash** (or your preferred model)
2. In the Coding Assistant text field, type a test prompt such as:
   ```
   Create a SwiftUI view that displays a list of tasks with checkboxes
   ```
3. Press **Enter** or click the send button
4. Wait for Gemini to generate a response (typically 2-5 seconds)
5. Review the generated code—Gemini should provide clean, well-structured SwiftUI code with comments

If you receive a response from Gemini with working SwiftUI code, your integration is successful!

## Expected Results
After completing these steps, you should:

- See "Google Gemini" listed as an available model provider in your Intelligence settings
- Be able to select Gemini models (2.0 Flash, 2.0 Pro, 2.5 Pro, etc.) from the Coding Assistant's model dropdown
- Receive AI-generated code suggestions and assistance powered by Google's Gemini models
- Experience Gemini's strengths in multimodal understanding, code generation, and natural language processing
- Have access to Google's latest AI capabilities for coding tasks
- Be able to switch between ChatGPT, Claude, and Gemini models as needed for different tasks
- Monitor your API usage through the Google AI Studio dashboard

## Troubleshooting

### Issue 1: "Unable to Connect to API Endpoint" Error
**Problem:** Xcode displays an error saying it cannot reach the Gemini API endpoint.

**Solution:**
- Verify your internet connection is active and stable
- Double-check the endpoint URL is exactly: `https://generativelanguage.googleapis.com/v1beta/openai`
- Ensure there are no typos or extra spaces in the URL
- Verify you're using HTTPS (not HTTP)
- Check if your network has firewall rules blocking access to generativelanguage.googleapis.com
- If behind a corporate firewall or VPN, verify external API access is permitted
- Try accessing https://aistudio.google.com in your browser to confirm Google services are accessible
- Restart Xcode and try adding the provider again
- Update to Xcode 26.1.1 or later, which has improved support for custom endpoints

### Issue 2: "Invalid API Key" or Authentication Error
**Problem:** Xcode shows an authentication error or says the API key is invalid.

**Solution:**
- Verify the **API Key Header** field contains exactly `x-goog-api-key` (lowercase, with hyphens)
- Confirm you copied the complete API key from Google AI Studio without truncating characters
- Check that your API key is active by visiting [https://aistudio.google.com](https://aistudio.google.com) and going to "Get API key"
- Ensure you haven't accidentally copied extra spaces before or after the key
- Try generating a new API key in Google AI Studio and updating your Xcode configuration
- Verify your Google account has access to the Gemini API (it should be available for all Google accounts)
- If you've reached your free tier quota, wait for the quota to reset or enable billing in Google Cloud Console

### Issue 3: Model Provider Added But No Models Appear
**Problem:** Google Gemini appears in your providers list but no models show in the model selector dropdown.

**Solution:**
- Restart Xcode completely (Quit and reopen)
- Wait 10-15 seconds after adding the provider for Xcode to query available models
- Remove the model provider and re-add it following all steps carefully
- Verify your API key has access to Gemini models by testing it in Google AI Studio
- Check the Google AI Studio status page for any service disruptions
- Ensure you're running Xcode 26.1 or later (earlier betas had issues with Gemini)
- Try selecting "Gemini" from the provider list and waiting a few moments for models to populate
- Clear Xcode's cache: Quit Xcode, delete `~/Library/Caches/com.apple.dt.Xcode`, and relaunch

### Issue 4: "Rate Limit Exceeded" Error
**Problem:** You receive errors about exceeding rate limits when using Gemini.

**Solution:**
- Check your current usage in Google AI Studio dashboard under "API Keys" > "Usage"
- Free tier has rate limits (requests per minute and per day)
- Wait for your quota to reset (typically resets daily)
- Enable billing in Google Cloud Console for higher rate limits
- Reduce the frequency of requests or batch your prompts
- Consider using Gemini 2.0 Flash-Lite for more cost-efficient requests
- Monitor your usage patterns and adjust your workflow accordingly

### Issue 5: Wrong Endpoint URL Format
**Problem:** You entered an incorrect endpoint URL and Gemini won't connect.

**Solution:**
The correct format is: `https://generativelanguage.googleapis.com/v1beta/openai`

Common mistakes to avoid:
- ❌ `https://generativelanguage.googleapis.com/v1/` (wrong version path)
- ❌ `https://generativelanguage.googleapis.com/v1beta/models/` (too specific)
- ❌ `https://aistudio.google.com` (this is the web interface, not the API)
- ❌ `https://ai.google.dev` (this is the documentation site)
- ❌ `generativelanguage.googleapis.com/v1beta/openai` (missing https://)
- ✅ `https://generativelanguage.googleapis.com/v1beta/openai` (correct)

## Additional Tips

### Choosing the Right Gemini Model
Different Gemini models have different strengths:

**Gemini 2.0 Flash (Recommended for Most Tasks):**
- Fast response times
- High-quality code generation
- Cost-efficient
- Best for: day-to-day coding tasks, quick questions, code explanations

**Gemini 2.0 Pro (Experimental):**
- More sophisticated reasoning
- Better for complex architectural decisions
- Slower but more thorough responses
- Best for: complex refactoring, architecture design, difficult debugging

**Gemini 2.5 Pro (Premium):**
- Highest capability model
- Extended context window
- Advanced multimodal capabilities
- Best for: large codebase analysis, comprehensive reviews, complex multi-file refactoring

**Gemini 2.0 Flash-Lite:**
- Most cost-efficient option
- Faster inference
- Good for simple tasks
- Best for: quick code snippets, simple questions, high-volume usage

### API Cost Management
- **Monitor Usage:** Check your API usage regularly in Google AI Studio
- **Free Tier First:** Use the free tier for development and testing before enabling billing
- **Choose Appropriate Models:** Use Flash-Lite for simple tasks, reserve Pro/2.5 for complex work
- **Batch Requests:** When possible, batch multiple questions into single prompts to reduce API calls
- **Set Budgets:** If using paid tier, set budget alerts in Google Cloud Console
- **Compare Costs:** Gemini's pricing is competitive—compare with Claude and ChatGPT for your use case

### Security Best Practices
- **Never Commit API Keys:** Add configuration files with API keys to `.gitignore`
- **Use Environment Variables:** Consider storing keys in system environment variables rather than directly in Xcode
- **Rotate Keys Regularly:** Generate new API keys periodically and revoke old ones
- **Monitor for Unauthorized Use:** Check Google AI Studio usage dashboard for unexpected activity
- **Project-Specific Keys:** Use different API keys for different projects or contexts
- **Revoke Compromised Keys:** If a key is exposed, immediately revoke it in Google AI Studio and generate a new one

### Performance Optimization
- **Model Selection:** Gemini 2.0 Flash offers the best speed-to-quality ratio for most coding tasks
- **Prompt Engineering:** Clear, specific prompts get better results faster
- **Context Selection:** Select only relevant code when asking questions to reduce token usage
- **Network Speed:** Ensure you have a stable, fast internet connection for best response times
- **Xcode Version:** Keep Xcode updated to the latest version for improved Intelligence performance

### Multiple Provider Strategy
You can use different AI providers for different tasks:
- **ChatGPT (GPT-5):** Fast responses, broad general knowledge, good for quick questions
- **Claude Sonnet 4:** Large context windows, excellent for code reviews and refactoring
- **Gemini 2.0 Flash:** Multimodal capabilities, strong reasoning, good balance of speed and quality
- **Gemini 2.5 Pro:** Complex problems requiring deep reasoning and analysis

Configure all three and switch between them based on your current task.

### Understanding the OpenAI-Compatible Endpoint
Google provides an OpenAI-compatible API endpoint (`/v1beta/openai`) specifically to enable integration with tools that expect OpenAI's API format. This allows Xcode to communicate with Gemini using the same protocol it uses for ChatGPT, making the integration seamless. The standard Gemini API endpoint (`/v1beta/models`) has a different request/response format that Xcode doesn't natively support, which is why the OpenAI-compatible endpoint is required.

### Regional Availability
As of November 2025, the Gemini API is available globally through the single endpoint `https://generativelanguage.googleapis.com`. Google may introduce regional endpoints in the future for improved latency, but currently, all regions use the same endpoint.

### Gemini's Strengths in Xcode
- **Multimodal Understanding:** Gemini can process and reason about code, natural language, and potentially images (in future Xcode versions)
- **Long Context:** Gemini 2.5 Pro supports very long context windows, excellent for large files
- **Code Generation Quality:** Competitive with ChatGPT and Claude for Swift/SwiftUI code generation
- **Natural Conversations:** Gemini excels at understanding conversational, natural language prompts
- **Reasoning Capabilities:** Strong logical reasoning for debugging and problem-solving
- **Up-to-Date Knowledge:** Regular model updates keep knowledge current

### Testing Your Integration
After setup, try these test prompts to verify different capabilities:

1. **Code Generation:** "Create a SwiftUI view with a gradient background"
2. **Code Explanation:** Select a complex function and ask "Explain what this code does"
3. **Debugging:** "Why am I getting a 'Cannot find type' error here?"
4. **Refactoring:** "Refactor this code to use async/await instead of completion handlers"
5. **Documentation:** "Generate documentation comments for this class"

## Related Articles
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-044: How to configure the Claude API endpoint (https://api.anthropic.com/)
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT
- KB-062: How to configure OpenRouter.io for multi-model access with one API key (coming soon)
- KB-063: How to set up local AI models using Ollama in Xcode (coming soon)

## Sources
- Google AI for Developers - Using Gemini API keys - https://ai.google.dev/gemini-api/docs/api-key (Accessed: November 17, 2025)
- Google AI for Developers - Gemini API Reference - https://ai.google.dev/api (Accessed: November 17, 2025)
- Google AI for Developers - Gemini models documentation - https://ai.google.dev/gemini-api/docs/models (Accessed: November 17, 2025)
- Google Developers Blog - "Gemini 2.0: Flash, Flash-Lite and Pro" - https://developers.googleblog.com/en/gemini-2-family-expands/ (Accessed: November 17, 2025)
- KDnuggets - "The Complete Guide to Using Google AI Studio" - https://www.kdnuggets.com/the-complete-guide-to-using-google-ai-studio (Accessed: November 3, 2025)
- Apideck - "How to Get Your Gemini API Key" - https://www.apideck.com/blog/how-to-get-your-gemini-api-key (Accessed: November 17, 2025)
- GitHub - Gemini API Release Notes - https://ai.google.dev/gemini-api/docs/changelog (Accessed: November 17, 2025)
- Wendy Liga - "Use Custom Models in the New Xcode 26 Intelligence" - https://wendyliga.com/blog/xcode-26-custom-model/ (Accessed: November 17, 2025)
- Carlo Zottmann - "How to use Google Gemini in Xcode 26 beta" - https://zottmann.org/2025/06/13/how-to-use-google-gemini.html (Accessed: November 17, 2025)
- Michael Tsai Blog - "How to Use Google Gemini in Xcode 26 Beta" - https://mjtsai.com/blog/2025/07/10/how-to-use-google-gemini-in-xcode-26-beta/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
