# How to ask AI to implement MVVM architecture for a feature

**Article ID:** KB-053
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 30-45 minutes

## Overview

Xcode 26.1+ includes integrated AI coding assistants (ChatGPT and Claude) that can generate complete MVVM (Model-View-ViewModel) implementations for iOS features from natural language descriptions. By crafting effective prompts, you can have AI create properly structured SwiftUI views, Observable ViewModels, and Models following modern 2025 best practices. This guide teaches you how to prompt AI assistants to implement MVVM architecture patterns, leverage Swift 6 features like @Observable, and create maintainable, testable code that adheres to separation of concerns principles.

## Prerequisites

- Xcode 26.1 or later installed on your Mac
- macOS 15 or later (macOS 26 Tahoe recommended for full Coding Intelligence features)
- An AI model provider configured in Xcode:
  - ChatGPT (built-in with OpenAI account, see KB-031, KB-032) or
  - Claude (requires Anthropic account, see KB-041-046)
- Basic understanding of SwiftUI and MVVM architectural concepts
- Familiarity with Swift data types, property wrappers, and view composition
- An iOS app project or new project where you want to implement a feature
- Active internet connection for AI requests

## Steps

### Step 1: Plan Your Feature Requirements

Before prompting the AI, clearly define what you want to build:

1. **Identify the feature scope:**
   - What is the main functionality? (e.g., user profile editor, shopping cart, task list)
   - What data needs to be displayed or modified?
   - What user interactions are required?
   - What business logic is involved?

2. **Define your data model:**
   - List all properties and their types
   - Identify relationships between entities
   - Determine which data is persistent vs. transient
   - Note any validation rules or constraints

3. **Outline the view requirements:**
   - What UI elements are needed? (lists, forms, buttons, images)
   - What states must be handled? (loading, empty, error, success)
   - Are there navigation requirements?
   - What accessibility features are needed?

**Example Feature Definition:**
```
Feature: Book Reading List Manager
- Display a list of books with title, author, cover image, reading status
- Add new books via a form (title, author, ISBN)
- Mark books as "reading," "completed," or "want to read"
- Filter books by reading status
- Search books by title or author
- Persist data locally using SwiftData
```

### Step 2: Open the Coding Assistant in Xcode

Access the AI coding assistant to begin implementation:

1. **Open your Xcode project** (Xcode 26.1 or later)
2. **Navigate to the file location** where you want to create your feature
3. **Open the Coding Assistant panel:**
   - Press **Cmd + Shift + A** to open the Assistant, or
   - Select **Editor > Coding Tools > Open Assistant** from the menu
4. **Select your preferred AI model:**
   - Click the model dropdown in the Assistant panel
   - Choose **ChatGPT (GPT-5)** for general MVVM implementations, or
   - Choose **Claude Sonnet 4** for complex features with extensive context
5. **Verify connection:** Ensure the model shows as "Connected" or "Ready"

### Step 3: Craft an Effective MVVM Implementation Prompt

Write a detailed prompt that specifies all aspects of the MVVM implementation. Use this proven prompt structure:

#### Recommended Prompt Template:

```
I want to implement a [feature name] feature using MVVM architecture in SwiftUI.

ARCHITECTURE REQUIREMENTS:
- Use MVVM pattern with clear separation of concerns
- ViewModel should use @Observable (Swift 6, iOS 17+, not @Published)
- Follow modern SwiftUI 2025 best practices
- Use async/await for asynchronous operations
- Implement proper error handling and loading states

FEATURE SPECIFICATION:
- Model: [Describe data structure with properties and types]
- View: [Describe UI components, layout, and interactions]
- ViewModel: [Describe business logic, state management, and operations]

SPECIFIC REQUIREMENTS:
- [List any validation rules]
- [List any navigation requirements]
- [List any data persistence needs]
- [List any networking requirements]

ADDITIONAL CONSTRAINTS:
- Target: iOS 26.1+, Swift 6.2.1
- Include proper documentation comments
- Add #Preview with sample data
- Make the ViewModel testable with dependency injection
- Handle edge cases: empty states, errors, loading states

Please generate:
1. The Model struct/class
2. The ViewModel with @Observable
3. The SwiftUI View
4. A #Preview with realistic sample data
```

#### Example Concrete Prompt:

```
I want to implement a Book Reading List feature using MVVM architecture in SwiftUI.

ARCHITECTURE REQUIREMENTS:
- Use MVVM pattern with clear separation of concerns
- ViewModel should use @Observable (Swift 6, iOS 17+, not @Published)
- Follow modern SwiftUI 2025 best practices
- Use async/await for asynchronous operations
- Implement proper error handling and loading states

FEATURE SPECIFICATION:
- Model: Book with properties: id (UUID), title (String), author (String),
  isbn (String), readingStatus (enum: reading, completed, wantToRead),
  dateAdded (Date), coverImageURL (URL?)
- View: List of books with search bar, filter buttons for status,
  add button to show form sheet, swipe actions to delete and change status
- ViewModel: Manages array of books, handles add/delete/update operations,
  filters books by status and search text, loads/saves data

SPECIFIC REQUIREMENTS:
- Validate ISBN format (13 digits)
- Search should filter by title or author (case-insensitive)
- Filter buttons show count of books in each status
- Use SF Symbols for icons (book, checkmark, bookmark)
- Show empty state with message when no books match filters

ADDITIONAL CONSTRAINTS:
- Target: iOS 26.1+, Swift 6.2.1
- Include proper documentation comments
- Add #Preview with sample data showing different reading states
- Make the ViewModel testable with protocol-based dependency injection
- Handle edge cases: empty states, errors, loading states
- Use .searchable modifier for search functionality

Please generate:
1. The Book model with ReadingStatus enum
2. The BookListViewModel with @Observable
3. The BookListView with search and filters
4. A #Preview with realistic sample data
```

### Step 4: Review the Generated MVVM Code

The AI will generate code for all MVVM components. Carefully review each part:

#### 1. Review the Model

Check that the Model:
- Contains all required properties with correct types
- Uses appropriate Swift types (structs for value types, classes if needed)
- Conforms to necessary protocols (Identifiable, Codable, Hashable, Sendable)
- Includes any nested types (enums, structs)
- Has sensible default values or initializers

**Example Expected Model:**
```swift
import Foundation

struct Book: Identifiable, Codable, Hashable {
    let id: UUID
    var title: String
    var author: String
    var isbn: String
    var readingStatus: ReadingStatus
    var dateAdded: Date
    var coverImageURL: URL?

    enum ReadingStatus: String, Codable, CaseIterable {
        case reading = "Reading"
        case completed = "Completed"
        case wantToRead = "Want to Read"
    }
}
```

#### 2. Review the ViewModel

Verify the ViewModel:
- Uses **@Observable macro** (not ObservableObject or @Published)
- Contains @Published or regular properties for state (with @Observable, properties are automatically observed)
- Implements business logic methods clearly
- Uses async/await for asynchronous operations
- Has proper error handling with Result or throws
- Includes computed properties for derived state (filtered data)
- Uses dependency injection for testability
- Follows single responsibility principle

**Example Expected ViewModel:**
```swift
import Foundation
import Observation

@Observable
final class BookListViewModel {
    // State properties
    var books: [Book] = []
    var searchText: String = ""
    var selectedStatus: Book.ReadingStatus?
    var isLoading: Bool = false
    var errorMessage: String?

    // Computed properties
    var filteredBooks: [Book] {
        var filtered = books

        // Filter by status
        if let status = selectedStatus {
            filtered = filtered.filter { $0.readingStatus == status }
        }

        // Filter by search text
        if !searchText.isEmpty {
            filtered = filtered.filter { book in
                book.title.localizedCaseInsensitiveContains(searchText) ||
                book.author.localizedCaseInsensitiveContains(searchText)
            }
        }

        return filtered.sorted { $0.dateAdded > $1.dateAdded }
    }

    var statusCounts: [Book.ReadingStatus: Int] {
        Dictionary(grouping: books, by: \.readingStatus)
            .mapValues { $0.count }
    }

    // Business logic methods
    func addBook(_ book: Book) {
        books.append(book)
        saveBooks()
    }

    func deleteBook(_ book: Book) {
        books.removeAll { $0.id == book.id }
        saveBooks()
    }

    func updateBookStatus(_ book: Book, status: Book.ReadingStatus) {
        if let index = books.firstIndex(where: { $0.id == book.id }) {
            books[index].readingStatus = status
            saveBooks()
        }
    }

    func validateISBN(_ isbn: String) -> Bool {
        let digits = isbn.filter { $0.isNumber }
        return digits.count == 13
    }

    // Data persistence
    private func saveBooks() {
        // Implementation for saving (UserDefaults, SwiftData, etc.)
    }

    func loadBooks() async {
        isLoading = true
        defer { isLoading = false }

        // Implementation for loading
    }
}
```

#### 3. Review the View

Ensure the View:
- Uses **@State** to hold the ViewModel instance
- Properly binds to ViewModel properties using $ syntax when needed
- Has clean, readable SwiftUI layout code
- Implements all required UI components
- Uses appropriate SwiftUI modifiers (.searchable, .toolbar, .sheet, etc.)
- Handles different states (loading, empty, error)
- Follows SwiftUI best practices for composition
- Includes accessibility labels and hints

**Key View Pattern:**
```swift
import SwiftUI

struct BookListView: View {
    @State private var viewModel = BookListViewModel()
    @State private var showingAddBook = false

    var body: some View {
        NavigationStack {
            // View implementation
        }
        .searchable(text: $viewModel.searchText, prompt: "Search books")
    }
}
```

#### 4. Review the Preview

Check that the Preview:
- Includes realistic sample data
- Demonstrates different states (populated, empty, various statuses)
- Shows the view in a useful context
- Uses multiple preview variants if helpful

### Step 5: Request Refinements and Improvements

Use follow-up prompts to refine the implementation:

#### Refinement Prompt Examples:

**Add data persistence:**
```
Update the BookListViewModel to use SwiftData for persistence instead of
in-memory storage. Create the SwiftData model and update the ViewModel to
use @Query and ModelContext.
```

**Improve error handling:**
```
Add comprehensive error handling to the ViewModel. Create a custom Error
enum for book-related errors (invalidISBN, duplicateBook, saveFailed).
Show errors in an alert on the view.
```

**Add unit tests:**
```
Generate XCTest unit tests for BookListViewModel covering:
- Adding a book successfully
- Validating ISBN (valid and invalid cases)
- Filtering books by status and search text
- Computing status counts correctly
```

**Enhance the UI:**
```
Improve the BookListView design:
- Add book cover images with AsyncImage
- Use cards instead of plain list rows
- Add a floating action button for adding books
- Include smooth animations for list updates
- Add pull-to-refresh for reloading data
```

**Add navigation:**
```
Create a BookDetailView that shows full book information. Update
BookListView to navigate to the detail view when tapping a book.
Include edit and delete actions in the detail view.
```

### Step 6: Integrate Generated Code into Your Project

After reviewing and refining:

1. **Create new Swift files** for each component:
   - `Book.swift` for the Model
   - `BookListViewModel.swift` for the ViewModel
   - `BookListView.swift` for the View

2. **Copy the generated code** into appropriate files

3. **Organize in your project structure:**
   ```
   YourApp/
   ├── Models/
   │   └── Book.swift
   ├── ViewModels/
   │   └── BookListViewModel.swift
   └── Views/
       └── BookListView.swift
   ```

4. **Build the project** (Cmd + B) to check for compile errors

5. **Fix any integration issues:**
   - Import statements
   - Access control (public/internal/private)
   - Dependencies on other parts of your app

### Step 7: Test the MVVM Implementation

Thoroughly test the implementation:

1. **Test the View in Xcode Preview:**
   - Open `BookListView.swift`
   - Enable Canvas (Cmd + Option + Return)
   - Verify the preview renders correctly
   - Test interactions in the live preview

2. **Run on Simulator:**
   - Press Cmd + R to run the app
   - Test all user interactions
   - Verify state changes update the UI
   - Test edge cases (empty state, long text, etc.)

3. **Run Unit Tests** (if generated):
   - Press Cmd + U to run tests
   - Verify all tests pass
   - Check test coverage

4. **Test on Physical Device:**
   - Run on an actual iPhone/iPad
   - Test performance with real data
   - Verify accessibility features work

### Step 8: Request Documentation and Best Practices

Ask the AI to document the implementation:

```
Add comprehensive documentation to the BookListViewModel:
- Class-level doc comment explaining the ViewModel's role
- Method doc comments with parameter descriptions and return values
- Inline comments for complex logic
- Usage examples in the doc comments
- Thread-safety notes for async operations
```

```
Review the MVVM implementation and suggest improvements for:
- Code organization and structure
- Performance optimization opportunities
- Better error handling approaches
- Testability improvements
- Accessibility enhancements
```

## Expected Results

After completing these steps, you should have:

1. ✅ **Complete MVVM Implementation:**
   - Clean, well-structured Model with proper types and protocols
   - Observable ViewModel with business logic and state management
   - SwiftUI View that binds to ViewModel properties
   - Clear separation of concerns across layers

2. ✅ **Modern Swift 6 Code:**
   - Uses @Observable macro instead of deprecated @Published patterns
   - Implements async/await for asynchronous operations
   - Follows Swift 6.2.1 concurrency best practices
   - Includes Sendable conformance where appropriate

3. ✅ **Working, Testable Code:**
   - Compiles without errors or warnings
   - Runs correctly in simulator and on device
   - ViewModel is testable through dependency injection
   - Includes unit tests (if requested)

4. ✅ **Professional Code Quality:**
   - Follows iOS and SwiftUI best practices
   - Includes documentation comments
   - Handles edge cases and errors gracefully
   - Implements proper state management

5. ✅ **Functional UI:**
   - All user interactions work as expected
   - State changes reflect in the UI immediately
   - Previews render correctly with sample data
   - Accessible to users with disabilities

## Troubleshooting

### Issue 1: AI Uses Deprecated @Published Instead of @Observable

**Problem:** Generated ViewModel uses `@Published` properties and `ObservableObject` protocol instead of modern `@Observable` macro.

**Solution:**
- Explicitly specify in your prompt: "Use @Observable macro from Swift 6, NOT @Published or ObservableObject"
- Add version context: "Target iOS 17+ with Swift 6.2.1 and Xcode 26.1+"
- If it still generates old patterns, correct it:
  ```
  The ViewModel uses deprecated ObservableObject. Please rewrite it using
  the @Observable macro from the Observation framework (iOS 17+). Remove
  @Published and make properties regular var declarations.
  ```
- Reference modern documentation: "Follow Apple's 2025 guidelines for SwiftUI ViewModels with @Observable"

### Issue 2: Generated MVVM Has Poor Separation of Concerns

**Problem:** ViewModel contains UI logic, or View contains business logic—not properly separated.

**Solution:**
- Clarify responsibilities in your prompt:
  ```
  Ensure strict separation of concerns:
  - Model: Only data structures, no logic
  - ViewModel: All business logic, validation, data operations, state
  - View: Only UI layout and user interaction handlers, no business logic
  ```
- Ask AI to review and refactor:
  ```
  Review the implementation and move any business logic from the View
  to the ViewModel. The View should only handle layout and call ViewModel
  methods for actions.
  ```
- Provide specific examples of violations you see

### Issue 3: ViewModel Is Not Testable

**Problem:** ViewModel has hard-coded dependencies, making unit testing difficult.

**Solution:**
- Request dependency injection:
  ```
  Refactor the ViewModel to use protocol-based dependency injection.
  Create protocols for data persistence and networking services.
  Inject these dependencies via the initializer so they can be mocked
  in tests.
  ```
- Ask for a testable example:
  ```
  Show me how to make BookListViewModel testable. Create a DataService
  protocol, update the ViewModel to accept it as a dependency, and
  provide an example unit test with a mock service.
  ```

### Issue 4: State Management Doesn't Work Correctly

**Problem:** UI doesn't update when ViewModel properties change, or updates happen unexpectedly.

**Solution:**
- Verify @Observable is properly applied:
  - Check that the ViewModel class has `@Observable` macro
  - Ensure the View holds the ViewModel with `@State`
  - Check that you're not accidentally creating new ViewModel instances
- For bindings, verify you're using `$viewModel.property` syntax
- Ask AI to debug:
  ```
  The UI doesn't update when I add a book. Debug the state management
  in BookListViewModel and BookListView. Ensure @Observable is correctly
  implemented and the View properly observes changes.
  ```

### Issue 5: Generated Code Is Too Complex

**Problem:** AI generates overly complex code with unnecessary abstractions or patterns.

**Solution:**
- Request simplification:
  ```
  Simplify the implementation. Remove unnecessary abstractions and keep
  the code straightforward and easy to understand. Follow the principle
  of "simple as possible, but no simpler."
  ```
- Be specific about complexity concerns:
  ```
  The ViewModel has too many nested types. Extract them into separate
  files and simplify the structure.
  ```
- Start simpler and add complexity incrementally:
  ```
  First, create a minimal MVVM implementation with just the core
  functionality. We'll add advanced features later.
  ```

### Issue 6: AI Mixes UIKit and SwiftUI Patterns

**Problem:** Generated code uses UIKit patterns (delegates, NSNotificationCenter) in a SwiftUI context.

**Solution:**
- Clarify the tech stack upfront:
  ```
  This is a pure SwiftUI app (no UIKit). Use only SwiftUI patterns:
  @Observable for state, @State/@Binding for view state, .onChange
  modifiers for observation, and SwiftUI navigation APIs.
  ```
- Ask for a SwiftUI-native rewrite:
  ```
  Remove all UIKit patterns (NotificationCenter, delegates) and replace
  them with SwiftUI-native approaches using @Observable, Combine, or
  async/await.
  ```

## Additional Tips

### Choosing Between ChatGPT and Claude for MVVM

**Use ChatGPT (GPT-5):**
- Quick, straightforward MVVM implementations
- Well-known patterns with standard features
- Learning MVVM concepts with explanations
- Generating boilerplate MVVM code rapidly

**Use Claude (Sonnet 4):**
- Complex features with multiple related ViewModels
- Large-scale refactoring to MVVM architecture
- Analyzing existing code and proposing MVVM restructuring
- Features requiring extensive context from your codebase

### Best Practices for MVVM Prompts

1. **Be specific about iOS/Swift versions:**
   - Always mention "iOS 26.1+, Swift 6.2.1, Xcode 26.1+"
   - Specify "@Observable, not @Published" to avoid deprecated patterns

2. **Define clear boundaries:**
   - Explicitly state what belongs in Model, View, and ViewModel
   - Mention that View should be "dumb" (no business logic)
   - Specify that ViewModel should have no UIKit/SwiftUI imports

3. **Request testability upfront:**
   - Ask for protocol-based dependencies from the start
   - Request that async operations be testable
   - Mention "dependency injection" in initial prompt

4. **Include edge cases:**
   - Specify loading, empty, and error states
   - Mention validation requirements
   - Define what happens with network failures or bad data

5. **Ask for modern patterns:**
   - Use "async/await, not completion handlers"
   - Request "structured concurrency with Swift 6"
   - Specify "SwiftData" or "Core Data" for persistence explicitly

### Iterative Refinement Strategy

Don't expect perfection on the first generation. Use this iterative approach:

1. **First prompt:** Get basic MVVM structure (Model, ViewModel, View)
2. **Second prompt:** Add specific business logic and validation
3. **Third prompt:** Implement data persistence
4. **Fourth prompt:** Add advanced UI features
5. **Fifth prompt:** Generate unit tests
6. **Sixth prompt:** Add documentation and polish

This staged approach produces better results than one massive prompt.

### MVVM Anti-Patterns to Watch For

Ask AI to avoid these common mistakes:

- **Massive ViewModels:** If a ViewModel has >300 lines, ask to split it
- **View logic in ViewModel:** ViewModels shouldn't know about Colors, Fonts, or layout
- **Tight coupling:** Model should never reference ViewModel or View
- **God objects:** Don't let one ViewModel manage unrelated features
- **Premature optimization:** Request simple code first, optimize later

### Documentation Prompts for MVVM

```
Document the MVVM architecture for this feature in a README format:
- Explain the role of each component
- Show the data flow diagram (in text/markdown)
- Provide usage examples
- List key design decisions and trade-offs
```

### Accessibility and Localization

```
Enhance the BookListView for accessibility and localization:
- Add .accessibilityLabel and .accessibilityHint to interactive elements
- Use .accessibilityValue for dynamic content
- Extract all user-facing strings to Localizable.strings
- Test with VoiceOver and Dynamic Type
```

### Performance Optimization

For large datasets:

```
Optimize BookListViewModel for performance with large book collections:
- Implement pagination for loading books in batches
- Use lazy evaluation for filtered results
- Add debouncing for search text (wait 300ms before filtering)
- Cache expensive computed properties
```

## Related Articles

- KB-037: How to generate SwiftUI views from natural language descriptions using ChatGPT
- KB-038: How to ask ChatGPT to explain selected code in your project
- KB-047: How to use Claude for complex refactoring tasks
- KB-049: How to ask Claude to analyze architecture patterns in your codebase
- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT integration
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant

## Sources

- Apple Developer - Writing code with intelligence in Xcode - https://developer.apple.com/documentation/xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- Medium - Modern MVVM in SwiftUI 2025: The Clean Architecture You've Been Waiting For - https://medium.com/@minalkewat/modern-mvvm-in-swiftui-2025-the-clean-architecture-youve-been-waiting-for-72a7d576648e (Accessed: November 17, 2025)
- FlyingHarley.dev - MVVM Architecture in SwiftUI: From ObservableObject to @Observable - https://flyingharley.dev/posts/mvvm-architecture-in-swift-ui-from-observable-object-to-observable (Accessed: November 17, 2025)
- Matteo Manferdini - MVVM in SwiftUI for a Better Architecture - https://matteomanferdini.com/mvvm-swiftui/ (Accessed: November 17, 2025)
- Kodeco - SwiftUI Cookbook: Implement MVVM Architecture in SwiftUI - https://www.kodeco.com/books/swiftui-cookbook/v1.0/chapters/10-implement-mvvm-architecture-in-swiftui (Accessed: November 17, 2025)
- Swift Anytime - Complete Guide to MVVM in SwiftUI - https://www.swiftanytime.com/blog/mvvm-in-swiftui (Accessed: November 17, 2025)
- zthh.dev - Master SwiftUI with MVVM: Best Practices, Tips, and Advanced Techniques - https://zthh.dev/blogs/swiftui-mvvm-best-practices-tips-techniques (Accessed: November 17, 2025)
- Medium - Code with Intelligence — xCode 26 - https://medium.com/@sachinsiwal/code-with-intelligence-xcode-26-9203a8ce665e (Accessed: November 17, 2025)
- Swift with Vincent - Discover 5 new AI features of Xcode 26 - https://www.swiftwithvincent.com/blog/discover-5-new-ai-features-of-xcode-26 (Accessed: November 17, 2025)
- TechCrunch - Apple brings ChatGPT and other AI models to Xcode - https://techcrunch.com/2025/06/09/apple-brings-chatgpt-and-other-ai-models-to-xcode/ (Accessed: November 17, 2025)
- Medium - From Red Builds to Release: How I Ship iOS Features 2× Faster with Claude in Xcode 26 - https://alirezarezvani.medium.com/from-red-builds-to-release-how-i-ship-ios-features-2-faster-with-claude-in-xcode-26-204265c7919c (Accessed: November 17, 2025)
- GitHub - Xcode 26 System Prompts - https://github.com/artemnovichkov/xcode-26-system-prompts (Accessed: November 17, 2025)
- Peter Friese - Reverse-Engineering Xcode's Coding Intelligence prompt - https://peterfriese.dev/blog/2025/reveng-xcode-coding-intelligence/ (Accessed: November 17, 2025)
- Medium - Developing in Xcode with AI: Comparing Copilot, Claude, MCP, Cursor, and More - https://medium.com/@avilevin23/developing-in-xcode-with-ai-comparing-copilot-claude-mcp-cursor-and-more-97a42254506c (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
