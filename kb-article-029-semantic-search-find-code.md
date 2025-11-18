# How to use Semantic Search to find code across your project

**Article ID:** KB-029
**Difficulty:** Beginner
**Last Updated:** November 17, 2025
**Estimated Time:** 10-15 minutes

## Overview

Xcode 26 introduces a powerful Semantic Search feature that revolutionizes how you find code across your projects. Unlike traditional exact-match searching, Semantic Search uses advanced search engine techniques to find clusters of related words, even when they appear in different orders or span multiple lines. Results are intelligently sorted by relevance, making it perfect for exploring unfamiliar codebases, locating specific functionality, or understanding how features are implemented. This feature transforms the Find Navigator into a semantic understanding tool that thinks more like a developer and less like a simple text matcher.

## Prerequisites

- Xcode 26.1 or later installed on your Mac
- An Xcode project open (existing project recommended for meaningful search results)
- Basic familiarity with Xcode's interface and navigation (see KB-006: How to understand and navigate the Xcode 26 interface)
- Understanding of basic programming concepts and code structure

## Steps

### Step 1: Understanding Semantic Search vs. Traditional Search

Before using Semantic Search, it's important to understand how it differs from traditional find-and-replace functionality.

**Traditional Search (Pre-Xcode 26):**
- Searches for exact text matches
- Case-sensitive or case-insensitive literal matching
- Finds text in the exact order you type it
- Results appear in file order or alphabetical order
- Requires precise knowledge of what you're looking for

**Semantic Search (Xcode 26+):**
- Finds clusters of related words across your project
- Search terms can appear in any order within the code
- Terms can span multiple lines of code
- Results are sorted by relevance to your search intent
- Understands context and code relationships
- Ideal for exploratory searches when you know what functionality you need but not the exact implementation

**Example Scenario:**
Suppose you're looking for code that handles user authentication with a token. With traditional search, you'd need to search for "authentication token" or "token authentication" as exact phrases. With Semantic Search, you can search for "user authentication token" and Xcode will find relevant code even if those words appear as "token for user authentication" or across multiple lines like:

```swift
// Authenticate user
let token = authenticationService.getToken()
```

### Step 2: Opening the Find Navigator for Semantic Search

The Semantic Search feature is integrated into Xcode's Find Navigator, which allows you to search across your entire project.

**To open the Find Navigator:**

**Method 1 - Keyboard Shortcut:**
- Press `⌘⇧F` (Command + Shift + F)
- This is the fastest and most commonly used method

**Method 2 - Navigator Panel:**
- Press `⌘4` (Command + 4) to switch to the Find Navigator
- If the Navigator panel is hidden, first press `⌘0` (Command + 0) to show it, then press `⌘4`

**Method 3 - Menu Bar:**
- Navigate to **Find > Find in Workspace** from the menu bar
- Or go to **View > Navigators > Find** (⌘4)

**What You'll See:**
The Find Navigator appears in the left sidebar with a search field at the top and a results area below. The search field is where you'll enter your semantic search terms.

### Step 3: Entering Your Semantic Search Query

With the Find Navigator open, you can now enter a semantic search query to find code across your project.

**Basic Semantic Search:**
1. Click in the search field at the top of the Find Navigator
2. Type multiple words that describe the functionality you're looking for
3. Press Return or wait for results to populate automatically

**Example Queries:**

```
user authentication token
```
Finds code related to user authentication and tokens, regardless of word order.

```
network request error handling
```
Locates code that deals with network requests and how errors are handled.

```
database save operation
```
Discovers database-related save operations throughout your project.

```
image download cache
```
Finds image downloading and caching implementations.

**Key Principle:** Think about the concepts and functionality you're searching for, not the exact variable names or syntax. Semantic Search understands the relationships between words and code patterns.

### Step 4: Configuring Search Options

The Find Navigator provides several options to refine your semantic search results.

**Search Options (located below the search field):**

1. **Text/References Toggle:**
   - **Text** - Searches through all text content in files (default for Semantic Search)
   - **References** - Searches for symbol definitions and references
   - For Semantic Search, keep this set to **Text**

2. **Search Scope:**
   - Click the scope selector to choose where to search
   - **Workspace** - Searches all files in your project (recommended for Semantic Search)
   - **Project** - Searches only your main project files
   - **Current File** - Limits search to the currently open file
   - **Selected Folders** - Searches only in folders you select

3. **Matching Options:**
   - Click **Show Find Options** (three-line icon) to reveal additional settings
   - **Ignore Case** - Makes search case-insensitive (recommended for Semantic Search)
   - **Match Word** - Only matches whole words (can limit semantic matching)
   - **Regular Expression** - Uses regex patterns (not typically needed for Semantic Search)

**Recommended Configuration for Semantic Search:**
- **Scope:** Workspace (to search your entire project)
- **Ignore Case:** Enabled (checked)
- **Match Word:** Disabled (unchecked) for more flexible semantic matching
- **Regular Expression:** Disabled (unchecked)

### Step 5: Interpreting Semantic Search Results

Once you enter your search query, Xcode displays results sorted by relevance rather than alphabetical or file order.

**Understanding the Results List:**

The Find Navigator displays results in a hierarchical format:
- **File groupings** - Files containing matches are listed with their paths
- **Line numbers** - Each match shows the line number where it was found
- **Context preview** - A snippet of code showing the match in context
- **Relevance ordering** - Most relevant results appear at the top

**Result Indicators:**

Each search result shows:
1. **File icon and name** - The file containing the match
2. **Line number** - Where in the file the match occurs
3. **Code snippet** - A preview of the matching code with search terms highlighted
4. **Match count badge** - Number of matches in each file (shown next to filename)

**Example Result Interpretation:**

If you search for "button action tap", you might see:
```
▼ LoginViewController.swift (3 matches)
   42: @IBAction func loginButtonTapped(_ sender: UIButton) {
   67: // Handle tap action on forgot password button
   89: submitButton.addTarget(self, action: #selector(buttonAction), for: .touchUpInside)
```

Notice how "button", "action", and "tap" appear in different forms and orders, but Semantic Search found all relevant locations. The word "tapped" is semantically similar to "tap", and "action" appears in both method names and comments.

### Step 6: Navigating to Search Results

Once you've found relevant results, you can quickly jump to specific code locations.

**To navigate to a search result:**

**Method 1 - Single Click:**
- Click any search result in the Find Navigator
- The file opens in the Editor Area with the matching line highlighted
- The cursor automatically positions at the matched text

**Method 2 - Double Click:**
- Double-click a result to open it in the Editor
- Some developers prefer double-click to avoid accidental navigation

**Method 3 - Keyboard Navigation:**
- Use **↑** and **↓** arrow keys to move between results
- Press **Return** to open the selected result in the Editor
- Press **⌥⏎** (Option + Return) to open in a new editor tab

**Keyboard Shortcuts While Viewing Results:**
- `⌘↑` - Jump to previous match
- `⌘↓` - Jump to next match
- `⌘⇧[` - Navigate to previous file with matches
- `⌘⇧]` - Navigate to next file with matches

**Pro Tip:** Keep the Find Navigator visible while navigating results so you can see the full list and context. Use `⌘0` to toggle the Navigator Area visibility.

### Step 7: Refining Your Semantic Search

If your initial search returns too many results or misses what you're looking for, you can refine your query for better precision.

**Strategies for Better Results:**

**1. Add More Context Words:**
Instead of: `save`
Try: `save user profile settings`

More context words help Semantic Search understand your intent and filter out irrelevant matches.

**2. Use Domain-Specific Terms:**
Instead of: `get data`
Try: `fetch network API response`

Technical terminology helps narrow results to specific implementations.

**3. Include Action and Object Words:**
Instead of: `login`
Try: `user login authentication flow`

Combining action words (login, authentication) with object words (user, flow) provides clearer intent.

**4. Specify Scope if Needed:**
- If you get too many results from your entire workspace, narrow the scope to specific folders
- Click the scope selector and choose specific folders or groups
- Example: Search only in "Models" folder for data-related queries

**5. Filter by File Type:**
After searching, you can mentally filter results by looking at file extensions:
- `.swift` files - Swift source code
- `.h` or `.m` files - Objective-C code
- `.storyboard` or `.xib` files - Interface Builder files
- `.json` files - Configuration or data files

### Step 8: Using Semantic Search for Code Exploration

Semantic Search excels at helping you explore unfamiliar codebases or understand how features are implemented.

**Exploratory Search Techniques:**

**1. Finding Feature Implementations:**
When joining a new project or exploring open-source code, search for features you know exist:
```
payment processing stripe
location tracking GPS
push notification handling
animation transition view
```

**2. Understanding Architecture Patterns:**
Search for architectural concepts to understand code organization:
```
view model data binding
coordinator navigation flow
dependency injection container
singleton shared instance
```

**3. Discovering Error Handling:**
Find how the codebase handles specific error scenarios:
```
network timeout retry
parse error JSON
authentication failure response
```

**4. Locating UI Implementations:**
Search for user interface elements and interactions:
```
table view cell configuration
collection view layout
button alert action
text field validation
```

**5. Finding Performance-Related Code:**
Identify optimization and performance code:
```
background thread async
cache memory management
image compression resize
lazy loading pagination
```

**Real-World Example:**

Imagine you've inherited a large codebase and need to understand how images are downloaded and cached. Instead of hunting through dozens of files, try this semantic search:

```
image download cache memory
```

Xcode will find all relevant code, even if it's spread across:
- `ImageDownloader.swift` - Download implementation
- `CacheManager.swift` - Cache storage logic
- `ImageViewModel.swift` - View model coordinating downloads and cache
- `NetworkService.swift` - Network request handling

The results appear sorted by relevance, so the most central implementation code appears first.

### Step 9: Combining Semantic Search with Other Find Features

Semantic Search works alongside Xcode's other search capabilities for comprehensive code discovery.

**Complementary Search Tools:**

**1. Symbol Navigator (⌘3):**
- Use Semantic Search to find general code patterns
- Then use Symbol Navigator to find specific class or method definitions
- Example workflow: Semantic search finds "authentication token", Symbol Navigator locates the exact `AuthenticationToken` class

**2. Find in File (⌘F):**
- Use Semantic Search to locate the right file
- Then use Find in File (⌘F) for precise searching within that file
- Helpful when Semantic Search points you to the right file but you need specific details

**3. Quick Open (⌘⇧O):**
- After Semantic Search reveals relevant class or file names
- Use Quick Open to instantly jump to those files by name
- Type the filename or symbol name for fast navigation

**4. Global Search and Replace:**
- Use Semantic Search to understand where functionality is implemented
- Then use Find and Replace (in Find Navigator) for refactoring
- Example: Find all authentication code semantically, then replace specific method names

**Workflow Example:**

1. **Semantic Search** (`⌘⇧F`): `network request timeout error`
   - Discover files and patterns related to network timeouts

2. **Click result** to open `NetworkManager.swift`
   - Review the general implementation

3. **Symbol Navigator** (`⌘3`): Type `timeout`
   - Find the specific `timeoutInterval` property or `handleTimeout()` method

4. **Find in File** (`⌘F`): Search for specific error codes
   - Locate exact error handling logic

This multi-tool approach leverages Semantic Search for broad discovery and traditional tools for precision work.

### Step 10: Best Practices for Semantic Search

Follow these best practices to get the most value from Semantic Search in your daily development workflow.

**Do's:**

✅ **Use 2-5 words** - Semantic Search works best with multiple context words, not single terms
✅ **Think conceptually** - Search for what the code does, not exact variable names
✅ **Include action verbs** - Words like "fetch", "save", "update", "handle" help identify functionality
✅ **Combine nouns and verbs** - "update user profile" is better than just "update" or "user"
✅ **Try variations** - If first search doesn't work, rephrase with synonyms
✅ **Search the workspace** - Use full workspace scope for comprehensive results
✅ **Explore top results first** - Relevance sorting means best matches are usually at the top

**Don'ts:**

❌ **Don't use exact syntax** - Semantic Search is conceptual, not literal
❌ **Don't include punctuation** - Search for "user authentication" not "user.authenticate()"
❌ **Don't expect regex patterns** - Use regular expression search for pattern matching
❌ **Don't search single words** - Single words return too many irrelevant results
❌ **Don't ignore context** - More descriptive searches yield better results
❌ **Don't forget scope** - Searching third-party libraries might add noise; narrow scope if needed

**Example Comparisons:**

| Less Effective | More Effective |
|----------------|----------------|
| `fetch` | `fetch user data network` |
| `error` | `network request error handling` |
| `view` | `configure table view cell` |
| `login` | `user login authentication flow` |
| `button` | `button tap action handler` |

**Performance Tip:** Semantic Search processes your entire workspace, which can take a moment in very large projects (50,000+ lines). For instant results during rapid development, consider narrowing the scope to specific folders or using traditional Find in File for exact matches in known locations.

## Expected Results

After following this guide, you should be able to:

- Understand the difference between Semantic Search and traditional exact-match searching
- Open the Find Navigator and enter semantic search queries effectively
- Configure search options (scope, case sensitivity) for optimal semantic matching
- Interpret search results sorted by relevance rather than alphabetical order
- Navigate quickly to specific code locations from search results
- Refine searches by adding context words and domain-specific terminology
- Use Semantic Search to explore unfamiliar codebases and discover feature implementations
- Combine Semantic Search with other Xcode find tools (Symbol Navigator, Quick Open, Find in File)
- Apply best practices for conceptual, multi-word searches that think like a developer

**Practical Outcome:**
You'll spend less time hunting through files manually and more time understanding and working with code. Semantic Search transforms code discovery from a frustrating needle-in-haystack experience to an intelligent, relevance-based exploration tool.

## Troubleshooting

### Common Issue 1: Search Returns Too Many Irrelevant Results
**Problem:** Your semantic search query returns hundreds of results, most of which aren't what you need.
**Solution:**
- Add more context words to your query. Instead of "save data", try "save user profile data database"
- Narrow your search scope. Click the scope selector and choose specific folders or groups
- Use more technical, domain-specific terms. Instead of "get", use "fetch" or "retrieve"
- Check that you're searching "Text" and not "References" (references search is for symbols only)
- Review the top 5-10 results - relevance sorting means the best matches appear first, so you may not need to scroll through all results

### Common Issue 2: Expected Code Doesn't Appear in Results
**Problem:** You know certain code exists but Semantic Search isn't finding it.
**Solution:**
- Try different synonym variations. Search for "authentication login user" if "user authentication" didn't work
- Verify the code is in your search scope. If you've limited scope to specific folders, the file might be elsewhere
- Check if the words you're searching for actually appear in the code. Semantic Search is powerful but still relies on text matching - it won't find code that uses completely different terminology
- Use traditional Find (`⌘⇧F` with exact text) to verify the code exists, then adjust your semantic query
- Ensure you haven't enabled "Match Word" option, which can restrict semantic matching
- Try shorter queries - sometimes 2-3 words work better than 5-6

### Common Issue 3: Search Results Not Sorted by Relevance
**Problem:** Results appear in alphabetical or random order instead of by relevance.
**Solution:**
- Verify you're using Xcode 26.1 or later - Semantic Search with relevance sorting is a Xcode 26+ feature
- Check that you're in the Find Navigator (`⌘⇧F` or `⌘4`), not doing a simple Find in File (`⌘F`)
- Results within each file are shown in line-number order, but files themselves are ordered by relevance
- If using an older version of Xcode, upgrade to Xcode 26.1+ to access Semantic Search
- Try closing and reopening Xcode if the feature seems unresponsive - indexing may need to complete

### Common Issue 4: Semantic Search Seems Slow or Unresponsive
**Problem:** Search takes a long time to return results or seems to hang.
**Solution:**
- Check the Activity viewer in the center of the Toolbar - Xcode might still be indexing your project
- Wait for indexing to complete (look for "Indexing..." message in Toolbar activity viewer)
- For very large projects (100,000+ lines), Semantic Search may take 5-10 seconds on first search
- Subsequent searches are faster due to caching
- Narrow your search scope to specific folders for faster results
- Close other resource-intensive applications to free up system resources
- Restart Xcode if performance doesn't improve - Product > Clean Build Folder (⌘⇧K) then reopen the project

### Common Issue 5: Can't Access Find Navigator
**Problem:** Pressing `⌘⇧F` or `⌘4` doesn't open the Find Navigator.
**Solution:**
- Verify the Navigator Area is visible - press `⌘0` to show it if hidden
- Try clicking the Find Navigator icon (magnifying glass) directly in the Navigator selector bar at the top of the Navigator panel
- Check keyboard shortcut settings: **Xcode > Settings > Key Bindings** and search for "Find in Workspace" to verify the shortcut
- Ensure another application isn't capturing the keyboard shortcut (like clipboard managers or window management apps)
- Restart Xcode to reset the interface state

## Additional Tips

- **Start broad, then narrow** - Begin with general semantic searches to explore a codebase, then use more specific queries or traditional find tools to pinpoint exact implementations.

- **Semantic Search for onboarding** - When joining a new team or project, use Semantic Search to quickly understand the architecture by searching for "view controller navigation", "data model", "API service", etc.

- **Combine with AI assistance** - After finding relevant code with Semantic Search, use Xcode 26's integrated AI coding assistant (see KB-007) to ask questions about the code you've discovered.

- **Search for patterns, not names** - Instead of searching for variable names you don't know yet, search for what the code does: "calculate total price discount" instead of trying to guess if it's `calculateTotal()` or `computePrice()`.

- **Use for refactoring planning** - Before refactoring, use Semantic Search to discover all related code areas. Search for the feature or pattern you want to refactor to ensure you don't miss any dependencies.

- **Document your searches** - If you discover useful semantic queries for your project, document them in your team wiki or README. Examples: "user notification permission handling", "offline data sync logic".

- **Multi-line searching** - Remember that Semantic Search can find words that span multiple lines, so search for "network request error retry" even if those words are on separate lines in the implementation.

- **Case doesn't matter** - Semantic Search with "Ignore Case" enabled finds `NetworkManager`, `networkManager`, and `NETWORK_MANAGER` equally well.

- **Leverage relevance sorting** - Don't feel obligated to review every search result. The relevance algorithm surfaces the most important matches first, so focusing on the top 10-15 results is often sufficient.

- **Quick context switching** - Use Semantic Search to quickly switch contexts between different features. Working on payments? Search "payment processing". Switching to authentication? Search "user authentication login". This helps you rapidly locate relevant code when task-switching.

- **Export search results** - While Xcode doesn't directly export Find Navigator results, you can take screenshots or manually document important findings for team discussions or code reviews.

- **Compare with Symbol Navigator** - For finding specific class or method definitions, the Symbol Navigator (`⌘3`) is faster. Use Semantic Search when you're exploring functionality across multiple files and classes.

## Related Articles

- KB-006: How to understand and navigate the Xcode 26 interface (Navigator, Editor, Inspector, Debug areas)
- KB-007: How to access the new Intelligence settings tab in Xcode preferences
- KB-026: How to use Quick Actions with semantic search and abbreviations (Command+Shift+A)
- KB-027: How to navigate code with the enhanced Symbol Navigator
- KB-028: How to use AI-assisted code completion and suggestions

## Sources

- Apple Developer Documentation - What's new in Xcode 26 - https://developer.apple.com/videos/play/wwdc2025/247/ (Accessed: November 17, 2025)
- Apple Developer Documentation - Xcode 26 Release Notes - https://developer.apple.com/documentation/xcode-release-notes/xcode-26-release-notes (Accessed: November 17, 2025)
- Apple Developer Documentation - Xcode 26.1.1 Release Notes - https://developer.apple.com/documentation/xcode-release-notes/xcode-26_1-release-notes (Accessed: November 17, 2025)
- Apple Developer Documentation - Finding and replacing content in a project - https://developer.apple.com/documentation/xcode/finding-and-replacing-content-in-a-project (Accessed: November 17, 2025)
- Zignuts - Top iOS 26 Developer Tools & APIs | WWDC 2025 Highlights - https://www.zignuts.com/blog/ios-26-developer-tools-api-enhancements (Accessed: November 17, 2025)
- DEV Community - WWDC 2025 - What's new in Xcode 26 - https://dev.to/arshtechpro/wwdc-2025-whats-new-in-xcode-26-3oo3 (Accessed: November 17, 2025)
- Medium - Xcode 26 & iOS 26: The New Frontier of Apple Development - https://medium.com/@serkankaraa/xcode-26-ios-26-the-new-frontier-of-apple-development-7ab30b81faf0 (Accessed: November 17, 2025)
- Google Search Query: "Xcode 26 Semantic Search keyboard shortcut Command Shift F how to access"
- Google Search Query: "Xcode 26 Find Navigator enhanced search features improvements WWDC 2025"

---
*This article is part of the Claude Code Web knowledge base*
