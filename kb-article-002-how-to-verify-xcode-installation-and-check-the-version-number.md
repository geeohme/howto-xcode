# How to verify Xcode installation and check the version number

**Article ID:** KB-002
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 5-10 minutes

## Overview

After installing Xcode 26.1 or later, it's essential to verify that the installation was successful and confirm you're running the correct version. This guide covers multiple methods to check your Xcode installation and version number using both the graphical interface and Terminal commands. These verification steps ensure you meet the requirements for iOS app development and AI-assisted coding features.

## Prerequisites

- macOS Sequoia 15.5 or later (macOS Tahoe v26 recommended for full Coding Intelligence features)
- M1 Mac or later (Apple Silicon required)
- Xcode 26.1+ downloaded from the Mac App Store
- Basic familiarity with Terminal (for command-line methods)

## Steps

### Step 1: Verify Installation Using Xcode GUI

The quickest way to check your Xcode installation is through the application itself.

1. Open **Spotlight Search** by pressing `Cmd + Space`
2. Type "Xcode" and press `Enter` to launch the application
3. Once Xcode opens, click **Xcode** in the menu bar (top-left)
4. Select **About Xcode** from the dropdown menu

A window will appear displaying:
- **Version number** (e.g., "Xcode 26.1.1")
- **Build version** (e.g., "17B100")
- **Copyright information**

**Example output:**
```
Version 26.1.1
Build version 17B100
```

This confirms Xcode is properly installed and shows the exact version you're running.

### Step 2: Check Version Using Terminal (xcodebuild command)

For quick verification or scripting purposes, use the `xcodebuild` command in Terminal.

1. Open **Terminal** (Applications > Utilities > Terminal)
2. Run the following command:

```bash
xcodebuild -version
```

**Expected output:**
```
Xcode 26.1.1
Build version 17B100
```

This displays both the Xcode version and build number. If you see an error message instead, Xcode may not be properly installed or the command-line tools aren't configured.

### Step 3: Verify Active Xcode Installation Path

If you have multiple Xcode versions installed, confirm which one is currently active using `xcode-select`.

```bash
xcode-select -p
```

**Expected output:**
```
/Applications/Xcode.app/Contents/Developer
```

This shows the path to the active Xcode installation. If the path points to a different Xcode version than you intend to use, you'll need to switch it (see Troubleshooting below).

### Step 4: Check Command Line Tools Installation

Verify that Xcode Command Line Tools are properly installed alongside Xcode.

```bash
xcode-select --install
```

**If already installed:**
```
xcode-select: error: command line tools are already installed, use "Software Update" in System Settings to install updates
```

**If not installed:**
The command will open a dialog to install the tools. Follow the on-screen instructions to complete installation.

### Step 5: Verify Xcode in System Information (Optional)

For detailed installation information, use macOS System Information.

1. Click the **Apple menu** (top-left corner)
2. Select **System Settings** (or **System Preferences** on older macOS)
3. Navigate to **General > About**
4. Click **System Report**
5. In the sidebar, expand **Software** and select **Applications**
6. Scroll down to find **Xcode** in the list

This shows:
- Version
- Last Modified date
- Location
- Installation size

## Expected Results

After completing these verification steps, you should see:

✓ **Xcode launches successfully** from Spotlight or Applications folder
✓ **About Xcode shows version 26.1 or higher** (current stable: 26.1.1)
✓ **Terminal commands execute without errors** and display version information
✓ **Active Xcode path points to** `/Applications/Xcode.app/Contents/Developer`
✓ **Command Line Tools are installed** and up to date

**For Xcode 26.1.1 (current stable release):**
- Version: 26.1.1
- Build: 17B100
- Includes Swift 6.2.1
- SDKs: iOS 26.1, iPadOS 26.1, tvOS 26.1, macOS 26.1, visionOS 26.1

## Troubleshooting

### Common Issue 1: "xcodebuild: command not found"

**Problem:** Running `xcodebuild -version` returns "command not found" error.
**Solution:** This indicates Command Line Tools aren't installed or properly configured.

1. Install Command Line Tools:
   ```bash
   xcode-select --install
   ```

2. After installation, verify the path:
   ```bash
   xcode-select -p
   ```

3. If the path is incorrect or empty, set it manually:
   ```bash
   sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
   ```

### Common Issue 2: Multiple Xcode Versions Installed

**Problem:** You have multiple Xcode versions and need to switch between them.
**Solution:** Use `xcode-select` to change the active version.

1. Check current active version:
   ```bash
   xcode-select -p
   ```

2. Switch to a different Xcode installation:
   ```bash
   sudo xcode-select --switch /Applications/Xcode-beta.app/Contents/Developer
   ```

3. Verify the change:
   ```bash
   xcodebuild -version
   ```

### Common Issue 3: Version Doesn't Match Mac App Store

**Problem:** Terminal shows an older version than what you just downloaded from the Mac App Store.
**Solution:** The download may still be in progress, or you need to switch the active Xcode.

1. Check Mac App Store to confirm download is complete
2. Verify Xcode.app is in `/Applications/` folder
3. Reset the Xcode path:
   ```bash
   sudo xcode-select --reset
   ```

4. Re-check version:
   ```bash
   xcodebuild -version
   ```

### Common Issue 4: Xcode Opens but Shows Older Version

**Problem:** About Xcode shows version older than 26.1.
**Solution:** You may have launched an older Xcode installation.

1. Completely quit Xcode (`Cmd + Q`)
2. In Finder, navigate to **Applications** folder
3. Check for multiple Xcode apps (Xcode.app, Xcode-legacy.app, etc.)
4. Rename or move old versions to avoid confusion
5. Launch the correct Xcode 26.1+ version
6. Verify in **Xcode > About Xcode**

## Additional Tips

- **Keep Xcode Updated:** Check the Mac App Store regularly for updates. Xcode 26.1.1 is the current stable release as of November 2025, with 26.2 in beta.

- **Check SDK Versions:** If you're targeting specific iOS versions, verify included SDKs by running:
  ```bash
  xcodebuild -showsdks
  ```

- **Disk Space Requirements:** Xcode 26.1+ requires approximately 40-50 GB of disk space. Ensure you have adequate free space before installing.

- **First Launch License Agreement:** The first time you launch Xcode after installation, you'll need to accept the license agreement. You can also accept it via Terminal:
  ```bash
  sudo xcodebuild -license accept
  ```

- **Xcode Beta Versions:** If testing beta features (e.g., Xcode 26.2 beta), install it alongside the stable version with a different name (e.g., "Xcode-beta.app") to avoid conflicts.

- **Swift Version Check:** To verify the included Swift compiler version:
  ```bash
  swift --version
  ```
  Expected for Xcode 26.1.1: Swift 6.2.1

- **Automatic Updates:** Enable automatic updates in System Settings > General > Software Update to receive Xcode updates promptly.

## Related Articles

- KB-001: How to download and install Xcode 26.1+ from the Mac App Store (when available)
- KB-003: How to enable Apple Intelligence in macOS System Settings for Coding Intelligence (when available)
- KB-007: How to access the new Intelligence settings tab in Xcode preferences (when available)

## Sources

- Apple Developer Documentation - Xcode 26.1.1 Release Notes - https://developer.apple.com/documentation/xcode-release-notes/xcode-26_1-release-notes (Accessed: November 17, 2025)
- Apple Developer Documentation - Xcode Release Notes Overview - https://developer.apple.com/documentation/xcode-release-notes/xcode-26-release-notes (Accessed: November 17, 2025)
- 9to5Mac - Apple releases Xcode 26.1.1 with coding intelligence improvements - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- The Mac Observer - Xcode 26.1.1 available with better app-building support - https://www.macobserver.com/news/xcode-26-1-1-available-with-better-app-building-support/ (Accessed: November 17, 2025)
- General knowledge: Standard xcodebuild and xcode-select command-line tools (stable across Xcode versions)

---
*This article is part of the Claude Code Web knowledge base*
