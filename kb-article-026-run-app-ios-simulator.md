# How to run your app on the iOS Simulator

**Article ID:** KB-026
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 5-10 minutes

## Overview

This guide shows you how to build and run your iOS app on the iOS Simulator in Xcode 26.1+. The iOS Simulator is a virtual iOS device that runs on your Mac, allowing you to test your app without needing a physical iPhone or iPad. This is essential for rapid development and testing, as you can quickly see how your app looks and behaves on different device types and iOS versions without deploying to actual hardware.

The iOS Simulator in Xcode 26.1+ supports iPhone, iPad, and iPod touch devices running iOS 26.1, with the ability to download additional runtime versions for testing on older iOS versions. The simulator provides features like touch gestures (via mouse/trackpad), hardware keyboard input, location simulation, and accessibility testing.

## Prerequisites

Before running your app on the iOS Simulator, ensure you have:

- **Xcode 26.1+** installed and configured (see KB-001)
- **An iOS app project** open in Xcode (see KB-008 to create your first project)
- **iOS Simulator** installed (automatically included with Xcode)
- **Valid project configuration** with no critical build errors
- **Mac with Apple Silicon** (M1 or later) recommended for best performance
- At least **5 GB of free disk space** for simulator runtimes

**Note:** While Intel-based Macs can run the iOS Simulator, Apple Silicon Macs provide significantly better simulator performance. Xcode 26 defaults to downloading arm64 simulator runtimes optimized for Apple Silicon.

## Steps

### Step 1: Open Your Project in Xcode

1. Launch **Xcode 26.1** from your Applications folder
2. Open your iOS app project using one of these methods:
   - Select **Open a project or file** from the welcome window
   - Go to **File** > **Open** and navigate to your project
   - Double-click your `.xcodeproj` or `.xcworkspace` file in Finder
3. Wait for Xcode to load your project and index the files

**Tip:** If you don't have a project yet, follow KB-008 to create your first iOS app using the App template.

### Step 2: Choose a Simulator Destination

The destination selector in Xcode's toolbar lets you choose which device (simulator or physical device) to run your app on.

1. Look at the **toolbar** at the top of the Xcode window
2. Find the **destination selector** near the center-left (next to the scheme selector)
   - It displays the currently selected device, like "iPhone 16 Pro"
   - The format is: `[Scheme Name] > [Destination]`
3. Click the **destination selector** to open the destination menu

**Understanding the Destination Menu:**

The destination menu shows available simulators organized by device type:
- **iOS Simulators** section lists all available iPhone and iPad simulators
- **My Mac (Designed for iPad)** allows running iPad apps on macOS
- **Any iOS Device** is for generic builds
- Each simulator shows the device name, iOS version, and status

**Common Simulator Choices:**
- **iPhone 16 Pro** - Latest flagship iPhone (6.9" display)
- **iPhone 16** - Standard latest iPhone (6.7" display)
- **iPhone SE (3rd generation)** - Smaller device (4.7" display)
- **iPad Pro (13-inch)** - Large tablet
- **iPad Air (11-inch)** - Standard tablet

### Step 3: Select Your Preferred Simulator

1. In the destination menu, browse the available simulators
2. Click on your desired simulator, for example:
   - **iPhone 16 Pro** for testing on the latest flagship device
   - **iPhone SE (3rd generation)** for testing on a smaller screen
   - **iPad Air (11-inch)** for tablet testing

3. The destination selector will update to show your selection

**If your desired simulator isn't listed:**

1. Click **Add Additional Simulators...** at the bottom of the destination menu
2. Or go to **Window** > **Devices and Simulators** (or press **Shift + Command + 2**)
3. Click the **Simulators** tab
4. Click the **+** button in the bottom-left corner
5. Configure:
   - **Simulator Name:** Give it a descriptive name (e.g., "iPhone 16 Pro Max")
   - **Device Type:** Choose from the dropdown (iPhone, iPad, etc.)
   - **OS Version:** Select the iOS version (iOS 26.1, iOS 25.0, etc.)
6. Click **Create**

**Downloading Additional iOS Runtimes:**

If you need to test on older iOS versions:

1. Go to **Xcode** > **Settings** (or press **Command + ,**)
2. Click the **Platforms** tab
3. Click the **+** button or **Get** next to the iOS version you need
4. Wait for the runtime to download (this can be several GB)
5. New simulators with that iOS version will become available

```bash
# You can also download runtimes via command line:
xcodebuild -downloadPlatform iOS

# Or for universal (Rosetta) runtime on Apple Silicon:
xcodebuild -downloadPlatform iOS -architectureVariant universal
```

### Step 4: Build and Run Your App

Once you've selected your simulator destination:

1. Click the **Run button** (▶) in the top-left corner of Xcode's toolbar
   - Or press **Command + R** (the universal build and run shortcut)
   - Or go to **Product** > **Run** from the menu bar

2. Xcode will begin the build process:
   - The toolbar shows **Build** with a progress indicator
   - The build progress appears in the activity viewer (center of toolbar)
   - Build logs appear in the **Report Navigator** (⌘ + 9, then click the latest build)

3. Watch for build completion:
   - **Build Succeeded** - The build completed without errors
   - **Build Failed** - There are errors preventing the build (see Step 7)

**What happens during the build:**

- Xcode compiles your Swift/Objective-C source code
- Links frameworks and libraries
- Processes resources (images, storyboards, etc.)
- Code signs the app bundle
- Creates the `.app` package

Typical build times:
- Small project (first build): 10-30 seconds
- Small project (incremental): 2-5 seconds
- Large project (first build): 1-5 minutes
- Large project (incremental): 10-30 seconds

### Step 5: Wait for Simulator to Launch

After a successful build:

1. The **iOS Simulator** app will launch automatically (if not already running)
2. The simulator window displays the selected device (e.g., iPhone 16 Pro)
3. You'll see the iOS boot sequence:
   - Apple logo
   - Boot animation (first launch only)
   - Home screen or lock screen

4. Your app's icon appears briefly with a loading indicator
5. Your app launches and displays its initial screen

**First-time simulator launch:**
- May take 30-60 seconds for the iOS Simulator to boot
- Subsequent launches are much faster (5-10 seconds)
- The simulator stays running between app launches for speed

**Simulator Window Features:**
- The window shows a realistic device bezel (hardware appearance)
- The status bar shows time, battery, signal strength (simulated)
- You can interact using your mouse/trackpad to simulate touch

### Step 6: Interact with Your App in the Simulator

Once your app is running in the simulator:

**Basic Interactions:**
- **Click** with your mouse to simulate a tap
- **Click and drag** to simulate swipe gestures
- **Scroll** with trackpad/mouse wheel for scrolling
- **Type** with your keyboard when text fields are active
- **Click and hold** to simulate long press

**Simulator Gestures:**
- **Home button:** Go to **Hardware** > **Home** (or **Command + Shift + H**)
- **App Switcher:** **Command + Shift + H** (twice quickly)
- **Rotate device:** **Command + Left/Right Arrow** (or **Hardware** > **Rotate**)
- **Shake gesture:** **Hardware** > **Shake Gesture** (or **Control + Command + Z**)
- **Lock screen:** **Command + L** (or **Hardware** > **Lock**)
- **Screenshot:** **Command + S** (saves to Desktop)
- **Record screen:** **Command + R** (creates video)

**Developer Features:**
- **Slow animations:** **Debug** > **Slow Animations** (or **Command + T**)
- **Debug location:** **Features** > **Location** > Choose location
- **Toggle appearance:** **Features** > **Toggle Appearance** (Dark/Light mode)
- **Accessibility Inspector:** **Xcode** > **Open Developer Tool** > **Accessibility Inspector**

**Testing Tips:**
- Test on multiple device sizes (small, standard, large)
- Test in both portrait and landscape orientations
- Test in Dark Mode and Light Mode
- Test with different dynamic type sizes (accessibility)
- Test location-based features using simulated locations

### Step 7: Stop Your App

To stop your running app:

1. Click the **Stop button** (■) in Xcode's toolbar (next to the Run button)
   - Or press **Command + .** (period)
   - Or go to **Product** > **Stop**

2. Your app will immediately terminate in the simulator
3. The simulator returns to the home screen
4. Xcode's status shows "Ready" in the toolbar

**Note:** The simulator itself continues running. You can leave it running for faster subsequent launches, or quit it via **Simulator** > **Quit Simulator** (or **Command + Q** while simulator is focused).

### Step 8: Make Changes and Re-run

During development, you'll frequently modify code and re-run:

1. **Make changes** to your Swift code, UI files, or resources
2. **Save your changes** (**Command + S**)
3. **Run again** (**Command + R**)
   - Xcode automatically rebuilds only changed files (incremental build)
   - The app relaunches with your changes

**Hot Reload (SwiftUI Previews):**

For even faster iteration with SwiftUI:

1. Open a SwiftUI view file
2. Click **Resume** in the preview pane (or **Option + Command + P**)
3. Make changes to your code
4. Previews update automatically without full rebuild

**Tip:** Use SwiftUI Previews for UI development, then run on simulator for full app testing including navigation, networking, and device features.

## Expected Results

After successfully running your app on the iOS Simulator, you should see:

**In Xcode:**
- **Build succeeded** message in the activity viewer
- No error indicators in the source code editor
- Debug console showing app launch logs (bottom panel)
- Debug session active (indicated by debug controls in toolbar)

**In iOS Simulator:**
- Your app launches and displays the initial screen
- App responds to user interactions (taps, swipes, typing)
- Navigation and UI elements work as expected
- Simulator accurately represents the selected device size and iOS version

**Console Output Example:**

```
Build succeeded
Installing /Users/yourname/Library/Developer/Xcode/DerivedData/MyApp-xxx/Build/Products/Debug-iphonesimulator/MyApp.app
Running MyApp.app
[MyApp] [DEBUG] App launched successfully
[MyApp] [DEBUG] Loaded main view
```

**Performance Expectations:**
- Smooth animations at 60 FPS (or 120 FPS on ProMotion simulators)
- Quick app launch (1-3 seconds)
- Responsive UI interactions
- Console logs showing app lifecycle events

## Troubleshooting

### Common Issue 1: Build Failed with Errors

**Problem:** Clicking Run produces "Build Failed" with red error messages

**Solution:**
1. Review errors in the **Issue Navigator** (⌘ + 5)
2. Click on each error to see details and suggested fixes
3. Common build errors:
   - **Syntax errors:** Fix typos, missing brackets, incorrect Swift syntax
   - **Missing files:** Ensure all referenced files exist in the project
   - **Framework issues:** Check framework imports and linking
   - **Code signing:** Verify signing settings in project settings

4. Fix the errors and try building again (**Command + B** to build without running)

**Quick Fix - Clean Build:**
```bash
# Clean build folder:
# Xcode menu: Product > Clean Build Folder (Shift + Command + K)

# Or via command line:
rm -rf ~/Library/Developer/Xcode/DerivedData
```

### Common Issue 2: Simulator Doesn't Appear in Destination Menu

**Problem:** No simulators are listed when you click the destination selector

**Solution:**

1. **Verify simulators are installed:**
   - Go to **Window** > **Devices and Simulators** (**Shift + Command + 2**)
   - Click the **Simulators** tab
   - Ensure at least one simulator exists

2. **Create a new simulator if needed:**
   - Click **+** in the Devices and Simulators window
   - Configure device type and iOS version
   - Click **Create**

3. **Check for architecture conflicts (Xcode 26 specific):**
   - Open project settings (click project name in navigator)
   - Select your app target
   - Go to **Build Settings** tab
   - Search for "Excluded Architectures"
   - Ensure **arm64** is NOT listed under **Excluded Architectures > Debug > Any iOS Simulator SDK**
   - If arm64 is excluded, delete it

4. **Download iOS platform if missing:**
   - Go to **Xcode** > **Settings** > **Platforms**
   - Ensure iOS 26.1 (or your target version) shows as "Installed"
   - Click **Get** if it shows as available but not installed

```bash
# Verify available simulators from Terminal:
xcrun simctl list devices available

# Check installed runtimes:
xcrun simctl list runtimes
```

### Common Issue 3: Simulator Launches but App Doesn't Install

**Problem:** Simulator opens but app doesn't appear or install

**Solution:**

1. **Check build output in console:**
   - Show the debug console (**Command + Shift + Y**)
   - Look for installation errors

2. **Reset simulator:**
   - While simulator is running: **Device** > **Erase All Content and Settings**
   - Or from Terminal:
   ```bash
   # Shut down all simulators:
   xcrun simctl shutdown all

   # Erase specific simulator (get UUID from simctl list):
   xcrun simctl erase <device-uuid>

   # Or erase all simulators:
   xcrun simctl erase all
   ```

3. **Restart simulator app:**
   - Quit Simulator (**Command + Q**)
   - Run your app again from Xcode

4. **Check deployment target:**
   - In project settings, verify **iOS Deployment Target** matches or is lower than your simulator's iOS version
   - For example, if simulator runs iOS 26.1, deployment target should be 26.1 or lower

### Common Issue 4: "Unable to Boot Device" Error

**Problem:** Simulator shows error: "Unable to boot device in current state: Booted"

**Solution:**

```bash
# Shutdown all simulators:
xcrun simctl shutdown all

# Kill any hung simulator processes:
killall -9 com.apple.CoreSimulator.CoreSimulatorService

# Restart Xcode and try again
```

If the issue persists:

```bash
# Delete simulator data (WARNING: removes all simulator data):
rm -rf ~/Library/Developer/CoreSimulator/Devices

# Restart Mac
# Re-run your app (Xcode will create new simulator data)
```

### Common Issue 5: App Crashes Immediately on Launch

**Problem:** App installs but crashes immediately when launched

**Solution:**

1. **Check crash logs:**
   - In Xcode, go to **Window** > **Devices and Simulators**
   - Select the simulator from the left panel
   - Click **View Device Logs**
   - Find your app's crash log and examine the error

2. **Common crash causes:**
   - **Force unwrapping nil optionals:** Check for `!` usage on optionals
   - **Missing required resources:** Ensure all images, files are in bundle
   - **Incompatible iOS version:** Check minimum deployment target
   - **Framework compatibility:** Verify all frameworks support iOS 26.1

3. **Enable exception breakpoint:**
   - Go to **Breakpoint Navigator** (⌘ + 8)
   - Click **+** at bottom
   - Choose **Exception Breakpoint**
   - This will pause execution when exceptions occur, showing you exactly where the crash happens

4. **Debug in console:**
   - Look at the console output (**Command + Shift + Y**)
   - Error messages often indicate the exact problem

### Common Issue 6: Slow Simulator Performance

**Problem:** Simulator runs slowly, animations are choppy

**Solution:**

1. **Close unused simulators:**
   - Only run one simulator at a time
   - Quit simulator when not needed

2. **Allocate more resources:**
   - Close other resource-intensive applications
   - Ensure your Mac has sufficient free RAM (check Activity Monitor)

3. **Use appropriate simulator:**
   - Simpler devices (iPhone SE) perform better than complex ones (iPhone 16 Pro Max)
   - Avoid running iPad simulators if you only need iPhone testing

4. **Check Graphics/Metal settings:**
   - Ensure your Mac's GPU is active (not integrated graphics only)
   - For MacBooks, ensure you're plugged into power for best GPU performance

5. **Reset Derived Data:**
   ```bash
   rm -rf ~/Library/Developer/Xcode/DerivedData
   ```

**Note:** Apple Silicon Macs (M1, M2, M3, M4) provide significantly better simulator performance than Intel Macs. If you're on an older Intel Mac, consider upgrading for better development experience.

### Common Issue 7: Command+R Starts Screen Recording Instead of Running App

**Problem:** Pressing Command+R in Simulator starts video recording instead of building/running app

**Solution:**

1. **Ensure Xcode is the active application:**
   - Click on Xcode window to make it active
   - Then press **Command + R**

2. **Explanation:**
   - In **Xcode**: **Command + R** = Build and run
   - In **Simulator**: **Command + R** = Start screen recording
   - The shortcut executes in whichever app is currently focused

3. **Alternative:** Click the Run button in Xcode's toolbar to avoid shortcut confusion

## Additional Tips

### Simulator Management

- **Multiple simulators:** You can run multiple simulators simultaneously, but it consumes significant RAM. Use only when necessary for testing.
- **Simulator naming:** Give simulators descriptive names like "iPhone 16 Pro - Development" vs "iPhone 16 Pro - Testing" to organize different configurations.
- **Simulator presets:** Create simulators for each target device you plan to support for quick testing.

### Keyboard and Hardware Features

- **Hardware keyboard:** By default, the simulator uses your Mac's keyboard. Toggle this with **I/O** > **Keyboard** > **Connect Hardware Keyboard** (or **Command + Shift + K**).
- **Software keyboard:** When hardware keyboard is disconnected, the iOS software keyboard appears. This is useful for testing keyboard interactions.
- **Touch Bar simulation:** If your app uses Touch Bar features, test on MacBook Pro simulators.

### Performance Testing

- **Profile with Instruments:** Use **Product** > **Profile** (⌘ + I) to launch Instruments for performance analysis.
- **Memory debugging:** Enable memory graph debugger during runtime to detect leaks.
- **Network Link Conditioner:** Simulate slow network connections via **Settings** > **Developer** > **Network Link Conditioner** in the simulator.

### Location Testing

Test location-based features without leaving your desk:

1. While simulator is running, go to **Features** > **Location**
2. Choose from preset locations:
   - Apple Park (Cupertino, CA)
   - London, England
   - Tokyo, Japan
   - Custom Location (enter coordinates)
3. Your app receives simulated location updates

### Dark Mode and Appearance Testing

Quickly test both appearances:

1. **Features** > **Toggle Appearance** (or **Control + Command + A**)
2. Or enable automatic switching in simulator Settings app
3. Ensure your app's UI looks correct in both modes

### Debugging Tips

- **Print debugging:** Use `print()` statements to log values to the console
- **Breakpoints:** Click line numbers in Xcode to add breakpoints, pause execution, and inspect variables
- **LLDB commands:** Use the LLDB console to inspect and modify runtime state
- **View debugging:** **Debug** > **View Debugging** > **Capture View Hierarchy** to inspect UI layout

### Xcode 26 Specific Features

- **Coding Intelligence:** Use AI-powered code suggestions while developing your app (see KB-007)
- **Enhanced Previews:** SwiftUI previews are faster and more reliable in Xcode 26.1
- **Improved Build Times:** Xcode 26 includes build system optimizations for faster incremental builds
- **Better Simulator Integration:** Simulators boot faster and have better performance on Apple Silicon

### Build Schemes

- **Debug vs Release:** By default, you run Debug builds. Release builds are optimized and have better performance but are harder to debug.
- **Scheme switching:** Click the scheme selector (left of destination) to switch schemes or edit schemes.
- **Custom schemes:** Create schemes for different environments (development, staging, production).

### Testing on Different iOS Versions

To ensure compatibility:

1. Download multiple iOS runtimes (e.g., iOS 25.0, iOS 26.1)
2. Create simulators for each iOS version
3. Test your app on each simulator
4. Verify features work across all supported versions

## Related Articles

- KB-001: How to download and install Xcode 26.1+ from the Mac App Store
- KB-002: How to verify Xcode installation and check the version number
- KB-006: How to understand and navigate the Xcode 26 interface
- KB-008: How to create your first iOS app project using the App template
- KB-010: How to set up signing and capabilities for your app project
- KB-011: How to write your first Hello World SwiftUI view
- KB-027: How to run your app on a physical iOS device (coming soon)
- KB-028: How to debug your iOS app using breakpoints and LLDB (coming soon)
- KB-029: How to use SwiftUI previews for faster development iteration (coming soon)

## Sources

- Apple Developer Documentation - Running Your App in Simulator or on a Device - https://developer.apple.com/documentation/xcode/running-your-app-in-simulator-or-on-a-device (Accessed: November 17, 2025)
- Apple Developer Documentation - Xcode 26 Release Notes - https://developer.apple.com/documentation/xcode-release-notes/xcode-26-release-notes (Accessed: November 17, 2025)
- Apple Developer Documentation - Xcode 26.1.1 Release Notes - https://developer.apple.com/documentation/xcode-release-notes/xcode-26_1-release-notes (Accessed: November 17, 2025)
- Apple Developer Documentation - Troubleshooting Simulator Launch or Animation Issues - https://developer.apple.com/documentation/xcode/troubleshooting-simulator-launch-or-animation-issues (Accessed: November 17, 2025)
- Stack Overflow - Rosetta Simulator Run Destination not showing in Xcode 26 - https://stackoverflow.com/questions/79773248/rosetta-simulator-run-destination-not-showing-in-xcode-26 (Accessed: November 17, 2025)
- 9to5Mac - Apple releases Xcode 26.1.1 with coding intelligence improvements - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- Apple Developer Forums - Xcode 26.0.1 Simulator crashes - https://developer.apple.com/forums/thread/803262 (Accessed: November 17, 2025)

---
*This article is part of the Claude Code Web knowledge base*
