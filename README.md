# OpenClaw Setup Guides

![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![Guides](https://img.shields.io/badge/Guides-11-blue)
![Platforms](https://img.shields.io/badge/Platforms-4-orange)
![Channels](https://img.shields.io/badge/Channels-9-purple)
![Updated](https://img.shields.io/badge/Updated-February_2026-yellow)
![License](https://img.shields.io/badge/License-MIT-green)

Complete step-by-step guides for installing and configuring [OpenClaw](https://openclaw.ai) — an open-source AI agent that runs on your own hardware and connects to messaging apps like WhatsApp, Telegram, Discord, Slack, iMessage, and more.

OpenClaw acts as a 24/7 personal AI assistant that can manage emails, run commands, do research, take autonomous actions, and remember context across conversations. Unlike cloud-based AI tools, OpenClaw runs locally with full system access.

---

## Guides

### Setup Guides

| Guide | Description | Difficulty |
|-------|-------------|------------|
| [Overview & Quick Start](00-OPENCLAW-SETUP-OVERVIEW.md) | Guide selection, prerequisites, and quick start | - |
| [Local Mac Setup](01-LOCAL-MAC-SETUP.md) | Install on your existing Mac for testing | Beginner |
| [Dedicated Mac Mini](02-MAC-MINI-DEDICATED-SETUP.md) | Best personal setup — always-on AI server | Beginner–Intermediate |
| [VPS (Linux)](03-VPS-LINUX-SETUP.md) | Deploy to Hetzner, DigitalOcean, Linode, etc. | Intermediate |
| [AWS Cloud](04-AWS-CLOUD-SETUP.md) | Enterprise-grade EC2 deployment | Intermediate–Advanced |

### Configuration & Operations

| Guide | Description |
|-------|-------------|
| [Security Hardening](05-SECURITY-HARDENING.md) | Account isolation, API key protection, firewall, prompt injection defense |
| [Troubleshooting](06-TROUBLESHOOTING.md) | Common issues and solutions across all setups |
| [Messaging Channels](07-MESSAGING-CHANNELS-SETUP.md) | WhatsApp, Telegram, Discord, Slack, iMessage, Signal, and more |
| [Google Integration](08-GOOGLE-INTEGRATION-SETUP.md) | Gmail, Calendar, Drive, Sheets, Contacts |
| [Skills Installation](09-SKILLS-INSTALLATION-GUIDE.md) | Plugin system — 1Password, Notion, Todoist, GitHub, Jira, etc. |
| [Monitoring & Backups](10-MONITORING-AND-BACKUPS.md) | Uptime monitoring, resource tracking, automated backups |

---

## Quick Start

```bash
# Install OpenClaw
curl -fsSL https://openclaw.ai/install.sh | bash

# Run the setup wizard
openclaw onboard --install-daemon

# Open the dashboard
openclaw dashboard
```

---

## Prerequisites

- **Node.js 22+** and npm 10+
- **Anthropic API Key** — sign up at [console.anthropic.com](https://console.anthropic.com)
- **Separate phone number** (eSIM recommended) for WhatsApp
- Basic terminal / command-line knowledge

---

## Which Setup Should You Choose?

| Setup | Hardware Cost | Monthly Cost | Best For |
|-------|--------------|--------------|----------|
| Local Mac | $0 | $0 | Testing, development |
| Mac Mini | ~$500–600 | $0 | Personal 24/7 use (recommended) |
| VPS | $0 | $5–20 | Remote access, no local hardware |
| AWS | $0 | $20–100 | Enterprise, teams, scaling |

All setups require **Anthropic API costs** of approximately $2–10/day depending on usage.

---

## Security Warning

OpenClaw has **full access** to whatever machine it runs on — it can read/write files, execute commands, access the network, and send messages as you.

**Always:**
- Use a dedicated Apple ID / Google account
- Use a separate phone number for WhatsApp
- Review the [Security Hardening](05-SECURITY-HARDENING.md) guide before deploying
- Never run on machines with sensitive financial or corporate data

---

## Key Commands

```bash
openclaw status          # Overall status
openclaw health          # Detailed health check
openclaw logs --tail 50  # View recent logs
openclaw dashboard       # Web UI at http://127.0.0.1:18789
openclaw channels status # Check messaging connections
openclaw skills list     # Show installed plugins
openclaw security audit  # Run security check
```

---

## Supported Platforms

- **macOS** 12 (Monterey) or later
- **Linux** — Ubuntu 22.04 / 24.04 LTS

## Supported Messaging Channels

WhatsApp, Telegram, Discord, Slack, iMessage (macOS only), Signal, Google Chat, Mattermost, Matrix

---

## Resources

- [Official Docs](https://docs.openclaw.ai)
- [GitHub Issues](https://github.com/openclaw/openclaw/issues)
- [Discord Community](https://openclaw.ai) (invite link on site)

---

## Contributing

Contributions are welcome — from fixing typos to adding new guides. See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## License

This project is licensed under the [MIT License](LICENSE).

---

*Last Updated: February 2026 — OpenClaw v2026.1.x*
