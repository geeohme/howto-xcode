# How to reverse-engineer and customize Coding Intelligence prompts

**Article ID:** KB-069
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 45-60 minutes

## Overview

Xcode 26.1+ includes built-in Coding Intelligence powered by AI models like ChatGPT and Claude, but the system prompts that instruct these models remain hidden from developers. By reverse-engineering these prompts using network inspection tools like Proxyman, you can understand exactly what context and instructions Xcode sends to AI models, discover hidden guidelines and constraints, and potentially customize behavior through custom model providers. This guide provides a comprehensive walkthrough of uncovering Xcode's AI prompts and exploring customization opportunities.

## Prerequisites

- Xcode 26.1 or later installed
- macOS 15.0 or later (macOS 26 Tahoe recommended)
- Proxyman app (free version is sufficient) - Download from https://proxyman.io
- Basic understanding of HTTP/HTTPS requests and responses
- Coding Intelligence configured with at least one AI model (ChatGPT or Claude)
- Active internet connection for AI model API access
- Basic familiarity with terminal commands and JSON (helpful but not required)

## Understanding Xcode's System Prompts

### What Are System Prompts?

System prompts are hidden instructions that Xcode automatically injects before every conversation with an AI model. These prompts:

- **Set behavioral guidelines:** Instruct the AI to prefer Swift, Objective-C, and Apple frameworks
- **Provide context:** Include information about your current file, selection, and project structure
- **Define output formats:** Specify how the AI should structure its responses
- **Enforce best practices:** Guide the AI to use modern patterns like Swift Concurrency and Swift Testing
- **Enable tool usage:** Allow the AI to search your codebase and make file edits

### Types of System Prompts in Xcode 26

Based on extracted `.idechatprompttemplate` files, Xcode 26 uses 40+ system prompt templates across six categories:

1. **Basic Coding Assistance:** `BasicSystemPrompt`, `ReasoningSystemPrompt`, and variant prompts
2. **Specialized Workflows:** `IntegratorSystemPrompt`, `NewCodeIntegratorSystemPrompt`, `FastApplyIntegratorSystemPrompt`
3. **Context Providers:** Templates for delivering complete files, abbreviated files, or selected code snippets
4. **Tool-Assisted Operations:** Prompts enhanced with search and editing capabilities
5. **Generation Functions:** `GenerateDocumentation`, `GeneratePlayground`, `GeneratePreview`
6. **Utility Support:** Prompts for chat titling, query processing, and issue identification

### Key Guidelines in Hidden Prompts

Research has revealed that Xcode's hidden prompts include instructions like:

- "Whenever possible, favor Apple programming languages and frameworks"
- "Always prefer Swift, Objective-C, C, and C++ over alternatives"
- "Prefer the use of Swift Concurrency (async/await, actors, etc.) over tools like Dispatch or Combine"
- "Projects can provide code examples using the new Swift Testing framework that uses Swift Macros"
- "Complete File Replacement: Never partial edits—always return entire file content"

## Steps

### Step 1: Install and Configure Proxyman

Proxyman is a network debugging tool that intercepts and inspects HTTP/HTTPS traffic, including Xcode's AI API requests.

1. **Download Proxyman:**
   - Visit https://proxyman.io
   - Download the free macOS version (no paid license required for this use case)
   - Open the downloaded DMG file and drag Proxyman to your Applications folder

2. **Launch Proxyman:**
   - Open Proxyman from Applications
   - Grant necessary permissions when prompted:
     - **Network Extensions:** Required to intercept traffic
     - **Full Disk Access:** May be needed in System Settings > Privacy & Security

3. **Install SSL Proxying Certificate:**
   - In Proxyman, go to **Certificate** → **Install Certificate on this Mac**
   - Follow the on-screen instructions to install and trust the certificate
   - This allows Proxyman to decrypt HTTPS traffic (including OpenAI and Anthropic API calls)

4. **Enable macOS Proxy:**
   - Proxyman should automatically configure your system proxy
   - Verify by going to **System Settings** → **Network** → **Wi-Fi** (or Ethernet) → **Details** → **Proxies**
   - You should see HTTP and HTTPS proxies set to `127.0.0.1:9090` (or Proxyman's configured port)

### Step 2: Configure SSL Proxying for AI API Domains

To inspect encrypted traffic to AI model providers, you need to enable SSL proxying for specific domains.

1. **In Proxyman, go to Tools** → **SSL Proxying** → **SSL Proxying Settings**

2. **Add OpenAI domain** (for ChatGPT):
   - Click the **+** button
   - Enter domain: `api.openai.com`
   - Port: `443`
   - Click **Save**

3. **Add Anthropic domain** (for Claude):
   - Click the **+** button
   - Enter domain: `api.anthropic.com`
   - Port: `443`
   - Click **Save**

4. **Enable SSL Proxying:**
   - Check the box **"Enable SSL Proxying"** at the top of the settings window
   - Click **OK** to save changes

### Step 3: Filter Traffic to Focus on AI Requests

Proxyman captures all network traffic by default, which can be overwhelming.

1. **Use the Search/Filter bar:**
   - In the main Proxyman window, locate the filter bar at the top
   - Type `openai` or `anthropic` to filter requests to AI providers

2. **Pin relevant domains:**
   - Right-click on `api.openai.com` or `api.anthropic.com` in the request list
   - Select **Pin** to keep these domains at the top for easy access

3. **Clear existing requests** (optional):
   - Click the **Trash** icon to clear old requests
   - This makes it easier to see new requests when you start using Coding Intelligence

### Step 4: Trigger a Coding Intelligence Request in Xcode

Now you'll generate traffic that Proxyman can intercept.

1. **Open Xcode 26.1+** with a project loaded

2. **Open the Coding Assistant panel:**
   - Press `⌘0` (Command + 0), or
   - Go to **Editor** → **Show Coding Assistant**

3. **Select your AI model:**
   - Choose ChatGPT or Claude from the model dropdown
   - Ensure you're signed in and the model is active

4. **Send a test prompt:**
   - Type a simple question like: "Explain the code in this file"
   - Make sure you have a Swift file open in the editor
   - Press **Enter** to send the request

5. **Wait for the response:**
   - The AI will respond within 10-60 seconds
   - During this time, Proxyman is capturing the API traffic

### Step 5: Inspect the Captured API Request

Now examine what Xcode sent to the AI model.

1. **Switch to Proxyman:**
   - You should see new requests appear for `api.openai.com` or `api.anthropic.com`

2. **Find the chat completion request:**
   - For OpenAI: Look for `POST https://api.openai.com/v1/chat/completions`
   - For Anthropic: Look for `POST https://api.anthropic.com/v1/messages`

3. **Click on the request** to view details

4. **Examine the Request tab:**
   - Click the **Request** section in the right panel
   - You'll see headers, query parameters, and the request body

5. **View the JSON payload:**
   - In the **Body** subsection, select **JSON** view
   - You'll see the complete request payload with:
     - `messages` array (for OpenAI) or `messages` field (for Anthropic)
     - Model identifier (e.g., `gpt-5`, `claude-sonnet-4-20250514`)
     - Parameters like `temperature`, `max_tokens`, etc.

### Step 6: Extract the System Prompt

The system prompt is embedded in the messages array.

1. **Locate the messages array:**
   - In OpenAI requests, look for `"messages": [ ... ]`
   - In Anthropic requests, look for the `messages` array

2. **Find the system message:**
   - For OpenAI: Look for a message with `"role": "system"`
   - For Anthropic: The system prompt may be in a separate `system` field at the root level
   - Example OpenAI structure:
     ```json
     {
       "messages": [
         {
           "role": "system",
           "content": "You are a helpful coding assistant. Whenever possible, favor Apple programming languages and frameworks..."
         },
         {
           "role": "user",
           "content": "Explain the code in this file"
         }
       ]
     }
     ```

3. **Copy the system prompt content:**
   - Select the text in the `"content"` field of the system message
   - Right-click and choose **Copy**, or use `⌘C`
   - Paste into a text editor for analysis

4. **Look for additional context:**
   - You may also see user messages that include:
     - File contents (your currently open file)
     - Code selection details
     - Project metadata
     - File paths and structure

### Step 7: Analyze the System Prompt Structure

Examine what Xcode includes in its system prompts.

**Common elements you'll find:**

1. **Behavioral Instructions:**
   - Guidelines to prefer Swift/Objective-C
   - Instructions to use modern Apple frameworks
   - Directives about code style and formatting

2. **Context Information:**
   - Current file name and path
   - Programming language detected
   - Selected code range (if applicable)
   - Cursor position

3. **Tool Definitions:**
   - Function schemas for searching code
   - Function schemas for editing files
   - Parameters and return types for each tool

4. **Output Format Instructions:**
   - How to structure code responses
   - Whether to return complete files or snippets
   - Formatting for explanations vs. code generation

5. **Constraints and Guardrails:**
   - Security considerations
   - Performance guidelines
   - Testing requirements

**Example analysis:**
```
System Prompt Analysis for ChatGPT in Xcode 26.1

Length: ~2,500 tokens

Key instructions:
- "Favor Apple programming languages" → Ensures Swift/Obj-C solutions
- "Prefer Swift Concurrency" → Modern async/await over older patterns
- "Use Swift Testing framework with macros" → Latest testing approach
- "Never partial edits—always return entire file" → Complete file replacement

Tools available:
1. search_files(query: String) → Find code in project
2. edit_file(path: String, content: String) → Modify files
3. read_file(path: String) → Access file contents

Context provided:
- Current file: /Users/dev/MyApp/ContentView.swift
- Language: Swift
- Selection: None
- Project: MyApp (iOS)
```

### Step 8: Compare Prompts Across Different Scenarios

To fully understand Xcode's prompt system, capture requests in different contexts.

**Test these scenarios:**

1. **Code Explanation:**
   - Prompt: "Explain this code"
   - Check if the system prompt changes based on file type

2. **Code Generation:**
   - Prompt: "Create a SwiftUI login view"
   - Look for generation-specific instructions

3. **Code Refactoring:**
   - Prompt: "Refactor this function to use async/await"
   - See if refactoring prompts include different tools or constraints

4. **Error Fixing:**
   - Prompt: "Fix the error in this code"
   - Check if error-handling guidelines appear

5. **Documentation Generation:**
   - Prompt: "Add documentation comments to this function"
   - Look for documentation format specifications

**Create a comparison table:**
```
Scenario          | Prompt Template Used    | Notable Instructions
------------------|-------------------------|---------------------
Explanation       | BasicSystemPrompt       | Read-only, no edits
Code Generation   | IntegratorSystemPrompt  | Complete file replacement
Refactoring       | NewCodeIntegrator       | Preserve existing structure
Error Fixing      | ToolAssistedBasic       | Search + edit tools enabled
Documentation     | GenerateDocumentation   | Format specs included
```

### Step 9: Export and Document Your Findings

Preserve your discoveries for future reference.

1. **Export requests from Proxyman:**
   - Right-click on a request
   - Select **Export** → **Save as HAR** or **Copy cURL**
   - Save to a documentation folder

2. **Create a system prompt library:**
   - Create a folder: `~/Documents/Xcode-System-Prompts/`
   - Save extracted prompts as text files:
     - `basic-system-prompt.txt`
     - `refactoring-system-prompt.txt`
     - `documentation-generation-prompt.txt`

3. **Document tool schemas:**
   - Extract the function/tool definitions from prompts
   - Save as JSON files for reference:
     - `search-tool-schema.json`
     - `edit-tool-schema.json`

4. **Create a prompt analysis document:**
   - Summarize key findings
   - Note differences between ChatGPT and Claude prompts
   - Document which scenarios use which prompt templates

### Step 10: Explore Customization Options

While you cannot directly edit Xcode's system prompts, you can influence behavior through custom model providers.

**Option A: Set Up a Custom Model Provider with Local Proxy**

This advanced technique allows you to modify prompts before they reach the AI model.

1. **Install a local OpenAI-compatible server:**
   - Options include: LiteLLM, LocalAI, or custom Python server
   - Example with LiteLLM:
     ```bash
     pip install litellm
     litellm --model gpt-4
     ```

2. **Create a proxy server that modifies requests:**
   - Write a simple Python/Node.js server that:
     - Receives requests from Xcode
     - Modifies the system prompt
     - Forwards to the actual AI provider
     - Returns the response to Xcode

3. **Configure Xcode to use your local proxy:**
   - Go to **Xcode** → **Settings** → **Intelligence**
   - Click **Add a Model Provider**
   - Select **Internet Hosted**
   - Enter URL: `http://localhost:8000` (your proxy server)
   - Configure API key and headers as needed

**Option B: Leverage Custom Model Providers**

Some hosted services allow prompt customization:

1. **OpenRouter:**
   - Visit https://openrouter.ai
   - Create an account and get an API key
   - Configure in Xcode with URL: `https://openrouter.ai/api`
   - OpenRouter supports custom system prompts in some configurations

2. **Poe.com API:**
   - Create custom bots with predefined prompts on Poe.com
   - Get API credentials
   - Add as a custom model provider in Xcode

**Option C: Use Prompt Engineering in User Messages**

If you can't modify system prompts, you can still influence behavior:

1. **Prepend instructions to your prompts:**
   ```
   Important context: This project uses MVVM architecture and requires
   full unit test coverage for all business logic.

   Now, please create a view model for user authentication.
   ```

2. **Create a project-level context file:**
   - Create `CLAUDE.md` or `CODING_ASSISTANT.md` in your project root
   - Include project-specific guidelines
   - Reference it in your prompts: "Follow the guidelines in CLAUDE.md"

3. **Use XML-structured prompts:**
   - Claude responds particularly well to XML structure:
     ```
     <task>Create a login view</task>
     <requirements>
       <architecture>MVVM</architecture>
       <testing>Include unit tests</testing>
       <accessibility>Full VoiceOver support</accessibility>
     </requirements>
     ```

### Step 11: Contribute to Community Knowledge

Share your findings with the developer community.

1. **Contribute to open-source documentation:**
   - The GitHub repo `artemnovichkov/xcode-26-system-prompts` collects extracted prompts
   - Submit pull requests with newly discovered prompts or updates

2. **Write blog posts or tutorials:**
   - Document your reverse-engineering process
   - Share prompt customization techniques you've discovered

3. **Report issues to Apple:**
   - If you discover problems or limitations in system prompts
   - Submit feedback at https://feedbackassistant.apple.com

## Expected Results

After following this guide, you should have:

- **Complete visibility** into the system prompts Xcode sends to AI models
- **Understanding** of how Xcode structures requests and provides context
- **Documentation** of prompt templates for different coding scenarios
- **Ability to compare** ChatGPT and Claude prompts side-by-side
- **Knowledge** of tool schemas and capabilities available to AI models
- **Techniques** for influencing AI behavior through prompt engineering
- **Foundation** for exploring advanced customization through proxy servers

**Typical discoveries:**
- System prompts are 2,000-4,000 tokens long
- ChatGPT prompts emphasize speed and conciseness
- Claude prompts include more context and reasoning instructions
- Tool schemas enable AI to search and edit files programmatically
- File context is automatically included based on open files and selections

## Troubleshooting

### Issue 1: Proxyman Not Capturing HTTPS Traffic

**Problem:** You see HTTP requests but not the decrypted HTTPS content, or you see "Certificate verification failed" errors.

**Solution:**
- Ensure the Proxyman certificate is properly installed and trusted:
  - Go to **Proxyman** → **Certificate** → **Install Certificate on this Mac**
  - Open **Keychain Access** → **System**, find "Proxyman CA", and set to "Always Trust"
- Restart Xcode after installing the certificate
- Check that SSL Proxying is enabled for `api.openai.com` and `api.anthropic.com`
- Try disabling any VPN or corporate proxy that might interfere
- Verify System Proxy is enabled in Proxyman preferences

### Issue 2: Cannot Find System Prompt in Request

**Problem:** The captured request doesn't contain a visible system prompt, or the messages array is empty.

**Solution:**
- Make sure you're looking at the **Request** tab, not the **Response** tab
- Check the **Body** section and ensure JSON view is selected
- For Anthropic/Claude requests, the system prompt may be in a separate `system` field at the root level, not in `messages`
- Some requests may use binary encoding—click **View as Text** or **View as JSON**
- Ensure the request is a chat completion, not a streaming chunk or health check
- The system message might be the first element in the `messages` array

### Issue 3: Proxyman Causes Xcode Connectivity Issues

**Problem:** After enabling Proxyman, Xcode cannot connect to AI models, or requests time out.

**Solution:**
- Verify that Proxyman is running while Xcode tries to connect
- Check that you're not blocking requests—Proxyman should pass them through
- Disable any request rewriting rules that might interfere
- Try disabling SSL Proxying temporarily to see if it's a certificate issue
- Check Xcode's **Coding Assistant** panel for specific error messages
- Restart both Proxyman and Xcode
- Temporarily disable Proxyman's macOS proxy and test if Xcode works normally

### Issue 4: Different Prompts Than Expected

**Problem:** The system prompts you capture don't match descriptions from online sources or seem incomplete.

**Solution:**
- Xcode updates system prompts frequently—prompts in Xcode 26.1.1 may differ from 26.1 or beta versions
- Different AI models (ChatGPT vs Claude) receive different prompts
- Prompts vary based on context (file type, user action, available tools)
- Capture prompts in multiple scenarios to see the full range of variations
- Check that you're capturing the complete request, not a truncated version
- Consult the `artemnovichkov/xcode-26-system-prompts` GitHub repo for reference examples

### Issue 5: Cannot Modify System Prompts

**Problem:** You want to customize the system prompts but can't find a way to edit Xcode's `.idechatprompttemplate` files.

**Solution:**
- **Xcode's system prompts are not officially user-editable** as of version 26.1.1
- The `.idechatprompttemplate` files are internal to Xcode and modifying them is not supported
- Focus on **workarounds** instead:
  - Use custom model providers with proxy servers to modify prompts in transit
  - Leverage prompt engineering in user messages to add project-specific context
  - Create standardized prompt templates for your team
  - Submit feature requests to Apple for official customization support
- For advanced users: Explore creating a man-in-the-middle proxy that rewrites requests

## Additional Tips

### Understanding Prompt Templates

**BasicSystemPrompt:**
- Used for general code questions and explanations
- Includes read-only context about your current file
- No editing tools enabled
- Optimized for speed and concise responses

**IntegratorSystemPrompt:**
- Used when generating new code or significant modifications
- Includes file editing capabilities
- Enforces "complete file replacement" rule
- Provides more detailed project context

**ReasoningSystemPrompt:**
- Used for complex architectural questions
- Includes additional reasoning steps
- May have longer response times
- Better for design decisions and trade-off analysis

**GenerateDocumentation:**
- Specialized for creating doc comments
- Includes Swift documentation format guidelines
- Focuses on clarity and completeness
- May reference Apple's documentation style guide

### Advanced Proxyman Features

**Request/Response Breakpoints:**
- Set breakpoints to pause requests before they're sent
- Modify the JSON payload manually
- Resume the request with your changes
- Useful for testing how different system prompts affect responses

**Scripting:**
- Proxyman supports JavaScript scripting for automated request modification
- Create scripts to consistently modify system prompts
- Go to **Tools** → **Scripting** → **New Script**

**External Proxy:**
- Chain Proxyman with other tools like Charles or Burp Suite
- Useful for advanced traffic analysis

### Prompt Engineering Best Practices

**To augment Xcode's system prompts:**

1. **Provide project-specific context:**
   ```
   Context: This iOS app uses Clean Architecture with MVVM presentation layer.
   Dependencies are managed via Swift Package Manager.
   All networking uses async/await with custom Result types.

   Task: Create a repository for fetching user data from our REST API.
   ```

2. **Override default preferences when needed:**
   ```
   Note: For this task, prefer Combine over async/await because we need
   to observe real-time value changes across multiple subscribers.

   Create a reactive data service for live sports scores.
   ```

3. **Request specific output formats:**
   ```
   Requirements:
   - Include complete unit tests using Swift Testing framework
   - Add comprehensive documentation comments
   - Follow our team's naming conventions (use snake_case for database columns)
   ```

### Comparing AI Model Prompts

**ChatGPT (OpenAI) prompts typically:**
- Are more concise (1,500-2,500 tokens)
- Focus on speed and practical solutions
- Include fewer reasoning steps
- Prefer simpler, more direct instructions

**Claude (Anthropic) prompts typically:**
- Are more detailed (2,500-4,000 tokens)
- Include more context and background
- Encourage step-by-step reasoning
- Provide more extensive tool documentation

### Security and Privacy Considerations

**When using Proxyman to inspect traffic:**
- System prompts may contain sensitive information:
  - File paths that reveal project structure
  - Code snippets that might be proprietary
  - Comments with internal notes or TODO items
- Be cautious about sharing captured requests publicly
- Redact sensitive information before posting to forums or GitHub
- Remember that your API keys are transmitted in headers—never share full request exports with keys included

### Contributing to Open-Source Prompt Collections

The community benefits from shared knowledge about Xcode's prompts:

1. **GitHub: artemnovichkov/xcode-26-system-prompts**
   - Contains 40+ extracted prompt templates
   - Includes additional documentation files
   - Accepts community contributions

2. **How to contribute:**
   - Fork the repository
   - Add newly discovered prompts in the appropriate category
   - Include version information (Xcode 26.1, 26.1.1, etc.)
   - Note the context in which the prompt appears
   - Submit a pull request with clear descriptions

### Future Possibilities

Apple may add official customization features in future Xcode versions:
- User-editable system prompt templates
- Project-specific AI configuration files
- Custom tool/function definitions
- Per-language or per-framework prompt variants

Until then, reverse-engineering and creative workarounds remain the primary methods for understanding and influencing Coding Intelligence behavior.

## Related Articles

- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-047: How to use Claude for complex refactoring tasks
- KB-048: How to leverage Claude's context window for large code reviews
- KB-050: How to compare responses between ChatGPT and Claude for the same coding task

## Sources

- GitHub: artemnovichkov/xcode-26-system-prompts - https://github.com/artemnovichkov/xcode-26-system-prompts (Accessed: November 17, 2025)
- Swift with Vincent: ChatGPT in Xcode 26: there's a hidden prompt! - https://www.swiftwithvincent.com/blog/chatgpt-in-xcode-26-theres-a-hidden-prompt (Accessed: November 17, 2025)
- Peter Friese: Reverse-Engineering Xcode's Coding Intelligence prompt - https://peterfriese.dev/blog/2025/reveng-xcode-coding-intelligence/ (Accessed: November 17, 2025)
- Carlo Zottmann: How to use Google Gemini in Xcode 26 beta - https://zottmann.org/2025/06/13/how-to-use-google-gemini.html (Accessed: November 17, 2025)
- Wendy Liga: Use Custom Models in the New Xcode 26 Intelligence - https://wendyliga.com/blog/xcode-26-custom-model/ (Accessed: November 17, 2025)
- Proxyman Documentation: SSL Proxying Setup - https://docs.proxyman.io/ssl-proxying (Accessed: November 17, 2025)
- Apple Developer: Writing code with intelligence in Xcode - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- Ronnie Rocha: Beyond ChatGPT: How to Use Claude and Gemini in Xcode Coding Intelligence - https://ronnierocha.dev/blog/beyond-chatgpt-how-to-use-claude-and-gemini-in-xcode-coding-intelligence/ (Accessed: November 17, 2025)
- 9to5Mac: Apple releases Xcode 26.1.1 with coding intelligence improvements - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- Medium (Vedprakash Wagh): Setting up a custom model provider in XCode Intelligence - https://medium.com/@tigerwed/setting-up-a-custom-model-provider-in-xcode-intelligence-529cf7268306 (Accessed: November 17, 2025)
- OpenRouter Documentation: Xcode Integration - https://openrouter.ai/docs/community/xcode (Accessed: November 17, 2025)
- Michael Tsai Blog: Xcode 26 Beta 7 - https://mjtsai.com/blog/2025/08/28/xcode-26-beta-7/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
