# OpenClaw Setup Guide: Local Mac (Your Existing Computer)

## 📋 Overview

This guide walks you through setting up OpenClaw on your existing Mac laptop or desktop computer. This is the quickest way to test OpenClaw, but NOT recommended for 24/7 operation.

**Time Required:** 30-60 minutes
**Difficulty:** Beginner
**Cost:** Free (just API costs)

---

## ⚠️ Before You Start

### Limitations of This Setup

- Your Mac must stay **ON and AWAKE** for OpenClaw to work
- Closing your laptop lid will disconnect OpenClaw
- Taking your laptop to a coffee shop will change networks (may cause issues)
- Uses resources (RAM, CPU) on your daily machine
- Your personal files are accessible to OpenClaw (security risk)

### When to Use This Setup

✅ Testing OpenClaw for the first time
✅ Learning how it works
✅ Short-term projects
✅ Development/hacking on OpenClaw

### When NOT to Use This Setup

❌ 24/7 AI assistant (use Mac Mini or VPS instead)
❌ If you have sensitive data on this machine
❌ If you need access from anywhere

---

## 📝 Prerequisites Checklist

Before starting, make sure you have:

- [ ] Mac running macOS 12 (Monterey) or later
- [ ] At least 8GB RAM (16GB recommended)
- [ ] At least 10GB free disk space
- [ ] Stable internet connection
- [ ] Anthropic API key (get from https://console.anthropic.com)
- [ ] Separate phone number for WhatsApp (eSIM recommended)

---

## Step 1: Install Homebrew (Package Manager)

Homebrew is a tool that makes installing software on Mac easy. Open **Terminal** (press `Cmd + Space`, type "Terminal", press Enter).

### Check if Homebrew is Already Installed

```bash
brew --version
```

If you see a version number (like `Homebrew 4.x.x`), skip to Step 2.

If you see "command not found", install Homebrew:

### Install Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**What happens:**
1. You'll be asked for your Mac password (for admin access)
2. Press ENTER when prompted
3. Wait 2-5 minutes for installation

### After Installation (IMPORTANT for Apple Silicon Macs)

If you have an M1/M2/M3/M4 Mac, you MUST run these commands:

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### Verify Installation

```bash
brew --version
```

You should see something like: `Homebrew 4.2.0`

---

## Step 2: Install Node.js

OpenClaw requires Node.js version 22 or higher.

### Install Node.js via Homebrew

```bash
brew install node@22
```

### Link Node.js (Make it the Default)

```bash
brew link node@22
```

If you get a warning about existing files, run:

```bash
brew link --overwrite node@22
```

### Verify Installation

```bash
node --version
```

You should see: `v22.x.x` (any 22.x version is fine)

```bash
npm --version
```

You should see: `10.x.x` or higher

---

## Step 3: Install OpenClaw

### Method 1: One-Line Installer (Recommended)

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

### Method 2: Manual npm Install

```bash
npm install -g openclaw@latest
```

### Verify Installation

```bash
openclaw --version
```

You should see: `openclaw 2026.x.x`

---

## Step 4: Get Your Anthropic API Key

### Create Anthropic Account

1. Go to: https://console.anthropic.com
2. Click "Sign Up" (or "Sign In" if you have an account)
3. Complete the registration

### Add Payment Method

1. Go to Settings → Billing
2. Add a credit card
3. Set a spending limit (recommend starting with $50/month)

### Generate API Key

1. Go to Settings → API Keys
2. Click "Create Key"
3. Give it a name like "OpenClaw"
4. **COPY THE KEY NOW** - you won't see it again!
5. Save it somewhere safe (password manager, secure note)

The key looks like: `sk-ant-api03-xxxxxxxxxxxx...`

---

## Step 5: Set Up Separate WhatsApp (Important!)

### Why a Separate Number?

OpenClaw connects to WhatsApp as a "linked device". If you use your personal WhatsApp, ALL your messages go through OpenClaw. You don't want that.

### Get an eSIM Phone Number

1. **Buy an eSIM plan** (choose one):
   - [Tello](https://tello.com) - ~$5/month, US numbers
   - [Mint Mobile](https://mintmobile.com) - ~$15/month, US numbers
   - [US Mobile](https://usmobile.com) - ~$10/month, US numbers

2. **Activate the eSIM:**
   - You'll receive a QR code via email
   - On iPhone: Settings → Cellular → Add eSIM → Scan QR code
   - On Android: Settings → Network → SIM → Add eSIM → Scan QR code

3. **Your phone now has 2 numbers!**

### Install WhatsApp Business

1. Download **WhatsApp Business** from App Store (NOT regular WhatsApp)
2. Open WhatsApp Business
3. Register with your **NEW eSIM phone number**
4. When asked for business name, enter anything (e.g., "My AI")

Now you have:
- Regular WhatsApp → Personal number (unchanged)
- WhatsApp Business → New eSIM number (for OpenClaw)

---

## Step 6: Run the Onboarding Wizard

This is where the magic happens!

### Start the Wizard

```bash
openclaw onboard --install-daemon
```

### Wizard Step-by-Step

**1. Gateway Type**
```
? Gateway type: (Use arrow keys)
❯ Local
  Remote
```
Choose: **Local** (press Enter)

---

**2. Model Provider**
```
? Select auth method: (Use arrow keys)
❯ Anthropic API Key
  OpenAI Codex OAuth
  Google Antigravity OAuth
```
Choose: **Anthropic API Key**

---

**3. Enter API Key**
```
? Enter your Anthropic API key:
```
Paste your API key (it won't show on screen for security)
Press Enter

---

**4. Channel Setup**
```
? Which channels do you want to configure?
 ◉ WhatsApp
 ◯ Telegram
 ◯ Discord
 ◯ Slack
```
Use Space to select **WhatsApp**, then press Enter

---

**5. Skills Setup**
```
? Do you want to install skills?
❯ Yes
  No
```
Choose: **Yes** (recommended)

```
? Preferred package manager:
❯ npm
  pnpm
  bun
```
Choose: **npm**

```
? Select skills to install:
❯ Skip for now
  steipete/gog (Google integration)
  ...
```
Choose: **Skip for now** (you can add skills later)

---

**6. Web Search API (Optional)**
```
? Do you have a Brave Search API key?
❯ No
  Yes
```
Choose: **No** (can add later)

---

**7. Daemon Installation**
```
? Install background service?
❯ Yes
  No
```
Choose: **Yes** (this makes OpenClaw start automatically)

---

**8. Gateway Token**
The wizard will generate a security token automatically. 
Note it down if shown, or find it later in the config.

---

## Step 7: Connect WhatsApp

After the wizard completes, connect WhatsApp:

### Start WhatsApp Login

```bash
openclaw channels login --channel whatsapp
```

### What You'll See

A QR code will appear in your terminal like this:

```
█████████████████████████████████
█████████████████████████████████
████ ▄▄▄▄▄ █▄ ▄▄▄█▀█ ▄▄▄▄▄ ████
████ █   █ ██▀█ ▄▀ █ █   █ ████
████ █▄▄▄█ █ ▄▀▄ ▄██ █▄▄▄█ ████
...
```

### Scan the QR Code

1. Open **WhatsApp Business** on your phone (the one with your eSIM number)
2. Go to: **Settings** → **Linked Devices** → **Link a Device**
3. Point your phone camera at the QR code in terminal
4. Wait for "Linked!" confirmation

### Verify Connection

```bash
openclaw channels status
```

You should see:
```
WhatsApp: connected
```

---

## Step 8: Verify Everything Works

### Check System Status

```bash
openclaw status
```

Expected output:
```
Gateway: running
Channels:
  - whatsapp: connected
Auth: configured
```

### Check Health

```bash
openclaw health
```

All checks should show green/OK.

### Security Audit

```bash
openclaw security audit --deep
```

Review any warnings.

---

## Step 9: Start Chatting!

### Option 1: Use WhatsApp

1. Open WhatsApp Business on your phone
2. You should see a chat with your AI
3. Send a message: "Hello! What can you do?"
4. Wait for response (may take 10-30 seconds first time)

### Option 2: Use the Dashboard

```bash
openclaw dashboard
```

Or open in browser: http://127.0.0.1:18789

---

## Step 10: Keep Your Mac Awake

Since you're using your laptop, you need to prevent it from sleeping.

### Prevent Sleep via System Settings

1. **System Settings** → **Battery** (or Energy Saver)
2. Under "Battery" tab:
   - Set "Turn display off after" to Never (or high value)
3. Under "Power Adapter" tab:
   - Turn OFF "Put hard disks to sleep when possible"
   - Turn OFF "Prevent automatic sleeping when display is off"

### Alternative: Use Amphetamine App

1. Download **Amphetamine** from App Store (free)
2. Click the pill icon in menu bar
3. Select "Indefinitely" to prevent sleep

### Keep Terminal Open

The OpenClaw gateway runs in the background, but keeping Terminal open helps with troubleshooting.

---

## 🎉 Congratulations!

You now have OpenClaw running on your Mac! Here's what you can try:

### Example Commands to Your AI

**Via WhatsApp or Dashboard:**
- "What time is it?"
- "Create a todo list for my week"
- "Search the web for the latest AI news"
- "What files are in my Documents folder?"
- "Write a Python script that prints Hello World"

### Useful CLI Commands

```bash
# Check status
openclaw status

# View logs
openclaw logs

# Restart gateway
openclaw gateway restart

# Stop everything
openclaw gateway stop

# Check connected channels
openclaw channels status

# Re-link WhatsApp (if disconnected)
openclaw channels login --channel whatsapp
```

---

## 🔧 Common Issues

### "WhatsApp disconnected after a few days"

This is normal. WhatsApp sessions expire periodically.

**Fix:**
```bash
openclaw channels login --channel whatsapp
```
Then scan the QR code again.

---

### "No response from AI"

Check if the gateway is running:
```bash
openclaw gateway status
```

If it says "not running":
```bash
openclaw gateway start
```

---

### "API key error"

Your API key might be invalid or expired.

**Fix:**
```bash
openclaw configure --section auth
```
Enter your API key again.

---

### "Port already in use"

Another process is using port 18789.

**Fix:**
```bash
# Find what's using the port
lsof -i :18789

# Kill it (replace PID with actual process ID)
kill -9 <PID>

# Restart gateway
openclaw gateway start
```

---

## 📤 Next Steps

1. **Read Security Guide:** `05-SECURITY-HARDENING.md`
2. **Add More Channels:** Telegram, Discord, Slack
3. **Install Skills:** Google Calendar, Gmail, etc.
4. **Consider Upgrade:** Mac Mini for 24/7 operation

---

## 🔄 Upgrading OpenClaw

When new versions are released:

```bash
# Check current version
openclaw --version

# Update to latest
npm update -g openclaw

# Or reinstall
npm install -g openclaw@latest

# Restart gateway after update
openclaw gateway restart
```

---

## 🗑️ Uninstalling

If you want to remove OpenClaw:

```bash
# Stop the service
openclaw gateway stop

# Uninstall
openclaw uninstall

# Remove npm package
npm uninstall -g openclaw

# Remove config files (optional)
rm -rf ~/.openclaw
```

---

*Guide Version: 1.0*
*Last Updated: February 2026*
