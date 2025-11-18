# How to implement SwiftData for modern data persistence

**Article ID:** KB-080
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 35-40 minutes

## Overview

SwiftData is Apple's modern, declarative framework for data persistence that seamlessly integrates with SwiftUI and Swift 6. This article guides you through implementing SwiftData in your iOS 18.2+ applications, covering model definition, data relationships, querying, and advanced features like unique constraints, indexing, and custom data stores. You'll learn to replace Core Data with a more Swift-native approach while maintaining full control over your app's data layer.

## Prerequisites

- Xcode 26.1 or later installed
- iOS 18.2 SDK or later
- Basic understanding of Swift classes and structs
- Familiarity with SwiftUI fundamentals
- Understanding of property wrappers (KB-022)
- Basic knowledge of Swift macros

## Steps

### Step 1: Create a Basic SwiftData Model

SwiftData uses the `@Model` macro to transform regular Swift classes into persistent model objects. Create your first model:

```swift
import SwiftData

@Model
final class Task {
    var title: String
    var isCompleted: Bool
    var dueDate: Date
    var notes: String?

    init(title: String, isCompleted: Bool = false, dueDate: Date, notes: String? = nil) {
        self.title = title
        self.isCompleted = isCompleted
        self.dueDate = dueDate
        self.notes = notes
    }
}
```

**Key points:**
- The `@Model` macro must be applied to a `class`, not a struct
- SwiftData automatically infers storage requirements from property types
- The macro adds conformances for `Hashable`, `Identifiable`, `Observable`, and `PersistentModel`
- Optional properties like `notes` are fully supported

### Step 2: Set Up ModelContainer in Your App

The `ModelContainer` manages the persistent backend for your models. Configure it in your app's entry point:

```swift
import SwiftUI
import SwiftData

@main
struct TaskManagerApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(for: [Task.self])
    }
}
```

For more advanced configuration with multiple model types:

```swift
@main
struct TaskManagerApp: App {
    let container: ModelContainer

    init() {
        do {
            let schema = Schema([Task.self, Category.self, Tag.self])
            let configuration = ModelConfiguration(
                schema: schema,
                isStoredInMemoryOnly: false
            )
            container = try ModelContainer(
                for: schema,
                configurations: [configuration]
            )
        } catch {
            fatalError("Failed to create ModelContainer: \(error)")
        }
    }

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(container)
    }
}
```

### Step 3: Query Data in SwiftUI Views

Use the `@Query` macro to automatically load and observe SwiftData objects in your views:

```swift
import SwiftUI
import SwiftData

struct TaskListView: View {
    @Query(sort: \.dueDate, order: .forward) var tasks: [Task]
    @Environment(\.modelContext) private var modelContext

    var body: some View {
        List {
            ForEach(tasks) { task in
                TaskRowView(task: task)
            }
            .onDelete(perform: deleteTasks)
        }
        .navigationTitle("Tasks")
        .toolbar {
            Button("Add Task", action: addTask)
        }
    }

    private func addTask() {
        let newTask = Task(
            title: "New Task",
            dueDate: Date()
        )
        modelContext.insert(newTask)
    }

    private func deleteTasks(at offsets: IndexSet) {
        for index in offsets {
            modelContext.delete(tasks[index])
        }
    }
}
```

**Important:** The `@Query` macro only works inside SwiftUI views and automatically updates when data changes.

### Step 4: Implement Relationships Between Models

Create relationships using standard Swift properties with appropriate types:

```swift
@Model
final class Category {
    var name: String
    var color: String

    @Relationship(deleteRule: .cascade, inverse: \Task.category)
    var tasks: [Task]?

    init(name: String, color: String) {
        self.name = name
        self.color = color
        self.tasks = []
    }
}

@Model
final class Task {
    var title: String
    var isCompleted: Bool
    var dueDate: Date
    var notes: String?

    var category: Category?

    init(title: String, isCompleted: Bool = false, dueDate: Date, notes: String? = nil, category: Category? = nil) {
        self.title = title
        self.isCompleted = isCompleted
        self.dueDate = dueDate
        self.notes = notes
        self.category = category
    }
}
```

**Delete rules:**
- `.cascade` - Deleting parent deletes children
- `.nullify` - Deleting parent sets relationship to nil (default)
- `.deny` - Prevents deletion if relationships exist
- `.noAction` - No automatic action taken

### Step 5: Add Unique Constraints with #Unique

Prevent duplicate records using the `#Unique` macro (iOS 18+):

```swift
@Model
final class User {
    #Unique<User>([\.email])

    var email: String
    var username: String
    var createdDate: Date

    init(email: String, username: String) {
        self.email = email
        self.username = username
        self.createdDate = Date()
    }
}
```

For compound uniqueness constraints:

```swift
@Model
final class Trip {
    #Unique<Trip>([\.name, \.startDate, \.endDate])

    var name: String
    var destination: String
    var startDate: Date
    var endDate: Date

    init(name: String, destination: String, startDate: Date, endDate: Date) {
        self.name = name
        self.destination = destination
        self.startDate = startDate
        self.endDate = endDate
    }
}
```

When a collision occurs, SwiftData performs an **upsert** (update if exists, insert if new).

### Step 6: Optimize Queries with #Index

Improve query performance for large datasets using the `#Index` macro (iOS 18+):

```swift
@Model
final class Product {
    #Index<Product>([\.category, \.price])

    var name: String
    var category: String
    var price: Double
    var stockCount: Int

    init(name: String, category: String, price: Double, stockCount: Int) {
        self.name = name
        self.category = category
        self.price = price
        self.stockCount = stockCount
    }
}
```

**Benefits:**
- Significantly faster filtering and sorting on indexed properties
- Compound indexes support multi-property queries
- Essential for tables with thousands of records

### Step 7: Use Advanced Queries with #Predicate

Filter data with type-safe predicates using the `#Predicate` macro:

```swift
struct CompletedTasksView: View {
    @Query(filter: #Predicate<Task> { task in
        task.isCompleted == true
    }, sort: \.dueDate) var completedTasks: [Task]

    var body: some View {
        List(completedTasks) { task in
            TaskRowView(task: task)
        }
    }
}
```

For more complex predicates:

```swift
struct FilteredTasksView: View {
    let searchText: String

    var body: some View {
        TaskListView(searchText: searchText)
    }
}

struct TaskListView: View {
    let searchText: String

    @Query var tasks: [Task]

    init(searchText: String) {
        self.searchText = searchText

        let predicate = #Predicate<Task> { task in
            searchText.isEmpty ||
            task.title.localizedStandardContains(searchText)
        }

        _tasks = Query(filter: predicate, sort: \.dueDate)
    }

    var body: some View {
        List(tasks) { task in
            TaskRowView(task: task)
        }
    }
}
```

### Step 8: Leverage #Expression for Complex Calculations (iOS 18+)

Use the `#Expression` macro for sophisticated query conditions:

```swift
import Foundation
import SwiftData

// Count incomplete tasks in a category
let incompleteTasksExpression = #Expression<[Task], Int> { tasks in
    tasks.filter { !$0.isCompleted }.count
}

// Query categories with unfinished work
@Query var categoriesWithWork: [Category]

init() {
    let predicate = #Predicate<Category> { category in
        incompleteTasksExpression.evaluate(category.tasks ?? []) > 0
    }
    _categoriesWithWork = Query(filter: predicate)
}
```

Another example for calculating averages:

```swift
// Calculate average score for passing records
let passingScoreExpression = #Expression<[Record], Double> { records in
    let passing = records.filter { $0.score >= 90 }
    guard !passing.isEmpty else { return 0.0 }
    return passing.reduce(0.0) { $0 + $1.score } / Double(passing.count)
}
```

### Step 9: Implement Manual Data Operations with ModelContext

For operations outside of SwiftUI views, use `ModelContext` directly:

```swift
import SwiftData

final class TaskService {
    let modelContext: ModelContext

    init(modelContext: ModelContext) {
        self.modelContext = modelContext
    }

    func createTask(title: String, dueDate: Date, category: Category?) throws {
        let task = Task(title: title, dueDate: dueDate, category: category)
        modelContext.insert(task)
        try modelContext.save()
    }

    func fetchTasks(completedOnly: Bool = false) throws -> [Task] {
        let predicate = completedOnly ?
            #Predicate<Task> { $0.isCompleted == true } :
            nil

        let descriptor = FetchDescriptor<Task>(
            predicate: predicate,
            sortBy: [SortDescriptor(\.dueDate)]
        )

        return try modelContext.fetch(descriptor)
    }

    func updateTask(_ task: Task, isCompleted: Bool) throws {
        task.isCompleted = isCompleted
        try modelContext.save()
    }

    func deleteTask(_ task: Task) throws {
        modelContext.delete(task)
        try modelContext.save()
    }

    func batchUpdate(tasks: [Task], isCompleted: Bool) throws {
        for task in tasks {
            task.isCompleted = isCompleted
        }
        try modelContext.save()
    }
}
```

### Step 10: Use Transient Properties and Custom Attributes

Control which properties persist using `@Transient` and `@Attribute`:

```swift
@Model
final class Task {
    var title: String
    var isCompleted: Bool
    var dueDate: Date

    @Attribute(.unique) var taskID: UUID

    // Not persisted - computed each time
    @Transient var isOverdue: Bool {
        !isCompleted && dueDate < Date()
    }

    // Not persisted - can be recalculated
    @Transient var formattedDueDate: String = ""

    init(title: String, isCompleted: Bool = false, dueDate: Date) {
        self.title = title
        self.isCompleted = isCompleted
        self.dueDate = dueDate
        self.taskID = UUID()
    }
}
```

### Step 11: Create Custom Data Stores (Advanced - iOS 18+)

Implement custom persistence formats by creating a custom data store:

```swift
import SwiftData
import Foundation

@available(iOS 18, *)
final class JSONStoreConfiguration: DataStoreConfiguration {
    typealias StoreType = JSONStore

    var name: String
    var schema: Schema?
    var fileURL: URL

    init(name: String, schema: Schema?, fileURL: URL) {
        self.name = name
        self.schema = schema
        self.fileURL = fileURL
    }
}

@available(iOS 18, *)
final class JSONStore: DataStore {
    typealias Configuration = JSONStoreConfiguration
    typealias Snapshot = DefaultSnapshot

    var configuration: JSONStoreConfiguration
    var name: String { configuration.name }
    var schema: Schema { configuration.schema! }

    init(_ configuration: JSONStoreConfiguration, migrationPlan: (any SchemaMigrationPlan.Type)?) throws {
        self.configuration = configuration
        // Initialize your JSON-based storage
    }

    func save(_ request: DataStoreSaveRequest) throws -> DataStoreSaveResult {
        // Implement JSON serialization logic
        return DataStoreSaveResult(for: request)
    }

    func fetch<T>(_ request: DataStoreFetchRequest) throws -> DataStoreFetchResult<T> where T : PersistentModel {
        // Implement JSON deserialization logic
        return DataStoreFetchResult<T>()
    }
}

// Usage
let jsonConfig = JSONStoreConfiguration(
    name: "TasksJSON",
    schema: Schema([Task.self]),
    fileURL: URL.documentsDirectory.appending(path: "tasks.json")
)

let container = try ModelContainer(
    for: Schema([Task.self]),
    configurations: jsonConfig
)
```

### Step 12: Set Up Preview Support

Create in-memory containers for SwiftUI previews:

```swift
extension ModelContainer {
    @MainActor
    static var preview: ModelContainer {
        let schema = Schema([Task.self, Category.self])
        let configuration = ModelConfiguration(isStoredInMemoryOnly: true)
        let container = try! ModelContainer(for: schema, configurations: configuration)

        // Add sample data
        let sampleCategory = Category(name: "Work", color: "blue")
        container.mainContext.insert(sampleCategory)

        let task1 = Task(title: "Review code", dueDate: Date(), category: sampleCategory)
        let task2 = Task(title: "Write tests", dueDate: Date().addingTimeInterval(86400))

        container.mainContext.insert(task1)
        container.mainContext.insert(task2)

        return container
    }
}

// Use in previews
#Preview {
    TaskListView()
        .modelContainer(ModelContainer.preview)
}
```

In iOS 18+, you can also use `@Previewable` for direct `@Query` usage:

```swift
#Preview {
    @Previewable @Query var tasks: [Task]
    List(tasks) { task in
        Text(task.title)
    }
    .modelContainer(ModelContainer.preview)
}
```

## Expected Results

After implementing SwiftData, you should observe:

1. **Data Persistence**: Data automatically saves and persists between app launches
2. **Automatic UI Updates**: SwiftUI views refresh automatically when data changes
3. **Type Safety**: Compile-time errors for incorrect property access or queries
4. **No Boilerplate**: Minimal setup compared to Core Data (no .xcdatamodeld files)
5. **SwiftUI Integration**: Seamless use of `@Query` in views with live updates
6. **Performance**: Fast queries with proper indexing on large datasets
7. **Relationship Integrity**: Cascade deletes and relationship management work automatically

## Troubleshooting

### Common Issue 1: @Query Not Updating
**Problem:** Views don't update when data changes
**Solution:**
- Ensure `@Query` is used inside a SwiftUI `View` (not a `class` or other type)
- Verify `modelContainer` is attached to the view hierarchy
- Check that you're using the `@Model` macro on your model class
- Confirm the `ModelContext` is from SwiftUI's environment

### Common Issue 2: "Fatal error: Failed to find a currently bound ModelContext"
**Problem:** Attempting to use `@Query` without a ModelContainer
**Solution:**
- Add `.modelContainer(for: [YourModel.self])` to your app's scene
- In previews, explicitly provide `.modelContainer(ModelContainer.preview)`
- Ensure parent views have the container configured

### Common Issue 3: Unique Constraint Violations Not Working
**Problem:** Duplicate records still being created
**Solution:**
- Verify you're using iOS 18+ (check deployment target)
- Ensure `#Unique` macro is placed correctly in the model
- Remember that uniqueness is enforced on save, not insert
- Check that unique properties have the correct values set

### Common Issue 4: Performance Issues with Large Datasets
**Problem:** UI freezes when displaying many records
**Solution:**
- Add `#Index` macro to frequently queried properties
- Use `FetchDescriptor` with limits: `FetchDescriptor(fetchLimit: 100)`
- Implement pagination or lazy loading
- Avoid loading unnecessary relationships (use `@Relationship` judiciously)
- Consider background context for heavy operations

### Common Issue 5: Migration from Core Data
**Problem:** Existing Core Data app needs SwiftData integration
**Solution:**
- SwiftData can coexist with Core Data using the same underlying store
- Use `ModelConfiguration(url:)` to point to existing Core Data SQLite file
- Models must match Core Data entities (same attribute names and types)
- Test thoroughly with a copy of production data first

## Additional Tips

- **Always use final classes** for `@Model` to avoid inheritance complications
- **Batch operations** by accumulating changes then calling `save()` once
- **Use descriptors for complex queries** instead of filtering in-memory arrays
- **Leverage Swift 6 concurrency** - ModelContext is `@MainActor` by default in SwiftUI
- **Test with realistic data volumes** early to identify performance bottlenecks
- **Use `@Transient`** for computed properties to avoid unnecessary persistence
- **Implement proper error handling** - all save/fetch operations can throw
- **Consider memory management** - large object graphs stay in memory
- **Use `#Predicate` over manual filtering** for better performance and type safety
- **Document your model relationships** to avoid confusion in complex schemas
- **Plan migrations early** if you expect schema changes post-release

## Related Articles

- KB-022 - How to use the @State property wrapper in SwiftUI
- KB-009 - How to choose between SwiftUI and UIKit for your project
- KB-011 - How to write your first "Hello World" SwiftUI view
- KB-051 - Use AI to generate unit tests for existing code

## Sources

- SwiftData | Apple Developer Documentation - https://developer.apple.com/documentation/swiftdata (Accessed: November 17, 2025)
- What's new in SwiftData - WWDC24 - https://developer.apple.com/videos/play/wwdc2024/10137/ (Accessed: November 17, 2025)
- Create a custom data store with SwiftData - WWDC24 - https://developer.apple.com/videos/play/wwdc2024/10138/ (Accessed: November 17, 2025)
- SwiftData updates | Apple Developer Documentation - https://developer.apple.com/documentation/updates/swiftdata (Accessed: November 17, 2025)
- What's new in SwiftData | WWDC Notes - https://wwdcnotes.com/documentation/wwdcnotes/wwdc24-10137-whats-new-in-swiftdata/ (Accessed: November 17, 2025)
- SwiftData Expressions - Use Your Loaf - https://useyourloaf.com/blog/swiftdata-expressions/ (Accessed: November 17, 2025)
- SwiftData by Example - Hacking with Swift - https://www.hackingwithswift.com/quick-start/swiftdata (Accessed: November 17, 2025)
- Swiftdata Architecture Patterns And Practices - AzamSharp - https://azamsharp.com/2025/03/28/swiftdata-architecture-patterns-and-practices.html (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
