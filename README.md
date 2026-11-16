# [🚀观看我的哔哩哔哩视频](https://space.bilibili.com/3493277319825652)

# Claude Code Complete Guide — Skill

A comprehensive Claude Code skill that covers **every feature and configuration option** of Claude Code, distilled from the official documentation at [code.claude.com/docs](https://code.claude.com/docs).

## What is a Claude Code Skill?

Skills are Markdown files that Claude Code automatically loads and follows when relevant tasks arise. Place this skill in:

- `~/.claude/skills/claude-code-guide/SKILL.md` — available in all projects (recommended)
- `.claude/skills/claude-code-guide/SKILL.md` — available in one project only

Claude Code detects when a question or task matches the skill's description and applies its guidance automatically.

## Installation

```bash
# Create skills directory if it doesn't exist
mkdir -p ~/.claude/skills/claude-code-guide

# Download the skill
curl -o ~/.claude/skills/claude-code-guide/SKILL.md \
  https://raw.githubusercontent.com/win4r/claude-code-guide-skill/main/SKILL.md
```

Or clone this repo:

```bash
git clone https://github.com/win4r/claude-code-guide-skill.git
cp claude-code-guide-skill/SKILL.md ~/.claude/skills/claude-code-guide/SKILL.md
```

## What's Covered (38 Sections, 2800+ Lines)

| Category | Topics |
|----------|--------|
| **Getting Started** | Installation, Basic Usage, CLI Reference (all flags) |
| **Core Features** | Interactive Mode, CLAUDE.md, Auto Memory, Settings & Configuration |
| **Integrations** | MCP (Model Context Protocol), Hooks (19 events, 4 handler types) |
| **Extensibility** | Skills, Subagents, Agent Teams (experimental), Plugins |
| **IDE Integration** | VS Code extension, JetBrains extension |
| **Remote & Browser** | Remote Control, Chrome Browser Integration |
| **Models** | All Claude models, aliases, opusplan, effort levels, 1M context |
| **Sessions** | Checkpointing, Rewind, Git Worktrees, Fork Session |
| **Automation** | Headless/Programmatic Use, Scheduled Tasks (`/loop`, CronCreate) |
| **CI/CD** | GitHub Actions, GitLab CI/CD (beta) |
| **Collaboration** | Claude Code in Slack |
| **Customization** | Fast Mode (`/fast`), Status Line, Keyboard Shortcuts, Output Styles |
| **Cloud & Web** | Claude Code on the Web (`--remote`, `--teleport`, cloud sessions) |
| **Desktop App** | Visual Diff, Live Preview, PR Monitoring, Parallel Sessions, SSH |
| **Terminal** | Terminal Configuration, Input Methods, Notifications |
| **Enterprise** | AWS Bedrock, Google Vertex AI, Azure Foundry, Server-Managed Settings |
| **Operations** | Analytics, Cost Management, OpenTelemetry Monitoring |
| **Reference** | Environment Variables, File Locations, Security, Troubleshooting |

## Usage

Once installed, simply ask Claude Code about any Claude Code feature:

```
"How do I set up hooks?"
"What's the difference between skills and agents?"
"How do I use Claude Code with GitHub Actions?"
"How do I customize the status line?"
"What are all the CLI flags?"
```

Claude Code will automatically apply this skill to give you accurate, detailed answers.

## Source

Built from the official Claude Code documentation:
- Source: https://code.claude.com/docs
- Last updated: 2026-03-13
- Coverage: All 61 documentation pages

## License

MIT
