# How to enable Apple Intelligence in macOS System Settings for Coding Intelligence

**Article ID:** KB-003
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 5-10 minutes

## Overview

Apple Intelligence is the foundation that powers Xcode 26's Coding Intelligence features, including AI-powered code generation, intelligent explanations, automated bug fixing, and documentation generation. This guide walks you through enabling Apple Intelligence in macOS System Settings, which is a prerequisite for using the Coding Assistant and other AI-powered development features in Xcode 26.1+.

Without Apple Intelligence enabled, Xcode's Coding Intelligence features will be unavailable, and you won't be able to access ChatGPT, Claude, or other AI model integrations within the IDE.

## Prerequisites

Before enabling Apple Intelligence, ensure you meet these requirements:

### Hardware Requirements
- **Mac with Apple Silicon:** M1, M1 Pro, M1 Max, M1 Ultra, M2, M2 Pro, M2 Max, M2 Ultra, M3, M3 Pro, M3 Max, M4, or newer
- **Storage:** At least 7 GB of available storage space for Apple Intelligence models

### Software Requirements
- **macOS Version:** macOS Sequoia 15.5 or later (macOS Tahoe 26.0+ recommended for full Coding Intelligence features)
- **Xcode:** Version 26.1 or later installed from the Mac App Store

### Regional and Language Requirements
- **Supported Region:** Your Mac must not be physically located in China mainland
- **Apple Account:** Your Apple Account Country/Region must not be set to China mainland
- **Language Settings:** Device language and Siri language must be set to the same supported language

### Supported Languages (as of November 2025)
English (US, UK, Australia, Canada, India, New Zealand, Singapore, South Africa), Chinese (Simplified, Traditional), Danish, Dutch, French, German, Italian, Japanese, Korean, Norwegian, Portuguese, Spanish, Swedish, Turkish, Vietnamese

## Steps

### Step 1: Check Your Mac's Compatibility

Before proceeding, verify that your Mac has Apple Silicon and meets the storage requirements.

1. Click the Apple menu () in the top-left corner
2. Select **About This Mac**
3. Check the **Chip** field to confirm you have an M-series processor (M1 or newer)
4. Verify you have at least 7 GB of available storage space

**If you see "Intel" instead of an M-series chip:** Apple Intelligence is not available on Intel-based Macs. You'll need an Apple Silicon Mac to use Coding Intelligence features in Xcode 26.

### Step 2: Update to a Compatible macOS Version

Ensure you're running macOS Sequoia 15.5 or later (macOS Tahoe 26.0+ recommended).

1. Click the Apple menu () and select **System Settings**
2. Click **General** in the sidebar
3. Click **Software Update**
4. If an update is available, click **Update Now** or **Upgrade Now**
5. Wait for the update to complete and restart your Mac

**Note:** macOS Tahoe (v26) provides the full Coding Intelligence experience with enhanced on-device processing and voice coding capabilities.

### Step 3: Configure Language Settings

Apple Intelligence requires that your device language and Siri language match.

1. Open **System Settings**
2. Click **General** in the sidebar
3. Click **Language & Region**
4. Verify your **Preferred Language** is set to a supported language (see list above)
5. In the sidebar, click **Siri & Spotlight**
6. Ensure the **Language** setting matches your device language
7. If you made changes, restart your Mac

### Step 4: Enable Apple Intelligence

On macOS Sequoia 15.3 and later, Apple Intelligence is typically enabled by default. However, you can verify and manually enable it if needed.

#### Option A: Via System Settings (Primary Method)

1. Open **System Settings** (click the Apple menu and select **System Settings**)
2. Click **Apple Intelligence & Siri** in the sidebar
3. Look for the **Apple Intelligence** section at the top
4. If you see a toggle switch or button next to "Apple Intelligence":
   - Click the toggle to **turn it ON** (it should turn blue/green)
   - Or click **Turn on Apple Intelligence**
5. A dialog may appear explaining Apple Intelligence features
6. Click **Enable Apple Intelligence** or **Continue**
7. Wait for the initial download and setup (this may take several minutes)

#### Option B: Via Spotlight Search (Alternative Method)

1. Press **Cmd + Space** to open Spotlight Search
2. Type `Apple Intelligence`
3. Click **Apple Intelligence** in the search results
4. This opens System Settings directly to the Apple Intelligence & Siri pane
5. Follow the same steps as Option A above

### Step 5: Wait for Initial Model Download

After enabling Apple Intelligence for the first time:

1. You'll see a progress indicator showing "Preparing Apple Intelligence..."
2. The system will download required AI models (approximately 7 GB)
3. This process can take 5-15 minutes depending on your internet connection
4. Your Mac may need to restart after the download completes
5. You can continue using your Mac during the download, but AI features won't be available yet

**Progress Check:** Look for a notification or status indicator in System Settings showing the download progress.

### Step 6: Verify Apple Intelligence is Active

Once the download completes:

1. Return to **System Settings > Apple Intelligence & Siri**
2. Confirm that Apple Intelligence shows as **ON** or **Enabled**
3. You should see a checkmark or confirmation indicator
4. Additional Apple Intelligence features should now be visible in System Settings

### Step 7: Launch Xcode and Verify Coding Intelligence Access

Now that Apple Intelligence is enabled, verify that Xcode can access Coding Intelligence features:

1. Open **Xcode 26.1** or later
2. Go to **Xcode > Settings** (or press **Cmd + ,**)
3. Click the **Intelligence** tab in the Settings window
4. You should see:
   - **Coding Intelligence** section with available AI models
   - **ChatGPT** listed as a default model provider
   - Options to add custom model providers (Claude, Gemini, etc.)
5. If you see these options, Apple Intelligence is successfully enabled for Xcode

## Expected Results

After successfully enabling Apple Intelligence, you should observe:

### In System Settings
- **Apple Intelligence & Siri** settings show Apple Intelligence as **ON** or **Enabled**
- Additional AI-powered features appear throughout macOS (Writing Tools, Image Playground, etc.)
- Siri gains enhanced capabilities with on-screen awareness

### In Xcode 26.1+
- **Coding Assistant** panel is accessible (typically **Cmd + Shift + A** or via menu)
- Right-click context menu in code editor shows AI options:
  - "Explain Selected Code"
  - "Generate Tests"
  - "Generate Documentation"
  - "Fix Error" (when applicable)
- **Intelligence** tab appears in Xcode Settings
- ChatGPT model is available by default (with optional free tier)
- Ability to add custom model providers (Claude, OpenRouter, local models)

### Coding Intelligence Features Unlocked
- Natural language code generation
- AI-powered code explanations
- Automated test generation
- Documentation comment generation
- Intelligent bug fixing suggestions
- Architecture and refactoring guidance
- Multi-model AI provider support

## Troubleshooting

### Common Issue 1: "Apple Intelligence is not available" Message

**Problem:** System Settings shows "Apple Intelligence is not available on this Mac" or the option doesn't appear.

**Solutions:**
- Verify you have an Apple Silicon Mac (M1 or newer). Check **About This Mac** under the Apple menu
- Ensure you're running macOS Sequoia 15.5 or later. Update via **System Settings > General > Software Update**
- Check regional settings: Your Mac cannot be in China mainland, and your Apple Account region must not be set to China mainland
- Restart your Mac and check again
- If you recently updated macOS, wait 10-15 minutes and check again—features may still be initializing

### Common Issue 2: Download Stuck or Failed

**Problem:** Apple Intelligence model download appears stuck at a certain percentage or fails with an error.

**Solutions:**
- Check your internet connection is stable and active
- Verify you have at least 7 GB of free storage space available
- Pause and resume the download:
  1. Go to **System Settings > Apple Intelligence & Siri**
  2. Turn OFF Apple Intelligence
  3. Wait 30 seconds
  4. Turn ON Apple Intelligence again
- Clear system caches and try again:
  1. Restart your Mac
  2. Re-enable Apple Intelligence
- If the download repeatedly fails, try downloading during off-peak hours when network traffic is lower

### Common Issue 3: Xcode Doesn't Show Coding Intelligence Options

**Problem:** Apple Intelligence is enabled in System Settings, but Xcode 26 doesn't show the Intelligence tab or Coding Assistant features.

**Solutions:**
- Verify you're running **Xcode 26.1 or later**. Earlier versions don't support Coding Intelligence
  - Check version: **Xcode > About Xcode**
  - Update via Mac App Store if needed
- Completely quit and restart Xcode (not just close windows)
  - Press **Cmd + Q** to quit Xcode
  - Relaunch from Applications or Spotlight
- Reset Xcode preferences:
  1. Quit Xcode
  2. In Terminal, run: `defaults delete com.apple.dt.Xcode`
  3. Restart Xcode
- Check that Apple Intelligence download completed successfully in System Settings
- Ensure your Mac hasn't been set to restrict Apple Intelligence:
  1. Go to **System Settings > Screen Time > Content & Privacy**
  2. Ensure Apple Intelligence is not restricted

### Common Issue 4: Language Mismatch Error

**Problem:** Apple Intelligence won't enable, showing a language compatibility error.

**Solutions:**
- Ensure device language and Siri language match:
  1. **System Settings > General > Language & Region** - note your preferred language
  2. **System Settings > Siri & Spotlight > Language** - set to match preferred language
- Both must be set to one of the supported languages (see Prerequisites section)
- After changing language settings, restart your Mac
- If using English, ensure both are set to the same English variant (e.g., both "English (United States)")

## Additional Tips

- **Default Enabled:** On macOS Sequoia 15.3 and later, Apple Intelligence is enabled by default for new installations. You may only need to verify it's ON rather than manually enabling it.

- **Privacy Considerations:** Apple Intelligence processes most requests on-device when possible. When using cloud AI models in Xcode (ChatGPT, Claude), your code snippets are sent to those providers. Review their privacy policies if working with sensitive code.

- **Free vs. Paid ChatGPT:** Xcode includes free-tier access to ChatGPT without creating an account, but with rate limits. Connect a paid OpenAI account in **Xcode > Settings > Intelligence** for higher limits.

- **Multiple AI Models:** Once Apple Intelligence is enabled, you can add Claude, Gemini, OpenRouter, or local models (Ollama, LM Studio) as additional providers in Xcode's Intelligence settings. This gives you flexibility to choose the best AI for each task.

- **Storage Management:** The 7 GB used by Apple Intelligence models is stored in system storage. If you later disable Apple Intelligence, these models are automatically removed to free up space.

- **Offline Capabilities:** Predictive code completion in Xcode works offline using on-device models. However, the Coding Assistant (chat interface) requires internet connectivity when using cloud-based AI models like ChatGPT or Claude.

- **macOS Updates:** Apple regularly updates Apple Intelligence capabilities. Keep your Mac updated to receive the latest features, model improvements, and language support.

- **Voice Coding:** If you're using macOS Tahoe (v26), Apple Intelligence also enables Voice Coding features in Xcode, allowing you to dictate Swift code and navigate the IDE using voice commands via Voice Control.

## Related Articles

- KB-001: How to download and install Xcode 26.1+ from the Mac App Store *(if available)*
- KB-002: How to verify Xcode installation and check the version number *(if available)*
- KB-007: How to access the new Intelligence settings tab in Xcode preferences *(if available)*
- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT *(if available)*
- KB-041: How to create an Anthropic API account at console.anthropic.com *(if available)*
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings *(if available)*

## Sources

- **How to get Apple Intelligence - Apple Support** - https://support.apple.com/en-us/121115 (Accessed: November 17, 2025)
- **Use Apple Intelligence on your Mac - Apple Support** - https://support.apple.com/guide/mac-help/get-started-with-apple-intelligence-mchl46361784/mac (Accessed: November 17, 2025)
- **macOS 15.3 Released With Apple Intelligence on by Default** - https://isc.upenn.edu/news/macos-153-released-apple-intelligence-default-1272025 (Accessed: November 17, 2025)
- **Apple releases Xcode 26.1.1 with coding intelligence improvements - 9to5Mac** - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- **Writing code with intelligence in Xcode - Apple Developer Documentation** - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- **Apple brings ChatGPT and other AI models to Xcode - TechCrunch** - https://techcrunch.com/2025/06/09/apple-brings-chatgpt-and-other-ai-models-to-xcode/ (Accessed: November 17, 2025)
- **Apple supercharges its tools and technologies for developers** - https://www.apple.com/newsroom/2025/06/apple-supercharges-its-tools-and-technologies-for-developers/ (Accessed: November 17, 2025)

---
*This article is part of the Claude Code Web knowledge base*
