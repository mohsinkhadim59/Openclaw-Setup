# OpenClaw Skills Installation Guide

## 📋 Overview

Skills are plugins that extend OpenClaw's capabilities. They let your AI assistant interact with external services, tools, and APIs.

This guide covers:
1. [Understanding Skills](#part-1-understanding-skills)
2. [Installing Skills from ClawHub](#part-2-installing-skills-from-clawhub)
3. [Popular Skills Setup](#part-3-popular-skills-setup)
4. [Managing Skills](#part-4-managing-skills)
5. [Creating Custom Skills](#part-5-creating-custom-skills)

---

## Part 1: Understanding Skills

### What Are Skills?

Skills are packages that teach OpenClaw how to:
- Use specific tools (1Password, Notion, Todoist)
- Interact with APIs (GitHub, Jira, Linear)
- Perform specialized tasks (web scraping, image processing)
- Access local system features (camera, screenshots)

### Skill Types

| Type | Description | Example |
|------|-------------|---------|
| **Bundled** | Included with OpenClaw | exec, web, file |
| **ClawHub** | Community skills from clawdhub.com | 1password, notion |
| **Workspace** | Your custom skills | Custom automations |

### How Skills Work

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   You       │────▶│  OpenClaw   │────▶│   Skill     │
│  "Add task" │     │  (Router)   │     │  (Todoist)  │
└─────────────┘     └─────────────┘     └─────────────┘
                                               │
                                               ▼
                                        ┌─────────────┐
                                        │  Todoist    │
                                        │    API      │
                                        └─────────────┘
```

---

## Part 2: Installing Skills from ClawHub

### Step 2.1: Browse Available Skills

```bash
# List all available skills
openclaw skills search

# Search for specific skill
openclaw skills search notion

# View skill details
openclaw skills info steipete/notion
```

Or browse online: https://clawdhub.com

### Step 2.2: Install a Skill

```bash
# Install skill by name
openclaw skills install steipete/notion

# Install specific version
openclaw skills install steipete/notion@1.0.0

# Install multiple skills
openclaw skills install steipete/notion steipete/todoist steipete/1password
```

### Step 2.3: Verify Installation

```bash
# List installed skills
openclaw skills list

# Check skill status
openclaw skills status
```

### Step 2.4: Restart Gateway

After installing skills:

```bash
openclaw gateway restart
```

---

## Part 3: Popular Skills Setup

### 3.1: 1Password Skill

**What it does:** Access passwords and secrets securely.

#### Prerequisites
- 1Password account
- 1Password CLI installed

#### Install 1Password CLI

**macOS:**
```bash
brew install 1password-cli
```

**Linux:**
```bash
curl -sS https://downloads.1password.com/linux/keys/1password.asc | sudo gpg --dearmor --output /usr/share/keyrings/1password-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/1password-archive-keyring.gpg] https://downloads.1password.com/linux/debian/amd64 stable main" | sudo tee /etc/apt/sources.list.d/1password.list
sudo apt update && sudo apt install 1password-cli
```

#### Enable Desktop App Integration

1. Open 1Password desktop app
2. Go to **Settings** → **Developer**
3. Enable **Integrate with 1Password CLI**

#### Sign In

```bash
# Sign in to 1Password
op signin

# Verify
op whoami
```

#### Install Skill

```bash
openclaw skills install steipete/1password
openclaw gateway restart
```

#### Usage Examples

```
"Get my Netflix password"
"What's the API key for AWS?"
"Show me my SSH key for server1"
```

---

### 3.2: Notion Skill

**What it does:** Read, create, and update Notion pages and databases.

#### Prerequisites
- Notion account
- Notion Integration (API key)

#### Create Notion Integration

1. Go to: https://www.notion.so/my-integrations
2. Click **+ New Integration**
3. Configure:
   - **Name:** OpenClaw
   - **Associated workspace:** Your workspace
   - **Capabilities:** Read, Update, Insert content
4. Click **Submit**
5. Copy the **Internal Integration Token** (starts with `secret_`)

#### Share Pages with Integration

For each page/database you want OpenClaw to access:

1. Open the page in Notion
2. Click **...** (top right) → **Connections**
3. Select **OpenClaw**

#### Configure OpenClaw

```bash
# Set API key
export NOTION_API_KEY="secret_xxxxxxxxxx"
echo 'export NOTION_API_KEY="secret_xxxxxxxxxx"' >> ~/.bashrc
```

#### Install Skill

```bash
openclaw skills install steipete/notion
openclaw gateway restart
```

#### Usage Examples

```
"Add a note to my Notion: Meeting notes from today..."
"Search my Notion for project plans"
"Update my reading list in Notion"
```

---

### 3.3: Todoist Skill

**What it does:** Manage tasks in Todoist.

#### Prerequisites
- Todoist account
- Todoist API token

#### Get API Token

1. Go to: https://todoist.com/app/settings/integrations
2. Scroll to **API token**
3. Copy the token

#### Configure OpenClaw

```bash
export TODOIST_API_KEY="your_token_here"
echo 'export TODOIST_API_KEY="your_token_here"' >> ~/.bashrc
```

#### Install Skill

```bash
openclaw skills install steipete/todoist
openclaw gateway restart
```

#### Usage Examples

```
"Add task: Buy groceries tomorrow"
"Show my tasks for today"
"Mark 'Call mom' as complete"
"What's on my todo list?"
```

---

### 3.4: GitHub Skill

**What it does:** Interact with GitHub repositories, issues, PRs.

#### Prerequisites
- GitHub account
- Personal Access Token

#### Create GitHub Token

1. Go to: https://github.com/settings/tokens
2. Click **Generate new token (classic)**
3. Scopes needed:
   - `repo` (full repository access)
   - `read:org` (if using organizations)
4. Generate and copy token

#### Configure OpenClaw

```bash
export GITHUB_TOKEN="ghp_xxxxxxxxxxxx"
echo 'export GITHUB_TOKEN="ghp_xxxxxxxxxxxx"' >> ~/.bashrc
```

#### Install Skill

```bash
openclaw skills install steipete/github
openclaw gateway restart
```

#### Usage Examples

```
"Show my open PRs"
"Create an issue in myrepo: Bug - login fails"
"List recent commits in myproject"
"What issues are assigned to me?"
```

---

### 3.5: Linear Skill

**What it does:** Manage issues in Linear.

#### Prerequisites
- Linear account
- Linear API key

#### Get API Key

1. Go to Linear Settings → API
2. Create Personal API Key
3. Copy the key

#### Configure OpenClaw

```bash
export LINEAR_API_KEY="lin_api_xxxxxxxxxxxx"
echo 'export LINEAR_API_KEY="lin_api_xxxxxxxxxxxx"' >> ~/.bashrc
```

#### Install Skill

```bash
openclaw skills install steipete/linear
openclaw gateway restart
```

#### Usage Examples

```
"Create a Linear issue: Implement dark mode"
"Show my assigned issues"
"Update issue LIN-123 to In Progress"
```

---

### 3.6: Jira Skill

**What it does:** Manage Jira issues and projects.

#### Prerequisites
- Jira account
- API token

#### Create API Token

1. Go to: https://id.atlassian.com/manage-profile/security/api-tokens
2. Click **Create API token**
3. Name: `OpenClaw`
4. Copy token

#### Configure OpenClaw

```bash
export JIRA_HOST="your-domain.atlassian.net"
export JIRA_EMAIL="your-email@example.com"
export JIRA_API_TOKEN="your_token"

# Add to bashrc
echo 'export JIRA_HOST="your-domain.atlassian.net"' >> ~/.bashrc
echo 'export JIRA_EMAIL="your-email@example.com"' >> ~/.bashrc
echo 'export JIRA_API_TOKEN="your_token"' >> ~/.bashrc
```

#### Install Skill

```bash
openclaw skills install steipete/jira
openclaw gateway restart
```

#### Usage Examples

```
"Show my Jira tickets"
"Create Jira issue in PROJECT: Bug description"
"Move PROJ-123 to Done"
```

---

### 3.7: Slack Skill (Extended)

**What it does:** Send messages, read channels, manage Slack.

This extends the basic Slack channel to add more capabilities.

#### Install Skill

```bash
openclaw skills install steipete/slack-tools
openclaw gateway restart
```

#### Usage Examples

```
"Post in #general: Team meeting at 3pm"
"Search Slack for messages about the budget"
"List channels I'm in"
```

---

### 3.8: Web Browser Skill

**What it does:** Browse websites, take screenshots, automate web tasks.

#### Prerequisites
- Chrome/Chromium installed

#### Install Skill

```bash
openclaw skills install steipete/browser
openclaw gateway restart
```

#### Usage Examples

```
"Take a screenshot of google.com"
"Check if example.com is up"
"Get the title of this webpage: https://..."
```

---

### 3.9: Camera/Screenshot Skill (macOS)

**What it does:** Capture screenshots and camera photos.

#### Grant Permissions (macOS)

1. **System Settings** → **Privacy & Security** → **Screen Recording**
2. Add Terminal (or your terminal app)
3. **Privacy & Security** → **Camera**
4. Add Terminal

#### Install Skill

```bash
openclaw skills install steipete/camsnap
openclaw gateway restart
```

#### Usage Examples

```
"Take a screenshot"
"Capture my screen and describe what you see"
"Take a photo with my webcam"
```

---

### 3.10: Peekaboo Skill (macOS)

**What it does:** See your screen and describe what's on it.

#### Install

```bash
# Install peekaboo tool
brew install steipete/tap/peekaboo

# Install skill
openclaw skills install steipete/peekaboo
openclaw gateway restart
```

#### Usage Examples

```
"What's on my screen right now?"
"Describe the window I'm looking at"
"Read the text in this app"
```

---

## Part 4: Managing Skills

### List Installed Skills

```bash
openclaw skills list
```

### Check Skill Status

```bash
openclaw skills status
```

### Update Skills

```bash
# Update all skills
openclaw skills update

# Update specific skill
openclaw skills update steipete/notion
```

### Remove Skills

```bash
# Remove a skill
openclaw skills remove steipete/notion

# Restart gateway after removal
openclaw gateway restart
```

### View Skill Documentation

```bash
# View skill readme
openclaw skills info steipete/notion --readme
```

### Skill Configuration

Some skills have configuration options:

```bash
# Configure a skill
openclaw skills configure steipete/notion

# Or edit directly
nano ~/.openclaw/skills/steipete/notion/config.json
```

---

## Part 5: Creating Custom Skills

### Skill Structure

A skill is a folder with:

```
my-custom-skill/
├── SKILL.md          # Required: Instructions for AI
├── package.json      # Optional: Dependencies
├── scripts/          # Optional: Helper scripts
│   └── helper.js
└── config.json       # Optional: Configuration
```

### Step 5.1: Create Skill Directory

```bash
mkdir -p ~/.openclaw/workspace/skills/my-skill
cd ~/.openclaw/workspace/skills/my-skill
```

### Step 5.2: Create SKILL.md

This file tells the AI how to use your skill:

```markdown
# My Custom Skill

## Description
This skill does X, Y, and Z.

## Commands

### Get Data
\`\`\`bash
curl -s "https://api.example.com/data" -H "Authorization: Bearer $API_KEY"
\`\`\`

### Post Data
\`\`\`bash
curl -X POST "https://api.example.com/data" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}'
\`\`\`

## Environment Variables
- `API_KEY`: Your API key from example.com

## Examples
- "Get my data from example" → Uses Get Data command
- "Save this to example: hello" → Uses Post Data command
```

### Step 5.3: Add Scripts (Optional)

For complex operations, add scripts:

```javascript
// scripts/get-data.js
const https = require('https');

const apiKey = process.env.API_KEY;
const url = `https://api.example.com/data`;

https.get(url, { headers: { 'Authorization': `Bearer ${apiKey}` }}, (res) => {
  let data = '';
  res.on('data', chunk => data += chunk);
  res.on('end', () => console.log(data));
});
```

### Step 5.4: Register Skill

```bash
# Link your skill
openclaw skills link ~/.openclaw/workspace/skills/my-skill

# Or add to config
nano ~/.openclaw/config.json
```

Add:
```json
{
  "skills": {
    "workspace": ["~/.openclaw/workspace/skills/my-skill"]
  }
}
```

### Step 5.5: Restart and Test

```bash
openclaw gateway restart
```

Test via your messaging channel:
```
"Use my custom skill to get data"
```

---

## 🔧 Troubleshooting Skills

### Skill Not Working

```bash
# Check if skill is loaded
openclaw skills status

# Check logs for errors
openclaw logs --tail 100 | grep -i skill

# Verify skill files exist
ls -la ~/.openclaw/skills/steipete/notion/
```

### "Skill Not Found"

```bash
# Reinstall skill
openclaw skills remove steipete/notion
openclaw skills install steipete/notion
openclaw gateway restart
```

### Permission Errors

- Check environment variables are set
- Check API keys are valid
- Check file permissions on skill directory

### API Rate Limits

- Most APIs have rate limits
- Space out requests
- Check API provider documentation

---

## 📋 Skills Quick Reference

| Skill | Install Command | Requires |
|-------|-----------------|----------|
| 1Password | `steipete/1password` | 1Password CLI |
| Notion | `steipete/notion` | API key |
| Todoist | `steipete/todoist` | API key |
| GitHub | `steipete/github` | Personal Access Token |
| Linear | `steipete/linear` | API key |
| Jira | `steipete/jira` | API token |
| Browser | `steipete/browser` | Chrome |
| Camera | `steipete/camsnap` | macOS permissions |
| Peekaboo | `steipete/peekaboo` | macOS + Homebrew |
| Google (GOG) | `steipete/gog` | OAuth setup |

---

## 🔒 Security Considerations

### API Key Management

1. **Never hardcode** API keys in skill files
2. **Use environment variables**
3. **Rotate keys** periodically
4. **Use least privilege** - only grant necessary permissions

### Skill Trust

- Only install skills from trusted sources
- Review skill code before installing unknown skills
- ClawHub skills are community-maintained

### Workspace Skills

- Keep custom skills in `~/.openclaw/workspace/skills/`
- Don't put skills in system directories
- Backup your custom skills

---

*Guide Version: 1.0*
*Last Updated: February 2026*
