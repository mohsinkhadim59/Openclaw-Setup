# OpenClaw Setup Guide: VPS (Virtual Private Server)

## 📋 Overview

This guide walks you through setting up OpenClaw on a **Virtual Private Server (VPS)** - a cloud-based Linux server you rent monthly. This is ideal for people who don't want to maintain local hardware.

**Time Required:** 1-2 hours
**Difficulty:** Intermediate
**Cost:** $5-20/month for VPS + API costs

---

## 🌐 What is a VPS?

A VPS is a virtual computer running in a data center that you rent. It's:
- Always on, always connected
- Accessible from anywhere
- No hardware to maintain
- Pay monthly, cancel anytime

**Trade-offs vs Mac Mini:**
- ❌ No iMessage support (Linux can't run iMessage)
- ❌ Monthly recurring cost
- ❌ Data lives on third-party servers
- ✅ No hardware to buy
- ✅ Access from anywhere without Tailscale
- ✅ Easy to upgrade/downgrade
- ✅ Professional infrastructure

---

## 🏢 Choosing a VPS Provider

### Recommended Providers (Cheapest to Most Featured)

| Provider | Starting Price | Best For | Signup |
|----------|---------------|----------|--------|
| **Hetzner** | €4.51/mo (~$5) | Best value, EU data centers | https://hetzner.cloud |
| **DigitalOcean** | $6/mo | Easy interface, US data centers | https://digitalocean.com |
| **Linode** | $5/mo | Good support | https://linode.com |
| **Vultr** | $5/mo | Many locations | https://vultr.com |

### Minimum Specs Required

- **RAM:** 2GB (4GB recommended)
- **CPU:** 1 vCPU (2 recommended)
- **Storage:** 20GB SSD
- **OS:** Ubuntu 22.04 or 24.04 LTS
- **Network:** Unlimited bandwidth (or at least 1TB/month)

---

## Part 1: Create Your VPS

I'll use **Hetzner** for this guide (cheapest + great quality), but steps are similar for all providers.

### Step 1.1: Create Hetzner Account

1. Go to: https://www.hetzner.com/cloud
2. Click **Get Started** or **Sign Up**
3. Enter email, create password
4. Verify email
5. Add payment method (credit card or PayPal)

### Step 1.2: Create a New Server

1. Click **+ Create Server** (or Add Server)
2. **Location:** Choose closest to you
   - US East: Ashburn
   - US West: Hillsboro
   - EU: Falkenstein, Nuremberg, or Helsinki
3. **Image:** Ubuntu 24.04
4. **Type:** Shared vCPU
5. **Size:** CX22 (2 vCPU, 4GB RAM) - €4.85/month
   - Or CX11 (1 vCPU, 2GB RAM) - €3.79/month for minimal use
6. **Networking:** 
   - Public IPv4: ✅ Enable
   - Public IPv6: ✅ Enable (free)
7. **SSH Keys:** We'll add this next
8. **Name:** `openclaw-server` or whatever you like

**DON'T CREATE YET** - we need to add SSH key first.

### Step 1.3: Create SSH Key (For Secure Access)

SSH keys are like super-secure passwords. You need to create one on your LOCAL computer (laptop/desktop), not the server.

**On macOS/Linux:**

Open Terminal on your LOCAL computer:

```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "openclaw-server"
```

When prompted:
- **File location:** Press Enter for default (`~/.ssh/id_ed25519`)
- **Passphrase:** Enter a password (recommended) or press Enter for none

```bash
# View your public key
cat ~/.ssh/id_ed25519.pub
```

Copy the output (starts with `ssh-ed25519 ...`)

**On Windows:**

1. Open PowerShell
2. Run: `ssh-keygen -t ed25519 -C "openclaw-server"`
3. Press Enter for defaults
4. Run: `type $env:USERPROFILE\.ssh\id_ed25519.pub`
5. Copy the output

### Step 1.4: Add SSH Key to Hetzner

1. Back in Hetzner console, click **SSH Keys** → **Add SSH Key**
2. Paste your public key
3. Name it: `my-laptop` or similar
4. Click **Add SSH Key**

### Step 1.5: Create the Server

1. Under **SSH Keys**, select the key you just added
2. Click **Create & Buy now**
3. Wait 30-60 seconds for server to boot
4. Note the **IP address** shown (like `167.235.xxx.xxx`)

---

## Part 2: Connect to Your VPS

### Step 2.1: SSH into Server

On your LOCAL computer:

```bash
ssh root@YOUR_SERVER_IP
```

Replace `YOUR_SERVER_IP` with the IP from Hetzner.

First time you'll see:
```
The authenticity of host '167.235.xxx.xxx' can't be established.
ED25519 key fingerprint is SHA256:...
Are you sure you want to continue connecting (yes/no)?
```

Type `yes` and press Enter.

If using passphrase, enter it when prompted.

**You're now connected to your VPS!**

### Step 2.2: Update System

First things first - update everything:

```bash
apt update && apt upgrade -y
```

Wait for completion (2-5 minutes).

---

## Part 3: Create Non-Root User (Security)

Running as root is dangerous. Create a regular user:

### Step 3.1: Create User

```bash
adduser openclaw
```

When prompted:
- **Password:** Enter a strong password (remember it!)
- **Full Name:** OpenClaw (or whatever)
- Other fields: Press Enter to skip

### Step 3.2: Give Sudo Access

```bash
usermod -aG sudo openclaw
```

### Step 3.3: Copy SSH Key to New User

```bash
# Create .ssh directory
mkdir -p /home/openclaw/.ssh

# Copy authorized keys
cp /root/.ssh/authorized_keys /home/openclaw/.ssh/

# Set permissions
chown -R openclaw:openclaw /home/openclaw/.ssh
chmod 700 /home/openclaw/.ssh
chmod 600 /home/openclaw/.ssh/authorized_keys
```

### Step 3.4: Test New User

Open a NEW terminal window (keep the root one open just in case):

```bash
ssh openclaw@YOUR_SERVER_IP
```

You should connect without password (using SSH key).

### Step 3.5: Disable Root Login (Security)

Back in the root terminal:

```bash
nano /etc/ssh/sshd_config
```

Find this line (use Ctrl+W to search):
```
PermitRootLogin yes
```

Change to:
```
PermitRootLogin no
```

Save and exit: `Ctrl+X`, then `Y`, then `Enter`

Restart SSH:
```bash
systemctl restart sshd
```

**From now on, use the `openclaw` user to connect.**

---

## Part 4: Install Required Software

### Step 4.1: Switch to OpenClaw User

If you're still root, exit and reconnect:

```bash
exit
ssh openclaw@YOUR_SERVER_IP
```

### Step 4.2: Install Dependencies

```bash
# Install essential packages
sudo apt install -y curl wget git build-essential

# Install Node.js 22
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs

# Verify
node --version    # Should show v22.x.x
npm --version     # Should show 10.x.x
```

### Step 4.3: Install OpenClaw

```bash
# Install globally
sudo npm install -g openclaw@latest

# Verify
openclaw --version
```

---

## Part 5: Set Up Anthropic API Key

### Step 5.1: Get API Key

1. Go to: https://console.anthropic.com
2. Sign up or log in
3. Go to **Settings** → **Billing** → Add payment method
4. Go to **Settings** → **API Keys** → **Create Key**
5. Copy the key (starts with `sk-ant-...`)

### Step 5.2: Save API Key Securely

On your VPS:

```bash
# Create config directory
mkdir -p ~/.openclaw

# Store API key in environment
echo 'export ANTHROPIC_API_KEY="sk-ant-YOUR-KEY-HERE"' >> ~/.bashrc

# Reload
source ~/.bashrc

# Verify (should show your key)
echo $ANTHROPIC_API_KEY
```

---

## Part 6: Set Up WhatsApp

### Step 6.1: Get eSIM Phone Number

On your personal phone:
1. Buy eSIM from Tello, Mint Mobile, or similar (~$5-10/month)
2. Scan QR code to add eSIM
3. Download **WhatsApp Business** app
4. Register with the NEW eSIM number

### Step 6.2: Note Your Number

Write down your eSIM phone number - you'll need it for pairing.

---

## Part 7: Configure OpenClaw

### Step 7.1: Run Onboarding Wizard

```bash
openclaw onboard --install-daemon
```

### Step 7.2: Follow the Wizard

**1. Gateway Type:**
```
? Gateway type:
❯ Local
  Remote
```
Choose: **Local** (even on VPS, this is correct)

---

**2. Model Provider:**
```
? Select auth method:
❯ Anthropic API Key
```
Choose: **Anthropic API Key**

---

**3. Enter API Key:**
Paste your Anthropic API key

---

**4. Channels:**
Select **WhatsApp**

---

**5. Skills:**
- Choose **Yes**
- Package manager: **npm**
- Skills: **Skip for now**

---

**6. API Keys:**
Choose **No** for all (add later)

---

**7. Daemon:**
Choose **Yes** to install systemd service

---

### Step 7.3: Connect WhatsApp

```bash
openclaw channels login --channel whatsapp
```

A QR code appears in your terminal.

**On your phone:**
1. Open **WhatsApp Business** (the one with your eSIM number)
2. Go to **Settings** → **Linked Devices** → **Link a Device**
3. Scan the QR code

**Note:** The QR code might look weird over SSH. If it doesn't scan:

```bash
# Try generating a cleaner QR
openclaw channels login --channel whatsapp --qr-terminal
```

Or use a different terminal app that renders better.

### Step 7.4: Verify Connection

```bash
openclaw channels status
```

Should show: `whatsapp: connected`

---

## Part 8: Set Up Firewall

Protect your VPS with a firewall:

```bash
# Install UFW
sudo apt install -y ufw

# Default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH (IMPORTANT - don't lock yourself out!)
sudo ufw allow 22/tcp

# Allow OpenClaw dashboard (optional, only if you need remote access to dashboard)
# sudo ufw allow 18789/tcp

# Enable firewall
sudo ufw enable
```

Type `y` when prompted.

```bash
# Verify
sudo ufw status
```

---

## Part 9: Keep OpenClaw Running

### Step 9.1: Check Systemd Service

```bash
# Check if service is running
sudo systemctl status openclaw

# If not running, start it
sudo systemctl start openclaw

# Enable on boot
sudo systemctl enable openclaw
```

### Step 9.2: View Logs

```bash
# View recent logs
openclaw logs --tail 50

# Or use journalctl
sudo journalctl -u openclaw -f
```

Press `Ctrl+C` to stop following logs.

---

## Part 10: Remote Dashboard Access (Optional)

By default, the dashboard only listens on localhost. To access it remotely:

### Option A: SSH Tunnel (Secure, Recommended)

On your LOCAL computer:

```bash
ssh -L 18789:localhost:18789 openclaw@YOUR_SERVER_IP
```

Then open in browser: http://localhost:18789

### Option B: Tailscale (Easiest)

On VPS:
```bash
# Install Tailscale
curl -fsSL https://tailscale.com/install.sh | sh

# Start Tailscale
sudo tailscale up
```

Follow the link to authenticate.

Then access dashboard via Tailscale IP: `http://100.x.x.x:18789`

### Option C: Open Port (Less Secure)

**Only do this if you understand the risks:**

```bash
# Open firewall port
sudo ufw allow 18789/tcp

# Edit OpenClaw config to listen on all interfaces
# This requires editing config file
```

---

## Part 11: Verify Everything

### Health Checks

```bash
# Overall status
openclaw status

# Health check
openclaw health

# Security audit
openclaw security audit --deep
```

### Send Test Message

Open WhatsApp Business on your phone, send:
```
Hello! What can you do?
```

Should get response within 30 seconds.

---

## 🎉 Congratulations!

Your VPS-based AI assistant is now running 24/7 in the cloud!

---

## 📅 Maintenance

### Check Status Regularly

```bash
ssh openclaw@YOUR_SERVER_IP
openclaw status
openclaw health
```

### Update OpenClaw

```bash
sudo npm update -g openclaw
sudo systemctl restart openclaw
```

### Update System

```bash
sudo apt update && sudo apt upgrade -y
sudo reboot  # If kernel updated
```

### Monitor Costs

- Check VPS provider dashboard for usage
- Check Anthropic console for API costs

---

## 🔧 Troubleshooting

### "Connection refused" when SSH

1. Check VPS is running in provider dashboard
2. Check IP address is correct
3. Check firewall allows port 22
4. Wait a minute and try again

### "WhatsApp disconnected"

```bash
openclaw channels login --channel whatsapp
```

Scan QR code again.

### "Gateway not running"

```bash
# Check service
sudo systemctl status openclaw

# Restart
sudo systemctl restart openclaw

# Check logs for errors
sudo journalctl -u openclaw --since "1 hour ago"
```

### "Out of memory"

Your VPS might be too small. Options:
1. Upgrade VPS to larger size (more RAM)
2. Add swap space:

```bash
# Create 2GB swap file
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make permanent
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

### "QR code doesn't scan"

Try different terminal:
```bash
# Use simpler QR output
openclaw channels login --channel whatsapp 2>&1 | less
```

Or take screenshot and zoom in on phone.

---

## 🔐 Security Best Practices

1. **Keep system updated** - run `apt upgrade` weekly
2. **Use strong passwords** - for user account and any services
3. **Don't open unnecessary ports** - keep firewall tight
4. **Monitor access logs** - `sudo journalctl -u sshd`
5. **Set up fail2ban** (optional, protects against brute force):

```bash
sudo apt install -y fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

6. **Use SSH keys only** - we already disabled root password login
7. **Backup config** - save `~/.openclaw/` regularly

---

## 💡 Pro Tips

### Use tmux for Persistent Sessions

```bash
# Install tmux
sudo apt install -y tmux

# Start new session
tmux new -s openclaw

# Detach: Ctrl+B, then D
# Reattach: tmux attach -t openclaw
```

### Set Up Monitoring

Consider setting up uptime monitoring:
- **UptimeRobot** (free) - checks if server responds
- **Healthchecks.io** (free) - monitors cron jobs

### Automatic Updates (Optional)

```bash
# Install unattended-upgrades
sudo apt install -y unattended-upgrades

# Enable
sudo dpkg-reconfigure -plow unattended-upgrades
```

This automatically installs security updates.

---

## 🗑️ Deleting Your VPS

If you want to stop and delete:

1. Stop OpenClaw: `openclaw gateway stop`
2. In VPS provider dashboard, **delete/destroy** the server
3. Stop paying

**Note:** This destroys everything. Backup important configs first!

---

*Guide Version: 1.0*
*Last Updated: February 2026*
