# How to run your app on a physical iPhone or iPad device

**Article ID:** KB-027
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 15-30 minutes

## Overview

Running your app on a physical iPhone or iPad device is essential for real-world testing and debugging. Unlike simulators, physical devices provide accurate performance metrics, access to hardware features (camera, GPS, sensors), and real user experience testing. This guide walks you through the complete process of configuring Xcode 26.1+, connecting your device, enabling Developer Mode, and successfully deploying your app for testing.

Starting with iOS 16 and continuing through iOS 26, Apple requires Developer Mode to be enabled on physical devices before you can install development builds from Xcode. This additional security step ensures that only intentional development activities can install unsigned or development-signed applications.

## Prerequisites

- Xcode 26.1+ installed on your Mac (see KB-001)
- An iOS app project with signing configured (see KB-010)
- An Apple ID added to Xcode (free or paid Developer Program)
- iPhone or iPad running iOS 16 or later (iOS 26+ recommended)
- USB-C to Lightning cable, USB-C to USB-C cable, or wireless network connection
- Device passcode enabled on your iPhone/iPad (required for Developer Mode)

**Important:** Developer Mode requires iOS 16 or later. If your device runs iOS 15 or earlier, you won't see the Developer Mode option, but you can still test apps after trusting the developer certificate.

## Steps

### Step 1: Connect Your Device to Your Mac

Connect your iPhone or iPad to your Mac using one of these methods:

**Via USB Cable (Recommended for First Setup):**

1. Use an appropriate cable for your devices:
   - **Lightning to USB-C:** For older iPhones/iPads with Lightning port to newer Macs
   - **USB-C to USB-C:** For newer iPhones (15 Pro and later) and iPads Pro
   - **Lightning to USB-A:** For older iPhones/iPads to older Macs (may require adapter)

2. Connect one end to your device and the other to your Mac

3. **On your iPhone/iPad:** A prompt will appear asking "Trust This Computer?"
   - Tap **Trust**
   - Enter your device passcode when prompted
   - This establishes a secure connection between your device and Mac

**Via Wireless Connection (After Initial USB Setup):**

Wireless debugging allows you to deploy apps without a cable connection, but requires initial USB setup.

1. First connect your device via USB and complete the trust process
2. In Xcode, go to **Window > Devices and Simulators** (or press `Shift + Cmd + 2`)
3. Select your device from the left sidebar
4. Check the box **"Connect via network"**
5. Wait for the network icon to appear next to your device name
6. You can now disconnect the USB cable

**Note:** Your Mac and iOS device must be on the same Wi-Fi network for wireless debugging to work.

### Step 2: Open Devices and Simulators Window

Verify that Xcode recognizes your connected device:

1. In Xcode, select **Window > Devices and Simulators** (or press `Shift + Cmd + 2`)
2. Click the **Devices** tab at the top
3. Your connected device should appear in the left sidebar under "Connected"

**What you should see:**
- Device name (e.g., "John's iPhone")
- iOS version (e.g., "iOS 26.1")
- Model identifier (e.g., "iPhone 15 Pro")
- Status indicator (yellow for preparing, green when ready)

**First-Time Connection:**

When you connect a device for the first time, Xcode displays a yellow banner message:

```
Preparing iPhone for development...
This may take a few minutes.
```

This process:
- Copies developer disk images to your device
- Installs debugging support files
- Prepares the device for app installation
- Takes 2-5 minutes on first connection

**Do not disconnect your device during this preparation.**

### Step 3: Enable Developer Mode on Your Device

Developer Mode is required on iOS 16+ and iPadOS 16+ devices. This security feature must be manually enabled to allow development app installation.

**To enable Developer Mode:**

1. **Ensure Xcode is running** on your Mac with your device connected

2. **On your iPhone/iPad**, open the **Settings** app

3. Navigate to **Privacy & Security**

4. Scroll down to find **Developer Mode**
   - If you don't see this option, disconnect and reconnect your device
   - Ensure Xcode is open with a project loaded

5. Tap **Developer Mode**

6. Toggle the **Developer Mode** switch to ON (green)

7. Read the warning dialog and tap **Restart**

8. Your device will restart automatically

9. **After restart**, unlock your device

10. A new alert appears: "Turn on Developer Mode?"
    - Tap **Turn On**
    - Enter your device passcode to confirm

11. Developer Mode is now active (you'll see the toggle is ON)

**Expected appearance:**
```
Settings > Privacy & Security > Developer Mode
Developer Mode                              [ON]

Developer Mode enables the installation of development
apps from Xcode and other advanced debugging features.
```

**Troubleshooting Developer Mode:**

- **Option not appearing:** Connect device via USB, open Xcode, open a project, then check Settings again
- **Option grayed out:** Ensure device has a passcode set (Settings > Face ID & Passcode or Touch ID & Passcode)
- **Still not visible:** Try unplugging and re-plugging your device while Xcode is running
- **iOS 15 or earlier:** Developer Mode doesn't exist on older iOS versions; skip this step

### Step 4: Verify Code Signing Configuration

Before running your app, ensure code signing is properly configured:

1. **In Xcode**, open your project

2. Select your project in the **Project Navigator** (left sidebar)

3. Under **Targets**, select your app target

4. Click the **Signing & Capabilities** tab

5. Verify the following:
   - ☑ **"Automatically manage signing"** is checked (recommended)
   - **Team:** Your Apple ID or Developer Team is selected
   - **Bundle Identifier:** Shows a valid identifier (e.g., `com.yourname.appname`)
   - **Signing Certificate:** Shows "Apple Development"
   - **Provisioning Profile:** Shows "Xcode Managed Profile"

**If you see a yellow warning:**

```
Signing for "YourApp" requires a development team.
Select a development team in the Signing & Capabilities editor.
```

**Solution:**
1. Click the **Team** dropdown
2. Select your Apple ID (appears as "Your Name (Personal Team)")
3. If no team appears, go to **Xcode > Settings > Accounts** and add your Apple ID

**For detailed signing setup, see KB-010: How to set up signing and capabilities for your app project.**

### Step 5: Select Your Device as the Run Destination

Configure Xcode to deploy your app to your physical device instead of the simulator:

1. In the Xcode toolbar (top-left area), locate the **scheme selector** and **device menu**
   - Shows something like "YourApp > iPhone 15 Pro Simulator"

2. Click the **device dropdown** (right side of the scheme selector)

3. A menu appears showing available destinations:
   ```
   Connected Devices
   ├─ Your iPhone Name (iOS 26.1)          ← Select this

   iOS Simulators
   ├─ iPhone 15 Pro
   ├─ iPhone 15
   ├─ iPad Pro 12.9-inch
   └─ ...
   ```

4. Under **"Connected Devices"**, select your physical device
   - It appears at the top of the list with your device's actual name
   - Shows the iOS version number next to it

5. The scheme selector should now display:
   ```
   YourApp > Your iPhone Name
   ```

**If your device doesn't appear:**

- Verify the USB cable is properly connected
- Check that you tapped "Trust" on the device
- Wait for the "Preparing device for development" process to complete
- In **Window > Devices and Simulators**, verify your device appears and shows a green status indicator

### Step 6: Build and Run Your App

With your device selected, you're ready to build and deploy your app:

1. Click the **Run** button (▶ Play button) in the toolbar
   - Alternatively, press `Cmd + R`
   - Or select **Product > Run** from the menu bar

2. **Xcode will:**
   - Compile your app's source code
   - Link frameworks and libraries
   - Sign the app with your development certificate
   - Create an app bundle (.app)
   - Transfer the app to your device
   - Install the app
   - Launch the app automatically

3. **Watch the progress** in Xcode's status bar (top center):
   ```
   Build YourApp: Build succeeded
   ↓
   Installing YourApp on Your iPhone Name
   ↓
   Running YourApp on Your iPhone Name
   ```

**First Build Notes:**

- Initial builds take longer (30-60 seconds or more for complex projects)
- Subsequent builds are faster due to incremental compilation
- Watch the Xcode console for build output and any errors

**Device Registration (Free Apple ID Only):**

If using a free Apple ID (Personal Team), Xcode automatically:
- Registers your device with your Apple ID
- Creates a development provisioning profile including your device
- Limits: Free accounts can register up to 3 devices

**Paid Developer Program:**
- Up to 100 devices per device type per year
- No weekly certificate expiration

### Step 7: Trust the Developer Certificate on Your Device

**This step is required ONLY if using a free Apple ID (Personal Team).** Paid Apple Developer Program members can skip this step.

When you first run an app signed with a free Apple ID, iOS blocks it for security. You must manually trust the developer certificate:

1. **On your iPhone/iPad**, the app will attempt to launch but immediately close

2. A system alert appears:
   ```
   Untrusted Developer

   Your device management settings do not allow apps from
   developer "Your Name (Personal Team)" to be installed.

   You can allow apps from this developer in Settings.

   [Cancel]
   ```

3. Tap **Cancel** (or just note the message)

4. Open the **Settings** app on your device

5. Navigate to **General > VPN & Device Management**
   - On some iOS versions: **General > Profiles & Device Management**
   - On older iOS: **General > Device Management**

6. Under **"Developer App"** section, you'll see:
   ```
   DEVELOPER APP
   Apple Development: your.email@example.com
   ```

7. Tap on **"Apple Development: your.email@example.com"**

8. A new screen shows your certificate information:
   ```
   Apple Development: your.email@example.com

   Apps from developer "Your Name" are not trusted
   on this iPhone.

   [Trust "your.email@example.com"]
   ```

9. Tap **Trust "your.email@example.com"**

10. A confirmation dialog appears:
    ```
    Trust "your.email@example.com"?

    After you trust this profile, apps from "Your Name"
    will run on this iPhone.

    [Cancel]  [Trust]
    ```

11. Tap **Trust**

**Certificate is now trusted.** Return to your home screen and launch your app - it will now open successfully.

**Important Notes:**

- You only need to trust each developer certificate once
- Free Apple ID certificates expire every 7 days and require re-signing
- When the certificate expires, rebuild and run from Xcode to refresh it
- You'll need to trust the new certificate again after expiration

### Step 8: Verify Your App is Running

After trusting the certificate (if required), your app should be running on your device:

1. **Check your device screen** - Your app should be visible and interactive

2. **In Xcode, verify:**
   - The **Run** button (▶) has changed to a **Stop** button (■)
   - The debug area shows console output (bottom panel)
   - The debug bar shows "Running YourApp on Your iPhone Name"

3. **Test app functionality:**
   - Interact with your app's UI
   - Test features that require hardware (camera, location, etc.)
   - Observe performance on real hardware
   - Check memory and CPU usage in Xcode's Debug Navigator

**Console Output:**

The debug console shows runtime logs and print statements:

```swift
// If your app includes:
print("App launched successfully!")

// You'll see in Xcode's console:
App launched successfully!
```

**Debugging Tools Available:**

- **Breakpoints:** Set breakpoints in your code; execution pauses when hit
- **View Debugger:** Inspect the UI hierarchy (Debug > View Debugging > Capture View Hierarchy)
- **Memory Graph:** Analyze memory usage (Debug > Memory Graph)
- **Network Profiling:** Monitor network requests
- **Console:** View print statements and system logs

### Step 9: Stop and Restart Your App

**To stop your running app:**

1. Click the **Stop** button (■) in Xcode's toolbar
   - Or press `Cmd + .` (Command + Period)
   - The app terminates on your device

2. The app icon remains on your home screen for future manual launches

**To run again:**

1. Click the **Run** button (▶) or press `Cmd + R`
2. Xcode rebuilds (if changes were made), reinstalls, and launches

**Manual Launch (Without Xcode):**

After the first installation:
- Your app appears on your device's home screen
- You can launch it like any other app
- It continues working until the provisioning profile expires
- Free Apple ID: Expires after 7 days
- Paid Developer Program: Expires after 1 year

### Step 10: Managing Installed Apps

**View all development apps installed on your device:**

1. In Xcode, go to **Window > Devices and Simulators** (`Shift + Cmd + 2`)
2. Select your device
3. The **"Installed Apps"** section lists all apps
4. You can:
   - See app versions and bundle identifiers
   - Remove apps by selecting them and clicking the **"–"** button
   - View app container data and logs

**Uninstalling development apps:**

**Method 1: From Device**
- Long-press the app icon on your home screen
- Tap **"Remove App"** > **"Delete App"**

**Method 2: From Xcode**
- Window > Devices and Simulators
- Select your device
- Find the app under "Installed Apps"
- Click the **"–"** button

**Method 3: Settings**
- Settings > General > iPhone Storage
- Find your app in the list
- Tap it > **Delete App**

## Expected Results

After successfully following these steps, you should observe:

✓ **Device appears in Xcode** under Window > Devices and Simulators

✓ **Developer Mode enabled** on iOS/iPadOS 16+ devices (Settings > Privacy & Security > Developer Mode is ON)

✓ **Device shows as trusted** with green indicator in Devices window

✓ **Device selectable** as run destination in Xcode's device menu

✓ **App builds successfully** without signing errors

✓ **App installs on device** and appears in Installed Apps list

✓ **App launches and runs** on physical device

✓ **Console output visible** in Xcode's debug area

✓ **Breakpoints work** and pause execution when hit

✓ **App icon appears** on device home screen for manual launching

✓ **Hardware features accessible** (camera, GPS, sensors, Face ID/Touch ID, etc.)

✓ **Realistic performance** reflects actual user experience

## Troubleshooting

### Common Issue 1: "Failed to prepare device for development"

**Problem:** Yellow banner message: "Failed to prepare device for development" appears in Devices window

**Symptoms:**
- Device shows in Xcode but with a warning icon
- Cannot select device as run destination
- Error persists after reconnecting

**Causes & Solutions:**

**Cause 1: Version Mismatch**
- Your device runs a newer iOS version than Xcode supports
- **Solution:** Update Xcode to the latest version (App Store > Updates)
- Xcode 26.1+ supports iOS 26.x devices

**Cause 2: Developer Mode Not Enabled**
- iOS 16+ requires Developer Mode activation
- **Solution:** Follow Step 3 to enable Developer Mode on your device

**Cause 3: Trust Not Established**
- Device hasn't been trusted or trust was revoked
- **Solution:**
  1. Disconnect device
  2. On device: Settings > General > Transfer or Reset iPhone > Reset Location & Privacy
  3. Reconnect device
  4. Tap "Trust" when prompted
  5. Enter device passcode

**Cause 4: Personal Hotspot Interference**
- Having Personal Hotspot enabled can cause this error
- **Solution:**
  1. On device: Settings > Personal Hotspot
  2. Toggle OFF
  3. Reconnect device to Xcode

**Cause 5: macOS/iOS Needs Restart**
- System connection issues can block device preparation
- **Solution:**
  1. Restart your iPhone/iPad
  2. Restart your Mac
  3. Reconnect and try again

**Cause 6: Paired Apple Watch Not Trusted**
- If an Apple Watch is paired, it must also trust the Mac
- **Solution:** Trust the Mac on your paired Apple Watch
  1. On Apple Watch, you should see a "Trust This Computer?" prompt
  2. Tap "Trust"

**Cause 7: Xcode Cache Issues**
- Corrupted device support files
- **Solution:**
```bash
# In Terminal, remove device support files:
rm -rf ~/Library/Developer/Xcode/iOS\ DeviceSupport/

# Reconnect device to download fresh support files
```

**Last Resort:**
```bash
# Clear Xcode caches completely:
rm -rf ~/Library/Developer/Xcode/DerivedData/
rm -rf ~/Library/Caches/com.apple.dt.Xcode/

# Restart Xcode and reconnect device
```

### Common Issue 2: Device Not Appearing in Xcode

**Problem:** Your iPhone/iPad doesn't show up in Xcode's device list

**Diagnostic Steps:**

1. **Check cable connection:**
   - Try a different USB port on your Mac
   - Try a different cable (some cables are charge-only, not data cables)
   - Ensure cable is fully inserted on both ends

2. **Verify device is unlocked:**
   - Unlock your iPhone/iPad
   - Keep it unlocked while connecting

3. **Check System Information:**
   ```bash
   # In Terminal:
   system_profiler SPUSBDataType
   ```
   Look for your iPhone/iPad in the output. If it doesn't appear, it's a hardware connection issue.

4. **Trust verification:**
   - On device, go to Settings > General > VPN & Device Management
   - Verify your Mac is listed under "Computers"

5. **Xcode refresh:**
   - In Xcode: Window > Devices and Simulators
   - Click the "+" button to manually add a device
   - Or disconnect/reconnect the device

6. **Reset Location & Privacy:**
   - Settings > General > Transfer or Reset iPhone
   - Tap "Reset" > "Reset Location & Privacy"
   - Reconnect and trust again

### Common Issue 3: "Signing for [App] requires a development team"

**Problem:** Yellow warning in Signing & Capabilities tab, cannot build to device

**Full Error Message:**
```
Signing for "YourApp" requires a development team.
Select a development team in the Signing & Capabilities editor.
```

**Solution:**

1. In Xcode, select your project in the Project Navigator
2. Select your target under Targets
3. Click **Signing & Capabilities** tab
4. In the **Team** dropdown:
   - Click the dropdown menu
   - Select your Apple ID (shows as "Your Name (Personal Team)")

5. **If no team appears:**
   - Click **"Add Account..."** in the Team dropdown
   - Or go to **Xcode > Settings > Accounts**
   - Click the **"+"** button
   - Select **Apple ID**
   - Sign in with your Apple ID and password
   - Return to Signing & Capabilities and select your newly added team

6. Xcode will automatically:
   - Create a development certificate
   - Generate a provisioning profile
   - Register your device
   - Update bundle identifier if needed

**See KB-010 for comprehensive signing setup instructions.**

### Common Issue 4: "Unable to install [App]"

**Problem:** App builds successfully but fails to install on device

**Possible Causes:**

**Cause 1: Storage Full**
- Device doesn't have enough free space
- **Solution:** Free up storage on your device (Settings > General > iPhone Storage)

**Cause 2: Bundle Identifier Conflict**
- Another app with the same bundle ID is installed
- **Solution:**
  - Delete the conflicting app from your device
  - Or change your app's bundle identifier in Signing & Capabilities

**Cause 3: Invalid Provisioning Profile**
- Profile doesn't include your device
- Profile expired
- **Solution:**
  1. Xcode > Settings > Accounts
  2. Select your Apple ID > View Details
  3. Click the refresh icon (circular arrow)
  4. Download latest profiles
  5. In Signing & Capabilities, toggle "Automatically manage signing" OFF then ON

**Cause 4: iOS Version Too Old**
- App's deployment target is newer than device's iOS version
- **Solution:**
  1. Select your target in Xcode
  2. Go to **Build Settings** tab
  3. Search for "iOS Deployment Target"
  4. Lower the version to match your device (or update your device's iOS)

**Cause 5: Developer Mode Disabled**
- Developer Mode was turned off after initial setup
- **Solution:** Re-enable Developer Mode (see Step 3)

### Common Issue 5: "Untrusted Developer" Message Won't Go Away

**Problem:** Repeatedly see "Untrusted Developer" even after trusting

**Solution Steps:**

1. **Verify you trusted the certificate:**
   - Settings > General > VPN & Device Management
   - Under "Developer App", you should see "Apple Development: youremail@example.com"
   - It should show as "Verified" or "Trusted"

2. **If certificate not listed:**
   - App installation may have failed
   - In Xcode, clean build folder: Product > Clean Build Folder (`Cmd + Shift + K`)
   - Run the app again from Xcode

3. **Certificate expired (Free Apple ID only):**
   - Free developer certificates expire after 7 days
   - **Solution:**
     ```
     1. Connect device to Mac
     2. Open project in Xcode
     3. Click Run button (rebuilds with fresh certificate)
     4. Trust the new certificate in Settings
     ```

4. **Clear trusted computers:**
   - Settings > General > VPN & Device Management
   - Find your Mac under "Computers"
   - Swipe left and delete
   - Reconnect device and trust again

5. **Rebuild from scratch:**
   ```bash
   # In Xcode:
   # 1. Product > Clean Build Folder (Cmd + Shift + K)
   # 2. Delete app from device
   # 3. Xcode > Settings > Accounts > Download Manual Profiles
   # 4. Run again
   ```

### Common Issue 6: Wireless Debugging Not Working

**Problem:** Cannot connect to device over network after enabling "Connect via network"

**Requirements Check:**
- ☑ Mac and device on same Wi-Fi network
- ☑ Device initially connected via USB
- ☑ "Connect via network" checked in Devices window
- ☑ No VPN active on Mac or device

**Solutions:**

1. **Disable and re-enable wireless:**
   - Window > Devices and Simulators
   - Select your device
   - Uncheck "Connect via network"
   - Disconnect USB cable
   - Reconnect USB cable
   - Check "Connect via network"
   - Wait for network icon to appear

2. **Check network connectivity:**
   - Ensure both devices on same network (not guest network)
   - Disable any VPNs on Mac and device
   - Check firewall settings (System Settings > Network > Firewall)
   - Temporarily disable firewall to test

3. **Reset network connection:**
   - On Mac: Disconnect and reconnect to Wi-Fi
   - On device: Settings > Wi-Fi > Toggle OFF then ON
   - Restart router if issues persist

4. **Use USB as backup:**
   - Wireless is convenient but USB is more reliable
   - USB provides faster app transfer
   - Use USB for large apps or initial debugging

### Common Issue 7: App Crashes Immediately on Launch

**Problem:** App installs but crashes immediately when launched

**Debugging Steps:**

1. **Check Xcode Console:**
   - View > Debug Area > Show Debug Area
   - Look for crash logs and error messages
   - Common errors: missing resources, incorrect entitlements

2. **Verify deployment target:**
   - Target > General > Deployment Info
   - Ensure "Minimum Deployments" iOS version ≤ your device's iOS version

3. **Check for missing resources:**
   - Ensure all required assets are in the project
   - Verify bundle resources in Build Phases > Copy Bundle Resources

4. **Review entitlements:**
   - If using capabilities, ensure they're properly configured
   - Invalid entitlements cause immediate crashes

5. **View crash logs:**
   - Window > Devices and Simulators
   - Select your device
   - Click "View Device Logs"
   - Find your app's crash log
   - Look for exception type and backtrace

6. **Test in Simulator first:**
   - Switch to simulator (iPhone 15 Pro)
   - Run app to see if it's a device-specific issue
   - If it works in simulator, issue may be hardware-related or entitlement-related

## Additional Tips

- **Keep devices charged** - Development and debugging drain battery faster than normal use

- **Use wireless debugging selectively** - USB is faster and more reliable for initial development; use wireless for convenience later

- **Free Apple ID limitations:**
  - Up to 3 devices registered
  - Certificates expire every 7 days
  - Must rebuild weekly to refresh
  - Cannot publish to App Store
  - No access to advanced capabilities

- **Paid Developer Program benefits ($99/year):**
  - Up to 100 devices per type per year
  - Certificates valid for 1 year
  - App Store distribution
  - TestFlight beta testing
  - Advanced capabilities (Push Notifications, HealthKit, etc.)
  - No weekly reinstallation needed

- **Testing on multiple devices** - Test on different iPhone/iPad models, screen sizes, and iOS versions to ensure compatibility

- **Device management** - In Window > Devices and Simulators, you can:
  - View system logs
  - Take screenshots
  - Access app container data
  - View crash logs
  - Monitor resource usage

- **Console output** - Use `print()` statements liberally during development to track execution flow:
  ```swift
  print("View did load")
  print("User tapped button: \(buttonName)")
  print("API response: \(data)")
  ```

- **Breakpoint debugging** - Click the line number gutter to set breakpoints; execution pauses to inspect variables

- **View hierarchy debugging** - When app is running, use Debug > View Debugging > Capture View Hierarchy to inspect UI layout

- **Network debugging** - Monitor network requests in Xcode's Network panel (Debug Navigator > Network)

- **Memory profiling** - Use Instruments (Product > Profile) for detailed memory, CPU, and performance analysis

- **Real hardware is essential** - Always test on real devices before release:
  - Simulators don't accurately represent performance
  - Hardware features (camera, GPS, biometrics) only work on devices
  - Touch interaction feels different on actual hardware
  - Network conditions vary on real devices

- **Simulator vs. Device:**
  ```
  Simulator Advantages:
  - Fast to launch
  - Easy to switch between different models
  - No cables needed
  - Great for UI development

  Device Advantages:
  - True performance metrics
  - Real hardware features
  - Actual user experience
  - Required for final testing
  ```

- **Provisioning profile expiration** - Free accounts must rebuild every 7 days; set a calendar reminder or you'll get "profile expired" errors

- **Multiple Xcode versions** - If running multiple Xcode versions, ensure you're using the correct one:
  ```bash
  # Check active Xcode:
  xcode-select -p

  # Switch if needed:
  sudo xcode-select -s /Applications/Xcode.app
  ```

- **Device preparation only once** - After the first connection and preparation, subsequent connections are instant

- **Background app debugging** - Use background modes testing by putting app in background (home button/swipe up) while debugging

- **Rotation testing** - Test landscape and portrait orientations on actual hardware to verify layout constraints

- **Dark Mode testing** - Toggle Dark Mode on your device (Settings > Display & Brightness) to test both appearances

## Related Articles

- KB-001: How to download and install Xcode 26.1+ from the Mac App Store
- KB-004: How to set up an Apple Developer account for iOS development
- KB-008: How to create your first iOS app project using the App template
- KB-010: How to set up signing and capabilities for your app project
- KB-028: How to use the iOS Simulator for app testing
- KB-029: How to debug your app with breakpoints and lldb
- KB-099: How to create an archive and upload to App Store Connect

## Sources

- Running your app in Simulator or on a device - Apple Developer Documentation (https://developer.apple.com/documentation/xcode/running-your-app-in-simulator-or-on-a-device) (Accessed: November 17, 2025)
- Enabling Developer Mode on a device - Apple Developer Documentation (https://developer.apple.com/documentation/xcode/enabling-developer-mode-on-a-device) (Accessed: November 17, 2025)
- Adding a Physical Device as a Run Destination - Russell Gordon Tutorial (https://www.russellgordon.ca/tutorials/adding-a-physical-device-as-a-run-destination/) (Accessed: November 17, 2025)
- How to enable developer mode on an iPhone or iPad - Stack Overflow (https://stackoverflow.com/questions/73733701/how-to-enable-developer-mode-on-an-iphone-or-ipad-ios-ipados) (Accessed: November 17, 2025)
- Xcode error: Failed to prepare device for development - Stack Overflow (https://stackoverflow.com/questions/64974291/xcode-error-failed-to-prepare-device-for-development) (Accessed: November 17, 2025)
- iOS 26 Developer Mode - Tenorshare Guide (https://www.tenorshare.com/ios-26/how-to-enable-developer-mode-on-ios-26-beta.html) (Accessed: November 17, 2025)
- How to Test Your App on an iPhone Using Xcode - Buildfire (https://buildfire.com/how-to-test-your-app-on-an-iphone-using-xcode/) (Accessed: November 17, 2025)
- iOS Developer Mode - Expo Documentation (https://docs.expo.dev/guides/ios-developer-mode/) (Accessed: November 17, 2025)
- Xcode Failed to Prepare Device for Development - Cocoacasts (https://cocoacasts.com/xcode-failed-to-prepare-device-for-development) (Accessed: November 17, 2025)
- Automatic Code Signing - Bitrise Documentation (https://devcenter.bitrise.io/en/code-signing/ios-code-signing/managing-ios-code-signing-files---automatic-provisioning.html) (Accessed: November 17, 2025)

---
*This article is part of the Claude Code Web knowledge base*
