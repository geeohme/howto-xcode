# How to prompt AI to localize strings and prepare for internationalization

**Article ID:** KB-060
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 30-45 minutes

## Overview

Xcode 26.1+ introduces revolutionary AI-powered localization features that transform how developers prepare apps for international audiences. The standout feature is automatic comment generation using an on-device language model that analyzes your code context and writes intelligent comments for translators. Combined with String Catalogs, type-safe Swift symbols, and automatic string discovery, Xcode 26.1+ makes professional-grade internationalization accessible to every iOS developer. This guide shows you how to effectively prompt AI to localize strings, generate contextual translator comments, and prepare your app for multiple languages.

## Prerequisites

- Xcode 26.1 or later installed
- macOS 15.0 or later (macOS 26 Tahoe recommended for full AI features)
- An iOS, iPadOS, watchOS, or macOS app project (new or existing)
- Basic understanding of SwiftUI or UIKit
- Apple Intelligence enabled in System Settings (for on-device AI features)
- Active internet connection (for ChatGPT/Claude assistance in Coding Assistant)
- Basic familiarity with internationalization concepts (locales, translations, string keys)

## Steps

### Step 1: Add a String Catalog to Your Project

String Catalogs are the foundation of modern localization in Xcode 26.1+:

1. **Open your project** in Xcode 26.1 or later
2. In the **Project Navigator** (left sidebar), select your project's main folder or group where you want to store localization files
3. Right-click and choose **New File...** (or press `Cmd + N`)
4. In the template chooser, scroll to the **Resource** section
5. Select **String Catalog** and click **Next**
6. **Name the file** (typically `Localizable.xcstrings` for the default catalog)
7. Choose your **target** (ensure your app target is checked)
8. Click **Create**

**Result:** Xcode creates an empty String Catalog file. After your next build, Xcode will automatically discover all localizable strings in your project and add them to this catalog.

**Note:** New projects created in Xcode 26.1+ automatically include a String Catalog with symbol generation enabled by default.

### Step 2: Enable String Catalog Symbol Generation

Generate type-safe Swift symbols for your localized strings:

1. Select your **project** in the Project Navigator
2. Choose your **app target** from the target list
3. Click the **Build Settings** tab
4. In the search field, type **"String Catalog"**
5. Find the setting **"Generate String Catalog Symbols"**
6. Set it to **"Yes"**
7. Press `Cmd + B` to **build your project**

**What this does:** Xcode generates Swift code that provides autocomplete suggestions and compile-time safety for your localized strings. Instead of using raw string keys, you can access strings through generated symbols.

**Example:**
```swift
// Without symbols (error-prone):
Text(NSLocalizedString("welcome_message", comment: ""))

// With symbols (type-safe, autocomplete):
Text(.localizable.welcomeMessage)
```

### Step 3: Write Localizable Code in Your App

Ensure your strings are discoverable by Xcode's automatic localization system:

**For SwiftUI (automatic localization):**
```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            // SwiftUI automatically localizes text literals
            Text("Welcome to My App")

            Button("Get Started") {
                // Action
            }

            // For dynamic content, use String interpolation
            Text("Hello, \(username)!")
        }
    }
}
```

**For UIKit (explicit localization):**
```swift
import UIKit

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()

        // Use NSLocalizedString for UIKit
        let titleLabel = UILabel()
        titleLabel.text = NSLocalizedString("app_title", comment: "Main app title shown on home screen")

        let button = UIButton()
        button.setTitle(NSLocalizedString("submit_button", comment: "Button to submit the form"), for: .normal)
    }
}
```

**Best practices:**
- Use clear, descriptive string keys (e.g., `welcome_message`, not `str1`)
- Write strings in your base language (typically English)
- Avoid concatenating strings (breaks grammar rules in other languages)
- Use string interpolation for dynamic content

### Step 4: Build Your Project to Discover Strings

Trigger Xcode's automatic string discovery:

1. Press **Cmd + B** to build your project
2. Wait for the build to complete successfully
3. Open your **String Catalog** file (`Localizable.xcstrings`)
4. **Review discovered strings:** You should see all localizable strings from your code automatically listed

**What you'll see:**
- String keys (for UIKit's `NSLocalizedString`)
- String values (the actual text)
- Empty comment fields (we'll populate these with AI in the next step)
- Language columns (initially just your base language)

**Note:** Xcode discovers strings after every build, so new strings are automatically added to the catalog as you develop.

### Step 5: Use AI to Generate Contextual Comments (Primary Method)

This is the revolutionary feature in Xcode 26.1+ - automatic AI-generated comments for translators:

1. **Open your String Catalog** file
2. **Select a string** that needs a comment (click on any row)
3. **Right-click** the string entry
4. Choose **"Generate Comment"** from the context menu
5. **Wait for AI analysis:** Xcode's on-device model analyzes:
   - Where the string appears in your code
   - Surrounding code context and function names
   - UI hierarchy and view structure
   - Usage patterns and variable names
6. **Review the generated comment:** Xcode populates the comment field with contextual information for translators
7. **Edit if needed:** You can refine the AI-generated comment to add specific details

**Example AI-generated comments:**
```
String: "Delete"
AI Comment: "Button label for permanently removing a user's account. Shows in account settings screen."

String: "Save"
AI Comment: "Confirmation button in the document editor. Saves changes to local storage."

String: "Loading..."
AI Comment: "Progress message shown during network data fetch. Appears in the center of the feed screen."
```

**Pro Tip:** Generate comments for all strings in your catalog to provide maximum context for translators.

### Step 6: Enable Automatic Comment Generation for Future Strings

Configure Xcode to automatically generate comments for new strings:

1. Navigate to **Xcode > Settings** (or press `Cmd + ,`)
2. Click the **Editing** tab
3. Scroll to the **Localization** section
4. Check **"Automatically generate string catalog comments"**
5. Close Settings

**Result:** Every new localizable string discovered during builds will automatically receive an AI-generated comment based on its code context.

**Benefits:**
- No manual comment writing required
- Consistent comment quality across your project
- Translators always have context, even for strings you forgot to document
- Saves significant development time on internationalization prep

### Step 7: Manually Add Strings with Custom Comments (Optional)

For strings not yet in code or requiring specific handling:

1. **Open your String Catalog**
2. Click the **"+"** button at the bottom of the string list
3. **Enter the string key** (e.g., `onboarding_title`)
4. **Enter the localized value** in your base language (e.g., "Welcome Aboard!")
5. In the **Comment** field, either:
   - Click **"Generate Comment"** to use AI
   - Or manually type a specific comment: "Title of the first onboarding screen, emphasizing excitement"
6. Press **Enter** to save

**When to manually add strings:**
- Planning localization before implementing UI
- Strings loaded from remote sources (APIs, configuration files)
- Placeholder text for future features
- Strings used dynamically via string formatting

### Step 8: Prompt ChatGPT/Claude for Localization Strategy

Use Xcode's integrated AI Coding Assistant to plan your localization approach:

1. Open the **Coding Assistant** panel (`Cmd + 0` or **Editor > Show Coding Assistant**)
2. Select **ChatGPT** or **Claude** from the model dropdown
3. **Prompt for localization strategy:**

```
I'm preparing my iOS app for internationalization in Xcode 26.1.
My app is a [describe your app type, e.g., "fitness tracking app with workout plans and social features"].
I need to support [list target languages, e.g., "Spanish, French, German, and Japanese"].

Please help me:
1. Identify which UI elements should be localized (text, images, dates, numbers)
2. Recommend best practices for handling pluralization in these languages
3. Suggest how to structure my String Catalog comments for maximum translator clarity
4. Identify potential localization challenges for right-to-left languages (if applicable)
5. Recommend testing strategies to verify localization quality

Context: My app includes [list key features, e.g., "user profiles, workout calendars, achievement notifications, in-app purchases"].
```

**Review the AI response** for specific recommendations tailored to your app.

### Step 9: Prompt AI to Review and Improve String Keys

Ask AI to optimize your string keys for maintainability:

1. **Export your String Catalog keys** (copy all keys from the catalog)
2. In the **Coding Assistant**, prompt:

```
Review these localization string keys from my Xcode String Catalog and suggest improvements:

[Paste your string keys]

For each key:
1. Check if the naming is clear and descriptive
2. Verify consistency in naming conventions (snake_case, camelCase, etc.)
3. Identify any keys that could be consolidated or reused
4. Suggest better names for ambiguous keys
5. Flag keys that might cause confusion for translators

Provide specific recommendations with explanations.
```

**Implement recommendations** by renaming keys in your String Catalog (Xcode will warn about usage in code).

### Step 10: Add Target Languages to Your Project

Configure which languages your app will support:

1. Select your **project** (top level) in the Project Navigator
2. In the **Info** tab, find the **Localizations** section
3. Click the **"+"** button under Localizations
4. **Select a language** from the dropdown (e.g., Spanish, French, Japanese)
5. In the dialog, **check your String Catalog** file (`Localizable.xcstrings`)
6. Click **Finish**
7. **Repeat** for each target language

**Result:** Your String Catalog now shows columns for each language. The base language values remain; translation columns are empty (ready for translators).

**Verification:**
- Open your String Catalog
- Confirm you see columns for each added language
- Each string now has a translation field per language

### Step 11: Prompt AI to Generate Draft Translations

Use AI for initial translation drafts (professional translators should review):

1. **Select strings** from your String Catalog (copy key, value, and comment)
2. In the **Coding Assistant**, prompt:

```
Translate these iOS app strings from English to [target language, e.g., Spanish].
Maintain technical accuracy and cultural appropriateness for [target region, e.g., Latin America].

For each string:
1. Provide the translation
2. Note any cultural considerations or alternatives
3. Flag strings that require special handling (plurals, gender, formal/informal tone)

Format your response as a table: English | Spanish | Notes

String Key | English Text | Context Comment
---------------------------------------------
welcome_message | "Welcome to FitTrack" | "Greeting shown on app launch"
start_workout_button | "Start Workout" | "Button to begin a new workout session"
achievement_unlocked | "Achievement Unlocked!" | "Notification title when user earns a badge"
[Add more strings...]
```

**Important:** AI translations are starting points. Professional human translators should review and refine all translations before release.

### Step 12: Prompt AI for Pluralization and Localization Variants

Handle complex localization scenarios:

1. **Identify strings with counts** (e.g., "1 workout" vs "5 workouts")
2. In your **String Catalog**, right-click the string
3. Choose **"Vary by Plural"**
4. Xcode creates plural variants (zero, one, two, few, many, other)
5. **Prompt AI for plural rules:**

```
I need to localize this iOS string with plural variations for [language]:

Base string: "%lld workouts completed"
Context: Shows count of completed workouts in user profile

For [language, e.g., Polish]:
1. Provide translations for all plural categories (zero, one, few, many, other)
2. Explain the plural rules for this language
3. Provide examples showing when each form is used
4. Format as: Category: Translation (Used for: numbers)

Example for English:
- one: "%lld workout completed" (Used for: 1)
- other: "%lld workouts completed" (Used for: 0, 2-∞)
```

**Apply the translations** to the appropriate plural categories in your String Catalog.

### Step 13: Test Localization with Xcode Schemes

Verify your localization in the simulator:

1. Select your **app scheme** (next to the Run button)
2. Choose **Edit Scheme...** (or press `Cmd + <`)
3. Select **Run** in the left sidebar
4. Click the **Options** tab
5. Under **App Language**, choose a localized language (e.g., Spanish)
6. Click **Close**
7. Press **Cmd + R** to run your app in the selected language

**Verify:**
- All UI text appears in the target language
- Layout accommodates longer/shorter text
- Right-to-left languages display correctly (if applicable)
- Pluralization works with different counts
- Date, time, and number formats match the locale

**Pro Tip:** Create multiple schemes for different languages to quickly switch during testing.

### Step 14: Prompt AI for Localization Testing Checklist

Generate a comprehensive testing plan:

```
Create a localization testing checklist for my iOS app in Xcode 26.1.

My app supports: [list languages]
Key features: [list main features]
App category: [type of app]

Include:
1. Text display issues (truncation, overlap, incorrect fonts)
2. Layout problems (elements outside safe areas, misaligned views)
3. Functional testing (buttons, navigation work in all languages)
4. Cultural appropriateness (colors, icons, imagery)
5. Format verification (dates, numbers, currencies, measurements)
6. Right-to-left language specific tests (if applicable)
7. Accessibility in localized versions (VoiceOver support)
8. Dynamic type and text sizing across languages

Format as a checklist with test cases for each category.
```

**Use the checklist** to systematically test each localized version of your app.

### Step 15: Export String Catalog for Professional Translation

Prepare your strings for external translators:

1. Select your **String Catalog** in the Project Navigator
2. Go to **Editor > Export For Localization...** (or press `Cmd + Shift + E`)
3. Choose an **export location** (creates an `.xcloc` folder)
4. **Select target languages** to export
5. Click **Export**

**What you get:** An `.xcloc` package containing:
- All strings with keys and comments
- Context information for translators
- Metadata about your project
- Standard format compatible with translation tools

**Send to translators** with instructions to:
- Read the AI-generated comments for context
- Maintain placeholder syntax (e.g., `%@`, `%lld`)
- Respect string formatting and structure
- Note any unclear contexts for follow-up questions

### Step 16: Import Translated Strings Back to Xcode

After receiving translations from professional translators:

1. Go to **Editor > Import Localizations...**
2. **Select the `.xcloc` folder** received from translators
3. Xcode **validates** the translations
4. **Review the import summary** (shows added/updated strings)
5. Click **Import**
6. **Build your project** (`Cmd + B`)

**Verification:**
- Open your String Catalog
- Confirm translations appear in the target language columns
- Check that no keys were accidentally modified
- Run the app in each language to verify integration

### Step 17: Prompt AI to Identify Missing or Incomplete Localizations

Audit your localization coverage:

```
Analyze my String Catalog for localization completeness.

I have [X] strings in my catalog targeting [Y] languages.
Some strings may be missing translations or have incomplete plural forms.

Please help me:
1. Identify patterns indicating missing localizations
2. Suggest how to prioritize which strings must be translated immediately vs later
3. Recommend strategies for handling untranslated strings at runtime
4. Create a formula to calculate localization completion percentage
5. Suggest tools or scripts to automate localization coverage reporting

Context: My app is [stage, e.g., "in beta testing with 1000 users"] and launching in [timeline].
```

**Act on recommendations** to complete your localization before release.

## Expected Results

After following these steps, you should have:

- A fully configured String Catalog with automatic string discovery
- Type-safe Swift symbols for all localized strings (autocomplete support in Xcode)
- AI-generated contextual comments for every string, helping translators understand usage
- Multiple target languages configured in your project
- Draft translations for initial testing (if using AI assistance)
- Proper pluralization handling for languages with complex plural rules
- A systematic testing process for each localized version
- Export/import workflow established for professional translation services
- Comprehensive localization coverage across your entire app

**Quality indicators:**
- **100% comment coverage:** Every string has a descriptive comment
- **Consistent naming:** String keys follow a clear convention
- **No hardcoded strings:** All user-facing text uses localization APIs
- **Plural variants:** Strings with counts have proper plural handling
- **Layout flexibility:** UI accommodates text length variations (150-200% expansion for some languages)
- **Format localization:** Dates, numbers, and currencies display according to locale conventions

**Typical completion metrics:**
- Initial String Catalog setup: **5-10 minutes**
- AI comment generation for 100 strings: **10-15 minutes**
- Adding 3-5 languages to project: **5 minutes**
- Testing each language: **15-30 minutes per language**
- Professional translation turnaround: **1-2 weeks** (external)

## Troubleshooting

### Issue 1: Strings Not Appearing in String Catalog

**Problem:** After building, some strings don't appear in the String Catalog.

**Solutions:**
- **SwiftUI:** Verify text literals are directly in `Text()` views, not stored in variables first
  ```swift
  // Won't be discovered:
  let message = "Hello"
  Text(message)

  // Will be discovered:
  Text("Hello")
  ```
- **UIKit:** Ensure you're using `NSLocalizedString()`, not plain strings
- **Check build success:** Localization discovery only works after successful builds
- **Clean build folder:** Press `Cmd + Shift + K`, then rebuild (`Cmd + B`)
- **Verify target membership:** Ensure Swift files are included in your app target
- **Update Xcode:** Some discovery features improved in Xcode 26.1.1

### Issue 2: AI Comment Generation Not Available

**Problem:** Right-clicking a string doesn't show "Generate Comment" option.

**Solutions:**
- **Verify Xcode version:** Ensure you're running Xcode 26.1 or later (check **Xcode > About Xcode**)
- **Check macOS version:** AI features require macOS 15.0+ (macOS 26 Tahoe recommended)
- **Enable Apple Intelligence:**
  - Go to **System Settings > Apple Intelligence & Siri**
  - Ensure Apple Intelligence is enabled
  - Verify your Mac supports Apple Intelligence (M1 chip or later)
- **Restart Xcode:** Quit and relaunch Xcode completely
- **Check String Catalog format:** Ensure you're using the new `.xcstrings` format, not legacy `.strings` files
- **Network connection:** Some AI features require internet connectivity for first-time setup

### Issue 3: Generated Symbols Not Working

**Problem:** Autocomplete doesn't show localized string symbols, or compiler errors when using symbol syntax.

**Solutions:**
- **Enable symbol generation:**
  1. Select your target → **Build Settings**
  2. Search for "String Catalog"
  3. Set **"Generate String Catalog Symbols"** to **Yes**
- **Rebuild project:** Press `Cmd + Shift + K` to clean, then `Cmd + B` to rebuild
- **Check project type:** Symbol generation works with Swift projects (not Objective-C only)
- **Verify Xcode version:** Feature requires Xcode 26.0+
- **Check generated code:** Look for `Localizable.swift` in your project's derived data
- **Import requirement:** Ensure you `import Foundation` or `import SwiftUI` in files using symbols
- **Clean derived data:**
  - Go to **Xcode > Settings > Locations**
  - Click arrow next to Derived Data path
  - Delete your project's derived data folder
  - Rebuild

### Issue 4: Translations Not Appearing in App

**Problem:** App runs but still shows English text despite adding translations.

**Solutions:**
- **Verify translations are saved:** Open String Catalog and confirm translation values are present
- **Check simulator language:**
  - Edit your scheme (**Cmd + <**)
  - Verify **App Language** is set to the test language
  - Close and rerun the app
- **Simulator settings:** In the Simulator, go to **Settings > General > Language & Region** and add the language
- **Clean and rebuild:** Press `Cmd + Shift + K`, then `Cmd + B`
- **Restart simulator:** Quit and relaunch the iOS Simulator
- **Check pluralization:** For plural strings, ensure you're using the correct format specifiers (`%lld` for integers)
- **Verify target membership:** Ensure String Catalog is included in your app target (select file → File Inspector → Target Membership)

### Issue 5: AI-Generated Comments Are Generic or Unhelpful

**Problem:** Generated comments don't provide useful context for translators.

**Solutions:**
- **Improve code context:** Use descriptive variable and function names near the string
  ```swift
  // Generic context (poor AI comment):
  Text("Submit")

  // Rich context (better AI comment):
  Button("Submit") {
      submitUserProfileForm()
  }
  .accessibilityLabel("Submit profile changes to server")
  ```
- **Manually enhance comments:** Edit AI-generated comments to add specific details:
  - What action the string triggers
  - Where in the app it appears
  - Any character limits or formatting constraints
  - Tone guidelines (formal, casual, playful)
- **Use descriptive string keys:** Better keys help AI understand context
  ```swift
  // Generic key:
  NSLocalizedString("btn1", comment: "")

  // Descriptive key (helps AI):
  NSLocalizedString("profile_submit_button", comment: "")
  ```
- **Add code comments:** Place Swift comments near localization calls to give AI more context
- **Manual comment for critical strings:** For important UX strings, write comments yourself

### Issue 6: Layout Breaks in Certain Languages

**Problem:** UI looks good in English but text overlaps, truncates, or breaks layout in other languages.

**Solutions:**
- **Use flexible layouts:**
  - SwiftUI: Prefer `VStack`, `HStack` with flexible spacing
  - UIKit: Use Auto Layout constraints, not fixed frames
- **Account for text expansion:** Some languages (German, Spanish) can be 30-50% longer
  ```swift
  // SwiftUI - flexible:
  Text(localized)
      .lineLimit(nil) // Allow multiple lines
      .fixedSize(horizontal: false, vertical: true)

  // UIKit - flexible:
  label.numberOfLines = 0
  label.lineBreakMode = .byWordWrapping
  ```
- **Test with longest language:** German and Finnish typically have longest strings
- **Adjust minimum font size:** Allow text to scale down if needed
- **Use dynamic type:** Respect user's text size preferences
- **Right-to-left support:** For Arabic, Hebrew:
  - Use leading/trailing instead of left/right
  - Test with **Edit Scheme > App Language > Right-to-Left Pseudolanguage**

## Additional Tips

### Best Practices for Prompting AI About Localization

1. **Be specific about target markets:**
   ```
   "Translate for Mexican Spanish (informal tone, targeting young adults)"
   vs
   "Translate for Castilian Spanish (formal business tone)"
   ```

2. **Provide cultural context:**
   ```
   "This fitness app uses motivational language. Adapt idioms like 'crush your goals'
   to culturally equivalent expressions in [language]."
   ```

3. **Request alternatives:**
   ```
   "Provide 3 translation options for this marketing tagline,
   each with different formality levels and cultural associations."
   ```

4. **Ask for explanation:**
   ```
   "Translate this string and explain why you chose this specific phrasing
   over literal translation, considering cultural norms in [country]."
   ```

5. **Specify constraints:**
   ```
   "Translate this button label. Keep it under 15 characters including spaces.
   Prioritize clarity over literal accuracy."
   ```

### Leveraging ChatGPT vs Claude for Localization

**Use ChatGPT for:**
- Quick translation drafts during development
- Simple string translations in common languages
- Rapid iteration on translation options
- General localization advice and best practices
- Generating translation testing checklists

**Use Claude for:**
- Complex cultural adaptation requiring nuanced understanding
- Analyzing full String Catalogs for consistency
- Detailed explanations of language-specific challenges
- Comprehensive localization strategies for multiple languages simultaneously
- In-depth reviews of pluralization logic across languages

### String Catalog Organization Tips

1. **Use namespaces in keys:**
   ```
   onboarding.welcome_title
   onboarding.welcome_subtitle
   profile.edit_button
   profile.save_button
   ```

2. **Group related strings:** Keep UI sections together in your String Catalog

3. **Add metadata in comments:**
   ```
   "Profile screen: Button to save user changes.
   Character limit: 12 max.
   Tone: Encouraging, action-oriented.
   Appears: Bottom-right of edit view."
   ```

4. **Create separate catalogs:** For different modules or features (optional)
   - `Onboarding.xcstrings`
   - `Profile.xcstrings`
   - `Store.xcstrings`

### Internationalization Beyond Strings

AI can also help with:

1. **Date and time formatting:**
   ```
   "Show me how to format dates according to locale in Swift,
   including short, medium, and long date styles for [language]."
   ```

2. **Number and currency formatting:**
   ```
   "Explain how to display currency values correctly for users in Japan,
   including proper yen symbol placement and decimal handling."
   ```

3. **Image localization:**
   ```
   "What visual elements should I localize or avoid when targeting [culture]?
   Consider colors, symbols, hand gestures, and icons."
   ```

4. **Layout direction:**
   ```
   "Review my SwiftUI views for right-to-left language support.
   What changes do I need to make for proper Arabic localization?"
   ```

### Continuous Localization Workflow

Establish an ongoing process as your app evolves:

1. **After each feature:**
   - Build project to discover new strings
   - Generate AI comments for new entries
   - Export `.xcloc` for translation

2. **Before each release:**
   - Audit String Catalog completeness
   - Test all supported languages
   - Verify new strings are translated

3. **Monitor user feedback:**
   - Use AI to analyze reviews mentioning localization issues
   - Prompt: "Analyze these user reviews and identify localization problems or mistranslations"

4. **Version control:**
   - Commit String Catalog changes separately
   - Document what changed in commit messages
   - Track translation progress in pull requests

### Accessibility in Localized Apps

Don't forget accessibility when localizing:

1. **Prompt AI for accessibility labels:**
   ```
   "Generate VoiceOver accessibility labels for these UI elements in [language].
   Ensure labels are clear, concise, and follow [language] accessibility guidelines."
   ```

2. **Test VoiceOver in each language:**
   - Enable VoiceOver in Simulator
   - Navigate through your app
   - Verify screen reader output makes sense

3. **Dynamic Type support:**
   - Ensure text scales properly in all languages
   - Test at largest accessibility text sizes
   - Verify layout doesn't break

### Cost-Effective Translation Strategy

For indie developers and small teams:

1. **AI for first draft:** Use ChatGPT/Claude for initial translations (free or low-cost)
2. **Community review:** Engage beta testers who are native speakers
3. **Professional for critical strings:** Pay for translation of key UX strings (onboarding, purchase flow, legal)
4. **Incremental approach:** Launch with 2-3 languages, expand based on user demand
5. **Crowdsource translations:** Platforms like Crowdin or Lokalise offer community translation

### Testing for Quality Assurance

Comprehensive testing strategies:

1. **Pseudolanguage testing:**
   - Use **Edit Scheme > Options > App Language > Double-Length Pseudolanguage**
   - Tests layout with 2x longer strings
   - Reveals UI constraints that will break in German, Finnish, etc.

2. **Automated testing:**
   - Write UI tests that run in multiple languages
   - Assert that key UI elements are visible and correctly positioned
   - Verify translations loaded correctly

3. **Screenshot testing:**
   - Use `XCUIApplication().screenshot()` in tests
   - Generate screenshots for all supported languages
   - Manually review for layout issues

4. **Beta testing:**
   - Use TestFlight to distribute localized builds
   - Recruit native speakers for each language
   - Collect feedback specifically about translation quality and cultural appropriateness

## Related Articles

- KB-021: How to add UI elements to a SwiftUI view (understanding what needs localization)
- KB-022: How to use the @State property wrapper (managing localized dynamic content)
- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT (for AI translation prompts)
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings (for advanced localization tasks)
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant (choosing the right AI)
- KB-050: How to compare responses between ChatGPT and Claude for the same coding task (comparing translation approaches)

## Sources

- Apple Developer: Code-along: Explore localization with Xcode - WWDC25 - https://developer.apple.com/videos/play/wwdc2025/225/ (Accessed: November 17, 2025)
- DEV Community: Apple Killed Manual Localization: Xcode 26's AI Writes Comments & Generates Code For You - https://dev.to/arshtechpro/wwdc-2025-explore-localization-with-xcode-26-n8n (Accessed: November 17, 2025)
- Medium: SwiftUI + Xcode 26: Easy Localization - https://medium.com/@poojanegi43/swiftui-xcode-26-easy-localization-c98ed546e04e (Accessed: November 17, 2025)
- 200OK Solutions: What's New in Xcode 26: Smarter, Faster, More Powerful — WWDC 2025 Highlights - https://200oksolutions.com/blog/whats-new-in-xcode-26-smarter-faster-more-powerful-wwdc-2025-highlights/ (Accessed: November 17, 2025)
- 9to5Mac: Apple releases Xcode 26.1.1 with coding intelligence improvements - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- String Catalog: Accurate Translation Comments in String Catalogs with Xcode 26 - https://stringcatalog.com/articles/accurate-translation-comments-string-catalogs-xcode-26 (Accessed: November 17, 2025)
- Medium: AI-Driven Localization for Xcode Projects - https://medium.com/@razanau/ai-driven-localization-for-xcode-projects-da36bc6bb93c (Accessed: November 17, 2025)
- Daniel Saidi Blog: Using AI and Cursor to localize Xcode String Catalogs - https://danielsaidi.com/blog/2025/06/08/using-ai-and-cursor-to-localize-xcode-string-catalogs (Accessed: November 17, 2025)
- SD Times: Development tool updates from WWDC: Foundation Models framework, Xcode 26, Swift 6.2, and more - https://sdtimes.com/softwaredev/development-tool-updates-from-wwdc-foundation-models-framework-xcode-26-swift-6-2-and-more/ (Accessed: November 17, 2025)
- WWDC Notes: Code-along: Explore localization with Xcode - https://wwdcnotes.com/documentation/wwdcnotes/wwdc25-225-codealong-explore-localization-with-xcode/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
