# How to ask AI to check your app against App Store review guidelines

**Article ID:** KB-097
**Difficulty:** Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 45-60 minutes

## Overview

With Apple rejecting approximately 24.8% of app submissions, ensuring compliance with App Store Review Guidelines before submission is critical. Xcode 26.1+'s integrated AI Coding Assistant (ChatGPT, Claude Sonnet 4, or other models) can systematically review your app's code, configuration files, and assets against the five main guideline categories: Safety, Performance, Business, Design, and Legal. This advanced workflow helps you identify potential violations, privacy concerns, and compliance gaps before submitting to App Store review, significantly increasing your approval chances.

## Prerequisites

- Xcode 26.1 or later installed with Coding Assistant configured
- A complete or near-complete iOS app project ready for submission
- AI model access configured (see KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT)
- Understanding of switching between AI models (see KB-046: How to switch between ChatGPT and Claude Sonnet 4)
- Familiarity with your app's data collection practices and third-party dependencies
- Access to your app's Info.plist, privacy manifest, and entitlements files
- Basic understanding of App Store Review Guidelines (available at https://developer.apple.com/app-store/review/guidelines/)

## Steps

### Step 1: Prepare Your Project for Compliance Review

Before engaging AI for compliance checking, gather essential project documentation:

1. Open your Xcode project
2. Locate and note the paths to these critical files:
   - `Info.plist`
   - `PrivacyInfo.xcprivacy` (Privacy Manifest)
   - App entitlements file (`.entitlements`)
   - Privacy policy URL (if applicable)
   - Third-party SDK list and their purposes
3. Document your app's core functionality, monetization model, and target audience
4. Identify any sensitive features (location tracking, camera access, health data, payments, etc.)

### Step 2: Open the Coding Assistant and Select Your AI Model

Press **Command+0** (⌘+0) to open the Coding Assistant panel, then start a new conversation.

**Model Selection Recommendation:**
- **Use Claude Sonnet 4** for comprehensive compliance analysis due to its superior contextual understanding and ability to analyze large code sections
- **Use ChatGPT GPT-5 (Reasoning)** for guideline interpretation and edge case analysis
- Consider running the review with both models to compare findings

Select your preferred model from the dropdown menu at the top of the Coding Assistant panel.

### Step 3: Conduct Safety Guidelines Compliance Check (Section 1)

The Safety section covers objectionable content, user-generated content, physical harm, and children's privacy. Use these targeted prompts:

**Prompt 1 - Objectionable Content (Guideline 1.1):**
```
Review my app for compliance with App Store Guideline 1.1 (Objectionable Content).
Analyze @ContentView.swift and all view files for:
- Potentially offensive, defamatory, or discriminatory content
- Violence or graphic content without appropriate age ratings
- Realistic portrayals of weapons or violence
- Content that encourages illegal activity or excessive consumption of substances

Provide a compliance report with specific code locations if issues found.
```

**Prompt 2 - User-Generated Content (Guideline 1.2):**
```
Check my app's compliance with Guideline 1.2 regarding user-generated content.
Analyze @[YourContentModerationFile].swift and related networking code for:
- Content moderation mechanisms for user-generated content
- Reporting and blocking functionality for inappropriate content
- Age-gating mechanisms if the app allows content creation
- Compliance with Guideline 1.2.1(a) for creator apps regarding age-restricted content

List any missing safety features required for App Store approval.
```

**Prompt 3 - Kids Category (Guideline 1.3):**
```
If my app targets children or is categorized in the Kids section, review compliance with:
- Guideline 1.3: No links out of the app, purchasing opportunities, or distracting elements
- COPPA compliance requirements
- Restrictions on behavioral advertising

Analyze @Info.plist for age rating and @[YourMainViews].swift for prohibited elements.
```

### Step 4: Verify Performance Guidelines Compliance (Section 2)

Performance guidelines ensure your app is complete, stable, and functions as advertised.

**Prompt 4 - App Completeness (Guideline 2.1):**
```
Evaluate my app's completeness per Guideline 2.1:
- Check for placeholder content, "Lorem ipsum" text, or empty states
- Verify all features mentioned in App Store description are implemented
- Search for debug code, test credentials, or development-only features
- Identify unfinished UI screens or broken navigation flows

Search across all Swift files and storyboards. Report any completeness issues.
```

**Prompt 5 - Accurate Metadata (Guideline 2.3):**
```
Review compliance with Guideline 2.3 (Accurate Metadata):
- Analyze @Info.plist for accurate app name, version, and descriptions
- Check that screenshots match actual app functionality
- Verify bundle identifier follows proper naming conventions
- Ensure privacy-sensitive permission descriptions (NSCameraUsageDescription,
  NSLocationWhenInUseUsageDescription, etc.) are clear and specific

Report any metadata accuracy concerns.
```

**Prompt 6 - Hardware Compatibility (Guideline 2.4.1):**
```
Check hardware and software compatibility per Guideline 2.4.1:
- Review minimum iOS version requirements in @Info.plist
- Verify device capability requirements (UIRequiredDeviceCapabilities)
- Ensure the app gracefully handles missing hardware (camera, GPS, etc.)
- Check for proper error messages when features aren't available

Identify compatibility issues that could cause rejection.
```

### Step 5: Analyze Business Guidelines Compliance (Section 3)

Business guidelines govern payments, subscriptions, and monetization practices—a common rejection area.

**Prompt 7 - In-App Purchase Requirements (Guideline 3.1.1):**
```
Critical compliance check for Guideline 3.1.1 (In-App Purchase):
Analyze all payment and purchase-related code (@[PaymentManager].swift, @StoreKit files):

- Verify digital goods/services use Apple's In-App Purchase (StoreKit)
- Identify any prohibited external payment links or buttons
- Check for compliance with the "reader" app exception (if applicable)
- Ensure subscription terms and pricing are clearly disclosed before purchase
- Verify auto-renewable subscriptions have proper terms disclosure

Flag ANY external payment mechanisms for digital content as this causes immediate rejection.
```

**Prompt 8 - Subscription Compliance (Guideline 3.1.2):**
```
For subscription-based features, verify Guideline 3.1.2 compliance:
- Check that subscriptions use StoreKit 2 or StoreKit 1
- Verify subscription management link is provided
- Ensure free trial terms are clearly displayed
- Confirm automatic renewal terms are disclosed
- Check for required subscription cancellation instructions

Review @[SubscriptionManager].swift and all subscription UI screens.
```

**Prompt 9 - Other Business Practices (Guideline 3.2):**
```
Review other business practices per Guideline 3.2:
- Check if app contains loan functionality - verify APR doesn't exceed 36% (Guideline 3.2.2(ix))
- Ensure cryptocurrency apps follow guidelines (if applicable)
- Verify charitable donations use approved methods
- Check that free apps don't mislead users about costs

Analyze relevant business logic code and UI flows.
```

### Step 6: Evaluate Design Guidelines Compliance (Section 4)

Design guidelines ensure apps provide unique value and aren't clones or spam.

**Prompt 10 - Copycat Apps (Guideline 4.1):**
```
Check compliance with Guideline 4.1 regarding copycat apps and spam:
- Review app name, bundle identifier, and branding in @Info.plist
- Verify we're not using another developer's icon, brand, or product name
  without authorization (Guideline 4.1(c) added November 2025)
- Ensure the app provides substantial unique functionality
- Check that app isn't a simple repackaging of existing content

Report any potential clone or spam concerns.
```

**Prompt 11 - Mini Apps and Extensions (Guideline 4.7):**
```
If my app contains mini apps, web views, or JavaScript-based mini games,
verify Guideline 4.7 compliance (updated November 2025):
- HTML5 and JavaScript mini apps must follow App Store guidelines
- Ensure mini apps don't bypass Apple's payment systems
- Verify mini apps don't provide access to prohibited content
- Check that web views are used for legitimate purposes, not to circumvent review

Analyze @WKWebView implementations and embedded content loading.
```

### Step 7: Ensure Legal Guidelines Compliance (Section 5) - CRITICAL

Legal compliance, especially privacy, is the #1 rejection reason in 2025.

**Prompt 12 - Privacy Policy and Data Collection (Guideline 5.1.1):**
```
CRITICAL PRIVACY CHECK - Guideline 5.1.1 (Data Collection and Storage):
Analyze all data collection code and @Info.plist:

1. Verify privacy policy URL is present in Info.plist (CFBundlePrivacyPolicyURL)
2. Check that privacy policy is accessible within the app
3. Ensure app only requests necessary permissions
4. Verify permission request descriptions are clear and specific:
   - NSCameraUsageDescription
   - NSPhotoLibraryUsageDescription
   - NSLocationWhenInUseUsageDescription
   - NSMicrophoneUsageDescription
   - NSContactsUsageDescription
   - NSHealthShareUsageDescription
   (and all others used)
5. Check that app doesn't force unnecessary data access
6. Verify no data collection happens without user awareness

Report all privacy policy and data collection issues.
```

**Prompt 13 - Third-Party Data Sharing and AI (Guideline 5.1.2):**
```
CRITICAL 2025 UPDATE - Guideline 5.1.2 (Data Use and Sharing):
This guideline was updated November 13, 2025 to require explicit disclosure
of data sharing with third-party AI.

Analyze networking code, SDKs, and analytics implementations:
1. Identify ALL third-party services receiving user data
2. Check for explicit user consent BEFORE sharing data with third parties
3. SPECIFICALLY verify disclosure for third-party AI data sharing
4. Ensure data sharing purposes are clearly explained to users
5. Verify compliance with GDPR, CCPA if operating in those regions
6. Check that data isn't shared for undisclosed advertising purposes

Review @NetworkManager.swift, @AnalyticsManager.swift, and all third-party SDK integrations.
Flag any data sharing without explicit user consent.
```

**Prompt 14 - App Tracking Transparency (Guideline 5.1.2 / ATT):**
```
Check App Tracking Transparency (ATT) compliance for iOS 14.5+:
- Verify NSUserTrackingUsageDescription is in Info.plist if tracking occurs
- Ensure AppTrackingTransparency framework is imported if needed
- Check that tracking only occurs after user grants permission
- Verify no circumvention of ATT requirements (e.g., fingerprinting)

Analyze @Info.plist and all tracking-related code.
Report any ATT violations as these cause immediate rejection.
```

**Prompt 15 - Privacy Manifest (Guideline 5.1.1):**
```
Check for required Privacy Manifest (PrivacyInfo.xcprivacy):
- Verify PrivacyInfo.xcprivacy file exists in project
- Ensure all collected data types are declared
- Check that required reason APIs are properly documented
- Verify third-party SDK privacy manifests are included

Analyze @PrivacyInfo.xcprivacy file structure and completeness.
Flag if privacy manifest is missing or incomplete.
```

**Prompt 16 - Intellectual Property (Guideline 5.2):**
```
Review intellectual property compliance per Guideline 5.2:
- Check for unauthorized use of Apple trademarks or copyrights
- Verify all third-party assets have proper licensing
- Ensure app doesn't infringe on other apps' IP
- Review that app name and assets don't mislead users

Analyze asset catalogs, app name, and any trademark usage.
```

### Step 8: Review Platform-Specific Requirements

**Prompt 17 - iOS-Specific Guidelines:**
```
Check iOS platform-specific requirements:
- Verify proper use of Apple's Human Interface Guidelines
- Check for prohibited use of private APIs (search for @available and
  any undocumented API usage)
- Ensure proper handling of background modes in @Info.plist
- Verify compliance with Notarization requirements (if applicable)

Scan entire codebase for private API usage and platform violations.
```

### Step 9: Generate Comprehensive Compliance Report

After completing individual category checks, request a consolidated report:

**Prompt 18 - Comprehensive Summary:**
```
Based on all the compliance checks we've performed, generate a comprehensive
App Store Review Guidelines compliance report with:

1. **Executive Summary**: Overall compliance status
2. **Critical Issues**: Any violations that will cause immediate rejection
3. **High-Priority Warnings**: Issues likely to cause rejection
4. **Medium-Priority Recommendations**: Best practices to follow
5. **Category Breakdown**: Issues by guideline section (Safety, Performance,
   Business, Design, Legal)
6. **Action Items**: Prioritized list of fixes needed before submission
7. **Estimated Risk Level**: Low/Medium/High rejection risk

Format as a detailed markdown report.
```

### Step 10: Implement AI-Recommended Fixes

Work through the AI's compliance report systematically:

1. Address all **Critical Issues** first (privacy violations, payment issues, etc.)
2. Fix **High-Priority Warnings** next
3. Implement **Medium-Priority Recommendations** as time allows
4. Re-run specific compliance checks after making changes
5. Use the AI to verify your fixes:

**Prompt 19 - Verify Fixes:**
```
I've made the following changes to address compliance issues:
[Describe your changes]

Please re-analyze @[ModifiedFiles].swift and verify the issues are resolved.
Confirm the changes satisfy App Store Review Guidelines requirements.
```

### Step 11: Perform Final Pre-Submission Validation

Before submitting to App Store Connect:

**Prompt 20 - Final Validation:**
```
Perform a final pre-submission compliance validation:
- Confirm all critical and high-priority issues have been resolved
- Verify Info.plist contains all required keys
- Check that privacy manifest is complete and accurate
- Ensure all permission descriptions are user-friendly
- Validate that third-party AI data sharing disclosures are in place
- Confirm no placeholder or test content remains
- Verify build is stable and crash-free

Provide go/no-go recommendation for App Store submission.
```

## Expected Results

After completing this comprehensive AI-powered compliance review, you should have:

1. **Detailed Compliance Report**: A systematic analysis of your app against all five App Store Review Guideline sections
2. **Identified Violations**: Specific code locations and configurations that violate guidelines
3. **Prioritized Action Items**: Clear list of required fixes ranked by rejection risk
4. **Privacy Compliance Verification**: Confirmation that data collection, sharing, and third-party AI disclosures meet 2025 requirements
5. **Payment System Validation**: Assurance that in-app purchases and subscriptions comply with Apple's policies
6. **Reduced Rejection Risk**: Significantly higher probability of first-submission approval
7. **Documentation for Reference**: AI conversation history showing compliance due diligence

**Success Indicators:**
- Zero critical issues remaining in final report
- All privacy-related requirements satisfied (privacy policy, ATT, data sharing disclosures)
- Payment mechanisms properly implemented with StoreKit
- No placeholder content or incomplete features
- Accurate metadata and permission descriptions
- Compliance with November 2025 guideline updates (copycat apps, third-party AI, loan APR limits)

## Troubleshooting

### Common Issue 1: AI Misunderstands App Context or Functionality

**Problem:** The AI flags features as violations when they're actually compliant (false positives), or misses real issues because it doesn't understand your app's purpose.

**Solution:**
- Provide detailed context in your initial prompt: "My app is a [category] app that [primary function]. It uses [monetization model] and targets [audience]."
- Use @-mentions to reference specific files: `@PaymentManager.swift` helps AI focus on relevant code
- Correct the AI when it makes errors: "This isn't a violation because [reason]. Please re-evaluate."
- Break complex apps into smaller review sessions (e.g., review payments separately from privacy)
- Consider using Claude Sonnet 4 for better contextual understanding of complex codebases

### Common Issue 2: AI Can't Access All Required Files

**Problem:** AI doesn't have visibility into asset catalogs, storyboards, or certain project files needed for comprehensive review.

**Solution:**
- Manually review visual assets (icons, screenshots) yourself against Guideline 4.1(c) for copycat concerns
- Export Info.plist and PrivacyInfo.xcprivacy to text format and paste directly into AI prompts
- For storyboard-based apps, provide screenshots or descriptions of UI flows to the AI
- Use Xcode's built-in tools to validate assets (Product > Analyze, Organizer > Validate App)
- Combine AI code review with manual design and asset verification

### Common Issue 3: Conflicting Advice Between AI Models

**Problem:** ChatGPT and Claude give different recommendations about the same compliance issue, causing confusion.

**Solution:**
- When models disagree, reference the official App Store Review Guidelines document directly: https://developer.apple.com/app-store/review/guidelines/
- Check recent guideline updates (November 2025 is most recent) to ensure AI has current information
- Consult Apple Developer Forums for real-world examples of rejections/approvals
- When in doubt, choose the more conservative (stricter) interpretation to reduce rejection risk
- Use GPT-5 Reasoning mode specifically for guideline interpretation edge cases

### Common Issue 4: AI Misses Recently Updated Guidelines

**Problem:** AI doesn't flag violations of November 2025 guideline updates (copycat apps, third-party AI disclosures, loan APR limits).

**Solution:**
- Explicitly mention the guideline update date in your prompts: "Check compliance with Guideline 5.1.2(i) added November 13, 2025 regarding third-party AI data sharing"
- Provide the specific guideline text: "Guideline 4.1(c) now states: 'You cannot use another developer's icon, brand, or product name in your app's icon or name, without approval'"
- Manually verify these specific new requirements:
  - Guideline 1.2.1(a): Creator apps must age-gate content exceeding app rating
  - Guideline 3.2.2(ix): Loan apps cannot exceed 36% APR or require full repayment within 60 days
  - Guideline 4.1(c): No unauthorized use of other developers' branding
  - Guideline 4.7: HTML5/JS mini apps are now explicitly in scope
  - Guideline 5.1.2(i): Must disclose and get consent for third-party AI data sharing

## Additional Tips

### Key Guideline Areas to Focus On

**Highest Rejection Risk (Check These First):**
1. **Privacy compliance** (5.1.1, 5.1.2) - #1 rejection reason in 2025
2. **In-app purchase violations** (3.1.1) - External payment links cause immediate rejection
3. **App crashes or incomplete functionality** (2.1) - Apple tests your app during review
4. **Missing or inaccurate metadata** (2.3) - Include privacy policy, accurate descriptions
5. **Third-party AI data sharing** (5.1.2(i)) - NEW in November 2025, must have explicit consent

**Guideline Updates to Verify (November 2025):**
- Ensure your app doesn't violate the new anti-copycat rule (4.1(c))
- If your app shares data with third-party AI services, add explicit disclosure and consent flow (5.1.2(i))
- If your app offers loans, verify APR ≤ 36% and repayment period > 60 days (3.2.2(ix))
- If your app includes mini apps or web content, ensure compliance with expanded Guideline 4.7

### Best Practices for AI-Assisted Compliance Review

1. **Run Multiple Passes**: Don't rely on a single AI prompt. Use the 20-prompt workflow above for comprehensive coverage.
2. **Compare AI Models**: Run critical sections (privacy, payments) through both ChatGPT and Claude for verification.
3. **Document Your Review**: Save AI conversation history as evidence of due diligence if issues arise.
4. **Stay Current**: Check https://developer.apple.com/news/ for guideline updates before each submission.
5. **Test on Device**: AI can't catch runtime issues—always test on physical devices before submission.
6. **Use App Store Connect Pre-Checks**: After AI review, use App Store Connect's built-in validation before final submission.
7. **Review Third-Party SDKs**: Manually verify that analytics, advertising, and other SDKs comply with privacy requirements.

### Prompt Engineering Tips

- **Be Specific**: Instead of "check my app," use "check @PaymentManager.swift for Guideline 3.1.1 compliance with in-app purchase requirements"
- **Provide Context**: Always explain what your app does and its business model
- **Reference Exact Guidelines**: Use guideline numbers (e.g., "Guideline 5.1.2(i)") for precise analysis
- **Request Code Examples**: Ask AI to "show example code that would be compliant" for comparison
- **Iterate Based on Feedback**: If AI finds an issue, ask "how should I fix this to comply with [guideline]?"

### Understanding Common Rejection Scenarios

**Privacy Rejections:**
- Missing or inaccessible privacy policy URL
- Permission descriptions that don't explain actual usage
- Data collection without user awareness
- Missing App Tracking Transparency implementation
- No disclosure of third-party AI data sharing (new in 2025)

**Payment Rejections:**
- "Buy" buttons linking to external websites for digital goods
- Subscription apps without proper terms disclosure
- Apps bypassing in-app purchase for digital content
- Misleading pricing or hidden costs

**Performance Rejections:**
- App crashes during Apple's review testing
- Incomplete features or placeholder content
- Test/debug credentials left in production build
- Broken navigation or non-functional buttons

**Design Rejections:**
- App provides minimal functionality (shell apps)
- Copycat of existing apps without unique value
- Using another developer's branding without permission (new strict enforcement in 2025)

## Related Articles

- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-010: How to set up signing and capabilities for your app project
- KB-039: How to fix errors and bugs using ChatGPT in Xcode
- KB-048: How to leverage Claude's context window for comprehensive code reviews
- KB-055: How to have AI explain error messages and suggest fixes
- KB-040: How to generate documentation with ChatGPT for App Store submissions

## Sources

- Apple Developer - App Store Review Guidelines - https://developer.apple.com/app-store/review/guidelines/ (Accessed: November 17, 2025)
- Apple Developer News - Updated guidelines now available (November 13, 2025) - https://developer.apple.com/news/?id=9txfddzf (Accessed: November 17, 2025)
- Apple Developer - User Privacy and Data Use - https://developer.apple.com/app-store/user-privacy-and-data-use/ (Accessed: November 17, 2025)
- 9to5Mac - Apple's new App Review Guidelines crack down on copycat apps (November 13, 2025) - https://9to5mac.com/2025/11/13/apple-tightens-app-review-guidelines-to-crack-down-on-copycat-apps/ (Accessed: November 17, 2025)
- TechCrunch - Apple's new App Review Guidelines clamp down on apps sharing personal data with 'third-party AI' (November 13, 2025) - https://techcrunch.com/2025/11/13/apples-new-app-review-guidelines-clamp-down-on-apps-sharing-personal-data-with-third-party-ai/ (Accessed: November 17, 2025)
- H2S Media - Apple Updates App Store Review Guidelines: New Rules Target Clone Apps, Predatory Loans, and AI Data Sharing - https://www.how2shout.com/news/apple-app-store-guidelines-update-november-2025-clone-apps-ai-privacy.html (Accessed: November 17, 2025)
- ASO World - Apple App Store Agreement & Guideline Updates (June 2025) - https://asoworld.com/blog/apple-app-store-agreement-guideline-updates-june-2025/ (Accessed: November 17, 2025)
- The App Launchpad - App Store Review guidelines for Safety, Performance, Business, Design, and Legal in detail - https://theapplaunchpad.com/blog/app-store-review-guidelines (Accessed: November 17, 2025)
- AppFollow - iOS App Store Review Guidelines: How to Pass & Avoid Rejection - https://appfollow.io/blog/app-store-review-guidelines (Accessed: November 17, 2025)
- Next Native - App Store Review Guidelines (2025): Simple Checklist for Fast Approval - https://nextnative.dev/blog/app-store-review-guidelines (Accessed: November 17, 2025)
- UXCam - Top 10 App Store Rejection Reasons and How to Fix them - https://uxcam.com/blog/app-store-rejection-reasons/ (Accessed: November 17, 2025)
- Adapty - Top reasons for App Store rejections and how to avoid them - https://adapty.io/blog/app-store-rejection/ (Accessed: November 17, 2025)
- OneMobile - 14 Common Apple App Store Rejections and How To Avoid Them - https://onemobile.ai/common-app-store-rejections-and-how-to-avoid-them/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
