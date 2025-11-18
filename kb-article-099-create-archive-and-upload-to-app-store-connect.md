# How to create an archive and upload to App Store Connect

**Article ID**: KB-099
**Difficulty**: Advanced
**Last Updated**: November 17, 2025
**Estimated Time**: 30-45 minutes

## Overview

Creating an archive and uploading it to App Store Connect is the final step in preparing your iOS app for distribution through the App Store or TestFlight. Archiving packages your app in a distribution-ready format, including all necessary binaries, resources, and metadata. Xcode 26.1+ streamlines this process with improved validation, automatic code signing options, and direct integration with App Store Connect for seamless upload workflows.

## Prerequisites

Before creating an archive and uploading to App Store Connect, ensure you have:

- **Valid Apple Developer Account**: Active membership with appropriate permissions
- **Code Signing Configured**: Certificates and provisioning profiles set up correctly (see **KB-010: How to set up signing and capabilities for your app project**)
- **App Record Created**: App registered in App Store Connect with matching bundle identifier
- **Build Configuration**: Release build scheme configured for distribution (see **KB-096: Configure bundle identifier and version numbers**)
- **Provisioning Profiles**: Distribution provisioning profiles properly configured (see **KB-097: Set up provisioning profiles for distribution**)
- **App Metadata**: Basic app information and compliance details prepared (see **KB-098: Prepare app metadata for App Store submission**)
- **Xcode 26.1+**: Latest version of Xcode installed and command line tools configured
- **SDK Requirements**: Apps must be built with iOS 26 SDK or later (as of April 2026 requirement)

## Steps

### Step 1: Prepare Your Project for Archiving

Before creating an archive, verify your project configuration:

1. **Select Release Build Configuration**:
   - In Xcode, go to **Product** → **Scheme** → **Edit Scheme**
   - Select **Archive** from the left sidebar
   - Ensure **Build Configuration** is set to **Release** (not Debug)
   - Click **Close**

2. **Verify Version and Build Numbers**:
   - Select your project in the Project Navigator
   - Select your app target
   - Navigate to the **General** tab
   - Verify **Version** (e.g., "1.0.0") and **Build** (e.g., "1") numbers
   - **Important**: Each upload must have a unique build number; the version can remain the same

3. **Check Bundle Identifier**:
   - Ensure the **Bundle Identifier** exactly matches the one registered in App Store Connect
   - Format: `com.yourcompany.yourapp`

4. **Review Signing Configuration**:
   - In the **Signing & Capabilities** tab
   - Ensure **Automatically manage signing** is enabled (recommended) or
   - If manual signing, verify the correct **Distribution** provisioning profile is selected
   - Team should match your Apple Developer account

### Step 2: Select the Archive Destination

Before archiving, you must select the appropriate destination:

1. **Click the Destination Selector** in the Xcode toolbar (next to the scheme selector)

2. **Select Generic iOS Device** or **Any iOS Device (arm64)**:
   - Do **NOT** select a specific simulator or connected device
   - This ensures the archive is built for distribution, not a specific device
   - For Mac apps: Select **Any Mac**
   - For tvOS apps: Select **Generic tvOS Device**
   - For watchOS apps: Select **Generic watchOS Device**

3. **Verify Selection**: The toolbar should show "Generic iOS Device" or the appropriate platform

### Step 3: Create the Archive

With your project prepared, create the archive:

1. **Start the Archive Process**:
   - From the menu bar, select **Product** → **Archive**
   - Alternatively, press **⌘ Command + Shift + B** (if configured)

2. **Wait for Build Completion**:
   - Xcode will clean and build your project in Release configuration
   - Build progress appears in the toolbar
   - The process typically takes 2-10 minutes depending on project size
   - Review any warnings or errors in the build log

3. **Archive Build Success**:
   - If successful, the **Organizer** window opens automatically
   - If it doesn't appear, go to **Window** → **Organizer**
   - Your archive appears in the **Archives** tab, organized by date

### Step 4: Validate the Archive

Before uploading, validate your archive to catch potential issues:

1. **Access the Organizer**:
   - Open **Window** → **Organizer** if not already open
   - Select the **Archives** tab
   - Select your most recent archive from the list

2. **Start Validation**:
   - Click the **Validate App** button in the right panel
   - A sheet appears with distribution options

3. **Choose Distribution Method**:
   - Select **App Store Connect** as the destination
   - Click **Next**

4. **Configure Distribution Options**:
   - **Automatically manage signing**: Recommended for most developers (Xcode handles provisioning)
   - **Manually manage signing**: Select if you manage certificates yourself
   - Click **Next**

5. **Review Export Options**:
   - **Upload your app's symbols to receive symbolicated reports from Apple**: Recommended (enabled by default)
   - **Upload bitcode**: Not applicable for iOS 16+ (deprecated)
   - Click **Next**

6. **Validation Process**:
   - Xcode performs comprehensive validation checks:
     - Code signing verification
     - Entitlements validation
     - Asset catalog validation
     - App Store submission requirements
     - Privacy manifest validation (iOS 17+)
   - This process takes 1-5 minutes

7. **Review Validation Results**:
   - **Success**: "Validation Successful" message appears
   - **Warnings**: Review and address if critical
   - **Errors**: Must be fixed before upload (see Troubleshooting section)

### Step 5: Upload to App Store Connect

After successful validation, upload your archive:

1. **Start Upload Process**:
   - In the Organizer, with your archive selected, click **Distribute App**
   - A distribution method sheet appears

2. **Select Distribution Method**:
   - Choose **App Store Connect**
   - Click **Next**

3. **Choose Upload Method**:
   - **Upload**: Sends the build to App Store Connect (standard option)
   - **Export**: Saves the IPA file locally for upload via Transporter app
   - Select **Upload** for direct upload
   - Click **Next**

4. **Configure Distribution Options**:
   - **TestFlight & App Store**: Default setting for standard distribution
   - **TestFlight Internal Only**: Restricts to internal testing only (cannot be submitted to App Store)
   - **Automatically manage signing**: Recommended
   - **Upload your app's symbols**: Keep enabled
   - Click **Next**

5. **Review and Sign**:
   - Xcode re-signs the app with distribution certificates
   - Review the summary of your archive details:
     - App name and bundle ID
     - Version and build number
     - SDK version
     - Supported architectures (arm64)
   - Click **Upload**

6. **Upload Progress**:
   - Progress indicator shows upload status
   - Upload time varies: 2-15 minutes depending on app size and connection speed
   - **Do not interrupt** the upload process

7. **Upload Completion**:
   - Success message: "App Store Connect upload successful"
   - Click **Done**
   - Your build is now processing on App Store Connect

### Step 6: Verify Upload in App Store Connect

Confirm your build appears in App Store Connect:

1. **Access App Store Connect**:
   - Go to [https://appstoreconnect.apple.com](https://appstoreconnect.apple.com)
   - Sign in with your Apple Developer account

2. **Navigate to Your App**:
   - Click **My Apps**
   - Select your app from the list

3. **Check Build Status**:
   - Click the **TestFlight** tab (for beta testing)
   - Under **Builds**, select your platform (iOS, tvOS, etc.)
   - Your build appears with status **Processing** initially
   - Processing typically takes 10-30 minutes

4. **Build Statuses**:
   - **Processing**: App is being processed by Apple
   - **Missing Compliance**: Export compliance information required
   - **Ready to Submit**: Build is ready for TestFlight or App Review
   - **In Review**: Build is under App Review (for TestFlight external testing)

5. **Complete Export Compliance** (if required):
   - If your app uses encryption, provide compliance information
   - Click the build and answer the encryption questions
   - Most apps can answer "No" to encryption usage

## Expected Results

After successfully completing these steps:

- ✓ Archive appears in Xcode Organizer with current date and time
- ✓ Validation completes without critical errors
- ✓ Upload to App Store Connect succeeds with confirmation message
- ✓ Build appears in App Store Connect TestFlight tab within 30 minutes
- ✓ Build status progresses from "Processing" to "Ready to Submit"
- ✓ No critical warnings in App Store Connect build details
- ✓ Build can be added to internal TestFlight testing groups immediately
- ✓ Build can be submitted for external TestFlight beta review or App Review

## Troubleshooting

### Issue 1: Code Signing Errors During Archive

**Symptoms**:
- "No signing certificate found" error
- "Provisioning profile doesn't include signing certificate"
- "Code signing is required for product type 'Application'"

**Solutions**:

1. **Enable Automatic Signing**:
   - Select your project → Target → **Signing & Capabilities**
   - Check **Automatically manage signing**
   - Select your **Team**
   - Xcode creates necessary certificates and profiles

2. **Verify Certificates in Keychain**:
   - Open **Keychain Access** app
   - Select **login** keychain → **My Certificates**
   - Verify you have valid **Apple Distribution** certificate
   - Certificate should not be expired and have valid private key (indicated by disclosure triangle)
   - If invalid, revoke and regenerate in [https://developer.apple.com/account/resources/certificates](https://developer.apple.com/account/resources/certificates)

3. **Refresh Provisioning Profiles**:
   - Go to **Xcode** → **Settings** → **Accounts**
   - Select your Apple ID → Select your team
   - Click **Download Manual Profiles** button (refresh icon)
   - Return to your project and clean build folder: **Product** → **Clean Build Folder** (⌘ Shift + K)

4. **Check Team Membership**:
   - Verify your Apple Developer account has active membership
   - Visit [https://developer.apple.com/account](https://developer.apple.com/account)
   - Ensure membership is not expired

**Reference**: See Apple Technical Note TN2407 and TN2250 for comprehensive code signing troubleshooting.

### Issue 2: Validation Failures

**Symptoms**:
- "Invalid bundle structure" error
- "Missing required icon" warning
- "Invalid privacy manifest" error
- "Unsupported architecture" error

**Solutions**:

1. **Bundle Structure Issues**:
   - Ensure **Info.plist** contains all required keys
   - Verify **Bundle Identifier** matches App Store Connect exactly
   - Check that all required app icons are present in **Assets.xcassets**
   - Run **Product** → **Clean Build Folder**, then archive again

2. **Icon Requirements**:
   - App icon must include all required sizes in **AppIcon** asset
   - Required sizes for iOS: 1024x1024 (App Store), 60x60, 76x76, 83.5x83.5
   - Icons must be PNG format, no transparency
   - Use **Asset Catalog Creator** or similar tool to generate all sizes

3. **Privacy Manifest Issues (iOS 17+)**:
   - If using required reason APIs, include **PrivacyInfo.xcprivacy** file
   - Declare API usage reasons in the manifest
   - Common APIs requiring declaration: UserDefaults, file timestamps, system boot time
   - See [https://developer.apple.com/documentation/bundleresources/privacy_manifest_files](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files)

4. **Architecture Validation**:
   - Ensure build settings support **arm64** for iOS
   - In Build Settings, search for **Architectures**
   - Should be set to **Standard Architectures (arm64)**
   - Remove any simulator-only architectures

**Reference**: See Apple Technical Note TN3109 for resolving common archiving issues.

### Issue 3: Upload to App Store Connect Fails

**Symptoms**:
- "Authentication failed" error
- "Network connection error" during upload
- "App Store Connect operation failed"
- Upload progress hangs or times out

**Solutions**:

1. **Authentication Issues**:
   - Verify Apple ID credentials in **Xcode** → **Settings** → **Accounts**
   - Sign out and sign back in
   - Generate app-specific password if using 2-factor authentication
   - Ensure account has necessary App Manager or Admin role in App Store Connect

2. **Network Problems**:
   - Check internet connection stability
   - Disable VPN if active (can interfere with Apple services)
   - Try uploading during off-peak hours (early morning)
   - Upload large apps via Ethernet instead of WiFi for stability

3. **Use Transporter as Alternative**:
   - If Xcode upload fails repeatedly, export IPA and use **Transporter** app
   - In Organizer, click **Distribute App** → **App Store Connect** → **Export**
   - Save IPA file locally
   - Download **Transporter** app from Mac App Store
   - Drag IPA into Transporter and deliver to App Store Connect

4. **App Store Connect Record**:
   - Verify app record exists in App Store Connect with matching bundle ID
   - Go to [https://appstoreconnect.apple.com](https://appstoreconnect.apple.com) → **My Apps**
   - If no record exists, create new app entry first
   - Ensure bundle ID exactly matches (case-sensitive)

5. **Check Apple System Status**:
   - Visit [https://developer.apple.com/system-status/](https://developer.apple.com/system-status/)
   - Verify App Store Connect services are operational
   - If services are down, wait and retry later

## Additional Tips

### Build Number Management

- **Incrementing Build Numbers**: Each upload requires a unique build number, even if the version number stays the same
- **Versioning Strategy**:
  - Version number (e.g., 1.0.0) represents user-facing app version
  - Build number (e.g., 1, 2, 3) represents internal build iteration
  - Example: Version 1.0.0 might have builds 1, 2, 3 during testing
- **Automatic Increment**: Consider using build scripts to auto-increment build numbers
- **agvtool**: Use Apple's `agvtool` command-line tool for automated version management:
  ```bash
  # Increment build number
  agvtool next-version -all
  ```
- **Build Number in Archive**: Archive name in Organizer shows both version and build for easy identification

### TestFlight Beta Testing

- **Internal Testing**: Builds are immediately available to up to 100 internal testers after "Ready to Submit" status
- **External Testing**: First build requires App Review approval; subsequent builds may not require full review
- **Distribution Options**:
  - **TestFlight & App Store**: Standard option allowing both beta testing and App Store submission
  - **TestFlight Internal Only**: Restricts build to internal testing only (indicated with "Internal" badge)
- **Automatic Distribution**: Enable in TestFlight groups to auto-deliver new builds to testers
- **Beta Feedback**: TestFlight provides crash logs and tester feedback directly in App Store Connect
- **90-Day Testing Period**: TestFlight builds expire 90 days after upload

### Performance Optimization

- **Archive Size**: Larger archives take longer to upload; consider app thinning and bitcode (deprecated iOS 16+)
- **Symbols Upload**: Always upload symbols for crash report symbolication (human-readable stack traces)
- **Parallel Uploads**: You can upload multiple builds simultaneously for different apps, but not the same app
- **Build While Uploading**: Continue development work while Organizer uploads in background

### Version Control

- **Tag Releases**: After successful upload, tag your git commit with version/build number:
  ```bash
  git tag -a v1.0.0-build1 -m "App Store submission build 1"
  git push origin v1.0.0-build1
  ```
- **Archive Retention**: Xcode stores archives locally; back up important archives to version control or cloud storage
- **Archive Location**: Archives stored at `~/Library/Developer/Xcode/Archives/`

### Xcode 26.1+ Specific Features

- **Enhanced Validation**: Xcode 26.1+ includes improved validation for privacy manifests and required reason APIs
- **SDK Requirements**: As of April 2026, builds must use iOS 26 SDK or later
- **Transporter Integration**: Improved error messaging when using Transporter for uploads
- **Notarization**: macOS apps are automatically notarized during distribution process

## Related Articles

- **KB-010**: How to set up signing and capabilities for your app project
- **KB-096**: Configure bundle identifier and version numbers (prerequisite)
- **KB-097**: Set up provisioning profiles for distribution (prerequisite)
- **KB-098**: Prepare app metadata for App Store submission (prerequisite)
- **KB-004**: How to set up an Apple Developer account for iOS development
- **KB-027**: How to run app on physical device (for pre-submission testing)
- **KB-091**: Use AI to generate comprehensive test suites (quality assurance before archiving)

## Sources

1. **Upload builds - Manage builds - App Store Connect - Help - Apple Developer**
   [https://developer.apple.com/help/app-store-connect/manage-builds/upload-builds/](https://developer.apple.com/help/app-store-connect/manage-builds/upload-builds/)
   Accessed: November 17, 2025

2. **TN3109: Resolving common archiving issues | Apple Developer Documentation**
   [https://developer.apple.com/documentation/technotes/tn3109-resolving-common-archiving-issues](https://developer.apple.com/documentation/technotes/tn3109-resolving-common-archiving-issues)
   Accessed: November 17, 2025

3. **Customizing the Xcode archive process | Apple Developer Documentation**
   [https://developer.apple.com/documentation/security/customizing-the-xcode-archive-process](https://developer.apple.com/documentation/security/customizing-the-xcode-archive-process)
   Accessed: November 17, 2025

4. **Technical Note TN2407: iOS Code Signing Troubleshooting Index**
   [https://developer.apple.com/library/archive/technotes/tn2407/_index.html](https://developer.apple.com/library/archive/technotes/tn2407/_index.html)
   Accessed: November 17, 2025

5. **Technical Note TN2250: iOS Code Signing Troubleshooting**
   [https://developer.apple.com/library/archive/technotes/tn2250/_index.html](https://developer.apple.com/library/archive/technotes/tn2250/_index.html)
   Accessed: November 17, 2025

6. **TestFlight overview - Test a beta version - App Store Connect - Help - Apple Developer**
   [https://developer.apple.com/help/app-store-connect/test-a-beta-version/testflight-overview/](https://developer.apple.com/help/app-store-connect/test-a-beta-version/testflight-overview/)
   Accessed: November 17, 2025

7. **Upcoming Requirements - Apple Developer**
   [https://developer.apple.com/news/upcoming-requirements/](https://developer.apple.com/news/upcoming-requirements/)
   Accessed: November 17, 2025

8. **Validate an archive of your app - Xcode Help**
   [https://help.apple.com/xcode/mac/current/en.lproj/dev37441e273.html](https://help.apple.com/xcode/mac/current/en.lproj/dev37441e273.html)
   Accessed: November 17, 2025

9. **App Store Connect - Help - Apple Developer (Release Notes)**
   [https://developer.apple.com/help/app-store-connect/release-notes/](https://developer.apple.com/help/app-store-connect/release-notes/)
   Accessed: November 17, 2025

10. **Privacy manifest files - Bundle Resources | Apple Developer Documentation**
    [https://developer.apple.com/documentation/bundleresources/privacy_manifest_files](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files)
    Accessed: November 17, 2025
