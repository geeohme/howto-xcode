# How to manage multiple API keys for different providers

**Article ID:** KB-067
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 20-30 minutes

## Overview
Xcode 26.1+ Intelligence settings allow you to configure multiple AI model providers simultaneously, giving you access to different AI capabilities from OpenAI (ChatGPT), Anthropic (Claude), Google (Gemini), and other providers through services like OpenRouter. This guide walks you through setting up, managing, and organizing multiple API keys for different providers, enabling you to leverage the unique strengths of each AI model within a single Xcode installation.

## Prerequisites
- Xcode 26.1 or later (version 26.1.1+ recommended)
- macOS Sequoia 15.5+ or later
- Apple Intelligence enabled in System Settings (see KB-003)
- API accounts and keys from desired providers:
  - OpenAI account with API key (for ChatGPT models)
  - Anthropic account with API key (for Claude models)
  - Google Cloud account with API key (for Gemini models)
  - OpenRouter account with API key (for multi-provider access)
- Password manager or secure storage for API keys (recommended)
- Internet connection

## Steps

### Step 1: Plan Your Provider Strategy
Before adding multiple providers, determine which AI models best serve your development needs:

1. **Identify your use cases:**
   - General coding: ChatGPT (GPT-4.1, GPT-5)
   - Complex refactoring: Claude Sonnet 4
   - Large context analysis: Claude Opus
   - Cost-effective solutions: Google Gemini 1.5 Flash
   - Experimental models: OpenRouter multi-provider access

2. **Assess budget considerations:**
   - Review pricing at each provider's website
   - Consider usage patterns (requests per day/month)
   - Plan for rate limits and quota management

3. **Create a naming convention** for your API keys that includes:
   - Provider name (e.g., "openai-", "anthropic-", "google-")
   - Environment (e.g., "-dev", "-prod")
   - Machine identifier (e.g., "-macbook-pro")
   - Example: `anthropic-xcode-dev-macbook-2025`

### Step 2: Generate API Keys from Each Provider

#### For OpenAI (ChatGPT)
1. Navigate to **https://platform.openai.com/api-keys**
2. Sign in with your OpenAI account
3. Click **"Create new secret key"**
4. Name the key: `xcode-dev-[your-machine]`
5. Copy the key (format: `sk-proj-...` or `sk-...`)
6. Store securely in your password manager

#### For Anthropic (Claude)
1. Navigate to **https://console.anthropic.com/settings/keys**
2. Sign in with your Anthropic account
3. Click **"Create Key"**
4. Name the key: `xcode-dev-[your-machine]`
5. Copy the key (format: `sk-ant-api03-...`)
6. Store securely in your password manager
7. See KB-042 for detailed instructions

#### For Google (Gemini)
1. Navigate to **https://aistudio.google.com/apikey**
2. Sign in with your Google account
3. Click **"Create API key"** or **"Get API key"**
4. Select or create a Google Cloud project
5. Copy the generated API key
6. Store securely in your password manager

#### For OpenRouter (Multi-Provider)
1. Navigate to **https://openrouter.ai/keys**
2. Sign in or create an account
3. Click **"Create Key"**
4. Name the key: `xcode-dev-[your-machine]`
5. Copy the key (format: `sk-or-v1-...`)
6. Store securely in your password manager

**CRITICAL:** Save all keys immediately. Most providers only show keys once at creation time.

### Step 3: Access Xcode Intelligence Settings
Open the Intelligence configuration panel where you'll add all providers:

1. Launch **Xcode 26.1** or later
2. Click **Xcode** in the menu bar
3. Select **Settings** (⌘+,)
4. Click the **Intelligence** tab at the top

You should see the Intelligence settings panel with any existing providers (ChatGPT is typically configured by default).

### Step 4: Add OpenAI Provider (If Not Already Configured)
Configure or verify your OpenAI connection:

1. In Intelligence settings, check if OpenAI/ChatGPT is already listed
2. If not, click the **"+"** button or **"Add a Model Provider"**
3. Select **"Internet Hosted"**
4. Fill in the configuration:
   - **Provider Name:** OpenAI
   - **API Endpoint URL:** `https://api.openai.com/v1`
   - **API Key Header:** `Authorization`
   - **API Key:** `Bearer [your-openai-key]` (include "Bearer " prefix)
5. Click **"Add"** to save

**Note:** Some Xcode versions may have OpenAI pre-configured. Verify the connection is active.

### Step 5: Add Anthropic Provider (Claude)
Add Claude models to your available providers:

1. Click the **"+"** button in Intelligence settings
2. Select **"Internet Hosted"**
3. Fill in the configuration:
   - **Provider Name:** Anthropic
   - **API Endpoint URL:** `https://api.anthropic.com/`
   - **API Key Header:** `x-api-key`
   - **API Key:** `[your-anthropic-key]` (paste key without prefix)
4. Click **"Add"** to save
5. Restart Xcode if prompted (often required for new providers)

See KB-043 for detailed Claude setup instructions.

### Step 6: Add Google Gemini Provider
Configure Google's Gemini models:

1. Click the **"+"** button in Intelligence settings
2. Select **"Internet Hosted"**
3. Fill in the configuration:
   - **Provider Name:** Google Gemini
   - **API Endpoint URL:** `https://generativelanguage.googleapis.com/v1beta/openai`
   - **API Key Header:** `x-goog-api-key`
   - **API Key:** `[your-google-api-key]` (paste key without prefix)
4. Click **"Add"** to save
5. Restart Xcode to ensure models appear

**Note:** Google uses OpenAI-compatible endpoints as of late 2025, making integration straightforward.

### Step 7: Add OpenRouter Provider (Optional Multi-Provider Access)
OpenRouter provides access to 100+ models from various providers with a single API key:

1. Click the **"+"** button in Intelligence settings
2. Select **"Internet Hosted"**
3. Fill in the configuration:
   - **Provider Name:** OpenRouter
   - **API Endpoint URL:** `https://openrouter.ai/api/v1`
   - **API Key Header:** `Authorization`
   - **API Key:** `Bearer [your-openrouter-key]` (include "Bearer " prefix)
4. Click **"Add"** to save
5. Restart Xcode to load available models

**Advantage:** OpenRouter gives you access to Claude, GPT, Gemini, Llama, and many other models through a single API key and billing account.

### Step 8: Verify All Providers Are Active
Confirm that all your configured providers are accessible:

1. Open the **Coding Assistant** panel (⌘+0 or View > Panels > Coding Assistant)
2. Click **"New Conversation"**
3. Click the **model selector dropdown** at the top of the panel
4. You should now see multiple provider categories:
   - **OpenAI** (GPT-4.1, GPT-5, GPT-5 Reasoning)
   - **Anthropic** (Claude Sonnet 4, Claude Opus)
   - **Google Gemini** (Gemini 1.5 Flash, Gemini 1.5 Pro, Gemini 2.0 Flash)
   - **OpenRouter** (100+ models from various providers)
5. Expand each provider to see available models

### Step 9: Organize Providers with Favorites
Streamline access to your most-used models:

1. In the model selector dropdown, locate your preferred models
2. Hover over each model name
3. Click the **star icon** or **favorite button** next to the model
4. Mark your top 3-5 models as favorites:
   - Example: GPT-5, Claude Sonnet 4, Gemini 1.5 Flash
5. Favorited models appear at the top of the dropdown for quick access

### Step 10: Test Each Provider
Verify that all API keys are working correctly:

**Test OpenAI:**
1. Start a new conversation
2. Select **OpenAI > GPT-5**
3. Prompt: "Generate a simple SwiftUI button"
4. Verify you receive a response

**Test Anthropic:**
1. Start a new conversation
2. Select **Anthropic > Claude Sonnet 4**
3. Prompt: "Explain the MVVM pattern in Swift"
4. Verify you receive a detailed response

**Test Google Gemini:**
1. Start a new conversation
2. Select **Google Gemini > Gemini 1.5 Flash**
3. Prompt: "Create a Swift array manipulation example"
4. Verify you receive a response

**Test OpenRouter (if configured):**
1. Start a new conversation
2. Select **OpenRouter > [any available model]**
3. Prompt: "What is SwiftUI?"
4. Verify you receive a response

If all tests succeed, your multi-provider setup is complete!

### Step 11: Implement Key Management Best Practices
Establish secure practices for managing multiple API keys:

1. **Document your configuration:**
   - Create a secure note listing which keys are used for which providers
   - Include key creation dates and intended usage
   - Note any rate limits or quotas for each provider

2. **Set up key rotation reminders:**
   - Schedule API key rotation every 90 days
   - Add calendar reminders for key renewal dates

3. **Monitor usage across providers:**
   - OpenAI: https://platform.openai.com/usage
   - Anthropic: https://console.anthropic.com/settings/limits
   - Google: https://console.cloud.google.com/apis/dashboard
   - OpenRouter: https://openrouter.ai/activity

4. **Configure budget alerts:**
   - Set spending limits in each provider's console
   - Enable email notifications for usage thresholds
   - Review monthly billing statements

5. **Back up your configuration:**
   - Export Intelligence settings periodically (if supported)
   - Document provider configurations in your password manager
   - Keep a secure backup of all API keys

## Expected Results

After successfully completing these steps, you should have:

- **Multiple AI providers** visible in the Xcode Intelligence settings panel
- **Immediate access** to models from OpenAI, Anthropic, Google, and optionally OpenRouter
- **A model selector dropdown** showing categorized providers with their available models
- **Working API connections** verified through successful test prompts
- **Favorited models** appearing at the top of the model selector for quick access
- **Organized key management** with secure storage and monitoring systems in place

When working in Xcode, you can now:
- Switch between ChatGPT, Claude, and Gemini models based on task requirements
- Leverage each AI's unique strengths (GPT for speed, Claude for reasoning, Gemini for cost-effectiveness)
- Access 100+ models through OpenRouter if configured
- Monitor usage and costs across all providers from their respective dashboards
- Maintain secure, organized API key management across multiple services

## Troubleshooting

### Issue: Provider Not Appearing After Adding
**Cause:** Xcode hasn't refreshed the provider list
**Solution:**
- Quit Xcode completely (Xcode > Quit or ⌘Q)
- Relaunch Xcode from Applications
- Wait 10-15 seconds for providers to load
- Check the model selector dropdown again
- If still missing, verify the API endpoint URL format matches exactly (including trailing slashes where specified)

### Issue: "Invalid API Key" Error for Specific Provider
**Cause:** Incorrect key format, header, or expired credentials
**Solution:**
- **For OpenAI:** Ensure key includes "Bearer " prefix in the API Key field, not just the key header
- **For Anthropic:** Verify key starts with `sk-ant-api03-` and uses header `x-api-key`
- **For Google:** Confirm key uses header `x-goog-api-key` (not `Authorization`)
- **For OpenRouter:** Ensure key includes "Bearer " prefix and starts with `sk-or-v1-`
- Regenerate the API key at the provider's website and update Xcode configuration
- Check that the API key has proper permissions and isn't restricted to specific IPs

### Issue: Models from One Provider Missing
**Cause:** API key lacks access to certain model tiers or account limitations
**Solution:**
- **OpenAI:** Visit https://platform.openai.com/account/limits and check "Allowed Models"
- **Anthropic:** Verify your account tier at https://console.anthropic.com/settings/plans
- **Google:** Ensure Gemini API is enabled in Google Cloud Console
- **OpenRouter:** Check model availability and credits at https://openrouter.ai/models
- Some models require upgraded accounts or higher API tiers
- Add payment method if using free trial with limited model access

### Issue: Cannot Switch Between Providers Mid-Conversation
**Cause:** Model selection is locked to the conversation
**Solution:**
- This is expected behavior—each conversation uses one model provider
- Click **"New Conversation"** to select a different provider
- Use multiple conversations side-by-side to compare responses
- Previous conversations remain accessible in the conversation history

### Issue: High Costs or Unexpected API Usage
**Cause:** Multiple providers consuming credits without monitoring
**Solution:**
- Review usage dashboards for each provider weekly
- Set hard spending limits in provider billing settings:
  - OpenAI: https://platform.openai.com/account/limits
  - Anthropic: https://console.anthropic.com/settings/limits
  - Google: https://console.cloud.google.com/billing
  - OpenRouter: https://openrouter.ai/settings/limits
- Disable unused providers in Xcode Intelligence settings
- Favor cost-effective models like Gemini 1.5 Flash or GPT-4.1 for routine tasks
- Reserve expensive models (Claude Opus, GPT-5 Reasoning) for complex problems

### Issue: Rate Limit Errors from Specific Provider
**Cause:** Exceeded requests per minute/hour quota
**Solution:**
- Wait a few minutes before retrying
- Check rate limits in provider documentation:
  - OpenAI Tier 1: 500 RPM, Tier 2: 5,000 RPM
  - Anthropic: Varies by API tier (check console)
  - Google: 60 requests per minute (varies by quota)
- Switch to an alternative provider temporarily
- Upgrade your API tier for higher rate limits
- Implement request queuing by spacing out coding assistant usage

### Issue: Authentication Errors After API Key Rotation
**Cause:** Old key deleted but Xcode still using cached credentials
**Solution:**
- Go to Xcode > Settings > Intelligence
- Click the provider with authentication issues
- Click **"Edit"** or select the provider and click the info button
- Update the API Key field with the new key
- Click **"Save"** or **"Update"**
- Restart Xcode to clear any cached authentication tokens

## Additional Tips

### Provider Selection Strategy
**Use ChatGPT (GPT-5) when:**
- You need fast responses for standard coding tasks
- Generating boilerplate code or UI layouts
- Working with iOS/macOS-specific frameworks
- Quick debugging and syntax fixes
- Time-sensitive development tasks

**Use Claude Sonnet 4 when:**
- Performing complex multi-file refactoring
- Analyzing architecture patterns and design decisions
- Understanding legacy code or large codebases
- Code reviews requiring deep contextual understanding
- Tasks needing conservative, well-reasoned suggestions

**Use Google Gemini when:**
- Cost-effective high-volume usage
- Multimodal tasks (if applicable in future Xcode versions)
- Experimenting with prompts without cost concerns
- Background research or documentation queries
- Testing AI responses for comparison

**Use OpenRouter when:**
- You want access to cutting-edge or experimental models
- Comparing multiple AI responses for the same task
- Accessing models not directly supported by Xcode
- Managing multiple providers with unified billing

### Security Best Practices
1. **Never commit API keys to version control:**
   - Add `.xcode-intelligence-config` to `.gitignore` if applicable
   - Use environment variables for keys in CI/CD pipelines
   - Review git history before pushing to ensure no keys leaked

2. **Use separate keys per environment:**
   - Development keys for local Xcode work
   - Production keys for CI/CD or team environments (if applicable)
   - Testing keys with lower rate limits for experimentation

3. **Enable provider security features:**
   - OpenAI: Enable IP restrictions and set expiration dates
   - Anthropic: Monitor "Last used" timestamps for unusual activity
   - Google: Enable Cloud Console audit logs
   - All: Set up email alerts for suspicious usage patterns

4. **Regular key rotation:**
   - Rotate all API keys every 90 days minimum
   - Delete old keys immediately after rotation
   - Update Xcode configuration promptly
   - Document rotation dates in password manager

5. **Secure storage:**
   - Use a reputable password manager (1Password, Bitwarden, etc.)
   - Enable two-factor authentication on all provider accounts
   - Never store keys in plain text files or screenshots
   - Use encrypted notes for configuration documentation

### Cost Optimization
1. **Model pricing awareness (approximate as of November 2025):**
   - GPT-4.1: $0.03 per 1M tokens (input), $0.10 per 1M tokens (output)
   - GPT-5: $0.075 per 1M tokens (input), $0.30 per 1M tokens (output)
   - Claude Sonnet 4: $3 per 1M tokens (input), $15 per 1M tokens (output)
   - Gemini 1.5 Flash: $0.075 per 1M tokens (input), $0.30 per 1M tokens (output)
   - Pricing varies—check provider websites for current rates

2. **Token usage optimization:**
   - Keep prompts concise and specific
   - Use smaller models for simple tasks
   - Clear conversation history to reduce context tokens
   - Avoid repeatedly asking the same question across providers

3. **Budget allocation:**
   - Allocate 60% to your primary provider (typically OpenAI)
   - Allocate 25% to secondary provider (Claude or Gemini)
   - Reserve 15% for experimentation with OpenRouter or alternatives

### Team Configuration
If working in a team environment:
1. **Create organization accounts** where possible (OpenAI, Anthropic support this)
2. **Use workspace-specific keys** to track usage by team or project
3. **Document provider choices** in team wiki or README files
4. **Establish provider guidelines** for different project types or tasks
5. **Share configuration templates** (without keys) for consistent setup

### Provider-Specific Tips
**OpenAI:**
- GPT-5 Reasoning is slower but excels at algorithmic problems
- Monitor usage at https://platform.openai.com/usage daily if using heavily
- Free trial credits expire—ensure billing is active

**Anthropic:**
- Claude Sonnet 4 has exceptional context window (200K tokens)
- Prepaid credit system requires regular balance monitoring
- Workspace management allows separate billing for different projects

**Google Gemini:**
- Free tier includes generous quota for experimentation
- Gemini 1.5 Flash offers best cost-performance ratio
- API quotas can be increased via Google Cloud Console requests

**OpenRouter:**
- Browse https://openrouter.ai/models for full model catalog
- Models from different providers have different pricing
- Some models offer free tier access for testing
- Unified billing simplifies cost tracking across providers

### Keyboard Shortcuts for Efficient Switching
- **⌘+0**: Open Coding Assistant panel
- **⌘+N**: New conversation (in Coding Assistant context)
- **⌘+,**: Open Xcode Settings
- **Esc**: Close model selector dropdown
- **↑/↓ arrows**: Navigate model list in dropdown

### Monitoring and Maintenance Schedule
**Daily:**
- Quick visual check of conversation responses for errors
- Monitor for authentication failures or rate limit warnings

**Weekly:**
- Review usage across all providers
- Check remaining credits/quotas
- Verify all favorited models still work

**Monthly:**
- Analyze cost breakdown by provider
- Evaluate if provider mix matches usage patterns
- Review and optimize provider selection strategy

**Quarterly:**
- Rotate all API keys
- Update provider documentation
- Review new models and pricing changes
- Reassess provider needs based on project evolution

## Related Articles
- KB-042: How to generate an API key for Claude in the Anthropic console
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-034: How to connect your paid OpenAI account for more requests
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-050: How to compare responses between ChatGPT and Claude for the same coding task
- KB-007: How to access the new Intelligence settings tab in Xcode preferences
- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT

## Sources
1. Anthropic Console - API Keys Management - https://console.anthropic.com/settings/keys (Accessed: November 17, 2025)
2. OpenAI Platform - API Key Management - https://platform.openai.com/api-keys (Accessed: November 17, 2025)
3. Google AI Studio - API Keys - https://aistudio.google.com/apikey (Accessed: November 17, 2025)
4. OpenRouter Documentation - Xcode Integration - https://openrouter.ai/docs/community/xcode (Accessed: November 17, 2025)
5. Ronnie Rocha - "Beyond ChatGPT: How to Use Claude and Gemini in Xcode Coding Intelligence" - https://ronnierocha.dev/blog/beyond-chatgpt-how-to-use-claude-and-gemini-in-xcode-coding-intelligence/ (Accessed: November 17, 2025)
6. Carlo Zottmann - "How to use Google Gemini in Xcode 26 beta" - https://zottmann.org/2025/06/13/how-to-use-google-gemini.html (Accessed: November 17, 2025)
7. Wendy Liga - "Use Custom Models in the New Xcode 26 Intelligence" - https://wendyliga.com/blog/xcode-26-custom-model/ (Accessed: November 17, 2025)
8. Apple Developer Documentation - "Xcode 26.1.1 Release Notes" - https://developer.apple.com/documentation/xcode-release-notes/xcode-26_1-release-notes (Accessed: November 17, 2025)
9. TechCrunch - "Apple brings ChatGPT and other AI models to Xcode" - https://techcrunch.com/2025/06/09/apple-brings-chatgpt-and-other-ai-models-to-xcode/ (Accessed: November 17, 2025)
10. Peter Friese - "Reverse-Engineering Xcode's Coding Intelligence prompt" - https://peterfriese.dev/blog/2025/reveng-xcode-coding-intelligence/ (Accessed: November 17, 2025)
11. Mehmet Baykar - "Harnessing All Powerful AI Models in Xcode 26.0 With One Subscription" - https://mehmetbaykar.com/posts/how-to-use-models-in-xcode-26-using-poe-com/ (Accessed: November 17, 2025)
12. Anthropic Support - "API Key Best Practices" - https://support.anthropic.com/en/articles/9767949-api-key-best-practices (Accessed: November 17, 2025)
13. OpenAI Documentation - "Best Practices for API Key Safety" - https://help.openai.com/en/articles/5112595-best-practices-for-api-key-safety (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
