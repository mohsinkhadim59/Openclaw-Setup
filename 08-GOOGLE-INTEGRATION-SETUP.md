# OpenClaw Google Integration Setup Guide

## 📋 Overview

This guide covers integrating OpenClaw with Google services:

1. [Gmail](#1-gmail-integration) - Read, search, send emails
2. [Google Calendar](#2-google-calendar-integration) - View, create, manage events
3. [Google Drive](#3-google-drive-integration) - Search, read, create files
4. [Google Contacts](#4-google-contacts-integration) - Access contact information
5. [Google Sheets](#5-google-sheets-integration) - Read/write spreadsheets

After setup, your AI assistant can:
- "Check my email for messages from John"
- "Schedule a meeting with Sarah tomorrow at 2pm"
- "Find the Q4 report in my Drive"
- "Add a row to my expense spreadsheet"

---

## ⚠️ Prerequisites

Before starting, you need:

- [ ] Google account (personal or Workspace)
- [ ] Access to Google Cloud Console
- [ ] OpenClaw installed and running
- [ ] ~30 minutes for setup

**Cost:** Google Cloud has a free tier that covers normal personal use. You won't be charged unless you exceed generous limits.

---

## Part 1: Create Google Cloud Project

### Step 1.1: Access Google Cloud Console

1. Go to: https://console.cloud.google.com
2. Sign in with your Google account
3. Accept terms of service if prompted

### Step 1.2: Create New Project

1. Click the project dropdown (top left, next to "Google Cloud")
2. Click **New Project**
3. Configure:
   - **Project name:** `OpenClaw Integration`
   - **Organization:** Leave as-is or select yours
   - **Location:** Leave as-is
4. Click **Create**
5. Wait for project creation (30 seconds)

### Step 1.3: Select Your Project

1. Click the project dropdown again
2. Select **OpenClaw Integration**
3. Verify project name shows in header

---

## Part 2: Enable Required APIs

### Step 2.1: Navigate to API Library

1. In left sidebar, click **APIs & Services** → **Library**
2. Or search "API Library" in the search bar

### Step 2.2: Enable Each API

Search for and enable each of these APIs:

**Gmail API:**
1. Search: `Gmail API`
2. Click on **Gmail API**
3. Click **Enable**
4. Wait for activation

**Google Calendar API:**
1. Search: `Google Calendar API`
2. Click on **Google Calendar API**
3. Click **Enable**

**Google Drive API:**
1. Search: `Google Drive API`
2. Click on **Google Drive API**
3. Click **Enable**

**Google Sheets API:**
1. Search: `Google Sheets API`
2. Click on **Google Sheets API**
3. Click **Enable**

**Google Docs API:** (optional)
1. Search: `Google Docs API`
2. Click on **Google Docs API**
3. Click **Enable**

**People API:** (for Contacts)
1. Search: `People API`
2. Click on **People API**
3. Click **Enable**

### Step 2.3: Verify APIs Enabled

1. Go to **APIs & Services** → **Enabled APIs**
2. Confirm all APIs are listed

---

## Part 3: Configure OAuth Consent Screen

This is required before creating credentials.

### Step 3.1: Navigate to OAuth Consent

1. Go to **APIs & Services** → **OAuth consent screen**
2. Or search "OAuth consent" in search bar

### Step 3.2: Select User Type

- **Internal:** Only users in your Google Workspace (if applicable)
- **External:** Any Google user (select this for personal accounts)

Click **Create**

### Step 3.3: Configure App Information

**App Information:**
- **App name:** `OpenClaw Assistant`
- **User support email:** Your email
- **App logo:** Optional (upload if desired)

**App Domain:**
- Leave all fields blank (not needed for personal use)

**Developer Contact Information:**
- **Email addresses:** Your email

Click **Save and Continue**

### Step 3.4: Configure Scopes

Click **Add or Remove Scopes**

Add these scopes (search or scroll to find them):

**Gmail Scopes:**
```
https://www.googleapis.com/auth/gmail.readonly
https://www.googleapis.com/auth/gmail.send
https://www.googleapis.com/auth/gmail.compose
https://www.googleapis.com/auth/gmail.modify
```

**Calendar Scopes:**
```
https://www.googleapis.com/auth/calendar
https://www.googleapis.com/auth/calendar.events
```

**Drive Scopes:**
```
https://www.googleapis.com/auth/drive.readonly
https://www.googleapis.com/auth/drive.file
```

**Sheets Scopes:**
```
https://www.googleapis.com/auth/spreadsheets
```

**Contacts Scopes:**
```
https://www.googleapis.com/auth/contacts.readonly
```

Click **Update** then **Save and Continue**

### Step 3.5: Add Test Users

Since app is in "Testing" mode, add yourself:

1. Click **Add Users**
2. Enter your Gmail address
3. Click **Add**
4. Click **Save and Continue**

### Step 3.6: Review and Complete

Review summary and click **Back to Dashboard**

---

## Part 4: Create OAuth Credentials

### Step 4.1: Navigate to Credentials

1. Go to **APIs & Services** → **Credentials**
2. Or search "Credentials" in search bar

### Step 4.2: Create OAuth Client ID

1. Click **+ Create Credentials**
2. Select **OAuth client ID**

### Step 4.3: Configure OAuth Client

**Application type:** Desktop app

**Name:** `OpenClaw Desktop`

Click **Create**

### Step 4.4: Download Credentials

A popup shows your Client ID and Client Secret.

1. Click **Download JSON**
2. Save the file as `client_secret.json`
3. Move to a safe location:

```bash
# Create credentials directory
mkdir -p ~/.openclaw/credentials

# Move the downloaded file
mv ~/Downloads/client_secret_*.json ~/.openclaw/credentials/client_secret.json

# Secure permissions
chmod 600 ~/.openclaw/credentials/client_secret.json
```

### Step 4.5: Note Your Credentials

From the popup or downloaded file, note:
- **Client ID:** `xxxx.apps.googleusercontent.com`
- **Client Secret:** `GOCSPX-xxxxx`

Click **OK** to close popup.

---

## Part 5: Install GOG CLI Tool

GOG (Google Operations for OpenClaw) is the recommended way to interact with Google services.

### Step 5.1: Install via Homebrew (macOS)

```bash
# Add the tap
brew tap steipete/tap

# Install gog
brew install gogcli
```

### Step 5.2: Install via npm (All Platforms)

```bash
npm install -g @openclaw/gog
```

### Step 5.3: Verify Installation

```bash
gog --version
```

---

## Part 6: Authenticate with Google

### Step 6.1: Set Up Credentials

```bash
gog auth credentials ~/.openclaw/credentials/client_secret.json
```

### Step 6.2: Add Your Account

```bash
gog auth add your.email@gmail.com --services gmail,calendar,drive,contacts,sheets
```

### Step 6.3: Complete OAuth Flow

1. A browser window opens
2. Sign in with your Google account
3. Click **Continue** (ignore "unverified app" warning - it's your own app)
4. Grant all requested permissions
5. Browser shows "Authentication successful"
6. Return to terminal

### Step 6.4: Verify Authentication

```bash
# List authenticated accounts
gog auth list

# Test Gmail access
gog gmail search 'newer_than:1d' --max 5

# Test Calendar access
gog calendar events primary --from $(date -I) --max 5

# Test Drive access
gog drive search "test" --max 5
```

---

## Part 7: Configure OpenClaw to Use Google

### Step 7.1: Set Environment Variable

```bash
# Add to bashrc/zshrc
echo 'export GOG_ACCOUNT="your.email@gmail.com"' >> ~/.bashrc
source ~/.bashrc
```

### Step 7.2: Install Google Skills

```bash
# Install the Google skill
openclaw skills install steipete/gog
```

### Step 7.3: Restart Gateway

```bash
openclaw gateway restart
```

---

## 1. Gmail Integration

### What You Can Do

- Search emails
- Read email content
- Send emails
- Reply to emails
- List labels/folders

### Example Commands to Your AI

```
"Check my email for messages from john@example.com"
"Search emails with subject 'invoice'"
"Send an email to sarah@example.com saying I'll be late"
"Show me unread emails from today"
"Find emails with attachments from last week"
```

### CLI Commands (for testing)

```bash
# Search emails
gog gmail search 'from:john@example.com' --max 10

# Search recent emails
gog gmail search 'newer_than:7d' --max 20

# Search by subject
gog gmail search 'subject:invoice' --max 10

# Send email
gog gmail send --to recipient@example.com --subject "Hello" --body "Message body"

# Send with CC
gog gmail send --to main@example.com --cc copy@example.com --subject "Hello" --body "Message"
```

### Gmail Search Operators

| Operator | Example | Description |
|----------|---------|-------------|
| `from:` | `from:john` | Emails from sender |
| `to:` | `to:sarah` | Emails to recipient |
| `subject:` | `subject:meeting` | Subject contains |
| `newer_than:` | `newer_than:7d` | Within time period |
| `older_than:` | `older_than:1m` | Older than period |
| `has:attachment` | `has:attachment` | Has attachments |
| `is:unread` | `is:unread` | Unread emails |
| `label:` | `label:important` | Has label |

---

## 2. Google Calendar Integration

### What You Can Do

- View upcoming events
- Create new events
- Update existing events
- Delete events
- Check availability

### Example Commands to Your AI

```
"What's on my calendar today?"
"Schedule a meeting with John tomorrow at 2pm"
"Create a reminder for Friday at 10am to call the dentist"
"Cancel my 3pm meeting"
"What do I have next week?"
"Am I free on Thursday afternoon?"
```

### CLI Commands (for testing)

```bash
# List upcoming events
gog calendar events primary --from $(date -I) --to $(date -I -d "+7 days") --max 20

# List today's events
gog calendar events primary --from $(date -I) --to $(date -I -d "+1 day")

# Create event (via AI is easier, but for testing:)
# Use the OpenClaw AI to create events naturally
```

### Calendar Tips

- Use `primary` for your main calendar
- Get calendar ID from Google Calendar settings for secondary calendars
- Events can include:
  - Title
  - Start/end time
  - Location
  - Description
  - Attendees

---

## 3. Google Drive Integration

### What You Can Do

- Search for files
- Read document contents
- List files in folders
- Download files
- Create new documents

### Example Commands to Your AI

```
"Find the Q4 report in my Drive"
"Search for files named 'budget'"
"What documents did I edit last week?"
"Show me files shared with me"
"Find spreadsheets in my Work folder"
```

### CLI Commands (for testing)

```bash
# Search files by name
gog drive search "budget" --max 10

# Search specific file types
gog drive search "name contains 'report' and mimeType contains 'spreadsheet'" --max 10

# List recent files
gog drive search "modifiedTime > '2024-01-01'" --max 20
```

### Drive Search Operators

| Query | Description |
|-------|-------------|
| `name contains 'x'` | Filename contains |
| `fullText contains 'x'` | Content contains |
| `mimeType = 'application/pdf'` | File type |
| `'email' in owners` | Owned by |
| `sharedWithMe` | Shared with you |
| `starred` | Starred files |
| `trashed = false` | Not in trash |

---

## 4. Google Contacts Integration

### What You Can Do

- Search contacts
- Get contact details
- List all contacts

### Example Commands to Your AI

```
"What's John Smith's email?"
"Find Sarah's phone number"
"List my contacts"
"Who do I know at Acme Corp?"
```

### CLI Commands (for testing)

```bash
# List contacts
gog contacts list --max 20

# Search contacts (via AI is easier)
```

---

## 5. Google Sheets Integration

### What You Can Do

- Read spreadsheet data
- Write/update cells
- Append rows
- Create new sheets

### Example Commands to Your AI

```
"Read my expense spreadsheet"
"Add a new row to my budget tracker: Coffee, $4.50, today"
"What's the total in column B of my sales sheet?"
"Update cell A1 in my tracker to 'Completed'"
```

### CLI Commands (for testing)

```bash
# Get spreadsheet data
gog sheets get SPREADSHEET_ID "Sheet1!A1:D10" --json

# Update cells
gog sheets update SPREADSHEET_ID "Sheet1!A1:B2" --values-json '[["A","B"],["1","2"]]' --input USER_ENTERED

# Append row
gog sheets append SPREADSHEET_ID "Sheet1!A:C" --values-json '[["new","row","data"]]' --insert INSERT_ROWS

# Get spreadsheet metadata
gog sheets metadata SPREADSHEET_ID --json
```

### Finding Spreadsheet ID

The spreadsheet ID is in the URL:
```
https://docs.google.com/spreadsheets/d/SPREADSHEET_ID_HERE/edit
```

---

## Part 8: Advanced Configuration

### 8.1: Multiple Google Accounts

You can add multiple accounts:

```bash
# Add work account
gog auth add work@company.com --services gmail,calendar,drive

# Add personal account
gog auth add personal@gmail.com --services gmail,calendar

# List all accounts
gog auth list

# Use specific account
gog gmail search 'test' --account work@company.com
```

### 8.2: Refresh Tokens

If authentication expires:

```bash
# Re-authenticate
gog auth add your.email@gmail.com --services gmail,calendar,drive,contacts,sheets --force
```

### 8.3: Remove Account

```bash
gog auth remove your.email@gmail.com
```

---

## 🔒 Security Considerations

### What OpenClaw Can Access

With this setup, OpenClaw CAN:
- ✅ Read your emails
- ✅ Send emails as you
- ✅ View/modify your calendar
- ✅ Access your Drive files
- ✅ Read your contacts

### Minimize Risk

1. **Use Dedicated Account** (Recommended)
   - Create a new Google account just for OpenClaw
   - Forward important emails to it
   - Share specific calendars with it

2. **Limit Scopes**
   - Only enable services you'll actually use
   - Use `readonly` scopes where possible

3. **Monitor Activity**
   - Check Google Account → Security → Recent activity
   - Review sent emails periodically

4. **Revoke Access If Needed**
   - Google Account → Security → Third-party apps
   - Remove OpenClaw access

### Token Storage

OAuth tokens are stored in:
```
~/.openclaw/credentials/oauth.json
```

Protect this file:
```bash
chmod 600 ~/.openclaw/credentials/oauth.json
```

---

## 🔧 Troubleshooting

### "Access Denied" or "Insufficient Permissions"

1. Check scopes are correct in OAuth consent screen
2. Re-authenticate: `gog auth add email --force`
3. Verify APIs are enabled in Google Cloud Console

### "App Not Verified" Warning

This is normal for personal apps. Click:
1. **Advanced**
2. **Go to OpenClaw Assistant (unsafe)**
3. **Allow**

### "Token Expired"

```bash
# Re-authenticate
gog auth add your.email@gmail.com --services gmail,calendar,drive --force
```

### "Rate Limit Exceeded"

Google APIs have usage limits. Solutions:
- Wait a few minutes
- Reduce request frequency
- Check quotas in Google Cloud Console

### "File Not Found" in Drive

- Check file exists and isn't trashed
- Verify you have access to the file
- Try searching by exact name

### Gmail Not Sending

1. Check `gmail.send` scope is enabled
2. Verify "Less secure app access" isn't blocking (shouldn't be needed with OAuth)
3. Check Google Account security settings

---

## 📊 API Quotas (Free Tier)

| API | Daily Limit | Per-Minute Limit |
|-----|-------------|------------------|
| Gmail | 1 billion quota units | 250 units/user/sec |
| Calendar | 1 million requests | 500 requests/100 sec |
| Drive | 1 billion requests | 1000 requests/100 sec |
| Sheets | 500 requests/100 sec | Per-project limits |

Normal personal use stays well within these limits.

---

## ✅ Verification Checklist

After setup, verify everything works:

```bash
# Check authentication
gog auth list

# Test Gmail
gog gmail search 'newer_than:1d' --max 3

# Test Calendar
gog calendar events primary --from $(date -I) --max 3

# Test Drive
gog drive search "test" --max 3

# Test via OpenClaw
openclaw status
```

Then test via your messaging channel:
- "What's on my calendar today?"
- "Check my recent emails"
- "Search my Drive for documents"

---

*Guide Version: 1.0*
*Last Updated: February 2026*
