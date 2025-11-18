# How to implement tab-based navigation with TabView

**Article ID:** KB-075
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 20-25 minutes

## Overview

TabView is SwiftUI's primary component for implementing tab-based navigation, allowing users to switch between multiple views using a tab bar interface. With iOS 18+, TabView received a major redesign introducing the new `Tab` struct, sidebar adaptability for iPads, and extensive customization options that replace the deprecated `.tabItem()` modifier.

## Prerequisites

- Xcode 26.1 or later installed
- Basic understanding of SwiftUI views and state management
- iOS 18.2+ deployment target
- Familiarity with SwiftUI view modifiers
- Related articles: KB-011 (SwiftUI Views), KB-022 (State Property Wrapper)

## Steps

### Step 1: Create a Basic TabView with the New Tab Syntax

The modern TabView uses the `Tab` struct instead of the deprecated `.tabItem()` modifier. Each tab requires a title, system image, and content view.

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        TabView {
            Tab("Home", systemImage: "house") {
                HomeView()
            }

            Tab("Explore", systemImage: "magnifyingglass") {
                ExploreView()
            }

            Tab("Profile", systemImage: "person") {
                ProfileView()
            }

            Tab("Settings", systemImage: "gear") {
                SettingsView()
            }
        }
    }
}

// Example child views
struct HomeView: View {
    var body: some View {
        NavigationStack {
            Text("Home View")
                .navigationTitle("Home")
        }
    }
}

struct ExploreView: View {
    var body: some View {
        NavigationStack {
            Text("Explore View")
                .navigationTitle("Explore")
        }
    }
}

struct ProfileView: View {
    var body: some View {
        NavigationStack {
            Text("Profile View")
                .navigationTitle("Profile")
        }
    }
}

struct SettingsView: View {
    var body: some View {
        NavigationStack {
            Text("Settings View")
                .navigationTitle("Settings")
        }
    }
}
```

**Key Points:**
- The `Tab` initializer takes a title string and a `systemImage` parameter for SF Symbols
- Each tab wraps its content view in a trailing closure
- Embedding each tab's content in a `NavigationStack` enables navigation within that tab

### Step 2: Implement Programmatic Tab Selection

To control which tab is displayed programmatically, bind the TabView to a state property and assign values to each tab.

```swift
import SwiftUI

struct ContentView: View {
    enum TabSelection: Hashable {
        case home
        case explore
        case profile
        case settings
    }

    @State private var selectedTab: TabSelection = .home

    var body: some View {
        TabView(selection: $selectedTab) {
            Tab("Home", systemImage: "house", value: .home) {
                HomeView(selectedTab: $selectedTab)
            }

            Tab("Explore", systemImage: "magnifyingglass", value: .explore) {
                ExploreView(selectedTab: $selectedTab)
            }

            Tab("Profile", systemImage: "person", value: .profile) {
                ProfileView(selectedTab: $selectedTab)
            }

            Tab("Settings", systemImage: "gear", value: .settings) {
                SettingsView()
            }
        }
    }
}

struct HomeView: View {
    @Binding var selectedTab: ContentView.TabSelection

    var body: some View {
        NavigationStack {
            VStack(spacing: 20) {
                Text("Home View")
                    .font(.largeTitle)

                Button("Go to Profile") {
                    selectedTab = .profile
                }
                .buttonStyle(.borderedProminent)
            }
            .navigationTitle("Home")
        }
    }
}

struct ExploreView: View {
    @Binding var selectedTab: ContentView.TabSelection

    var body: some View {
        NavigationStack {
            VStack(spacing: 20) {
                Text("Explore View")
                    .font(.largeTitle)

                Button("Go to Settings") {
                    selectedTab = .settings
                }
                .buttonStyle(.borderedProminent)
            }
            .navigationTitle("Explore")
        }
    }
}

struct ProfileView: View {
    @Binding var selectedTab: ContentView.TabSelection

    var body: some View {
        NavigationStack {
            VStack(spacing: 20) {
                Text("Profile View")
                    .font(.largeTitle)

                Button("Back to Home") {
                    selectedTab = .home
                }
                .buttonStyle(.borderedProminent)
            }
            .navigationTitle("Profile")
        }
    }
}
```

**Key Points:**
- Create an enum conforming to `Hashable` to represent tab selections
- Use `@State` to track the selected tab
- Add the `value` parameter to each `Tab` matching your enum cases
- Pass the binding to child views that need to change tabs programmatically

### Step 3: Add Badges and Special Tab Roles

Enhance your tabs with badges for notifications and use special roles like search tabs.

```swift
import SwiftUI

struct ContentView: View {
    @State private var messageCount = 5
    @State private var notificationCount = 12

    var body: some View {
        TabView {
            Tab("Home", systemImage: "house") {
                HomeView()
            }

            Tab("Search", systemImage: "magnifyingglass", role: .search) {
                SearchView()
            }

            Tab("Messages", systemImage: "message") {
                MessagesView()
            }
            .badge(messageCount)

            Tab("Notifications", systemImage: "bell") {
                NotificationsView()
            }
            .badge(notificationCount)

            Tab("Profile", systemImage: "person") {
                ProfileView()
            }
        }
    }
}

struct SearchView: View {
    @State private var searchText = ""

    var body: some View {
        NavigationStack {
            List {
                ForEach(0..<10) { index in
                    Text("Search Result \(index + 1)")
                }
            }
            .searchable(text: $searchText, prompt: "Search...")
            .navigationTitle("Search")
        }
    }
}

struct MessagesView: View {
    var body: some View {
        NavigationStack {
            List {
                ForEach(0..<5) { index in
                    HStack {
                        Image(systemName: "person.circle.fill")
                            .font(.largeTitle)
                        VStack(alignment: .leading) {
                            Text("Contact \(index + 1)")
                                .font(.headline)
                            Text("New message...")
                                .font(.subheadline)
                                .foregroundStyle(.secondary)
                        }
                    }
                }
            }
            .navigationTitle("Messages")
        }
    }
}

struct NotificationsView: View {
    var body: some View {
        NavigationStack {
            List {
                ForEach(0..<12) { index in
                    HStack {
                        Image(systemName: "bell.badge")
                            .foregroundStyle(.blue)
                        Text("Notification \(index + 1)")
                    }
                }
            }
            .navigationTitle("Notifications")
        }
    }
}
```

**Key Points:**
- Use `.badge()` modifier to display notification counts on tabs
- Badge values can be integers or text
- The `role: .search` parameter provides default behavior for search tabs
- Badges automatically update when their bound values change

### Step 4: Enable Sidebar Adaptability for iPad

Make your TabView adapt to a sidebar on larger devices like iPads using the `.sidebarAdaptable` style.

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        TabView {
            Tab("Home", systemImage: "house") {
                HomeView()
            }

            Tab("Library", systemImage: "books.vertical") {
                LibraryView()
            }

            Tab("Favorites", systemImage: "heart") {
                FavoritesView()
            }

            TabSection("Tools") {
                Tab("Calculator", systemImage: "plus.forwardslash.minus") {
                    CalculatorView()
                }

                Tab("Calendar", systemImage: "calendar") {
                    CalendarView()
                }

                Tab("Notes", systemImage: "note.text") {
                    NotesView()
                }
            }

            Tab("Settings", systemImage: "gear") {
                SettingsView()
            }
        }
        .tabViewStyle(.sidebarAdaptable)
    }
}

struct LibraryView: View {
    var body: some View {
        NavigationStack {
            List {
                ForEach(0..<20) { index in
                    Text("Book \(index + 1)")
                }
            }
            .navigationTitle("Library")
        }
    }
}

struct FavoritesView: View {
    var body: some View {
        NavigationStack {
            List {
                ForEach(0..<10) { index in
                    Text("Favorite Item \(index + 1)")
                }
            }
            .navigationTitle("Favorites")
        }
    }
}

struct CalculatorView: View {
    var body: some View {
        NavigationStack {
            Text("Calculator")
                .navigationTitle("Calculator")
        }
    }
}

struct CalendarView: View {
    var body: some View {
        NavigationStack {
            Text("Calendar")
                .navigationTitle("Calendar")
        }
    }
}

struct NotesView: View {
    var body: some View {
        NavigationStack {
            Text("Notes")
                .navigationTitle("Notes")
        }
    }
}
```

**Key Points:**
- `.tabViewStyle(.sidebarAdaptable)` transforms the tab bar into a sidebar on iPadOS
- On iOS, the style displays as a standard bottom tab bar
- `TabSection` groups related tabs together in the sidebar with a header
- Sections automatically collapse/expand in the sidebar interface

### Step 5: Implement User Customization with TabViewCustomization

Allow users to reorder, hide, and customize tabs using the TabViewCustomization feature.

```swift
import SwiftUI

struct ContentView: View {
    @AppStorage("tabCustomization") private var customization: TabViewCustomization

    var body: some View {
        TabView {
            Tab("Home", systemImage: "house") {
                HomeView()
            }
            .customizationID("com.myapp.home")

            Tab("Search", systemImage: "magnifyingglass", role: .search) {
                SearchView()
            }
            .customizationID("com.myapp.search")

            Tab("Messages", systemImage: "message") {
                MessagesView()
            }
            .customizationID("com.myapp.messages")
            .badge(5)

            TabSection {
                Tab("Library", systemImage: "books.vertical") {
                    LibraryView()
                }
                .customizationID("com.myapp.library")

                Tab("Favorites", systemImage: "heart") {
                    FavoritesView()
                }
                .customizationID("com.myapp.favorites")

                Tab("Downloads", systemImage: "arrow.down.circle") {
                    DownloadsView()
                }
                .customizationID("com.myapp.downloads")
            } header: {
                Label("Content", systemImage: "folder")
            }
            .customizationID("com.myapp.content-section")

            Tab("Settings", systemImage: "gear") {
                SettingsView()
            }
            .customizationID("com.myapp.settings")
        }
        .tabViewStyle(.sidebarAdaptable)
        .tabViewCustomization($customization)
    }
}

struct DownloadsView: View {
    var body: some View {
        NavigationStack {
            List {
                ForEach(0..<8) { index in
                    HStack {
                        Image(systemName: "doc")
                        Text("Download \(index + 1)")
                    }
                }
            }
            .navigationTitle("Downloads")
        }
    }
}
```

**Key Points:**
- Use `@AppStorage` to persist customization settings across app launches
- Each tab and section needs a unique `.customizationID()` for customization to work
- Users can drag tabs between the sidebar and tab bar on iPad
- Customization IDs should follow reverse domain notation (e.g., "com.myapp.tabname")
- Without customization IDs, tabs won't be movable or hideable by users

### Step 6: Use Custom Images Instead of SF Symbols

Replace system images with custom assets for unique branding.

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        TabView {
            Tab("Home", image: "custom-home-icon") {
                HomeView()
            }

            Tab("Shop", image: "custom-shop-icon") {
                ShopView()
            }

            Tab("Cart", image: "custom-cart-icon") {
                CartView()
            }
            .badge(3)

            Tab("Profile", image: "custom-profile-icon") {
                ProfileView()
            }
        }
    }
}

struct ShopView: View {
    var body: some View {
        NavigationStack {
            ScrollView {
                LazyVGrid(columns: [GridItem(.adaptive(minimum: 150))]) {
                    ForEach(0..<10) { index in
                        VStack {
                            RoundedRectangle(cornerRadius: 12)
                                .fill(.blue.opacity(0.3))
                                .frame(height: 150)
                            Text("Product \(index + 1)")
                                .font(.headline)
                        }
                    }
                }
                .padding()
            }
            .navigationTitle("Shop")
        }
    }
}

struct CartView: View {
    var body: some View {
        NavigationStack {
            List {
                ForEach(0..<3) { index in
                    HStack {
                        RoundedRectangle(cornerRadius: 8)
                            .fill(.gray.opacity(0.3))
                            .frame(width: 60, height: 60)
                        VStack(alignment: .leading) {
                            Text("Product \(index + 1)")
                                .font(.headline)
                            Text("$29.99")
                                .font(.subheadline)
                                .foregroundStyle(.secondary)
                        }
                        Spacer()
                    }
                }
            }
            .navigationTitle("Cart")
        }
    }
}
```

**Key Points:**
- Use the `image` parameter instead of `systemImage` for custom assets
- Custom images must be added to your asset catalog
- Provide both light and dark mode variants in the asset catalog
- Image names are case-sensitive and should match exactly
- Custom images should be designed at appropriate sizes (typically 25x25 points)

## Expected Results

After implementing TabView:

1. **Bottom Tab Bar (iOS)**: A tab bar appears at the bottom of the screen with all defined tabs
2. **Tab Switching**: Tapping a tab instantly switches to that view
3. **Selection State**: The active tab is highlighted with the accent color
4. **Badges**: Notification counts appear as red badges on relevant tabs
5. **Sidebar (iPad)**: When using `.sidebarAdaptable`, tabs transform into a sidebar on iPadOS
6. **User Customization**: With TabViewCustomization, users can long-press tabs to enter edit mode and rearrange them
7. **Navigation Persistence**: Each tab maintains its own navigation stack independently

## Troubleshooting

### Common Issue 1: Tab Selection Not Updating

**Problem:** Programmatic tab changes don't work or tabs don't respond to selection binding
**Solution:**
- Ensure you're using `TabView(selection: $selectedTab)` with a binding
- Verify each `Tab` has a unique `value` parameter matching your enum cases
- Check that your enum conforms to `Hashable`
- Make sure the state property is declared with `@State`

```swift
// Correct implementation
@State private var selectedTab = TabSelection.home

TabView(selection: $selectedTab) {
    Tab("Home", systemImage: "house", value: .home) {
        HomeView()
    }
}
```

### Common Issue 2: Sidebar Not Appearing on iPad

**Problem:** `.sidebarAdaptable` style doesn't show sidebar on iPad
**Solution:**
- Verify you're testing on iPadOS, not iOS (sidebar only appears on iPad)
- Check that `.tabViewStyle(.sidebarAdaptable)` is applied to the TabView
- Ensure your iPad simulator is in landscape or using enough screen width
- Test in Split View or on a physical iPad

```swift
// Correct implementation
TabView {
    // tabs here
}
.tabViewStyle(.sidebarAdaptable)  // Must be applied to TabView
```

### Common Issue 3: Customization Not Persisting

**Problem:** User tab customizations reset when the app relaunches
**Solution:**
- Use `@AppStorage` instead of `@State` for the customization binding
- Ensure every tab has a unique `.customizationID()`
- Verify customization IDs are strings and follow a consistent naming pattern
- Check that `.tabViewCustomization()` modifier is applied with a binding

```swift
// Correct implementation
@AppStorage("tabCustomization") private var customization: TabViewCustomization

TabView {
    Tab("Home", systemImage: "house") {
        HomeView()
    }
    .customizationID("com.myapp.home")  // Unique ID required
}
.tabViewCustomization($customization)  // Binding required
```

### Common Issue 4: Custom Images Not Displaying

**Problem:** Custom tab icons don't appear or show as blank
**Solution:**
- Verify the image exists in your asset catalog with the exact name
- Check that image rendering mode is set to "Template" in asset settings
- Ensure images are provided at 1x, 2x, and 3x resolutions
- Confirm you're using `image:` parameter, not `systemImage:`

```swift
// Use image parameter for custom assets
Tab("Home", image: "custom-home")  // Not systemImage

// In Assets.xcassets:
// - Set Render As: Template Image
// - Provide all scale factors (1x, 2x, 3x)
```

## Additional Tips

- **Navigation Stacks**: Always wrap tab content in `NavigationStack` to enable navigation within each tab
- **State Isolation**: Each tab maintains its own navigation stack, so pushing views in one tab doesn't affect others
- **Performance**: TabView keeps all tab views in memory, so avoid heavy initialization in tab content
- **Accessibility**: Tab titles are automatically used for VoiceOver; ensure they're descriptive
- **Testing**: Test your TabView on both iPhone and iPad simulators to verify proper adaptation
- **Migration**: When migrating from `.tabItem()`, replace with `Tab` struct and update to value-based selection
- **Badge Updates**: Use `@State` or observable objects to dynamically update badge values
- **Deep Linking**: Use the selection binding to navigate to specific tabs from notifications or URLs
- **Tab Limits**: iOS typically shows 5 tabs in the tab bar; additional tabs appear in a "More" tab
- **Section Best Practices**: Use `TabSection` to organize related functionality in sidebar mode

## Related Articles

- KB-011: How to write your first Hello World SwiftUI view
- KB-022: State Property Wrapper
- KB-023: Button Actions and User Interactions
- KB-021: Add UI Elements to SwiftUI Views

## Sources

- TabView - Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/tabview (Accessed: November 17, 2025)
- Enhancing your app's content with tab navigation - Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/enhancing-your-app-content-with-tab-navigation (Accessed: November 17, 2025)
- Tab - Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/tab (Accessed: November 17, 2025)
- TabViewCustomization - Apple Developer Documentation - https://developer.apple.com/documentation/swiftui/tabviewcustomization (Accessed: November 17, 2025)
- Elevate your tab and sidebar experience in iPadOS - WWDC24 - Apple Developer - https://developer.apple.com/videos/play/wwdc2024/10147/ (Accessed: November 17, 2025)
- What's new in SwiftUI - WWDC24 - Apple Developer - https://developer.apple.com/videos/play/wwdc2024/10144/ (Accessed: November 17, 2025)
- Getting Started with the Improved TabView in iOS 18 - Medium - https://medium.com/@jpmtech/getting-started-with-the-improved-tabview-in-ios-18-111974b70db9 (Accessed: November 17, 2025)
- What's new in SwiftUI for iOS 18 - Hacking with Swift - https://www.hackingwithswift.com/articles/270/whats-new-in-swiftui-for-ios-18 (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
