# How to access the new Intelligence settings tab in Xcode preferences

**Article ID:** KB-007
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 5 minutes

## Overview
Xcode 26 introduces a new Intelligence settings tab in preferences that allows you to configure AI-powered coding assistance features. This tab provides access to Coding Intelligence settings, including enabling the feature, configuring code generation levels, and adding custom AI model providers like ChatGPT, Claude, and other OpenAI-compatible services.

## Prerequisites
- Xcode 26 or later (26.1.1 recommended for latest improvements)
- macOS 26 Tahoe or later
- Apple Intelligence enabled in System Settings
- Basic familiarity with Xcode interface

**Note:** The Intelligence features are not available on earlier versions of macOS, even if you have Xcode 26 installed. Both the operating system and Xcode must meet the minimum version requirements.

## Steps

### Step 1: Open Xcode Settings/Preferences
Launch Xcode and access the Settings window using one of these methods:

**Method 1 - Menu Bar:**
1. Click **Xcode** in the top menu bar
2. Select **Settings** (or **Preferences** in some Xcode versions)

**Method 2 - Keyboard Shortcut:**
1. Press **⌘ + ,** (Command + Comma)

This opens the Xcode Settings window where you can configure various aspects of your development environment.

### Step 2: Navigate to the Intelligence Tab
In the Settings window:

1. Look for the **Intelligence** tab in the sidebar or top navigation
2. Click on **Intelligence** to open the Intelligence settings panel

The Intelligence tab is typically located among other preference categories like General, Accounts, Behaviors, and Navigation.

### Step 3: Verify Intelligence Feature Availability
Once in the Intelligence settings panel, you should see:

- A toggle or checkbox to **Enable Coding Intelligence**
- Configuration options for code generation levels (Minimal, Balanced, Comprehensive)
- An **Add a Model Provider** button for connecting external AI services

If you see a message indicating "Intelligence Features not available in your region" or "Intelligence not available," verify that:
- You're running macOS 26 Tahoe or later
- Apple Intelligence is enabled in System Settings > Apple Intelligence & Siri
- Your system region is set to a supported location (primarily United States)

### Step 4: Configure Intelligence Settings (Optional)
After accessing the Intelligence tab, you can customize your AI coding assistant:

1. **Enable Coding Intelligence:** Check the box or toggle the switch to activate the feature
2. **Set Code Generation Level:** Choose from:
   - **Minimal:** Conservative suggestions with less AI intervention
   - **Balanced:** Moderate AI assistance (recommended for most users)
   - **Comprehensive:** Maximum AI-powered code generation and suggestions
3. **Add Model Providers:** Click "Add a Model Provider" to integrate services like:
   - ChatGPT (GPT-4.1, GPT-5)
   - Claude (Sonnet 4)
   - Custom OpenAI-compatible endpoints

## Expected Results
After successfully accessing the Intelligence settings tab, you should:

- See the Intelligence configuration panel with all available options
- Be able to enable or disable Coding Intelligence features
- Have the ability to configure code generation preferences
- Access the interface to add and manage AI model providers
- See status indicators showing whether Intelligence features are active and available

When properly configured, Coding Intelligence will provide inline code suggestions, help fix bugs, generate code snippets, and offer contextual assistance while you develop.

## Troubleshooting

### Common Issue 1
**Problem:** Intelligence tab is not visible in Xcode Settings
**Solution:**
- Verify you're running Xcode 26 or later (check Xcode > About Xcode)
- Ensure your macOS version is 26 Tahoe or later (check  > About This Mac)
- Update to the latest version of both if needed

### Common Issue 2
**Problem:** "Intelligence Features not available in your region" message
**Solution:**
- Open System Settings > Apple Intelligence & Siri
- Verify Apple Intelligence is turned on
- Check that your system region is set to a supported location (primarily US)
- If you recently updated macOS, restart your Mac to ensure all services are initialized

### Common Issue 3
**Problem:** Performance issues or slow response when using Intelligence features
**Solution:**
- Update to Xcode 26.1.1, which includes significant performance improvements for Coding Intelligence
- Check that your AI model provider (ChatGPT, Claude, etc.) API is responding properly
- Reduce the code generation level from Comprehensive to Balanced if experiencing slowdowns
- Close unnecessary projects or editor tabs to reduce system load

## Additional Tips
- The Settings window can be kept open while working, allowing quick access to Intelligence configurations
- You can quickly toggle Intelligence features on/off without closing your project
- If you use multiple AI model providers, you can switch between them within the Intelligence settings
- Xcode 26.1.1 includes bug fixes specifically addressing performance issues when the AI assistant applies code changes
- The Intelligence tab also provides access to configure custom local AI models if you prefer not to use cloud-based services

## Related Articles
- KB-001: How to enable predictive code completion in Xcode (when available)
- KB-002: How to use AI-powered code suggestions in Xcode (when available)
- Related articles about Xcode Intelligence features will be linked as they become available

## Sources
- Apple releases Xcode 26.1.1 with coding intelligence improvements - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- Xcode 26.1.1 Update Includes Coding Intelligence Improvements - https://www.mactrast.com/2025/11/xcode-26-1-1-update-includes-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- Writing code with intelligence in Xcode - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- Google Search Query: "Xcode 26 Intelligence settings tab preferences" (November 17, 2025)
- Google Search Query: "Xcode 26.1 preferences Intelligence tab keyboard shortcut access" (November 17, 2025)

---
*This article is part of the Claude Code Web knowledge base*
