# Contributing to OpenClaw Setup Guides

Thanks for your interest in improving these guides. Contributions of all kinds are welcome — from fixing typos to adding entirely new setup guides.

---

## How to Contribute

### 1. Fork the Repository

Click the **Fork** button at the top right of the repo page to create your own copy.

### 2. Clone Your Fork

```bash
git clone https://github.com/YOUR_USERNAME/openclaw-setup.git
cd openclaw-setup
```

### 3. Create a Branch

```bash
git checkout -b fix/your-change-description
```

Use a descriptive branch name, for example:
- `fix/typo-in-vps-guide`
- `add/docker-setup-guide`
- `update/aws-pricing-info`

### 4. Make Your Changes

Edit or add markdown files as needed. See the style guide below.

### 5. Commit and Push

```bash
git add .
git commit -m "Brief description of your change"
git push origin fix/your-change-description
```

### 6. Open a Pull Request

Go to the original repo and click **New Pull Request**. Describe what you changed and why.

---

## What You Can Contribute

### Bug Fixes & Corrections
- Fix typos, broken links, or incorrect commands
- Update outdated version numbers or pricing
- Correct platform-specific instructions

### Improvements
- Clarify confusing steps
- Add missing troubleshooting entries
- Improve formatting or readability

### New Content
- Add setup guides for new platforms (Docker, Raspberry Pi, Windows WSL, etc.)
- Add guides for new messaging channels or integrations
- Add new skill setup walkthroughs

### Translations
- Translate guides into other languages
- Place translations in a `translations/` folder (e.g., `translations/es/01-LOCAL-MAC-SETUP.md`)

---

## Style Guide

To keep the guides consistent, please follow these conventions:

### File Naming
- Use numbered prefixes: `XX-DESCRIPTIVE-NAME.md`
- Use uppercase with hyphens for filenames
- Example: `11-DOCKER-SETUP.md`

### Markdown Formatting
- Use `#` for the main title (one per file)
- Use `##` for major sections, `###` for subsections
- Use fenced code blocks with language hints (` ```bash `, ` ```json `, etc.)
- Use tables for comparisons and reference data
- Use checklists (`- [ ]`) for prerequisite and verification lists

### Code Blocks
- Always specify the language for syntax highlighting
- Include comments explaining non-obvious commands
- Show expected output where helpful

### Content Guidelines
- Write for beginners — assume minimal prior knowledge
- Include "what happens" explanations after complex commands
- Provide troubleshooting tips for steps that commonly fail
- Always mention security implications when relevant
- Never include real API keys, tokens, or credentials in examples

---

## Reporting Issues

If you find a problem but don't want to fix it yourself, open an [Issue](../../issues) with:

1. Which guide the issue is in
2. What the problem is (incorrect command, missing step, etc.)
3. Your platform and setup type (if relevant)

---

## Code of Conduct

- Be respectful and constructive
- Focus on the content, not the contributor
- Assume good intent
- Help newcomers feel welcome

---

## License

By contributing, you agree that your contributions will be licensed under the [MIT License](LICENSE).
