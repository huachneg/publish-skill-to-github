# publish-skill-to-github

[中文说明](README.zh-CN.md)

An Agent skill for safely publishing or syncing local skills to GitHub.

It guides an agent through locating a skill folder, checking required files, scanning for secrets or risky files, preparing public documentation, publishing through the most reliable GitHub path available in the current environment, and verifying the published repository.

## What It Does

- Locates local skill folders across common agent environments.
- Checks the skill structure before publishing.
- Scans for API keys, tokens, private keys, passwords, local-only files, generated artifacts, and large binaries.
- Helps prepare public, agent-neutral README files.
- Supports both creating new GitHub repositories manually and updating existing repositories through GitHub tools or local Git.
- Verifies the published files after upload.

## Requirements

- An AI agent environment that supports local skills.
- GitHub access through one of the following:
  - A connected GitHub app or API tool.
  - A locally authenticated Git installation.
  - A browser session where the user can create or authorize a repository.

No API keys are required by this skill itself.

## Installation

### Option 1: One-line install

```bash
npx skills add https://github.com/huachneg/publish-skill-to-github --skill publish-skill-to-github
```

### Option 2: Ask Your AI Agent

Send this to your agent:

> Help me install the `publish-skill-to-github` Agent skill. Please:
>
> 1. Make sure `~/.claude/skills/` exists, creating it if needed.
> 2. Run `git clone https://github.com/huachneg/publish-skill-to-github.git ~/.claude/skills/publish-skill-to-github`.
> 3. Verify that the folder contains `SKILL.md` and `agents/openai.yaml`.
> 4. Tell me when it is installed.

If your agent uses a different skill directory, replace `~/.claude/skills/` with that agent's configured skill or plugin directory.

### Option 3: Manual Clone

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/huachneg/publish-skill-to-github.git ~/.claude/skills/publish-skill-to-github
```

PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\skills"
git clone https://github.com/huachneg/publish-skill-to-github.git "$HOME\.claude\skills\publish-skill-to-github"
```

## Files

- `SKILL.md`: The skill workflow and publishing guidance.
- `agents/openai.yaml`: OpenAI agent display metadata.

## Safety Notes

Before publishing any skill, this skill instructs the agent to scan for secrets and risky files. If a real credential is found, publishing should stop until the credential is removed and rotated if necessary.
