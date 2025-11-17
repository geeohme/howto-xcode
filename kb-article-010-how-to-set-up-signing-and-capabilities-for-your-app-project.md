# How to set up signing and capabilities for your app project

**Article ID:** KB-010
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 15-20 minutes

## Overview

Code signing is essential for running your iOS app on physical devices and for using Apple's app services. Capabilities give your app access to features like iCloud, push notifications, Game Center, and more. This guide walks you through setting up both signing and capabilities using Xcode's Signing & Capabilities tab, with automatic signing recommended for most developers.

## Prerequisites

- Xcode 26.1+ installed on your Mac
- An active Xcode project (see KB-008: How to create your first iOS app project)
- An Apple ID (free) for development, or Apple Developer Program membership ($99/year) for App Store distribution
- For device testing: A physical iPhone or iPad connected to your Mac

## Steps

### Step 1: Access Your Project Settings

Open your Xcode project and locate the project navigator on the left side.

1. Click on your project name at the very top of the navigator (the blue project icon)
2. In the main editor area, you'll see two sections: **Projects** and **Targets**
3. Under **Targets**, select your app target (typically has the same name as your project)

**Important:** You must select a target, not the project itself, to see the Signing & Capabilities tab.

### Step 2: Navigate to Signing & Capabilities

With your target selected, look at the horizontal tab bar at the top of the editor area. Click on the **Signing & Capabilities** tab (between "Info" and "Resource Tags").

This tab is divided into two main sections:
- **Signing** (at the top)
- **Capabilities** (below, with a "+ Capability" button)

### Step 3: Configure Automatic Signing (Recommended)

Apple recommends automatic signing for both development and distribution. Xcode will handle certificates and provisioning profiles for you.

1. Ensure the **"Automatically manage signing"** checkbox is checked (it's checked by default)
2. Click the **Team** dropdown menu
3. Select your Apple ID or team:
   - **Personal Team (Your Name):** Free Apple ID, allows device testing but not App Store distribution
   - **Your Developer Team:** If you're enrolled in the Apple Developer Program ($99/year)
   - **Add Account...** If you haven't added your Apple ID yet

**When you select a team:**
- Xcode automatically creates a development certificate if needed
- Xcode generates a unique Bundle Identifier (e.g., `com.yourname.AppName`)
- Provisioning profiles are created and managed automatically
- Your connected devices are registered automatically

### Step 4: Verify Your Bundle Identifier

Below the Team setting, you'll see the **Bundle Identifier** field.

```
Bundle Identifier: com.yourcompany.YourAppName
```

This uniquely identifies your app across the Apple ecosystem. Xcode generates this automatically based on your team and app name, but you can customize it:

- Use reverse-domain notation (e.g., `com.example.myapp`)
- Use only alphanumeric characters and periods
- Keep it lowercase
- Make it unique if you plan to publish to the App Store

**Note:** Once you publish an app, you cannot change its Bundle Identifier.

### Step 5: Configure Signing for Different Build Configurations

You'll see two sections in the Signing area:

**Debug (Development):**
- Used when running your app from Xcode
- Uses Development certificates
- Allows installation on registered devices only

**Release (Distribution):**
- Used for TestFlight and App Store submissions
- Uses Distribution certificates
- For App Store, ad hoc, or enterprise distribution

With automatic signing, Xcode handles both configurations automatically. Leave **"Automatically manage signing"** checked for both.

### Step 6: Add Capabilities to Your App

Capabilities enable specific Apple services and features. To add a capability:

1. Click the **"+ Capability"** button in the Signing & Capabilities section
2. A popup menu appears showing all available capabilities
3. Search or scroll to find the capability you need
4. Click on it to add it to your project

**Common Capabilities:**

**Push Notifications:**
- Enables remote notifications from your server
- Xcode creates the necessary entitlements file
- You'll need to configure push notification certificates separately in Apple Developer

**iCloud:**
- Enables CloudKit, iCloud Documents, and Key-Value Storage
- After adding, configure which iCloud services you need (CloudKit containers, iCloud Documents, etc.)
- Xcode creates CloudKit containers automatically if needed

**Game Center:**
- Integrates leaderboards, achievements, and multiplayer gaming
- No additional configuration required after adding

**In-App Purchase:**
- Allows your app to sell digital goods and services
- Must configure products in App Store Connect separately

**Sign in with Apple:**
- Provides Apple's authentication system
- Required if you use third-party sign-in methods
- After adding, configure capabilities like email relay

**App Groups:**
- Enables data sharing between your app and extensions
- After adding, specify or create an App Group identifier (e.g., `group.com.yourcompany.yourapp`)

### Step 7: Configure Capability Settings

After adding a capability, additional configuration options appear below it:

For example, after adding **iCloud**:

```
iCloud
├── Services
│   ├── ☑ CloudKit
│   ├── ☐ CloudKit (API with legacy compatibility)
│   ├── ☑ Documents
│   └── ☑ Key-value storage
└── Containers
    └── iCloud.com.yourcompany.YourAppName (default container)
```

1. Check or uncheck specific services you need
2. Add containers by clicking the "+" button
3. Xcode updates your entitlements file automatically

### Step 8: Verify Signing Status

After configuration, check the signing status indicator:

**Green checkmark:** Signing is configured correctly
- Shows: "Signing Certificate: Apple Development"
- Shows: "Provisioning Profile: Xcode Managed Profile"

**Yellow warning triangle:** Minor issue that may not block development
- Example: "Signing for 'AppName' requires a development team"
- Solution: Select a team in the Team dropdown

**Red error icon:** Critical issue that blocks building
- Common error: "Failed to register bundle identifier"
- Solution: Check Bundle Identifier is unique and follows naming rules

Click on warning or error messages for detailed information and potential fixes.

### Step 9: Test on a Physical Device

To verify your signing setup works:

1. Connect your iPhone or iPad via USB (or use wireless debugging if configured)
2. Select your device from the device dropdown in Xcode's toolbar (next to the scheme selector)
3. Click the **Run** button (▶) or press `Cmd + R`

**First-time device registration:**
- Xcode automatically registers your device to your Apple Developer account
- Creates and updates provisioning profiles to include your device
- Installs the app on your device

**On the device:**
- If using a free Apple ID (Personal Team), you'll see a security prompt
- Go to Settings > General > VPN & Device Management
- Tap on your Apple ID under Developer App
- Tap "Trust [Your Name]"
- Return to your home screen and launch the app

## Expected Results

After completing these steps, you should observe:

✓ **Signing & Capabilities tab shows green checkmarks** for both Debug and Release configurations

✓ **Bundle Identifier is properly formatted** (e.g., `com.yourcompany.appname`)

✓ **Team field shows your selected team** (either Personal or Developer Program team)

✓ **Capabilities are listed** with any required configurations completed

✓ **No error messages** in the signing section

✓ **App builds successfully** when clicking the Run button

✓ **App installs and launches on your physical device** without signing errors

✓ **Entitlements file created** in your project (e.g., `YourApp.entitlements`) if you added any capabilities

## Troubleshooting

### Common Issue 1: "Signing for [App] requires a development team"

**Problem:** Yellow warning appears, no team selected in Team dropdown

**Solution:**
1. Click the Team dropdown
2. Select your Apple ID (appears as "Your Name (Personal Team)")
3. If no Apple ID appears, select "Add Account..." and sign in
4. Wait a few seconds for Xcode to verify and download team information

### Common Issue 2: "Failed to register bundle identifier"

**Problem:** Red error when trying to build, Bundle Identifier conflicts or is invalid

**Solution:**
1. Check your Bundle Identifier follows reverse-domain notation
2. Ensure it contains only lowercase letters, numbers, hyphens, and periods
3. Try adding your initials or a unique suffix (e.g., `com.example.myapp-jd`)
4. If in Developer Program, check App Store Connect for Bundle ID conflicts
5. For Personal Team, the Bundle Identifier must not match any published apps

### Common Issue 3: Provisioning profile errors on device

**Problem:** "Unable to install [App]" or "The executable was signed with invalid entitlements"

**Solution:**
1. Go to Xcode > Settings > Accounts
2. Select your Apple ID
3. Click "Download Manual Profiles" button
4. Return to Signing & Capabilities and toggle "Automatically manage signing" off then on again
5. Clean build folder (Product > Clean Build Folder or `Cmd + Shift + K`)
6. Rebuild and try installing again

### Common Issue 4: "A valid provisioning profile for this executable was not found"

**Problem:** Device won't run the app, provisioning profile missing or expired

**Solution:**
1. Ensure your device is connected and unlocked
2. Xcode > Settings > Accounts > select your Apple ID > View Details
3. Click the refresh button (circular arrow) to update provisioning profiles
4. Check that "Automatically manage signing" is enabled
5. For free accounts, note that development profiles expire after 7 days and must be refreshed

### Common Issue 5: Signing & Capabilities tab is missing

**Problem:** Cannot find the Signing & Capabilities tab

**Solution:**
1. Ensure you selected a **target** in the project navigator, not the project itself
2. Look under the Targets section when you click your project
3. Select your app target (has your app's name)
4. The tab should appear between "Info" and "Resource Tags"

## Additional Tips

- **Use automatic signing whenever possible** - Apple recommends it and it handles complexity for you

- **Free Apple ID limitations** - Personal Teams can test on devices but cannot submit to App Store, and certificates expire every 7 days

- **Developer Program benefits** - $99/year membership enables App Store distribution, TestFlight, advanced capabilities, and doesn't require weekly certificate refresh

- **Keep Xcode updated** - Newer Xcode versions handle signing and provisioning more reliably with bug fixes

- **Check managed capabilities** - Some capabilities (Push Notifications, iCloud, Sign in with Apple, etc.) can be enabled directly in Xcode 26+ without visiting Apple Developer portal

- **Entitlements file** - Xcode creates a `.entitlements` file when you add capabilities; this file is XML and defines which services your app uses

- **Device registration limits** - Free accounts can register up to 3 devices; Developer Program accounts can register up to 100 devices per device type per year

- **Bundle Identifier strategy** - For companies, use `com.companyname.appname`; for individuals, use `com.yourname.appname` or `io.github.username.appname`

- **Capability dependencies** - Some capabilities require specific iOS SDK versions or hardware features (e.g., NFC requires iPhone 7 or later)

- **Signing in CI/CD** - For automated builds, you'll need to use manual signing or tools like Fastlane for certificate and profile management

## Related Articles

- KB-004: How to set up an Apple Developer account for iOS development
- KB-008: How to create your first iOS app project using the App template
- KB-027: How to run your app on a physical iPhone or iPad device
- KB-099: How to create an archive and upload to App Store Connect

## Sources

- Adding capabilities to your app - Apple Developer Documentation (https://developer.apple.com/documentation/xcode/adding-capabilities-to-your-app) (Accessed: November 17, 2025)
- Signing & Capabilities workflow - Apple Xcode Help (https://help.apple.com/xcode/mac/current/en.lproj/dev60b6fbbc7.html) (Accessed: November 17, 2025)
- Provisioning with managed capabilities - Apple Developer (https://developer.apple.com/help/account/reference/provisioning-with-managed-capabilities/) (Accessed: November 17, 2025)
- Capabilities Overview - Apple Developer (https://developer.apple.com/help/account/capabilities/capabilities-overview/) (Accessed: November 17, 2025)
- Certificates - Apple Developer Support (https://developer.apple.com/support/certificates/) (Accessed: November 17, 2025)
- Code Signing in Xcode: Simplified for Beginners - Commit Studio (https://commitstudiogs.medium.com/code-signing-in-xcode-simplified-for-beginners-ff1ce308f991) (Accessed: November 17, 2025)
- The Apple Code Signing Handbook - freeCodeCamp (https://www.freecodecamp.org/news/apple-code-signing-handbook/) (Accessed: November 17, 2025)

---
*This article is part of the Claude Code Web knowledge base*
