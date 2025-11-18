# How to enable Compilation Caching for faster build times

**Article ID:** KB-094
**Difficulty:** Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 15-30 minutes

## Overview

Xcode 26 introduces compilation caching, an opt-in feature that significantly accelerates iterative build and test cycles by caching compilation results for both Swift and C-family languages. Instead of recompiling unchanged code, Xcode computes a hermetic fingerprint for each build action and retrieves cached results from content-addressable storage. This feature is particularly beneficial when switching between git branches, performing clean builds, or working in continuous integration environments, with real-world benchmarks showing build time reductions ranging from 19% to 77% depending on project complexity.

## Prerequisites

- Xcode 26.1 or later installed on your Mac
- Basic understanding of Xcode build settings and configurations
- An iOS, macOS, or multi-platform project
- Familiarity with command-line tools (optional, for advanced configuration)

## Steps

### Method 1: Enable via Xcode Build Settings (GUI)

1. **Open your project in Xcode 26.1+**
   - Launch Xcode and open your project file (.xcodeproj or .xcworkspace)

2. **Access Build Settings**
   - Select your project in the Project Navigator (left sidebar)
   - Select your target in the project editor
   - Click on the "Build Settings" tab
   - Ensure "All" and "Combined" are selected at the top

3. **Locate the Compilation Cache Setting**
   - In the search field at the top right, type: `COMPILATION_CACHE_ENABLE_CACHING`
   - Alternatively, scroll through build settings to find the compilation cache section

4. **Enable Compilation Caching**
   - Find the setting `COMPILATION_CACHE_ENABLE_CACHING`
   - Change the value from `NO` (default) to `YES`
   - This enables LLVM CAS (Content-Addressable Storage) and Plugin format support

5. **Apply to Multiple Targets (if needed)**
   - Repeat steps 2-4 for each target in your project
   - For consistency, consider enabling this at the project level rather than per-target

6. **Save and Rebuild**
   - Press `Command + B` to build your project
   - The first build will populate the cache (no time savings yet)
   - Subsequent builds will benefit from cached compilation results

### Method 2: Enable via .xcconfig Configuration File

1. **Create or open your .xcconfig file**
   - If you don't have one, create a new file: File > New > File > Configuration Settings File
   - Name it appropriately (e.g., `BuildOptimizations.xcconfig`)

2. **Add the compilation cache setting**
   ```
   // Enable Xcode 26 Compilation Caching
   COMPILATION_CACHE_ENABLE_CACHING = YES
   ```

3. **Link the .xcconfig file to your project**
   - Select your project in the Project Navigator
   - Go to the Info tab
   - Under "Configurations", expand your configuration (Debug/Release)
   - Select your .xcconfig file for each target

4. **Rebuild your project**
   - Clean the build folder: `Command + Shift + K`
   - Build the project: `Command + B`

### Method 3: Enable via Command Line (xcodebuild)

1. **Open Terminal**
   - Navigate to your project directory

2. **Build with compilation caching enabled**
   ```bash
   xcodebuild -scheme YourSchemeName \
              -configuration Debug \
              COMPILATION_CACHE_ENABLE_CACHING=YES \
              clean build
   ```

3. **Verify the setting is active**
   ```bash
   xcodebuild -scheme YourSchemeName \
              -configuration Debug \
              -showBuildSettings | grep COMPILATION_CACHE
   ```

4. **For CI/CD environments**
   - Add the `COMPILATION_CACHE_ENABLE_CACHING=YES` parameter to all xcodebuild commands
   - Ensure the DerivedData directory is cached between builds for maximum benefit

### Method 4: Enable Globally via User Defaults (Advanced)

1. **Set user default for all projects**
   ```bash
   defaults write com.apple.dt.Xcode COMPILATION_CACHE_ENABLE_CACHING -bool YES
   ```

2. **Verify the setting**
   ```bash
   defaults read com.apple.dt.Xcode COMPILATION_CACHE_ENABLE_CACHING
   ```
   - Should output: `1` (enabled) or `0` (disabled)

3. **Restart Xcode**
   - Close and reopen Xcode for the setting to take effect

4. **To disable globally later**
   ```bash
   defaults delete com.apple.dt.Xcode COMPILATION_CACHE_ENABLE_CACHING
   ```

## Expected Results

### Performance Improvements

Once compilation caching is enabled and the cache is populated, you should observe:

- **First Clean Build:** No performance improvement (cache is being populated)
- **Subsequent Builds:** 19-55% reduction in build times for typical projects
- **Branch Switching:** Significant time savings when switching between git branches with similar code
- **CI/CD Environments:** 30-50% faster build times when DerivedData cache is preserved

### Real-World Benchmarks (from Xcode 26.0.1 testing)

| Project Type | Local Cache Improvement | Remote Cache Improvement |
|-------------|------------------------|-------------------------|
| Wikipedia iOS | 24% faster | 18% faster |
| Pocket Casts | 36% faster | 20% faster |
| Tuist CLI | 77% faster | 53% faster |
| Mastodon (Tuist) | 55% faster | 69% faster |

### Cache Location

- **Local cache stored at:** `~/Library/Developer/Xcode/DerivedData/`
- **Compilation cache uses:** Content-addressable storage with hermetic fingerprints
- **Cache structure:** Each artifact identified by unique digest of contents

### Verification

To verify compilation caching is working:

1. **Build your project twice consecutively**
   - First build: Normal or slightly slower (cache population)
   - Second build: Should be noticeably faster

2. **Check build logs**
   - In Xcode, show the Report Navigator (`Command + 9`)
   - Select your build
   - Look for cache-related messages in the build log

3. **Monitor DerivedData size**
   ```bash
   du -sh ~/Library/Developer/Xcode/DerivedData/
   ```
   - The cache will grow as more artifacts are stored

## Troubleshooting

### Issue 1: No Performance Improvement After Enabling

**Symptoms:**
- Build times remain the same after enabling compilation caching
- Cache appears not to be working

**Solutions:**
1. **Verify the setting is actually enabled**
   ```bash
   xcodebuild -scheme YourSchemeName -showBuildSettings | grep COMPILATION_CACHE
   ```
   - Should show: `COMPILATION_CACHE_ENABLE_CACHING = YES`

2. **Clean DerivedData and rebuild**
   ```bash
   rm -rf ~/Library/Developer/Xcode/DerivedData/
   ```
   - Then rebuild your project twice to test cache effectiveness

3. **Check for unsupported task types**
   - As of Xcode 26.1, these tasks are NOT cacheable:
     - Swift Package Manager (SPM) dependencies
     - CompileStoryboard
     - CompileXIB
     - CompileAssetCatalogVariant
     - PhaseScriptExecution (build scripts)
     - DataModelCompile (Core Data models)
     - CopyPNGFile
     - GenerateDSYMFile
     - Ld (linking tasks)
   - If your build consists primarily of these tasks, benefits will be minimal

4. **Ensure you're testing correctly**
   - The first build after enabling won't show improvements
   - Test with a clean build followed by another clean build: `Command + Shift + K`, then `Command + B` (twice)

### Issue 2: Cache Not Shared Between Machines

**Symptoms:**
- Local builds are faster, but CI/CD or team members don't benefit
- Cache doesn't work on different machines

**Solutions:**
1. **Understand current limitations**
   - Many artifacts in Xcode 26.1 still rely on local absolute paths
   - Bridging headers (xxx-Bridging-Header.h) use absolute paths
   - This prevents cache reuse across different machines

2. **Workaround for teams:**
   - Ensure all developers use consistent project directory structures
   - Consider using a shared network DerivedData location (not recommended for performance)
   - Wait for future Xcode updates that improve cross-machine caching

3. **CI/CD optimization:**
   - Cache the entire DerivedData directory between CI runs on the same machine
   - Use consistent build agents with cached DerivedData
   - Example for GitHub Actions:
     ```yaml
     - name: Cache DerivedData
       uses: actions/cache@v3
       with:
         path: ~/Library/Developer/Xcode/DerivedData
         key: ${{ runner.os }}-deriveddata-${{ hashFiles('**/*.xcodeproj') }}
     ```

### Issue 3: Excessive Disk Space Usage

**Symptoms:**
- DerivedData folder growing to tens of gigabytes
- Running out of disk space
- Slow Xcode performance

**Solutions:**
1. **Monitor cache size regularly**
   ```bash
   du -sh ~/Library/Developer/Xcode/DerivedData/
   ```

2. **Clean old project caches**
   - In Xcode: Xcode > Settings > Locations
   - Click the arrow next to DerivedData path
   - Manually delete old project folders you no longer need

3. **Automated cleanup script**
   ```bash
   # Delete DerivedData older than 30 days
   find ~/Library/Developer/Xcode/DerivedData -type d -mtime +30 -maxdepth 1 -exec rm -rf {} \;
   ```

4. **Selective cache cleaning**
   - Only clean specific project caches:
     ```bash
     rm -rf ~/Library/Developer/Xcode/DerivedData/YourProjectName-*
     ```

5. **Keep ModuleCache folder**
   - Don't delete the ModuleCache.noindex folder itself
   - Only remove its contents if needed:
     ```bash
     rm -rf ~/Library/Developer/Xcode/DerivedData/ModuleCache.noindex/*
     ```

## Additional Tips

### Combine with Other Build Optimizations

For maximum build performance, enable compilation caching alongside these optimizations:

1. **Use Incremental Compilation for Debug Builds**
   - Build Settings > Swift Compiler - Code Generation
   - Set "Compilation Mode" to "Incremental" for Debug configuration
   - Set to "Whole Module" for Release configuration

2. **Optimize Debug Information Level**
   - Build Settings > Debug Information Format
   - Use "DWARF" for Debug builds (not "DWARF with dSYM")
   - Only use "DWARF with dSYM" for Release builds

3. **Enable Parallel Build Execution**
   - Xcode > Settings > Locations > Advanced
   - Set Build Location to "Relative to Derived Data"
   - Xcode > Settings > Behaviors > Build
   - Enable "Run parallel targets"

4. **Index While Building**
   - Build Settings > Index-While-Building Functionality
   - Set to "Yes, including Locals" for better autocomplete without sacrificing build speed

5. **Modularize Your Codebase**
   - Split large monolithic targets into frameworks or Swift packages
   - Xcode only rebuilds modified modules with compilation caching
   - Consider using Swift Package Manager for internal modularization

### Best Practices for Compilation Caching

1. **Clean builds are now faster**
   - With compilation caching, the penalty for clean builds is reduced
   - Don't be afraid to clean build (`Command + Shift + K`) when debugging issues

2. **Git workflow optimization**
   - The cache works extremely well when switching between similar branches
   - Common code between branches remains cached

3. **Team coordination**
   - Have your entire team enable compilation caching for consistency
   - Document the setting in your project's README or setup documentation

4. **Monitor and measure**
   - Use Xcode's build timeline to see actual improvements
   - Product > Perform Action > Build With Timing Summary
   - Track build times before and after enabling caching

5. **Keep Xcode updated**
   - Apple continues to improve compilation caching in point releases
   - Update to the latest Xcode 26.x version for best results
   - Check release notes for caching improvements

### Performance Monitoring

Create a script to benchmark your builds:

```bash
#!/bin/bash
# build-benchmark.sh

SCHEME="YourSchemeName"
ITERATIONS=3

echo "Building $SCHEME $ITERATIONS times..."

for i in $(seq 1 $ITERATIONS); do
    echo "Build $i of $ITERATIONS"

    # Clean build
    xcodebuild -scheme "$SCHEME" clean > /dev/null 2>&1

    # Timed build
    START=$(date +%s)
    xcodebuild -scheme "$SCHEME" build > /dev/null 2>&1
    END=$(date +%s)

    DURATION=$((END - START))
    echo "Build $i took ${DURATION} seconds"
done
```

Usage:
```bash
chmod +x build-benchmark.sh
./build-benchmark.sh
```

## Related Articles

- KB-028: How to debug common build errors
- KB-056: How to use AI to optimize slow-performing code
- KB-005: How to configure Xcode preferences for optimal development experience
- KB-069: How to reverse engineer and customize coding intelligence prompts

## Sources

This article is based on information from the following sources:

1. **Apple Developer Documentation - Xcode 26 Release Notes**
   - URL: https://developer.apple.com/documentation/xcode-release-notes/xcode-26-release-notes
   - Accessed: November 17, 2025
   - Official documentation on Xcode 26's new compilation caching feature

2. **Bitrise Blog - Accelerate iOS Builds with Xcode 26 Compilation Caching**
   - URL: https://bitrise.io/blog/post/accelerate-ios-builds-with-xcode-26-compilation-caching-on-bitrise
   - Accessed: November 17, 2025
   - Real-world benchmarks showing 19-55% build time improvements

3. **Tuist Blog - Speed up your builds with the remote Tuist cache for Xcode**
   - URL: https://tuist.dev/blog/2025/10/22/xcode-cache
   - Published: October 22, 2025
   - Accessed: November 17, 2025
   - Detailed benchmarks on Wikipedia, Pocket Casts, and Tuist CLI projects

4. **Bitrise Documentation - Xcode Compilation Cache FAQ**
   - URL: https://docs.bitrise.io/en/bitrise-build-cache/build-cache-for-xcode/xcode-compilation-cache-faq.html
   - Accessed: November 17, 2025
   - Technical details on cache limitations and unsupported task types

5. **Apple Developer Documentation - Improving the speed of incremental builds**
   - URL: https://developer.apple.com/documentation/xcode/improving-the-speed-of-incremental-builds
   - Accessed: November 17, 2025
   - Official guidance on build optimization techniques

6. **Swift Forums - About Swift shared cache across machines**
   - URL: https://forums.swift.org/t/about-swift-shared-cache-across-machines/81850
   - Accessed: November 17, 2025
   - Community discussion on cross-machine caching limitations

7. **Google Search Queries Used:**
   - "Xcode 26 compilation caching build performance 2025"
   - "Xcode 26.1 build settings compilation cache faster builds"
   - "Xcode 26 release notes build optimization caching features"
   - "COMPILATION_CACHE_ENABLE_CACHING Xcode 26 build settings configuration"
   - "Xcode build optimization techniques incremental builds whole module optimization"

---

**Note:** Compilation caching in Xcode 26.1 is still an evolving feature. Apple continues to expand the types of tasks that can be cached and improve cross-machine cache sharing. Check the latest Xcode release notes for updates on caching capabilities.
