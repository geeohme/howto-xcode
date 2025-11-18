# How to use @MainActor for UI updates with AI-generated patterns

**Article ID:** KB-087
**Difficulty:** Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 30-45 minutes

## Overview

The `@MainActor` attribute is Swift 6's primary mechanism for ensuring UI updates happen on the main thread, preventing data races and crashes. This advanced guide demonstrates how to leverage AI coding assistants in Xcode 26.1+ to generate robust, thread-safe UI update patterns using @MainActor with Swift 6.2's latest concurrency features. You'll learn to use ChatGPT, Claude, or local AI models to produce code that automatically isolates UI-critical code to the main thread while maintaining optimal performance.

## Prerequisites

- Xcode 26.1+ installed with Swift 6.2 support
- Understanding of Swift concurrency basics (async/await, actors)
- Familiarity with SwiftUI or UIKit development
- At least one AI provider configured in Xcode (see KB-031, KB-041, or KB-043)
- Swift 6 language mode enabled in project settings
- iOS 15+ deployment target (iOS 26+ for UIKit integration features)
- Related: KB-082 (Async/await network calls), KB-079 (@Observable macro), KB-085 (Mock data services)

## Steps

### Step 1: Configure AI Assistant for Swift 6.2 Concurrency Context

Before generating @MainActor code, set up your AI assistant with appropriate concurrency context to ensure it produces Swift 6.2-compliant code.

**Open the Coding Assistant panel** (View → Show Coding Assistant or `Cmd+Shift+A`) and provide initial context:

```
I'm working on a Swift 6.2 project in Xcode 26.1+ targeting iOS 26+. I need to implement
thread-safe UI updates using @MainActor. Please ensure all suggestions:

1. Use @MainActor for UI-related classes and methods
2. Follow Swift 6.2 concurrency best practices
3. Leverage default actor isolation where appropriate
4. Use nonisolated for intentionally non-isolated code
5. Avoid legacy DispatchQueue.main patterns
6. Include proper isolation annotations for async methods
7. Use the @concurrent attribute when introducing parallelism
8. Handle actor context transitions efficiently

Target Swift 6 language mode with complete concurrency checking enabled.
```

**Why this matters:** AI models trained on older Swift versions may suggest deprecated patterns. Providing explicit Swift 6.2 context ensures modern, correct code generation.

### Step 2: Generate a MainActor-Isolated ViewModel with AI

Ask your AI assistant to create a ViewModel that properly isolates UI state to the main actor.

**Prompt:**
```
Create a SwiftUI ViewModel using @Observable and @MainActor that:
1. Fetches user profile data from a network service
2. Manages loading, success, and error states
3. Updates UI properties only on the main thread
4. Uses async/await for network calls
5. Handles errors gracefully with user-friendly messages
6. Includes a refresh method for pull-to-refresh
7. Follows Swift 6.2 concurrency best practices

The network service should be injected as a dependency and is an actor.
```

**Expected AI-Generated Code:**

```swift
import SwiftUI
import Observation

@Observable
@MainActor
class ProfileViewModel {
    // MARK: - Published State
    var profile: UserProfile?
    var isLoading = false
    var errorMessage: String?

    // MARK: - Dependencies
    private let networkService: NetworkService

    // MARK: - Initialization
    init(networkService: NetworkService) {
        self.networkService = networkService
    }

    // MARK: - Public Methods
    func loadProfile(userId: String) async {
        isLoading = true
        errorMessage = nil

        do {
            // Network call happens on actor's executor, result returns to MainActor
            let fetchedProfile: UserProfile = try await networkService.request(
                endpoint: "/users/\(userId)",
                method: .get,
                body: nil as String?
            )

            // These assignments happen on MainActor automatically
            profile = fetchedProfile
            isLoading = false
        } catch let error as NetworkError {
            errorMessage = formatNetworkError(error)
            isLoading = false
        } catch {
            errorMessage = "An unexpected error occurred"
            isLoading = false
        }
    }

    func refresh(userId: String) async {
        // Clear existing data before refresh
        profile = nil
        await loadProfile(userId: userId)
    }

    // MARK: - Private Helpers
    private func formatNetworkError(_ error: NetworkError) -> String {
        switch error {
        case .invalidURL:
            return "Invalid server configuration"
        case .invalidResponse:
            return "Server returned invalid response"
        case .httpError(let statusCode):
            return "Server error (Code: \(statusCode))"
        case .decodingError:
            return "Failed to parse server response"
        case .networkError(let underlyingError):
            return underlyingError.localizedDescription
        }
    }
}
```

**Key AI-Generated Features to Verify:**
- `@MainActor` attribute on the class ensures all properties and methods run on the main thread
- `@Observable` macro provides SwiftUI state management
- All async methods automatically maintain MainActor context
- No explicit `MainActor.run` calls needed (Swift 6.2 handles this automatically)
- Properties update on the main thread without manual dispatching

### Step 3: Request AI to Generate Nonisolated Methods for Background Work

Some operations shouldn't block the main thread. Ask AI to create nonisolated methods that explicitly opt out of MainActor isolation.

**Prompt:**
```
Add a nonisolated method to ProfileViewModel that performs expensive data processing
in the background without blocking the main thread. The method should:
1. Calculate analytics from the user profile
2. Not access MainActor-isolated properties directly
3. Return results that can be assigned to MainActor properties
4. Use proper isolation annotations
5. Include documentation explaining the isolation strategy
```

**Expected AI-Generated Enhancement:**

```swift
extension ProfileViewModel {
    /// Performs expensive analytics calculation in a background context.
    ///
    /// This method is `nonisolated` to avoid blocking the main thread during
    /// intensive computation. Results can be safely assigned to MainActor
    /// properties when the method completes.
    ///
    /// - Parameter profile: The user profile to analyze
    /// - Returns: Computed analytics data
    nonisolated func calculateAnalytics(for profile: UserProfile) async -> ProfileAnalytics {
        // This runs on a background executor, not the main thread
        await Task.detached {
            var totalPosts = 0
            var totalEngagement = 0.0

            // Expensive computation
            for post in profile.posts {
                totalPosts += 1
                totalEngagement += Double(post.likes + post.comments + post.shares)
            }

            let averageEngagement = totalPosts > 0 ? totalEngagement / Double(totalPosts) : 0

            return ProfileAnalytics(
                totalPosts: totalPosts,
                averageEngagement: averageEngagement,
                engagementTrend: calculateTrend(profile.posts)
            )
        }.value
    }

    /// Process profile data and update UI with results.
    /// This method coordinates background work with MainActor updates.
    func processProfileData() async {
        guard let profile = profile else { return }

        isLoading = true

        // Calculate in background (nonisolated)
        let analytics = await calculateAnalytics(for: profile)

        // Update UI on MainActor (automatically)
        self.analytics = analytics
        isLoading = false
    }

    private nonisolated func calculateTrend(_ posts: [Post]) -> String {
        // Additional background processing
        guard posts.count >= 2 else { return "insufficient_data" }

        let recentEngagement = posts.suffix(5).map { $0.likes + $0.comments }.reduce(0, +)
        let olderEngagement = posts.dropLast(5).suffix(5).map { $0.likes + $0.comments }.reduce(0, +)

        return recentEngagement > olderEngagement ? "increasing" : "decreasing"
    }
}
```

**Verify AI Generated:**
- `nonisolated` keyword explicitly opts out of MainActor
- Background work doesn't block UI thread
- Results safely return to MainActor context
- Clear documentation of isolation strategy

### Step 4: Generate SwiftUI Views with Proper Task Isolation

Ask AI to create SwiftUI views that properly integrate with MainActor-isolated ViewModels.

**Prompt:**
```
Create a SwiftUI view that displays the ProfileViewModel data with:
1. Proper .task modifier for automatic cancellation
2. Loading, error, and success states
3. Pull-to-refresh functionality
4. Retry button for errors
5. Smooth state transitions
6. Accessibility labels
7. Modern iOS 26+ SwiftUI patterns
```

**Expected AI-Generated View:**

```swift
import SwiftUI

struct ProfileView: View {
    @State private var viewModel: ProfileViewModel
    let userId: String

    init(userId: String, networkService: NetworkService) {
        self.userId = userId
        self._viewModel = State(wrappedValue: ProfileViewModel(networkService: networkService))
    }

    var body: some View {
        ScrollView {
            content
        }
        .refreshable {
            await viewModel.refresh(userId: userId)
        }
        .task {
            // Automatically cancelled when view disappears
            await viewModel.loadProfile(userId: userId)
        }
        .navigationTitle("Profile")
    }

    @ViewBuilder
    private var content: some View {
        if viewModel.isLoading {
            loadingView
        } else if let errorMessage = viewModel.errorMessage {
            errorView(message: errorMessage)
        } else if let profile = viewModel.profile {
            profileContent(profile)
        } else {
            emptyStateView
        }
    }

    private var loadingView: some View {
        VStack(spacing: 16) {
            ProgressView()
                .scaleEffect(1.5)
            Text("Loading profile...")
                .foregroundStyle(.secondary)
        }
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .padding()
    }

    private func errorView(message: String) -> some View {
        VStack(spacing: 20) {
            Image(systemName: "exclamationmark.triangle.fill")
                .font(.system(size: 50))
                .foregroundStyle(.red)
                .accessibilityLabel("Error")

            Text(message)
                .multilineTextAlignment(.center)
                .foregroundStyle(.primary)

            Button {
                Task {
                    await viewModel.loadProfile(userId: userId)
                }
            } label: {
                Label("Retry", systemImage: "arrow.clockwise")
            }
            .buttonStyle(.borderedProminent)
            .accessibilityLabel("Retry loading profile")
        }
        .padding()
    }

    private func profileContent(_ profile: UserProfile) -> some View {
        VStack(alignment: .leading, spacing: 20) {
            // Profile header
            HStack(spacing: 16) {
                AsyncImage(url: URL(string: profile.avatarURL)) { image in
                    image
                        .resizable()
                        .aspectRatio(contentMode: .fill)
                } placeholder: {
                    Color.gray.opacity(0.3)
                }
                .frame(width: 80, height: 80)
                .clipShape(Circle())
                .accessibilityLabel("Profile picture of \(profile.name)")

                VStack(alignment: .leading, spacing: 4) {
                    Text(profile.name)
                        .font(.title2)
                        .fontWeight(.semibold)

                    Text(profile.email)
                        .font(.subheadline)
                        .foregroundStyle(.secondary)

                    Text("Member since \(profile.memberSince.formatted(date: .abbreviated, time: .omitted))")
                        .font(.caption)
                        .foregroundStyle(.tertiary)
                }
            }
            .padding()

            Divider()

            // Analytics section (if available)
            if let analytics = viewModel.analytics {
                VStack(alignment: .leading, spacing: 12) {
                    Text("Analytics")
                        .font(.headline)

                    HStack {
                        StatView(title: "Posts", value: "\(analytics.totalPosts)")
                        StatView(title: "Avg Engagement", value: String(format: "%.1f", analytics.averageEngagement))
                        StatView(title: "Trend", value: analytics.engagementTrend)
                    }
                }
                .padding()
            }
        }
    }

    private var emptyStateView: some View {
        VStack(spacing: 16) {
            Image(systemName: "person.slash")
                .font(.system(size: 50))
                .foregroundStyle(.secondary)
            Text("No profile data available")
                .foregroundStyle(.secondary)
        }
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .padding()
    }
}

struct StatView: View {
    let title: String
    let value: String

    var body: some View {
        VStack(spacing: 4) {
            Text(value)
                .font(.title3)
                .fontWeight(.bold)
            Text(title)
                .font(.caption)
                .foregroundStyle(.secondary)
        }
        .frame(maxWidth: .infinity)
    }
}
```

**Key Features to Verify:**
- `.task` modifier provides automatic cancellation
- All ViewModel interactions happen on MainActor
- No explicit threading code needed
- Accessibility labels included
- Modern SwiftUI patterns

### Step 5: Request AI to Generate UIKit Integration (iOS 26+)

For projects using UIKit, ask AI to demonstrate iOS 26's new Observable integration with @MainActor.

**Prompt:**
```
Create a UIKit view controller for iOS 26+ that:
1. Uses the ProfileViewModel with @Observable
2. Integrates with the new updateProperties() API
3. Updates UI elements when ViewModel changes
4. Uses @MainActor appropriately
5. Handles view lifecycle properly
6. Shows loading and error states
```

**Expected AI-Generated UIKit Code:**

```swift
import UIKit

@MainActor
class ProfileViewController: UIViewController {
    // MARK: - Properties
    private let viewModel: ProfileViewModel
    private let userId: String

    // MARK: - UI Elements
    private let scrollView = UIScrollView()
    private let contentStackView = UIStackView()
    private let avatarImageView = UIImageView()
    private let nameLabel = UILabel()
    private let emailLabel = UILabel()
    private let loadingIndicator = UIActivityIndicatorView(style: .large)
    private let errorLabel = UILabel()
    private let retryButton = UIButton(type: .system)

    // MARK: - Initialization
    init(userId: String, networkService: NetworkService) {
        self.userId = userId
        self.viewModel = ProfileViewModel(networkService: networkService)
        super.init(nibName: nil, bundle: nil)
    }

    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

    // MARK: - Lifecycle
    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
        setupConstraints()
        observeViewModel()

        Task {
            await viewModel.loadProfile(userId: userId)
        }
    }

    // MARK: - Setup
    private func setupUI() {
        view.backgroundColor = .systemBackground
        title = "Profile"

        // Configure stack view
        contentStackView.axis = .vertical
        contentStackView.spacing = 16
        contentStackView.alignment = .leading

        // Configure avatar
        avatarImageView.contentMode = .scaleAspectFill
        avatarImageView.clipsToBounds = true
        avatarImageView.layer.cornerRadius = 40
        avatarImageView.backgroundColor = .systemGray5

        // Configure labels
        nameLabel.font = .preferredFont(forTextStyle: .title2)
        nameLabel.numberOfLines = 0

        emailLabel.font = .preferredFont(forTextStyle: .subheadline)
        emailLabel.textColor = .secondaryLabel
        emailLabel.numberOfLines = 0

        // Configure error UI
        errorLabel.textAlignment = .center
        errorLabel.numberOfLines = 0
        errorLabel.textColor = .systemRed
        errorLabel.isHidden = true

        retryButton.setTitle("Retry", for: .normal)
        retryButton.isHidden = true
        retryButton.addAction(UIAction { [weak self] _ in
            guard let self = self else { return }
            Task {
                await self.viewModel.loadProfile(userId: self.userId)
            }
        }, for: .primaryActionTriggered)

        // Add subviews
        view.addSubview(scrollView)
        scrollView.addSubview(contentStackView)
        view.addSubview(loadingIndicator)
        view.addSubview(errorLabel)
        view.addSubview(retryButton)
    }

    private func setupConstraints() {
        scrollView.translatesAutoresizingMaskIntoConstraints = false
        contentStackView.translatesAutoresizingMaskIntoConstraints = false
        loadingIndicator.translatesAutoresizingMaskIntoConstraints = false
        errorLabel.translatesAutoresizingMaskIntoConstraints = false
        retryButton.translatesAutoresizingMaskIntoConstraints = false

        NSLayoutConstraint.activate([
            scrollView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            scrollView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            scrollView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            scrollView.bottomAnchor.constraint(equalTo: view.bottomAnchor),

            contentStackView.topAnchor.constraint(equalTo: scrollView.topAnchor, constant: 20),
            contentStackView.leadingAnchor.constraint(equalTo: scrollView.leadingAnchor, constant: 20),
            contentStackView.trailingAnchor.constraint(equalTo: scrollView.trailingAnchor, constant: -20),
            contentStackView.bottomAnchor.constraint(equalTo: scrollView.bottomAnchor, constant: -20),
            contentStackView.widthAnchor.constraint(equalTo: scrollView.widthAnchor, constant: -40),

            loadingIndicator.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            loadingIndicator.centerYAnchor.constraint(equalTo: view.centerYAnchor),

            errorLabel.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            errorLabel.centerYAnchor.constraint(equalTo: view.centerYAnchor, constant: -30),
            errorLabel.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 40),
            errorLabel.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -40),

            retryButton.topAnchor.constraint(equalTo: errorLabel.bottomAnchor, constant: 20),
            retryButton.centerXAnchor.constraint(equalTo: view.centerXAnchor)
        ])
    }

    // MARK: - Observation (iOS 26+ feature)
    private func observeViewModel() {
        // iOS 26+ updateProperties API integrates with @Observable
        updateProperties {
            // Update loading state
            if viewModel.isLoading {
                loadingIndicator.startAnimating()
                contentStackView.isHidden = true
                errorLabel.isHidden = true
                retryButton.isHidden = true
            } else {
                loadingIndicator.stopAnimating()
            }

            // Update error state
            if let errorMessage = viewModel.errorMessage {
                errorLabel.text = errorMessage
                errorLabel.isHidden = false
                retryButton.isHidden = false
                contentStackView.isHidden = true
            } else {
                errorLabel.isHidden = true
                retryButton.isHidden = true
            }

            // Update profile content
            if let profile = viewModel.profile {
                updateProfileUI(with: profile)
                contentStackView.isHidden = false
            }
        }
    }

    private func updateProfileUI(with profile: UserProfile) {
        nameLabel.text = profile.name
        emailLabel.text = profile.email

        // Load avatar image
        if let url = URL(string: profile.avatarURL) {
            Task {
                do {
                    let (data, _) = try await URLSession.shared.data(from: url)
                    avatarImageView.image = UIImage(data: data)
                } catch {
                    avatarImageView.image = UIImage(systemName: "person.circle.fill")
                }
            }
        }

        // Clear and rebuild stack view
        contentStackView.arrangedSubviews.forEach { $0.removeFromSuperview() }

        let headerStack = UIStackView(arrangedSubviews: [avatarImageView, nameLabel])
        headerStack.axis = .horizontal
        headerStack.spacing = 16
        headerStack.alignment = .center

        contentStackView.addArrangedSubview(headerStack)
        contentStackView.addArrangedSubview(emailLabel)
    }
}
```

**Key iOS 26 Features:**
- `updateProperties()` method observes @Observable changes
- Automatic MainActor isolation for UI updates
- No manual KVO or notification center needed
- Integration with SwiftUI's Observation framework

### Step 6: Generate Concurrent UI Update Patterns with AI

Ask AI to demonstrate handling multiple concurrent operations while maintaining UI thread safety.

**Prompt:**
```
Extend ProfileViewModel with a method that loads multiple pieces of data concurrently
(profile, posts, followers) and updates the UI as each completes. Use:
1. Task groups for parallel execution
2. @MainActor isolation for UI updates
3. Partial result handling (update UI as each piece arrives)
4. Error handling that doesn't cancel other tasks
5. Progress tracking
```

**Expected AI-Generated Concurrent Pattern:**

```swift
extension ProfileViewModel {
    /// Loads all profile data concurrently with progress updates.
    ///
    /// This method demonstrates advanced @MainActor patterns:
    /// - Concurrent execution of independent tasks
    /// - Progressive UI updates as data arrives
    /// - Isolated error handling per task
    /// - MainActor isolation for all UI state updates
    func loadAllData(userId: String) async {
        isLoading = true
        errorMessage = nil
        loadingProgress = 0.0

        await withTaskGroup(of: LoadResult.self) { group in
            // Launch concurrent tasks
            group.addTask {
                do {
                    let profile: UserProfile = try await self.networkService.request(
                        endpoint: "/users/\(userId)",
                        method: .get,
                        body: nil as String?
                    )
                    return .profile(profile)
                } catch {
                    return .error("profile", error)
                }
            }

            group.addTask {
                do {
                    let posts: [Post] = try await self.networkService.request(
                        endpoint: "/users/\(userId)/posts",
                        method: .get,
                        body: nil as String?
                    )
                    return .posts(posts)
                } catch {
                    return .error("posts", error)
                }
            }

            group.addTask {
                do {
                    let followers: [User] = try await self.networkService.request(
                        endpoint: "/users/\(userId)/followers",
                        method: .get,
                        body: nil as String?
                    )
                    return .followers(followers)
                } catch {
                    return .error("followers", error)
                }
            }

            // Process results as they arrive
            var completedTasks = 0
            let totalTasks = 3

            for await result in group {
                completedTasks += 1

                // Update UI on MainActor (automatic due to class annotation)
                switch result {
                case .profile(let profile):
                    self.profile = profile
                case .posts(let posts):
                    self.posts = posts
                case .followers(let followers):
                    self.followers = followers
                case .error(let source, let error):
                    print("Failed to load \(source): \(error)")
                    // Don't set errorMessage - allow partial success
                }

                // Update progress
                loadingProgress = Double(completedTasks) / Double(totalTasks)
            }
        }

        isLoading = false

        // Only show error if everything failed
        if profile == nil && posts == nil && followers == nil {
            errorMessage = "Failed to load any data. Please try again."
        }
    }

    // Supporting types
    private enum LoadResult {
        case profile(UserProfile)
        case posts([Post])
        case followers([User])
        case error(String, Error)
    }
}
```

**Verify Concurrent Pattern Features:**
- Task group enables parallel execution
- UI updates happen progressively
- Each result processed independently
- All UI updates automatically on MainActor
- Partial failures handled gracefully

### Step 7: Ask AI to Generate Testing Patterns for MainActor Code

Request AI to create test cases that properly handle MainActor isolation.

**Prompt:**
```
Create Swift Testing test suite for ProfileViewModel that:
1. Tests MainActor-isolated methods correctly
2. Uses async test functions
3. Mocks the network service
4. Verifies UI state updates
5. Tests error handling
6. Checks concurrent loading behavior
7. Uses modern Swift Testing framework (@Test macro)
```

**Expected AI-Generated Tests:**

```swift
import Testing
@testable import YourApp

@Suite("Profile ViewModel Tests")
@MainActor
struct ProfileViewModelTests {

    @Test("Successfully loads profile data")
    func loadProfileSuccess() async throws {
        // Given
        let mockService = MockNetworkService()
        let expectedProfile = UserProfile(
            id: "123",
            name: "Jane Doe",
            email: "jane@example.com",
            avatarURL: "https://example.com/avatar.jpg",
            memberSince: Date()
        )
        await mockService.setResponse(expectedProfile, for: "/users/123")

        let viewModel = ProfileViewModel(networkService: mockService)

        // When
        await viewModel.loadProfile(userId: "123")

        // Then
        #expect(viewModel.profile?.id == "123")
        #expect(viewModel.profile?.name == "Jane Doe")
        #expect(viewModel.isLoading == false)
        #expect(viewModel.errorMessage == nil)
    }

    @Test("Handles network errors gracefully")
    func loadProfileError() async throws {
        // Given
        let mockService = MockNetworkService()
        await mockService.setError(NetworkError.httpError(statusCode: 500), for: "/users/123")

        let viewModel = ProfileViewModel(networkService: mockService)

        // When
        await viewModel.loadProfile(userId: "123")

        // Then
        #expect(viewModel.profile == nil)
        #expect(viewModel.errorMessage != nil)
        #expect(viewModel.isLoading == false)
    }

    @Test("Concurrent loading updates state progressively")
    func concurrentLoadingProgression() async throws {
        // Given
        let mockService = MockNetworkService()

        let profile = UserProfile(id: "123", name: "Test", email: "test@example.com",
                                  avatarURL: "", memberSince: Date())
        let posts = [Post(id: "1", content: "Hello", likes: 10, comments: 2, shares: 1)]
        let followers = [User(id: "2", name: "Follower")]

        await mockService.setResponse(profile, for: "/users/123")
        await mockService.setResponse(posts, for: "/users/123/posts")
        await mockService.setResponse(followers, for: "/users/123/followers")

        let viewModel = ProfileViewModel(networkService: mockService)

        // When
        await viewModel.loadAllData(userId: "123")

        // Then
        #expect(viewModel.profile != nil)
        #expect(viewModel.posts?.count == 1)
        #expect(viewModel.followers?.count == 1)
        #expect(viewModel.loadingProgress == 1.0)
        #expect(viewModel.isLoading == false)
    }

    @Test("Refresh clears existing data before reload")
    func refreshClearsData() async throws {
        // Given
        let mockService = MockNetworkService()
        let profile = UserProfile(id: "123", name: "Test", email: "test@example.com",
                                  avatarURL: "", memberSince: Date())
        await mockService.setResponse(profile, for: "/users/123")

        let viewModel = ProfileViewModel(networkService: mockService)
        await viewModel.loadProfile(userId: "123")

        #expect(viewModel.profile != nil, "Initial profile should load")

        // When
        await viewModel.refresh(userId: "123")

        // Then
        #expect(viewModel.profile != nil, "Profile should reload after refresh")

        let callCount = await mockService.callCount(for: "/users/123")
        #expect(callCount == 2, "Should call endpoint twice (load + refresh)")
    }

    @Test("Nonisolated analytics calculation runs in background")
    func analyticsCalculationBackground() async throws {
        // Given
        let mockService = MockNetworkService()
        let viewModel = ProfileViewModel(networkService: mockService)

        let posts = [
            Post(id: "1", content: "Post 1", likes: 100, comments: 10, shares: 5),
            Post(id: "2", content: "Post 2", likes: 200, comments: 20, shares: 10),
            Post(id: "3", content: "Post 3", likes: 150, comments: 15, shares: 7)
        ]

        let profile = UserProfile(id: "123", name: "Test", email: "test@example.com",
                                  avatarURL: "", memberSince: Date(), posts: posts)

        // When - nonisolated method can be called without MainActor
        let analytics = await viewModel.calculateAnalytics(for: profile)

        // Then
        #expect(analytics.totalPosts == 3)
        #expect(analytics.averageEngagement > 0)
        #expect(analytics.engagementTrend == "insufficient_data" ||
                analytics.engagementTrend == "increasing" ||
                analytics.engagementTrend == "decreasing")
    }
}
```

**Testing Best Practices Verified:**
- `@MainActor` on test suite ensures proper isolation
- All test methods are `async`
- Mock service properly isolated
- Tests verify MainActor state updates
- Progressive loading tested

### Step 8: Request AI to Audit and Optimize MainActor Usage

Ask AI to review your code for common @MainActor issues and suggest optimizations.

**Prompt:**
```
Review my ProfileViewModel and suggest optimizations for:
1. Unnecessary MainActor isolation
2. Potential main thread blocking
3. Missing nonisolated annotations
4. Actor hop optimizations
5. Swift 6.2 approachable concurrency patterns
6. Performance improvements
7. Sendable conformance issues

Provide before/after code examples for each suggestion.
```

**Expected AI Analysis and Suggestions:**

```swift
// OPTIMIZATION 1: Reduce actor context hops
// ❌ Before - Multiple hops between actors
@MainActor
func fetchAndProcess() async {
    let data = await networkService.fetchData()  // Hop to NetworkService actor
    processData(data)  // Back to MainActor
}

// ✅ After - Batch actor operations
@MainActor
func fetchAndProcess() async {
    // Stay on NetworkService actor for related operations
    let processedData = await networkService.fetchAndProcessData()
    updateUI(with: processedData)
}

// OPTIMIZATION 2: Use nonisolated for non-UI methods
// ❌ Before - Unnecessarily isolated to MainActor
@MainActor
class ViewModel {
    func parseJSON(_ data: Data) -> Model? {
        // This doesn't touch UI - shouldn't be MainActor
        try? JSONDecoder().decode(Model.self, from: data)
    }
}

// ✅ After - Nonisolated for pure functions
@MainActor
class ViewModel {
    nonisolated func parseJSON(_ data: Data) -> Model? {
        try? JSONDecoder().decode(Model.self, from: data)
    }
}

// OPTIMIZATION 3: Avoid blocking main thread with synchronous work
// ❌ Before - Heavy computation on MainActor
@MainActor
func processImages(_ images: [UIImage]) {
    let processed = images.map { processImage($0) }  // Blocks main thread!
    self.images = processed
}

// ✅ After - Offload to background, return to MainActor for UI update
@MainActor
func processImages(_ images: [UIImage]) async {
    let processed = await Task.detached {
        images.map { self.processImage($0) }
    }.value
    self.images = processed  // UI update on MainActor
}

// Make processImage nonisolated
nonisolated func processImage(_ image: UIImage) -> UIImage {
    // Heavy image processing
    return image
}

// OPTIMIZATION 4: Use Swift 6.2 default isolation for simple cases
// ❌ Before - Explicit MainActor everywhere
@MainActor
class SimpleViewModel {
    @MainActor var title: String = ""
    @MainActor var subtitle: String = ""

    @MainActor
    func updateTitle(_ newTitle: String) {
        title = newTitle
    }
}

// ✅ After - Class-level annotation covers all members
@MainActor
class SimpleViewModel {
    var title: String = ""
    var subtitle: String = ""

    func updateTitle(_ newTitle: String) {
        title = newTitle
    }
}

// OPTIMIZATION 5: Ensure Sendable conformance for types crossing actors
// ❌ Before - Non-Sendable type passed between actors
class UserData {  // Missing Sendable
    var name: String
    var email: String
}

// ✅ After - Sendable struct for safe actor crossing
struct UserData: Sendable {
    let name: String
    let email: String
}
```

## Expected Results

After completing these steps, you should have:

1. **Thread-safe UI code** with automatic MainActor isolation preventing data races
2. **AI-generated patterns** that follow Swift 6.2 best practices without manual review
3. **Optimal performance** with proper balance of main thread and background work
4. **Comprehensive tests** verifying MainActor behavior correctly
5. **Modern concurrency patterns** leveraging Swift 6.2's approachable concurrency features
6. **Clean, maintainable code** with clear isolation boundaries

**Visual Indicators of Success:**
- No "Publishing changes from background threads" warnings
- No data race warnings when building with Swift 6 language mode
- Smooth UI performance without main thread blocking
- Xcode's Thread Sanitizer shows no issues
- Tests pass reliably without timing issues

**Build Settings to Verify:**
```bash
# Enable complete concurrency checking
SWIFT_STRICT_CONCURRENCY = complete

# Enable Swift 6 language mode
SWIFT_VERSION = 6.0
```

## Troubleshooting

### Issue 1: "Expression is 'async' but is not marked with 'await'"

**Problem:** Calling MainActor-isolated async methods without await keyword.

**Solution:**
```swift
// ❌ Wrong
viewModel.loadProfile(userId: "123")

// ✅ Correct
await viewModel.loadProfile(userId: "123")

// Or wrap in Task if not in async context
Task {
    await viewModel.loadProfile(userId: "123")
}
```

**AI Assistance Tip:** Ask your AI: "Identify all missing await keywords for MainActor method calls in this file."

### Issue 2: "Call to main actor-isolated method requires 'await'"

**Problem:** Trying to call MainActor methods from non-isolated context synchronously.

**Solution:**
```swift
// ❌ Wrong - Non-isolated calling MainActor
nonisolated func helper() {
    updateUI()  // Error: updateUI is MainActor-isolated
}

// ✅ Correct - Make caller async and use await
nonisolated func helper() async {
    await updateUI()
}

// Or use MainActor.run
nonisolated func helper() {
    Task { @MainActor in
        updateUI()
    }
}
```

**AI Assistance Tip:** Prompt "Convert this synchronous method to async to properly call MainActor methods."

### Issue 3: "Main actor-isolated property cannot be mutated from a non-isolated context"

**Problem:** Attempting to modify MainActor properties from background tasks.

**Solution:**
```swift
// ❌ Wrong - Background task modifying MainActor property
Task.detached {
    viewModel.isLoading = false  // Error!
}

// ✅ Correct - Explicitly switch to MainActor
Task.detached {
    await MainActor.run {
        viewModel.isLoading = false
    }
}

// ✅ Better - Use regular Task which inherits MainActor
Task {
    viewModel.isLoading = false  // OK - Task inherits MainActor
}
```

**AI Assistance Tip:** Ask "Fix MainActor isolation errors in this task group implementation."

### Issue 4: AI Generates Legacy DispatchQueue.main Pattern

**Problem:** AI suggests old-style main queue dispatching instead of @MainActor.

**Solution:** Provide explicit instruction:
```
Please rewrite this using @MainActor and Swift 6.2 concurrency, NOT DispatchQueue.main.
Remove all GCD patterns and use structured concurrency with async/await.
```

**Example correction:**
```swift
// ❌ AI generated (outdated)
DispatchQueue.main.async {
    self.updateUI()
}

// ✅ Request this instead
@MainActor
func updateUI() {
    // UI updates
}

// Call with:
await updateUI()
```

### Issue 5: SwiftUI View Updates Not Reflecting MainActor Changes

**Problem:** Modifying @MainActor ViewModel properties but SwiftUI not re-rendering.

**Checklist:**
- ✓ ViewModel marked with both `@Observable` and `@MainActor`
- ✓ View using `@State` (not `@StateObject`) for view model
- ✓ Actually reading the property in view's `body`
- ✓ Running on iOS 17+ (for @Observable support)

**Solution:**
```swift
// Ensure both annotations present
@Observable
@MainActor
class ViewModel {
    var count: Int = 0
}

// Use @State in view
struct ContentView: View {
    @State private var viewModel = ViewModel()

    var body: some View {
        Text("\(viewModel.count)")  // Must read property here
    }
}
```

**AI Assistance Tip:** Prompt "Debug why my @Observable @MainActor ViewModel isn't updating SwiftUI views."

### Issue 6: Performance Issues from Excessive Main Thread Work

**Problem:** UI feels sluggish because too much work is MainActor-isolated.

**Solution:** Profile with Instruments and ask AI to optimize:
```
Analyze this ViewModel and identify methods that should be nonisolated
to run on background threads. Show me which operations can safely move
off the main actor without causing isolation issues.
```

**AI will suggest:**
- Data parsing → `nonisolated`
- Heavy computations → `nonisolated async`
- Pure transformations → `nonisolated`
- Network calls → Already actor-isolated, fine as-is
- UI updates → Keep `@MainActor`

## Additional Tips

- **Let AI explain isolation**: When AI generates complex MainActor code, ask "Explain the actor isolation strategy in this code and why each annotation is necessary."

- **Request documentation**: Prompt AI to "Add inline documentation explaining the MainActor isolation rationale for each method" to help future developers.

- **Use AI for migration**: Converting legacy code? Ask: "Convert this DispatchQueue-based code to use @MainActor and Swift 6.2 structured concurrency."

- **Verify with Xcode's concurrency checker**: After AI generates code, enable Thread Sanitizer (Edit Scheme → Diagnostics → Thread Sanitizer) to catch issues.

- **Ask AI for trade-offs**: Prompt "What are the performance trade-offs of marking this entire class @MainActor vs. individual methods?"

- **Generate multiple approaches**: Request "Show me 3 different ways to structure this code with @MainActor, and explain pros/cons of each."

- **Leverage Swift 6.2's default isolation**: For new projects in Xcode 26.1+, enable "Default Actor Isolation" compiler setting and ask AI to utilize it.

- **Use AI for code reviews**: Paste existing code and ask: "Audit this for Swift 6 concurrency issues, focusing on MainActor isolation and sendability."

- **Test isolation boundaries**: Ask AI to "Generate tests that verify proper actor isolation for this ViewModel."

- **Profile before optimizing**: Use Instruments' Time Profiler to identify actual bottlenecks before asking AI to optimize MainActor usage.

## Related Articles

- KB-082: How to implement async/await for network calls using AI assistance
- KB-079: How to use @Observable macro for state management (Swift 6)
- KB-085: How to ask AI to create mock data services for testing
- KB-083: How to use AI for error handling in API requests
- KB-084: How to build authentication flows with AI-generated code
- KB-053: How to ask AI to implement MVVM architecture for a feature
- KB-051: How to use AI to generate unit tests for existing code

## Sources

- Apple Developer Documentation - MainActor - https://developer.apple.com/documentation/Swift/MainActor (Accessed: November 17, 2025)
- Swift.org - Swift 6.2 Released - https://www.swift.org/blog/swift-6.2-released/ (Accessed: November 17, 2025)
- SwiftLee - Default Actor Isolation in Swift 6.2 - https://www.avanderlee.com/concurrency/default-actor-isolation-in-swift-6-2/ (Accessed: November 17, 2025)
- SwiftLee - MainActor usage in Swift explained to dispatch to the main thread - https://www.avanderlee.com/swift/mainactor-dispatch-main-thread/ (Accessed: November 17, 2025)
- Swift.org - Adopting Swift 6 - https://developer.apple.com/documentation/swift/adoptingswift6 (Accessed: November 17, 2025)
- InfoQ - Swift 6.2 Introduces Approachable Concurrency - https://www.infoq.com/news/2025/08/swift62-approachable-concurrency/ (Accessed: November 17, 2025)
- Hacking with Swift - How to use @MainActor to run code on the main queue - https://www.hackingwithswift.com/quick-start/concurrency/how-to-use-mainactor-to-run-code-on-the-main-queue (Accessed: November 17, 2025)
- Donny Wals - Dispatching to the Main thread with MainActor in Swift - https://www.donnywals.com/dispatching-to-the-main-thread-with-mainactor-in-swift/ (Accessed: November 17, 2025)
- Swift Pal - MainActor in Swift 6.2: Complete Guide to Thread Safety, New Features, and 2025 Best Practices - https://swift-pal.com/mainactor-in-swift-6-2-complete-guide-to-thread-safety-new-features-and-2025-best-practices-5a49eafce896 (Accessed: November 17, 2025)
- Medium - Understanding @MainActor in SwiftUI: A Practical Guide for Swift 6 - https://medium.com/@donatogomez88/understanding-mainactor-in-swiftui-a-practical-guide-for-swift-6-69e657872ec5 (Accessed: November 17, 2025)
- massicotte.org - Problematic Swift Concurrency Patterns - https://www.massicotte.org/problematic-patterns (Accessed: November 17, 2025)
- 9to5Mac - Xcode 26 will support multiple AI models, like Claude - https://9to5mac.com/2025/06/10/beyond-chatgpt-xcode-26-will-support-multiple-ai-models-like-claude/ (Accessed: November 17, 2025)
- SwiftLee - Approachable Concurrency in Swift 6.2: A Clear Guide - https://www.avanderlee.com/concurrency/approachable-concurrency-in-swift-6-2-a-clear-guide/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
