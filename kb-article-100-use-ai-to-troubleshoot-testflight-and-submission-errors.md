# How to use AI to troubleshoot TestFlight and submission errors

**Article ID:** KB-100
**Difficulty:** Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 30-45 minutes (per issue)

## Overview

App Store Connect and TestFlight submissions often fail with cryptic error codes, validation issues, or beta review rejections that can consume hours of debugging time. This guide demonstrates how to leverage AI coding assistants in Xcode 26.1+ (ChatGPT, Claude, or other models) to rapidly diagnose and resolve submission errors. You'll learn to craft effective prompts that provide AI with proper context, interpret complex ITMS error codes, troubleshoot code signing failures, and resolve beta review rejections—transforming frustrating submission roadblocks into learning opportunities.

## Prerequisites

- Xcode 26.1+ installed with an active project ready for submission
- macOS 15 (Sequoia) or later
- Active Apple Developer Program membership ($99/year)
- At least one AI provider configured in Xcode (see KB-031, KB-046)
- Basic understanding of App Store Connect workflow
- Project configured with proper signing and capabilities (see KB-010)
- Familiarity with Xcode Organizer and Archive validation
- Understanding of TestFlight internal vs external testing concepts

**Recommended prerequisites:**
- KB-097: How to prepare your app for TestFlight submission (Coming Soon)
- KB-099: How to configure App Store Connect for beta testing (Coming Soon)

## Steps

### Step 1: Set Up Error Capture Workflow

Before you can troubleshoot with AI, you need to capture complete error information.

**Configure error logging:**

1. Open Xcode 26.1+
2. Navigate to **Window > Organizer**
3. When validation or upload fails, click **Export** to save the validation report
4. Enable detailed console output: **Product > Scheme > Edit Scheme > Run > Arguments > Environment Variables**
5. Add environment variable: `IDEDistributionLogLevel = 3`

**Create an error documentation template:**

```
Error Context Template (keep this handy for AI prompts):
1. Error Code/Message: [exact error]
2. When it occurred: [Archive/Upload/Validation/TestFlight Processing]
3. Xcode version: [e.g., 26.1.1]
4. iOS deployment target: [e.g., iOS 17.0]
5. Distribution method: [App Store Connect/TestFlight/Ad Hoc]
6. Recent changes: [what changed since last successful build]
7. Full error log: [paste complete console output]
```

This structured format helps AI provide targeted solutions rather than generic advice.

### Step 2: Understand Common Error Categories with AI

Ask AI to explain the error landscape before diving into specific issues.

**Prompt for AI:**
```
Explain the different categories of App Store submission errors:
1. ITMS errors (with common examples and causes)
2. Code signing errors
3. Archive validation errors
4. TestFlight processing errors
5. Beta review rejection reasons
6. API usage and privacy manifest issues

For each category, provide 2-3 real-world examples with error codes.
```

**Expected AI Explanation:**

The AI should explain these major categories:

**1. ITMS Errors (iTunes Metadata Store):**
- ITMS-90725: SDK Version Issue - Using outdated SDK
- ITMS-90683: Missing Purpose String - Privacy keys missing from Info.plist
- ITMS-91053: Missing API declaration - Required privacy manifests
- ITMS-90717: Invalid App Store Icon - Icon format/transparency issues
- ITMS-90562: Invalid Bundle - Architecture or bitcode problems

**2. Code Signing Errors:**
- Profile/certificate mismatches
- Expired provisioning profiles
- Missing private keys in keychain
- Team ID mismatches between target and provisioning profile

**3. Archive Validation Errors:**
- Missing required architectures (arm64)
- Invalid entitlements
- Embedded provisioning profile issues
- App Thinning failures

**4. TestFlight Processing Errors:**
- Builds stuck at "Processing" for 24+ hours
- Processing failed with generic error messages
- Missing export compliance information
- Corrupted binary issues

**5. Beta Review Rejections:**
- Missing demo account credentials
- Apps displaying "beta" or "test" text
- Guideline violations (crashers, incomplete features)
- In-app purchase configuration issues

**6. Privacy and API Issues:**
- Missing NSCameraUsageDescription and similar privacy keys
- Missing required reason API declarations
- Privacy manifest not included in frameworks

### Step 3: Troubleshoot ITMS Error Codes with AI

Use AI to decode cryptic ITMS errors and get actionable solutions.

**Scenario: You receive ITMS-90683 error**

**Error message:**
```
ERROR ITMS-90683: "Missing Purpose String in Info.plist.
Your app's code references one or more APIs that access sensitive user data.
The app's Info.plist file should contain a NSPhotoLibraryUsageDescription
key with a user-facing purpose string explaining clearly and completely
why your app needs the data."
```

**Prompt for AI:**
```
I received this App Store validation error:

ERROR ITMS-90683: "Missing Purpose String in Info.plist - NSPhotoLibraryUsageDescription"

My app is a social media app that allows users to upload photos.

Please:
1. Explain why this error occurred
2. Show me the exact Info.plist entry I need to add
3. Provide a user-friendly purpose string example
4. Explain how to verify all required privacy keys are present
5. List all common privacy usage description keys I should check
```

**Expected AI Response:**

```swift
// AI will explain the error and provide solution:

/*
EXPLANATION:
iOS requires apps to declare why they access sensitive user data.
Your app's code (or a third-party framework) references photo library APIs,
triggering this requirement.

SOLUTION:
Add this entry to your Info.plist file:
*/

// In Info.plist (XML format):
<key>NSPhotoLibraryUsageDescription</key>
<string>We need access to your photo library so you can select and share photos with your friends in the app.</string>

// In Info.plist (Xcode UI format):
// 1. Open Info.plist in Xcode
// 2. Click the + button
// 3. Select "Privacy - Photo Library Usage Description"
// 4. Add the description string

/*
COMMON PRIVACY KEYS TO CHECK:
*/

NSCameraUsageDescription // "Privacy - Camera Usage Description"
NSPhotoLibraryUsageDescription // "Privacy - Photo Library Usage Description"
NSPhotoLibraryAddUsageDescription // "Privacy - Photo Library Additions Usage Description"
NSLocationWhenInUseUsageDescription // "Privacy - Location When In Use Usage Description"
NSLocationAlwaysUsageDescription // "Privacy - Location Always Usage Description"
NSMicrophoneUsageDescription // "Privacy - Microphone Usage Description"
NSContactsUsageDescription // "Privacy - Contacts Usage Description"
NSCalendarsUsageDescription // "Privacy - Calendars Usage Description"
NSRemindersUsageDescription // "Privacy - Reminders Usage Description"
NSMotionUsageDescription // "Privacy - Motion Usage Description"
NSHealthShareUsageDescription // "Privacy - Health Share Usage Description"
NSHealthUpdateUsageDescription // "Privacy - Health Update Usage Description"
NSBluetoothAlwaysUsageDescription // "Privacy - Bluetooth Always Usage Description"
NSUserTrackingUsageDescription // "Privacy - Tracking Usage Description"

// VERIFICATION SCRIPT:
// Ask AI to generate a script to scan your codebase for API usage
```

**Follow-up prompt to generate verification script:**
```
Generate a grep/ripgrep command to scan my codebase for photo library API usage
that would trigger the NSPhotoLibraryUsageDescription requirement.
```

### Step 4: Resolve Code Signing Errors with AI Assistance

Code signing errors are among the most frustrating. Use AI to systematically diagnose them.

**Scenario: Code signing failure during archive**

**Error message:**
```
Code Signing Error: Code signing is required for product type 'Application'
in SDK 'iOS 18.2'

No signing certificate "iOS Distribution" found: No "iOS Distribution"
signing certificate matching team ID "ABC123XYZ" with a private key was found.
```

**Comprehensive AI prompt:**
```
I'm getting this code signing error when archiving my iOS app:

[paste complete error message]

My setup:
- Xcode: 26.1.1
- Target iOS: 17.0
- Distribution: App Store Connect
- Team: [Your Team Name]
- Last successful archive: 3 days ago
- Changes since: Updated pods, added new framework

Please provide:
1. Step-by-step diagnosis of this specific error
2. How to verify my certificate is installed correctly
3. Commands to check keychain for private keys
4. How to regenerate/download the correct certificate
5. Common mistakes that cause this error
6. How to sync Xcode with App Store Connect certificates
```

**Expected AI troubleshooting steps:**

```bash
# AI will provide systematic diagnosis:

# STEP 1: Check installed certificates
# Run this command in Terminal:
security find-identity -v -p codesigning

# Expected output should include:
# 1) ABC123... "Apple Distribution: Your Name (ABC123XYZ)"
# If missing, you need to download the certificate

# STEP 2: Verify private key exists
# In Keychain Access:
# 1. Open Keychain Access.app
# 2. Select "login" keychain
# 3. Select "My Certificates" category
# 4. Find "Apple Distribution: Your Name"
# 5. Expand the arrow - you should see a private key underneath
# If no private key: You need to create a new certificate or import from another Mac

# STEP 3: Sync Xcode with App Store Connect
# 1. Open Xcode > Settings > Accounts
# 2. Select your Apple ID
# 3. Click "Download Manual Profiles"
# 4. Wait for sync to complete

# STEP 4: Check Team ID matches
# In Xcode project settings:
# 1. Select your target
# 2. Signing & Capabilities tab
# 3. Verify Team matches your Developer Portal team
# 4. Check "Automatically manage signing" is enabled (recommended)

# STEP 5: Clean derived data and retry
rm -rf ~/Library/Developer/Xcode/DerivedData/*
# Then restart Xcode and try archiving again

# STEP 6: Verify provisioning profile
# In Terminal:
cd ~/Library/MobileDevice/Provisioning\ Profiles
ls -la
# Check modification dates - profiles should be recent

# If automatic signing fails, try manual signing:
# 1. Disable "Automatically manage signing"
# 2. Go to developer.apple.com/account
# 3. Certificates, Identifiers & Profiles > Profiles
# 4. Create/Download App Store Distribution profile
# 5. Import into Xcode
# 6. Select manually in project settings
```

**Follow-up AI prompt if issue persists:**
```
I've completed all those steps but still getting the error.
Here's the output from security find-identity:
[paste output]

What's the next troubleshooting step?
```

### Step 5: Debug TestFlight Processing Failures

Handle the dreaded "Processing" status that never completes.

**Scenario: Build stuck at "Processing" for 24+ hours**

**Prompt for AI:**
```
My TestFlight build has been stuck on "Processing" in App Store Connect for 30 hours.

Build details:
- Build number: 47
- Upload time: 30 hours ago
- iOS SDK: 18.2
- No email from Apple about issues
- Previous build (46) processed successfully in 15 minutes

What should I do? Provide:
1. Common causes of stuck processing
2. How long to wait before taking action
3. Steps to diagnose the issue
4. Whether to upload a new build or wait
5. How to contact Apple support if needed
6. How to prevent this in future builds
```

**Expected AI Response:**

```markdown
# TESTFLIGHT PROCESSING TROUBLESHOOTING

## Common Causes:
1. **Bitcode processing issues** - Apple processes bitcode on their servers
2. **App Thinning failures** - Errors during app slicing for different devices
3. **Backend infrastructure issues** - Sometimes Apple's systems have delays
4. **Invalid binary** - Corrupted upload or validation issue
5. **Missing export compliance** - Build needs manual export compliance review

## Recommended Actions:

### Immediate Steps (if stuck 24+ hours):

1. **Upload a new build with incremented build number**
   - This is recommended even by Apple support
   - Increment CFBundleVersion in Info.plist (e.g., 47 → 48)
   - Archive and submit again
   - The stuck build often gets "unstuck" when new build appears

2. **Check for email from Apple**
   - Check spam/junk folders
   - Look for messages about:
     - Export compliance needed
     - Missing beta review information
     - Validation issues not caught during upload

3. **Verify build actually uploaded**
   - Go to Activity tab in App Store Connect
   - Check if build shows any status beyond "Processing"
   - Look for error indicators

### Diagnostic Checks:

```bash
# Check if bitcode is causing issues
# In Xcode project settings:
# Target > Build Settings > Search "Enable Bitcode"
# Try setting to "No" for next build

# Check build log for warnings
# Xcode > Window > Organizer > Archives
# Right-click archive > Show in Finder
# Check accompanying logs
```

### When to Contact Apple:

Contact Apple Developer Support if:
- Build stuck > 48 hours
- Multiple consecutive builds fail to process
- You receive error emails without resolution
- Urgent release needed

**Contact method:**
1. Go to developer.apple.com/contact
2. Select "App Review"
3. Select "TestFlight and Beta App Review"
4. Explain the stuck build with Build number and upload timestamp

### Prevention for Future Builds:

1. **Disable bitcode** if not needed (most apps don't need it)
2. **Keep build sizes reasonable** (< 4GB for TestFlight)
3. **Upload during off-peak hours** (avoid US business hours)
4. **Test with internal testers first** (they don't require App Review)
5. **Increment build numbers sequentially** (avoid skipping numbers)
6. **Complete export compliance proactively** in App Store Connect

### Build Number Increment Command:

```bash
# Automatically increment build number before upload
agvtool next-version -all
```

If uploading a new build doesn't resolve it within 24 hours,
this indicates a potential Apple infrastructure issue requiring
direct support contact.
```

### Step 6: Resolve Beta Review Rejections with AI

TestFlight beta review rejections require different strategies than App Store rejections.

**Scenario: Beta review rejection - missing demo account**

**Rejection message in Resolution Center:**
```
Guideline 2.1 - Performance - App Completeness

We were unable to review your app as the app requires a login,
but the necessary credentials were not provided.

Next Steps:
To help us proceed with the review of your app, please provide
demo account credentials in the Review Notes section of
App Store Connect.
```

**Prompt for AI:**
```
My TestFlight beta review was rejected with this message:
[paste rejection message]

My app is a B2B SaaS platform that requires company-specific login.

Help me:
1. Understand why TestFlight requires demo accounts
2. Create a proper demo account strategy
3. Write comprehensive Review Notes
4. Decide what demo account capabilities to provide
5. Understand if beta review guidelines differ from App Store review
6. Resubmit properly
```

**Expected AI guidance:**

```markdown
# BETA REVIEW DEMO ACCOUNT STRATEGY

## Why TestFlight Requires Demo Accounts:

Even beta testing requires Apple to review your app for:
- Basic functionality verification
- Compliance with content guidelines
- Absence of crashes or major bugs
- No offensive/harmful content

TestFlight App Review uses the same guidelines as App Store Review,
but may be slightly less thorough. Demo accounts are mandatory.

## Creating Proper Demo Accounts:

### Account Characteristics:
1. **Permanent** - Don't expire during review (3-7 day window)
2. **Fully functional** - Access to all advertised features
3. **Pre-populated data** - Don't show empty screens
4. **No payment required** - Free access to test all features
5. **Clear credentials** - Simple username/password

### Example Demo Account Setup:

```
Username: demo@yourapp.com
Password: TestFlight2025!

OR for phone-based login:
Phone: +1 (555) 123-4567
Verification code: Any code (disable verification for this account)
```

## Writing Comprehensive Review Notes:

```
Template for App Review Information section:

---
DEMO ACCOUNT CREDENTIALS:

Username: demo@yourapp.com
Password: TestFlight2025!

ACCOUNT DETAILS:
- This is a pre-configured demo account with sample data
- No payment information required
- Full access to all features

HOW TO TEST:
1. Launch app and tap "Sign In"
2. Enter the credentials above
3. You will see a pre-populated dashboard with sample projects
4. Test features: [list 3-4 key features to try]

NOTES:
- This app is designed for [target user type]
- [Any special testing considerations]
- [Explanation of any unusual UI/UX]

CONTACT:
For review questions: support@yourapp.com
---
```

## What Features to Enable:

### ✅ Demo Account Should Include:
- Read access to all screens
- Ability to create/edit sample items
- All free features fully functional
- Sample/dummy data pre-loaded
- Push notifications (if implemented)
- Any onboarding flows

### ❌ Demo Account Can Exclude:
- Payment processing (can be simulated/mocked)
- Actual data from real users
- Admin/destructive capabilities
- Third-party integrations requiring real credentials

## Resubmission Process:

1. **Update Review Notes in App Store Connect:**
   - App Store Connect > TestFlight > iOS Builds
   - Select your build
   - Click "Provide Export Compliance Information" if needed
   - Add demo credentials to "Test Information" section

2. **Don't upload a new build** (unless code changes needed)
   - The same build can be resubmitted for beta review
   - Just update the metadata/review information

3. **Resubmit for Beta Review:**
   - Select the build
   - Click "Submit for Review"
   - Review will typically complete in 24-48 hours

## Common Beta Review vs App Store Review Differences:

| Aspect | Beta Review | App Store Review |
|--------|-------------|------------------|
| Depth of testing | Lighter testing | Thorough testing |
| Tolerance for bugs | Higher (it's a beta) | Very low |
| Content review | Same guidelines | Same guidelines |
| Feature completeness | Core features must work | All features must work |
| Crashers | Major crashers rejected | Any crashers rejected |

## Additional Tips:

- **Remove "Beta" text** from app if visible to avoid confusion
- **Test demo account yourself** before submission
- **Include screenshot walkthrough** if app has complex flow
- **Explain any server-side features** that may not work in review environment
```

### Step 7: Handle SDK and API Requirement Errors

Modern submission errors often relate to SDK versions and required reason APIs.

**Scenario: ITMS-90725 SDK Version error**

**Error message:**
```
ERROR ITMS-90725: "SDK Version Issue. This app was built with the iOS 17.4 SDK.
All iOS and iPadOS apps submitted to the App Store must be built with the
iOS 18 SDK or later, included in Xcode 26.1 or later."
```

**Prompt for AI:**
```
I'm getting ITMS-90725 error saying I need iOS 18 SDK, but I'm already using Xcode 26.1.

Error: [paste error]

My setup:
- Xcode: 26.1.1 (from Mac App Store)
- iOS deployment target: 17.0
- Base SDK: iOS (in build settings)

Why am I still getting this error? Help me:
1. Understand the difference between deployment target and SDK
2. Verify which SDK my build actually used
3. Fix the SDK version issue
4. Check for other tools that might be using old SDK (CI/CD)
5. Prevent this error in future builds
```

**Expected AI explanation:**

```markdown
# SDK VERSION TROUBLESHOOTING

## Understanding the Error:

**Deployment Target** ≠ **Build SDK**

- **Deployment Target**: Minimum iOS version users need (e.g., iOS 17.0)
  - This is fine to be older
  - Controls what APIs you can use

- **Build SDK**: The SDK used to compile the app (e.g., iOS 18.2 SDK in Xcode 26.1)
  - Must be latest SDK (iOS 18+) as of April 2025
  - Automatically set by Xcode version

## Diagnosing Which SDK Was Actually Used:

```bash
# Extract SDK version from IPA/archive
# Method 1: Check from .app bundle
cd ~/Library/Developer/Xcode/Archives
# Find your latest archive
cd [YourArchive].xcarchive/Products/Applications/
otool -l YourApp.app/YourApp | grep -A 5 LC_VERSION_MIN_IPHONEOS

# Method 2: Check build settings
# In Xcode:
# 1. Select your target
# 2. Build Settings
# 3. Search for "Base SDK"
# 4. Should show "iOS" (uses latest from Xcode)

# Method 3: Check Xcode version used for build
xcodebuild -version
# Should show: Xcode 26.1.1 or later
```

## Common Causes and Fixes:

### Cause 1: Using Xcode Command Line Tools from Old Xcode

```bash
# Check which Xcode is active
xcode-select -p

# Should show: /Applications/Xcode.app/Contents/Developer

# If it shows old Xcode path, fix with:
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer

# Verify Xcode version
xcodebuild -version
# Must show Xcode 26.1 or later
```

### Cause 2: CI/CD Using Old Xcode Image

If using CI/CD (GitHub Actions, Bitrise, etc.):

```yaml
# GitHub Actions - update to use Xcode 26.1+
- uses: maxim-lobanov/setup-xcode@v1
  with:
    xcode-version: '26.1.1'

# CircleCI - update xcode version
macos:
  xcode: "26.1.1"

# Bitrise - select latest stack with Xcode 26.1+
# Update stack in Bitrise workflow settings
```

### Cause 3: Cached Archive from Old Xcode

```bash
# Delete all old archives and rebuild
# Xcode > Window > Organizer > Archives
# Delete old archives or:
rm -rf ~/Library/Developer/Xcode/Archives/*

# Clean derived data
rm -rf ~/Library/Developer/Xcode/DerivedData/*

# Clean build in Xcode
# Product > Clean Build Folder (⌘⇧K)

# Archive again
# Product > Archive
```

### Cause 4: Third-Party Tool Building with Old SDK

```bash
# If using fastlane or other build tools
# Update fastlane to latest:
bundle update fastlane

# Verify fastlane uses correct Xcode:
fastlane run print_tools

# Set Xcode version in Fastfile:
xcodes(version: "26.1.1")
```

## Verification Checklist:

```
✅ Xcode 26.1.1 or later installed
✅ Xcode is from Mac App Store or developer.apple.com (not beta)
✅ xcode-select points to correct Xcode
✅ Base SDK set to "iOS" (not hard-coded to iOS 17)
✅ Clean build (no cached artifacts)
✅ Archive created with correct Xcode
✅ CI/CD using Xcode 26.1+ image (if applicable)
```

## Prevention:

1. **Update Xcode via App Store**
   - Enable automatic updates
   - Check after WWDC and quarterly

2. **Use Latest SDK setting**
   - Base SDK: iOS (not specific version)
   - This auto-updates with Xcode

3. **Monitor Apple requirements**
   - https://developer.apple.com/news/
   - Typically announced 6 months before enforcement

4. **Test on CI/CD early**
   - Update CI/CD images before submission deadline
   - Test a build from CI to catch SDK issues
```

### Step 8: Troubleshoot Privacy Manifest and Required Reason API Errors

As of 2025, Apple requires privacy manifests for certain APIs.

**Scenario: ITMS-91053 Missing API Declaration**

**Error message:**
```
ITMS-91053: Missing API declaration - Your app's code references one or more APIs
that require reasons, including the following API categories: NSPrivacyAccessedAPICategoryFileTimestamp.
While no action is required at this time, starting May 1, 2025, you must include
NSPrivacyAccessedAPITypes in your privacy manifest to continue uploading apps.
```

**Prompt for AI:**
```
I received ITMS-91053 error about missing Required Reason API declarations.

Error: [paste full error]

My app uses: Firebase SDK, Alamofire, and some file system operations.

Please help me:
1. Understand what Required Reason APIs are
2. Identify which of my dependencies need privacy manifests
3. Create a privacy manifest file
4. Add proper API declarations
5. Verify my third-party SDKs include their own manifests
6. Test that the error is resolved before resubmitting
```

**Expected AI guidance:**

```markdown
# REQUIRED REASON API & PRIVACY MANIFEST GUIDE

## What Are Required Reason APIs?

Starting in 2025, certain APIs require you to declare:
1. **Which APIs** you're using from specific categories
2. **Why** you're using them (from approved reason codes)

Affected API categories:
- **File timestamp APIs** (NSFileCreationDate, NSFileModificationDate, etc.)
- **System boot time APIs** (systemUptime)
- **Disk space APIs** (volumeAvailableCapacityKey)
- **Active keyboard APIs** (activeInputModes)
- **User defaults APIs** (UserDefaults in some contexts)

## Creating Privacy Manifest File:

### Step 1: Add PrivacyInfo.xcprivacy to your project

```
File > New > File > App Privacy > Privacy Manifest
Save as: PrivacyInfo.xcprivacy (must be this exact name)
Add to your app target
```

### Step 2: Configure Privacy Manifest

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <!-- Privacy Tracking Domains (if applicable) -->
    <key>NSPrivacyTracking</key>
    <false/>

    <!-- Collected Data Types (if you collect data) -->
    <key>NSPrivacyCollectedDataTypes</key>
    <array>
        <!-- Add collected data types here -->
    </array>

    <!-- Required Reason API Usage -->
    <key>NSPrivacyAccessedAPITypes</key>
    <array>
        <!-- File Timestamp APIs -->
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryFileTimestamp</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
                <!-- Reason codes from Apple documentation -->
                <string>C617.1</string> <!-- Example: Displaying file timestamps to user -->
                <string>DDA9.1</string> <!-- Example: Accessing file timestamps to determine file age -->
            </array>
        </dict>

        <!-- System Boot Time APIs (if used) -->
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategorySystemBootTime</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
                <string>35F9.1</string> <!-- Measuring time between events -->
            </array>
        </dict>

        <!-- Disk Space APIs (if used) -->
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryDiskSpace</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
                <string>E174.1</string> <!-- Checking available space before writing -->
            </array>
        </dict>

        <!-- User Defaults APIs (if used inappropriately) -->
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryUserDefaults</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
                <string>CA92.1</string> <!-- Accessing user defaults for app functionality -->
            </array>
        </dict>
    </array>
</dict>
</plist>
```

## Common Reason Codes:

### File Timestamp APIs (NSPrivacyAccessedAPICategoryFileTimestamp):
- **C617.1**: Displaying file timestamps to the user
- **3B52.1**: Accessing file timestamps for file management
- **DDA9.1**: Determining file age for cache management
- **0A2A.1**: No actual file timestamp access (false positive)

### System Boot Time APIs:
- **35F9.1**: Measuring time between events in the app
- **8FFB.1**: Calculating absolute elapsed time
- **3D61.1**: No actual access to system boot time

### Disk Space APIs:
- **E174.1**: Checking available space before writing files
- **85F4.1**: Displaying storage UI to user
- **7D9E.1**: Including SDK that checks disk space
- **B728.1**: No actual access to disk space APIs

### User Defaults APIs:
- **CA92.1**: App functionality (reading app preferences)
- **1C8F.1**: No actual UserDefaults access

## Identifying Which APIs Your App Uses:

```bash
# Use AI to generate a script to scan for API usage

# Scan for File Timestamp API usage:
grep -r "NSFileModificationDate\|NSFileCreationDate\|fileModificationDate\|creationDate" . --include="*.swift" --include="*.m"

# Scan for System Boot Time:
grep -r "systemUptime\|mach_absolute_time" . --include="*.swift" --include="*.m"

# Scan for Disk Space APIs:
grep -r "volumeAvailableCapacity\|NSURLVolumeAvailableCapacityKey" . --include="*.swift" --include="*.m"

# Scan for problematic UserDefaults usage:
grep -r "UserDefaults\.standard\|NSUserDefaults" . --include="*.swift" --include="*.m"
```

## Handling Third-Party SDKs:

Many SDKs now include their own PrivacyInfo.xcprivacy:

```bash
# Check if Firebase includes privacy manifest:
find ~/Library/Developer/Xcode/DerivedData -name "PrivacyInfo.xcprivacy"

# Update dependencies to latest versions with manifests:
# For CocoaPods:
pod update

# For Swift Package Manager:
# File > Packages > Update to Latest Package Versions
```

**Major SDKs with Privacy Manifests (as of 2025):**
- Firebase (v10.19.0+)
- Facebook SDK (v17.0+)
- Google Analytics (v11.0+)
- Alamofire (v5.9.0+)

If SDK doesn't include manifest and triggers error:
1. Update to latest SDK version
2. Contact SDK maintainer
3. Add their API usage to your manifest

## Verification Before Resubmission:

```bash
# Method 1: Check archive for privacy manifest
cd ~/Library/Developer/Xcode/Archives
# Navigate to latest archive
find . -name "PrivacyInfo.xcprivacy"
# Should list your manifest and any SDK manifests

# Method 2: Generate privacy report
# Xcode > Product > Archive
# After archiving: Distribute App > App Store Connect > Export
# Check "Generate App Privacy Report"
# Review the generated report for completeness
```

## Testing:

1. Create archive with privacy manifest included
2. Validate archive (don't upload yet)
   - Window > Organizer > Archives
   - Select archive > Validate App
3. Check validation results for privacy warnings
4. If no warnings, proceed with upload
```

### Step 9: Use AI to Generate Prevention Scripts

Create automated checks to prevent future submission errors.

**Prompt for AI:**
```
Create a pre-submission validation script that checks:
1. All required privacy keys present in Info.plist
2. Privacy manifest exists and includes required APIs
3. App icons meet requirements (no alpha channel)
4. Build number incremented from last submitted build
5. SDK version is iOS 18+ (Xcode 26.1+)
6. Code signing is valid
7. No "TODO" or "FIXME" in user-facing strings

Generate this as a bash script I can run before each submission,
with clear output showing pass/fail for each check.
```

**Expected AI-generated script:**

```bash
#!/bin/bash
# Pre-Submission Validation Script
# Run before archiving for App Store/TestFlight

set -e

echo "🚀 Pre-Submission Validation Starting..."
echo "========================================"

# Colors for output
GREEN='\033[0;32m'
RED='\033[0;31m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

ERRORS=0
WARNINGS=0

# Check 1: Xcode version
echo -e "\n📱 Checking Xcode Version..."
XCODE_VERSION=$(xcodebuild -version | head -1 | sed 's/Xcode //')
XCODE_MAJOR=$(echo $XCODE_VERSION | cut -d. -f1)
if [ "$XCODE_MAJOR" -ge 26 ]; then
    echo -e "${GREEN}✓${NC} Xcode $XCODE_VERSION (iOS 18+ SDK)"
else
    echo -e "${RED}✗${NC} Xcode $XCODE_VERSION too old. Need Xcode 26.1+"
    ((ERRORS++))
fi

# Check 2: Privacy keys in Info.plist
echo -e "\n🔐 Checking Privacy Usage Descriptions..."
INFO_PLIST=$(find . -name "Info.plist" -path "*/YourApp/*" -not -path "*/Pods/*" | head -1)

REQUIRED_KEYS=(
    "NSCameraUsageDescription"
    "NSPhotoLibraryUsageDescription"
    "NSLocationWhenInUseUsageDescription"
    # Add your required keys
)

for KEY in "${REQUIRED_KEYS[@]}"; do
    if /usr/libexec/PlistBuddy -c "Print :$KEY" "$INFO_PLIST" &> /dev/null; then
        echo -e "${GREEN}✓${NC} $KEY present"
    else
        echo -e "${YELLOW}⚠${NC} $KEY missing (only if API used)"
        ((WARNINGS++))
    fi
done

# Check 3: Privacy Manifest exists
echo -e "\n📋 Checking Privacy Manifest..."
if [ -f "YourApp/PrivacyInfo.xcprivacy" ]; then
    echo -e "${GREEN}✓${NC} PrivacyInfo.xcprivacy found"
else
    echo -e "${YELLOW}⚠${NC} PrivacyInfo.xcprivacy not found (required if using certain APIs)"
    ((WARNINGS++))
fi

# Check 4: App Icons (no alpha channel)
echo -e "\n🎨 Checking App Icons..."
ICON_SET=$(find . -name "AppIcon.appiconset" | head -1)
if [ -d "$ICON_SET" ]; then
    ALPHA_ICONS=$(find "$ICON_SET" -name "*.png" -exec file {} \; | grep "with alpha" | wc -l)
    if [ "$ALPHA_ICONS" -eq 0 ]; then
        echo -e "${GREEN}✓${NC} App icons have no alpha channel"
    else
        echo -e "${RED}✗${NC} $ALPHA_ICONS icon(s) have alpha channel (not allowed for App Store icon)"
        ((ERRORS++))
    fi
else
    echo -e "${YELLOW}⚠${NC} Could not locate AppIcon.appiconset"
    ((WARNINGS++))
fi

# Check 5: Build number incremented
echo -e "\n🔢 Checking Build Number..."
CURRENT_BUILD=$(/usr/libexec/PlistBuddy -c "Print :CFBundleVersion" "$INFO_PLIST")
echo -e "${GREEN}ℹ${NC} Current build: $CURRENT_BUILD"
echo -e "${YELLOW}⚠${NC} Verify this is incremented from last submission"

# Check 6: Code Signing
echo -e "\n✍️  Checking Code Signing..."
CERT_COUNT=$(security find-identity -v -p codesigning | grep -c "Apple Distribution" || true)
if [ "$CERT_COUNT" -gt 0 ]; then
    echo -e "${GREEN}✓${NC} Distribution certificate found ($CERT_COUNT)"
else
    echo -e "${RED}✗${NC} No Apple Distribution certificate found"
    ((ERRORS++))
fi

# Check 7: Scan for TODO/FIXME in localized strings
echo -e "\n📝 Scanning for TODOs in user-facing strings..."
TODO_COUNT=$(grep -r "TODO\|FIXME" . --include="*.strings" --include="Localizable.swift" 2>/dev/null | wc -l || echo "0")
if [ "$TODO_COUNT" -eq 0 ]; then
    echo -e "${GREEN}✓${NC} No TODOs found in localized strings"
else
    echo -e "${YELLOW}⚠${NC} Found $TODO_COUNT TODO/FIXME in strings files"
    ((WARNINGS++))
fi

# Summary
echo -e "\n========================================"
echo -e "📊 Validation Summary:"
echo -e "   Errors: $ERRORS"
echo -e "   Warnings: $WARNINGS"

if [ "$ERRORS" -eq 0 ]; then
    echo -e "\n${GREEN}✅ Validation PASSED - Ready to archive!${NC}"
    exit 0
else
    echo -e "\n${RED}❌ Validation FAILED - Fix errors before submitting${NC}"
    exit 1
fi
```

Save as `pre-submission-check.sh`, make executable with `chmod +x pre-submission-check.sh`, then run before each submission.

### Step 10: Document Recurring Issues with AI Assistance

Create a knowledge base of your app's specific submission issues.

**Prompt for AI:**
```
Help me create a submission issues log template that I can maintain for my project.

Include sections for:
1. Date and error code
2. Full error message
3. Root cause analysis
4. Solution that worked
5. Prevention steps
6. Time to resolve
7. AI prompts that helped

Generate the template as a markdown document.
```

**Use this to build institutional knowledge and reduce future debugging time.**

## Expected Results

After mastering AI-assisted troubleshooting, you should experience:

1. **Faster error resolution** - Reduce debugging time from hours to 15-30 minutes per issue
2. **Higher submission success rate** - Catch errors before submission with validation scripts
3. **Deeper understanding** - AI explanations help you learn submission requirements
4. **Systematic approach** - Methodical troubleshooting instead of trial-and-error
5. **Reusable solutions** - Build a library of prompts and scripts for common errors
6. **Confidence in submissions** - Predictable outcomes instead of anxiety
7. **Better error prevention** - Proactive checks prevent recurring issues

**Specific success indicators:**
- Archive validation passes on first attempt
- TestFlight builds process in under 30 minutes
- Beta review approvals within 24 hours
- Zero ITMS errors during upload
- Clean code signing without manual intervention
- Privacy manifest properly configured
- Automated pre-submission checks pass

## Troubleshooting

### Common Issue 1: AI Provides Generic Solutions That Don't Apply

**Problem:** AI gives general advice that doesn't solve your specific error.

**Solution:**
Improve your prompts with more context:

```
Bad prompt:
"How do I fix code signing errors?"

Good prompt:
"I'm getting this exact error: [paste full error]
My setup: Xcode 26.1.1, Team ID ABC123XYZ, Distribution method App Store Connect
Recent changes: Added new framework, updated CocoaPods
Previous successful build: 3 days ago using same setup
Here's my security find-identity output: [paste output]
Provide step-by-step diagnosis specific to THIS error."
```

Include:
- Exact error messages (copy-paste, don't summarize)
- Your environment details
- What you've already tried
- Recent changes to the project
- Output from diagnostic commands

### Common Issue 2: AI Suggests Outdated Solutions

**Problem:** AI recommends deprecated approaches or references old Xcode versions.

**Solution:**
Add version constraints to prompts:

```
Prompt template:
"Using Xcode 26.1+ and App Store Connect as of November 2025, [your question]
Please provide solutions compatible with:
- iOS 18 SDK
- Swift 6
- Latest App Store requirements as of 2025
Avoid deprecated approaches like Bitcode or UIWebView."
```

If AI gives outdated advice, respond:
```
"That solution references [deprecated feature]. Please provide
an updated solution for Xcode 26.1+ and November 2025
App Store submission requirements."
```

### Common Issue 3: Error Persists After Following AI Advice

**Problem:** You implement AI's solution but still get the same error.

**Solution:**
Provide feedback loop to AI:

```
"I followed these steps: [list what you did]
The error now shows: [new error or same error]
Here's the command output: [paste output]
What should I try next? Are there any diagnostic commands
I can run to get more information about the root cause?"
```

**Escalation path:**
1. Try AI solution (5-10 minutes)
2. If fails, provide diagnostic output to AI (5 minutes)
3. Try refined solution (5-10 minutes)
4. If still fails after 3 iterations, search Apple Developer Forums for exact error
5. Contact Apple Developer Support (if urgent) or file radar (if bug)

### Common Issue 4: Too Many Errors to Troubleshoot at Once

**Problem:** Validation returns 15+ errors, overwhelming to address.

**Solution:**
Prioritize with AI:

```
"I received multiple validation errors. Help me prioritize which to fix first:

Errors:
1. ITMS-90683: Missing NSCameraUsageDescription
2. ITMS-90717: Invalid App Store Icon
3. ITMS-91053: Missing privacy manifest
4. Code signing error with Team ID
5. [etc...]

Which errors are:
- Blockers (prevent submission)?
- Quick fixes (< 5 minutes)?
- Related (fix one solves multiple)?

Provide a priority order and estimated time for each."
```

Generally fix in this order:
1. Code signing errors (blocks everything)
2. SDK version issues (blocks submission)
3. Privacy manifests and Info.plist keys (regulatory)
4. Asset issues (icons, launch screens)
5. Warning-level issues (can sometimes submit with these)

### Common Issue 5: AI Can't Access Error Details in App Store Connect

**Problem:** AI needs information from App Store Connect dashboard that you can't easily share.

**Solution:**
Take screenshots and use AI's image analysis (if available):

```
For ChatGPT or Claude with vision capabilities:
1. Take screenshot of error in App Store Connect
2. Include screenshot in prompt: "I'm seeing this error in App Store Connect [attach image]
   What does this mean and how do I fix it?"

For text-only AI:
1. Copy all visible text from error page
2. Include full JSON response if visible in browser dev tools:
   - Open browser developer console (F12)
   - Network tab
   - Find the API call that returned error
   - Copy response body
   - Paste into AI prompt with context
```

## Additional Tips

### Optimizing AI Model Selection for Different Error Types

**Use ChatGPT when:**
- Quick lookups of common errors (fast responses)
- Generating validation scripts
- Scanning code for API usage
- Following established Apple documentation

**Use Claude Sonnet 4+ when:**
- Complex multi-step debugging
- Analyzing long error logs
- Understanding interdependencies between errors
- Explaining nuanced code signing flows
- Reviewing privacy manifest configurations

**Use both in parallel:**
- Ask same question to both models
- Compare responses for comprehensive understanding
- ChatGPT often faster, Claude often more thorough

### Building an Error Resolution Database

Create a searchable log of past errors:

```markdown
# submission-errors-log.md

## 2025-11-15: ITMS-90683 NSPhotoLibraryUsageDescription

**Error:** Missing Purpose String in Info.plist
**Cause:** Updated to iOS 18 SDK which enforces privacy strings
**Solution:** Added NSPhotoLibraryUsageDescription to Info.plist
**Time to resolve:** 10 minutes
**Helpful AI prompt:** "Explain ITMS-90683 and show Info.plist entry for photo library access"
**Prevention:** Run privacy key checker script before archiving

## 2025-11-10: Code Signing - No matching provisioning profile

[Continue documenting...]
```

Search this log before asking AI to see if you've solved it before.

### Creating Custom AI Instructions for Your Project

For AI services that support custom instructions or system prompts:

```
My iOS project context:
- App: [Your App Name]
- Deployment target: iOS 17.0
- Xcode: 26.1.1
- Distribution: App Store Connect
- Team ID: ABC123XYZ
- Third-party SDKs: Firebase, Alamofire, SDWebImage
- Special requirements: Health Kit integration, StoreKit 2

When helping with submission errors:
- Always assume latest 2025 App Store requirements
- Consider my SDK dependencies when diagnosing
- Provide commands I can copy-paste
- Explain why errors occur, not just how to fix
```

### Automating Error Capture

Create a shell function to capture errors automatically:

```bash
# Add to ~/.zshrc or ~/.bash_profile
capture_xcode_error() {
    echo "Paste error message, then press Ctrl+D:"
    ERROR=$(cat)
    echo "\n$ERROR" >> ~/xcode_errors.log
    echo "Error logged. Opening AI chat..."
    # Could integrate with AI CLI tools here
}
```

### Learning from TestFlight Feedback

When beta testers report issues:

```
Prompt for AI:
"A TestFlight tester reports: [paste feedback]

Their device: iPhone 15 Pro, iOS 18.1
Our build: 1.2.0 (47)

Help me:
1. Identify potential causes
2. Determine if this could cause App Store rejection
3. Suggest debugging steps
4. Decide if I need to upload new build or can ship current one
```

### Submission Checklist Workflow

```
Pre-submission (1 hour before archiving):
□ Run pre-submission validation script
□ Ask AI: "Review my recent changes and identify potential submission issues"
□ Update build number
□ Test on multiple devices/OS versions
□ Review TestFlight feedback from previous build

During archive/upload (30 min):
□ Monitor console for warnings
□ Capture any error messages immediately
□ Don't click "retry" more than twice - investigate instead

Post-upload (within 24 hours):
□ Check email for compliance issues
□ Monitor App Store Connect for processing status
□ If beta review needed, ensure demo account is valid
□ Be available to respond to reviewer questions
```

## Related Articles

- KB-031: How to access the coding assistant in Xcode 26 (ChatGPT)
- KB-046: How to switch between ChatGPT and Claude in the coding assistant
- KB-010: How to set up signing and capabilities for your app project
- KB-055: How to have AI explain error messages and suggest fixes
- KB-039: How to fix errors and bugs with ChatGPT assistance
- KB-097: How to prepare your app for TestFlight submission (Coming Soon)
- KB-099: How to configure App Store Connect for beta testing (Coming Soon)

## Sources

- Apple Developer Documentation - App Store Connect API Error Handling - https://developer.apple.com/documentation/appstoreconnectapi/interpreting-and-handling-errors (Accessed: November 17, 2025)
- Apple Developer Documentation - App and Submission Statuses - https://developer.apple.com/help/app-store-connect/reference/app-and-submission-statuses (Accessed: November 17, 2025)
- Apple Developer Documentation - Managing Submissions with Unresolved Issues - https://developer.apple.com/help/app-store-connect/manage-submissions-to-app-review/manage-a-submission-with-unresolved-issues/ (Accessed: November 17, 2025)
- Apple Developer Technical Note TN2250 - iOS Code Signing Troubleshooting - https://developer.apple.com/library/archive/technotes/tn2250/_index.html (Accessed: November 17, 2025)
- Apple Developer Technical Note TN2294 - Xcode Validation and Submission Issues for iOS - https://developer.apple.com/library/archive/technotes/tn2294/_index.html (Accessed: November 17, 2025)
- Apple Developer Documentation - Describing data use in privacy manifests - https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/describing_data_use_in_privacy_manifests (Accessed: November 17, 2025)
- Apple Developer Documentation - Describing use of required reason API - https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/describing_use_of_required_reason_api (Accessed: November 17, 2025)
- Codemagic Blog - Issues with uploading to the App Store and how to solve them - https://blog.codemagic.io/app-store-connect-issues/ (Accessed: November 17, 2025)
- Stack Overflow - Multiple TestFlight builds stuck on Processing - https://stackoverflow.com/questions/52977768/multiple-testflight-builds-stuck-on-processing-including-already-processed-on (Accessed: November 17, 2025)
- Stack Overflow - iOS Testflight There was an error loading your builds - https://stackoverflow.com/questions/27089131/ios-testflight-there-was-an-error-loading-your-builds (Accessed: November 17, 2025)
- Apple Developer Forums - TestFlight Issues - https://developer.apple.com/forums/tags/testflight (Accessed: November 17, 2025)
- Apple Developer Forums - App Submission Issues - https://developer.apple.com/forums/tags/app-submission (Accessed: November 17, 2025)
- Simon B. Stöckli - Using Claude with Coding Assistant in Xcode 26 - https://simonbs.dev/posts/using-claude-with-coding-assistant-in-xcode-26/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
