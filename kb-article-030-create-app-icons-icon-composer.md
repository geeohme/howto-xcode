# Knowledge Base Article: KB-030

**Title:** How to create app icons using Icon Composer for all platforms
**Topic:** Xcode v26.1+
**Difficulty Level:** Beginner
**Article ID:** KB-030
**Date Created:** November 17, 2025
**Last Updated:** November 17, 2025

---

## Overview

Icon Composer is Apple's new tool introduced with Xcode 26 that enables developers to create layered app icons featuring the Liquid Glass design system for all Apple platforms. Instead of managing multiple export pipelines and dozens of icon sizes, Icon Composer lets you create a single `.icon` file from your design layers that works seamlessly across iPhone, iPad, Mac, and Apple Watch.

This revolutionary tool replaces the traditional asset catalog workflow for app icons by introducing a multi-layer format with dynamic Liquid Glass properties, real-time preview capabilities with dynamic lighting effects, and automatic size generation for all platforms. The Liquid Glass material brings icons to life with expressive properties that respond to lighting and automatically scale across different contexts.

Icon Composer comes bundled with Xcode 26 and integrates directly into your development workflow, allowing you to sync changes from your design tool directly into your Xcode project with a single file.

**What You'll Learn:**
- How to download and set up Icon Composer
- How to prepare and export design layers for Icon Composer
- How to import layers and configure Liquid Glass properties
- How to customize icons for different platforms and appearance modes
- How to integrate `.icon` files into Xcode projects
- Troubleshooting common compatibility issues

---

## Prerequisites

Before creating app icons with Icon Composer, ensure you have:

**System Requirements:**
- macOS Sequoia 15.3 or later
- Xcode 26.0 or later (Xcode 26.1+ for latest features)
- Apple Developer account (for downloading Icon Composer)

**Design Tools:**
- A vector design tool capable of exporting SVG files (recommended):
  - Figma
  - Sketch
  - Adobe Illustrator
  - Adobe Photoshop
- Or a raster tool that exports PNG with transparency

**Design Assets:**
- Icon artwork at 1024x1024px canvas size
- Separate layers for foreground elements
- Vector formats (SVG) preferred for scalability
- PNG for complex gradients or raster effects

**Knowledge Requirements:**
- Basic understanding of app icon design principles
- Familiarity with layer-based design tools
- Basic Xcode project navigation

---

## Step-by-Step Guide

### Step 1: Download and Install Icon Composer

Icon Composer is available as a separate download from Apple's Additional Tools page.

**Download Process:**

1. Visit the Apple Developer Downloads page:
   ```
   https://developer.apple.com/download/all/
   ```

2. Sign in with your Apple Developer account

3. Search for "Icon Composer" or "Additional Tools for Xcode 26"

4. Download the Icon Composer disk image (.dmg)

5. Open the downloaded DMG file and drag Icon Composer to your Applications folder

**Verification:**

```bash
# Launch Icon Composer from Terminal (optional)
open -a "Icon Composer"
```

Icon Composer should launch successfully on macOS Sequoia 15.3 or later.

---

### Step 2: Design Your Icon Artwork

Create your icon design using your preferred design tool with proper layer organization.

**Design Specifications:**

**Canvas Size:**
- iPhone, iPad, Mac: 1024x1024px
- Apple Watch: Uses same canvas with platform-specific adjustments
- Grid: Use the new rounded-rectangle shape from Apple Design Resources

**Layer Guidelines:**

1. **Layer Count:** Use 1-4 layer groups for optimal visual complexity
   - 1 layer: Simple, flat icons
   - 2-3 layers: Recommended for most icons
   - 4 layers: Maximum complexity for depth effects

2. **Layer Organization:**
   - Name layers descriptively (e.g., "background", "symbol", "highlight")
   - Number layers by Z-order if desired (e.g., "01-background", "02-symbol")
   - Keep transparency areas clear
   - Use fully opaque artwork

3. **Design Best Practices:**
   - Design for the Liquid Glass material effect
   - Consider how layers will interact with lighting
   - Test foreground elements against various backgrounds
   - Avoid extremely thin lines or tiny details

**Get Apple Templates:**

Download official templates from Apple Design Resources:
```
https://developer.apple.com/design/resources/
```

Templates available for:
- Figma
- Sketch
- Adobe Photoshop
- Adobe Illustrator

---

### Step 3: Export Layers from Your Design Tool

Export individual layers as separate files for import into Icon Composer.

**SVG Export (Recommended):**

SVG format provides the best scalability and is ideal for vector artwork.

**Export Settings:**
- Format: SVG
- Size: Export at full canvas size (1024x1024px)
- Artboard: Include artboard to maintain positioning
- Text: Convert to outlines/paths before exporting
- Transparency: Preserve transparent background

**Adobe Illustrator Users:**

Apple provides a custom script to automate layer-to-SVG export:

1. Download the "Layer to SVG" script from Apple Design Resources
2. Place script in: `~/Library/Application Support/Adobe/Illustrator/Scripts/`
3. In Illustrator: File > Scripts > Layer to SVG
4. Select output folder and export

**PNG Export (Alternative):**

Use PNG for layers with:
- Custom gradients that can't be expressed in SVG
- Raster images or textures
- Complex effects that require pixel-perfect rendering

**Export Settings:**
- Format: PNG-24
- Size: 1024x1024px
- Color Mode: RGB
- Transparency: Enabled (alpha channel)
- Compression: Lossless

**Naming Convention:**

```
icon-01-background.svg
icon-02-symbol.svg
icon-03-highlight.svg
icon-04-shadow.png
```

Numbering ensures correct import order (Z-index from back to front).

---

### Step 4: Import Layers into Icon Composer

Launch Icon Composer and create your layered icon structure.

**Interface Overview:**

Icon Composer's interface consists of three main sections:
- **Left Sidebar:** Layer management and organization
- **Center Preview Panel:** Real-time icon preview with lighting
- **Right Inspector:** Layer properties and Liquid Glass controls

**Import Process:**

1. **Create New Icon:**
   ```
   File > New Icon
   ```

2. **Import Layers:**
   - Drag SVG or PNG files from Finder into the Icon Composer sidebar
   - Layers import in the order dropped
   - Alternatively: Click the "+" button and select files

3. **Reorder Layers (if needed):**
   - Drag layers up or down in the sidebar
   - Bottom layer = back, top layer = front
   - Maintain logical Z-order for depth

4. **Group Layers (optional):**
   - Select multiple layers: Cmd + Click
   - Right-click > Group Layers
   - Maximum 4 groups recommended

**Auto-Applied Properties:**

When you import layers, Icon Composer automatically:
- Enables Liquid Glass material (can be toggled per layer)
- Centers artwork on canvas
- Applies default Liquid Glass properties

---

### Step 5: Configure Liquid Glass Properties

Customize the Liquid Glass material to bring your icon to life.

**Available Properties:**

Each layer can have the following Liquid Glass properties adjusted:

**1. Liquid Glass Toggle:**
```
Inspector > Liquid Glass: ON/OFF
```
Enable or disable the Liquid Glass effect per layer.

**2. Fill & Opacity:**
```
Inspector > Fill: Color picker
Inspector > Opacity: 0-100%
```
Adjust base color and transparency of the layer.

**3. Blend Mode:**
```
Inspector > Blend Mode: Normal, Multiply, Screen, Overlay, etc.
```
Control how layers composite together.

**4. Specular Highlights:**
```
Inspector > Specular: 0-100%
```
Controls intensity of light reflection on the glass surface. Higher values create more pronounced glossy effects.

**5. Blur Intensity:**
```
Inspector > Blur: 0-100%
```
Adds depth-of-field effects and softness to layers.

**6. Translucency:**
```
Inspector > Translucency: 0-100%
```
Determines how much light passes through the layer, creating glass-like transparency.

**7. Shadows:**
```
Inspector > Shadow: Neutral or Chromatic
Inspector > Shadow Intensity: 0-100%
```
- **Neutral:** Grayscale shadow for depth
- **Chromatic:** Colored shadow derived from layer colors

**Example Configuration:**

```
Layer: Background
- Liquid Glass: ON
- Fill: #3A7BD5 (Blue)
- Opacity: 100%
- Blend Mode: Normal
- Specular: 40%
- Blur: 0%
- Translucency: 20%
- Shadow: Neutral, 60%

Layer: Symbol
- Liquid Glass: ON
- Fill: #FFFFFF (White)
- Opacity: 95%
- Blend Mode: Normal
- Specular: 80%
- Blur: 5%
- Translucency: 10%
- Shadow: Chromatic, 30%
```

**Real-Time Preview:**

The center preview panel updates immediately as you adjust properties, showing:
- Dynamic lighting effects
- Layer interactions
- Material response to light
- Overall composition

---

### Step 6: Customize for Platforms and Modes

Icon Composer allows platform-specific and appearance mode customizations from a single design.

**Platform Customization:**

**Preview Switcher:**
```
Preview Panel > Platform Selector:
- iPhone
- iPad
- Mac
- Apple Watch
```

By default, all platforms use the same design, but you can customize each:

1. **Select Target Platform** in the preview panel

2. **Enable Platform Override:**
   ```
   Inspector > Platform Overrides > Enable [Platform]
   ```

3. **Adjust Properties** specific to that platform

4. **Common Platform Adjustments:**
   - **Apple Watch:** Reduce complexity, increase contrast
   - **Mac:** Add more detail, refined shadows
   - **iPhone/iPad:** Balance between detail and simplicity

**Appearance Mode Customization:**

Icon Composer supports three appearance modes:

1. **Default (Light Mode):**
   - Standard appearance for light backgrounds
   - Full color and vibrancy

2. **Dark Mode:**
   - Optimized for dark backgrounds
   - Adjusted contrast and brightness

3. **Tinted Mode:**
   - Single-color tinted appearance
   - Used in certain UI contexts (Focus modes, etc.)

**Configure Appearance Modes:**

1. **Select Appearance:**
   ```
   Preview Panel > Mode Selector: Default / Dark / Tinted
   ```

2. **Enable Mode Override:**
   ```
   Inspector > Appearance > Override for [Mode]
   ```

3. **Adjust Layer Properties** for the selected mode

**Example Dark Mode Adjustments:**

```
Dark Mode Overrides:
- Increase overall opacity by 10-20%
- Boost specular highlights by 10-15%
- Reduce shadow intensity by 20-30%
- Adjust fill colors to be slightly brighter
```

**Testing Across Modes:**

Quickly switch between modes using keyboard shortcuts:
```
Cmd + 1: Default Mode
Cmd + 2: Dark Mode
Cmd + 3: Tinted Mode
```

---

### Step 7: Save Your Icon File

Save your layered icon in the new `.icon` format for Xcode integration.

**Save Process:**

1. **Save Icon:**
   ```
   File > Save (Cmd + S)
   ```

2. **Choose Location:**
   - Save to your Xcode project directory
   - Recommended: Create an "Assets" or "Icons" folder
   - Example: `~/Projects/MyApp/Assets/AppIcon.icon`

3. **File Naming:**
   - Use descriptive names without spaces
   - Match the name you'll reference in Xcode
   - Examples: `AppIcon.icon`, `AppIcon-iOS.icon`

**File Format:**

The `.icon` file is NOT a simple image—it's a package containing:
- Original layer sources (SVG/PNG)
- Liquid Glass property metadata
- Platform and appearance mode overrides
- Preview cache data

**File Size:**

Typical `.icon` files range from 50KB to 2MB depending on:
- Number of layers
- Use of PNG vs SVG
- Complexity of artwork

---

### Step 8: Add Icon to Xcode Project

Integrate your Icon Composer file into your Xcode project.

**Integration Steps:**

1. **Open Your Xcode Project:**
   ```bash
   cd /path/to/your/project
   open YourApp.xcodeproj
   ```

2. **Add Icon File to Project:**

   **Method A: Drag and Drop**
   - Drag `AppIcon.icon` from Finder
   - Drop into the Project Navigator sidebar
   - Select target folder (e.g., project root or Assets)
   - Check "Copy items if needed"
   - Click "Finish"

   **Method B: Add Files Menu**
   ```
   File > Add Files to "YourApp"...
   - Navigate to AppIcon.icon
   - Select file
   - Check "Copy items if needed"
   - Select target(s)
   - Click "Add"
   ```

3. **Verify File Added:**
   - Icon file appears in Project Navigator with `.icon` extension
   - File type shows as "Icon Composer Document"

**Configure App Icon in Target Settings:**

1. **Select Your Target:**
   - In Project Navigator, click on project file (top)
   - Select your app target from the list

2. **Open General Tab:**
   ```
   Target > General > App Icons and Launch Screen
   ```

3. **Set App Icon:**
   - Locate "App Icon" text field
   - Enter the name WITHOUT extension: `AppIcon`
   - Match the name of your `.icon` file exactly

4. **Verify Configuration:**
   - Icon preview should appear next to the text field
   - Preview shows the icon with Liquid Glass effects

**Build and Test:**

```bash
# Clean build folder
Cmd + Shift + K

# Build project
Cmd + B

# Run on simulator or device
Cmd + R
```

The app should now use your Icon Composer icon with full Liquid Glass effects on iOS 26+, iPadOS 26+, macOS 26+, and watchOS 26+.

---

### Step 9: Export Marketing Assets (Optional)

Icon Composer can export static assets for marketing use.

**Export Process:**

1. **In Icon Composer:**
   ```
   File > Export...
   ```

2. **Choose Export Options:**
   - **Format:** PNG or JPEG
   - **Size:** Select from standard sizes (1024px, 512px, etc.)
   - **Platform:** Choose which platform view to export
   - **Mode:** Select appearance mode (Default, Dark, Tinted)

3. **Save Exported Assets:**
   - Choose destination folder
   - Files export as static raster images
   - Use for App Store screenshots, marketing materials, website

**Common Export Sizes:**

```
App Store: 1024x1024px (PNG, no alpha)
Marketing: 512x512px, 256x256px (PNG with alpha)
Social Media: 512x512px (JPEG or PNG)
```

---

## Expected Results

After successfully implementing Icon Composer icons in your Xcode project:

**Visual Results:**

1. **Icon Appearance:**
   - Icons display with Liquid Glass material effects
   - Dynamic lighting responds to system lighting changes
   - Smooth transitions between light/dark/tinted modes
   - Platform-appropriate scaling and rendering

2. **Home Screen (iOS/iPadOS):**
   - Icon shows depth and glass-like quality
   - Specular highlights reflect light dynamically
   - Shadow effects add dimension
   - Icon integrates with iOS 26 design language

3. **Dock (macOS):**
   - Icon displays with proper macOS scaling
   - Maintains Liquid Glass effects in Dock
   - Adapts to light/dark mode automatically

4. **Watch Face (watchOS):**
   - Icon optimized for small display
   - Maintains visibility and recognition
   - Adapts to watch face complications

**Technical Results:**

1. **Automatic Size Generation:**
   Xcode automatically generates all required icon sizes:
   ```
   iPhone: 40x40, 60x60, 76x76, 83.5x83.5, 120x120, 152x152, 167x167, 180x180, 1024x1024
   iPad: Same as iPhone
   Mac: 16x16, 32x32, 64x64, 128x128, 256x256, 512x512, 1024x1024
   Watch: 48x48, 55x55, 58x58, 87x87, 172x172, 196x196, 216x216
   ```

2. **Build Output:**
   ```
   Build succeeded
   No icon-related warnings or errors
   App bundle contains properly formatted icons
   ```

3. **File Structure:**
   ```
   YourApp.app/
   ├── AppIcon~ios-marketing.png
   ├── AppIcon@2x.png
   ├── AppIcon@3x.png
   └── [Additional auto-generated sizes]
   ```

**Performance:**

- No performance impact on app launch
- Icons load instantly
- Smooth rendering across all platforms
- Proper memory management

---

## Troubleshooting

### Issue 1: Icon Composer Won't Launch

**Symptoms:**
- Icon Composer crashes on launch
- Error: "Icon Composer cannot be opened"
- Application not responding

**Solutions:**

```bash
# Verify macOS version
sw_vers
# ProductVersion should be 15.3 or later

# Check Icon Composer integrity
codesign -v /Applications/Icon\ Composer.app

# Reset Icon Composer preferences
defaults delete com.apple.IconComposer

# Reinstall Icon Composer
# Download fresh copy from Apple Developer Downloads
```

**Additional Checks:**
- Ensure macOS Sequoia 15.3+
- Verify Apple Silicon or Intel compatibility
- Check system security settings (System Settings > Privacy & Security)

---

### Issue 2: SVG Import Fails or Appears Blank

**Symptoms:**
- Dragging SVG shows no preview
- Layer appears but is invisible
- "Unsupported format" error

**Solutions:**

**Check SVG Compatibility:**

```bash
# Verify SVG file is valid
cat your-icon.svg | head -n 5
# Should show XML header: <?xml version="1.0"?>
```

**Common SVG Issues:**

1. **Text Not Converted to Paths:**
   - Reopen in design tool
   - Select all text: Type > Create Outlines (Illustrator)
   - Re-export as SVG

2. **Unsupported Effects:**
   - Expand appearance: Object > Expand Appearance
   - Rasterize complex effects to PNG instead

3. **Incorrect Export Settings:**
   - Use SVG 1.1 (not SVG Tiny or Basic)
   - Enable "Presentation Attributes" (Illustrator)
   - Include artboard in export

**Workaround:**
Export problematic layers as PNG (1024x1024px, transparent background) instead.

---

### Issue 3: Xcode Doesn't Recognize .icon File

**Symptoms:**
- Dragging .icon to Xcode shows no file
- "Unsupported file type" error
- Icon doesn't appear in Project Navigator

**Solutions:**

1. **Verify Xcode Version:**
   ```bash
   xcodebuild -version
   # Should show: Xcode 26.0 or later
   ```

2. **Update Xcode:**
   - Open App Store
   - Search for Xcode
   - Install Xcode 26.1 or later

3. **Manual Addition:**
   ```bash
   # Copy icon to project directory
   cp AppIcon.icon ~/Projects/YourApp/

   # In Xcode: File > Add Files to "YourApp"
   # Select AppIcon.icon
   # Check "Copy items if needed"
   ```

4. **Check File Integrity:**
   ```bash
   # Verify .icon file is a valid package
   ls -la AppIcon.icon
   # Should show as directory (package)

   # Check contents
   ls -la AppIcon.icon/
   # Should contain metadata and assets
   ```

---

### Issue 4: Icon Doesn't Apply to Built App

**Symptoms:**
- Build succeeds but app shows default icon
- Icon not visible on device/simulator
- App icon appears as blank

**Solutions:**

1. **Verify Target Configuration:**
   ```
   Target > General > App Icons and Launch Screen
   App Icon field: "AppIcon" (no extension)
   ```

2. **Check Icon Name Matching:**
   ```bash
   # Icon file name: AppIcon.icon
   # Xcode setting: AppIcon (no .icon)

   # They must match exactly (case-sensitive)
   ```

3. **Clean Build:**
   ```
   Product > Clean Build Folder (Cmd + Shift + K)
   Product > Build (Cmd + B)
   ```

4. **Reset Simulator:**
   ```
   Device > Erase All Content and Settings...
   # Then rebuild and run
   ```

5. **Check Info.plist:**
   ```xml
   <!-- Should NOT contain old icon references -->
   <!-- Remove if present: -->
   <key>CFBundleIcons</key>
   <key>CFBundleIconFiles</key>
   ```

6. **Verify Deployment Target:**
   ```
   Target > General > Deployment Info
   Minimum Deployments: iOS 26.0 (for full Liquid Glass support)
   ```

---

### Issue 5: Backward Compatibility Problems (iOS 18 and Earlier)

**Symptoms:**
- App works on iOS 26 but crashes on iOS 18
- Icons don't show on older OS versions
- Build warnings about missing assets

**Known Issue:**

As of Xcode 26.1, there is **no supported way** to use Icon Composer `.icon` files for iOS 26+ while maintaining traditional asset catalog icons for iOS 18 and earlier in the same build.

**Current Limitations:**
- Xcode 26.1+ ignores asset catalog icons when deployment target is iOS 26.0+
- Setting deployment target below iOS 26.0 causes `.icon` files to be ignored
- Cannot have both icon types in a single build

**Solutions:**

**Option 1: Drop iOS 18 Support (Recommended for New Apps)**

```
Target > General > Deployment Info
iOS: 26.0
```

This gives you full Icon Composer and Liquid Glass support.

**Option 2: Use Xcode 26.0 (Not 26.1+)**

Xcode 26.0 has better backward compatibility:
- Download Xcode 26.0 from Apple Developer Downloads
- Use alongside Xcode 26.1+ (rename: Xcode26.app)
- Build with Xcode 26.0 for apps supporting iOS 18-26

**Option 3: Maintain Separate Builds**

```bash
# iOS 26+ build with Icon Composer
Deployment Target: iOS 26.0
Icons: AppIcon.icon

# iOS 18+ build with Asset Catalog
Deployment Target: iOS 18.0
Icons: AppIcon.xcassets
```

Distribute as separate app versions.

**Option 4: Wait for Apple Fix**

Monitor Xcode updates and Apple Developer Forums for official backward compatibility support.

---

### Issue 6: Alternate Icons Not Working

**Symptoms:**
- `setAlternateIconName()` doesn't work with `.icon` files
- Multiple `.icon` files not recognized as alternates
- Runtime error when switching icons

**Known Issue:**

Icon Composer has limited support for alternate app icons as of Xcode 26.1.

**Partial Workaround:**

If you had asset catalog icons named `AppIcon`, `AppIcon2`, `AppIcon3`:

1. **Create matching .icon files:**
   ```
   AppIcon.icon
   AppIcon2.icon
   AppIcon3.icon
   ```

2. **Add all to Xcode project**

3. **Use standard alternate icon API:**
   ```swift
   // This MAY work on iOS 26, not guaranteed
   UIApplication.shared.setAlternateIconName("AppIcon2") { error in
       if let error = error {
           print("Error setting icon: \(error.localizedDescription)")
       }
   }
   ```

**Current Recommendation:**

Alternate icon support with Icon Composer is not fully functional. For apps requiring alternate icons:
- Use traditional asset catalogs (.xcassets) instead
- Monitor Apple Developer Forums for updates
- File feedback with Apple (https://feedbackassistant.apple.com)

---

### Issue 7: Liquid Glass Effects Not Visible

**Symptoms:**
- Icon looks flat, no glass effect
- No dynamic lighting or depth
- Appears like standard PNG icon

**Solutions:**

1. **Verify iOS/macOS Version:**
   ```
   Liquid Glass requires:
   - iOS 26.0+
   - iPadOS 26.0+
   - macOS 26.0+
   - watchOS 26.0+
   ```

2. **Check Layer Properties:**
   - In Icon Composer, ensure "Liquid Glass" toggle is ON for layers
   - Verify Specular, Translucency, and Shadow values are > 0

3. **Test in Different Lighting:**
   - Physical device: Move under different light sources
   - Simulator: Change simulator appearance (Cmd + Shift + A)

4. **Rebuild Icon:**
   - Increase specular highlights to 60%+
   - Add translucency at 15-30%
   - Enable chromatic shadows
   - Re-save and rebuild

---

## Additional Tips

### Design Best Practices

**Layer Composition:**

1. **Keep It Simple:**
   - Use 2-3 layers for most icons
   - Avoid overly complex compositions
   - Test readability at small sizes (40x40px)

2. **Foreground Focus:**
   - Primary content should be in foreground layers
   - Use background layers for depth, not detail
   - Ensure icon is recognizable without Liquid Glass

3. **Color Strategy:**
   - Use vibrant, saturated colors for better Liquid Glass effect
   - Avoid extremely dark colors in foreground (< 20% brightness)
   - Test against both light and dark backgrounds

**Liquid Glass Tuning:**

1. **Start Conservative:**
   ```
   Initial Settings:
   - Specular: 30-40%
   - Blur: 0-10%
   - Translucency: 10-20%
   - Shadow: 40-50%
   ```

2. **Iterate:**
   - Gradually increase effects
   - Test on actual device
   - Get feedback from different lighting conditions

3. **Platform Differences:**
   - **Apple Watch:** Higher contrast, lower translucency
   - **Mac:** More subtle effects, refined shadows
   - **iPhone/iPad:** Balanced approach

### Performance Optimization

**Reduce File Size:**

1. **Prefer SVG over PNG:**
   - SVG files are typically 5-10x smaller
   - Better scalability
   - Faster processing

2. **Optimize PNG Exports:**
   ```bash
   # Use ImageOptim or similar tool
   imageoptim icon-layer.png

   # Or use pngcrush
   pngcrush -reduce icon-layer.png icon-layer-optimized.png
   ```

3. **Minimize Layers:**
   - Merge similar layers
   - Remove unnecessary decorative elements
   - Combine effects where possible

**Build Time:**

Icon Composer processing adds minimal build time:
- Simple icons (1-2 layers): < 1 second
- Complex icons (3-4 layers): 1-3 seconds

---

### Version Control

**Git Integration:**

Add `.icon` files to version control:

```bash
# .icon files are directories/packages
# Track them as directories

git add AppIcon.icon
git commit -m "Add Icon Composer app icon with Liquid Glass"
```

**.gitignore Considerations:**

```gitignore
# DON'T ignore .icon files
# Do ignore derived icon assets (if generated separately)
**/GeneratedIcons/
```

**Large Files:**

If `.icon` files are very large (> 5MB):

```bash
# Consider using Git LFS
git lfs track "*.icon"
git add .gitattributes
git add AppIcon.icon
git commit -m "Add app icon with Git LFS"
```

---

### Workflow Automation

**Design to Icon Script (Advanced):**

For teams with frequent icon updates:

```bash
#!/bin/bash
# auto-export-icon.sh

# 1. Export layers from Figma/Sketch using API or plugin
# 2. Auto-import to Icon Composer (requires AppleScript)
# 3. Apply standard Liquid Glass preset
# 4. Save to project directory

osascript <<EOF
tell application "Icon Composer"
    activate
    open POSIX file "/path/to/layers"
    -- Additional scripting to apply settings
    save document 1 in POSIX file "/path/to/project/AppIcon.icon"
end tell
EOF
```

**Note:** Icon Composer AppleScript support may be limited; check documentation.

---

### Testing Checklist

Before releasing your app with Icon Composer icons:

**Visual Testing:**

- [ ] Icon looks correct on iOS 26 device (light mode)
- [ ] Icon looks correct on iOS 26 device (dark mode)
- [ ] Icon looks correct in tinted mode
- [ ] Icon is recognizable at smallest size (40x40px)
- [ ] Liquid Glass effects are visible and appropriate
- [ ] Icon works on iPad
- [ ] Icon works on Mac (if applicable)
- [ ] Icon works on Apple Watch (if applicable)

**Technical Testing:**

- [ ] Build succeeds without warnings
- [ ] No console errors related to icons
- [ ] App launches normally
- [ ] Icon appears in Settings app
- [ ] Icon appears in Spotlight search
- [ ] Icon appears in Notifications
- [ ] App Store build includes proper icons

**Device Testing:**

- [ ] iPhone 16 Pro Max
- [ ] iPhone SE (3rd gen)
- [ ] iPad Pro
- [ ] Mac with Apple Silicon
- [ ] Apple Watch Series 10

---

### Migration from Asset Catalogs

If migrating from traditional `.xcassets` app icons:

**Migration Steps:**

1. **Backup Existing Icons:**
   ```bash
   cp -r Assets.xcassets/AppIcon.appiconset ~/Desktop/AppIcon-backup
   ```

2. **Extract Largest Icon:**
   - Locate 1024x1024px icon from `.appiconset`
   - Use as reference for new design

3. **Create Icon Composer Version:**
   - Design layers based on existing icon
   - Import to Icon Composer
   - Match colors and style

4. **Add to Project:**
   - Keep `.xcassets` for backward compatibility (if needed)
   - Add `.icon` file for iOS 26+
   - Configure deployment target appropriately

5. **Test Both:**
   - Test with iOS 26 (uses `.icon`)
   - Test with iOS 18 (uses `.xcassets` if properly configured)

**Note:** Full dual-icon support is limited in Xcode 26.1+. See Troubleshooting Issue 5.

---

### Resources and Templates

**Official Apple Resources:**

1. **Design Templates:**
   ```
   https://developer.apple.com/design/resources/
   ```
   - Icon templates for Figma, Sketch, Photoshop, Illustrator
   - Grid and guide layers
   - Platform-specific specifications

2. **WWDC Session:**
   ```
   WWDC25 Session 361: Create icons with Icon Composer
   https://developer.apple.com/videos/play/wwdc2025/361/
   ```
   - Complete video tutorial
   - Best practices from Apple designers
   - Advanced techniques

3. **Human Interface Guidelines:**
   ```
   https://developer.apple.com/design/human-interface-guidelines/app-icons
   ```
   - Updated for Liquid Glass design system
   - Platform-specific guidelines
   - Accessibility considerations

**Third-Party Tools:**

1. **Icon Generators:**
   - AppIconBuilder (supports Icon Composer export)
   - Icon.app (macOS)

2. **Design Resources:**
   - SF Symbols for consistent icon language
   - Apple's design templates (Figma/Sketch)

---

## Related Articles

**In This Knowledge Base:**

- **KB-001:** How to set up a new iOS project in Xcode 26.1
- **KB-005:** How to configure app assets and resources
- **KB-012:** How to support Dark Mode in Xcode 26+
- **KB-018:** How to configure Info.plist for modern apps
- **KB-025:** How to prepare app for App Store submission
- **KB-028:** How to use SF Symbols in app development

**External Resources:**

- [Apple Human Interface Guidelines - App Icons](https://developer.apple.com/design/human-interface-guidelines/app-icons)
- [WWDC25-361: Create icons with Icon Composer](https://developer.apple.com/videos/play/wwdc2025/361/)
- [Apple Design Resources](https://developer.apple.com/design/resources/)

---

## Sources

This article was compiled from the following authoritative sources:

1. **Apple Developer Documentation - Creating your app icon using Icon Composer**
   - URL: https://developer.apple.com/documentation/Xcode/creating-your-app-icon-using-icon-composer
   - Accessed: November 17, 2025

2. **Apple Developer - Icon Composer**
   - URL: https://developer.apple.com/icon-composer/
   - Accessed: November 17, 2025

3. **WWDC25 Session 361 - Create icons with Icon Composer**
   - URL: https://developer.apple.com/videos/play/wwdc2025/361/
   - Accessed: November 17, 2025

4. **Stack Overflow - Icon Composer Integration Issues**
   - URL: https://stackoverflow.com/questions/79686691/how-do-i-add-icon-composer-glass-app-icons-to-my-existing-pre-ios-26-app-and-su
   - Accessed: November 17, 2025

5. **Stack Overflow - Alternate App Icons in Xcode 26**
   - URL: https://stackoverflow.com/questions/79686722/how-do-i-support-alternate-app-icons-in-xcode-26-for-an-app-that-only-supports-i
   - Accessed: November 17, 2025

6. **Use Your Loaf - Adding Icon Composer icons to Xcode**
   - URL: https://useyourloaf.com/blog/adding-icon-composer-icons-to-xcode/
   - Accessed: November 17, 2025

7. **Create with Swift - Crafting Liquid Glass app icons with Icon Composer**
   - URL: https://www.createwithswift.com/crafting-liquid-glass-app-icons-with-icon-composer/
   - Accessed: November 17, 2025

8. **WWDC Notes - Create icons with Icon Composer**
   - URL: https://wwdcnotes.com/documentation/wwdcnotes/wwdc25-361-create-icons-with-icon-composer/
   - Accessed: November 17, 2025

---

**Document Information:**

- **Version:** 1.0
- **Last Reviewed:** November 17, 2025
- **Next Review:** February 17, 2026
- **Maintainer:** Xcode Knowledge Base Team
- **Feedback:** Submit corrections or suggestions via GitHub Issues

---

*This article is part of the Xcode 26.1+ Knowledge Base series. For the complete collection, see the [Knowledge Base Index](index.md).*
