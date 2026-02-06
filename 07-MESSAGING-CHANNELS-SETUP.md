# OpenClaw Messaging Channels Setup Guide

## 📋 Overview

This guide covers setting up ALL messaging channels supported by OpenClaw:

1. [WhatsApp](#1-whatsapp-setup) (Most Popular)
2. [Telegram](#2-telegram-setup)
3. [Discord](#3-discord-setup)
4. [Slack](#4-slack-setup)
5. [iMessage](#5-imessage-setup) (macOS Only)
6. [Signal](#6-signal-setup)
7. [Google Chat](#7-google-chat-setup)
8. [Mattermost](#8-mattermost-setup)
9. [Matrix](#9-matrix-setup)

---

## 🔑 Quick Reference

| Channel | Difficulty | Requirements | Best For |
|---------|------------|--------------|----------|
| WhatsApp | Easy | Separate phone number | Personal use, mobile |
| Telegram | Easy | Telegram account | Tech users, groups |
| Discord | Medium | Discord server ownership | Communities, teams |
| Slack | Medium | Slack workspace admin | Business teams |
| iMessage | Hard | macOS + Apple ID | Apple ecosystem |
| Signal | Medium | Separate phone number | Privacy-focused |

---

## 1. WhatsApp Setup

### Prerequisites
- Separate phone number (eSIM recommended)
- WhatsApp Business app installed on your phone
- Phone number registered with WhatsApp Business

### Step 1.1: Get a Separate Phone Number

**Why separate?** OpenClaw takes over the WhatsApp session. Using your personal number means ALL your messages go through OpenClaw.

**Option A: eSIM (Recommended)**
1. Purchase eSIM from:
   - Tello (https://tello.com) - ~$5/month
   - Mint Mobile (https://mintmobile.com) - ~$15/month
   - US Mobile (https://usmobile.com) - ~$10/month
2. Receive QR code via email
3. On iPhone: Settings → Cellular → Add eSIM → Scan QR
4. On Android: Settings → Network → SIM → Add eSIM

**Option B: Prepaid SIM**
- Buy a cheap prepaid SIM card
- Activate with minimal plan
- Use in a spare phone for initial setup

### Step 1.2: Install WhatsApp Business

1. Download **WhatsApp Business** (NOT regular WhatsApp)
   - Green icon with "B"
   - Separate app from regular WhatsApp
2. Open and register with your NEW number
3. Complete profile setup (business name can be anything)

### Step 1.3: Connect to OpenClaw

```bash
# Start WhatsApp login
openclaw channels login --channel whatsapp
```

A QR code appears in terminal.

### Step 1.4: Scan QR Code

1. Open WhatsApp Business on your phone
2. Go to **Settings** → **Linked Devices**
3. Tap **Link a Device**
4. Point camera at terminal QR code
5. Wait for "Linked" confirmation

### Step 1.5: Verify Connection

```bash
openclaw channels status
```

Should show: `whatsapp: connected`

### Step 1.6: Approve Pairing

First message from a new contact requires approval:

```bash
# List pending pairings
openclaw pairing list whatsapp

# Approve your number
openclaw pairing approve whatsapp <CODE>
```

### WhatsApp Troubleshooting

**QR Code Won't Scan**
```bash
# Try cleaner QR output
TERM=xterm-256color openclaw channels login --channel whatsapp
```

**Session Expired**
- Sessions expire every 1-4 weeks
- Re-run: `openclaw channels login --channel whatsapp`
- Scan QR again

**Rate Limited**
- WhatsApp has anti-spam measures
- Wait 24-48 hours
- Don't send bulk messages

---

## 2. Telegram Setup

### Prerequisites
- Telegram account
- Ability to create a bot via BotFather

### Step 2.1: Create Telegram Bot

1. Open Telegram app
2. Search for **@BotFather**
3. Start chat and send: `/newbot`
4. Follow prompts:
   - **Name:** `My OpenClaw Assistant` (display name)
   - **Username:** `myopenclaw_bot` (must end in `bot`)
5. BotFather gives you a **token** like:
   ```
   7123456789:AAHxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   ```
6. **SAVE THIS TOKEN** - you'll need it!

### Step 2.2: Configure Bot Settings (Optional but Recommended)

Still in BotFather chat:

```
/setdescription
```
Enter description for your bot.

```
/setuserpic
```
Upload a profile picture.

```
/setcommands
```
Add commands (optional):
```
start - Start conversation
help - Get help
status - Check status
```

### Step 2.3: Add Token to OpenClaw

**Method A: Via Wizard**
```bash
openclaw configure --section channels
```
Select Telegram, paste token.

**Method B: Manual Config**
```bash
# Edit config file
nano ~/.openclaw/config.json
```

Add/edit:
```json
{
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "7123456789:AAHxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
    }
  }
}
```

### Step 2.4: Restart Gateway

```bash
openclaw gateway restart
```

### Step 2.5: Start Chat with Your Bot

1. In Telegram, search for your bot username
2. Click **Start**
3. Send a message: "Hello!"
4. You'll receive a pairing code

### Step 2.6: Approve Pairing

```bash
# List pending
openclaw pairing list telegram

# Approve
openclaw pairing approve telegram <CODE>
```

### Step 2.7: Verify

```bash
openclaw channels status
```

Should show: `telegram: connected`

### Telegram Bot Privacy Settings

By default, bots can only see messages that:
- Are sent directly to them
- Start with `/` commands
- Mention the bot

To allow bot in groups:

1. In BotFather: `/setprivacy`
2. Select your bot
3. Choose **Disable**

Now bot sees all group messages.

### Telegram Troubleshooting

**Bot Not Responding**
- Check token is correct
- Check gateway is running: `openclaw gateway status`
- Check logs: `openclaw logs --tail 50`

**"Unauthorized" Error**
- Token is invalid
- Regenerate token in BotFather: `/token`
- Update in OpenClaw config

---

## 3. Discord Setup

### Prerequisites
- Discord account
- A Discord server you own/admin
- Discord Developer Portal access

### Step 3.1: Create Discord Application

1. Go to: https://discord.com/developers/applications
2. Click **New Application**
3. Name: `OpenClaw Assistant`
4. Click **Create**

### Step 3.2: Create Bot User

1. In your application, go to **Bot** tab (left sidebar)
2. Click **Add Bot**
3. Confirm by clicking **Yes, do it!**

### Step 3.3: Configure Bot Settings

Still in Bot tab:

1. **Username:** Change if desired
2. **Icon:** Upload bot avatar
3. **Public Bot:** Toggle OFF (only you can add it)
4. **Requires OAuth2 Code Grant:** OFF
5. **Privileged Gateway Intents:**
   - ✅ **Presence Intent**
   - ✅ **Server Members Intent**
   - ✅ **Message Content Intent** ← IMPORTANT!

### Step 3.4: Get Bot Token

1. In Bot tab, find **Token** section
2. Click **Reset Token**
3. Click **Copy**
4. **SAVE THIS TOKEN SECURELY**

⚠️ Never share this token! Anyone with it controls your bot.

### Step 3.5: Generate Invite Link

1. Go to **OAuth2** → **URL Generator**
2. **Scopes:** Select `bot`
3. **Bot Permissions:** Select:
   - ✅ Read Messages/View Channels
   - ✅ Send Messages
   - ✅ Send Messages in Threads
   - ✅ Embed Links
   - ✅ Attach Files
   - ✅ Read Message History
   - ✅ Add Reactions
   - ✅ Use Slash Commands
4. Copy the generated URL at bottom

### Step 3.6: Add Bot to Your Server

1. Open the URL in browser
2. Select your server from dropdown
3. Click **Authorize**
4. Complete CAPTCHA

### Step 3.7: Configure OpenClaw

**Method A: Via Wizard**
```bash
openclaw configure --section channels
```
Select Discord, paste token.

**Method B: Manual Config**
```bash
nano ~/.openclaw/config.json
```

Add:
```json
{
  "channels": {
    "discord": {
      "enabled": true,
      "token": "YOUR_BOT_TOKEN_HERE"
    }
  }
}
```

### Step 3.8: Restart Gateway

```bash
openclaw gateway restart
```

### Step 3.9: Test the Bot

1. Go to your Discord server
2. Find a text channel
3. Send: `@YourBotName hello!`
4. Or DM the bot directly

### Step 3.10: Approve Pairing

```bash
openclaw pairing list discord
openclaw pairing approve discord <CODE>
```

### Discord Troubleshooting

**Bot is Offline**
- Check token is correct
- Check gateway running: `openclaw gateway status`
- Check intents are enabled (especially Message Content)

**Bot Doesn't Respond**
- Ensure **Message Content Intent** is enabled
- Check bot has permissions in that channel
- Check pairing is approved

**"Missing Permissions" Error**
- Re-invite bot with correct permissions
- Check channel-specific permission overrides

---

## 4. Slack Setup

### Prerequisites
- Slack workspace where you're an admin
- Ability to install apps to workspace

### Step 4.1: Create Slack App

1. Go to: https://api.slack.com/apps
2. Click **Create New App**
3. Choose **From scratch**
4. **App Name:** `OpenClaw Assistant`
5. **Workspace:** Select your workspace
6. Click **Create App**

### Step 4.2: Configure Bot Token Scopes

1. Go to **OAuth & Permissions** (left sidebar)
2. Scroll to **Scopes** → **Bot Token Scopes**
3. Add these scopes:
   - `app_mentions:read`
   - `channels:history`
   - `channels:read`
   - `chat:write`
   - `groups:history`
   - `groups:read`
   - `im:history`
   - `im:read`
   - `im:write`
   - `mpim:history`
   - `mpim:read`
   - `reactions:read`
   - `reactions:write`
   - `users:read`

### Step 4.3: Enable Socket Mode

1. Go to **Socket Mode** (left sidebar)
2. Toggle **Enable Socket Mode** ON
3. Create an app-level token:
   - Name: `openclaw-socket`
   - Scope: `connections:write`
4. Click **Generate**
5. **SAVE THE APP TOKEN** (starts with `xapp-`)

### Step 4.4: Enable Events

1. Go to **Event Subscriptions** (left sidebar)
2. Toggle **Enable Events** ON
3. Under **Subscribe to bot events**, add:
   - `app_mention`
   - `message.channels`
   - `message.groups`
   - `message.im`
   - `message.mpim`

### Step 4.5: Install App to Workspace

1. Go to **Install App** (left sidebar)
2. Click **Install to Workspace**
3. Review permissions and click **Allow**
4. **SAVE THE BOT TOKEN** (starts with `xoxb-`)

### Step 4.6: Configure OpenClaw

You need TWO tokens:
- **Bot Token:** `xoxb-...` (from Install App page)
- **App Token:** `xapp-...` (from Socket Mode)

```bash
nano ~/.openclaw/config.json
```

Add:
```json
{
  "channels": {
    "slack": {
      "enabled": true,
      "botToken": "xoxb-your-bot-token",
      "appToken": "xapp-your-app-token"
    }
  }
}
```

### Step 4.7: Restart Gateway

```bash
openclaw gateway restart
```

### Step 4.8: Test in Slack

1. Invite bot to a channel: `/invite @OpenClaw Assistant`
2. Mention the bot: `@OpenClaw Assistant hello!`
3. Or DM the bot directly

### Step 4.9: Approve Pairing

```bash
openclaw pairing list slack
openclaw pairing approve slack <CODE>
```

### Slack Troubleshooting

**Bot Shows Offline**
- Check both tokens are correct
- Check Socket Mode is enabled
- Restart gateway

**"not_in_channel" Error**
- Invite bot to channel: `/invite @BotName`

**Messages Not Received**
- Check Event Subscriptions are enabled
- Verify all scopes are added
- Check Socket Mode is ON

---

## 5. iMessage Setup

### Prerequisites
- macOS device (Mac Mini, MacBook, etc.)
- Apple ID signed into Messages
- OpenClaw running on the SAME Mac

⚠️ **iMessage ONLY works on macOS** - not on Linux/Windows/VPS

### Step 5.1: Enable Messages on Mac

1. Open **Messages** app
2. Sign in with Apple ID (use your dedicated OpenClaw Apple ID)
3. Go to **Messages** → **Settings** → **iMessage**
4. Ensure **Enable Messages in iCloud** is checked
5. Verify your phone number/email is listed

### Step 5.2: Grant Terminal Full Disk Access

OpenClaw needs to read Messages database:

1. **System Settings** → **Privacy & Security** → **Full Disk Access**
2. Click **+** button
3. Navigate to `/Applications/Utilities/Terminal.app`
4. Add Terminal
5. If using iTerm2 or another terminal, add that too

### Step 5.3: Grant Automation Permissions

1. **System Settings** → **Privacy & Security** → **Automation**
2. Find Terminal (or your terminal app)
3. Enable access to **Messages**

### Step 5.4: Install BlueBubbles (Recommended Method)

BlueBubbles provides better iMessage integration:

1. Download BlueBubbles Server: https://bluebubbles.app
2. Install the .dmg
3. Open BlueBubbles
4. Complete setup wizard
5. Note the connection details

### Step 5.5: Configure OpenClaw for iMessage

```bash
nano ~/.openclaw/config.json
```

Add:
```json
{
  "channels": {
    "imessage": {
      "enabled": true
    }
  }
}
```

Or with BlueBubbles:
```json
{
  "channels": {
    "bluebubbles": {
      "enabled": true,
      "host": "localhost",
      "port": 1234,
      "password": "your-bluebubbles-password"
    }
  }
}
```

### Step 5.6: Restart Gateway

```bash
openclaw gateway restart
```

### Step 5.7: Test

Send an iMessage to the phone number/email registered with Messages on your Mac.

### iMessage Troubleshooting

**"Permission Denied" Errors**
- Ensure Full Disk Access is granted
- Restart Terminal after granting permissions
- Try: `tccutil reset All com.apple.Terminal`

**Messages Not Received**
- Check Messages app is signed in
- Check iCloud sync is enabled
- Verify phone number is registered

**Delays in Responses**
- iMessage integration can be slower than other channels
- Check Messages.app isn't hanging

---

## 6. Signal Setup

### Prerequisites
- Separate phone number
- Signal app installed

### Step 6.1: Install Signal CLI

```bash
# macOS
brew install signal-cli

# Linux
wget https://github.com/AsamK/signal-cli/releases/latest/download/signal-cli-0.x.x-Linux.tar.gz
tar xf signal-cli-*.tar.gz
sudo mv signal-cli-*/bin/signal-cli /usr/local/bin/
```

### Step 6.2: Register Phone Number

```bash
# Request verification code
signal-cli -u +1YOURNUMBER register

# You'll receive SMS with code, then verify:
signal-cli -u +1YOURNUMBER verify CODE
```

### Step 6.3: Configure OpenClaw

```bash
nano ~/.openclaw/config.json
```

Add:
```json
{
  "channels": {
    "signal": {
      "enabled": true,
      "phoneNumber": "+1YOURNUMBER"
    }
  }
}
```

### Step 6.4: Restart and Test

```bash
openclaw gateway restart
```

Send a Signal message to your registered number.

---

## 7. Google Chat Setup

### Prerequisites
- Google Workspace account (not personal Gmail)
- Admin access to Google Workspace

### Step 7.1: Create Google Cloud Project

1. Go to: https://console.cloud.google.com
2. Create new project: `OpenClaw Bot`
3. Enable **Google Chat API**

### Step 7.2: Configure Chat App

1. Go to: https://console.cloud.google.com/apis/api/chat.googleapis.com
2. Click **Configuration**
3. Fill in:
   - **App name:** OpenClaw
   - **Avatar URL:** Your bot image
   - **Description:** AI Assistant
   - **Functionality:** Check both DMs and Spaces
   - **Connection settings:** HTTP endpoint or Cloud Pub/Sub
4. Add your OpenClaw webhook URL

### Step 7.3: Configure OpenClaw

```bash
nano ~/.openclaw/config.json
```

Add:
```json
{
  "channels": {
    "googlechat": {
      "enabled": true,
      "projectId": "your-project-id",
      "credentials": "/path/to/service-account.json"
    }
  }
}
```

### Step 7.4: Restart Gateway

```bash
openclaw gateway restart
```

---

## 8. Mattermost Setup

### Prerequisites
- Mattermost server (self-hosted or cloud)
- Admin access

### Step 8.1: Create Bot Account

1. Go to **System Console** → **Integrations** → **Bot Accounts**
2. Enable bot accounts
3. Create new bot:
   - Username: `openclaw`
   - Display Name: `OpenClaw Assistant`
4. Save the access token

### Step 8.2: Install OpenClaw Plugin (Alternative)

1. Download Mattermost plugin from OpenClaw releases
2. Go to **System Console** → **Plugin Management**
3. Upload plugin
4. Enable and configure

### Step 8.3: Configure OpenClaw

```bash
nano ~/.openclaw/config.json
```

Add:
```json
{
  "channels": {
    "mattermost": {
      "enabled": true,
      "url": "https://your-mattermost-server.com",
      "token": "your-bot-token"
    }
  }
}
```

### Step 8.4: Restart Gateway

```bash
openclaw gateway restart
```

---

## 9. Matrix Setup

### Prerequisites
- Matrix homeserver access
- Matrix account for bot

### Step 9.1: Create Bot Account

Create a dedicated Matrix account for your bot on your homeserver or matrix.org.

### Step 9.2: Get Access Token

```bash
curl -XPOST \
  -d '{"type":"m.login.password","user":"bot_username","password":"bot_password"}' \
  "https://matrix.org/_matrix/client/r0/login"
```

Save the `access_token` from response.

### Step 9.3: Configure OpenClaw

```bash
nano ~/.openclaw/config.json
```

Add:
```json
{
  "channels": {
    "matrix": {
      "enabled": true,
      "homeserver": "https://matrix.org",
      "userId": "@openclaw:matrix.org",
      "accessToken": "your-access-token"
    }
  }
}
```

### Step 9.4: Restart Gateway

```bash
openclaw gateway restart
```

---

## 🔧 Managing Multiple Channels

### View All Channel Status

```bash
openclaw channels status
```

### Enable/Disable Channels

```bash
# Disable a channel temporarily
openclaw channels disable telegram

# Re-enable
openclaw channels enable telegram
```

### Channel-Specific Logs

```bash
# View logs for specific channel
openclaw logs --channel whatsapp --tail 50
```

### Reconnect a Channel

```bash
# If channel disconnected
openclaw channels reconnect whatsapp
```

---

## 📊 Channel Comparison

| Feature | WhatsApp | Telegram | Discord | Slack | iMessage |
|---------|----------|----------|---------|-------|----------|
| Setup Difficulty | Easy | Easy | Medium | Medium | Hard |
| Requires Phone # | Yes | No | No | No | Yes* |
| Group Support | Yes | Yes | Yes | Yes | Yes |
| File Sharing | Yes | Yes | Yes | Yes | Yes |
| Voice Notes | Yes | Yes | No | No | Yes |
| Session Expiry | Yes | No | No | No | No |
| Mobile Friendly | Best | Great | Good | Good | Best |
| Works on Linux | Yes | Yes | Yes | Yes | No |

*iMessage can use email instead

---

## 🔐 Security Considerations

1. **Use Separate Accounts**
   - Don't use personal WhatsApp/Telegram
   - Create dedicated bot accounts

2. **Approve Pairings Carefully**
   - Review who can message your AI
   - Don't approve unknown contacts

3. **Monitor Conversations**
   - Review logs periodically
   - Watch for suspicious activity

4. **Token Security**
   - Never share bot tokens
   - Rotate tokens periodically
   - Store tokens securely

---

*Guide Version: 1.0*
*Last Updated: February 2026*
