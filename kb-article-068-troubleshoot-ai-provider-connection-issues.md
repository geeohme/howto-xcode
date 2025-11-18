# How to troubleshoot AI provider connection issues

**Article ID:** KB-068
**Difficulty:** Intermediate
**Last Updated:** November 17, 2025
**Estimated Time:** 20-40 minutes

## Overview

Xcode 26.1+ integrates AI-powered Coding Intelligence features that rely on connections to external AI providers like OpenAI (ChatGPT) and Anthropic (Claude). Connection failures, timeouts, and authentication errors can prevent these features from working properly. This guide provides systematic troubleshooting steps to diagnose and resolve common AI provider connection issues, including network problems, API key configuration errors, firewall restrictions, proxy conflicts, and service outages. Understanding how to troubleshoot these issues ensures reliable access to AI assistance during development.

## Prerequisites

- Xcode 26.1 or later installed
- macOS 15.0 (Sequoia) or later (macOS 26 Tahoe recommended for full features)
- At least one AI model provider configured (ChatGPT, Claude, or custom provider)
- Administrator access to your Mac for network and firewall settings
- Access to your AI provider's account dashboard (OpenAI or Anthropic)
- Basic understanding of network concepts (proxies, firewalls, DNS)
- Terminal.app access for command-line diagnostics

## Steps

### Step 1: Verify the Symptom and Error Message

Before troubleshooting, clearly identify the connection issue:

1. Open **Xcode** and navigate to a project
2. Open the **Coding Assistant** (press `Cmd + Shift + A` or go to **Editor > Show Coding Assistant**)
3. Try sending a test message to your AI model:
   ```
   Hello, can you help me with Swift code?
   ```
4. Note the exact error message or symptom you encounter:
   - **"Connection timeout"** or **"Request timed out"**: Network or firewall blocking requests
   - **"Authentication failed"** or **"Invalid API key"**: API key configuration problem
   - **"Service unavailable"** or **"503 error"**: AI provider service outage
   - **"Rate limit exceeded"** or **"Over daily limit"**: API usage quota exceeded
   - **"Model not available"** or **"Provider disconnected"**: Configuration or regional issue
   - **Assistant panel is blank** or doesn't respond: Interface or caching problem
   - **"Intelligence features unavailable in your region"**: Geographic restriction
5. Take a screenshot of the error for reference
6. Check the **Console.app** for detailed error logs:
   - Open **Applications > Utilities > Console.app**
   - Filter for "Xcode" in the search bar
   - Look for error messages related to "Intelligence" or "Coding Assistant"

### Step 2: Check AI Provider Service Status

Verify that the AI service itself is operational:

1. **For OpenAI (ChatGPT):**
   - Visit **https://status.openai.com** in your web browser
   - Check for any ongoing incidents or maintenance
   - Look specifically for "API" service status (not just ChatGPT web interface)
   - If there's an outage, wait until service is restored before continuing

2. **For Anthropic (Claude):**
   - Visit **https://status.anthropic.com**
   - Verify "Claude API" shows as "Operational"
   - Check for any degraded performance notifications
   - Review recent incident reports that might affect your region

3. **For Apple Developer Services:**
   - Visit **https://developer.apple.com/support/system-status/**
   - Verify "Xcode Automatic Configuration" is available (not grayed out)
   - If Xcode services are offline, some configuration features may be unavailable

4. If all services show as operational, the issue is likely on your end—proceed to next steps

### Step 3: Verify API Key Configuration

Ensure your API key is correctly configured in Xcode:

1. Open **Xcode > Settings** (or **Xcode > Preferences** on some systems)
2. Click the **Intelligence** tab
3. Look under **Model Providers** section
4. For each configured provider (ChatGPT, Claude, or custom):
   - Verify the provider appears in the list
   - Check for a **green checkmark** or **"Connected"** status indicator
   - If you see a **red X** or **"Disconnected"** status, the configuration has failed

5. **Reconfigure the API key:**
   - Click on the provider name to select it
   - Click the **"Edit"** button or **gear icon**
   - Re-enter your API key carefully (copy-paste from your provider's dashboard)
   - For **OpenAI**: API key format is `sk-proj-...` or `sk-...` (48-51 characters)
   - For **Anthropic**: API key format is `sk-ant-...` (starts with "sk-ant-")
   - Ensure there are **no extra spaces** before or after the key
   - Click **"Save"** or **"Apply"**

6. **Verify API key validity on provider dashboard:**
   - **OpenAI**: Go to **https://platform.openai.com/api-keys**
     - Log in to your account
     - Verify your API key is listed and not revoked
     - Check the **"Last used"** timestamp to confirm it's working
     - Verify your account has available credits under **Settings > Billing**
   - **Anthropic**: Go to **https://console.anthropic.com/settings/keys**
     - Verify your API key is active
     - Check your usage limits and remaining credits

7. **Test the connection:**
   - In Xcode Intelligence settings, click **"Test Connection"** button (if available)
   - Or return to the Coding Assistant and try sending a simple message again

### Step 4: Check Network Connectivity

Verify your Mac can reach AI provider servers:

1. **Test basic internet connectivity:**
   - Open **Safari** or any web browser
   - Navigate to **https://www.google.com** to confirm internet access
   - If internet is down, troubleshoot your network connection first

2. **Test AI provider endpoint connectivity:**
   - Open **Terminal.app** (Applications > Utilities > Terminal)
   - Test OpenAI connectivity:
     ```bash
     curl -I https://api.openai.com/v1/models
     ```
   - You should see **HTTP/2 200** or **HTTP/2 401** (401 is okay—it means the server is reachable)
   - Test Anthropic connectivity:
     ```bash
     curl -I https://api.anthropic.com/v1/messages
     ```
   - Expected response: **HTTP/2 200** or **HTTP/2 401**

3. **Check DNS resolution:**
   - In Terminal, run:
     ```bash
     nslookup api.openai.com
     nslookup api.anthropic.com
     ```
   - Each should return IP addresses
   - If you see **"server can't find"** errors, you have a DNS issue:
     - Try switching DNS servers: **System Settings > Network > Wi-Fi > Details > DNS**
     - Add Google DNS: `8.8.8.8` and `8.8.4.4`
     - Or Cloudflare DNS: `1.1.1.1` and `1.0.0.1`
     - Click **"OK"** and **"Apply"**

4. **Test for timeout issues:**
   - Run a connectivity test with timeout:
     ```bash
     time curl -m 10 https://api.openai.com/v1/models
     ```
   - If the command takes more than 10 seconds and times out, you have network latency or firewall issues
   - Proceed to Steps 5 and 6 to check firewall and proxy settings

### Step 5: Check Firewall Settings

Firewalls can block Xcode's connections to AI providers:

1. **Check macOS Firewall:**
   - Open **System Settings > Network > Firewall**
   - If Firewall is **"On"**, it may be blocking Xcode
   - Click **"Options"** button
   - Look for **"Xcode"** in the list of applications
   - Ensure Xcode is set to **"Allow incoming connections"**
   - If Xcode is not listed:
     - Click the **"+"** button
     - Navigate to **Applications** folder
     - Select **Xcode.app** and click **"Add"**
     - Set to **"Allow incoming connections"**
   - Click **"OK"** to save changes

2. **Test with firewall temporarily disabled** (only for troubleshooting):
   - In **System Settings > Network > Firewall**, toggle Firewall to **"Off"**
   - Test the AI connection in Xcode again
   - If it works now, the firewall was blocking the connection
   - **Re-enable the firewall** immediately after testing
   - Add proper firewall rules for Xcode instead of leaving it disabled

3. **Check corporate or network firewall:**
   - If you're on a corporate network, contact your IT department
   - Over 40% of connectivity failures are due to restrictive corporate firewalls
   - Request that these domains be whitelisted:
     - **api.openai.com** (for ChatGPT)
     - **api.anthropic.com** (for Claude)
     - Any other custom AI provider endpoints you use
   - Port **443 (HTTPS)** must be open for outbound connections
   - HTTPS Interception (SSL Inspection) must be disabled for AI provider domains

4. **Check third-party firewall software:**
   - If you use **Little Snitch**, **Lulu**, or other firewall apps:
     - Open the firewall application
     - Check for rules blocking Xcode or AI provider domains
     - Add allow rules for Xcode to connect to AI providers
     - Restart Xcode after changing firewall rules

### Step 6: Check for VPN or Proxy Interference

VPNs and proxies can disrupt AI provider connections:

1. **Disable VPN temporarily:**
   - If you're using a VPN (corporate or personal), disconnect it
   - Open **System Settings > VPN** and toggle your VPN connection **"Off"**
   - Test the AI connection in Xcode again
   - If it works, the VPN was interfering with the connection

2. **Configure VPN split tunneling:**
   - If you need the VPN to remain active, configure **split tunneling**
   - Contact your VPN provider or IT department for instructions
   - Add AI provider domains to the split tunnel exclusion list:
     - api.openai.com
     - api.anthropic.com
   - This allows AI traffic to bypass the VPN tunnel

3. **Check proxy settings:**
   - Open **System Settings > Network > Wi-Fi** (or Ethernet)
   - Click **"Details"** button for your active connection
   - Click **"Proxies"** tab
   - Note if any proxies are configured (HTTP, HTTPS, SOCKS)
   - Try **unchecking all proxy options** to disable proxies
   - Click **"OK"** and test the connection

4. **Configure proxy for AI providers (if proxy is required):**
   - If your organization requires a proxy, Xcode must be configured to use it
   - Unfortunately, Xcode 26.1 does not have built-in proxy configuration for AI providers
   - Workaround using environment variables:
     - Open **Terminal.app**
     - Launch Xcode from Terminal with proxy variables:
       ```bash
       export http_proxy="http://proxy.company.com:8080"
       export https_proxy="http://proxy.company.com:8080"
       open -a Xcode
       ```
     - Replace proxy address with your actual proxy server
   - Test if AI connection works when launched this way

5. **Use Proxyman or Charles for debugging:**
   - Download **Proxyman** from https://proxyman.io (free trial available)
   - Install and launch Proxyman
   - Install Proxyman's SSL certificate for HTTPS decryption
   - **Completely quit and relaunch Xcode** to pick up proxy settings
   - In Proxyman, watch for connection attempts to ai.openai.com or api.anthropic.com
   - Check for connection errors, timeouts, or blocked requests
   - This helps identify exactly where the connection is failing

### Step 7: Verify Regional Availability

Some features may be restricted based on your location:

1. **Check Apple Intelligence availability:**
   - Apple Intelligence features require macOS 26 (Tahoe) or later
   - Only available in certain regions initially
   - Open **System Settings > Apple Intelligence & Siri**
   - Verify that Apple Intelligence is available and enabled
   - If you see **"Not available in your region"**:
     - Change your **Region** in **System Settings > General > Language & Region**
     - Set region to **"United States"** (most features available here first)
     - Restart your Mac after changing region
     - Note: This may affect date/time formats and other locale settings

2. **Check AI provider regional restrictions:**
   - OpenAI API is available in most countries, but some are restricted
   - Anthropic Claude API is available globally but may have different limits
   - If using a VPN to access AI providers, ensure the VPN location is not in a restricted region
   - Check your AI provider's documentation for supported regions

3. **Verify Xcode language and region settings:**
   - Open **Xcode > Settings > Intelligence**
   - Check if there are any region-specific warnings or restrictions displayed
   - Ensure your Mac's language is set to a supported language (English works best for AI features)

### Step 8: Check API Usage Limits and Quotas

API rate limits and quota exhaustion can cause connection failures:

1. **Check OpenAI usage and limits:**
   - Go to **https://platform.openai.com/usage**
   - Log in to view your current usage statistics
   - Check if you've exceeded your **rate limit** (requests per minute)
   - Check if you've exhausted your **token quota** or **monthly credits**
   - For free tier users: ChatGPT free tier has daily limits that can be quickly exhausted
   - For paid API users: Verify your billing is active under **Settings > Billing**
   - If you've hit limits:
     - Wait for the rate limit window to reset (usually 1-60 minutes)
     - Upgrade to a higher tier plan if you need more quota
     - Add credits to your account if balance is depleted

2. **Check Anthropic usage and limits:**
   - Go to **https://console.anthropic.com/settings/usage**
   - Review your current token usage for the billing period
   - Check if you've exceeded your organization's limits
   - Claude API requires a paid account with credits
   - Add credits under **Settings > Billing** if needed

3. **Monitor usage in real-time:**
   - Keep provider dashboards open while troubleshooting
   - After each test query in Xcode, refresh the usage page
   - Verify that usage increments (confirms connection is working)
   - If usage doesn't increment, the connection isn't reaching the API

4. **Error message interpretation:**
   - **"Rate limit exceeded"**: Too many requests in short time—wait and retry
   - **"Quota exceeded"**: Monthly or daily limit reached—add credits or upgrade
   - **"Insufficient quota"**: Prepaid credits depleted—purchase more credits
   - **"Invalid API key"**: Key has been revoked or deactivated—generate new key

### Step 9: Clear Xcode Caches and Reset Configuration

Corrupted caches can cause persistent connection issues:

1. **Clear Xcode Derived Data:**
   - In Xcode, go to **Window > Devices and Simulators**
   - Close the window
   - In Finder, press **Cmd + Shift + G** to open "Go to Folder"
   - Enter: `~/Library/Developer/Xcode/DerivedData`
   - Delete all folders inside (or move to Trash)
   - This clears build caches but not AI settings

2. **Clear Xcode Intelligence cache:**
   - Quit Xcode completely (**Cmd + Q**)
   - Open **Terminal.app**
   - Run this command to clear Intelligence caches:
     ```bash
     rm -rf ~/Library/Developer/Xcode/UserData/IDEIntelligence*
     ```
   - Also clear any cached credentials:
     ```bash
     rm -rf ~/Library/Caches/com.apple.dt.Xcode*
     ```
   - Restart Xcode

3. **Reset Xcode Intelligence settings:**
   - Open **Xcode > Settings > Intelligence**
   - For each configured Model Provider:
     - Click to select the provider
     - Click **"Remove"** or **"-"** button to delete it
   - Click **"OK"** to close Settings
   - Quit and restart Xcode
   - Reconfigure your AI providers from scratch (see KB-034, KB-043)
   - This ensures clean configuration without corrupted settings

4. **Reset network configuration (advanced):**
   - If nothing else works, reset macOS network settings:
   - Open **Terminal.app**
   - Run (requires admin password):
     ```bash
     sudo networksetup -setv6off Wi-Fi
     sudo networksetup -setv6automatic Wi-Fi
     sudo dscacheutil -flushcache
     sudo killall -HUP mDNSResponder
     ```
   - This flushes DNS cache and resets network stack
   - Restart your Mac after running these commands

### Step 10: Test with Alternative Configuration

Isolate the problem by testing different configurations:

1. **Try a different AI model:**
   - In the Coding Assistant, click the **model dropdown**
   - Switch from ChatGPT to Claude (or vice versa)
   - Try sending a test message
   - If the alternative works, the issue is specific to one provider

2. **Try a different network:**
   - Connect to a different Wi-Fi network (home vs. office)
   - Or use **iPhone Personal Hotspot** for testing
   - If it works on a different network, the issue is with your primary network (firewall, proxy, or ISP blocking)

3. **Test on a different Mac (if available):**
   - Configure the same API key on another Mac with Xcode 26.1+
   - If it works on the other Mac, the issue is system-specific on your Mac
   - Suggests corrupted Xcode installation or system configuration problem

4. **Create a new macOS user account for testing:**
   - Open **System Settings > Users & Groups**
   - Create a new **Administrator** user account
   - Log out and log into the new account
   - Install Xcode (or use existing installation)
   - Configure AI provider with your API key
   - Test the connection
   - If it works in the new account, your main user account has a configuration conflict
   - You may need to migrate to the new account or manually fix settings in the original account

5. **Reinstall Xcode (last resort):**
   - If all else fails, completely remove and reinstall Xcode
   - Quit Xcode completely
   - Open **Applications** folder and drag **Xcode.app** to **Trash**
   - Empty Trash
   - Open **Terminal** and remove all Xcode data:
     ```bash
     rm -rf ~/Library/Developer/Xcode
     rm -rf ~/Library/Caches/com.apple.dt.Xcode*
     rm -rf ~/Library/Preferences/com.apple.dt.Xcode*
     ```
   - Download fresh Xcode 26.1+ from the Mac App Store
   - Install and configure AI providers from scratch

## Expected Results

After completing these troubleshooting steps, you should:

- **Identify the root cause** of your AI provider connection issue (network, configuration, service outage, or quota)
- **Successfully establish a connection** between Xcode and your AI provider
- See a **green checkmark** or **"Connected"** status in Intelligence settings
- **Send test messages** to the AI Coding Assistant and receive responses without errors
- **Verify API usage** increments on your provider dashboard when making requests
- Have a **working diagnosis process** for future connection issues
- Understand **preventive measures** to avoid connection problems

**Typical resolution outcomes:**
- **50-60% of issues**: API key misconfiguration or expired—fixed by re-entering key
- **25-30% of issues**: Network firewall or proxy blocking—fixed by adjusting network settings
- **10-15% of issues**: API quota exceeded—fixed by adding credits or waiting for reset
- **5-10% of issues**: Service outage or regional restriction—must wait or change settings

## Troubleshooting

### Issue 1: Connection Works Intermittently

**Problem:** AI connection works sometimes but fails randomly with timeout errors.

**Solution:**
- **Check for rate limiting**: You may be hitting per-minute request limits
  - Spread out your requests instead of sending many in quick succession
  - Monitor your provider dashboard for rate limit messages
- **Network instability**: Your internet connection may be unstable
  - Run a ping test: `ping -c 10 api.openai.com` in Terminal
  - Look for packet loss or high latency (>500ms)
  - Consider switching to a wired Ethernet connection
- **VPN fluctuations**: VPN connections can drop intermittently
  - Check VPN connection stability
  - Try a different VPN server location
- **Provider API instability**: Check status pages for "degraded performance"
  - Even if not a full outage, providers can have partial issues
- **Try increasing timeout settings**: Some network configurations need longer timeouts
  - Unfortunately Xcode 26.1 doesn't expose timeout configuration
  - Workaround: Ensure your network has consistent low-latency access

### Issue 2: "Authentication Failed" Despite Correct API Key

**Problem:** You've verified your API key is correct, but Xcode still shows authentication errors.

**Solution:**
- **API key format issues**:
  - Ensure no whitespace before/after the key when pasting
  - For OpenAI: Key must start with `sk-` and be 48-51 characters
  - For Anthropic: Key must start with `sk-ant-`
- **API key permissions**:
  - OpenAI: Verify the key has "All" permissions or at least "Model capabilities"
  - Anthropic: Ensure the key hasn't been restricted to specific models
  - Check your provider dashboard for key permission settings
- **Account status issues**:
  - Verify your account is active and in good standing
  - Check for any security alerts or verification requirements on your account
  - Confirm billing is active for paid accounts
- **Try regenerating the API key**:
  - On OpenAI or Anthropic dashboard, revoke the old key
  - Generate a completely new API key
  - Update Xcode with the new key immediately
- **API endpoint mismatch**:
  - For custom providers, verify the URL is formatted correctly
  - OpenAI-compatible API URL should be `https://api.openai.com/` (without `/v1`)
  - Custom endpoints must be OpenAI-compatible in format

### Issue 3: Works on Another Computer But Not Yours

**Problem:** Same API key works on a colleague's Mac but not yours.

**Solution:**
- **System-specific firewall or security**:
  - Your Mac may have additional security software installed
  - Check for corporate endpoint security (Crowdstrike, Carbon Black, etc.)
  - Contact IT to whitelist Xcode and AI provider domains
- **macOS version differences**:
  - Verify you're both on the same macOS version
  - Older macOS versions may have SSL/TLS compatibility issues
  - Update to macOS 26 (Tahoe) or latest version of macOS 15 (Sequoia)
- **Network configuration differences**:
  - Your network may have different firewall rules
  - Compare proxy settings between the two Macs
  - Test on the same network as your colleague to isolate network vs. system issues
- **Xcode version differences**:
  - Ensure both Macs have the exact same Xcode version
  - Check: **Xcode > About Xcode** for version and build number
  - Update to Xcode 26.1.1 or later for best compatibility
- **User account restrictions**:
  - Your user account may have restricted network access
  - Test with an Administrator account
  - Check **System Settings > Screen Time** for any restrictions

### Issue 4: ChatGPT Shows "Over Daily Limit" Despite Paid Subscription

**Problem:** You have ChatGPT Plus subscription, but Xcode shows daily limit exceeded.

**Solution:**
- **ChatGPT Plus vs. OpenAI API confusion**:
  - **Important**: ChatGPT Plus subscription ($20/month) is different from OpenAI API access
  - Xcode uses the **OpenAI API**, not ChatGPT web interface
  - You need a separate **OpenAI API account** with pay-as-you-go credits
  - Go to **https://platform.openai.com** (not chatgpt.com)
  - Add billing and credits under **Settings > Billing**
- **Free tier limitations**:
  - If using OpenAI's free API tier, daily limits are very low
  - Upgrade to a paid API plan with sufficient credits
- **Rate limits vs. daily limits**:
  - You may be hitting per-minute rate limits, not daily limits
  - Space out your requests more over time
- **Check actual usage**:
  - Visit https://platform.openai.com/usage to see real-time usage
  - Verify you haven't actually exceeded limits
  - Sometimes the error message is misleading for other connection issues

### Issue 5: "Intelligence Features Unavailable" Error

**Problem:** Xcode shows that Intelligence features are not available.

**Solution:**
- **macOS version requirement**:
  - Intelligence tab requires macOS 15.0 (Sequoia) or later
  - For full features, upgrade to macOS 26 (Tahoe)
  - Check: **About This Mac** to verify your macOS version
- **Xcode version requirement**:
  - Coding Intelligence with AI requires Xcode 26.1+
  - Update Xcode from the Mac App Store
- **Regional restrictions**:
  - Apple Intelligence features are region-dependent
  - Change region to **United States** in **System Settings > General > Language & Region**
  - Restart Mac after changing region
- **Apple Intelligence not enabled**:
  - Go to **System Settings > Apple Intelligence & Siri**
  - Enable **Apple Intelligence** if the option is available
  - Join the waitlist if required
- **Hardware limitations**:
  - Some Intelligence features require Apple Silicon (M1 or newer)
  - Intel Macs may have limited functionality
  - Check **About This Mac** to confirm you have an M-series chip

## Additional Tips

### Best Practices for Maintaining Reliable AI Connections

- **Monitor API usage regularly** to avoid unexpected quota exhaustion
- **Set up billing alerts** on your OpenAI/Anthropic dashboard to warn before limits are reached
- **Keep Xcode updated** to the latest version for bug fixes and improved connectivity
- **Document your network configuration** (proxies, VPNs) for quick troubleshooting reference
- **Test AI connection periodically** even when not actively using it to catch issues early
- **Maintain backup API keys** so you can quickly swap if one becomes problematic
- **Save troubleshooting commands** in a text file for quick access when issues arise

### Preventive Measures

- **Whitelist AI domains in your firewall** before issues occur (proactive approach)
- **Configure split-tunneling on your VPN** for AI provider endpoints
- **Use a dedicated API key for Xcode** separate from other tools for better usage tracking
- **Set up multiple model providers** (both ChatGPT and Claude) for redundancy
- **Keep credits prepaid** on your API accounts to avoid disruption
- **Subscribe to provider status notifications** (via email or RSS) to know about outages immediately

### Diagnostic Commands Cheat Sheet

Save these commands for quick troubleshooting:

```bash
# Test OpenAI connectivity
curl -I https://api.openai.com/v1/models

# Test Anthropic connectivity
curl -I https://api.anthropic.com/v1/messages

# Check DNS resolution
nslookup api.openai.com

# Test with timeout
time curl -m 10 https://api.openai.com/v1/models

# Flush DNS cache
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder

# Check network route to API
traceroute api.openai.com

# Test API with your key (replace YOUR_API_KEY)
curl https://api.openai.com/v1/models \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Understanding Connection Error Types

**Timeout errors (-1001):**
- Request takes too long to reach server
- Usually firewall, proxy, or network latency issue
- Solution: Check firewall and proxy settings

**Connection refused (-61):**
- Server actively rejected the connection
- Rare with AI providers unless IP is blocked
- Solution: Check if your IP is blocked, contact provider support

**No internet connection (-1009):**
- Mac has no network connectivity
- Solution: Fix basic internet connection first

**SSL/TLS errors (-1200):**
- Security handshake failed
- Often caused by SSL inspection/HTTPS interception
- Solution: Disable SSL inspection for AI domains

**Host not found (-1003):**
- DNS cannot resolve the API domain
- Solution: Check DNS settings, flush DNS cache

### When to Contact Support

Contact your AI provider's support if:
- **Connection works in browser/Terminal but not in Xcode**: Provider-specific Xcode compatibility issue
- **New API key also fails immediately**: Possible account or service issue
- **Error messages mention "account restricted" or "API access revoked"**: Account status problem
- **Works on all other Macs but not yours with same configuration**: May need provider to investigate

Contact Apple Developer Support if:
- **Xcode crashes when opening Intelligence settings**: Xcode bug
- **Intelligence tab is missing entirely in Xcode 26.1+**: Installation issue
- **All network tests pass but Xcode still can't connect**: Xcode-specific networking bug

### Cost Optimization While Troubleshooting

- **Use shorter test messages** when diagnosing connection issues (e.g., "Hi" instead of long prompts)
- **Each test message costs tokens/credits**, so avoid excessive trial-and-error
- **Use curl commands** for free connectivity testing before testing in Xcode
- **Monitor usage dashboard** during troubleshooting to see when charges occur
- **Disable auto-completion features** temporarily while diagnosing to reduce automated API calls

### Advanced Debugging with Network Tools

**Using Network Utility:**
- Download **Network Utility** from Mac App Store or use built-in tools
- Test port 443 connectivity to API endpoints
- Check for packet loss or high latency

**Using Wireshark (Advanced Users):**
- Install Wireshark for deep packet inspection
- Filter for traffic to `api.openai.com` or `api.anthropic.com`
- Identify where packets are being dropped or blocked
- Requires technical networking knowledge

**Using System Logs:**
- Open **Console.app**
- Filter for "Xcode" AND "Intelligence"
- Look for detailed error codes and stack traces
- Particularly useful for authentication and SSL errors

## Related Articles

- KB-031: How to access the Coding Assistant in Xcode 26 with ChatGPT
- KB-034: How to connect a paid OpenAI account for ChatGPT in Xcode
- KB-043: How to add Claude as a model provider in Xcode Intelligence settings
- KB-044: How to configure the Claude API endpoint
- KB-045: How to set the API key header (x-api-key) for Claude authentication
- KB-046: How to switch between ChatGPT and Claude Sonnet 4 in the Coding Assistant
- KB-055: How to have AI explain error messages and suggest fixes
- KB-050: How to compare responses between ChatGPT and Claude for the same coding task

## Sources

- Apple releases Xcode 26.1.1 with coding intelligence improvements - https://9to5mac.com/2025/11/11/apple-releases-xcode-26-1-1-with-coding-intelligence-improvements/ (Accessed: November 17, 2025)
- ChatGPT Xcode Integration Not Working: Fix Guide 2025 - https://www.byteplus.com/en/topic/550877 (Accessed: November 17, 2025)
- Troubleshooting Network Issues in Xcode - An Expert Guide for Developers - https://moldstud.com/articles/p-troubleshooting-network-issues-in-xcode-an-expert-guide-for-developers (Accessed: November 17, 2025)
- Carlo Zottmann - Use any OpenAI-compatible LLM provider in Xcode 26 - https://zottmann.org/2025/06/11/use-any-openaicompatible-llm-provider.html (Accessed: November 17, 2025)
- Using Claude with Coding Assistant in Xcode 26 - https://simonbs.dev/posts/using-claude-with-coding-assistant-in-xcode-26/ (Accessed: November 17, 2025)
- I'm getting an API connection error. How can I fix it? | Claude Help Center - https://support.claude.com/en/articles/10366432-i-m-getting-an-api-connection-error-how-can-i-fix-it (Accessed: November 17, 2025)
- How to Fix Claude Code API Error: Request Timed Out - https://www.cursor-ide.com/blog/claude-code-api-timeout-error-fix (Accessed: November 17, 2025)
- Xcode 26.1.1 Release Notes | Apple Developer Documentation - https://developer.apple.com/documentation/xcode-release-notes/xcode-26_1-release-notes (Accessed: November 17, 2025)
- Writing code with intelligence in Xcode | Apple Developer Documentation - https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode (Accessed: November 17, 2025)
- Use Apple products on enterprise networks - Apple Support - https://support.apple.com/en-us/101555 (Accessed: November 17, 2025)
- Michael Tsai - Blog - Xcode 26.1 - https://mjtsai.com/blog/2025/11/05/xcode-26-1/ (Accessed: November 17, 2025)

---
*This article is part of the Xcode v26.1+ knowledge base*
