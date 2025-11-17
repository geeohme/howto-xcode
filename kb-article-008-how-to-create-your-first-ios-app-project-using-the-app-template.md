# How to create your first iOS app project using the App template

**Article ID:** KB-008
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 10-15 minutes

## Overview

This guide walks you through creating your first iOS app project in Xcode 26.1+ using the App template. The App template is the standard starting point for iOS applications, providing a basic structure with a single view that you can customize to build your app. Whether you choose SwiftUI or UIKit (Storyboard), this template generates all the necessary files and configuration to start developing immediately.

## Prerequisites

Before creating your first iOS app project, ensure you have:

- **Xcode 26.1+:** Installed and verified on your Mac
- **macOS:** Sequoia 15.6 or later (Tahoe 26 recommended for AI features)
- **Apple Silicon Mac:** M1 or later (recommended for full features)
- **Basic Understanding:** Familiarity with Xcode interface (optional but helpful)

**Related Prerequisites:**
- KB-001: How to download and install Xcode 26.1+ from the Mac App Store
- KB-002: How to verify Xcode installation and check the version number

**Note:** You do NOT need a paid Apple Developer account to create and test projects in the iOS Simulator. A free Apple ID is sufficient for local development.

## Steps

### Step 1: Launch Xcode

Open Xcode using one of these methods:
1. Press **Command + Space** to open Spotlight, type "Xcode," and press **Enter**
2. Navigate to your **Applications** folder and double-click **Xcode**
3. Click the **Xcode** icon in your Dock (if pinned)

When Xcode opens, you'll see the welcome window with several options.

### Step 2: Start Creating a New Project

In the Xcode welcome window, select **Create New Project** or use one of these alternatives:

- **Menu Bar:** Choose **File** > **New** > **Project** (or press **Shift + Command + N**)
- **No Welcome Window?** If Xcode is already open, use **File** > **New** > **Project**

```bash
# You can also create projects from Terminal (advanced):
# xcodebuild -create-project -name MyFirstApp -organization com.example -language Swift
```

The **New Project Assistant** will open, presenting template options.

### Step 3: Select the iOS Platform and App Template

1. At the top of the New Project Assistant, ensure **iOS** is selected (it's usually selected by default)
   - You'll see tabs for: iOS, watchOS, tvOS, visionOS, macOS, Multiplatform
2. In the **Application** section, select the **App** template
   - The App icon shows a blue square with rounded corners
3. Click **Next** to proceed to project configuration

**Template Options Explained:**
- **App:** The standard template for iOS applications (most common choice)
- **Document App:** For document-based applications (like Pages or Numbers)
- **Game:** Pre-configured for game development with SpriteKit or SceneKit
- **Augmented Reality App:** For AR experiences using ARKit
- **Sticker Pack App:** For creating iMessage sticker packs

For your first iOS app, the **App** template is the recommended choice.

### Step 4: Configure Your Project Settings

On the project options screen, you'll need to configure several important settings:

#### Product Name
- **Field:** Product Name
- **What to Enter:** The name of your app (e.g., "MyFirstApp" or "HelloWorld")
- **Note:** This becomes your app's display name and is used in the code
- **Tip:** Use alphanumeric characters without spaces; use CamelCase for multiple words

#### Team
- **Field:** Team
- **What to Select:**
  - Choose your Apple Developer team if you have one
  - Select **None** or your personal Apple ID if you're just learning
- **Note:** Required for running on physical devices; not required for Simulator testing

#### Organization Identifier
- **Field:** Organization Identifier
- **What to Enter:** A reverse-domain notation identifier (e.g., "com.yourname" or "com.example")
- **Format:** Typically follows the pattern: `com.companyname` or `com.yourname`
- **Example:** If your name is John Smith, use: `com.johnsmith`
- **Note:** This is combined with the Product Name to create your Bundle Identifier

#### Bundle Identifier
- **Field:** Bundle Identifier (auto-generated)
- **Format:** `[Organization Identifier].[Product Name]`
- **Example:** `com.johnsmith.MyFirstApp`
- **Note:** This is automatically created and uniquely identifies your app
- **Important:** Bundle Identifiers must be unique across the entire App Store

```swift
// Example of how Bundle Identifier is formed:
Organization Identifier: com.example
Product Name: MyFirstApp
Bundle Identifier: com.example.MyFirstApp
```

#### Interface
- **Field:** Interface
- **Options:** SwiftUI or Storyboard
- **Recommendation for beginners:**
  - **SwiftUI:** Modern, declarative UI framework (recommended for new projects)
  - **Storyboard:** Traditional UIKit approach with visual interface builder
- **Note:** SwiftUI is the future of iOS development and is recommended for Xcode 26.1+

**For this guide, we'll select SwiftUI.**

**Related Article:** KB-009: How to choose between SwiftUI and UIKit for your project

#### Language
- **Field:** Language
- **Options:** Swift or Objective-C
- **Recommendation:** **Swift** (modern, safe, and Apple's preferred language)
- **Note:** Objective-C is legacy; Swift is used for all new development

#### Storage
- **Field:** Storage
- **Options:** None, SwiftData, or Core Data
- **Recommendation for first project:** **None** (you can add data persistence later)
- **Note:**
  - **SwiftData:** Modern data persistence framework (Swift-native, iOS 26+)
  - **Core Data:** Traditional data persistence framework
  - **None:** No data storage framework (simplest starting point)

#### Include Tests
- **Checkbox:** Include Tests (may be visible depending on Xcode version)
- **Recommendation:** Leave checked (creates test targets for unit and UI testing)

### Step 5: Choose Your Project Location

1. After configuring settings, click **Next**
2. Choose where to save your project:
   - Select a location on your Mac (e.g., Documents, Desktop, or a dedicated Development folder)
   - **Tip:** Create a dedicated folder like `~/Developer/iOS Projects` for organization
3. **Create Git repository** checkbox:
   - **Recommendation:** Leave this **checked** to enable version control
   - Git tracks changes to your code, allowing you to revert mistakes
4. Click **Create**

Xcode will create your project and open it automatically.

### Step 6: Understand Your Project Structure

After creating the project, you'll see the Xcode workspace with these key files:

```
MyFirstApp/
├── MyFirstApp/
│   ├── MyFirstAppApp.swift         # App entry point
│   ├── ContentView.swift            # Main user interface
│   ├── Assets.xcassets              # Images, icons, and colors
│   └── Preview Content/
│       └── Preview Assets.xcassets  # Assets for SwiftUI previews
├── MyFirstApp.xcodeproj             # Project file
└── Products/
    └── MyFirstApp.app               # Your compiled app (after building)
```

**Key Files Explained:**

**1. MyFirstAppApp.swift** (App entry point)
```swift
import SwiftUI

@main
struct MyFirstAppApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```
- The `@main` attribute marks this as your app's entry point
- Defines the app's structure and initial view
- Launches `ContentView()` when the app starts

**2. ContentView.swift** (Main UI)
```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundStyle(.tint)
            Text("Hello, world!")
        }
        .padding()
    }
}

#Preview {
    ContentView()
}
```
- Defines your app's user interface
- Contains a basic "Hello, world!" template
- `#Preview` macro enables SwiftUI live previews in the canvas

**3. Assets.xcassets**
- Stores images, app icons, and colors
- Organized asset catalog for visual resources
- Includes AccentColor and AppIcon by default

**4. Preview Content**
- Contains assets used only for SwiftUI previews
- Not included in the final app build
- Useful for mock data and development-only images

### Step 7: Build and Run Your First App

Now it's time to see your app in action!

1. **Select a Simulator Device:**
   - In the toolbar at the top of Xcode, click the device menu (next to the project name)
   - It might show "iPhone 16 Pro" or another device by default
   - Click to see all available simulators
   - Select **iPhone 16 Pro** or **iPhone 16** (or any iPhone you prefer)

2. **Click the Run Button:**
   - Click the **Play** (▶) button in the top-left corner of Xcode
   - Alternatively, press **Command + R**

3. **Wait for Build and Launch:**
   - Xcode will compile your app (first build takes 30-60 seconds)
   - The iOS Simulator will launch automatically
   - Your app will open in the Simulator

**Build Process Details:**
```bash
# Xcode performs these steps automatically:
# 1. Compiles Swift code
# 2. Links frameworks
# 3. Creates app bundle
# 4. Installs on Simulator
# 5. Launches the app
```

### Step 8: Verify Your App is Running

In the iOS Simulator, you should see:
- A white screen with a globe icon at the top
- The text "Hello, world!" below the globe icon
- Standard iOS interface elements (status bar, home indicator)

**Congratulations!** You've successfully created and run your first iOS app.

### Step 9: Make Your First Code Change (Optional)

Let's customize the app to see how changes work:

1. In Xcode, open **ContentView.swift** (click on it in the left sidebar)
2. Find this line:
   ```swift
   Text("Hello, world!")
   ```
3. Change it to:
   ```swift
   Text("Welcome to my first iOS app!")
   ```
4. Press **Command + R** to build and run again
5. The Simulator will reload with your updated text

**Alternative: Live Preview (Fast)**
- With ContentView.swift open, press **Option + Command + P** to enable live preview
- You'll see changes instantly without rebuilding the entire app
- Click the "Resume" button if the preview is paused

### Step 10: Explore Xcode's Interface (Optional)

Take a moment to familiarize yourself with the Xcode interface:

**Key Areas:**
1. **Navigator Area** (left sidebar): Browse project files, search, debugging, etc.
2. **Editor Area** (center): Write and edit code
3. **Canvas/Preview** (right side, SwiftUI): Live preview of your UI
4. **Inspector Area** (right sidebar): Configure properties and settings
5. **Debug Area** (bottom): Console output and debugging tools

**Useful Keyboard Shortcuts:**
- **Command + R:** Build and run
- **Command + B:** Build only
- **Command + .** (period): Stop running app
- **Command + 0:** Show/hide Navigator
- **Command + Option + 0:** Show/hide Inspector
- **Command + Shift + Y:** Show/hide Debug area

## Expected Results

After completing these steps, you should have:

- A new iOS app project created from the App template
- A project structure with App.swift and ContentView.swift files
- Successfully built and run your app in the iOS Simulator
- Seen "Hello, world!" displayed in the running app
- Understanding of basic Xcode project components

**What Success Looks Like:**
- Xcode opens your project without errors
- The build succeeds (green checkmark, no red errors)
- iOS Simulator launches and displays your app
- You can see and interact with the app interface
- Making code changes and rebuilding works correctly

## Troubleshooting

### Common Issue 1: "Signing for [App Name] requires a development team"

**Problem:** When trying to run the app, you see an error about code signing requiring a development team
**Solution:**
1. Select your project in the Navigator (blue icon at the top)
2. Select your app target under "TARGETS"
3. Go to the **Signing & Capabilities** tab
4. **For Simulator testing (free):**
   - Check **Automatically manage signing**
   - Select your personal Apple ID from the **Team** dropdown
   - If no team appears, add your Apple ID: Xcode > Settings > Accounts > + (Add)
5. **Alternative:**
   - You only need a team for running on physical devices
   - Ensure you selected the iOS Simulator (not a real device) in the device menu

**Related Article:** KB-010: How to set up signing and capabilities for your app project

### Common Issue 2: Build Failed with "Command CompileSwift failed"

**Problem:** Build fails with Swift compilation errors
**Solution:**
1. Check the **Issue Navigator** (triangle icon with exclamation mark in left sidebar)
2. Look for red error messages and read them carefully
3. Common causes:
   - Typos in code (check spelling and syntax)
   - Missing import statements
   - Incorrect SwiftUI syntax
4. **Quick Fix:**
   - Choose **Product** > **Clean Build Folder** (Shift + Command + K)
   - Build again with **Command + B**
5. If the error persists after cleaning, the issue is likely in your code

**Tip:** Click on any error in the Issue Navigator to jump directly to the problematic line.

### Common Issue 3: Simulator Won't Launch or Gets Stuck

**Problem:** iOS Simulator doesn't open, gets stuck on black screen, or shows "Unable to boot device"
**Solution:**
1. **Quit the Simulator:** Simulator menu > Quit Simulator
2. **Restart Xcode:** Xcode menu > Quit Xcode, then reopen
3. **Reset the Simulator:**
   - Open Simulator app directly (from Applications folder)
   - Go to **Device** > **Erase All Content and Settings**
   - Restart and try again
4. **Try a different Simulator device:**
   - In Xcode's device menu, select a different iPhone model
   - iPhone 16 or iPhone 15 are usually most stable
5. **Advanced reset:**
   ```bash
   # Kill all Simulator processes
   killall Simulator

   # Reset Simulator devices (WARNING: Deletes all Simulator data)
   xcrun simctl shutdown all
   xcrun simctl erase all
   ```

### Common Issue 4: "Cannot Preview in this file"

**Problem:** SwiftUI preview shows an error or won't display
**Solution:**
1. Check that your Mac meets preview requirements (Apple Silicon recommended)
2. Press **Option + Command + P** to resume preview
3. Check for syntax errors in your SwiftUI code
4. **Reset Preview:**
   - In Xcode menu: **Editor** > **Canvas** (toggle off/on)
5. **Clean and Rebuild:**
   - Press **Shift + Command + K** (Clean)
   - Press **Command + B** (Build)
   - Try preview again

**Note:** Previews require macOS Sequoia or later and work best on Apple Silicon Macs.

### Common Issue 5: "Bundle Identifier is Already in Use"

**Problem:** Error message saying the Bundle Identifier conflicts with another app
**Solution:**
1. In Xcode, select your project in the Navigator
2. Select your target under "TARGETS"
3. Go to the **General** tab
4. Under **Identity**, change the **Bundle Identifier** to something unique
   - Add your name or a random number: `com.yourname.MyFirstApp2`
   - Or use a more specific organization identifier: `com.yourname.apps.MyFirstApp`
5. Build again

**Note:** This usually only affects deployment to physical devices or App Store, not Simulator testing.

## Additional Tips

- **Save Frequently:** Press **Command + S** regularly to save your changes, though Xcode auto-saves
- **Use Live Preview:** SwiftUI's live preview is faster than building and running for UI changes
- **Experiment Safely:** Try modifying the ContentView.swift code to learn SwiftUI syntax
- **Explore Templates:** SwiftUI provides built-in code completion; type slowly to see suggestions
- **Simulator Features:** The iOS Simulator supports many features like rotation (Command + Arrow keys), shake gesture, and location simulation
- **Learn SwiftUI:** Apple's SwiftUI tutorials are excellent resources for beginners
- **Keep It Simple:** Start with small changes; don't try to build complex features immediately
- **Version Control:** If you enabled Git, commit your changes regularly to track your progress
- **Multiple Projects:** Create several simple projects to practice different concepts
- **Project Organization:** Keep projects organized in folders like `~/Developer/iOS-Projects/`

**Helpful SwiftUI Modifications to Try:**
```swift
// Change text color
Text("Hello, world!")
    .foregroundColor(.blue)

// Make text larger
Text("Hello, world!")
    .font(.largeTitle)

// Add a button
Button("Tap Me") {
    print("Button tapped!")
}

// Change background
VStack {
    Text("Hello, world!")
}
.background(Color.yellow)
```

## Related Articles

- KB-001: How to download and install Xcode 26.1+ from the Mac App Store
- KB-002: How to verify Xcode installation and check the version number
- KB-004: How to set up an Apple Developer account for iOS development
- KB-009: How to choose between SwiftUI and UIKit for your project
- KB-010: How to set up signing and capabilities for your app project
- KB-011: How to use the iOS Simulator for testing your app (coming soon)
- KB-012: How to understand the basic structure of a SwiftUI app (coming soon)

## Sources

- Apple Developer Documentation - Creating an Xcode project for an app - https://developer.apple.com/documentation/xcode/creating-an-xcode-project-for-an-app (Accessed: November 17, 2025)
- Apple Developer Documentation - Xcode 26.1 Release Notes - https://developer.apple.com/documentation/xcode-release-notes/xcode-26_1-release-notes (Accessed: November 17, 2025)
- Hacking with Swift - Understanding the basic structure of a SwiftUI app - https://www.hackingwithswift.com/books/ios-swiftui/understanding-the-basic-structure-of-a-swiftui-app (Accessed: November 17, 2025)
- Medium - Xcode iOS Project Folder Structure Explained - https://medium.com/@qmshahzad/xcode-ios-project-folder-structure-explained-c888c3161641 (Accessed: November 17, 2025)
- Kodeco - Xcode Project and File Templates - https://www.kodeco.com/26582967-xcode-project-and-file-templates (Accessed: November 17, 2025)
- Code with Chris - Xcode Tutorial for Beginners (2025 Updated for Xcode 16) - https://codewithchris.com/xcode-tutorial/ (Accessed: November 17, 2025)
- Apple Developer Forums - What Does Each Template Do - https://developer.apple.com/forums/thread/69702 (Accessed: November 17, 2025)
- Sign My Code - Most Common Code Signing Issues in Xcode - https://signmycode.com/resources/troubleshooting-common-code-signing-issues-in-xcode-14-15 (Accessed: November 17, 2025)

---
*This article is part of the Claude Code Web knowledge base*
