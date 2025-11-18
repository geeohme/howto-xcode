# How to configure OpenRouter.io for multi-model access with one API key

**Article ID:** KB-062
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 10-15 minutes

## Overview

OpenRouter.io is a unified AI model gateway that provides access to hundreds of AI models from multiple providers (Anthropic, OpenAI, Google, Meta, DeepSeek, and more) through a single API key. This guide shows you how to configure OpenRouter as a custom model provider in Xcode 26.1+, enabling you to access Claude, GPT, Gemini, LLaMA, and other models without managing separate API keys for each provider. OpenRouter is particularly valuable for developers who want to test different models, optimize costs, or access models not directly supported in Xcode.

## Prerequisites

- Xcode 26.1 or later installed (version 26.1.1+ recommended)
- macOS Sequoia 15.5+ or later
- Active internet connection
- OpenRouter account with API key (free tier available at https://openrouter.ai)
- Basic understanding of custom model providers in Xcode Intelligence settings

## Steps

### Step 1: Create an OpenRouter Account

Before configuring Xcode, obtain your OpenRouter API key:

1. Navigate to [https://openrouter.ai](https://openrouter.ai) in your web browser
2. Click **"Sign Up"** in the top-right corner
3. Create your account using email, Google, or GitHub authentication
4. Complete the registration process and verify your email if required
5. After login, you'll be directed to the OpenRouter dashboard

**Note:** OpenRouter offers a free tier with access to many models. You can add credits for paid models later.

### Step 2: Generate Your OpenRouter API Key

Create an API key for Xcode integration:

1. Click on your **profile icon** or username in the top-right corner
2. Select **"Keys"** or **"API Keys"** from the dropdown menu
3. Click the **"Create Key"** or **"+ Create API Key"** button
4. Optionally, give your key a descriptive name (e.g., "Xcode Development")
5. Click **"Create"** to generate the API key
6. **Immediately copy** the API key to your clipboard—it starts with `sk-or-v1-` followed by a long alphanumeric string
7. Store the key securely (you may not be able to view it again after closing the dialog)

**Security Note:** Treat your OpenRouter API key like a password. Never commit it to version control or share it publicly.

### Step 3: Open Xcode Intelligence Settings

Access the Intelligence configuration panel in Xcode:

1. Launch **Xcode 26.1** or later
2. Click **Xcode** in the menu bar (top-left corner)
3. Select **Settings** (or **Preferences** on older macOS versions)
4. Click the **Intelligence** tab at the top of the Settings window

You should see the Intelligence settings panel with existing model providers (ChatGPT is typically listed by default).

### Step 4: Add OpenRouter as a Custom Model Provider

Configure OpenRouter as a new model provider:

1. In the Intelligence settings panel, click the **"+"** button or **"Add a Model Provider"** option
2. Select **"Internet Hosted"** when prompted for provider type
3. Choose **"Custom"** or **"Other"** since OpenRouter isn't a preset option
4. You'll see a configuration form with the following fields:

   **Provider Name/Label:**
   ```
   OpenRouter
   ```
   (or any descriptive name you prefer, like "OpenRouter - Multi-Model")

   **API Endpoint URL:**
   ```
   https://openrouter.ai/api
   ```

   **Important:** Do NOT add `/v1` at the end of the URL. Xcode appends paths automatically.

   **API Key Header:**
   ```
   api_key
   ```

   **Important:** OpenRouter uses `api_key` as the header name for Xcode integration, NOT `Authorization` or `x-api-key`.

   **API Key:**
   Paste the API key you copied in Step 2 (starting with `sk-or-v1-`)

5. Click **"Add"**, **"Save"**, or **"Continue"** to save the configuration

**Critical Configuration Notes:**
- The endpoint URL must be exactly `https://openrouter.ai/api` (no trailing `/v1`)
- The API key header must be `api_key` (lowercase, no hyphens)
- These settings differ from other providers like Anthropic or OpenAI

### Step 5: Restart Xcode (If Necessary)

To ensure the new model provider is recognized:

1. **Save all your work** in Xcode
2. Quit Xcode completely (Xcode > Quit Xcode or ⌘Q)
3. Relaunch Xcode from your Applications folder or Dock

**Note:** Restarting Xcode is often required for new custom providers to appear in the model list. This is normal behavior in Xcode 26.1+.

### Step 6: Verify OpenRouter Models Are Available

Confirm that OpenRouter models are now accessible:

1. Open the **Coding Assistant** panel in Xcode (press **⌘+0** or go to **View** > **Panels** > **Coding Assistant**)
2. Click **"New Conversation"** or the **+** button to start a new chat
3. Look for the **model selector dropdown** at the top of the Coding Assistant panel
4. Click the dropdown to view available providers and models
5. You should now see **"OpenRouter"** (or your custom name) as an option
6. Expand OpenRouter to see available models, which may include:
   - **Claude Sonnet 4** (Anthropic)
   - **GPT-5** and **GPT-4.1** (OpenAI)
   - **Gemini 2.5 Pro** (Google)
   - **LLaMA 4** models (Meta)
   - **DeepSeek** models (thinking and chat variants)
   - **Free models** (marked with "free" suffix)
   - Hundreds of other models from various providers

**Known Issue:** Some users report that OpenRouter connects successfully but fails to load the complete model list, possibly due to the large number of available models (400+). If this occurs, try restarting Xcode or wait a few moments for the list to populate.

### Step 7: Select and Test an OpenRouter Model

Test your OpenRouter integration:

1. From the model selector dropdown, choose **OpenRouter** as your provider
2. Select a model to test—we recommend starting with a free model like:
   - `meta-llama/llama-3.3-70b-instruct:free`
   - `google/gemini-2.5-flash-exp:free`
   - `deepseek/deepseek-chat-v3:free`
3. In the Coding Assistant text field, type a test prompt such as:
   ```
   Create a simple SwiftUI view with a button that increments a counter
   ```
4. Press **Enter** or click the send button
5. Wait for the selected model to generate a response
6. Review the generated code to verify the integration is working

If you receive a response from your selected model, your OpenRouter integration is successful!

### Step 8: Explore Different Models (Optional)

One of OpenRouter's key benefits is model flexibility:

1. Start new conversations with different models to compare outputs
2. Try Claude for complex refactoring tasks
3. Use GPT-5 for general coding assistance
4. Test Gemini for Google-optimized responses
5. Experiment with DeepSeek for cost-effective alternatives
6. Use LLaMA models for open-source options
7. Compare free models against paid models to find the best value

**Tip:** Keep track of which models work best for specific tasks (code generation, debugging, documentation, etc.).

## Expected Results

After completing these steps, you should:

- See "OpenRouter" listed as an available model provider in your Intelligence settings
- Access hundreds of AI models from multiple providers through a single API key
- Switch between Claude, GPT, Gemini, LLaMA, and other models in the Coding Assistant
- Use both free and paid models depending on your OpenRouter credits
- Receive AI-generated code suggestions from your selected OpenRouter model
- Compare different models' responses to the same coding task
- Optimize costs by choosing the most appropriate model for each task
- Access models not directly available through Xcode's built-in providers

## Troubleshooting

### Issue 1: OpenRouter Provider Not Showing After Adding

**Problem:** You added the OpenRouter configuration but don't see it in the model list.

**Solution:**
- Restart Xcode completely (this is often required for new providers)
- Verify the API endpoint URL is exactly `https://openrouter.ai/api` (no `/v1` suffix)
- Check that the API key header is `api_key` (lowercase, all one word)
- Ensure you're running Xcode 26.1 or later (check Xcode > About Xcode)
- Remove and re-add the provider if the issue persists
- Wait 30-60 seconds after adding the provider before checking the model list

### Issue 2: Authentication or API Key Errors

**Problem:** You see "Invalid API Key" or authentication failed errors when trying to use OpenRouter.

**Solution:**
- Verify you copied the entire API key correctly (should start with `sk-or-v1-`)
- Check that your OpenRouter account has available credits at [https://openrouter.ai/credits](https://openrouter.ai/credits)
- Ensure the API key header is set to `api_key` (not "Authorization" or "x-api-key")
- Try regenerating a new API key from the OpenRouter dashboard and updating Xcode
- Verify your API key hasn't been revoked or expired
- Check if you're trying to use a paid model without credits—switch to a free model to test

### Issue 3: "Could Not Connect to Model Provider" Error

**Problem:** Xcode can't establish a connection to OpenRouter's API.

**Solution:**
- Check your internet connection
- Verify the API endpoint URL is correct: `https://openrouter.ai/api`
- Ensure there are no firewall or VPN settings blocking the connection
- Visit [https://openrouter.ai/status](https://openrouter.ai) to check if the service is operational
- Try accessing openrouter.ai in your browser to verify connectivity
- Temporarily disable VPN or proxy settings to test direct connectivity

### Issue 4: Models Not Loading or List Is Empty

**Problem:** OpenRouter provider is added but no models appear in the dropdown, or the list takes a very long time to load.

**Solution:**
- Restart Xcode (this is the most common fix)
- Wait 1-2 minutes for the model list to fully load—OpenRouter has 400+ models
- Verify your API key is valid by testing it at [https://openrouter.ai/playground](https://openrouter.ai/playground)
- Check your internet connection speed—a slow connection may time out when loading the large model list
- Try removing the provider and adding it again with fresh credentials
- Clear Xcode's cache: Quit Xcode, delete `~/Library/Caches/com.apple.dt.Xcode`, and relaunch
- Known issue: Some users report model list loading issues due to the sheer number of available models

### Issue 5: "Insufficient Credits" or Rate Limit Errors

**Problem:** You receive errors about insufficient credits or rate limits when using certain models.

**Solution:**
- Check your credit balance at [https://openrouter.ai/credits](https://openrouter.ai/credits)
- Switch to a free model (models with `:free` suffix) for testing
- Add credits to your OpenRouter account for paid models
- Monitor your usage to understand which models consume the most credits
- Different models have different rate limits—wait a moment and try again
- Consider upgrading your OpenRouter tier if you consistently hit limits

## Additional Tips

- **Cost Optimization:** OpenRouter displays real-time pricing for each model. Free models are marked with `:free` suffix. Monitor your usage at [https://openrouter.ai/activity](https://openrouter.ai/activity) to optimize costs.

- **Model Selection Strategy:**
  - Use **free models** for testing and simple tasks
  - Use **Claude Sonnet 4** for complex refactoring and architecture decisions
  - Use **GPT-5** for general-purpose coding assistance
  - Use **DeepSeek** models for extremely cost-effective alternatives (often $0.28 per 1M tokens)
  - Use **Gemini** for Google Cloud integration or multimodal tasks
  - Use **LLaMA** models for open-source, privacy-focused development

- **Unified Access:** With OpenRouter, you don't need separate API keys for Anthropic, OpenAI, Google, etc. One key gives you access to all providers.

- **Model Comparison:** Start parallel conversations using different models through OpenRouter to compare quality, speed, and cost for your specific use cases.

- **Privacy Considerations:** When using OpenRouter, your code is sent to the selected model provider. Free models may opt you into data training unless you configure otherwise. Review OpenRouter's privacy policy for details.

- **Experimental Models:** OpenRouter often provides early access to experimental or preview models (marked with `-exp` or `-preview`) before they're widely available.

- **Rate Limits:** OpenRouter implements rate limiting based on your account tier. If you hit limits frequently, consider upgrading or distributing requests across different models.

- **Multiple Providers:** You can have OpenRouter AND individual providers (Anthropic, OpenAI) configured simultaneously. Use OpenRouter for experimentation and dedicated keys for production workflows.

- **Performance Optimization:** Update to Xcode 26.1.1 or later for improved Coding Assistant memory usage and performance, especially when switching between many models.

- **Community Models:** OpenRouter provides access to community-hosted models and experimental projects not available through official provider APIs.

## Related Articles

- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-044: How to configure the Claude API endpoint (https://api.anthropic.com/)
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-050: How to compare responses between ChatGPT and Claude for the same coding task
- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT
- KB-007: How to access the new Intelligence settings tab in Xcode preferences
- KB-061: How to add Google Gemini as a custom model provider via API *(if available)*
- KB-063: How to set up local AI models using Ollama in Xcode *(if available)*

## Sources

- OpenRouter Official Documentation - "Xcode Integration" - https://openrouter.ai/docs/community/xcode (Accessed: November 17, 2025)
- OpenRouter API Reference - https://openrouter.ai/docs/api-reference/overview (Accessed: November 17, 2025)
- Carlo Zottmann - "Use any OpenAI-compatible LLM provider in Xcode 26" - https://zottmann.org/2025/06/11/use-any-openaicompatible-llm-provider.html (Accessed: November 17, 2025)
- Wendy Liga - "Use Custom Models in the New Xcode 26 Intelligence" - https://wendyliga.com/blog/xcode-26-custom-model/ (Accessed: November 17, 2025)
- Ronnie Rocha - "Beyond ChatGPT: How to Use Claude and Gemini in Xcode Coding Intelligence" - https://ronnierocha.dev/blog/beyond-chatgpt-how-to-use-claude-and-gemini-in-xcode-coding-intelligence/ (Accessed: November 17, 2025)
- OpenRouter Models Catalog - https://openrouter.ai/models (Accessed: November 17, 2025)
- Teamday.ai - "Top AI Models on OpenRouter 2025: Cost vs Performance Analysis" - https://www.teamday.ai/blog/top-ai-models-openrouter-2025 (Accessed: November 17, 2025)
- IntuitionLabs - "LLM API Pricing Comparison (2025): OpenAI, Gemini, Claude" - https://intuitionlabs.ai/articles/llm-api-pricing-comparison-2025 (Accessed: November 17, 2025)
- Skywork.ai - "OpenRouter Review 2025: Unified AI Model API, Pricing & Privacy" - https://skywork.ai/blog/openrouter-review-2025-unified-ai-model-api-pricing-privacy/ (Accessed: November 17, 2025)
- OpenRouter Quickstart Guide - https://openrouter.ai/docs/quickstart (Accessed: November 17, 2025)
- Apple Developer Documentation - "Xcode 26.1.1 Release Notes" - https://developer.apple.com/documentation/xcode-release-notes/xcode-26_1-release-notes (Accessed: November 17, 2025)
- 9to5Mac - "Apple releases Xcode 26.1.1 with coding intelligence improvements" - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
