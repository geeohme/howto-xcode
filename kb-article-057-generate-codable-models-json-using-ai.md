# How to generate Codable models from JSON using AI

**Article ID:** KB-057
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 15-25 minutes

## Overview

Xcode 26.1+ includes integrated AI coding assistants (ChatGPT and Claude) that can automatically generate Swift Codable models from JSON data. Instead of manually creating structs, defining properties, and writing CodingKeys enums, you can paste JSON into the Coding Assistant and receive production-ready, type-safe Swift models in seconds. This guide shows you how to leverage AI to streamline API integration, reduce errors, and follow Swift best practices when working with JSON data.

## Prerequisites

- Xcode 26.1 or later installed
- macOS 15.0 or later (macOS 26 Tahoe recommended for full Coding Intelligence features)
- ChatGPT configured in Xcode Coding Assistant (see KB-031, KB-032)
- Optional: Claude Sonnet 4 configured for more complex scenarios (see KB-043 through KB-046)
- A sample JSON response from your API or data source
- Basic understanding of Swift structs and protocols
- Active internet connection for AI services

## Steps

### Step 1: Obtain Your JSON Sample Data

Before generating Codable models, you need a representative JSON sample:

1. **From an API endpoint:** Use a tool like Postman, curl, or your browser to fetch a real JSON response
2. **From documentation:** Copy the example JSON from your API documentation
3. **From a file:** Load JSON from a local .json file in your project

**Important considerations:**
- Use a complete, real-world example that includes all fields you'll need
- Ensure the JSON is valid (use a JSON validator like jsonlint.com if unsure)
- Include nested objects and arrays if your data structure contains them
- Choose a sample that represents edge cases (optional fields, null values, varied data types)

**Example JSON (User API Response):**
```json
{
  "id": 12345,
  "username": "johndoe",
  "email": "john@example.com",
  "full_name": "John Doe",
  "profile_image_url": "https://example.com/avatar.jpg",
  "is_verified": true,
  "follower_count": 1250,
  "created_at": "2024-03-15T10:30:00Z",
  "settings": {
    "notifications_enabled": true,
    "theme": "dark"
  },
  "recent_posts": [
    {
      "post_id": 789,
      "title": "My First Post",
      "published": true
    }
  ]
}
```

### Step 2: Open the Coding Assistant in Xcode

Access Xcode's AI-powered Coding Assistant:

1. Open your Xcode project where you want to add the Codable models
2. Navigate to **Editor > Show Coding Assistant** (or press `Cmd + Shift + A` if you've configured the shortcut)
3. The **Coding Assistant** panel will appear on the right side of the Xcode window
4. Verify that **ChatGPT** is selected in the model dropdown at the top (GPT-5 is recommended for this task)

**Alternative:** If you prefer Claude for more detailed, production-ready models with comprehensive error handling, select **Anthropic - Claude Sonnet 4** from the dropdown.

### Step 3: Prepare Your AI Prompt

Create a clear, specific prompt that tells the AI exactly what you need:

**Basic prompt template:**
```
Generate Swift Codable models from this JSON response:

[PASTE YOUR JSON HERE]

Requirements:
- Use structs (not classes)
- Follow Swift naming conventions (camelCase for properties)
- Map snake_case JSON keys to camelCase Swift properties using CodingKeys
- Make optional fields optional (use String? instead of String where appropriate)
- Use appropriate Swift types (Int, String, Bool, Double, Date, URL)
- Include nested types for complex objects
- Add brief comments for clarity
```

**Enhanced prompt for production code:**
```
Generate production-ready Swift Codable models from this JSON API response:

[PASTE YOUR JSON HERE]

Requirements:
- Use structs conforming to Codable protocol
- Follow Swift 6 best practices and naming conventions
- Map snake_case JSON keys to camelCase Swift properties with CodingKeys enum
- Make appropriate fields optional based on API contract
- Use proper types: URL for URLs, Date for timestamps (ISO8601), Int/Double for numbers
- Create nested structs for complex objects
- Include struct and property documentation comments
- Add error handling guidance for decoding
- Ensure thread-safety with Sendable conformance where appropriate
```

### Step 4: Submit Your Prompt to the AI Assistant

Send your request to the AI model:

1. **Paste your prepared prompt** into the Coding Assistant text input field
2. **Paste your JSON sample** where indicated in the prompt
3. **Review the prompt** to ensure the JSON is complete and properly formatted
4. Press **Enter** or click the **Send** button
5. Wait for the AI to generate the response (typically 10-30 seconds for ChatGPT, 30-60 seconds for Claude)

**Pro Tip:** Start a new conversation (`Cmd + N` in the Coding Assistant) to ensure clean context if you've been working on other tasks.

### Step 5: Review the Generated Codable Models

The AI will generate Swift code similar to this:

```swift
/// Represents a user profile from the API
struct UserResponse: Codable, Sendable {
    /// Unique identifier for the user
    let id: Int

    /// User's chosen username
    let username: String

    /// User's email address
    let email: String

    /// User's full display name
    let fullName: String

    /// URL to the user's profile image
    let profileImageURL: URL?

    /// Indicates if the user account is verified
    let isVerified: Bool

    /// Number of followers
    let followerCount: Int

    /// Account creation timestamp
    let createdAt: Date

    /// User's preference settings
    let settings: UserSettings

    /// Array of recent posts
    let recentPosts: [Post]

    enum CodingKeys: String, CodingKey {
        case id
        case username
        case email
        case fullName = "full_name"
        case profileImageURL = "profile_image_url"
        case isVerified = "is_verified"
        case followerCount = "follower_count"
        case createdAt = "created_at"
        case settings
        case recentPosts = "recent_posts"
    }
}

/// User preference settings
struct UserSettings: Codable, Sendable {
    /// Whether notifications are enabled
    let notificationsEnabled: Bool

    /// Selected theme preference
    let theme: String

    enum CodingKeys: String, CodingKey {
        case notificationsEnabled = "notifications_enabled"
        case theme
    }
}

/// Represents a user post
struct Post: Codable, Sendable {
    /// Unique post identifier
    let postId: Int

    /// Post title
    let title: String

    /// Publication status
    let published: Bool

    enum CodingKeys: String, CodingKey {
        case postId = "post_id"
        case title
        case published
    }
}
```

**What to check:**
- ✅ Property names use camelCase (Swift convention)
- ✅ JSON keys are mapped correctly via CodingKeys enum
- ✅ Types are appropriate (Int, String, Bool, URL, Date, etc.)
- ✅ Optional fields use `?` where data might be missing
- ✅ Nested objects have their own struct definitions
- ✅ Arrays are properly typed (e.g., `[Post]`)
- ✅ Documentation comments are included

### Step 6: Customize the Generated Models

Refine the AI-generated code for your specific needs:

#### A. Adjust Optionality

Make fields optional if they might be missing from the API:

```swift
// If profile_image_url might be null or missing:
let profileImageURL: URL?

// If follower_count is always present:
let followerCount: Int  // Not optional
```

**Ask the AI for help:**
```
Make the following fields optional in the UserResponse model: profileImageURL, settings. Update the CodingKeys accordingly.
```

#### B. Add Custom Date Decoding

If your API uses non-standard date formats:

```swift
struct UserResponse: Codable {
    let createdAt: Date

    // Custom date decoder
    static let dateFormatter: DateFormatter = {
        let formatter = DateFormatter()
        formatter.dateFormat = "yyyy-MM-dd'T'HH:mm:ssZ"
        formatter.locale = Locale(identifier: "en_US_POSIX")
        formatter.timeZone = TimeZone(secondsFromGMT: 0)
        return formatter
    }()
}
```

**Prompt for AI:**
```
Add a custom JSONDecoder configuration to handle the ISO8601 date format for createdAt field. Include the decoder setup code.
```

#### C. Add Computed Properties

Enhance your models with convenience properties:

```swift
struct UserResponse: Codable {
    // ... existing properties ...

    /// Full display string for user
    var displayName: String {
        fullName.isEmpty ? username : fullName
    }

    /// Checks if user is a popular account
    var isPopular: Bool {
        followerCount > 1000
    }
}
```

#### D. Add Default Values with Custom Decoding

For robust error handling:

```swift
struct UserSettings: Codable {
    let notificationsEnabled: Bool
    let theme: String

    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        self.notificationsEnabled = try container.decodeIfPresent(Bool.self, forKey: .notificationsEnabled) ?? true
        self.theme = try container.decodeIfPresent(String.self, forKey: .theme) ?? "light"
    }
}
```

**Ask AI to add this:**
```
Add custom init(from decoder:) to UserSettings with default values: notificationsEnabled defaults to true, theme defaults to "light" if missing.
```

### Step 7: Create the Model File in Your Project

Add the generated models to your Xcode project:

1. **Create a new Swift file:**
   - Right-click your project's **Models** folder (or create one if it doesn't exist)
   - Select **New File...** → **Swift File**
   - Name it appropriately: `UserModels.swift` or `APIModels.swift`

2. **Copy the AI-generated code:**
   - Select all the generated code from the Coding Assistant
   - Press `Cmd + C` to copy
   - Paste into your new Swift file (`Cmd + V`)

3. **Organize your models:**
   - Group related models together in the same file
   - Use `// MARK: -` comments for organization:
   ```swift
   // MARK: - User Models
   struct UserResponse: Codable { ... }

   // MARK: - Settings Models
   struct UserSettings: Codable { ... }

   // MARK: - Post Models
   struct Post: Codable { ... }
   ```

4. **Build your project** (`Cmd + B`) to check for any compilation errors

### Step 8: Test Decoding with Your JSON Data

Verify that your models correctly decode the JSON:

#### A. Create a Test Decoder Function

Add this test code temporarily to verify decoding:

```swift
func testUserDecoding() {
    let jsonString = """
    {
      "id": 12345,
      "username": "johndoe",
      "email": "john@example.com",
      "full_name": "John Doe",
      "profile_image_url": "https://example.com/avatar.jpg",
      "is_verified": true,
      "follower_count": 1250,
      "created_at": "2024-03-15T10:30:00Z",
      "settings": {
        "notifications_enabled": true,
        "theme": "dark"
      },
      "recent_posts": [
        {
          "post_id": 789,
          "title": "My First Post",
          "published": true
        }
      ]
    }
    """

    let jsonData = jsonString.data(using: .utf8)!

    do {
        let decoder = JSONDecoder()
        decoder.dateDecodingStrategy = .iso8601
        decoder.keyDecodingStrategy = .useDefaultKeys

        let user = try decoder.decode(UserResponse.self, from: jsonData)
        print("Successfully decoded user: \(user.username)")
        print("Email: \(user.email)")
        print("Followers: \(user.followerCount)")
        print("Settings theme: \(user.settings.theme)")
    } catch {
        print("Decoding error: \(error)")
        if let decodingError = error as? DecodingError {
            switch decodingError {
            case .keyNotFound(let key, let context):
                print("Missing key '\(key.stringValue)' - \(context.debugDescription)")
            case .typeMismatch(let type, let context):
                print("Type mismatch for type '\(type)' - \(context.debugDescription)")
            case .valueNotFound(let type, let context):
                print("Missing value for type '\(type)' - \(context.debugDescription)")
            case .dataCorrupted(let context):
                print("Data corrupted - \(context.debugDescription)")
            @unknown default:
                print("Unknown decoding error")
            }
        }
    }
}
```

#### B. Use AI to Generate Test Cases

**Prompt:**
```
Generate XCTest unit tests for the UserResponse Codable model, including:
- Test successful decoding with complete JSON
- Test decoding with missing optional fields
- Test decoding failure with invalid data types
- Test encoding back to JSON
```

#### C. Integration with Network Layer

Example of using the models with URLSession:

```swift
func fetchUser(id: Int) async throws -> UserResponse {
    let url = URL(string: "https://api.example.com/users/\(id)")!
    let (data, _) = try await URLSession.shared.data(from: url)

    let decoder = JSONDecoder()
    decoder.dateDecodingStrategy = .iso8601

    return try decoder.decode(UserResponse.self, from: data)
}
```

**Ask AI to generate this:**
```
Create an async function using URLSession and async/await to fetch and decode the UserResponse model from an API endpoint. Include error handling for network and decoding errors.
```

### Step 9: Handle Complex Scenarios with AI Assistance

For advanced use cases, ask the AI for help:

#### A. Nested Arrays of Objects

**Prompt:**
```
Update the UserResponse model to handle a nested "addresses" array where each address has street, city, state, and zipCode fields. Generate the complete Codable structs.
```

#### B. Polymorphic JSON (Different Types)

**Prompt:**
```
Generate Codable models for a JSON response that can contain different post types (text posts, image posts, video posts) using a "type" discriminator field. Use Swift enums with associated values.
```

#### C. Custom Transformations

**Prompt:**
```
Add custom decoding logic to convert a JSON string "rgb(255, 0, 0)" to a Swift tuple (red: Int, green: Int, blue: Int) in the UserSettings theme property.
```

#### D. Error Recovery

**Prompt:**
```
Modify the Post struct to gracefully handle decoding errors for individual posts in an array, logging failures but continuing with valid posts instead of failing the entire array.
```

### Step 10: Refine with Follow-Up AI Prompts

Iterate on the generated code for production quality:

**Code quality improvements:**
```
Review the generated Codable models and suggest improvements for:
- Thread safety with Sendable conformance
- Immutability using let vs var
- Access control (public, internal, private)
- Documentation completeness
```

**Performance optimization:**
```
Are there any performance concerns with these Codable models for decoding large JSON arrays (10,000+ items)? Suggest optimizations.
```

**Validation logic:**
```
Add validation to ensure email addresses are valid format, URLs are properly formed, and followerCount is non-negative. Where should this validation go?
```

## Expected Results

After following these steps, you should have:

- **Production-ready Swift Codable models** that accurately represent your JSON structure
- **Properly typed properties** using Swift's type system (Int, String, Bool, URL, Date, etc.)
- **CodingKeys enums** that correctly map snake_case JSON keys to camelCase Swift properties
- **Nested structs** for complex JSON objects
- **Optional properties** for fields that might be missing from the API
- **Documentation comments** explaining each model and property
- **Working decoder configuration** for your specific JSON format (dates, keys, etc.)
- **Test cases** validating that your models correctly decode real JSON data

**Time savings:**
- **Manual approach:** 15-45 minutes for complex JSON structures
- **AI-assisted approach:** 2-5 minutes with review and customization
- **Error reduction:** AI catches common mistakes like incorrect CodingKeys mappings

## Troubleshooting

### Issue 1: Decoding Fails with "keyNotFound" Error

**Problem:** JSONDecoder throws an error: `keyNotFound(CodingKeys(stringValue: "full_name"))`

**Solutions:**

1. **Check JSON key spelling:**
   - Verify the JSON actually contains `full_name` (not `fullName` or `Full_Name`)
   - Use a JSON formatter to view the actual keys

2. **Make the property optional:**
   ```swift
   let fullName: String?  // Add ? to make optional
   ```

3. **Use decodeIfPresent for optional fields:**
   ```swift
   init(from decoder: Decoder) throws {
       let container = try decoder.container(keyedBy: CodingKeys.self)
       fullName = try container.decodeIfPresent(String.self, forKey: .fullName) ?? ""
   }
   ```

4. **Ask AI to fix it:**
   ```
   The decoding fails with keyNotFound for "full_name". Review the JSON and model, and fix the CodingKeys mapping or make the field optional if it's sometimes missing.
   ```

### Issue 2: Type Mismatch Errors

**Problem:** `typeMismatch(Swift.Int, ...): Expected to decode Int but found a string instead`

**Solutions:**

1. **Check actual JSON data types:**
   - The JSON might have `"id": "12345"` (string) instead of `"id": 12345` (number)
   - Update your model to match: `let id: String`

2. **Use custom decoding for flexible types:**
   ```swift
   init(from decoder: Decoder) throws {
       let container = try decoder.container(keyedBy: CodingKeys.self)
       // Try decoding as Int first, fall back to String if needed
       if let intValue = try? container.decode(Int.self, forKey: .id) {
           id = intValue
       } else if let stringValue = try? container.decode(String.self, forKey: .id),
                 let intValue = Int(stringValue) {
           id = intValue
       } else {
           throw DecodingError.typeMismatch(Int.self, DecodingError.Context(
               codingPath: [CodingKeys.id],
               debugDescription: "Could not decode id as Int or String"
           ))
       }
   }
   ```

3. **Ask AI for help:**
   ```
   The API sometimes sends the "id" field as a string "12345" and sometimes as a number 12345. Update the Codable model to handle both cases and always store as Int.
   ```

### Issue 3: Date Parsing Fails

**Problem:** Dates fail to decode with error about date format

**Solutions:**

1. **Configure JSONDecoder date strategy:**
   ```swift
   let decoder = JSONDecoder()
   decoder.dateDecodingStrategy = .iso8601  // For "2024-03-15T10:30:00Z"
   ```

2. **For custom date formats:**
   ```swift
   let decoder = JSONDecoder()
   let formatter = DateFormatter()
   formatter.dateFormat = "yyyy-MM-dd HH:mm:ss"
   formatter.locale = Locale(identifier: "en_US_POSIX")
   decoder.dateDecodingStrategy = .formatted(formatter)
   ```

3. **Decode dates as strings and convert:**
   ```swift
   struct UserResponse: Codable {
       let createdAtString: String

       var createdAt: Date? {
           let formatter = ISO8601DateFormatter()
           return formatter.date(from: createdAtString)
       }

       enum CodingKeys: String, CodingKey {
           case createdAtString = "created_at"
       }
   }
   ```

4. **Ask AI:**
   ```
   The API returns dates in format "15/03/2024 10:30:45". Update the Codable model and decoder configuration to handle this format correctly.
   ```

### Issue 4: AI Generates Incorrect Type Mappings

**Problem:** AI maps a number field to String or makes wrong assumptions about types

**Solutions:**

1. **Be more specific in your prompt:**
   ```
   Generate Codable models from this JSON. Important: "follower_count" should be Int, "profile_image_url" should be URL?, and "created_at" should be Date using ISO8601 format.
   ```

2. **Provide type hints in the prompt:**
   ```
   Map the following fields to these Swift types:
   - id → Int
   - username → String
   - email → String
   - is_verified → Bool
   - profile_image_url → URL?
   - created_at → Date
   ```

3. **Review and manually correct:**
   - Always review AI-generated code
   - Fix type mappings that don't match your API contract
   - Build and test to catch type mismatches early

### Issue 5: Missing Nested Structures

**Problem:** AI generates a flat model instead of nested structs for complex objects

**Solutions:**

1. **Explicitly request nested structures:**
   ```
   Generate Codable models with proper nested structs for the "settings" and "recent_posts" objects. Each nested object should be its own struct.
   ```

2. **Provide complete JSON:**
   - Ensure your sample JSON includes all nested levels
   - Don't truncate nested objects with `...`

3. **Ask for specific nested models:**
   ```
   Create separate Codable structs for: UserResponse (top level), UserSettings (nested in settings), and Post (items in recent_posts array).
   ```

## Additional Tips

### Best Practices for AI-Generated Codable Models

1. **Always review AI output:** Don't blindly trust generated code; verify it matches your API contract
2. **Test with real data:** Use actual API responses, not just sample JSON
3. **Start with complete JSON:** Provide comprehensive samples that include all edge cases
4. **Iterate with follow-ups:** Refine the models by asking the AI to make specific improvements
5. **Document your models:** Ask AI to add documentation comments for clarity
6. **Use version control:** Commit working models before making AI-suggested changes
7. **Validate assumptions:** Check that optional vs required fields match your API documentation

### Choosing Between ChatGPT and Claude

**Use ChatGPT (GPT-5) for:**
- Simple, straightforward JSON structures
- Quick prototyping and initial model generation
- Basic Codable models without complex custom decoding
- Faster responses when time is critical

**Use Claude Sonnet 4 for:**
- Complex nested JSON with multiple levels
- APIs with inconsistent data types or edge cases
- Production-ready code with comprehensive error handling
- Detailed documentation and explanation of design decisions
- Custom decoding logic for non-standard formats

### Effective Prompts for Codable Generation

**Basic prompt:**
```
Generate Swift Codable models from this JSON:
[paste JSON]
```

**Better prompt:**
```
Generate Swift Codable structs from this JSON API response. Use camelCase property names, map snake_case JSON keys with CodingKeys, and make appropriate fields optional.
[paste JSON]
```

**Best prompt:**
```
Generate production-ready Swift 6 Codable models from this user profile API JSON response:

[paste JSON]

Requirements:
- Structs conforming to Codable and Sendable protocols
- Swift naming conventions (camelCase properties)
- CodingKeys enum to map snake_case JSON keys
- Appropriate types: URL for image URLs, Date for timestamps (ISO8601), Int for IDs
- Optional fields where values might be null/missing: profile_image_url, bio
- Nested structs for complex objects (settings, address)
- Documentation comments for each struct and property
- Consider decoding strategy notes (date format, key decoding)

Focus on type safety and production readiness.
```

### Handling Different JSON Patterns

**Array responses:**
```swift
// For JSON: [{"id": 1, "name": "Item 1"}, {"id": 2, "name": "Item 2"}]
struct Item: Codable {
    let id: Int
    let name: String
}

// Decode as:
let items = try decoder.decode([Item].self, from: jsonData)
```

**Wrapped responses:**
```swift
// For JSON: {"status": "success", "data": {"id": 1, "name": "Item"}}
struct APIResponse<T: Codable>: Codable {
    let status: String
    let data: T
}

struct Item: Codable {
    let id: Int
    let name: String
}

// Decode as:
let response = try decoder.decode(APIResponse<Item>.self, from: jsonData)
let item = response.data
```

**Prompt for wrapped responses:**
```
Generate a generic Codable wrapper model for API responses with format {"status": "success", "data": {...}, "message": "OK"}. The data field should be generic to work with any Codable type.
```

### Common JSON to Swift Type Mappings

| JSON Type | Swift Type | Notes |
|-----------|------------|-------|
| `"string"` | `String` | Basic string |
| `123` | `Int` | Integer number |
| `123.45` | `Double` | Floating point |
| `true` / `false` | `Bool` | Boolean |
| `null` | `Type?` | Optional in Swift |
| `"2024-03-15T10:30:00Z"` | `Date` | Use `.iso8601` decoder strategy |
| `"https://example.com"` | `URL` | String representation of URL |
| `[1, 2, 3]` | `[Int]` | Array of integers |
| `{"key": "value"}` | Nested `struct` | Object/dictionary |

### Organizing Models in Your Project

**Recommended structure:**
```
YourProject/
├── Models/
│   ├── User/
│   │   ├── UserModels.swift          // UserResponse, UserSettings
│   │   └── UserProfileModels.swift   // Profile-specific models
│   ├── Post/
│   │   └── PostModels.swift          // Post, Comment models
│   └── Shared/
│       ├── APIResponse.swift         // Generic wrapper models
│       └── DateDecodingStrategies.swift
```

**File naming conventions:**
- `{Feature}Models.swift` for related models
- Group by domain/feature, not by technical type
- Keep related nested structs in the same file

### Advanced Techniques

**1. Decode partial data with default values:**
```swift
struct UserSettings: Codable {
    var notificationsEnabled: Bool = true
    var theme: String = "light"

    enum CodingKeys: String, CodingKey {
        case notificationsEnabled = "notifications_enabled"
        case theme
    }

    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        notificationsEnabled = try container.decodeIfPresent(Bool.self, forKey: .notificationsEnabled) ?? true
        theme = try container.decodeIfPresent(String.self, forKey: .theme) ?? "light"
    }
}
```

**2. Polymorphic JSON decoding:**
```swift
enum PostType: String, Codable {
    case text
    case image
    case video
}

enum Post: Codable {
    case text(TextPost)
    case image(ImagePost)
    case video(VideoPost)

    enum CodingKeys: String, CodingKey {
        case type
    }

    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        let type = try container.decode(PostType.self, forKey: .type)

        switch type {
        case .text:
            self = .text(try TextPost(from: decoder))
        case .image:
            self = .image(try ImagePost(from: decoder))
        case .video:
            self = .video(try VideoPost(from: decoder))
        }
    }
}
```

**Prompt for polymorphic types:**
```
Generate Codable models for polymorphic JSON where a "type" field determines the structure. Types include "text", "image", and "video" posts, each with different fields. Use Swift enums with associated values.
```

**3. Flattening nested JSON:**
```swift
// JSON: {"user": {"profile": {"name": "John", "age": 30}}}
// Want: UserInfo with name and age at top level

struct UserInfo: Codable {
    let name: String
    let age: Int

    enum OuterKeys: String, CodingKey {
        case user
    }

    enum MiddleKeys: String, CodingKey {
        case profile
    }

    enum InnerKeys: String, CodingKey {
        case name, age
    }

    init(from decoder: Decoder) throws {
        let outer = try decoder.container(keyedBy: OuterKeys.self)
        let middle = try outer.nestedContainer(keyedBy: MiddleKeys.self, forKey: .user)
        let inner = try middle.nestedContainer(keyedBy: InnerKeys.self, forKey: .profile)

        name = try inner.decode(String.self, forKey: .name)
        age = try inner.decode(Int.self, forKey: .age)
    }
}
```

### Testing Strategies

**1. Unit tests for Codable models:**
```swift
import XCTest

class UserModelTests: XCTestCase {
    func testUserDecoding() throws {
        let json = """
        {
            "id": 123,
            "username": "testuser",
            "email": "test@example.com"
        }
        """
        let data = json.data(using: .utf8)!
        let user = try JSONDecoder().decode(UserResponse.self, from: data)

        XCTAssertEqual(user.id, 123)
        XCTAssertEqual(user.username, "testuser")
        XCTAssertEqual(user.email, "test@example.com")
    }

    func testUserEncodingDecoding() throws {
        let original = UserResponse(id: 456, username: "encode", email: "test@test.com")
        let encoded = try JSONEncoder().encode(original)
        let decoded = try JSONDecoder().decode(UserResponse.self, from: encoded)

        XCTAssertEqual(original.id, decoded.id)
        XCTAssertEqual(original.username, decoded.username)
    }
}
```

**Ask AI to generate tests:**
```
Generate comprehensive XCTest unit tests for the UserResponse Codable model including: successful decoding, encoding/decoding roundtrip, missing optional fields, invalid data types, and date format validation.
```

**2. Snapshot testing for large JSON:**
- Save API responses as .json files in test bundle
- Load and decode in tests to ensure models stay in sync with API

### Performance Considerations

**For large JSON arrays (10,000+ items):**
1. Use `lazy` sequences where appropriate
2. Consider streaming JSON parsers for extremely large datasets
3. Decode in background threads using `Task { }` or `DispatchQueue`
4. Profile with Instruments to identify bottlenecks

**Memory optimization:**
```swift
// Instead of loading all at once:
let allUsers = try decoder.decode([User].self, from: largeData)

// Process in batches or use streaming:
// (This requires custom implementation or third-party libraries)
```

### Documentation Best Practices

**Ask AI to add comprehensive docs:**
```
Add comprehensive documentation comments to all Codable models following Swift DocC format. Include parameter descriptions, usage examples, and notes about optional fields.
```

**Example well-documented model:**
```swift
/// Represents a user profile from the User API v2.
///
/// This model is used to decode JSON responses from the `/users/:id` endpoint.
/// All properties map from snake_case JSON keys to camelCase Swift properties.
///
/// ## Example JSON
/// ```json
/// {
///   "id": 12345,
///   "username": "johndoe",
///   "email": "john@example.com"
/// }
/// ```
///
/// ## Usage
/// ```swift
/// let decoder = JSONDecoder()
/// let user = try decoder.decode(UserResponse.self, from: jsonData)
/// print(user.username)
/// ```
struct UserResponse: Codable, Sendable {
    /// The unique identifier for this user account.
    ///
    /// This ID is immutable and assigned when the account is created.
    let id: Int

    /// The user's chosen username.
    ///
    /// Usernames must be unique across the platform and are case-insensitive.
    let username: String

    // ... more properties
}
```

## Related Articles

- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT
- KB-032: How to start a new conversation with ChatGPT in Xcode
- KB-037: How to generate SwiftUI views from natural language descriptions using ChatGPT
- KB-038: How to ask ChatGPT to explain selected code in your project
- KB-039: How to use ChatGPT to fix compiler errors and bugs
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-050: How to compare responses between ChatGPT and Claude for the same coding task
- KB-053: How to ask AI to implement MVVM architecture for a feature
- KB-058: How to ask AI to add comprehensive error handling to functions

## Sources

- Apple Developer Documentation: Writing code with Intelligence in Xcode - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- 9to5Mac: Xcode 26 will support multiple AI models, like Claude - https://9to5mac.com/2025/06/10/beyond-chatgpt-xcode-26-will-support-multiple-ai-models-like-claude/ (Accessed: November 17, 2025)
- TechRepublic: Apple's Xcode 26 Beta Now Supports GPT-5 and Claude - https://www.techrepublic.com/article/news-xcode-26-ai-integration-tab-redesign/ (Accessed: November 17, 2025)
- Matteo Manferdini: JSON Decoding in Swift with Codable: A Practical Guide - https://matteomanferdini.com/codable/ (Accessed: November 17, 2025)
- Hacking with Swift: Parsing JSON using the Codable protocol - https://www.hackingwithswift.com/read/7/3/parsing-json-using-the-codable-protocol (Accessed: November 17, 2025)
- Medium (Vikram Kumar): Swift Codable: Handling JSON Like a Pro - https://vikramios.medium.com/swift-codable-handling-92a0f2432874 (Accessed: November 17, 2025)
- Donny Wals: JSON parsing in Swift explained using Codable - https://www.donnywals.com/json-parsing-in-swift-explained-using-codable/ (Accessed: November 17, 2025)
- swiftyplace: Codable: How to simplify converting JSON data to Swift objects - https://www.swiftyplace.com/blog/codable-how-to-simplify-converting-json-data-to-swift-objects-and-vice-versa (Accessed: November 17, 2025)
- quicktype.io: JSON to Swift - https://quicktype.io/swift (Accessed: November 17, 2025)
- Swift by Sundell: Creating generic networking APIs in Swift - https://www.swiftbysundell.com/articles/creating-generic-networking-apis-in-swift/ (Accessed: November 17, 2025)
- Medium (Sachin Siwal): Code with Intelligence — Xcode 26 - https://medium.com/@sachinsiwal/code-with-intelligence-xcode-26-9203a8ce665e (Accessed: November 17, 2025)
- GitHub: xcode-loves-ai - Xcode Source Editor Extension for ChatGPT - https://github.com/marcosiino/xcode-loves-ai (Accessed: November 17, 2025)
- Using Claude with Coding Assistant in Xcode 26 - https://simonbs.dev/posts/using-claude-with-coding-assistant-in-xcode-26/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
