# How to generate SwiftUI views from natural language descriptions using ChatGPT

**Article ID:** KB-037
**Difficulty:** Beginner-Intermediate
**Last Updated:** November 18, 2025
**Estimated Time:** 10-15 minutes

## Overview
Xcode 26.1+ includes integrated ChatGPT support that allows you to generate complete SwiftUI views directly from natural language descriptions. This powerful feature eliminates tedious boilerplate code and accelerates prototyping by converting text prompts into functional Swift code. Whether you're building a simple list view or a complex form, you can describe what you want and let ChatGPT handle the implementation details.

## Prerequisites
- Xcode 26.1 or later installed
- macOS 15 (Sequoia) or later running on Apple silicon Mac (M1 or newer)
- An OpenAI account with ChatGPT API access (for extended usage), or use Xcode's built-in free tier for basic requests
- Basic understanding of SwiftUI syntax and structure
- Familiarity with Swift data types and view composition

## Steps

### Step 1: Access the Coding Assistant in Xcode

Open Xcode 26.1 or later and navigate to the file where you want to generate a SwiftUI view. The Coding Assistant is built directly into the editor. You can access it by:

1. Click on **Xcode** in the menu bar
2. Navigate to **Settings** (or press `Cmd + ,`)
3. Select the **Intelligence** tab
4. Verify that ChatGPT is available as a model provider (it's included by default in Xcode 26+)

If you want to use your own OpenAI API key for higher usage limits, click **Add a Model Provider** and enter your OpenAI credentials. Otherwise, you can use the built-in ChatGPT support for basic requests.

### Step 2: Create a New SwiftUI File

Create a new Swift file for your SwiftUI view:

1. In Xcode, select **File** → **New** → **File**
2. Choose **Swift File** from the template options
3. Name your file (e.g., `CustomListView.swift`)
4. Save it in your project

Alternatively, open an existing Swift file where you want to add a new view.

### Step 3: Write a Clear Natural Language Prompt

Click in the editor where you want to generate code and open the Coding Assistant by pressing `Cmd + I` (or clicking the Coding Assistant button in the editor). In the prompt field, write a clear, descriptive request for your SwiftUI view.

**Example prompts:**

```
Create a SwiftUI view that displays a list of books with title, author, and publication year. Include a search bar at the top to filter books by title. Use a NavigationStack for navigation. The Book model has properties: id (UUID), title (String), author (String), and publicationYear (Int).
```

```
Generate a login form with email and password text fields, a "Remember Me" toggle switch, and a blue login button. Include basic form validation that disables the button if either field is empty. Show appropriate placeholder text.
```

```
Create a settings screen with sections for Account, Notifications, and About. Include a user profile image in a circular frame at the top, toggle switches for notification preferences, and text labels for version information.
```

The more specific your prompt, the better ChatGPT can generate code matching your requirements. Include:
- The purpose of the view
- Specific UI components needed (buttons, lists, text fields, etc.)
- Data to display or input
- Any navigation or interaction behavior
- Design preferences (colors, layout style, etc.)

### Step 4: Review and Refine the Generated Code

ChatGPT will generate SwiftUI code based on your prompt. The assistant will typically provide:

- A complete SwiftUI `View` struct
- Required state variables and properties
- Preview code for testing
- Proper View composition and layout

Example generated code might look like:

```swift
import SwiftUI

struct BookListView: View {
    @State private var searchText = ""
    @State private var books = [
        Book(id: UUID(), title: "Swift Programming", author: "Apple", publicationYear: 2024),
        Book(id: UUID(), title: "SwiftUI by Example", author: "Paul Hudson", publicationYear: 2023)
    ]

    var filteredBooks: [Book] {
        if searchText.isEmpty {
            return books
        } else {
            return books.filter { $0.title.localizedCaseInsensitiveContains(searchText) }
        }
    }

    var body: some View {
        NavigationStack {
            List {
                SearchBar(text: $searchText)

                ForEach(filteredBooks) { book in
                    VStack(alignment: .leading) {
                        Text(book.title)
                            .font(.headline)
                        Text(book.author)
                            .font(.subheadline)
                            .foregroundColor(.gray)
                        Text("Published: \(book.publicationYear)")
                            .font(.caption)
                    }
                }
            }
            .navigationTitle("Books")
        }
    }
}

struct SearchBar: View {
    @Binding var text: String

    var body: some View {
        HStack {
            Image(systemName: "magnifyingglass")
                .foregroundColor(.gray)

            TextField("Search books", text: $text)
                .textFieldStyle(.roundedBorder)

            if !text.isEmpty {
                Button(action: { text = "" }) {
                    Image(systemName: "xmark.circle.fill")
                        .foregroundColor(.gray)
                }
            }
        }
        .padding(.vertical, 8)
    }
}

struct Book: Identifiable {
    let id: UUID
    let title: String
    let author: String
    let publicationYear: Int
}

#Preview {
    BookListView()
}
```

Review the generated code for:
- Correct logic and functionality
- Proper SwiftUI patterns (using @State, @Binding, @Observable as appropriate)
- Clean code structure and readability
- Alignment with your app's design system

### Step 5: Integrate and Test the Generated View

Once satisfied with the generated code:

1. **Copy the code** into your Xcode file (or accept it if using inline generation)
2. **Build the project** by pressing `Cmd + B` to check for compile errors
3. **View the preview** in Xcode's Canvas to see how the view renders
4. **Make adjustments** if needed - modify colors, spacing, or behavior
5. **Run on a simulator or device** to test interactivity and layout on actual hardware

If you're using the generated view in your app's navigation flow, ensure it's properly integrated with your existing views and data models.

### Step 6: Iterate and Refine Using Follow-Up Prompts

Use the Coding Assistant to make incremental improvements. You can ask follow-up questions or request modifications:

```
Add a favorite button (heart icon) to each book entry that saves the favorite state to UserDefaults.
```

```
Change the search bar background color to light gray and add a clear button on the right side.
```

```
Add pull-to-refresh functionality to reload the book list.
```

The Coding Assistant maintains context of previous prompts, allowing you to refine the implementation iteratively without starting over.

## Expected Results

After following these steps, you should have:

1. **A fully functional SwiftUI view** that matches your natural language description
2. **Compilable Swift code** with proper syntax and SwiftUI conventions
3. **A preview** that displays correctly in Xcode's Canvas
4. **Reusable components** that you can integrate into your larger app
5. **Code that follows MVVM architecture** (separating view UI from data logic) when appropriate

The generated view should be immediately functional and displayable in a preview. You may need minor adjustments for customization, but the core functionality should work as described in your prompt.

## Troubleshooting

### Common Issue 1: Generated Code Doesn't Compile
**Problem:** ChatGPT generates code with syntax errors or uses outdated SwiftUI APIs.
**Solution:**
- Copy the error message from Xcode's issue navigator and ask the Coding Assistant to fix it
- Request specific iOS/Swift versions in your prompt (e.g., "Use SwiftUI for iOS 16+")
- Break complex views into smaller, simpler prompts
- Review the generated code and manually fix obvious issues

### Common Issue 2: View Preview Shows Blank or Error
**Problem:** The preview crashes or doesn't display the view.
**Solution:**
- Check the #Preview block for correct view initialization
- Ensure all required data models are defined or have sample data
- Ask ChatGPT to "Add a #Preview with sample data"
- Verify the view's body syntax is correct - use Xcode's syntax highlighting to identify issues
- Check that all @State variables are properly initialized

### Common Issue 3: ChatGPT API Connection Fails or Rate Limited
**Problem:** "Unable to connect to ChatGPT" error or "Rate limit exceeded" message.
**Solution:**
- Verify your internet connection
- Check that your OpenAI API key is valid (if using custom key)
- Wait a few moments and retry - transient connection issues are common
- Use Xcode's built-in free tier instead of custom API keys for basic usage
- If using a custom key, ensure your OpenAI account has available credits
- Check the Xcode Intelligence settings to confirm the model provider is correctly configured

### Common Issue 4: Generated Code Doesn't Match Your Requirements
**Problem:** The view works but doesn't look or behave as expected.
**Solution:**
- Be more specific in your next prompt - mention exact colors, sizes, and behaviors
- Break the request into smaller pieces - ask for styling in a separate prompt
- Provide example data models or show what you want by describing the final appearance
- Use follow-up prompts to refine specific aspects (layout, colors, interactions)
- Ask ChatGPT to "explain" what it generated so you understand and can modify it

## Additional Tips

- **Start with simple views** - Generate basic views first, then use follow-up prompts for added complexity
- **Use descriptive language** - Instead of "make a nice list," say "create a list with system image thumbnails, titles in headline font, and gray subtitles below titles"
- **Reference your data models** - Include property names and types in your prompts for more accurate code generation
- **Test with preview data** - Ask ChatGPT to generate sample data for testing before connecting real data
- **Ask for explanations** - Request that ChatGPT explain the generated code to understand patterns and learn best practices
- **Use MVVM architecture** - Specify "Use MVVM with a ViewModel class" for more complex views to keep logic separate from UI
- **Leverage composition** - Generate smaller, reusable components and combine them in follow-up prompts
- **Keep prompts focused** - Generate one view feature at a time; combining too many requirements in one prompt often produces less optimal code

## Related Articles

- How to use Coding Assistant for debugging and code explanations (when available)
- Understanding SwiftUI View composition and state management (when available)
- Best practices for MVVM architecture in SwiftUI apps (when available)

## Sources

- Apple Developer - Xcode 26 Release Notes - https://developer.apple.com/xcode/ (Accessed: November 18, 2025)
- Swift with Vincent - ChatGPT in Xcode 26: there's a hidden prompt - https://www.swiftwithvincent.com/blog/chatgpt-in-xcode-26-theres-a-hidden-prompt (Accessed: November 18, 2025)
- Apple Newsroom - Apple supercharges its tools and technologies for developers - https://www.apple.com/newsroom/2025/06/apple-supercharges-its-tools-and-technologies-for-developers/ (Accessed: November 18, 2025)
- Anton Derevyanko - How to Integrate ChatGPT and Claude in Xcode 26 - https://www.cisin.com/coffee-break/how-to-integrate-chatgpt-and-claude-in-xcode-26.html (Accessed: November 18, 2025)
- Wendy Liga - Use Custom Models in the New Xcode 26 Intelligence - https://wendyliga.com/blog/xcode-26-custom-model/ (Accessed: November 18, 2025)
- TechCrunch - Apple brings ChatGPT and other AI models to Xcode - https://techcrunch.com/2025/06/09/apple-brings-chatgpt-and-other-ai-models-to-xcode/ (Accessed: November 18, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
