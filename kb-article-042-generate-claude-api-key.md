# How to generate an API key for Claude in the Anthropic console

**Article ID:** KB-042
**Difficulty:** Beginner-Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 5-10 minutes

## Overview
This article guides you through generating an API key in the Anthropic Console (console.anthropic.com), which is required to authenticate Claude API requests from Xcode 26.1+ Intelligence settings. API keys are workspace-scoped credentials that allow your development environment to securely access Claude's language models through the Anthropic API.

## Prerequisites
- Active Anthropic API account (see KB-041)
- Access to console.anthropic.com
- Appropriate workspace permissions (Developer, Admin, or Billing role)
- Payment method added to your account (required for API access)
- Funds added to your account balance (Anthropic uses a prepaid credit system)

## Steps

### Step 1: Access the Anthropic Console
1. Navigate to **https://console.anthropic.com** in your web browser
2. Sign in with your Anthropic account credentials
3. You will land on the Console dashboard

### Step 2: Navigate to API Keys Settings
1. In the left-hand navigation menu, locate and click on **"API Keys"** (or access directly via **https://console.anthropic.com/settings/keys**)
2. You will see a list of any existing API keys for the current workspace
3. If you have multiple workspaces, ensure you're in the correct workspace by checking the workspace selector at the top of the page

### Step 3: Create a New API Key
1. Click the **"Create Key"** button (typically located in the top-right corner of the API Keys page)
2. A dialog box will appear prompting you to name your API key

### Step 4: Name Your API Key
1. Enter a descriptive name that helps you identify the key's purpose
   - Examples: "Xcode-Development", "MacBook-Pro-Xcode", "Production-Xcode-26"
   - Use naming conventions that indicate the environment (dev/staging/prod) and tool (Xcode)
2. Click **"Create Key"** to generate the key

### Step 5: Copy and Securely Store the API Key
1. **CRITICAL**: The API key will be displayed **only once** in a dialog box
2. The key format starts with `sk-ant-api03-` followed by a long alphanumeric string
3. Click the **"Copy"** button or manually select and copy the entire key
4. **Immediately save** the key in a secure location:
   - Password manager (recommended)
   - Encrypted secrets management system
   - Secure note-taking application
5. **Do NOT** close the dialog until you have confirmed the key is saved
6. Once you close the dialog, you will never be able to view this key again

### Step 6: Verify Key Creation
1. After closing the dialog, you should see your newly created key listed on the API Keys page
2. The listing will show:
   - Key name (e.g., "Xcode-Development")
   - Key prefix (first few characters like `sk-ant-api03-...`)
   - Creation date
   - Last used date (initially showing "Never")
   - Actions menu (three dots icon)

## Expected Results

After successfully completing these steps, you should have:
- A new API key visible in your console.anthropic.com API Keys list
- The complete API key securely stored in your password manager or secrets system
- The key ready to use in Xcode Intelligence settings (see KB-043)

When you use this key in Xcode 26.1+ to authenticate with Claude:
- The key will appear in the "Last used" column with a timestamp
- API requests will successfully authenticate with the `x-api-key` header
- You'll be able to access Claude models specified in your Xcode Intelligence configuration

## Troubleshooting

### Issue: "Create Key" Button is Disabled or Not Visible
**Cause**: Insufficient workspace permissions
**Solution**:
- Verify you have Developer, Admin, or Billing role in the current workspace
- Contact your organization administrator to grant appropriate permissions
- Check the Roles and Permissions documentation: https://support.anthropic.com/en/articles/10186004-api-console-roles-and-permissions

### Issue: Cannot Access API Keys Page
**Cause**: Missing payment method or account funding
**Solution**:
- Navigate to **Plans and Billing** in the console
- Add a valid payment method
- Click **"Add Funds"** and prepay your account with credits
- Return to the API Keys page to create your key

### Issue: Lost or Forgot to Copy API Key
**Cause**: API keys are only shown once at creation
**Solution**:
- You **cannot** retrieve the original key value
- Delete the key you didn't copy (click the three-dot menu → "Delete API Key")
- Create a new API key and ensure you copy it before closing the dialog
- Consider using a password manager's browser extension to automatically capture it

### Issue: Authentication Error "invalid x-api-key"
**Cause**: Incorrect key format or key from wrong workspace
**Solution**:
- Verify the key starts with `sk-ant-api03-` (standard keys) or `sk-ant-admin-` (admin keys)
- Ensure you copied the complete key without extra spaces or line breaks
- Confirm you're using the key from the correct workspace
- Check that the key hasn't been deleted in the console

## Additional Tips

### Security Best Practices
1. **Treat API keys like passwords**: Never share them publicly or include in version control
2. **Use environment-specific keys**: Create separate keys for development, staging, and production
3. **Rotate keys regularly**: Create new keys and delete old ones every 90 days
4. **Monitor key usage**: Regularly review the "Last used" timestamps in the console
5. **Use .gitignore**: Ensure configuration files containing keys are excluded from git commits
6. **Prefer secrets management**: Use encrypted KMS (Key Management Systems) in cloud environments

### Key Naming Conventions
- Include the tool: `xcode-`, `cursor-`, `vscode-`
- Include the environment: `-dev`, `-staging`, `-prod`
- Include the machine: `-macbook-pro`, `-mac-mini`
- Example: `xcode-dev-macbook-pro-2025`

### Workspace Considerations
- API keys are scoped to the workspace where they're created
- Keys cannot be moved between workspaces
- Organization Admins automatically have permissions in all workspaces
- Each workspace can have independent rate limits

### Deleting Compromised Keys
If you suspect a key has been compromised:
1. Go to console.anthropic.com/settings/keys
2. Click the three-dot menu (⋯) next to the compromised key
3. Select **"Delete API Key"**
4. Immediately create a new key as a replacement
5. Update all systems using the old key with the new key

### Admin API Keys
- Admin API keys (starting with `sk-ant-admin-`) are different from standard keys
- Only organization members with Admin role can create Admin API keys
- Admin keys are required for using the Administration API
- For Xcode Intelligence, you typically need standard API keys, not Admin keys

## Related Articles
- KB-041: How to create an Anthropic API account at console.anthropic.com
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-045: How to set the API key header (x-api-key) for Claude authentication
- KB-044: How to configure Claude model parameters in Xcode settings

## Sources
1. Anthropic Console API Keys - https://console.anthropic.com/settings/keys (Accessed: November 17, 2025)
2. Claude Documentation - Getting Started - https://docs.claude.com/en/docs/get-started (Accessed: November 17, 2025)
3. Anthropic Support - API Key Best Practices - https://support.anthropic.com/en/articles/9767949-api-key-best-practices-keeping-your-keys-safe-and-secure (Accessed: November 17, 2025)
4. Anthropic Support - Compromised API Keys - https://support.anthropic.com/en/articles/8384961-what-should-i-do-if-i-suspect-my-api-key-has-been-compromised (Accessed: November 17, 2025)
5. Anthropic Support - Console Roles and Permissions - https://support.anthropic.com/en/articles/10186004-api-console-roles-and-permissions (Accessed: November 17, 2025)
6. Anthropic News - Workspaces in the API Console - https://www.anthropic.com/news/workspaces (Accessed: November 17, 2025)
7. Anthropic Support - Creating and Managing Workspaces - https://support.anthropic.com/en/articles/9796807-creating-and-managing-workspaces (Accessed: November 17, 2025)
8. Various third-party tutorials and guides (November 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
