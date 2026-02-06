# OpenClaw Setup Guide: Dedicated Mac Mini

## 📋 Overview

This guide walks you through setting up OpenClaw on a **dedicated Mac Mini** - the BEST setup for a personal 24/7 AI assistant. The Mac Mini sits in your home, always on, always available.

**Time Required:** 2-3 hours
**Difficulty:** Beginner-Intermediate
**Cost:** ~$500-600 hardware + API costs

---

## 🏆 Why Mac Mini is the Best Personal Setup

| Feature | Mac Mini | VPS | AWS |
|---------|----------|-----|-----|
| Always On | ✅ | ✅ | ✅ |
| iMessage Support | ✅ | ❌ | ❌ |
| One-time Cost | ✅ | ❌ | ❌ |
| No Monthly Fees | ✅ | ❌ | ❌ |
| Full Local Control | ✅ | ❌ | ❌ |
| Low Power (~10W) | ✅ | N/A | N/A |

---

## 📝 Shopping List

### Required Hardware

| Item | Price | Where to Buy |
|------|-------|--------------|
| Mac Mini (M2 or M4 base model) | $499-599 | Apple Store, Amazon, Best Buy |
| Ethernet Cable (Cat6) | $10-15 | Amazon |

### Optional But Recommended

| Item | Price | Why |
|------|-------|-----|
| USB-C Hub | $30-50 | For keyboard/mouse during setup |
| Keyboard (any USB/Bluetooth) | $20-50 | Temporary for setup |
| Mouse (any USB/Bluetooth) | $15-30 | Temporary for setup |
| Monitor/TV with HDMI | $0 | Use existing TV temporarily |

**Note:** After setup, you can disconnect keyboard/mouse/monitor and control remotely.

### Required Accounts & Services

| Service | Cost | Purpose |
|---------|------|---------|
| Anthropic API | Pay per use (~$2-10/day) | Powers the AI brain |
| eSIM Phone Number | ~$5-10/month | Separate WhatsApp |
| New Apple ID | Free | Isolate from personal data |
| Tailscale | Free (personal) | Remote access |

---

## Part 1: Hardware Setup

### Step 1.1: Unbox and Connect Mac Mini

1. **Unbox** your Mac Mini
2. **Connect power cable** to Mac Mini and wall outlet
3. **Connect Ethernet cable** from Mac Mini to your router
   - Ethernet is more reliable than WiFi for 24/7 operation
   - If you must use WiFi, that's okay too
4. **Connect HDMI** to a monitor or TV (temporary)
5. **Connect keyboard and mouse** (USB or Bluetooth, temporary)
6. **Press power button** on Mac Mini (back of device)

### Step 1.2: Initial macOS Setup

When Mac Mini boots, you'll see the macOS setup wizard.

**IMPORTANT: Create a NEW Apple ID - don't use your personal one!**

1. **Select your country/region**
2. **Keyboard layout:** Choose your preference
3. **Connect to Network:** 
   - If Ethernet connected, it should auto-detect
   - Otherwise, connect to WiFi
4. **Data & Privacy:** Click Continue
5. **Migration Assistant:** Choose **"Don't transfer any information now"**
6. **Apple ID:** Choose **"Create new Apple ID"**
   - Use a NEW email address (create one if needed)
   - This keeps your personal iCloud data OFF this machine
7. **Terms & Conditions:** Agree
8. **Create Computer Account:**
   - Full name: Your name or "OpenClaw Admin"
   - Account name: `admin` (or whatever you prefer)
   - Password: **STRONG PASSWORD** (write it down!)
   - Hint: Leave blank or something only you know
9. **Location Services:** Enable (helps with timezone)
10. **Analytics:** Disable all (privacy)
11. **Screen Time:** Skip/Don't Set Up
12. **Siri:** Disable (not needed)
13. **Choose Your Look:** Light or Dark (your preference)

**Setup complete!** You're now at the macOS desktop.

---

## Part 2: Configure macOS for 24/7 Operation

### Step 2.1: Prevent Sleep & Auto-Start

Open **System Settings** (click Apple menu → System Settings)

#### Energy Settings
1. Go to **General** → **Energy Saver** (or just search "Energy")
2. Toggle ON: **"Prevent automatic sleeping when the display is off"**
3. Toggle ON: **"Start up automatically after a power failure"**
4. Toggle ON: **"Wake for network access"**

#### Lock Screen Settings
1. Go to **Lock Screen**
2. Set **"Start Screen Saver when inactive"** to **Never**
3. Set **"Turn display off on battery when inactive"** to **Never**
4. Set **"Turn display off on power adapter when inactive"** to **1 hour** or **Never**

### Step 2.2: Enable Remote Access

This lets you control the Mac Mini from your laptop/phone without a monitor.

#### Enable Screen Sharing
1. Go to **General** → **Sharing**
2. Toggle ON: **Screen Sharing**
3. Click the (i) info button next to Screen Sharing
4. Under "Allow access for:" select **"All users"** or your specific user
5. Note the address shown (like `vnc://192.168.1.100`)

#### Enable Remote Login (SSH)
1. Still in **Sharing** settings
2. Toggle ON: **Remote Login**
3. Under "Allow access for:" select your user

#### Enable Remote Management (Optional, more features)
1. Toggle ON: **Remote Management**
2. Check all options you want (Observe, Control, etc.)

### Step 2.3: Set Static IP (Recommended)

A static IP makes remote access more reliable.

1. Go to **Network** settings
2. Click on your connection (Ethernet or Wi-Fi)
3. Click **Details...**
4. Go to **TCP/IP** tab
5. Change "Configure IPv4" from DHCP to **Manually**
6. Set:
   - IP Address: `192.168.1.100` (or another unused IP on your network)
   - Subnet Mask: `255.255.255.0`
   - Router: Your router's IP (usually `192.168.1.1`)
7. Click **OK**

**Note:** The exact IPs depend on your network. If unsure, leave it on DHCP.

### Step 2.4: Disable Auto-Updates (Prevent Random Restarts)

1. Go to **General** → **Software Update**
2. Click **Automatic Updates** (gear icon)
3. Toggle OFF: **Install macOS updates**
4. Toggle OFF: **Install application updates from the App Store**
5. Keep ON: **Download new updates when available** (so you know when updates exist)
6. Keep ON: **Install Security Responses and system files** (important!)

**You'll update manually** when convenient, not randomly at 3 AM.

---

## Part 3: Install Required Software

### Step 3.1: Open Terminal

Press `Cmd + Space`, type `Terminal`, press Enter.

### Step 3.2: Install Xcode Command Line Tools

```bash
xcode-select --install
```

A popup appears. Click **Install**, then **Agree** to license.
Wait 5-10 minutes for download and installation.

### Step 3.3: Install Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

When prompted:
- Enter your password
- Press ENTER to continue

**IMPORTANT: After installation, run these commands (copy/paste from the output):**

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

Verify:
```bash
brew --version
```

### Step 3.4: Install Node.js 22

```bash
brew install node@22
brew link node@22
```

Verify:
```bash
node --version    # Should show v22.x.x
npm --version     # Should show 10.x.x or higher
```

### Step 3.5: Install Tailscale (Remote Access)

```bash
brew install tailscale
```

Then:
1. Open **Tailscale** from Applications
2. Click the Tailscale icon in menu bar
3. Click **Log in**
4. Create a Tailscale account (free) or sign in
5. Approve the device

**Why Tailscale?**
- Access your Mac Mini from ANYWHERE in the world
- Secure VPN tunnel
- No port forwarding needed
- Free for personal use

### Step 3.6: Install OpenClaw

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

Or via npm:
```bash
npm install -g openclaw@latest
```

Verify:
```bash
openclaw --version
```

---

## Part 4: Set Up Separate Accounts

### Step 4.1: Get Anthropic API Key

1. Go to: https://console.anthropic.com
2. **Sign up** for a new account (use your NEW email, not personal)
3. Go to **Settings** → **Billing**
4. Add payment method
5. Set spending limit (recommend $50-100/month to start)
6. Go to **Settings** → **API Keys**
7. Click **Create Key**
8. Name it "OpenClaw-MacMini"
9. **COPY THE KEY IMMEDIATELY** - save it in Notes app or password manager

### Step 4.2: Get eSIM Phone Number

1. Choose a provider:
   - **Tello:** https://tello.com (~$5/month)
   - **Mint Mobile:** https://mintmobile.com (~$15/month)
   - **US Mobile:** https://usmobile.com (~$10/month)

2. Purchase a plan with **SMS capability**
3. You'll receive a **QR code** via email
4. On your personal iPhone:
   - Go to **Settings** → **Cellular** → **Add eSIM**
   - Scan the QR code
   - Wait for activation (5-10 minutes)

5. Your personal phone now has TWO numbers!

### Step 4.3: Set Up WhatsApp Business

On your personal phone:

1. Download **WhatsApp Business** from App Store
2. Open it and register with your **NEW eSIM number**
3. When asked for business name, enter "My AI" or similar
4. Complete setup

Now you have:
- **WhatsApp** (green) = Personal number (unchanged)
- **WhatsApp Business** (green with B) = eSIM number (for OpenClaw)

---

## Part 5: Configure OpenClaw

### Step 5.1: Run the Onboarding Wizard

In Terminal on your Mac Mini:

```bash
openclaw onboard --install-daemon
```

### Step 5.2: Follow the Wizard

**1. Gateway Type:**
```
? Gateway type:
❯ Local
  Remote
```
Choose: **Local**

---

**2. Model Provider:**
```
? Select auth method:
❯ Anthropic API Key
  OpenAI Codex OAuth
  Google Antigravity OAuth
```
Choose: **Anthropic API Key**

---

**3. Enter API Key:**
```
? Enter your Anthropic API key:
```
Paste your Anthropic API key and press Enter

---

**4. Channels:**
```
? Which channels do you want to configure?
 ◉ WhatsApp
 ◯ Telegram
 ◯ Discord
```
Select **WhatsApp** (use Space to toggle, Enter to confirm)

---

**5. Skills:**
```
? Do you want to install skills?
❯ Yes
```
Choose **Yes**, then **npm**, then **Skip for now**

---

**6. API Keys (Optional):**
```
? Do you have a Brave Search API key?
❯ No
```
Choose **No** for all (can add later)

---

**7. Daemon:**
```
? Install background service?
❯ Yes
```
Choose **Yes** - this is crucial for 24/7 operation!

---

**8. Complete!**

The wizard will:
- Save your configuration
- Install the background service (launchd)
- Start the gateway

---

## Part 6: Connect WhatsApp

### Step 6.1: Start WhatsApp Login

```bash
openclaw channels login --channel whatsapp
```

A QR code appears in Terminal.

### Step 6.2: Scan from Your Phone

1. Open **WhatsApp Business** on your personal phone
2. Go to **Settings** → **Linked Devices** → **Link a Device**
3. Point camera at the QR code in Terminal
4. Wait for "Linked!" message

### Step 6.3: Verify Connection

```bash
openclaw channels status
```

Should show: `whatsapp: connected`

---

## Part 7: Verify Everything

### Run Health Checks

```bash
# Check overall status
openclaw status

# Detailed health check
openclaw health

# Security audit
openclaw security audit --deep

# View dashboard in browser
openclaw dashboard
```

### Send Test Message

Open **WhatsApp Business** on your phone and send:
```
Hello! Are you working?
```

You should get a response within 30 seconds.

---

## Part 8: Set Up Remote Access

Now let's configure access from your laptop/phone.

### Step 8.1: Install Tailscale on Other Devices

On your **laptop/phone**:
1. Download Tailscale from https://tailscale.com
2. Install and sign in with SAME account as Mac Mini
3. Your devices are now on the same private network!

### Step 8.2: Find Your Mac Mini's Tailscale IP

On Mac Mini:
```bash
tailscale ip -4
```

Note this IP (like `100.x.x.x`)

### Step 8.3: Access Dashboard Remotely

From your laptop browser:
```
http://100.x.x.x:18789
```

(Replace with your actual Tailscale IP)

### Step 8.4: Screen Share Remotely

From your Mac laptop:
1. Open **Finder**
2. Press `Cmd + K`
3. Enter: `vnc://100.x.x.x` (your Tailscale IP)
4. Click **Connect**
5. Enter Mac Mini username/password
6. You now see the Mac Mini desktop!

---

## Part 9: Final Checklist

### Disconnect Physical Connections

Now that remote access works, you can:
1. Disconnect the monitor
2. Disconnect keyboard
3. Disconnect mouse
4. Keep only: Power + Ethernet

Your Mac Mini now runs "headless" - controlled entirely remotely!

### Test 24/7 Operation

1. Wait 24 hours
2. Send a WhatsApp message to your AI
3. If it responds, congratulations! 🎉

### Things to Monitor

```bash
# Check if gateway is running
openclaw gateway status

# View recent logs
openclaw logs --tail 50

# Check channel status
openclaw channels status
```

---

## 🎉 Congratulations!

You now have a dedicated AI assistant running 24/7!

### What You Can Do Now

**Via WhatsApp:**
- "What's on my calendar today?"
- "Draft an email to John about the project"
- "Search the web for..."
- "Create a file called notes.txt with..."
- "What's the weather like?"

**Expand Capabilities:**
- Add Telegram, Discord, Slack channels
- Install Google Calendar/Gmail skills
- Connect to your smart home
- Build custom automations

---

## 📅 Maintenance Schedule

### Weekly
- Check `openclaw status`
- Review any error logs
- Check API costs in Anthropic console

### Monthly
- Check for OpenClaw updates: `openclaw update`
- Check for macOS security updates
- Review security audit: `openclaw security audit --deep`

### When WhatsApp Disconnects
```bash
openclaw channels login --channel whatsapp
```
Then scan QR code again. This happens every few weeks.

---

## 🔧 Troubleshooting

### "Can't connect remotely via Tailscale"

1. Check Tailscale is running on both devices
2. Check both devices show "Connected" in Tailscale app
3. Try: `tailscale ping <mac-mini-ip>`

### "Gateway not running after restart"

```bash
# Check daemon status
launchctl list | grep openclaw

# If not running, start it
openclaw gateway start

# Or reinstall daemon
openclaw onboard --install-daemon
```

### "WhatsApp stopped working"

```bash
# Check status
openclaw channels status

# If disconnected, re-login
openclaw channels login --channel whatsapp
```

### "AI not responding"

```bash
# Check health
openclaw health

# Check logs for errors
openclaw logs --tail 100

# Restart gateway
openclaw gateway restart
```

### "High API costs"

1. Check usage in Anthropic console
2. Review chat history for runaway conversations
3. Set spending limits in Anthropic dashboard
4. Consider using smaller models for simple tasks

---

## 🔐 Security Reminders

1. **Never share** your API key
2. **Keep backups** of your config: `~/.openclaw/`
3. **Update regularly** but review changelogs first
4. **Monitor access** - you should know who's chatting with your AI
5. **Use strong passwords** for Mac Mini account
6. **Enable FileVault** (disk encryption) in System Settings

---

*Guide Version: 1.0*
*Last Updated: February 2026*
