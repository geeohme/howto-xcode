# How to configure the Claude API endpoint (https://api.anthropic.com/)

**Article ID:** KB-044
**Difficulty:** Beginner-Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 5-10 minutes

## Overview
Configuring the Claude API endpoint in Xcode 26.1+ is an essential step to enable Claude Sonnet 4 as an AI coding assistant. This guide walks you through entering the correct Anthropic API endpoint URL in Xcode's Intelligence settings, ensuring your development environment can communicate with Claude's powerful language models for code generation, debugging, and refactoring tasks.

## Prerequisites
- Xcode 26.1 or later installed
- macOS 14.6 or later
- Claude added as model provider (see KB-043)
- Anthropic API key ready (see KB-042)
- An active internet connection
- Basic familiarity with Xcode Intelligence settings

## Steps

### Step 1: Access Intelligence Settings in Xcode
1. Open Xcode 26.1 or later
2. Click **Xcode** in the menu bar
3. Select **Settings** (or **Preferences** on older macOS versions)
4. Click on the **Intelligence** tab at the top of the Settings window

### Step 2: Initiate Adding a Model Provider
1. In the Intelligence settings panel, locate the **Model Providers** section
2. Click the **"Add a Model Provider"** button (usually marked with a **+** icon)
3. Select **"Internet Hosted"** from the provider type options
4. Choose **"Custom"** or **"Other"** if Anthropic is not listed as a preset option

### Step 3: Enter the Claude API Endpoint URL
This is the critical step for configuring Claude's connection:

1. In the **URL** or **Endpoint** field, enter exactly:
   ```
   https://api.anthropic.com/
   ```
2. Ensure the URL is entered correctly with:
   - **HTTPS protocol** (not HTTP)
   - **No trailing slash after .com/** (some configurations accept it, but it's best practice to include it as shown)
   - **Lowercase letters** throughout
   - **No extra spaces** before or after the URL

**Important:** The base endpoint `https://api.anthropic.com/` is used for Xcode configuration. The API internally routes to versioned endpoints like `/v1/messages`, but you should only enter the base URL in Xcode settings.

### Step 4: Configure the API Key Header Name
1. In the **API Key Header** field (sometimes labeled **Authentication Header**), enter exactly:
   ```
   x-api-key
   ```
2. This header name is case-sensitive and must be lowercase
3. Do not add any prefixes like "Bearer" or other authentication schemes

### Step 5: Enter Your Anthropic API Key
1. In the **API Key** or **Key** field, paste your Anthropic API key
2. Your API key should start with `sk-ant-` followed by additional characters
3. Ensure you've copied the complete key with no extra spaces
4. The key should have been generated from [console.anthropic.com/settings/keys](https://console.anthropic.com/settings/keys)

### Step 6: Provide a Name for the Provider (Optional)
1. In the **Name** field, you can enter a descriptive name such as:
   - "Anthropic"
   - "Claude"
   - "Claude Sonnet 4"
2. This name will appear in the Coding Assistant model selection dropdown
3. If left blank, Xcode may auto-detect and name it "Anthropic"

### Step 7: Save and Verify the Configuration
1. Click **"Add"**, **"Save"**, or **"Done"** to complete the setup
2. Xcode will attempt to verify the connection to the API endpoint
3. If successful, you'll see "Anthropic" or your custom name appear in the list of available model providers
4. A green checkmark or "Connected" status should appear next to the provider

### Step 8: Select Claude as Your Active Model
1. In the Intelligence settings or Coding Assistant panel, locate the **Model** dropdown
2. Select your newly added Anthropic/Claude provider
3. Choose your preferred Claude model (e.g., "Claude Sonnet 4" or "claude-sonnet-4-5")
4. The selection will be saved for future coding sessions

## Expected Results
After successfully configuring the Claude API endpoint, you should:

- See "Anthropic" or "Claude" listed as an available model provider in Intelligence settings
- Be able to select Claude models from the Coding Assistant model dropdown
- Receive AI-powered code suggestions from Claude when using the Coding Assistant
- See a "Connected" or verification status indicator next to the Anthropic provider
- Be able to start conversations with Claude in the Coding Assistant panel (Command+0)
- Experience Claude's larger context window and strong reasoning capabilities for coding tasks

## Troubleshooting

### Common Issue 1: "Unable to Connect to API Endpoint" Error
**Problem:** Xcode displays an error message saying it cannot reach the API endpoint or the connection failed.

**Solution:**
- Verify your internet connection is active and stable
- Double-check the endpoint URL is exactly `https://api.anthropic.com/` with no typos
- Ensure you're using HTTPS (not HTTP)
- Check if your network has firewall rules blocking access to api.anthropic.com
- Try accessing https://api.anthropic.com/ in a web browser to confirm connectivity
- If behind a corporate firewall or VPN, verify external API access is permitted
- Restart Xcode and try adding the provider again

### Common Issue 2: "Invalid API Key" or Authentication Error
**Problem:** Xcode shows an authentication error or says the API key is invalid even though you copied it correctly.

**Solution:**
- Verify the **API Key Header** field contains exactly `x-api-key` (lowercase, no spaces)
- Confirm your API key starts with `sk-ant-` and is complete
- Check that your API key hasn't expired or been revoked in the Anthropic console
- Ensure you have an active paid Anthropic account (free trials may have limitations)
- Generate a new API key at [console.anthropic.com/settings/keys](https://console.anthropic.com/settings/keys) and try again
- Verify your Anthropic account has available credits or an active subscription

### Common Issue 3: Endpoint Works But No Models Appear
**Problem:** The endpoint connects successfully, but no Claude models show up in the model selection dropdown.

**Solution:**
- Restart Xcode completely (Quit and reopen)
- Remove the model provider and re-add it following all steps carefully
- Verify your Anthropic API key has access to Claude models (check your subscription tier)
- Wait a few moments for Xcode to query available models from the API
- Check the Anthropic API status at https://status.anthropic.com/ for any service disruptions
- Ensure your API key has the necessary permissions (some keys may be restricted)

### Common Issue 4: Wrong Endpoint Format Entered
**Problem:** You accidentally entered an incorrect endpoint URL format.

**Solution:**
- The correct format is: `https://api.anthropic.com/`
- Common mistakes to avoid:
  - ❌ `http://api.anthropic.com/` (missing 's' in https)
  - ❌ `https://api.anthropic.com/v1/messages` (too specific, remove version path)
  - ❌ `https://console.anthropic.com/` (this is the web console, not the API)
  - ❌ `api.anthropic.com` (missing https:// protocol)
  - ✅ `https://api.anthropic.com/` (correct)

## Additional Tips
- **Endpoint Versioning:** While the Anthropic API uses versioned endpoints like `/v1/messages` for direct API calls, Xcode only requires the base URL `https://api.anthropic.com/`. The Xcode Intelligence system handles version negotiation automatically.
- **SSL/HTTPS Requirement:** The Claude API endpoint requires HTTPS for secure communication. HTTP connections will be rejected. Always use `https://` in the URL.
- **API Key Security:** Your API key is stored securely in Xcode's keychain. Never hardcode API keys in your source code or commit them to version control.
- **Multiple Providers:** You can add multiple AI model providers (ChatGPT, Claude, Gemini, etc.) to Xcode and switch between them based on your current task.
- **Regional Endpoints:** As of November 2025, Anthropic uses a single global endpoint. Regional endpoints may be introduced in the future, but currently `https://api.anthropic.com/` serves all regions.
- **Console Migration:** The Anthropic Console is transitioning from console.anthropic.com to platform.claude.com. However, the API endpoint `https://api.anthropic.com/` remains unchanged, so your Xcode configuration will continue working without modification.
- **Testing the Connection:** After configuration, open the Coding Assistant (Command+0) and try a simple prompt like "explain this code" with a Swift function selected to verify Claude is responding correctly.
- **Rate Limits:** Depending on your Anthropic subscription tier (Pro, Team, or Enterprise), you'll have different rate limits. Monitor your usage in the Anthropic console to avoid unexpected interruptions.

## Related Articles
- KB-042: How to generate an API key for Claude in the Anthropic console
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-045: How to set the API key header (x-api-key) for Claude authentication
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT

## Sources
- Anthropic API Documentation - https://docs.anthropic.com/en/api/overview (Accessed: November 17, 2025)
- Using Claude with Coding Assistant in Xcode 26 - Simon B. Støvring - https://simonbs.dev/posts/using-claude-with-coding-assistant-in-xcode-26/ (Accessed: November 17, 2025)
- Beyond ChatGPT: How to Use Claude and Gemini in Xcode Coding Intelligence - Ronnie Rocha - https://ronnierocha.dev/blog/beyond-chatgpt-how-to-use-claude-and-gemini-in-xcode-coding-intelligence/ (Accessed: November 17, 2025)
- Use Custom Models in the New Xcode 26 Intelligence - Wendy Liga - https://wendyliga.com/blog/xcode-26-custom-model/ (Accessed: November 17, 2025)
- Claude API Quickstart - https://docs.claude.com/en/docs/quickstart (Accessed: November 17, 2025)
- Anthropic Console - https://console.anthropic.com/ (Accessed: November 17, 2025)
- How to Integrate ChatGPT and Claude in Xcode 26 - CISIN - https://www.cisin.com/coffee-break/how-to-integrate-chatgpt-and-claude-in-xcode-26.html (Accessed: November 17, 2025)
- Claude API: How to get a key and use the API - Zapier Blog - https://zapier.com/blog/claude-api/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
