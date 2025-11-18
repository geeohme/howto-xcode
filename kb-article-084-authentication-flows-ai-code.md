# How to implement authentication flows with AI-generated code

**Article ID:** KB-084
**Difficulty:** Intermediate-Advanced
**Last Updated:** November 17, 2025
**Estimated Time:** 40-45 minutes

## Overview

Modern iOS apps require secure, user-friendly authentication flows. This guide demonstrates how to leverage AI coding assistants in Xcode 26.1+ to generate production-ready authentication code using Apple's latest AuthenticationServices framework, including Sign in with Apple, passkeys, and OAuth 2.1 flows. You'll learn to prompt AI assistants effectively for secure authentication implementations that follow iOS 18.2+ best practices.

## Prerequisites

- Xcode 26.1+ installed with an active coding assistant (ChatGPT or Claude)
- An active iOS app project with signing configured
- Basic understanding of Swift and SwiftUI
- Apple Developer account (for Sign in with Apple capability)
- Related articles: KB-031 (Access coding assistant), KB-043 (Add Claude), KB-010 (Signing and capabilities)

## Steps

### Step 1: Configure Your Project for Authentication

Before generating code, add the necessary capabilities to your project.

1. Open your project in Xcode 26.1+
2. Select your app target in the Project Navigator
3. Click the **Signing & Capabilities** tab
4. Click **+ Capability** and add:
   - **Sign in with Apple** (for Apple authentication)
   - **Associated Domains** (for passkeys, format: `webcredentials:yourdomain.com`)
5. In your `Info.plist`, ensure you have appropriate privacy descriptions for any sensitive data

```swift
// Privacy keys to add in Info.plist if using Face ID/Touch ID:
// NSFaceIDUsageDescription: "We use Face ID to securely authenticate you"
```

### Step 2: Prompt AI to Generate Sign in with Apple Implementation

Use your AI assistant to generate the core authentication manager. Open the coding assistant panel and provide this structured prompt:

**Prompt for AI:**
```
Create a SignInWithAppleManager class for iOS 18.2+ using the latest
AuthenticationServices framework. Requirements:
- SwiftUI compatible using @Observable macro
- Handle authorization with ASAuthorizationAppleIDProvider
- Request .fullName and .email scopes
- Implement ASAuthorizationControllerDelegate
- Secure token storage in Keychain
- Error handling with specific AuthenticationError cases
- Follow Apple's 2025 security best practices
```

The AI will generate code similar to this:

```swift
import AuthenticationServices
import SwiftUI

@Observable
class SignInWithAppleManager: NSObject {
    var userID: String?
    var email: String?
    var fullName: PersonNameComponents?
    var isAuthenticated = false
    var errorMessage: String?

    enum AuthenticationError: Error, LocalizedError {
        case failed
        case cancelled
        case invalidResponse
        case keychainError

        var errorDescription: String? {
            switch self {
            case .failed: return "Authentication failed. Please try again."
            case .cancelled: return "Sign in was cancelled."
            case .invalidResponse: return "Invalid response from Apple."
            case .keychainError: return "Failed to save credentials securely."
            }
        }
    }

    func signIn() {
        let provider = ASAuthorizationAppleIDProvider()
        let request = provider.createRequest()
        request.requestedScopes = [.fullName, .email]

        let controller = ASAuthorizationController(authorizationRequests: [request])
        controller.delegate = self
        controller.performRequests()
    }

    func checkCredentialState() async {
        guard let userID = self.userID else { return }
        let provider = ASAuthorizationAppleIDProvider()

        do {
            let state = try await provider.credentialState(forUserID: userID)
            switch state {
            case .authorized:
                self.isAuthenticated = true
            case .revoked, .notFound:
                self.isAuthenticated = false
                self.userID = nil
            default:
                break
            }
        } catch {
            self.errorMessage = "Failed to check credential state"
        }
    }
}

extension SignInWithAppleManager: ASAuthorizationControllerDelegate {
    func authorizationController(controller: ASAuthorizationController,
                                didCompleteWithAuthorization authorization: ASAuthorization) {
        if let appleIDCredential = authorization.credential as? ASAuthorizationAppleIDCredential {
            self.userID = appleIDCredential.user
            self.email = appleIDCredential.email
            self.fullName = appleIDCredential.fullName
            self.isAuthenticated = true

            // Store user ID securely in Keychain
            KeychainManager.shared.save(userID: appleIDCredential.user)

            // Send identity token to your backend for verification
            if let identityToken = appleIDCredential.identityToken,
               let tokenString = String(data: identityToken, encoding: .utf8) {
                // Send tokenString to your backend
                Task {
                    await verifyTokenWithBackend(token: tokenString)
                }
            }
        }
    }

    func authorizationController(controller: ASAuthorizationController,
                                didCompleteWithError error: Error) {
        if let authError = error as? ASAuthorizationError {
            switch authError.code {
            case .canceled:
                self.errorMessage = AuthenticationError.cancelled.localizedDescription
            case .failed:
                self.errorMessage = AuthenticationError.failed.localizedDescription
            default:
                self.errorMessage = error.localizedDescription
            }
        }
        self.isAuthenticated = false
    }

    private func verifyTokenWithBackend(token: String) async {
        // Implement backend verification here
        // Use URLSession with HTTPS and certificate pinning
    }
}
```

### Step 3: Generate Keychain Storage Manager

Prompt the AI to create a secure storage solution:

**Prompt for AI:**
```
Create a KeychainManager singleton for iOS 18+ that securely stores
authentication tokens using kSecAttrAccessibleWhenUnlockedThisDeviceOnly.
Include methods to save, retrieve, and delete user credentials. Use modern
async/await and proper error handling.
```

Generated code:

```swift
import Security
import Foundation

class KeychainManager {
    static let shared = KeychainManager()
    private init() {}

    private let service = Bundle.main.bundleIdentifier ?? "com.app.auth"

    enum KeychainError: Error {
        case saveFailed
        case retrievalFailed
        case deletionFailed
        case itemNotFound
    }

    func save(userID: String, account: String = "appleUserID") throws {
        let data = Data(userID.utf8)

        let query: [String: Any] = [
            kSecClass as String: kSecClassGenericPassword,
            kSecAttrService as String: service,
            kSecAttrAccount as String: account,
            kSecValueData as String: data,
            kSecAttrAccessible as String: kSecAttrAccessibleWhenUnlockedThisDeviceOnly
        ]

        // Delete existing item if present
        SecItemDelete(query as CFDictionary)

        let status = SecItemAdd(query as CFDictionary, nil)
        guard status == errSecSuccess else {
            throw KeychainError.saveFailed
        }
    }

    func retrieve(account: String = "appleUserID") throws -> String {
        let query: [String: Any] = [
            kSecClass as String: kSecClassGenericPassword,
            kSecAttrService as String: service,
            kSecAttrAccount as String: account,
            kSecReturnData as String: true,
            kSecMatchLimit as String: kSecMatchLimitOne
        ]

        var result: AnyObject?
        let status = SecItemCopyMatching(query as CFDictionary, &result)

        guard status == errSecSuccess,
              let data = result as? Data,
              let userID = String(data: data, encoding: .utf8) else {
            throw KeychainError.itemNotFound
        }

        return userID
    }

    func delete(account: String = "appleUserID") throws {
        let query: [String: Any] = [
            kSecClass as String: kSecClassGenericPassword,
            kSecAttrService as String: service,
            kSecAttrAccount as String: account
        ]

        let status = SecItemDelete(query as CFDictionary)
        guard status == errSecSuccess || status == errSecItemNotFound else {
            throw KeychainError.deletionFailed
        }
    }
}
```

### Step 4: Create SwiftUI Sign In Button View

Ask the AI to generate the UI component:

**Prompt for AI:**
```
Create a SwiftUI view that implements Sign in with Apple button using
SignInWithAppleButton from AuthenticationServices. The view should use
@Environment to access the SignInWithAppleManager and handle all
authentication states: loading, authenticated, and error.
```

```swift
import SwiftUI
import AuthenticationServices

struct SignInView: View {
    @State private var authManager = SignInWithAppleManager()

    var body: some View {
        VStack(spacing: 20) {
            if authManager.isAuthenticated {
                authenticatedView
            } else {
                signInButton
            }

            if let errorMessage = authManager.errorMessage {
                Text(errorMessage)
                    .foregroundStyle(.red)
                    .font(.caption)
                    .multilineTextAlignment(.center)
            }
        }
        .padding()
        .task {
            await authManager.checkCredentialState()
        }
    }

    private var signInButton: some View {
        SignInWithAppleButton(.signIn) { request in
            request.requestedScopes = [.fullName, .email]
        } onCompletion: { result in
            switch result {
            case .success(let authorization):
                authManager.authorizationController(
                    controller: ASAuthorizationController(authorizationRequests: []),
                    didCompleteWithAuthorization: authorization
                )
            case .failure(let error):
                authManager.errorMessage = error.localizedDescription
            }
        }
        .signInWithAppleButtonStyle(.black)
        .frame(height: 50)
        .padding(.horizontal)
    }

    private var authenticatedView: some View {
        VStack(spacing: 16) {
            Image(systemName: "checkmark.circle.fill")
                .font(.system(size: 60))
                .foregroundStyle(.green)

            Text("Signed In Successfully")
                .font(.title2)
                .fontWeight(.semibold)

            if let email = authManager.email {
                Text(email)
                    .font(.subheadline)
                    .foregroundStyle(.secondary)
            }

            Button("Sign Out") {
                Task {
                    try? KeychainManager.shared.delete()
                    authManager.userID = nil
                    authManager.isAuthenticated = false
                }
            }
            .buttonStyle(.bordered)
        }
    }
}
```

### Step 5: Implement Passkeys Support (iOS 18+)

For modern passwordless authentication, prompt AI to add passkeys:

**Prompt for AI:**
```
Add passkeys support to the authentication manager for iOS 18+. Use
ASAuthorizationPlatformPublicKeyCredentialProvider for registration and
authentication. Include challenge-response pattern with backend integration.
Follow FIDO2 WebAuthn standards.
```

```swift
import AuthenticationServices

extension SignInWithAppleManager {
    func registerPasskey(username: String) async throws {
        // Fetch challenge from your backend
        let challenge = try await fetchChallengeFromBackend()

        let provider = ASAuthorizationPlatformPublicKeyCredentialProvider(
            relyingPartyIdentifier: "yourdomain.com"
        )

        let registrationRequest = provider.createCredentialRegistrationRequest(
            challenge: challenge,
            name: username,
            userID: Data(username.utf8)
        )

        let controller = ASAuthorizationController(authorizationRequests: [registrationRequest])
        controller.delegate = self
        controller.performRequests()
    }

    func signInWithPasskey() async throws {
        let challenge = try await fetchChallengeFromBackend()

        let provider = ASAuthorizationPlatformPublicKeyCredentialProvider(
            relyingPartyIdentifier: "yourdomain.com"
        )

        let assertionRequest = provider.createCredentialAssertionRequest(
            challenge: challenge
        )

        let controller = ASAuthorizationController(authorizationRequests: [assertionRequest])
        controller.delegate = self
        controller.performRequests()
    }

    private func fetchChallengeFromBackend() async throws -> Data {
        // Implement secure HTTPS call to your backend
        // Backend should generate cryptographically secure random challenge
        guard let url = URL(string: "https://api.yourdomain.com/auth/challenge") else {
            throw AuthenticationError.invalidResponse
        }

        let (data, _) = try await URLSession.shared.data(from: url)
        return data
    }
}
```

### Step 6: Add Biometric Authentication for Token Access

Enhance security with Face ID/Touch ID:

**Prompt for AI:**
```
Add LocalAuthentication framework support to require biometric
authentication before accessing stored tokens. Use LAContext with
biometryType detection and proper fallback for devices without biometrics.
```

```swift
import LocalAuthentication

extension KeychainManager {
    func retrieveWithBiometrics(account: String = "appleUserID") async throws -> String {
        let context = LAContext()
        var error: NSError?

        guard context.canEvaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, error: &error) else {
            // Fallback to retrieve without biometrics or handle error
            return try retrieve(account: account)
        }

        let reason = "Authenticate to access your account"

        do {
            let success = try await context.evaluatePolicy(
                .deviceOwnerAuthenticationWithBiometrics,
                localizedReason: reason
            )

            if success {
                return try retrieve(account: account)
            } else {
                throw KeychainError.retrievalFailed
            }
        } catch {
            throw KeychainError.retrievalFailed
        }
    }
}
```

### Step 7: Validate and Test AI-Generated Code

Critical security review process:

1. **Review authentication flow**: Ensure all user data flows through HTTPS
2. **Verify token handling**: Tokens should never be logged or stored in UserDefaults
3. **Test error scenarios**: Network failures, user cancellation, invalid tokens
4. **Check Keychain security**: Verify `kSecAttrAccessibleWhenUnlockedThisDeviceOnly` is used
5. **Backend validation**: Identity tokens MUST be validated server-side

**Ask AI to generate test cases:**

**Prompt for AI:**
```
Generate XCTest unit tests for SignInWithAppleManager that mock
ASAuthorizationController responses and verify proper error handling,
token storage, and state management.
```

```swift
import XCTest
@testable import YourApp

final class SignInWithAppleManagerTests: XCTestCase {
    var sut: SignInWithAppleManager!

    override func setUp() {
        super.setUp()
        sut = SignInWithAppleManager()
    }

    override func tearDown() {
        try? KeychainManager.shared.delete()
        sut = nil
        super.tearDown()
    }

    func testInitialState() {
        XCTAssertNil(sut.userID)
        XCTAssertFalse(sut.isAuthenticated)
        XCTAssertNil(sut.errorMessage)
    }

    func testSuccessfulAuthentication() {
        // Mock authorization credential
        sut.userID = "test-user-id"
        sut.isAuthenticated = true

        XCTAssertNotNil(sut.userID)
        XCTAssertTrue(sut.isAuthenticated)
    }

    func testKeychainStorage() throws {
        let testUserID = "test-user-12345"

        // Save
        try KeychainManager.shared.save(userID: testUserID)

        // Retrieve
        let retrieved = try KeychainManager.shared.retrieve()

        XCTAssertEqual(retrieved, testUserID)
    }

    func testKeychainDeletion() throws {
        try KeychainManager.shared.save(userID: "test-user")
        try KeychainManager.shared.delete()

        XCTAssertThrowsError(try KeychainManager.shared.retrieve()) { error in
            XCTAssertEqual(error as? KeychainManager.KeychainError, .itemNotFound)
        }
    }
}
```

### Step 8: Implement Backend Token Verification

Ask AI to help with backend integration:

**Prompt for AI:**
```
Create a NetworkManager that verifies Sign in with Apple identity tokens
with my backend using iOS 18 URLSession. Include certificate pinning,
proper error handling, and token refresh logic. Use async/await and modern
Codable responses.
```

```swift
import Foundation

class AuthNetworkManager {
    static let shared = AuthNetworkManager()
    private init() {}

    struct TokenVerificationRequest: Codable {
        let identityToken: String
        let authorizationCode: String?
        let user: String
    }

    struct TokenVerificationResponse: Codable {
        let accessToken: String
        let refreshToken: String
        let expiresIn: Int
        let tokenType: String
    }

    enum NetworkError: Error, LocalizedError {
        case invalidURL
        case invalidResponse
        case unauthorized
        case serverError(statusCode: Int)

        var errorDescription: String? {
            switch self {
            case .invalidURL: return "Invalid server URL"
            case .invalidResponse: return "Invalid server response"
            case .unauthorized: return "Authentication failed"
            case .serverError(let code): return "Server error: \(code)"
            }
        }
    }

    func verifyToken(identityToken: String, authCode: String?, userID: String) async throws -> TokenVerificationResponse {
        guard let url = URL(string: "https://api.yourdomain.com/auth/apple/verify") else {
            throw NetworkError.invalidURL
        }

        var request = URLRequest(url: url)
        request.httpMethod = "POST"
        request.setValue("application/json", forHTTPHeaderField: "Content-Type")

        let body = TokenVerificationRequest(
            identityToken: identityToken,
            authorizationCode: authCode,
            user: userID
        )
        request.httpBody = try JSONEncoder().encode(body)

        // Use URLSession with TLS 1.3 minimum
        let config = URLSessionConfiguration.default
        config.tlsMinimumSupportedProtocolVersion = .TLSv13
        let session = URLSession(configuration: config)

        let (data, response) = try await session.data(for: request)

        guard let httpResponse = response as? HTTPURLResponse else {
            throw NetworkError.invalidResponse
        }

        switch httpResponse.statusCode {
        case 200...299:
            return try JSONDecoder().decode(TokenVerificationResponse.self, from: data)
        case 401:
            throw NetworkError.unauthorized
        default:
            throw NetworkError.serverError(statusCode: httpResponse.statusCode)
        }
    }
}
```

## Expected Results

After implementing AI-generated authentication code, you should have:

1. **Functional Sign in with Apple**: Tapping the button presents Apple's authentication sheet, successfully authenticates, and stores credentials securely
2. **Secure token storage**: User credentials saved in Keychain with device-only accessibility
3. **Passkeys support**: Optional passwordless authentication for returning users
4. **Biometric protection**: Face ID/Touch ID required to access stored credentials
5. **Error handling**: Clear error messages for all failure scenarios
6. **Backend integration**: Identity tokens verified server-side before granting access
7. **Clean UI states**: Loading, authenticated, and error states properly displayed

Test by:
- Authenticating successfully with Sign in with Apple
- Force-quitting and reopening app (credentials should persist)
- Testing on device without biometrics
- Disconnecting network during authentication
- Checking Keychain contents in device settings

## Troubleshooting

### Common Issue 1: "Sign in with Apple" Button Not Appearing
**Problem:** The SignInWithAppleButton doesn't render or crashes immediately
**Solution:**
- Verify "Sign in with Apple" capability is added in Signing & Capabilities
- Ensure you're testing on a device/simulator signed into an Apple ID
- Check that `import AuthenticationServices` is present
- For Xcode Previews, wrap button in a conditional: `if ProcessInfo.processInfo.environment["XCODE_RUNNING_FOR_PREVIEWS"] == nil`

### Common Issue 2: Keychain Saving Fails with Status -25300
**Problem:** KeychainManager throws saveFailed error
**Solution:**
- This is `errSecDuplicateItem` - the query should delete existing items first (code above handles this)
- If persists, check that Bundle Identifier is set correctly
- For simulator testing, reset the simulator: Device > Erase All Content and Settings
- Verify you're not trying to save empty or invalid data

### Common Issue 3: AI Generates Deprecated Authentication Code
**Problem:** AI suggests `ASWebAuthenticationSession` for Sign in with Apple
**Solution:**
- Be specific in prompts: "Use ASAuthorizationAppleIDProvider, not ASWebAuthenticationSession"
- Reference the version: "for iOS 18.2+ using Xcode 26.1+"
- If AI uses deprecated APIs, ask: "Update this code to use the latest AuthenticationServices APIs from iOS 18"
- Cross-reference with Apple's official documentation links provided in Sources

### Common Issue 4: Backend Token Verification Always Fails
**Problem:** Identity tokens rejected by your backend
**Solution:**
- Identity tokens are JWT format - decode at https://jwt.io to verify contents
- Ensure your backend validates tokens against Apple's public keys: https://appleid.apple.com/auth/keys
- Tokens are single-use - don't retry with the same token
- Check token hasn't expired (typically 10 minutes validity)
- Verify your backend accepts the correct `aud` (audience) and `iss` (issuer) claims

### Common Issue 5: Passkeys Not Available on Device
**Problem:** Passkey registration fails or option doesn't appear
**Solution:**
- Passkeys require iOS 16.0+, but automatic upgrades need iOS 18.0+
- Verify Associated Domains capability includes `webcredentials:yourdomain.com`
- Domain must serve `apple-app-site-association` file at `https://yourdomain.com/.well-known/apple-app-site-association`
- Check device has iCloud Keychain enabled in Settings
- Simulator testing may be limited - test on physical device

## Additional Tips

- **Prompt engineering for security**: Always include "following iOS security best practices" and specify the iOS version in your AI prompts to get modern, secure code
- **Incremental generation**: Generate one component at a time (manager, then Keychain, then UI) rather than asking for the entire system at once - easier to review
- **AI code review**: Ask the AI to review its own code: "Review this authentication code for security vulnerabilities and suggest improvements"
- **Model selection**: Claude models (especially Claude Sonnet 3.5+) tend to provide more detailed security considerations; GPT-4 Turbo is faster for boilerplate
- **Token refresh**: Implement refresh token logic before production - access tokens typically expire in 15-60 minutes
- **Testing environment**: Use a staging environment for backend integration testing; never test authentication flows in production
- **Multi-factor authentication**: For sensitive apps, combine passkeys/Sign in with Apple with SMS or email verification
- **Privacy compliance**: Always request minimal scopes - only ask for email/name if your app genuinely needs it
- **Fallback authentication**: Implement at least two authentication methods (e.g., Sign in with Apple + Email/Password) for users who can't use primary method
- **Session management**: Use `@Observable` with SwiftUI for reactive authentication state across your app
- **Logging**: Never log tokens, credentials, or PII; use redacted logging for debugging: `Logger.auth.debug("Token received: [REDACTED]")`

## Related Articles

- KB-031: How to access the coding assistant in Xcode 26
- KB-043: How to add Claude as a model provider in Xcode
- KB-010: How to set up signing and capabilities for your app project
- KB-037: How to generate SwiftUI views with ChatGPT
- KB-051: How to use AI to generate unit tests for existing code
- KB-055: How to have AI explain error messages and suggest fixes
- KB-058: How to ask AI to add comprehensive error handling to functions

## Sources

- Implementing User Authentication with Sign in with Apple - https://developer.apple.com/documentation/authenticationservices/implementing-user-authentication-with-sign-in-with-apple (Accessed: November 17, 2025)
- Supporting passkeys - https://developer.apple.com/documentation/authenticationservices/supporting-passkeys (Accessed: November 17, 2025)
- Connecting to a service with passkeys - https://developer.apple.com/documentation/authenticationservices/connecting-to-a-service-with-passkeys (Accessed: November 17, 2025)
- Public-Private Key Authentication - https://developer.apple.com/documentation/authenticationservices/public-private-key-authentication (Accessed: November 17, 2025)
- Authentication Services Framework - https://developer.apple.com/documentation/authenticationservices (Accessed: November 17, 2025)
- Mobile App Security Best Practices 2025 - https://isitdev.com/mobile-app-security-best-practices-2025/ (Accessed: November 17, 2025)
- iOS 18 Passkeys Automatic Passkey Upgrades - https://www.corbado.com/blog/ios-18-passkeys-automatic-passkey-upgrades (Accessed: November 17, 2025)
- 8 Mobile Authentication Best Practices for 2025 - https://nextnative.dev/blog/mobile-authentication-best-practices (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
