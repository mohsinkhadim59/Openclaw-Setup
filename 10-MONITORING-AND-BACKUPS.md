# OpenClaw Monitoring and Backups Guide

## 📋 Overview

This guide covers keeping your OpenClaw installation healthy and protected:

1. [Uptime Monitoring](#part-1-uptime-monitoring) - Know when it's down
2. [Resource Monitoring](#part-2-resource-monitoring) - CPU, memory, disk
3. [Cost Monitoring](#part-3-cost-monitoring) - Track API spending
4. [Log Management](#part-4-log-management) - Track what's happening
5. [Automated Backups](#part-5-automated-backups) - Protect your data
6. [Alerting Setup](#part-6-alerting-setup) - Get notified of issues
7. [Health Checks](#part-7-health-checks) - Automated verification

---

## Part 1: Uptime Monitoring

### Why Monitor Uptime?

- Know immediately when OpenClaw goes down
- Track uptime history
- Get alerts via email/SMS/Slack

### Option A: UptimeRobot (Free)

**Best for:** Personal use, simple monitoring

#### Setup

1. Go to: https://uptimerobot.com
2. Create free account
3. Click **Add New Monitor**
4. Configure:
   - **Monitor Type:** HTTP(s)
   - **Friendly Name:** `OpenClaw Gateway`
   - **URL:** `http://YOUR_IP:18789/health` (if exposed)
   - Or use **Port** type for localhost
   - **Monitoring Interval:** 5 minutes
5. Click **Create Monitor**

#### For Localhost (via Healthchecks.io)

If gateway isn't publicly accessible:

1. Go to: https://healthchecks.io
2. Create free account
3. Create new check
4. Copy the ping URL

Add cron job on your server:

```bash
# Edit crontab
crontab -e

# Add ping every 5 minutes
*/5 * * * * curl -fsS --retry 3 https://hc-ping.com/YOUR-UUID > /dev/null
```

This "phones home" every 5 minutes. If it stops, you get alerted.

### Option B: Better Uptime (More Features)

**Best for:** Teams, advanced alerting

1. Go to: https://betteruptime.com
2. Create account
3. Add monitor with your endpoint
4. Configure alert channels (email, Slack, PagerDuty)

### Option C: Self-Hosted (Uptime Kuma)

**Best for:** Privacy-conscious, full control

```bash
# Install via Docker
docker run -d \
  --name uptime-kuma \
  -p 3001:3001 \
  -v uptime-kuma:/app/data \
  louislam/uptime-kuma:1

# Access at http://localhost:3001
```

Configure monitors for:
- Gateway health endpoint
- Channel status
- External ping

---

## Part 2: Resource Monitoring

### 2.1: Quick Manual Checks

```bash
# CPU and Memory
top -l 1 | head -15    # macOS
htop                    # Linux (install: apt install htop)

# Disk usage
df -h

# OpenClaw specific
ps aux | grep openclaw
```

### 2.2: macOS Activity Monitor

1. Open **Activity Monitor** (Spotlight → "Activity Monitor")
2. Find `node` processes (OpenClaw runs on Node.js)
3. Monitor CPU, Memory, Energy, Disk, Network tabs

### 2.3: Linux System Monitoring

#### Install htop (Enhanced top)

```bash
sudo apt install htop
htop
```

#### Install glances (All-in-one)

```bash
sudo apt install glances
glances
```

### 2.4: Automated Resource Monitoring

#### Simple Script

Create monitoring script:

```bash
nano ~/monitor-openclaw.sh
```

```bash
#!/bin/bash

# Configuration
ALERT_CPU=80
ALERT_MEM=80
ALERT_DISK=90
WEBHOOK_URL="your-slack-webhook-url"  # Optional

# Get metrics
CPU=$(top -l 1 | grep "CPU usage" | awk '{print $3}' | sed 's/%//')
MEM=$(ps -o %mem= -p $(pgrep -f openclaw) 2>/dev/null | awk '{sum+=$1} END {print sum}')
DISK=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')

# Log
echo "$(date): CPU=$CPU%, MEM=$MEM%, DISK=$DISK%" >> ~/openclaw-metrics.log

# Alert if thresholds exceeded
if (( $(echo "$CPU > $ALERT_CPU" | bc -l) )); then
    echo "ALERT: High CPU usage: $CPU%"
    # Optionally send to Slack
    # curl -X POST -H 'Content-type: application/json' --data "{\"text\":\"⚠️ OpenClaw CPU: $CPU%\"}" $WEBHOOK_URL
fi

if (( $(echo "$DISK > $ALERT_DISK" | bc -l) )); then
    echo "ALERT: Low disk space: $DISK% used"
fi
```

Make executable and schedule:

```bash
chmod +x ~/monitor-openclaw.sh

# Run every 15 minutes
crontab -e
```

Add:
```
*/15 * * * * ~/monitor-openclaw.sh
```

### 2.5: AWS CloudWatch (For AWS Deployments)

If using AWS EC2:

1. Go to **CloudWatch** → **Alarms** → **Create Alarm**
2. Select metric:
   - **EC2** → **Per-Instance Metrics** → **CPUUtilization**
3. Configure:
   - Threshold: Greater than 80%
   - Period: 5 minutes
4. Add notification (SNS topic → Email)
5. Create alarm

Repeat for:
- Memory (requires CloudWatch Agent)
- Disk space
- Network

---

## Part 3: Cost Monitoring

### 3.1: Anthropic API Costs

#### Check Usage Dashboard

1. Go to: https://console.anthropic.com
2. Click **Usage** in sidebar
3. Review:
   - Daily usage
   - Cost by model
   - Request counts

#### Set Spending Limits

1. Go to **Settings** → **Billing**
2. Set **Monthly spending limit** (e.g., $100)
3. Enable **Usage alerts** at 50%, 80%, 100%

#### Export Usage Data

```bash
# If using OpenClaw's built-in tracking
openclaw usage --last 30d
openclaw usage --export csv > usage-report.csv
```

### 3.2: VPS/Cloud Costs

#### Hetzner

1. Log into Hetzner Cloud Console
2. Click your project
3. View **Usage & Billing**

#### DigitalOcean

1. Log into DigitalOcean
2. Click **Billing** in sidebar
3. View current month costs

#### AWS

1. Go to **AWS Cost Explorer**
2. View by service
3. Set up **Budgets** for alerts

### 3.3: Cost Tracking Spreadsheet

Create a simple tracker:

| Date | Anthropic API | VPS | eSIM | Total |
|------|---------------|-----|------|-------|
| Jan 2026 | $45.00 | $5.00 | $5.00 | $55.00 |
| Feb 2026 | $62.00 | $5.00 | $5.00 | $72.00 |

---

## Part 4: Log Management

### 4.1: View Logs

```bash
# Recent logs
openclaw logs --tail 50

# Follow logs in real-time
openclaw logs -f

# Logs from specific time
openclaw logs --since "2 hours ago"

# Filter by level
openclaw logs --level error --tail 100
```

### 4.2: Log Locations

| Platform | Location |
|----------|----------|
| All | `~/.openclaw/logs/` |
| macOS (launchd) | `~/Library/Logs/openclaw/` |
| Linux (systemd) | `journalctl -u openclaw` |

### 4.3: Log Rotation

Prevent logs from filling disk:

#### Automatic (Built-in)

OpenClaw handles basic rotation. Configure in:

```bash
nano ~/.openclaw/config.json
```

```json
{
  "logging": {
    "maxFiles": 7,
    "maxSize": "10m"
  }
}
```

#### Manual Cleanup

```bash
# Clear old logs
openclaw logs clear --older-than 30d

# Or manually
find ~/.openclaw/logs -name "*.log" -mtime +30 -delete
```

### 4.4: Centralized Logging (Advanced)

For production deployments, send logs to a service:

#### Papertrail (Free Tier Available)

```bash
# Install remote_syslog2
wget https://github.com/papertrail/remote_syslog2/releases/download/v0.21/remote_syslog_linux_amd64.tar.gz
tar xzf remote_syslog_linux_amd64.tar.gz
sudo mv remote_syslog/remote_syslog /usr/local/bin/

# Configure
sudo nano /etc/log_files.yml
```

```yaml
files:
  - /home/openclaw/.openclaw/logs/*.log
destination:
  host: logsX.papertrailapp.com
  port: XXXXX
  protocol: tls
```

---

## Part 5: Automated Backups

### 5.1: What to Backup

| Item | Location | Priority |
|------|----------|----------|
| Config | `~/.openclaw/config.json` | Critical |
| Credentials | `~/.openclaw/credentials/` | Critical |
| Memory/Context | `~/.openclaw/agents/` | High |
| Skills | `~/.openclaw/skills/` | Medium |
| Logs | `~/.openclaw/logs/` | Low |

### 5.2: Simple Backup Script

Create backup script:

```bash
nano ~/backup-openclaw.sh
```

```bash
#!/bin/bash

# Configuration
BACKUP_DIR="/path/to/backups"  # Local or mounted drive
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="openclaw-backup-$DATE.tar.gz"

# Create backup directory
mkdir -p $BACKUP_DIR

# Create compressed backup
tar -czvf $BACKUP_DIR/$BACKUP_FILE \
    ~/.openclaw/config.json \
    ~/.openclaw/credentials \
    ~/.openclaw/agents \
    ~/.openclaw/skills \
    2>/dev/null

# Remove backups older than 30 days
find $BACKUP_DIR -name "openclaw-backup-*.tar.gz" -mtime +30 -delete

# Encrypt backup (optional)
# gpg -c $BACKUP_DIR/$BACKUP_FILE

echo "Backup created: $BACKUP_DIR/$BACKUP_FILE"
```

Make executable:

```bash
chmod +x ~/backup-openclaw.sh
```

### 5.3: Schedule Automated Backups

```bash
crontab -e
```

Add:
```bash
# Daily backup at 3 AM
0 3 * * * /home/openclaw/backup-openclaw.sh >> /home/openclaw/backup.log 2>&1
```

### 5.4: Backup to Cloud Storage

#### AWS S3

```bash
# Install AWS CLI
brew install awscli  # macOS
sudo apt install awscli  # Linux

# Configure credentials
aws configure

# Modify backup script to upload
aws s3 cp $BACKUP_DIR/$BACKUP_FILE s3://your-bucket/openclaw-backups/
```

#### Google Drive (via rclone)

```bash
# Install rclone
brew install rclone  # macOS
sudo apt install rclone  # Linux

# Configure Google Drive
rclone config

# Add to backup script
rclone copy $BACKUP_DIR/$BACKUP_FILE gdrive:OpenClaw-Backups/
```

#### Backblaze B2 (Cheapest)

```bash
# Install B2 CLI
pip install b2

# Authorize
b2 authorize-account

# Upload
b2 upload-file your-bucket $BACKUP_DIR/$BACKUP_FILE openclaw-backups/$BACKUP_FILE
```

### 5.5: Restore from Backup

```bash
# Stop OpenClaw
openclaw gateway stop

# Extract backup
tar -xzvf openclaw-backup-XXXXXXXX.tar.gz -C /

# Or to specific location
mkdir ~/restore-test
tar -xzvf openclaw-backup-XXXXXXXX.tar.gz -C ~/restore-test

# Restart
openclaw gateway start
```

### 5.6: Backup Verification

Add verification to backup script:

```bash
# Verify backup integrity
if tar -tzf $BACKUP_DIR/$BACKUP_FILE > /dev/null 2>&1; then
    echo "✅ Backup verified: $BACKUP_FILE"
else
    echo "❌ Backup CORRUPT: $BACKUP_FILE"
    # Send alert
fi
```

---

## Part 6: Alerting Setup

### 6.1: Email Alerts

#### Using mailutils (Linux)

```bash
sudo apt install mailutils

# Test
echo "Test alert" | mail -s "OpenClaw Alert" your@email.com
```

#### Using msmtp (Recommended)

```bash
# Install
sudo apt install msmtp msmtp-mta

# Configure
nano ~/.msmtprc
```

```
defaults
auth           on
tls            on
tls_trust_file /etc/ssl/certs/ca-certificates.crt
logfile        ~/.msmtp.log

account        gmail
host           smtp.gmail.com
port           587
from           your@gmail.com
user           your@gmail.com
password       your-app-password

account default : gmail
```

```bash
chmod 600 ~/.msmtprc
```

### 6.2: Slack Alerts

#### Create Webhook

1. Go to: https://api.slack.com/apps
2. Create app → From scratch
3. Add feature: **Incoming Webhooks**
4. Activate and **Add New Webhook to Workspace**
5. Select channel
6. Copy webhook URL

#### Send Alert

```bash
#!/bin/bash
WEBHOOK_URL="https://hooks.slack.com/services/XXX/XXX/XXX"

send_slack_alert() {
    local message=$1
    curl -X POST -H 'Content-type: application/json' \
        --data "{\"text\":\"$message\"}" \
        $WEBHOOK_URL
}

# Usage
send_slack_alert "⚠️ OpenClaw Alert: Gateway down!"
```

### 6.3: Telegram Alerts

```bash
#!/bin/bash
BOT_TOKEN="your-bot-token"
CHAT_ID="your-chat-id"

send_telegram_alert() {
    local message=$1
    curl -s -X POST "https://api.telegram.org/bot$BOT_TOKEN/sendMessage" \
        -d chat_id=$CHAT_ID \
        -d text="$message"
}

# Usage
send_telegram_alert "⚠️ OpenClaw Alert: High CPU usage!"
```

### 6.4: PagerDuty (On-Call)

For serious deployments:

1. Create PagerDuty account
2. Create service for OpenClaw
3. Get integration key
4. Send events via API

```bash
curl -X POST https://events.pagerduty.com/v2/enqueue \
  -H 'Content-Type: application/json' \
  -d '{
    "routing_key": "YOUR_INTEGRATION_KEY",
    "event_action": "trigger",
    "payload": {
      "summary": "OpenClaw gateway down",
      "severity": "critical",
      "source": "openclaw-monitor"
    }
  }'
```

---

## Part 7: Health Checks

### 7.1: Built-in Health Commands

```bash
# Quick health check
openclaw health

# Detailed health
openclaw health --verbose

# Security audit
openclaw security audit --deep

# Full status
openclaw status --all
```

### 7.2: Automated Health Check Script

```bash
nano ~/health-check.sh
```

```bash
#!/bin/bash

# Configuration
ALERT_METHOD="slack"  # email, slack, or telegram
SLACK_WEBHOOK="https://hooks.slack.com/..."

# Check gateway
if ! openclaw gateway status > /dev/null 2>&1; then
    message="🔴 OpenClaw Gateway is DOWN!"
    
    # Try to restart
    openclaw gateway start
    
    # Alert
    if [ "$ALERT_METHOD" = "slack" ]; then
        curl -X POST -H 'Content-type: application/json' \
            --data "{\"text\":\"$message\"}" \
            $SLACK_WEBHOOK
    fi
fi

# Check channels
channels_status=$(openclaw channels status 2>&1)
if echo "$channels_status" | grep -q "disconnected"; then
    message="⚠️ OpenClaw Channel Disconnected: $channels_status"
    # Send alert...
fi

# Check disk space
disk_usage=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')
if [ "$disk_usage" -gt 90 ]; then
    message="💾 OpenClaw Server: Low disk space ($disk_usage%)"
    # Send alert...
fi

# Log health check
echo "$(date): Health check completed" >> ~/health-check.log
```

Schedule:

```bash
crontab -e
```

```bash
# Every 10 minutes
*/10 * * * * /home/openclaw/health-check.sh >> /home/openclaw/health-check.log 2>&1
```

### 7.3: Heartbeat Monitoring

Use healthchecks.io for "dead man's switch" monitoring:

1. Create check at https://healthchecks.io
2. Copy ping URL
3. Add to crontab:

```bash
# Ping every 5 minutes if gateway is healthy
*/5 * * * * openclaw health > /dev/null 2>&1 && curl -fsS --retry 3 https://hc-ping.com/YOUR-UUID > /dev/null
```

If the ping stops, you get alerted that something's wrong.

---

## 📊 Monitoring Dashboard Template

Create a simple status page:

```bash
nano ~/openclaw-status.sh
```

```bash
#!/bin/bash

echo "========================================"
echo "    OpenClaw Status Dashboard"
echo "    $(date)"
echo "========================================"
echo ""

echo "🖥️  SYSTEM"
echo "   CPU: $(top -l 1 | grep "CPU usage" | awk '{print $3}')"
echo "   Memory: $(top -l 1 | grep "PhysMem" | awk '{print $2}')"
echo "   Disk: $(df -h / | awk 'NR==2 {print $5 " used"}')"
echo ""

echo "🦞 OPENCLAW"
echo "   Gateway: $(openclaw gateway status 2>&1 | head -1)"
echo "   Version: $(openclaw --version)"
echo ""

echo "📱 CHANNELS"
openclaw channels status 2>&1 | sed 's/^/   /'
echo ""

echo "💰 TODAY'S USAGE"
openclaw usage --today 2>&1 | sed 's/^/   /' || echo "   (Usage tracking not available)"
echo ""

echo "📝 RECENT LOGS"
openclaw logs --tail 5 2>&1 | sed 's/^/   /'
echo ""

echo "========================================"
```

Run anytime:

```bash
chmod +x ~/openclaw-status.sh
~/openclaw-status.sh
```

---

## ✅ Monitoring Checklist

### Initial Setup

- [ ] Uptime monitoring configured (UptimeRobot/Healthchecks.io)
- [ ] Resource monitoring in place
- [ ] Anthropic spending limits set
- [ ] Alerts configured (email/Slack/Telegram)
- [ ] Backup script created and tested
- [ ] Backup schedule configured (cron)
- [ ] Health check script running
- [ ] Log rotation configured

### Weekly Tasks

- [ ] Review uptime reports
- [ ] Check API costs
- [ ] Verify backups exist
- [ ] Review any alerts/errors

### Monthly Tasks

- [ ] Test backup restoration
- [ ] Review and clean old logs
- [ ] Update monitoring thresholds
- [ ] Review security audit: `openclaw security audit --deep`

---

*Guide Version: 1.0*
*Last Updated: February 2026*
