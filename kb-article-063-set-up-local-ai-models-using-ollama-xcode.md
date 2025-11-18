# How to set up local AI models using Ollama in Xcode

**Article ID:** KB-063
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 20-30 minutes

## Overview

Setting up local AI models using Ollama in Xcode 26.1+ enables you to leverage powerful large language models (LLMs) for coding assistance without relying on cloud-based services. Ollama is a free, open-source application that allows you to download and run state-of-the-art AI models directly on your Mac. Xcode 26.1+ introduced native support for locally hosted model providers, making it seamless to integrate Ollama-powered AI assistants into your development workflow. This approach offers complete privacy, offline functionality, no API costs, and full control over model selection—ideal for developers working with sensitive codebases or those seeking independence from third-party AI services.

## Prerequisites

- **Xcode 26.1 or later** installed on your Mac
- **macOS 14.0 (Sonoma) or later** (macOS 15.0+ recommended for optimal performance)
- **Minimum 8 GB RAM** (16 GB recommended for 7B models, 32 GB for larger models)
- **Minimum 10 GB free disk space** (varies by model size; larger models require 20-40 GB)
- **Apple Silicon (M1/M2/M3/M4) or Intel Mac** (Apple Silicon strongly recommended for performance)
- **Terminal access** for running command-line installations
- **Administrator privileges** for installing software
- **Basic familiarity with command-line operations**

## Steps

### Step 1: Install Ollama on Your Mac

**Option A: Download from Official Website (Recommended)**

1. Open your web browser and navigate to **https://ollama.com**
2. Click the **"Download for macOS"** button on the homepage
3. Once the `.dmg` file downloads, open it
4. Drag the **Ollama** application icon into your **Applications** folder
5. Open **Applications** in Finder and double-click **Ollama** to launch it
6. If prompted with a security warning (unidentified developer), go to **System Settings > Privacy & Security** and click **"Open Anyway"**
7. Ollama will install and start running in your menu bar (look for the llama icon)

**Option B: Install via Homebrew**

If you prefer using Homebrew package manager:

```bash
brew install ollama
```

After installation, start the Ollama service:

```bash
ollama serve
```

**Note:** The Ollama application runs as a background service on **port 11434** by default.

### Step 2: Verify Ollama Installation

1. Open **Terminal** (Applications > Utilities > Terminal)
2. Check if Ollama is installed correctly:

```bash
which ollama
```

Expected output: `/usr/local/bin/ollama` or `/opt/homebrew/bin/ollama`

3. Verify the Ollama service is running:

```bash
curl http://localhost:11434/api/version
```

Expected output: A JSON response with version information, such as:
```json
{"version":"0.5.1"}
```

4. If you receive a "Connection refused" error, ensure Ollama is running by launching the Ollama app from Applications or running `ollama serve` in Terminal.

### Step 3: Download Your First AI Model

Choose a coding-optimized model to download. For Xcode development, **Qwen2.5-Coder** and **CodeLlama** are highly recommended.

**Recommended Models for Xcode (2025):**

1. **Qwen2.5-Coder (Best Performance)**
   - **qwen2.5-coder:7b** - Lightweight, fast (requires 8 GB RAM)
   - **qwen2.5-coder:14b** - Balanced performance (requires 16 GB RAM)
   - **qwen2.5-coder:32b** - GPT-4o-level performance (requires 32 GB RAM)

2. **CodeLlama (Broad Language Support)**
   - **codellama:7b** - Lightweight alternative
   - **codellama:13b** - Balanced option
   - **codellama:34b** - High performance (requires 32 GB+ RAM)

3. **DeepSeek-Coder (Specialized for Code Generation)**
   - **deepseek-coder:6.7b** - Efficient and fast
   - **deepseek-coder:33b** - Advanced coding capabilities

To download a model, run the following command in Terminal:

```bash
ollama run qwen2.5-coder:7b
```

**What happens:**
- Ollama downloads the model (this may take 5-15 minutes depending on model size and internet speed)
- Once complete, the model starts in an interactive chat session
- Type `/bye` to exit the chat and return to Terminal

**To download additional models without starting a chat session:**

```bash
ollama pull codellama:13b
ollama pull deepseek-coder:6.7b
```

**Check installed models:**

```bash
ollama list
```

Expected output:
```
NAME                    ID              SIZE    MODIFIED
qwen2.5-coder:7b       abc123def       4.7 GB  2 minutes ago
codellama:13b          def456ghi       7.3 GB  5 minutes ago
```

### Step 4: Configure Xcode 26.1+ Intelligence Settings

1. Open **Xcode 26.1 or later**
2. From the menu bar, select **Xcode > Settings** (or press **Cmd + ,**)
3. Click on the **"Intelligence"** tab in the Settings window
4. Under **"Model Providers"**, click the **"+"** button (Add a Model Provider)
5. In the dialog that appears, select **"Locally Hosted"** (not "Internet Hosted")
6. Configure the provider settings:
   - **Provider Name:** Enter a descriptive name like `Ollama` or `Local Models`
   - **Base URL:** Enter `http://127.0.0.1:11434/v1` or `http://localhost:11434/v1`
   - **API Key:** Leave this field **blank** (local models don't require authentication)
7. Click **"Add"** or **"Done"** to save the provider

**Important Notes:**
- The `/v1` endpoint is critical—it ensures OpenAI-compatible API format
- If Xcode shows "Unable to connect," verify Ollama is running (check menu bar icon or run `ollama serve`)

### Step 5: Select and Activate Your Local Model in Xcode

1. In Xcode Settings > Intelligence, you should now see your **Ollama** provider listed
2. Under **"Available Models"**, click the dropdown menu
3. If successful, you'll see a list of your downloaded Ollama models:
   - `qwen2.5-coder:7b`
   - `codellama:13b`
   - `deepseek-coder:6.7b`
4. Select your preferred model (e.g., **qwen2.5-coder:7b**)
5. Ensure the **"Enable Intelligence Features"** toggle is turned **ON**
6. Close the Settings window

**Troubleshooting:** If no models appear:
- Verify models are installed: Run `ollama list` in Terminal
- Restart Xcode completely (Quit and reopen)
- Restart the Ollama service: Run `killall Ollama` then reopen the Ollama app

### Step 6: Test the Integration in Xcode's Coding Assistant

1. Open an existing Swift project or create a new one (**File > New > Project**)
2. Open a Swift file in the editor
3. Open the **Coding Assistant** panel:
   - Click the **chat icon** in the right sidebar, or
   - Press **Cmd + Shift + A** (keyboard shortcut), or
   - Select **View > Inspectors > Coding Assistant** from the menu bar
4. In the Coding Assistant panel, click the **model selector dropdown** at the top
5. Confirm your local Ollama model is selected (you should see your provider name and model)
6. Type a test prompt, such as:
   ```
   Write a SwiftUI view that displays "Hello, Ollama!" with a gradient background
   ```
7. Press **Enter** or click **Send**
8. The local model should generate a response within a few seconds

**Expected Behavior:**
- The model generates Swift code directly in the Coding Assistant
- Response time is typically 2-10 seconds depending on model size and hardware
- You can insert generated code directly into your editor

### Step 7: Optimize Model Performance (Optional)

For better performance and resource management:

**A. Check Running Models and Memory Usage:**

```bash
ollama ps
```

This shows which models are currently loaded in memory.

**B. Unload Models to Free Memory:**

If a model is using too much RAM, stop it:

```bash
ollama stop qwen2.5-coder:7b
```

**C. Delete Unused Models:**

To free disk space, remove models you no longer need:

```bash
ollama rm codellama:13b
```

**D. Configure Model Temperature (Advanced):**

You can adjust model creativity by creating a custom Modelfile, but this is optional for most users.

## Expected Results

After completing these steps, you should have:

1. ✅ **Ollama installed and running** on your Mac with the menu bar icon visible
2. ✅ **At least one coding model downloaded** (e.g., qwen2.5-coder:7b, codellama:13b)
3. ✅ **Xcode 26.1+ configured** with Ollama as a locally hosted model provider
4. ✅ **Model appearing in Xcode's Coding Assistant** with successful connection
5. ✅ **Functional AI assistance** generating Swift code, explanations, and debugging help
6. ✅ **Completely offline capability** with no internet connection required after initial setup
7. ✅ **No API costs** and full privacy for your codebase

When you open the Coding Assistant in Xcode and select your local model, you should see responses generated entirely on your Mac without any data leaving your machine.

## Troubleshooting

### Issue 1: "Connection Refused" Error When Adding Provider

**Symptoms:** Xcode shows "Unable to connect to http://localhost:11434" when adding the provider

**Solutions:**
- **Verify Ollama is running:** Check for the Ollama icon in your menu bar (top-right of screen). If absent, open the Ollama app from Applications
- **Check the port:** Run `curl http://localhost:11434/api/version` in Terminal. If this fails, Ollama isn't running
- **Restart Ollama service:** Quit the Ollama app completely (right-click menu bar icon > Quit), then reopen it
- **Check firewall settings:** Ensure macOS Firewall isn't blocking local connections. Go to **System Settings > Network > Firewall** and add Ollama to allowed applications
- **Try the IP address:** Use `http://127.0.0.1:11434/v1` instead of `http://localhost:11434/v1`
- **Reinstall Ollama:** If issues persist, uninstall Ollama, delete `~/.ollama`, and reinstall

### Issue 2: Models Not Appearing in Xcode's Model List

**Symptoms:** Ollama provider is added successfully, but no models show in the dropdown

**Solutions:**
- **Confirm models are installed:** Run `ollama list` in Terminal to verify models are downloaded
- **Restart Xcode completely:** Quit Xcode (Cmd + Q) and reopen it
- **Check Ollama logs:** View errors with `cat ~/.ollama/logs/server.log`
- **Re-add the provider:** Remove the Ollama provider in Xcode Settings > Intelligence and add it again
- **Verify the endpoint:** Ensure you used `http://localhost:11434/v1` (the `/v1` suffix is required)
- **Test with curl:** Run this command to check if models are accessible:
  ```bash
  curl http://localhost:11434/v1/models
  ```
  This should return a JSON list of available models
- **Update Ollama:** Ensure you have the latest version by running `brew upgrade ollama` or downloading the latest version from ollama.com

### Issue 3: Slow Response Times or Model Hangs

**Symptoms:** Coding Assistant takes longer than 30 seconds to respond, or responses never complete

**Solutions:**
- **Choose a smaller model:** If using a 32B model on 16 GB RAM, switch to 7B or 13B models
- **Check available RAM:** Open **Activity Monitor** (Applications > Utilities) and verify sufficient free memory
- **Close other applications:** Free up resources by closing browser tabs, Docker containers, or memory-intensive apps
- **Check model is fully loaded:** Run `ollama ps` to see if the model is still loading
- **Increase swap space:** For Intel Macs or Macs with limited RAM, ensure adequate swap space is available
- **Restart Ollama:** Sometimes the service needs a fresh start: `killall Ollama` then reopen the app
- **Use Apple Silicon Macs:** Performance is significantly better on M1/M2/M3/M4 chips compared to Intel

### Issue 4: "Error: model not found" in Coding Assistant

**Symptoms:** Xcode shows an error when trying to use the selected model

**Solutions:**
- **Verify model name exactly matches:** Run `ollama list` and ensure the model name in Xcode matches exactly (including version tag like `:7b`)
- **Re-download the model:** Sometimes downloads get corrupted:
  ```bash
  ollama rm qwen2.5-coder:7b
  ollama pull qwen2.5-coder:7b
  ```
- **Check disk space:** Ensure sufficient space is available in `~/.ollama/models`
- **Restart both Ollama and Xcode:** Quit both applications completely and restart

### Issue 5: High CPU or Fan Noise During Inference

**Symptoms:** Mac fans run loudly and CPU usage is at 100% when using local models

**Solutions:**
- **Expected behavior:** This is normal for large models (13B+) on older Macs
- **Use smaller models:** Switch to 3B or 7B parameter models for quieter operation
- **Limit concurrent requests:** Wait for one response to complete before sending another
- **Check background processes:** Ensure no other intensive tasks are running (use Activity Monitor)
- **Consider GPU acceleration:** Apple Silicon Macs automatically use Neural Engine and GPU, but Intel Macs rely more on CPU

## Additional Tips

### Best Practices for Model Selection

- **For 8 GB RAM Macs:** Use 3B or 7B models (qwen2.5-coder:7b, codellama:7b)
- **For 16 GB RAM Macs:** Use 7B or 13B models (qwen2.5-coder:14b, codellama:13b)
- **For 32 GB+ RAM Macs:** Use 14B, 32B, or 34B models (qwen2.5-coder:32b, codellama:34b)
- **For Swift/iOS development:** Qwen2.5-Coder and CodeLlama perform exceptionally well
- **For Python data science:** Consider codellama-python or deepseek-coder
- **For web development:** Yi-Coder models excel at JavaScript/TypeScript

### Privacy and Security Benefits

- **Complete data privacy:** All code and prompts stay on your Mac—nothing is sent to external servers
- **Offline development:** Continue coding with AI assistance even without internet connectivity
- **Sensitive codebase protection:** Ideal for proprietary, classified, or NDA-protected projects
- **No telemetry:** Ollama doesn't collect usage data or send analytics
- **Full control:** You choose exactly which models to use and when to update them

### Performance Optimization Tips

- **Keep models in memory:** Ollama caches models after first use for faster subsequent responses
- **Use M1/M2/M3/M4 Macs:** Apple Silicon provides 2-5x faster inference than Intel Macs
- **Monitor memory usage:** Run `ollama ps` to see which models are loaded
- **Close unused models:** Free RAM by stopping models you're not actively using
- **Experiment with quantization:** Smaller quantized models (Q4, Q5) offer faster speeds with minimal quality loss

### Multi-Model Workflow

You can install multiple models and switch between them in Xcode:

- **Quick tasks:** Use lightweight 7B models for code completion and simple questions
- **Complex refactoring:** Switch to 32B models for architectural analysis and large-scale changes
- **Code reviews:** Use DeepSeek-Coder for specialized code quality analysis

### Updating Ollama and Models

To update Ollama to the latest version:

```bash
brew upgrade ollama  # If installed via Homebrew
```

Or re-download from ollama.com if installed manually.

To update models (new versions are released regularly):

```bash
ollama pull qwen2.5-coder:7b  # Re-pulls latest version
```

### Comparing Local vs. Cloud Models

**Local Models (Ollama) Advantages:**
- ✅ No API costs
- ✅ Complete privacy
- ✅ Offline functionality
- ✅ No rate limits
- ✅ Predictable performance

**Cloud Models (ChatGPT, Claude) Advantages:**
- ✅ Larger context windows (200K+ tokens)
- ✅ More advanced reasoning (GPT-4o, Claude Sonnet)
- ✅ No hardware requirements
- ✅ Always up-to-date with latest models

**Recommended Hybrid Approach:**
- Use **local models** for routine coding tasks, refactoring, and debugging
- Use **cloud models** for complex architecture decisions, advanced code reviews, and large codebase analysis

### Next Steps After Setup

1. **Explore different models:** Try Qwen2.5-Coder, CodeLlama, and DeepSeek-Coder to find your favorite
2. **Test with real projects:** Use the Coding Assistant for actual development tasks
3. **Learn prompt engineering:** Craft better prompts for more accurate code generation
4. **Integrate with CI/CD:** Advanced users can use Ollama's API in build scripts for automated code analysis
5. **Join the community:** Visit ollama.com/discord or github.com/ollama/ollama for tips and model recommendations

## Related Articles

- KB-007: How to access the new Intelligence settings tab in Xcode preferences
- KB-031: How to access the Coding Assistant in Xcode 26 (ChatGPT integration)
- KB-041: How to create an Anthropic API account at console.anthropic.com
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-046: How to switch between ChatGPT and Claude in the Coding Assistant
- KB-050: How to compare ChatGPT and Claude responses side-by-side in Xcode

## Sources

1. **Ollama Official Website**
   https://ollama.com
   Accessed: November 17, 2025

2. **Ollama Documentation - Xcode Integration**
   https://docs.ollama.com/integrations/xcode
   Accessed: November 17, 2025

3. **Ollama GitHub Repository**
   https://github.com/ollama/ollama
   Accessed: November 17, 2025

4. **Ollama Documentation - Troubleshooting**
   https://docs.ollama.com/troubleshooting
   Accessed: November 17, 2025

5. **Connecting Xcode's Coding Assistant to Your Local Ollama LLMs - Ronnie Rocha**
   https://ronnierocha.dev/blog/connecting-xcodes-coding-assistant-to-your-local-ollama-llms/
   Accessed: November 17, 2025

6. **Use Custom Models in the New Xcode 26 Intelligence - Wendy Liga**
   https://wendyliga.com/blog/xcode-26-custom-model/
   Accessed: November 17, 2025

7. **Top 10 Best Ollama Models for Developers in 2025 - Collabnix**
   https://collabnix.com/best-ollama-models-for-developers-complete-2025-guide-with-code-examples/
   Accessed: November 17, 2025

8. **Choosing the Best Ollama Model for Your Coding Projects: A 2025 Developer's Guide - CodeGPT**
   https://www.codegpt.co/blog/choosing-best-ollama-model
   Accessed: November 17, 2025

9. **Qwen2.5-Coder Official Page - Ollama Library**
   https://ollama.com/library/qwen2.5-coder
   Accessed: November 17, 2025

10. **How to Fix Ollama API Connection Refused Error - Markaicode**
    https://markaicode.com/fix-ollama-api-connection-refused-error-troubleshooting/
    Accessed: November 17, 2025

11. **Ollama Guide: Run Large Language Models Locally 2025 - Collabnix**
    https://collabnix.com/ollama-complete-guide-how-to-run-large-language-models-locally-in-2025/
    Accessed: November 17, 2025

12. **Local LLMs for Mobile Development - Brenno Giovanini de Moura (Medium)**
    https://onnerb.medium.com/local-llms-for-mobile-development-5e65ba8d2890
    Accessed: November 17, 2025

---
*This article is part of the Xcode v26.1+ knowledge base*
