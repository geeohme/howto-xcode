# How to set the API key header (x-api-key) for Claude authentication

**Article ID:** KB-045
**Difficulty:** Beginner-Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 5 minutes

## Overview
When adding Claude as a custom model provider in Xcode 26.1+, you need to configure the API key header to enable proper authentication with Anthropic's API. The x-api-key header is required for all requests to the Claude API and must be correctly configured in Xcode's Intelligence settings. This guide shows you exactly how to set the API key header name and value to authenticate your Claude integration.

## Prerequisites
- Xcode 26.1 or later installed
- macOS 26 (Tahoe) or later for full Intelligence features
- Claude configured as model provider (see KB-043)
- Valid Anthropic API key already generated (see KB-042)
- Access to Xcode Intelligence settings

## Steps

### Step 1: Open Xcode Intelligence Settings
1. Launch Xcode 26.1 or later
2. Click **Xcode** in the menu bar
3. Select **Settings** (or press **Command+,**)
4. Click on the **Intelligence** tab at the top of the Settings window

### Step 2: Locate Your Model Provider Configuration
1. In the Intelligence settings, look for the list of configured model providers
2. Find the **Anthropic** or **Claude** provider entry you previously added
3. If you haven't added it yet, click the **Add a Model Provider** button first
4. Click on your Anthropic/Claude provider to view or edit its configuration

### Step 3: Configure the API Endpoint URL
Before setting the header, ensure the API endpoint URL is correctly configured:
1. In the **URL** field, enter: `https://api.anthropic.com/v1/messages`
2. Alternatively, you can use the base URL: `https://api.anthropic.com/`
3. Verify the URL is entered correctly without any trailing spaces

### Step 4: Set the API Key Header Name
This is the critical step for authentication:
1. Locate the **API Key Header** field (sometimes labeled "Header Name" or "Authentication Header")
2. Enter exactly: `x-api-key`
3. **Important:** The header name is case-sensitive and must be lowercase
4. Do not include any quotes, spaces, or additional characters

### Step 5: Enter Your API Key Value
1. In the **API Key** field (or "Header Value" field), paste your Anthropic API key
2. Your API key should start with `sk-ant-` followed by additional characters
3. Do not include any prefix like "Bearer" or additional formatting
4. Example format: `sk-ant-api03-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

### Step 6: Verify Additional Required Settings
Xcode may automatically handle these, but verify if visible:
1. **Content-Type:** Should be set to `application/json` (usually automatic)
2. **Model Name:** You may need to specify the model (e.g., `claude-sonnet-4-20250514`)
3. **API Version:** Anthropic requires an `anthropic-version` header (Xcode typically handles this automatically)

### Step 7: Save and Test the Configuration
1. Click **Save** or **Done** to save your model provider configuration
2. Close the Settings window
3. Open the Coding Assistant panel by pressing **Command+0**
4. At the top of the Coding Assistant, click the model selector dropdown
5. Select your **Anthropic** or **Claude** provider from the list
6. Type a simple test prompt like: "Hello, can you help me with Swift?"
7. Press Enter and wait for a response from Claude

### Step 8: Verify Authentication Success
If configured correctly, you should see:
- Claude responds to your test prompt within a few seconds
- No authentication error messages appear
- The model name (e.g., "Claude Sonnet 4") appears in the assistant header

## Expected Results
Once the x-api-key header is properly configured:
- You can successfully use Claude models in Xcode's Coding Assistant
- All requests to the Anthropic API will be authenticated automatically
- You can switch between ChatGPT and Claude seamlessly in the model selector
- Claude will have access to your full API rate limits and features
- No "401 Unauthorized" or "403 Forbidden" authentication errors occur

## Troubleshooting

### Common Issue 1: "401 Unauthorized" Error
**Problem:** Claude returns an authentication error when you try to use it.

**Solutions:**
- Verify the API key header name is exactly `x-api-key` (lowercase, with hyphen)
- Check that your API key is valid and not expired in the Anthropic Console
- Ensure you copied the complete API key without truncating any characters
- Verify your API key has usage credits remaining (check console.anthropic.com)
- Try regenerating your API key and updating the configuration

### Common Issue 2: Wrong Header Format
**Problem:** You entered the header incorrectly and Claude won't connect.

**Solutions:**
- Do NOT use "Authorization" as the header name (that's for OpenAI)
- Do NOT include "Bearer" before your API key value
- Do NOT add quotes around the header name or value
- The header name must be lowercase: `x-api-key` not `X-API-Key`
- Check for accidental spaces before or after the header name or key value

### Common Issue 3: API Key Not Working
**Problem:** The header is configured correctly but authentication still fails.

**Solutions:**
- Log into console.anthropic.com/settings/keys to verify the key is active
- Check if the API key has been revoked or deleted
- Ensure your Anthropic account has available credits or an active subscription
- Generate a new API key and replace the old one in Xcode settings
- Verify you're using a standard API key, not a legacy or restricted key

### Common Issue 4: Model Provider Not Saving
**Problem:** Your x-api-key configuration doesn't persist after saving.

**Solutions:**
- Update to Xcode 26.1.1 or later, which fixes several configuration bugs
- Try quitting Xcode completely and reopening it
- Check file permissions on `~/Library/Application Support/Xcode/`
- Delete the cached preferences: `~/Library/Caches/com.apple.dt.Xcode`
- Restart your Mac and reconfigure the provider

## Additional Tips

### Security Best Practices
- **Never commit API keys to version control:** Add `.env` files or configuration files containing keys to `.gitignore`
- **Use environment variables:** Consider storing keys in your system environment rather than directly in Xcode
- **Rotate keys regularly:** Generate new API keys periodically and revoke old ones
- **Monitor API usage:** Check console.anthropic.com regularly to detect unauthorized usage
- **Use workspace-specific keys:** If working on multiple projects, use different API keys for different contexts

### Header Format Reference
The x-api-key header is specific to Anthropic's Claude API. Here's how it differs from other providers:

| Provider | Header Name | Header Format |
|----------|-------------|---------------|
| Anthropic Claude | `x-api-key` | Just the key value |
| OpenAI GPT | `Authorization` | `Bearer sk-...` |
| Google Gemini | `x-goog-api-key` | Just the key value |
| Local Models | Usually none | N/A |

### Understanding Required API Headers
When Xcode makes requests to Claude, it includes multiple headers:
1. **x-api-key:** Your authentication credential (what you configure)
2. **anthropic-version:** API version identifier (Xcode handles automatically)
3. **content-type:** Set to `application/json` (Xcode handles automatically)

Xcode abstracts most of this complexity, but understanding these headers helps with troubleshooting.

### Performance Optimization
- **Use specific model names:** Specify exact model versions for consistent behavior
- **Monitor rate limits:** Claude has different rate limits based on your plan
- **Cache responses locally:** For repeated queries, consider caching to reduce API calls
- **Check response times:** If Claude is slow, verify your internet connection and API status

### Multiple Provider Management
You can configure multiple Claude providers with different keys:
- **Personal projects:** Use a personal API key with lower rate limits
- **Work projects:** Use an organization API key with higher limits
- **Testing:** Use a dedicated test API key with separate billing

Simply create multiple model providers in Intelligence settings, each with its own x-api-key configuration.

## Related Articles
- KB-042: How to generate an API key for Claude in the Anthropic console
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-044: How to configure the Claude API endpoint (https://api.anthropic.com/)
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT

## Sources
- Anthropic API Documentation - Authentication (accessed November 17, 2025): https://docs.anthropic.com/en/api/getting-started
- Anthropic Claude API Key Guide - Nightfall AI Security 101 (accessed November 17, 2025): https://www.nightfall.ai/ai-security-101/anthropic-claude-api-key
- Simon B. Stöckli: Using Claude with Coding Assistant in Xcode 26 (accessed November 17, 2025): https://simonbs.dev/posts/using-claude-with-coding-assistant-in-xcode-26/
- Apple Developer Documentation - Writing code with intelligence in Xcode (accessed November 17, 2025): https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode
- Zapier Blog: Claude API Guide (accessed November 17, 2025): https://zapier.com/blog/claude-api/
- Stack Overflow: Claude Code with Anthropic API Key (accessed November 17, 2025): https://stackoverflow.com/questions/79629224/how-do-i-use-claude-code-with-an-existing-anthropic-api-key
- Wendy Liga: Use Custom Models in the New Xcode 26 Intelligence (accessed November 17, 2025): https://wendyliga.com/blog/xcode-26-custom-model/

---
*This article is part of the Xcode v26.1+ knowledge base*
