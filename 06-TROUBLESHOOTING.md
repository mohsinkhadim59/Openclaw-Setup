# OpenClaw Troubleshooting Guide

## 🔍 Quick Diagnostics

Before diving into specific issues, run these commands:

```bash
# Check overall status
openclaw status

# Detailed health check
openclaw health

# Security audit
openclaw security audit

# View recent logs
openclaw logs --tail 50

# Full debug report (great for sharing when asking for help)
openclaw status --all
```

---

## 📋 Common Issues Index

1. [Installation Problems](#1-installation-problems)
2. [Gateway Won't Start](#2-gateway-wont-start)
3. [WhatsApp Issues](#3-whatsapp-issues)
4. [AI Not Responding](#4-ai-not-responding)
5. [API Key Errors](#5-api-key-errors)
6. [High Resource Usage](#6-high-resource-usage)
7. [Network/Connection Issues](#7-networkconnection-issues)
8. [Remote Access Problems](#8-remote-access-problems)
9. [Permission Errors](#9-permission-errors)
10. [Update Issues](#10-update-issues)

---

## 1. Installation Problems

### "npm: command not found"

**Cause:** Node.js not installed or not in PATH.

**Fix (macOS):**
```bash
# Install via Homebrew
brew install node@22
brew link node@22

# For Apple Silicon, add to PATH
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
source ~/.zprofile
```

**Fix (Linux):**
```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
```

---

### "EACCES permission denied" during npm install

**Cause:** npm trying to write to system directory without permissions.

**Fix:**
```bash
# Option 1: Fix npm permissions
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

# Option 2: Use sudo (less ideal)
sudo npm install -g openclaw@latest
```

---

### "node: No such file or directory"

**Cause:** Node.js not linked properly.

**Fix:**
```bash
# macOS
brew link --overwrite node@22

# Verify
which node
node --version
```

---

### "openclaw: command not found" after install

**Cause:** OpenClaw not in PATH.

**Fix:**
```bash
# Find where it's installed
npm list -g openclaw

# Add npm global bin to PATH
export PATH="$(npm bin -g):$PATH"
echo 'export PATH="$(npm bin -g):$PATH"' >> ~/.bashrc
source ~/.bashrc
```

---

## 2. Gateway Won't Start

### "Port 18789 already in use"

**Cause:** Another process using the port, or orphaned OpenClaw process.

**Fix:**
```bash
# Find what's using the port
lsof -i :18789

# Kill the process (replace PID with actual number)
kill -9 <PID>

# Or kill all openclaw processes
pkill -f openclaw

# Start gateway
openclaw gateway start
```

---

### "Gateway not running" even after start

**Cause:** Gateway crashing on startup.

**Fix:**
```bash
# Run in foreground to see errors
openclaw gateway --verbose

# Check for config issues
openclaw doctor

# Reset and reconfigure if needed
openclaw reset --config
openclaw onboard
```

---

### "ENOENT: no such file or directory" for config

**Cause:** Missing configuration files.

**Fix:**
```bash
# Check config directory
ls -la ~/.openclaw/

# Re-run onboarding
openclaw onboard --install-daemon
```

---

### Gateway starts but immediately stops

**Cause:** Usually authentication issue.

**Fix:**
```bash
# Check health
openclaw health

# If auth issue:
openclaw configure --section auth
# Re-enter API key

# Restart
openclaw gateway restart
```

---

## 3. WhatsApp Issues

### "WhatsApp not connected" / "Session expired"

**Cause:** WhatsApp sessions expire periodically (every 1-4 weeks).

**Fix:**
```bash
# Re-login
openclaw channels login --channel whatsapp

# Scan QR code with WhatsApp Business app
# Go to: Settings → Linked Devices → Link a Device
```

---

### QR code doesn't scan

**Cause:** Terminal rendering issues.

**Fix:**
```bash
# Try different terminal rendering
openclaw channels login --channel whatsapp --qr-terminal

# Or try in different terminal app (iTerm2, Alacritty, etc.)

# If over SSH, try:
TERM=xterm-256color openclaw channels login --channel whatsapp
```

---

### "WhatsApp: authentication failed"

**Cause:** Session was revoked from phone.

**Fix:**
1. On your phone, go to WhatsApp Business
2. Settings → Linked Devices
3. Remove old linked device if present
4. Re-link: `openclaw channels login --channel whatsapp`

---

### Messages not being received

**Cause:** Pairing not approved.

**Fix:**
```bash
# Check for pending pairings
openclaw pairing list whatsapp

# Approve your number
openclaw pairing approve whatsapp <CODE>
```

---

### "rate limit exceeded" on WhatsApp

**Cause:** Too many messages too fast.

**Fix:**
- Wait 24-48 hours
- WhatsApp has anti-spam measures
- Don't send bulk messages through OpenClaw

---

## 4. AI Not Responding

### No response at all

**Check in order:**

```bash
# 1. Is gateway running?
openclaw gateway status

# 2. Is channel connected?
openclaw channels status

# 3. Is auth configured?
openclaw health

# 4. Any errors in logs?
openclaw logs --tail 100
```

---

### Response takes forever (>60 seconds)

**Cause:** Usually network or API issues.

**Fix:**
```bash
# Check API connectivity
curl -I https://api.anthropic.com

# Check your internet
ping google.com

# Check Anthropic status
# Visit: https://status.anthropic.com
```

---

### AI responds with errors

**Example:** "Error: API request failed"

**Fix:**
```bash
# Check API key is valid
openclaw configure --section auth
# Re-enter key

# Check API key has credits
# Visit: https://console.anthropic.com → Usage
```

---

### AI gives strange/wrong responses

**Cause:** Possibly prompt injection, context issues, or model confusion.

**Fix:**
```bash
# Clear session context
openclaw sessions clear

# Restart gateway
openclaw gateway restart
```

---

## 5. API Key Errors

### "Invalid API key"

**Cause:** Key is wrong, revoked, or expired.

**Fix:**
1. Go to https://console.anthropic.com
2. Check if key exists and is active
3. Generate new key if needed
4. Update in OpenClaw:
```bash
openclaw configure --section auth
# Paste new key
```

---

### "Insufficient credits" / "Payment required"

**Cause:** Account has no payment method or hit spending limit.

**Fix:**
1. Go to Anthropic Console → Billing
2. Add/update payment method
3. Increase spending limit if needed

---

### "Rate limited"

**Cause:** Too many API requests.

**Fix:**
- Wait a few minutes
- Check for runaway loops in conversations
- Consider upgrading API tier

---

## 6. High Resource Usage

### High CPU usage

**Diagnose:**
```bash
# Check what's using CPU
top -l 1 | head -20  # macOS
top -bn1 | head -20  # Linux

# Check OpenClaw specifically
ps aux | grep openclaw
```

**Fix:**
- Restart gateway: `openclaw gateway restart`
- Check for infinite loops in skills
- Check logs for repeated errors

---

### High memory usage

**Diagnose:**
```bash
# Check memory
free -h  # Linux
vm_stat  # macOS
```

**Fix:**
```bash
# Restart gateway
openclaw gateway restart

# Clear old sessions
openclaw sessions prune

# If VPS, add swap:
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

---

### Disk filling up

**Diagnose:**
```bash
df -h
du -sh ~/.openclaw/*
```

**Fix:**
```bash
# Clear old logs
openclaw logs clear --older-than 7d

# Clear old sessions
openclaw sessions prune --older-than 30d

# Clear npm cache
npm cache clean --force
```

---

## 7. Network/Connection Issues

### "ECONNREFUSED" errors

**Cause:** Can't connect to localhost service.

**Fix:**
```bash
# Check gateway is running
openclaw gateway status

# Check what's listening
netstat -tlnp | grep 18789  # Linux
lsof -i :18789              # macOS
```

---

### "ETIMEDOUT" to Anthropic API

**Cause:** Network issues, firewall, or DNS problems.

**Fix:**
```bash
# Test connectivity
curl -v https://api.anthropic.com/v1/messages

# Check DNS
nslookup api.anthropic.com

# Try different DNS
# Edit /etc/resolv.conf or network settings
# Use 8.8.8.8 or 1.1.1.1
```

---

### Firewall blocking connections

**Fix (macOS):**
```bash
# Check firewall status
/usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate

# Add exception for node
/usr/libexec/ApplicationFirewall/socketfilterfw --add /usr/local/bin/node
```

**Fix (Linux):**
```bash
# Check UFW
sudo ufw status

# Allow needed ports
sudo ufw allow out to any port 443
sudo ufw allow out to any port 80
```

---

## 8. Remote Access Problems

### Can't SSH into VPS

**Diagnose:**
```bash
# Verbose SSH
ssh -v user@server

# Check if server responds
ping server-ip
```

**Fix:**
- Check security group/firewall allows port 22
- Check correct IP address
- Check SSH key permissions: `chmod 400 ~/.ssh/key.pem`
- Check correct username (ubuntu, root, openclaw)

---

### Tailscale not connecting

**Fix:**
```bash
# Check status
tailscale status

# Reconnect
tailscale down
tailscale up

# Check for key expiration
# Reauthenticate if needed
```

---

### Screen sharing not working (Mac Mini)

**Fix:**
1. System Settings → Sharing → Screen Sharing: ON
2. Check "Allow access for" includes your user
3. Make sure both machines on same Tailscale network
4. Try connecting via Tailscale IP: `vnc://100.x.x.x`

---

### Dashboard not accessible remotely

**Remember:** Dashboard is localhost only by default.

**Access via SSH tunnel:**
```bash
ssh -L 18789:localhost:18789 user@server
# Then open: http://localhost:18789
```

**Access via Tailscale:**
```bash
# On server, check Tailscale IP
tailscale ip -4
# Open: http://100.x.x.x:18789
```

---

## 9. Permission Errors

### "Permission denied" on files

**Fix:**
```bash
# Fix ownership
sudo chown -R $(whoami) ~/.openclaw

# Fix permissions
chmod -R 755 ~/.openclaw
chmod 600 ~/.openclaw/credentials/*  # Keep secrets secure
```

---

### "EACCES" when writing files

**Cause:** OpenClaw trying to write outside allowed directories.

**Fix:**
- Check workspace permissions
- Don't run as wrong user
- Check disk isn't full

---

### Exec permission denied for commands

**Cause:** OpenClaw security policy blocking command.

**Fix:**
```bash
# Check exec policy
openclaw approvals list

# Approve specific command if safe
openclaw approvals add "safe-command"

# Or adjust security settings
openclaw configure --section security
```

---

## 10. Update Issues

### "Cannot update, process is running"

**Fix:**
```bash
# Stop gateway first
openclaw gateway stop

# Update
npm update -g openclaw

# Start again
openclaw gateway start
```

---

### Update broke something

**Fix:**
```bash
# Check version
openclaw --version

# Roll back to specific version
npm install -g openclaw@2026.1.28  # Replace with last working version

# Restart
openclaw gateway restart
```

---

### Config incompatible after update

**Fix:**
```bash
# Run doctor to check
openclaw doctor

# If needed, reset config
openclaw reset --config

# Re-configure
openclaw onboard
```

---

## 🆘 Getting More Help

### Gather Debug Info

Before asking for help, collect:

```bash
# Full status report
openclaw status --all > status.txt

# Recent logs
openclaw logs --tail 500 > logs.txt

# System info
uname -a > system.txt
node --version >> system.txt
npm --version >> system.txt
openclaw --version >> system.txt
```

### Where to Ask

1. **GitHub Issues:** https://github.com/openclaw/openclaw/issues
   - Search existing issues first
   - Include debug info

2. **Discord Community:** (link on openclaw.ai)
   - Good for quick questions
   - Real-time help

3. **Stack Overflow:** Tag with `openclaw`

### What to Include in Help Request

1. What you're trying to do
2. What error you're seeing (exact text)
3. Steps you already tried
4. Output of `openclaw status --all`
5. Relevant logs
6. Your setup (macOS/Linux, local/VPS/AWS)

---

## 🔄 Factory Reset (Last Resort)

If nothing works, start fresh:

```bash
# Stop everything
openclaw gateway stop

# Backup config (just in case)
cp -r ~/.openclaw ~/.openclaw-backup

# Uninstall
openclaw uninstall
npm uninstall -g openclaw

# Remove all data
rm -rf ~/.openclaw

# Fresh install
npm install -g openclaw@latest

# Reconfigure from scratch
openclaw onboard --install-daemon
```

**Note:** You'll need to:
- Re-enter API keys
- Re-link WhatsApp
- Re-approve pairings
- Reconfigure any skills

---

*Guide Version: 1.0*
*Last Updated: February 2026*
