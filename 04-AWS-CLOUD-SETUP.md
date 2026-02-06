# OpenClaw Setup Guide: AWS (Amazon Web Services)

## 📋 Overview

This guide walks you through setting up OpenClaw on **Amazon Web Services (AWS)** - the world's largest cloud platform. This is the most professional and scalable option, but also the most complex.

**Time Required:** 2-4 hours
**Difficulty:** Intermediate-Advanced
**Cost:** $20-100/month for EC2 + API costs

---

## ⚠️ Who Should Use AWS?

**Use AWS if:**
- ✅ You need enterprise-grade reliability
- ✅ Your company already uses AWS
- ✅ You need to scale to multiple instances
- ✅ You need compliance features (HIPAA, SOC2, etc.)
- ✅ You're comfortable with cloud infrastructure

**Don't use AWS if:**
- ❌ You just want a personal AI assistant (use Mac Mini or VPS)
- ❌ You're cost-sensitive (AWS gets expensive)
- ❌ You're new to cloud computing (start with Hetzner/DigitalOcean)
- ❌ You hate complex pricing models

---

## 🏛️ AWS Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                        AWS                          │
│  ┌───────────────────────────────────────────────┐ │
│  │                    VPC                        │ │
│  │  ┌─────────────────┐  ┌─────────────────────┐ │ │
│  │  │   Public Subnet │  │   Security Group    │ │ │
│  │  │  ┌───────────┐  │  │   - SSH (22)        │ │ │
│  │  │  │   EC2     │  │  │   - HTTPS (443)     │ │ │
│  │  │  │ Instance  │◄─┼──┤   - Dashboard (opt) │ │ │
│  │  │  │           │  │  └─────────────────────┘ │ │
│  │  │  └───────────┘  │                          │ │
│  │  └─────────────────┘                          │ │
│  └───────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────┘
                           │
                           ▼
                    ┌─────────────┐
                    │  WhatsApp   │
                    │  Telegram   │
                    │  etc.       │
                    └─────────────┘
```

---

## Part 1: Create AWS Account

### Step 1.1: Sign Up for AWS

1. Go to: https://aws.amazon.com
2. Click **Create an AWS Account**
3. Enter email, password, account name
4. Choose **Personal** or **Business** account type
5. Enter payment information (credit card required)
6. Verify phone number
7. Select **Basic Support** (free)

### Step 1.2: Set Up Billing Alerts (IMPORTANT!)

AWS can get expensive fast. Set up alerts:

1. Go to **AWS Console** → Search "Billing"
2. Click **Billing Dashboard**
3. On left menu, click **Budgets**
4. Click **Create budget**
5. Choose **Cost budget**
6. Set amount: **$50** (or whatever your limit is)
7. Add email alert at **80%** and **100%**
8. Create budget

**This prevents surprise bills!**

### Step 1.3: Create IAM User (Best Practice)

Don't use the root account for daily work:

1. Search for **IAM** in console
2. Click **Users** → **Add users**
3. Username: `admin`
4. Select **Provide user access to AWS Management Console**
5. Select **I want to create an IAM user**
6. Create password
7. Click **Next**
8. **Attach policies directly**
9. Search and select **AdministratorAccess**
10. Click **Next** → **Create user**
11. **Download credentials CSV** and save securely

**Sign out and sign back in with the IAM user** for all following steps.

---

## Part 2: Create EC2 Instance

### Step 2.1: Navigate to EC2

1. Go to AWS Console
2. Search for **EC2**
3. Click **EC2 Dashboard**
4. Select your preferred **Region** (top-right dropdown)
   - US East (N. Virginia) - us-east-1
   - US West (Oregon) - us-west-2
   - EU (Ireland) - eu-west-1

### Step 2.2: Create Key Pair

Before creating instance, create SSH key:

1. Left menu: **Network & Security** → **Key Pairs**
2. Click **Create key pair**
3. Name: `openclaw-key`
4. Key pair type: **ED25519** (or RSA)
5. Private key file format: **.pem** (for macOS/Linux) or **.ppk** (for Windows PuTTY)
6. Click **Create key pair**
7. **Save the downloaded file securely!** You can't download it again.

Move the key to proper location:
```bash
# On your local computer
mv ~/Downloads/openclaw-key.pem ~/.ssh/
chmod 400 ~/.ssh/openclaw-key.pem
```

### Step 2.3: Launch EC2 Instance

1. Click **Launch instance**

2. **Name:** `openclaw-server`

3. **Application and OS Images:**
   - Click **Ubuntu**
   - Select **Ubuntu Server 24.04 LTS**
   - Architecture: **64-bit (x86)**

4. **Instance type:**
   - **t3.small** (2 vCPU, 2GB RAM) - ~$15/month
   - Or **t3.medium** (2 vCPU, 4GB RAM) - ~$30/month (recommended)
   
5. **Key pair:**
   - Select `openclaw-key` (created above)

6. **Network settings:**
   - Click **Edit**
   - **VPC:** Default VPC (or create new)
   - **Subnet:** Any public subnet
   - **Auto-assign public IP:** **Enable**
   - **Create security group:**
     - Name: `openclaw-sg`
     - Description: `Security group for OpenClaw`
   - **Inbound Security Group Rules:**
     - Rule 1: **SSH** | Port 22 | Source: **My IP** (or 0.0.0.0/0 if IP changes)
     - Click **Add security group rule**
     - Rule 2: **Custom TCP** | Port **18789** | Source: **My IP** (for dashboard, optional)

7. **Configure storage:**
   - **20 GiB** gp3 (or gp2)
   - Delete on termination: **Yes** (or No if you want data to persist)

8. **Advanced details:**
   - Leave defaults

9. Click **Launch instance**

10. Click **View all instances**

### Step 2.4: Wait for Instance to Start

1. Wait for **Instance state** to show **Running**
2. Wait for **Status checks** to show **2/2 checks passed**
3. Note the **Public IPv4 address** (like `54.123.xxx.xxx`)

---

## Part 3: Connect to EC2 Instance

### Step 3.1: SSH into Instance

On your local computer:

```bash
ssh -i ~/.ssh/openclaw-key.pem ubuntu@YOUR_EC2_PUBLIC_IP
```

Replace `YOUR_EC2_PUBLIC_IP` with the IP from Step 2.4.

First time, type `yes` when asked about fingerprint.

**You're now connected to your AWS instance!**

### Step 3.2: Update System

```bash
sudo apt update && sudo apt upgrade -y
```

Wait 2-5 minutes.

---

## Part 4: Create OpenClaw User

### Step 4.1: Create User

```bash
sudo adduser openclaw
```

Enter password and details (or press Enter to skip optional fields).

### Step 4.2: Give Sudo Access

```bash
sudo usermod -aG sudo openclaw
```

### Step 4.3: Copy SSH Key to New User

```bash
sudo mkdir -p /home/openclaw/.ssh
sudo cp ~/.ssh/authorized_keys /home/openclaw/.ssh/
sudo chown -R openclaw:openclaw /home/openclaw/.ssh
sudo chmod 700 /home/openclaw/.ssh
sudo chmod 600 /home/openclaw/.ssh/authorized_keys
```

### Step 4.4: Test New User

Open NEW terminal:

```bash
ssh -i ~/.ssh/openclaw-key.pem openclaw@YOUR_EC2_PUBLIC_IP
```

Should connect successfully.

### Step 4.5: Disable Default Ubuntu User (Optional, More Secure)

```bash
sudo usermod -L ubuntu
```

---

## Part 5: Install Required Software

As the `openclaw` user:

### Step 5.1: Install Dependencies

```bash
# Install essentials
sudo apt install -y curl wget git build-essential

# Install Node.js 22
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs

# Verify
node --version    # v22.x.x
npm --version     # 10.x.x
```

### Step 5.2: Install OpenClaw

```bash
sudo npm install -g openclaw@latest

# Verify
openclaw --version
```

---

## Part 6: Configure AWS for Production

### Step 6.1: Attach Elastic IP (Static IP)

EC2 public IPs can change on reboot. Get a static IP:

1. In EC2 Console, left menu: **Network & Security** → **Elastic IPs**
2. Click **Allocate Elastic IP address**
3. Click **Allocate**
4. Select the new Elastic IP
5. Click **Actions** → **Associate Elastic IP address**
6. **Instance:** Select your `openclaw-server`
7. Click **Associate**

Now your instance has a permanent IP.

**Note:** Elastic IPs are free while associated. You're charged if not associated with a running instance.

### Step 6.2: Configure Security Group (Fine-tune Access)

Go to **EC2** → **Security Groups** → Select `openclaw-sg`:

1. Click **Inbound rules** tab
2. Click **Edit inbound rules**
3. Adjust rules:

| Type | Port | Source | Description |
|------|------|--------|-------------|
| SSH | 22 | My IP (or your office IP range) | SSH access |
| Custom TCP | 18789 | My IP | Dashboard access (optional) |

4. Click **Save rules**

### Step 6.3: Set Up CloudWatch Alarms (Monitoring)

1. Search for **CloudWatch**
2. Click **Alarms** → **Create alarm**
3. Click **Select metric**
4. Choose **EC2** → **Per-Instance Metrics**
5. Find your instance, select **CPUUtilization**
6. Set threshold: **Greater than 80** for **5 minutes**
7. Create SNS topic for email alerts
8. Name: `openclaw-cpu-alarm`
9. Create alarm

Repeat for memory/disk if needed.

---

## Part 7: Set Up Anthropic API

### Step 7.1: Get API Key

1. Go to: https://console.anthropic.com
2. Sign up / Sign in
3. Add payment method in Billing
4. Go to **API Keys** → **Create Key**
5. Copy the key

### Step 7.2: Store API Key

On EC2:

```bash
# Add to bashrc
echo 'export ANTHROPIC_API_KEY="sk-ant-YOUR-KEY-HERE"' >> ~/.bashrc
source ~/.bashrc

# Verify
echo $ANTHROPIC_API_KEY
```

---

## Part 8: Configure OpenClaw

### Step 8.1: Run Onboarding

```bash
openclaw onboard --install-daemon
```

### Step 8.2: Follow Wizard

1. **Gateway:** Local
2. **Auth:** Anthropic API Key
3. **API Key:** Paste your key
4. **Channels:** WhatsApp
5. **Skills:** Yes → npm → Skip for now
6. **Daemon:** Yes

### Step 8.3: Connect WhatsApp

```bash
openclaw channels login --channel whatsapp
```

Scan QR code with WhatsApp Business app on your phone.

### Step 8.4: Verify

```bash
openclaw status
openclaw health
openclaw channels status
```

---

## Part 9: Production Hardening

### Step 9.1: Configure UFW Firewall

```bash
sudo apt install -y ufw

# Allow SSH
sudo ufw allow 22/tcp

# Allow OpenClaw dashboard (optional - remove if not needed)
sudo ufw allow 18789/tcp

# Enable firewall
sudo ufw enable
```

### Step 9.2: Install Fail2ban

```bash
sudo apt install -y fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

### Step 9.3: Enable Automatic Security Updates

```bash
sudo apt install -y unattended-upgrades
sudo dpkg-reconfigure -plow unattended-upgrades
```

### Step 9.4: Set Up Log Rotation

```bash
# Check if logrotate is installed
which logrotate

# If not:
sudo apt install -y logrotate
```

### Step 9.5: Verify Systemd Service

```bash
# Check OpenClaw service
sudo systemctl status openclaw

# Enable on boot
sudo systemctl enable openclaw

# View logs
sudo journalctl -u openclaw -f
```

---

## Part 10: Set Up Backups (AWS Snapshots)

### Step 10.1: Manual Snapshot

1. Go to **EC2** → **Elastic Block Store** → **Volumes**
2. Select your instance's volume
3. Click **Actions** → **Create snapshot**
4. Description: `openclaw-backup-DATE`
5. Click **Create snapshot**

### Step 10.2: Automated Backups with Lifecycle Manager

1. Go to **EC2** → **Elastic Block Store** → **Lifecycle Manager**
2. Click **Create lifecycle policy**
3. **Policy type:** EBS snapshot policy
4. **Target resource type:** Volume
5. **Target resource tags:** Add a tag to your volume first
6. **Schedule:** Daily, retain 7 copies
7. Create policy

---

## Part 11: Cost Optimization

### Instance Recommendations

| Usage | Instance | Monthly Cost |
|-------|----------|--------------|
| Light | t3.small | ~$15 |
| Normal | t3.medium | ~$30 |
| Heavy | t3.large | ~$60 |

### Savings Plans

If running long-term:
1. Go to **AWS Cost Management** → **Savings Plans**
2. Purchase 1-year commitment for ~30% savings
3. Or use Reserved Instances for even more savings

### Spot Instances (Advanced)

For non-critical workloads, spot instances save 60-90%:
- **Warning:** Spot instances can be terminated with 2-minute notice
- Not recommended for primary OpenClaw, but good for testing

---

## Part 12: Multi-Region / High Availability (Advanced)

For enterprise deployments:

### Architecture

```
                    ┌──────────────┐
                    │   Route 53   │
                    │   (DNS)      │
                    └──────┬───────┘
                           │
           ┌───────────────┼───────────────┐
           │               │               │
           ▼               ▼               ▼
    ┌──────────┐    ┌──────────┐    ┌──────────┐
    │ us-east-1│    │ eu-west-1│    │ap-south-1│
    │   EC2    │    │   EC2    │    │   EC2    │
    └──────────┘    └──────────┘    └──────────┘
```

This is beyond scope of this guide, but involves:
- Multiple EC2 instances in different regions
- Route 53 for DNS failover
- Shared state via DynamoDB or Redis
- Load balancing

---

## 🎉 Congratulations!

You have a production-grade OpenClaw deployment on AWS!

---

## 📊 Monitoring Dashboard

### CloudWatch Metrics to Watch

1. **CPU Utilization:** Keep under 80%
2. **Network In/Out:** Track for unusual spikes
3. **Disk Read/Write:** Monitor for IO issues
4. **Status Check Failed:** Alert on instance problems

### Application Monitoring

```bash
# On EC2
openclaw status --all
openclaw health
openclaw logs --tail 100
```

---

## 🔧 Troubleshooting

### "Permission denied (publickey)"

```bash
# Check key permissions
chmod 400 ~/.ssh/openclaw-key.pem

# Use correct username
ssh -i ~/.ssh/openclaw-key.pem ubuntu@IP    # Not root!
```

### "Connection timed out"

1. Check instance is **Running**
2. Check **Security Group** allows your IP
3. Check **Network ACL** allows traffic
4. Check **Elastic IP** is associated

### "Instance unreachable after reboot"

If not using Elastic IP, IP changed:
1. Check new IP in EC2 console
2. Or associate an Elastic IP (recommended)

### "High AWS bill"

1. Check **Cost Explorer** for breakdown
2. Look for:
   - Unused Elastic IPs (charged when not associated)
   - Unused EBS volumes
   - Data transfer costs
3. Set up **Budgets** and **Alarms**

### "OpenClaw using too much CPU"

1. Check for runaway conversations
2. Upgrade to larger instance
3. Check logs for errors: `openclaw logs`

---

## 🗑️ Cleanup (Deleting Everything)

To avoid charges, delete in this order:

1. **Stop EC2 instance**
2. **Release Elastic IP** (if used)
3. **Delete Snapshots** (if created)
4. **Delete EBS Volumes** (if orphaned)
5. **Terminate EC2 instance**
6. **Delete Security Group** (after instance terminated)
7. **Delete Key Pair** (optional)

Check **Billing** console to confirm no ongoing charges.

---

## 📚 AWS Resources

- **AWS Free Tier:** https://aws.amazon.com/free/
- **EC2 Pricing:** https://aws.amazon.com/ec2/pricing/
- **AWS Documentation:** https://docs.aws.amazon.com/ec2/

---

*Guide Version: 1.0*
*Last Updated: February 2026*
