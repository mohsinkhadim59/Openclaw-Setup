# OpenClaw (MoltBot/ClawdBot) - Complete Setup Guide Collection

## 🦞 What is OpenClaw?

OpenClaw (formerly known as MoltBot and ClawdBot) is an open-source AI agent that runs on your own hardware and connects to messaging apps like WhatsApp, Telegram, Discord, Slack, and iMessage. It acts as a 24/7 personal AI assistant that can:

- Manage your emails and calendar
- Run commands on your computer
- Do research and summarize information
- Take autonomous actions on your behalf
- Remember context across conversations (persistent memory)
- Work while you sleep

**Key difference from ChatGPT/Claude.ai:** OpenClaw runs locally on YOUR machine with full system access, not in the cloud. This means it can actually DO things, not just tell you how to do them.

---

## 📁 Guide Files Included

| File | Description | Best For |
|------|-------------|----------|
| `01-LOCAL-MAC-SETUP.md` | Setup on your existing Mac laptop/desktop | Testing, light use, developers |
| `02-MAC-MINI-DEDICATED-SETUP.md` | Dedicated Mac Mini as AI server | Best home setup, 24/7 operation |
| `03-VPS-LINUX-SETUP.md` | Virtual Private Server (Hetzner, DigitalOcean, etc.) | Remote access, no local hardware |
| `04-AWS-CLOUD-SETUP.md` | Amazon Web Services EC2 deployment | Enterprise, scalable, professional |
| `05-SECURITY-HARDENING.md` | Security best practices for all setups | Everyone (READ THIS!) |
| `06-TROUBLESHOOTING.md` | Common issues and solutions | When things go wrong |
| `07-MESSAGING-CHANNELS-SETUP.md` | WhatsApp, Telegram, Discord, Slack, iMessage, Signal, etc. | Connecting messaging apps |
| `08-GOOGLE-INTEGRATION-SETUP.md` | Gmail, Calendar, Drive, Sheets, Contacts | Google services integration |
| `09-SKILLS-INSTALLATION-GUIDE.md` | Plugin system for extending capabilities | Adding 1Password, Notion, GitHub, etc. |
| `10-MONITORING-AND-BACKUPS.md` | Uptime monitoring, backups, alerting | Keeping your setup healthy |

---

## 🎯 Which Setup Should You Choose?

### 🖥️ Local Mac (Your Existing Computer)
**Pros:**
- Free (no additional hardware)
- Quick to test and experiment
- Full GUI access

**Cons:**
- Computer must stay on and awake
- Takes up resources on your daily machine
- Can't access when laptop is closed/traveling

**Best for:** Developers testing OpenClaw, weekend experiments, learning

---

### 🍎 Dedicated Mac Mini
**Pros:**
- Always on, always available
- iMessage support (macOS only)
- Low power consumption (~10W)
- One-time cost, no monthly fees
- Full local control

**Cons:**
- Upfront cost (~$500-600)
- Physical device to maintain
- Need reliable home internet

**Best for:** Personal use, home office, families, small businesses

---

### 🌐 VPS (Hetzner, DigitalOcean, Linode)
**Pros:**
- Access from anywhere
- No local hardware needed
- Professional infrastructure
- Easy to scale
- ~$5-20/month

**Cons:**
- Monthly recurring cost
- No iMessage support (Linux)
- Slightly more technical setup
- Data lives on third-party servers

**Best for:** Remote workers, travelers, those without reliable home internet

---

### ☁️ AWS (Amazon Web Services)
**Pros:**
- Enterprise-grade infrastructure
- Massive scalability
- Global availability
- Professional support options

**Cons:**
- Most expensive option
- Complex pricing model
- Steeper learning curve
- Overkill for personal use

**Best for:** Businesses, teams, enterprise deployments, production workloads

---

## ⚡ Quick Start (For the Impatient)

If you just want to try OpenClaw quickly on your Mac:

```bash
# 1. Install OpenClaw
curl -fsSL https://openclaw.ai/install.sh | bash

# 2. Run the setup wizard
openclaw onboard --install-daemon

# 3. Open the dashboard
openclaw dashboard
```

Then follow the wizard prompts. For the full detailed guide, read the appropriate file for your setup type.

---

## 💰 Cost Summary

| Setup Type | Hardware Cost | Monthly Cost | API Cost |
|------------|---------------|--------------|----------|
| Local Mac | $0 (use existing) | $0 | $2-10/day |
| Mac Mini | $500-600 | $0 | $2-10/day |
| VPS | $0 | $5-20/month | $2-10/day |
| AWS | $0 | $20-100/month | $2-10/day |

**API costs** depend on usage. Light use = $2-5/day, heavy use = $10-20/day.

---

## ⚠️ IMPORTANT SECURITY WARNING

**READ THIS BEFORE PROCEEDING:**

OpenClaw has FULL ACCESS to whatever machine it runs on. It can:
- Read and write any file
- Execute any command
- Access your network
- Send messages as you

**NEVER run OpenClaw on:**
- A machine with sensitive work/financial data
- Your primary personal computer (use dedicated hardware)
- Machines connected to corporate networks

**ALWAYS:**
- Use a separate Apple ID / Google account
- Use a separate phone number for WhatsApp
- Review the Security Hardening guide
- Keep the system updated

---

## 📚 Prerequisites (All Setups)

Before starting ANY guide, you need:

1. **Anthropic API Key** (or Claude Code subscription)
   - Sign up at: https://console.anthropic.com
   - Add payment method
   - Generate API key

2. **Separate Phone Number** (for WhatsApp)
   - Get an eSIM from Tello, Mint Mobile, or similar (~$5-10/month)
   - Or use a separate physical SIM

3. **Basic Terminal Knowledge**
   - Know how to open Terminal
   - Know how to copy/paste commands
   - Know how to navigate folders with `cd`

4. **Stable Internet Connection**
   - For the machine running OpenClaw
   - Preferably wired (Ethernet) for reliability

---

## 🚀 Let's Begin!

Choose your setup guide and follow it step by step:

1. **[Local Mac Setup](01-LOCAL-MAC-SETUP.md)** - Start here if testing
2. **[Mac Mini Dedicated Setup](02-MAC-MINI-DEDICATED-SETUP.md)** - Recommended for personal use
3. **[VPS Linux Setup](03-VPS-LINUX-SETUP.md)** - For cloud deployment
4. **[AWS Cloud Setup](04-AWS-CLOUD-SETUP.md)** - For enterprise/professional use

After completing your setup, read:
- **[Security Hardening](05-SECURITY-HARDENING.md)** - Protect your setup
- **[Troubleshooting](06-TROUBLESHOOTING.md)** - Fix common issues
- **[Messaging Channels](07-MESSAGING-CHANNELS-SETUP.md)** - Connect WhatsApp, Telegram, Discord, etc.
- **[Google Integration](08-GOOGLE-INTEGRATION-SETUP.md)** - Gmail, Calendar, Drive, Sheets
- **[Skills Installation](09-SKILLS-INSTALLATION-GUIDE.md)** - Extend with plugins
- **[Monitoring & Backups](10-MONITORING-AND-BACKUPS.md)** - Keep it running smoothly

---

## 📞 Getting Help

- **Official Docs:** https://docs.openclaw.ai
- **GitHub Issues:** https://github.com/openclaw/openclaw/issues
- **Discord Community:** Check openclaw.ai for invite link

---

*Last Updated: February 2026*
*OpenClaw Version: 2026.1.x*
