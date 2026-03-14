---
name: claude-code-guide
description: Comprehensive guide for Claude Code usage, configuration, and features. This skill should be used when users ask questions about Claude Code features (CLI, MCP, hooks, skills, subagents, agent teams, plugins, IDE integration, remote control, permissions, settings, CLAUDE.md, memory, models, scheduled tasks, GitHub Actions, headless mode, checkpointing, worktrees), or need help setting up, configuring, or troubleshooting Claude Code.
---

# Claude Code Complete Guide

Claude Code is an AI-powered agentic coding tool that reads your codebase, edits files, runs commands, and integrates with your development tools. Available as a terminal CLI, VS Code/JetBrains extensions, desktop app, and web.

---

## Installation

```bash
# macOS/Linux
curl -fsSL https://claude.ai/install.sh | bash

# Windows PowerShell
irm https://claude.ai/install.ps1 | iex

# Homebrew
brew install --cask claude-code

# WinGet
winget install Anthropic.ClaudeCode
```

---

## Basic Usage

```bash
cd your-project
claude                    # Start interactive session
claude "task"             # Run task once and stay interactive
claude -p "query"         # Query and exit (non-interactive / headless)
claude -c                 # Continue most recent conversation
claude -r [session-name]  # Resume specific session
```

---

## CLI Reference

### Full Flag List

**Mode & Permissions**
- `--permission-mode plan` — Read-only exploration, Claude cannot modify files
- `--permission-mode acceptEdits` — Auto-approve file edits, ask for commands
- `--permission-mode dontAsk` — Auto-deny permission prompts
- `--permission-mode bypassPermissions` — Skip ALL permission checks (dangerous)
- `--dangerously-skip-permissions` — Skip all permission prompts for this session
- `--allow-dangerously-skip-permissions` — Enable the bypass option in settings
- `-w / --worktree [name]` — Create an isolated git worktree session; auto-names if no name given

**Model & Configuration**
- `--model sonnet|opus|haiku|opusplan|sonnet[1m]|<full-id>` — Choose model
- `--agent agent-name` — Use a specific subagent as the main agent
- `--agents '{"name":{...}}'` — Define custom subagents as JSON for this session
- `--system-prompt "text"` — Replace entire system prompt
- `--append-system-prompt "text"` — Add to default system prompt
- `--add-dir ../other/path` — Add additional working directories
- `--mcp-config ./mcp.json` — Load MCP servers from file
- `--settings ./settings.json` — Load custom settings file
- `--plugin-dir ./my-plugin` — Load a local plugin for testing (repeatable)

**Output & Format**
- `-p / --print` — Non-interactive mode (print output and exit)
- `--output-format text|json|stream-json` — Output format
- `--json-schema '{"type":"object",...}'` — Structured output schema (with `--output-format json`)
- `--include-partial-messages` — Show streaming events
- `--max-turns N` — Limit agentic turns
- `--allowedTools "Bash,Read,Edit"` — Auto-approve specific tools (permission rule syntax)
- `--disallowedTools "Write"` — Deny specific tools

**Session Control**
- `--continue / -c` — Continue most recent session
- `--resume [session-name-or-id]` — Resume a specific session
- `--from-pr 123` — Resume sessions linked to a PR
- `--fork-session` — Fork the current session into a new branch
- `--teammate-mode in-process|tmux` — Agent team display mode

**Remote & Browser**
- `--chrome` — Enable browser integration
- `--remote` — (used via `claude remote-control`) Start remote session

**Advanced**
- `--debug [category]` — Enable debug logging
- `--init` — Run initialization hooks
- `--verbose` — Show detailed output

### Management Commands
```bash
claude update              # Update to latest version
claude --version           # Check version
claude auth login          # Sign in (Anthropic / claude.ai)
claude auth login --bedrock    # Sign in with AWS Bedrock
claude auth login --vertex     # Sign in with Google Vertex AI
claude auth login --foundry    # Sign in with Microsoft Foundry
claude agents              # List subagents
claude mcp list            # List MCP servers
claude mcp add ...         # Add MCP server
claude mcp get name        # Get server details
claude mcp remove name     # Remove server
claude remote-control ["name"] # Start remote control session
claude remote-control --sandbox # Start with filesystem/network isolation
claude remote-control --verbose # Show detailed connection logs
```

---

## Interactive Mode

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `?` | Show all shortcuts |
| `Tab` | Autocomplete (file paths, commands) |
| `↑` / `↓` | Command history |
| `/` | Show all commands/skills |
| `Ctrl+R` | Reverse history search |
| `Shift+Tab` | Cycle permission modes |
| `Ctrl+O` | Toggle verbose mode (see gray italic thinking) |
| `Ctrl+K` | Clear screen |
| `Ctrl+C` | Interrupt current task |
| `Ctrl+B` | Background running task (moves to background) |
| `Ctrl+G` | Open plan in default text editor |
| `Ctrl+T` | Toggle task list (agent teams) |
| `Shift+Down` | Cycle through agent team teammates |
| `Esc` twice | Open rewind menu (undo changes) |
| `Option+T` / `Alt+T` | Toggle extended thinking on/off |
| `Shift+Enter` | Add new line without submitting |

### Built-in Commands (slash commands)
```
/help               Show help
/resume             Resume / pick previous session
/clear              Clear conversation
/checkpoint         Create checkpoint manually
/rewind             Open rewind menu (undo changes)
/memory             View/edit memory files
/init               Create CLAUDE.md for this project
/agents             Manage subagents interactively
/hooks              Configure hooks interactively
/plugins            Manage plugins
/mcp                Configure / authenticate MCP servers
/status             Show session status + account info
/context            Show context window usage
/cost               Show token usage and cost
/rename [name]      Name current session
/model              Change model / adjust effort level
/config             Toggle settings (thinking, remote control, etc.)
/settings           View current settings
/doctor             Diagnose issues
/compact            Compact conversation to free context
/btw                Side question (no tools, answer discarded)
/loop [interval] <prompt>  Schedule recurring prompt (e.g. /loop 5m check build)
/debug [desc]       Troubleshoot current session
/remote-control [name]  Start remote control session from active session
/rc [name]          Alias for /remote-control
/ide                Connect external terminal to VS Code or JetBrains
/mobile             Show QR code to download Claude mobile app
/reload-plugins     Reload plugins without restarting
/usage              Show plan usage (available in VS Code panel too)
/install-github-app Setup GitHub Actions integration
/statusline         Configure status line display
```

### Referencing Files & Resources
```
@src/utils/auth.js          # Include specific file in context
@src/components/            # Directory listing
@github:issue://123         # MCP resource
@database:schema://users    # MCP resource
@terminal:name              # Terminal output (VS Code)
@browser                    # Browser context (Chrome integration)
```

---

## CLAUDE.md — Persistent Instructions

Store instructions at multiple levels (higher specificity = higher priority):

| Level | Path | Scope |
|-------|------|-------|
| Managed | `/Library/Application Support/ClaudeCode/CLAUDE.md` (macOS) / `/etc/claude-code/CLAUDE.md` (Linux) | Organization-wide |
| User | `~/.claude/CLAUDE.md` | All your projects |
| Project | `./CLAUDE.md` or `./.claude/CLAUDE.md` | This project (check into git) |
| Rules | `./.claude/rules/*.md` | Topic-specific rule files |

**Import other files**: use `@filename` syntax to import additional files into CLAUDE.md.

**Auto-load nested**: Claude Code auto-discovers CLAUDE.md files from nested subdirectories when working in them (monorepo support).

**Load from `--add-dir`**: set `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1` to load CLAUDE.md from extra directories.

### What to Include
- Build and test commands
- Coding standards and conventions
- Architectural decisions and patterns
- Naming conventions
- Project structure explanation
- Common workflows
- Forbidden patterns

### Best Practices
- Keep under 200 lines per file
- Use organized sections with headers
- Write specific, actionable instructions
- Use `.claude/rules/` for topic-specific rules
- Run `/init` to auto-generate CLAUDE.md from your codebase

---

## Auto Memory

Claude automatically saves learnings about your project:
- **Location**: `~/.claude/projects/<project>/memory/`
- **Loaded**: First 200 lines of `MEMORY.md` each session
- **Contents**: Build commands, debugging patterns, preferences discovered
- **Disable**: Set `CLAUDE_CODE_DISABLE_AUTO_MEMORY=1`
- **View/edit**: Run `/memory` in interactive mode

---

## Settings & Configuration

### Settings Files Priority (highest → lowest)
1. Managed / policy settings (system-wide, IT-deployed)
2. CLI arguments (current session only)
3. Local (`.claude/settings.local.json`) — gitignored
4. Project (`.claude/settings.json`) — shared via git
5. User (`~/.claude/settings.json`) — all projects

Add JSON schema for autocomplete in VS Code:
```json
{ "$schema": "https://json.schemastore.org/claude-code-settings.json" }
```

### Available Settings (Key Fields)
```json
{
  "model": "claude-sonnet-4-6",
  "availableModels": ["sonnet", "haiku"],
  "language": "english",
  "autoMemoryEnabled": true,
  "cleanupPeriodDays": 30,
  "effortLevel": "medium",
  "alwaysThinkingEnabled": true,
  "defaultPermissionMode": "default",
  "teammateMode": "auto",
  "attribution": { "coAuthoredBy": true },
  "statusLine": "...",
  "enabledPlugins": ["namespace/plugin-name"],
  "extraKnownMarketplaces": { ... },
  "env": {
    "MY_VAR": "value"
  }
}
```

### Permissions Configuration
```json
{
  "permissions": {
    "allow": [
      "Bash(npm run lint)",
      "Bash(npm test *)",
      "Bash(git *)",
      "Read(~/.zshrc)",
      "Edit(src/**)",
      "Skill(commit)",
      "Agent(Explore)"
    ],
    "deny": [
      "Bash(curl *)",
      "Bash(rm -rf *)",
      "Read(.env)",
      "Read(.env.*)",
      "Read(**/.aws/)",
      "Read(**/.ssh/)",
      "Agent(Explore)"
    ],
    "defaultMode": "default"
  }
}
```

### Permission Rule Syntax
- `Bash(npm test)` — exact command match
- `Bash(npm *)` — prefix wildcard (note: space before `*` matters)
- `Read(src/**)` — glob pattern
- `Bash` — shorthand: any bash command
- `WebFetch` — any web fetch
- `Skill(name)` — exact skill match
- `Skill(name *)` — skill with any arguments
- `Agent(subagent-name)` — specific subagent

### Permission Modes
| Mode | Behavior |
|------|---------|
| `default` | Ask for each tool use |
| `acceptEdits` | Auto-approve file changes, ask for commands |
| `plan` | Read-only exploration, no modifications |
| `dontAsk` | Auto-deny permission prompts (allowed rules still work) |
| `bypassPermissions` | Skip ALL checks (dangerous) |

### Hooks Configuration (in settings)
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write $FILE"
          }
        ]
      }
    ]
  }
}
```

### Sandboxing
```json
{
  "sandbox": {
    "enabled": true,
    "filesystem": {
      "allowWrite": ["//tmp", "~/.cache"],
      "denyRead": ["~/.aws", "~/.ssh"]
    },
    "network": {
      "allowedDomains": ["github.com", "api.example.com"],
      "allowLocalBinding": true
    }
  }
}
```

---

## MCP (Model Context Protocol)

Connects Claude Code to external tools, databases, and services.

### Installing MCP Servers
```bash
# HTTP (recommended, remote)
claude mcp add --transport http name https://mcp.example.com/mcp

# Stdio (local process)
claude mcp add --transport stdio name -- npx -y @package/mcp

# SSE (deprecated, remote)
claude mcp add --transport sse name https://sse.example.com

# Specify scope
claude mcp add --scope user ...        # All projects, private
claude mcp add --scope project ...     # This project, shared via .mcp.json
claude mcp add --scope local ...       # This project, private (default)
```

### MCP Scope & Files
| Scope | File | Visibility |
|-------|------|-----------|
| Local (default) | `~/.claude.json` | Current project only, private |
| Project | `.mcp.json` | Shared with team via git |
| User | `~/.claude.json` (user scope) | All projects, private |
| Managed | `managed-mcp.json` (system dir) | IT-controlled, all users |

### Managed MCP Allowlist/Denylist (Enterprise)
```json
{
  "allowedMcpServers": [
    { "serverName": "github" },
    { "serverUrl": "https://mcp.company.com/*" },
    { "serverCommand": ["npx", "-y", "approved-package"] }
  ],
  "deniedMcpServers": [
    { "serverName": "dangerous-tool" }
  ]
}
```

### Popular MCP Servers
- **GitHub**: PRs, issues, code search
- **Sentry**: Error monitoring
- **Slack**: Messaging
- **Jira**: Issue tracking
- **PostgreSQL**: Database queries
- **Notion**: Documents
- **Playwright**: Browser automation
- **Memory**: Persistent knowledge graph

### Using MCP Resources
```
@github:issue://123         # Reference GitHub issue
@postgres:schema://users    # Reference DB schema
```

### MCP Tool Naming Pattern
MCP tools follow: `mcp__<server>__<tool>` (e.g., `mcp__github__search_repositories`)

---

## Hooks — Complete Reference

Hooks are shell commands, HTTP endpoints, LLM prompts, or agents that fire at lifecycle events.

### Hook Locations (Priority)
| Location | Scope | Shareable |
|----------|-------|-----------|
| `~/.claude/settings.json` | All projects | No |
| `.claude/settings.json` | Single project | Yes (git) |
| `.claude/settings.local.json` | Single project | No (gitignored) |
| Plugin `hooks/hooks.json` | When plugin enabled | Yes (bundled) |
| Skill/agent frontmatter | While component active | Yes |
| Managed policy settings | Organization | Yes (admin) |

### All Hook Events

| Event | Fires When | Matcher Field |
|-------|-----------|--------------|
| `SessionStart` | Session begins or resumes | how started: `startup`, `resume`, `clear`, `compact` |
| `UserPromptSubmit` | You submit a prompt | (no matcher) |
| `PreToolUse` | Before a tool executes | tool name |
| `PermissionRequest` | Permission dialog appears | tool name |
| `PostToolUse` | After tool succeeds | tool name |
| `PostToolUseFailure` | After tool fails | tool name |
| `Notification` | Claude sends a notification | type: `permission_prompt`, `idle_prompt`, `auth_success`, `elicitation_dialog` |
| `SubagentStart` | Subagent spawned | agent type name |
| `SubagentStop` | Subagent finishes | agent type name |
| `Stop` | Claude finishes responding | (no matcher) |
| `TeammateIdle` | Agent team teammate goes idle | (no matcher) |
| `TaskCompleted` | Task marked complete | (no matcher) |
| `InstructionsLoaded` | CLAUDE.md or rules file loaded | (no matcher) |
| `ConfigChange` | Config file changes during session | source: `user_settings`, `project_settings`, `local_settings`, `policy_settings`, `skills` |
| `WorktreeCreate` | Worktree being created | (no matcher, replaces default git behavior) |
| `WorktreeRemove` | Worktree being removed | (no matcher) |
| `PreCompact` | Before context compaction | `manual`, `auto` |
| `SessionEnd` | Session terminates | why: `clear`, `logout`, `prompt_input_exit`, `bypass_permissions_disabled`, `other` |

### Matcher Patterns
- `"Bash"` — exact tool name
- `"Edit|Write"` — regex: either Edit or Write
- `"mcp__memory__.*"` — all tools from memory server
- `"mcp__.*__write.*"` — any write tool from any MCP server
- `"*"` or `""` or omit — match all

### Hook Handler Types

**1. Command Hook** (`type: "command"`) — Shell command, receives JSON on stdin
```json
{
  "type": "command",
  "command": "./scripts/validate.sh",
  "async": false,
  "timeout": 600,
  "statusMessage": "Validating...",
  "once": false
}
```

**2. HTTP Hook** (`type: "http"`) — POST to URL, receives JSON as body
```json
{
  "type": "http",
  "url": "http://localhost:8080/hooks/pre-tool-use",
  "headers": { "Authorization": "Bearer $MY_TOKEN" },
  "allowedEnvVars": ["MY_TOKEN"],
  "timeout": 30
}
```

**3. Prompt Hook** (`type: "prompt"`) — Single-turn LLM evaluation (yes/no decision)
```json
{
  "type": "prompt",
  "prompt": "Does this bash command look safe?",
  "timeout": 30
}
```

**4. Agent Hook** (`type: "agent"`) — Spawns subagent with tools to verify conditions
```json
{
  "type": "agent",
  "prompt": "Verify the code passes security standards",
  "timeout": 60
}
```

### Common Fields (all hook types)
| Field | Description |
|-------|-------------|
| `type` | `command`, `http`, `prompt`, or `agent` |
| `timeout` | Seconds before cancel (default: 600 cmd, 30 prompt, 60 agent) |
| `statusMessage` | Custom spinner message while hook runs |
| `once` | If `true`, runs only once per session then removed (skills only) |
| `async` | If `true` (command only), runs in background without blocking |

### Hook Exit Codes
| Code | Behavior |
|------|---------|
| `0` | Allow, proceed normally |
| `2` | Block/deny action (stderr shown to user as error message) |
| Other non-zero | Log error but proceed |

### Hook JSON Output (stdout)
```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "deny",
    "permissionDecisionReason": "Blocked by security policy"
  }
}
```

Or to add context to Claude's view:
```json
{
  "decision": "block",
  "reason": "Reason shown to Claude"
}
```

### Hook Setup
Run `/hooks` in interactive mode to configure via menu, or add to settings JSON directly.

---

## Skills — Complete Reference

Skills extend Claude's capabilities with custom commands and workflows.

### Skill Structure
```
~/.claude/skills/my-skill/       # Personal skill
.claude/skills/my-skill/         # Project skill (check into git)
<plugin>/skills/my-skill/        # Plugin skill
    ├── SKILL.md                 # Main file (required)
    ├── references/              # Detailed docs loaded on demand
    ├── scripts/                 # Executable scripts
    └── examples/                # Example outputs
```

Note: `.claude/commands/deploy.md` and `.claude/skills/deploy/SKILL.md` are equivalent — commands are a simpler form of skills.

### SKILL.md Frontmatter Reference (all fields)

```yaml
---
name: skill-name                    # Directory name if omitted. Max 64 chars, lowercase + hyphens
description: What this skill does and when to use it (third-person). Used by Claude to decide when to auto-load.
argument-hint: "[issue-number]"     # Hint shown in autocomplete
disable-model-invocation: true      # Prevent Claude from auto-triggering. Default: false
user-invocable: false               # Hide from / menu (only Claude invokes). Default: true
allowed-tools: Read, Grep, Bash     # Tools allowed without permission when skill active
model: sonnet                       # Model to use when skill active
context: fork                       # Run in isolated subagent context
agent: Explore                      # Which subagent type for context:fork (default: general-purpose)
hooks:                              # Lifecycle hooks scoped to this skill
  PostToolUse:
    - matcher: "Edit"
      hooks:
        - type: command
          command: "./lint.sh"
---

Your skill instructions here...
```

### String Substitutions in Skills
| Variable | Description |
|---------|-------------|
| `$ARGUMENTS` | All text typed after skill name |
| `$ARGUMENTS[N]` | Nth argument (0-based index) |
| `$N` | Shorthand for `$ARGUMENTS[N]` |
| `${CLAUDE_SESSION_ID}` | Current session ID |
| `${CLAUDE_SKILL_DIR}` | Directory containing this SKILL.md |

### Dynamic Context Injection
Use `` !`command` `` syntax to run shell commands before skill content reaches Claude:
```yaml
---
name: pr-summary
context: fork
agent: Explore
---

PR diff: !`gh pr diff`
PR comments: !`gh pr view --comments`

Summarize this pull request...
```

### Skill Invocation Control
| Frontmatter | You can invoke | Claude can invoke | Context loaded |
|-------------|---------------|------------------|---------------|
| (default) | Yes | Yes | Description always in context |
| `disable-model-invocation: true` | Yes | No | Not in context |
| `user-invocable: false` | No | Yes | Description always in context |

### Skill Locations & Priority
| Location | Path | Priority |
|----------|------|---------|
| Enterprise | Managed deployment | Highest |
| Personal | `~/.claude/skills/<name>/SKILL.md` | All projects |
| Project | `.claude/skills/<name>/SKILL.md` | This project |
| Plugin | `<plugin>/skills/<name>/SKILL.md` | Namespaced |

Plugin skills are namespaced: `/plugin-name:skill-name`

### Using Skills
```
/skill-name                  # Invoke directly
/skill-name arguments        # With arguments
/plugin-name:skill-name      # Plugin skill
```

### Bundled Skills (ship with Claude Code)
- `/simplify` — Review changed files for reuse, quality, efficiency (spawns 3 parallel agents)
- `/batch <instruction>` — Large-scale parallel changes across codebase (5–30 independent units)
- `/debug [description]` — Troubleshoot current session by reading debug log
- `/loop [interval] <prompt>` — Run prompt on schedule (e.g. `/loop 5m check build`)
- `/claude-api` — Load Claude API reference for your language

### Skill Character Budget
Skill descriptions are loaded into context. If you have many skills, use `SLASH_COMMAND_TOOL_CHAR_BUDGET` to override the limit (default: 2% of context window, ~16K chars).

### Controlling Claude's Skill Access
```json
{
  "permissions": {
    "deny": ["Skill"],           // Disable ALL skills
    "allow": ["Skill(commit)"],  // Allow only specific skills
    "deny": ["Skill(deploy *)"]  // Deny skills with arguments
  }
}
```

---

## Subagents — Complete Reference

Specialized AI assistants that handle tasks in their own isolated context window.

### Built-in Subagents
| Agent | Model | Tools | Purpose |
|-------|-------|-------|---------|
| Explore | Haiku (fast) | Read-only | File discovery, code search |
| Plan | Inherits | Read-only | Research during plan mode |
| general-purpose | Inherits | All | Complex multi-step work |
| Bash | Inherits | Bash | Terminal commands in separate context |
| statusline-setup | Sonnet | Read, Edit | Configure status line |
| Claude Code Guide | Haiku | Search, Web | Answer questions about Claude Code |

### Creating Subagents

**File locations** (priority highest → lowest):
1. `--agents` CLI flag (session only)
2. `.claude/agents/` (project, shared via git)
3. `~/.claude/agents/` (user, all projects)
4. Plugin `agents/` directory

**Interactive**: run `/agents` → Create New Agent

**Manual file** (`<location>/agent-name.md` or `agent-name/AGENT.md`):

### AGENT.md Frontmatter — All Fields

```yaml
---
name: agent-name                    # Required. Lowercase + hyphens
description: When Claude should delegate to this agent. Include "use proactively" for automatic delegation.
tools: Read, Grep, Glob, Bash       # Allowlist. Inherits all if omitted
disallowedTools: Write, Edit        # Denylist. Removed from inherited/specified list
model: sonnet                       # sonnet | opus | haiku | inherit | full model ID
permissionMode: default             # default | acceptEdits | dontAsk | bypassPermissions | plan
maxTurns: 20                        # Maximum agentic turns before stopping
skills:                             # Preload skill content into agent at startup
  - api-conventions
  - error-handling-patterns
mcpServers:                         # MCP servers for this agent
  - github                          # String reference to already-configured server
  - playwright:                     # Inline definition
      type: stdio
      command: npx
      args: ["-y", "@playwright/mcp@latest"]
hooks:                              # Hooks scoped to this agent (same format as settings hooks)
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./validate.sh"
memory: user                        # user | project | local — enables persistent cross-session memory
background: false                   # Always run as background task if true
isolation: worktree                 # Run in isolated git worktree
---

Your agent system prompt here...
```

### Restrict Spawnable Subagents
```yaml
# In coordinator agent (only when running as main thread with claude --agent):
tools: Agent(worker, researcher), Read, Bash  # Only these agents can be spawned
# Or:
tools: Agent, Read, Bash  # Any agent can be spawned
# Omitting Agent: subagent cannot spawn others
```

### Subagent Memory Scopes
| Scope | Location | Use When |
|-------|----------|---------|
| `user` | `~/.claude/agent-memory/<name>/` | Knowledge useful across all projects |
| `project` | `.claude/agent-memory/<name>/` | Project-specific, shareable via git |
| `local` | `.claude/agent-memory-local/<name>/` | Project-specific, not in git |

When memory enabled: first 200 lines of `MEMORY.md` injected into agent's context each session.

### Subagent Foreground vs Background
- **Foreground** (default): blocks main conversation, permission prompts passed through
- **Background**: runs concurrently, permissions pre-approved at spawn, cannot ask clarifying questions
- Press `Ctrl+B` to background a running task
- Failed background agents can be resumed in foreground
- Disable all background tasks: `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS=1`

### Disable Specific Subagents
```json
{
  "permissions": {
    "deny": ["Agent(Explore)", "Agent(my-custom-agent)"]
  }
}
```

Or via CLI: `claude --disallowedTools "Agent(Explore)"`

### Resume Subagents
```
Continue that code review and now analyze the authorization logic
[Claude resumes the subagent with full context from previous conversation]
```
Subagent transcripts at: `~/.claude/projects/{project}/{sessionId}/subagents/agent-{agentId}.jsonl`

### CLI-Defined Subagents (Session Only)
```bash
claude --agents '{
  "code-reviewer": {
    "description": "Expert code reviewer. Use proactively after code changes.",
    "prompt": "You are a senior code reviewer...",
    "tools": ["Read", "Grep", "Glob"],
    "model": "sonnet"
  }
}'
```

---

## Agent Teams (Experimental)

Multiple Claude Code instances coordinating with shared tasks and messaging.

**Enable**: add to settings.json or environment:
```json
{ "env": { "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1" } }
```

Requires Claude Code v2.1.32+.

### When to Use Agent Teams vs Subagents
| Feature | Subagents | Agent Teams |
|---------|-----------|-------------|
| Communication | Report back to main only | Teammates message each other directly |
| Coordination | Main agent manages all | Shared task list, self-coordination |
| Context | Results summarized back | Each teammate fully independent |
| Best for | Focused tasks, result matters | Research with discussion, parallel features |
| Token cost | Lower | Higher (each teammate = full Claude instance) |

### Starting an Agent Team
```
Create an agent team with 3 teammates to review this codebase:
one on security, one on performance, one on test coverage.
```
Claude creates the team, spawns teammates, coordinates work.

### Display Modes
```json
{ "teammateMode": "auto" }     // Default: split panes if in tmux, else in-process
{ "teammateMode": "in-process" } // All in main terminal, Shift+Down to cycle
{ "teammateMode": "tmux" }     // Each teammate in own pane (requires tmux or iTerm2)
```
Or per session: `claude --teammate-mode in-process`

### Controlling Teams
- **Cycle teammates**: `Shift+Down` (in-process mode)
- **Message teammate directly**: cycle to them, type message
- **Interrupt teammate**: press `Escape` in their session
- **View task list**: `Ctrl+T`
- **Assign tasks**: tell lead "give the security review task to the researcher teammate"
- **Require plan approval**: "Spawn a teammate to refactor auth. Require plan approval before changes."
- **Shut down teammate**: "Ask the researcher teammate to shut down"
- **Clean up team**: "Clean up the team" (always via lead, not teammates)

### Team Architecture
- **Team config**: `~/.claude/teams/{team-name}/config.json`
- **Task list**: `~/.claude/tasks/{team-name}/`
- Teammates load same project context (CLAUDE.md, MCP, skills) but NOT lead's conversation history
- Tasks have states: pending → in-progress → completed; support dependencies

### Agent Team Hooks
```json
{
  "hooks": {
    "TeammateIdle": [{ "hooks": [{ "type": "command", "command": "./check-quality.sh" }] }],
    "TaskCompleted": [{ "hooks": [{ "type": "command", "command": "./verify-task.sh" }] }]
  }
}
```
Exit code 2 from TeammateIdle/TaskCompleted sends feedback and keeps agent working.

### Limitations
- Experimental — no session resumption with in-process teammates
- Task status can lag, task cleanup can be slow
- One team per lead session; no nested teams
- Split panes require tmux or iTerm2 (not VS Code terminal, Windows Terminal, Ghostty)

---

## Plugins

Plugins package skills, agents, hooks, MCP servers, and LSP servers for distribution.

### Plugin vs Standalone Configuration
| Approach | Skill names | Best for |
|----------|-------------|---------|
| Standalone `.claude/` | `/hello` | Personal, project-specific, quick experiments |
| Plugin | `/plugin-name:hello` | Team sharing, distribution, multiple projects |

### Plugin Structure
```
my-plugin/
├── .claude-plugin/
│   └── plugin.json          # Manifest (required)
├── commands/                # Skills as simple Markdown files
├── skills/                  # Skills with SKILL.md files
├── agents/                  # Custom agent definitions
├── hooks/
│   └── hooks.json           # Hook configuration
├── .mcp.json                # MCP server configurations
├── .lsp.json                # LSP server configurations
└── settings.json            # Default settings when plugin enabled
```

**IMPORTANT**: Only `plugin.json` goes inside `.claude-plugin/`. All other directories are at the plugin root level.

### Plugin Manifest (`plugin.json`)
```json
{
  "name": "my-plugin",
  "description": "What this plugin does",
  "version": "1.0.0",
  "author": { "name": "Your Name" },
  "homepage": "https://example.com",
  "repository": "https://github.com/user/repo",
  "license": "MIT"
}
```

The `name` field is the namespace prefix for all skills (e.g., `/my-plugin:hello`).

### Plugin `settings.json`
```json
{ "agent": "security-reviewer" }
```
Activates a plugin agent as the main thread (only `agent` key currently supported).

### Testing Plugins Locally
```bash
claude --plugin-dir ./my-plugin
claude --plugin-dir ./plugin-one --plugin-dir ./plugin-two  # Multiple plugins
```
Local plugin takes precedence over installed marketplace plugin with same name.
Run `/reload-plugins` after changes (except LSP config changes, which require restart).

### Discovering & Installing Plugins
```
/plugins                              # Open plugin manager
```
**Install scopes**:
- "Install for you" — user scope, all projects
- "Install for this project" — project scope, shared via git
- "Install locally" — local scope, this repo only, private

### Plugin Marketplaces
```json
{
  "extraKnownMarketplaces": {
    "my-marketplace": {
      "source": {
        "source": "github",
        "repo": "my-org/claude-plugins"
      }
    }
  }
}
```

### Submit to Official Marketplace
- Claude.ai: `claude.ai/settings/plugins/submit`
- Console: `platform.claude.com/plugins/submit`

### Converting Standalone Config to Plugin
```bash
mkdir -p my-plugin/.claude-plugin
# Create plugin.json
cp -r .claude/commands my-plugin/
cp -r .claude/agents my-plugin/
cp -r .claude/skills my-plugin/
# Migrate hooks from settings.json to my-plugin/hooks/hooks.json
```

---

## IDE Integration

### VS Code

**Install**: `Cmd+Shift+X` → search "Claude Code" → Install

Or: click [Install for VS Code](vscode:extension/anthropic.claude-code)

Requires VS Code 1.98.0+.

**Open Claude Code panel**:
- Spark icon in Editor Toolbar (top-right, requires open file)
- Activity Bar (left sidebar, always visible)
- Status Bar `✱ Claude Code` (bottom-right, works without open file)
- Command Palette: `Cmd+Shift+P` → "Claude Code"

**VS Code Keyboard Shortcuts**:
| Shortcut | Action |
|----------|--------|
| `Cmd+Esc` / `Ctrl+Esc` | Toggle focus between editor and Claude |
| `Cmd+Shift+Esc` / `Ctrl+Shift+Esc` | Open new conversation as tab |
| `Cmd+N` / `Ctrl+N` | New conversation (Claude must be focused) |
| `Option+K` / `Alt+K` | Insert @-mention of current file + selection |

**VS Code Extension Settings**:
| Setting | Default | Description |
|---------|---------|-------------|
| `selectedModel` | `default` | Model for new conversations |
| `useTerminal` | `false` | Launch Claude in terminal mode |
| `initialPermissionMode` | `default` | Default permission mode |
| `preferredLocation` | `panel` | Where Claude opens |
| `autosave` | `true` | Auto-save files before read/write |
| `useCtrlEnterToSend` | `false` | Use Ctrl+Enter instead of Enter |
| `respectGitIgnore` | `true` | Exclude .gitignore patterns |
| `disableLoginPrompt` | `false` | Skip auth (for third-party providers) |
| `allowDangerouslySkipPermissions` | `false` | Bypass all permission prompts |

**Rewind in VS Code**: hover over any message → rewind button → choose:
- "Fork conversation from here" — new branch, keep code changes
- "Rewind code to here" — revert file changes, keep conversation
- "Fork conversation and rewind code" — both

**Resume remote sessions** (claude.ai web sessions): Past Conversations → Remote tab

**Connect to external terminal**: run `/ide` in external terminal to connect to VS Code

**Share terminal output**: reference with `@terminal:name` in prompts

**Third-party providers**: enable `disableLoginPrompt` in VS Code settings, then configure provider in `~/.claude/settings.json`

### JetBrains IDEs

Supports: IntelliJ IDEA, PyCharm, Android Studio, WebStorm, PhpStorm, GoLand

**Install**: JetBrains Marketplace → search "Claude Code Beta"

**Features**:
- Diff viewing in IDE diff viewer
- Selection context auto-shared with Claude
- Diagnostic errors (lint, syntax) auto-shared
- Quick launch: `Cmd+Esc` (Mac) / `Ctrl+Esc` (Windows/Linux)
- File reference: `Cmd+Option+K` (Mac) / `Alt+Ctrl+K` (Windows/Linux)

**Connect from external terminal**: run `/ide` command

**Plugin Settings** (Settings → Tools → Claude Code [Beta]):
- Custom Claude command path
- Option+Enter for multiline prompts (macOS)
- Automatic plugin updates

**ESC key fix**: Settings → Tools → Terminal → uncheck "Move focus to editor with Escape"

**Remote Development**: install plugin on remote host (Settings → Plugin (Host)), not local machine

**WSL**: set command to `wsl -d Ubuntu -- bash -lic "claude"` in plugin settings

---

## Remote Control

Continue your local Claude Code session from any device (phone, tablet, browser).

Requires Claude Code v2.1.51+ and Pro/Max/Team/Enterprise plan. API keys not supported.

### Starting a Remote Session
```bash
# New session
claude remote-control                    # Start remote control
claude remote-control "My Project"       # With custom name
claude remote-control --sandbox          # With filesystem/network isolation
claude remote-control --verbose          # Detailed connection logs

# From active session
/remote-control                          # Start remote from current session
/remote-control My Project               # With name
/rc                                      # Alias
```

Press spacebar in `claude remote-control` to toggle QR code display.

### Connecting from Another Device
- **URL**: open displayed session URL in any browser → claude.ai/code
- **QR code**: scan with Claude mobile app (iOS/Android)
- **Browse**: go to claude.ai/code → find session by name (shows green dot when online)

### Enable for All Sessions
Run `/config` → set "Enable Remote Control for all sessions" to `true`

### Remote Control vs Claude Code on the Web
| Remote Control | Claude Code on the Web |
|---------------|----------------------|
| Runs on YOUR machine | Runs on Anthropic cloud |
| Local MCP servers, tools, filesystem | No local setup needed |
| Continue in-progress local work | Start fresh task from any device |
| Requires machine to stay on | Works without local machine |

### Security
- Outbound HTTPS only, no inbound ports opened
- Registered via Anthropic API, short-lived credentials
- Session times out after ~10min network outage

---

## Models — Complete Reference

### Model Aliases
| Alias | Behavior |
|-------|---------|
| `default` | Recommended for your account tier |
| `sonnet` | Latest Sonnet 4.6 — daily coding tasks |
| `opus` | Latest Opus 4.6 — complex reasoning |
| `haiku` | Fast, efficient — simple tasks, background |
| `sonnet[1m]` | Sonnet with 1M token context window (beta) |
| `opusplan` | Opus during plan mode, switches to Sonnet for execution |

### Setting Your Model
Priority: `/model` in session > `--model` flag > `ANTHROPIC_MODEL` env var > `settings.json`

```bash
claude --model opus             # Start with Opus
/model sonnet                   # Switch during session
/model sonnet[1m]               # Switch to 1M context version
```

```json
{ "model": "opus" }             # settings.json
```

### Effort Levels (Opus 4.6 & Sonnet 4.6)
Controls adaptive reasoning — how much thinking Claude does:
- `low` — fast, cheaper
- `medium` — default (Opus 4.6 on Max/Team)
- `high` — deeper reasoning, slower

**Set effort**:
- In `/model`: use left/right arrow keys on effort slider
- Env var: `CLAUDE_CODE_EFFORT_LEVEL=low|medium|high`
- Settings: `{ "effortLevel": "medium" }`

**ultrathink**: include "ultrathink" in prompt → sets high effort for that turn only

Current effort shown next to logo: "with low effort"

### Extended Thinking
- `Option+T` / `Alt+T` — toggle thinking on/off
- `Ctrl+O` — toggle verbose mode to see gray italic thinking text
- `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING=1` — revert to fixed thinking budget
- `MAX_THINKING_TOKENS=0` — disable thinking entirely on any model
- `MAX_THINKING_TOKENS=N` — limit budget (ignored on Opus 4.6/Sonnet 4.6 unless 0)

### 1M Token Context Window (Beta)
Available for Opus 4.6 and Sonnet 4.6:
- Standard rates below 200K tokens
- Long-context pricing above 200K tokens
- `CLAUDE_CODE_DISABLE_1M_CONTEXT=1` to disable

### Restrict Available Models (Enterprise)
```json
{ "availableModels": ["sonnet", "haiku"] }
```
The `default` option is always available regardless.

### Model Override for Third-Party Providers
```json
{
  "modelOverrides": {
    "claude-opus-4-6": "arn:aws:bedrock:us-east-2:123456789012:...",
    "claude-sonnet-4-6": "arn:aws:bedrock:us-east-2:123456789012:..."
  }
}
```

### Environment Variables for Model Aliases
| Variable | Description |
|---------|-------------|
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | Model ID for `opus` alias |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | Model ID for `sonnet` alias |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL` | Model ID for `haiku` alias |
| `CLAUDE_CODE_SUBAGENT_MODEL` | Model for subagents |

### Prompt Caching
```bash
DISABLE_PROMPT_CACHING=1         # Disable for all models
DISABLE_PROMPT_CACHING_OPUS=1    # Disable for Opus only
DISABLE_PROMPT_CACHING_SONNET=1  # Disable for Sonnet only
DISABLE_PROMPT_CACHING_HAIKU=1   # Disable for Haiku only
```

---

## Sessions, Checkpointing & Worktrees

### Session Management
```bash
claude --continue              # Most recent session
claude --resume auth-refactor  # By name
claude --resume                # Interactive picker
claude --from-pr 123           # Sessions linked to PR number
/resume                        # Pick from list inside session
/rename auth-refactor          # Name current session
```

**Session picker shortcuts**:
| Key | Action |
|-----|--------|
| `↑` / `↓` | Navigate |
| `→` / `←` | Expand/collapse groups |
| `Enter` | Select & resume |
| `P` | Preview session |
| `R` | Rename session |
| `/` | Search/filter |
| `A` | Toggle: current dir / all projects |
| `B` | Filter by current git branch |
| `Esc` | Exit |

Forked sessions are grouped under their root session.

### Checkpointing

**Automatic**: checkpoint created before every file edit.

**Manual**: `/checkpoint` to save state.

**Rewind menu** (`Esc` twice or `/rewind`):
- "Restore code and conversation" — revert both
- "Restore conversation" — rewind conversation, keep code
- "Restore code" — revert files, keep conversation
- "Summarize from here" — compress messages to free context (keeps original in transcript)
- "Never mind" — cancel

**Limitations**: bash command file modifications NOT tracked (only direct file edits via Claude's tools).

### Git Worktrees (Parallel Sessions)
```bash
claude --worktree feature-auth    # Create named worktree
claude -w feature-auth            # Short form
claude --worktree                 # Auto-generate name

# Manual worktree management:
git worktree add ../feature-a -b feature-a
cd ../feature-a && claude
git worktree remove ../feature-a
```

Worktrees created at `<repo>/.claude/worktrees/<name>/` on branch `worktree-<name>`.

**Cleanup behavior**:
- No changes → worktree and branch removed automatically
- Changes exist → Claude prompts to keep or remove

**Subagent worktrees**: add `isolation: worktree` to agent frontmatter; each subagent gets its own isolated worktree.

**Custom worktree logic**: configure `WorktreeCreate` and `WorktreeRemove` hooks to support non-git VCS.

Add `.claude/worktrees/` to `.gitignore`.

---

## Headless / Programmatic Use

Run Claude Code non-interactively from scripts, CI/CD, or code.

```bash
claude -p "task"                              # Basic headless query
claude -p "task" --output-format json         # Structured output
claude -p "task" --output-format stream-json  # Streaming JSON
claude -p "task" --allowedTools "Bash,Read,Edit"  # Auto-approve tools
claude -p "task" --max-turns 5                # Limit turns
claude -p "task" --json-schema '{"type":"object","properties":{"result":{"type":"string"}}}'
```

**Output formats**:
- `text` (default) — plain text response
- `json` — full JSON: `result`, `session_id`, metadata, `structured_output` (if schema provided)
- `stream-json` — newline-delimited JSON events for real-time streaming

**Continue sessions headlessly**:
```bash
session_id=$(claude -p "Start review" --output-format json | jq -r '.session_id')
claude -p "Focus on security" --resume "$session_id"
claude -p "Continue" --continue
```

**Pipe data**:
```bash
cat error.txt | claude -p "Explain this error" > explanation.txt
gh pr diff "$1" | claude -p "Review for vulnerabilities" --append-system-prompt "You are a security engineer"
```

**Agent SDK** (Python/TypeScript): see `platform.claude.com/docs/en/agent-sdk/overview` for structured outputs, tool approval callbacks, and streaming.

---

## Scheduled Tasks

Run prompts automatically on a schedule within the current session. Session-scoped — tasks stop when Claude Code exits.

Requires Claude Code v2.1.72+.

### /loop Command
```
/loop 5m check if the deployment finished
/loop 30m /review-pr 1234             # Loop over another skill
/loop check the build every 2 hours   # Trailing interval syntax
/loop check the build                  # Default: every 10 minutes
```

**Interval units**: `s` (seconds, rounded to minute), `m` (minutes), `h` (hours), `d` (days)

### One-Time Reminders
```
remind me at 3pm to push the release branch
in 45 minutes, check whether tests passed
```

### Managing Scheduled Tasks
```
what scheduled tasks do I have?
cancel the deploy check job
```

**Underlying tools**:
| Tool | Purpose |
|------|---------|
| `CronCreate` | Schedule task (cron expression, prompt, recurs/once) |
| `CronList` | List all tasks with IDs, schedules, prompts |
| `CronDelete` | Cancel task by ID |

Max 50 tasks per session. Task IDs are 8 characters.

### Cron Expression Reference
Standard 5-field: `minute hour day-of-month month day-of-week`

| Expression | Meaning |
|-----------|---------|
| `*/5 * * * *` | Every 5 minutes |
| `0 * * * *` | Every hour |
| `0 9 * * *` | Daily at 9am local |
| `0 9 * * 1-5` | Weekdays at 9am |
| `30 14 15 3 *` | March 15 at 2:30pm |

**Jitter**: recurring tasks fire up to 10% late (max 15 min) to avoid API spikes. One-shot tasks scheduled for `:00` or `:30` fire up to 90s early.

**Expiry**: recurring tasks auto-expire after 3 days.

**Disable**: `CLAUDE_CODE_DISABLE_CRON=1`

**For durable scheduling** (survives restarts): use Desktop scheduled tasks or GitHub Actions with `schedule:` trigger.

---

## GitHub Actions Integration

```bash
/install-github-app    # Quickstart (inside Claude Code)
```

Manual: install GitHub app at `github.com/apps/claude`, add `ANTHROPIC_API_KEY` secret, copy workflow file.

### Basic Workflow
```yaml
name: Claude Code
on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
jobs:
  claude:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          # Responds to @claude mentions in comments
```

### Action Parameters (v1)
| Parameter | Description |
|-----------|-------------|
| `prompt` | Instructions for Claude |
| `claude_args` | CLI arguments passed to Claude Code |
| `anthropic_api_key` | Claude API key |
| `github_token` | GitHub token (default: GITHUB_TOKEN) |
| `trigger_phrase` | Custom trigger (default: "@claude") |
| `use_bedrock` | Use AWS Bedrock |
| `use_vertex` | Use Google Vertex AI |

```yaml
claude_args: "--max-turns 10 --model claude-sonnet-4-6 --append-system-prompt 'Follow our coding standards'"
```

### Common Usage in Comments
```
@claude implement this feature based on the issue description
@claude fix the TypeError in the dashboard component
@claude review this PR for security issues
```

---

## Common Workflows

### Explore a New Codebase
```
give me an overview of this codebase
explain the main architecture patterns
what are the key data models?
how is authentication handled?
find files that handle user auth
trace the login process front-end to database
```

### Fix Bugs
```
I'm seeing this error: [paste error]
suggest a few ways to fix it
update auth.ts to add the null check you suggested
run the tests and verify the fix
```

### Refactor Code
```
find deprecated API usage in our codebase
suggest how to modernize utils.js
refactor utils.js while maintaining the same behavior
run tests for the refactored code
```

### Write Tests
```
find functions without test coverage in NotificationsService
add unit tests for the notification service
add edge case tests for error conditions
run the new tests and fix any failures
```

### Create Pull Requests
```
summarize the changes I've made
create a pr
enhance the PR description with security context
```

### Plan Mode (Safe Exploration)
```bash
claude --permission-mode plan     # Start in plan mode
# Or: Shift+Tab to cycle to plan mode
```
```
Ctrl+G                            # Open plan in text editor
```
Claude uses `AskUserQuestion` to gather requirements before proposing a plan.

Set as default: `{ "permissions": { "defaultMode": "plan" } }`

### Work with Images
- Drag and drop image into Claude Code window
- Copy image → `Ctrl+V` (not Cmd+V) to paste
- Reference by path: "Analyze this: /path/to/image.png"
- `Cmd+Click` / `Ctrl+Click` on `[Image #1]` references to open image

### Parallel Work with Worktrees
```bash
claude --worktree feature-auth    # First task in isolation
claude --worktree bugfix-123      # Second task in parallel
```

### Non-Interactive / Scripting
```bash
claude -p "task" --output-format json --max-turns 5
cat data.txt | claude -p "summarize" > output.txt

# As a linter in package.json:
"lint:claude": "claude -p 'Review changes vs main for typos. Report filename:line and description only.'"
```

---

## Environment Variables — Complete List

### Authentication
| Variable | Description |
|---------|-------------|
| `ANTHROPIC_API_KEY` | API key for Anthropic Console |
| `ANTHROPIC_MODEL` | Override model alias/name |

### Cloud Providers
| Variable | Description |
|---------|-------------|
| `CLAUDE_CODE_USE_BEDROCK=1` | Use AWS Bedrock |
| `CLAUDE_CODE_USE_VERTEX=1` | Use Google Vertex AI |
| `CLAUDE_CODE_USE_FOUNDRY=1` | Use Microsoft Azure Foundry |
| `ANTHROPIC_VERTEX_PROJECT_ID` | GCP project ID |
| `CLOUD_ML_REGION` | GCP region |

### Model Control
| Variable | Description |
|---------|-------------|
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | Model name for `opus` alias |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | Model name for `sonnet` alias |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL` | Model name for `haiku` alias |
| `CLAUDE_CODE_SUBAGENT_MODEL` | Model for subagents |
| `CLAUDE_CODE_EFFORT_LEVEL` | `low` / `medium` / `high` |
| `MAX_THINKING_TOKENS` | Limit thinking budget (0 = disable) |
| `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING=1` | Revert to fixed thinking budget |
| `CLAUDE_CODE_DISABLE_1M_CONTEXT=1` | Remove 1M context variants |
| `DISABLE_PROMPT_CACHING=1` | Disable prompt caching for all |
| `DISABLE_PROMPT_CACHING_OPUS=1` | Disable for Opus only |
| `DISABLE_PROMPT_CACHING_SONNET=1` | Disable for Sonnet only |
| `DISABLE_PROMPT_CACHING_HAIKU=1` | Disable for Haiku only |

### Behavior
| Variable | Description |
|---------|-------------|
| `CLAUDE_CODE_DISABLE_AUTO_MEMORY=1` | Disable auto memory |
| `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS=1` | No background subagents |
| `CLAUDE_CODE_DISABLE_TELEMETRY=1` | Disable telemetry |
| `CLAUDE_CODE_DISABLE_CRON=1` | Disable scheduled tasks |
| `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` | Enable agent teams (experimental) |
| `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1` | Load CLAUDE.md from `--add-dir` dirs |
| `CLAUDE_CODE_SHELL` | Override shell detection |

### Advanced
| Variable | Description |
|---------|-------------|
| `MAX_MCP_OUTPUT_TOKENS` | Limit MCP tool output (default: 25000) |
| `MCP_TIMEOUT` | MCP server timeout (milliseconds) |
| `ENABLE_TOOL_SEARCH` | MCP tool search behavior |
| `ENABLE_CLAUDEAI_MCP_SERVERS` | Use Claude.ai MCP servers |
| `SLASH_COMMAND_TOOL_CHAR_BUDGET` | Skill description budget override |
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` | Trigger compaction at % (default: 95) |

---

## File Locations — Complete Reference

| Purpose | Path |
|---------|------|
| User settings | `~/.claude/settings.json` |
| Project settings | `.claude/settings.json` |
| Local overrides | `.claude/settings.local.json` |
| Managed/policy settings | `/Library/Application Support/ClaudeCode/settings.json` (macOS) |
| User CLAUDE.md | `~/.claude/CLAUDE.md` |
| Project CLAUDE.md | `./CLAUDE.md` or `./.claude/CLAUDE.md` |
| Project rules | `.claude/rules/*.md` |
| Auto memory | `~/.claude/projects/<project>/memory/` |
| User skills | `~/.claude/skills/<skill>/SKILL.md` |
| Project skills | `.claude/skills/<skill>/SKILL.md` |
| User agents | `~/.claude/agents/<agent>.md` |
| Project agents | `.claude/agents/<agent>.md` |
| Agent memory (user) | `~/.claude/agent-memory/<agent>/` |
| Agent memory (project) | `.claude/agent-memory/<agent>/` |
| Agent memory (local) | `.claude/agent-memory-local/<agent>/` |
| MCP config (local/user) | `~/.claude.json` |
| MCP config (project) | `.mcp.json` |
| Managed MCP | `managed-mcp.json` (system dir) |
| Session transcripts | `~/.claude/projects/<project>/<session-id>/transcript.jsonl` |
| Subagent transcripts | `~/.claude/projects/<project>/<session-id>/subagents/agent-<id>.jsonl` |
| Worktrees | `<repo>/.claude/worktrees/<name>/` |
| Agent team config | `~/.claude/teams/<team-name>/config.json` |
| Agent team tasks | `~/.claude/tasks/<team-name>/` |

---

## Enterprise & Cloud Provider Integration

### AWS Bedrock
```bash
claude auth login --bedrock
claude --model us.anthropic.claude-opus-4-6-v1 ...
```
```json
{ "env": { "CLAUDE_CODE_USE_BEDROCK": "1" } }
```

### Google Vertex AI
```bash
claude auth login --vertex
```
```json
{ "env": { "CLAUDE_CODE_USE_VERTEX": "1", "ANTHROPIC_VERTEX_PROJECT_ID": "my-project" } }
```

### Microsoft Azure Foundry
```bash
claude auth login --foundry
```

### Managed Settings Deployment (IT/Admin)
Deploy `CLAUDE.md`, settings JSON, and managed-mcp.json to system-wide paths to enforce organization policies:
- Model restrictions via `availableModels`
- Mandatory permission rules
- Organization-wide MCP servers
- Hook policies with `allowManagedHooksOnly`

### Server-Managed Settings (Beta)
Centralized policy delivery, versioning, and audit logging via server-side configuration.

### LLM Gateway Configuration
Route Claude Code API calls through your organization's LLM gateway. Configure endpoint and authentication in settings. See `code.claude.com/docs/en/llm-gateway` for details.

---

## Security Best Practices

1. **Protect sensitive files** — Deny access to `.env`, `.aws`, `.ssh`
2. **Use sandboxing** — Enable filesystem/network isolation for untrusted code
3. **Verify MCP servers** — Only add servers from trusted sources; use allowlists
4. **Review before merge** — Don't auto-approve all edits blindly
5. **Use plan mode** — Review approach before execution for risky tasks
6. **Audit hooks** — Use hooks to log configuration changes; `allowManagedHooksOnly` for enterprise
7. **VS Code Restricted Mode** — Enable for untrusted workspaces when using auto-edit permissions
8. **JetBrains security** — Use manual approval mode; IDE config files may be auto-executed
9. **GitHub Actions** — Always use `${{ secrets.ANTHROPIC_API_KEY }}`, never hardcode keys
10. **API key management** — Pin model versions for third-party deployments to prevent silent breaks

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "command not found" | Check PATH; try `which claude`; reinstall |
| Authentication fails | Run `claude auth login`; check token expiry |
| MCP server not connecting | Check transport type, auth, `MCP_TIMEOUT` |
| Hook not firing | Check matcher regex, event type, exit codes |
| High CPU/memory | Check context with `/context`; run `/compact` |
| IDE extension not detecting | Open a file first; check VS Code version (1.98+); restart IDE |
| Spark icon not visible in VS Code | Open a file; Status Bar always works; check Restricted Mode |
| ESC key not working in JetBrains | Settings → Tools → Terminal → uncheck "Move focus to editor with Escape" |
| JetBrains Remote Development | Install plugin on remote host, not local |
| Context window full | Run `/compact`; start new session; use subagents |
| Skill not triggering | Check description keywords; run "What skills are available?"; try direct invocation |
| Too many permission prompts in agent teams | Pre-approve common operations in permissions settings |
| Agent teammates not appearing | Press Shift+Down to cycle; check tmux for split-pane mode |
| Background subagent fails | Resume it in foreground for interactive permissions |
| Scheduled task not firing | Check `CLAUDE_CODE_DISABLE_CRON`; verify Claude Code is idle (not mid-response) |

For deep diagnosis: run `/doctor` in interactive mode.

---

## Chrome Browser Integration (Beta)

Connect Claude Code to Chrome/Edge to automate browser tasks from CLI or VS Code.

**Requirements:** Chrome/Edge browser, Claude in Chrome extension v1.0.36+, Claude Code v2.0.73+, direct Anthropic plan (Pro/Max/Teams/Enterprise). NOT available via Bedrock, Vertex, or Azure.

```bash
claude --chrome          # Start with Chrome integration
# Or enable from within a session:
/chrome                  # Toggle, check status, manage permissions, reconnect
```

### Enable Chrome by Default

Run `/chrome` → "Enabled by default". Note: always-on increases context usage since browser tools are always loaded.

### Capabilities

- **Live debugging**: read console errors, DOM state, fix the code that caused them
- **Design verification**: build from Figma mock, open in browser to verify
- **Web app testing**: form validation, visual regressions, user flows
- **Authenticated web apps**: Google Docs, Gmail, Notion — no API connectors needed
- **Data extraction**: pull structured data from pages, save as CSV
- **Task automation**: data entry, form filling, multi-site workflows
- **Session recording**: record interactions as GIFs

### Example Workflows

```text
# Test local web app
I just updated the login form validation. Can you open localhost:3000,
try submitting the form with invalid data, and check if the error messages appear?

# Debug with console logs
Open the dashboard page and check the console for any errors when the page loads.

# Automate form filling
I have contacts.csv. For each row, go to crm.example.com, click "Add Contact",
fill in the name, email, and phone fields.

# Extract data
Go to the product listings page and extract the name, price, and availability
for each item. Save the results as a CSV file.

# Record a demo GIF
Record a GIF showing how to complete the checkout flow.
```

### Troubleshooting Chrome

| Error | Cause | Fix |
|---|---|---|
| "Browser extension is not connected" | Native messaging host can't reach extension | Restart Chrome and Claude Code, run `/chrome` |
| "Extension not detected" | Extension not installed/disabled | Install/enable in `chrome://extensions` |
| "No tab available" | Claude acted before tab was ready | Ask Claude to create a new tab |
| "Receiving end does not exist" | Extension service worker went idle | Run `/chrome` → "Reconnect extension" |

**Native messaging host config paths:**
- Chrome macOS: `~/Library/Application Support/Google/Chrome/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
- Edge macOS: `~/Library/Application Support/Microsoft Edge/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
- Chrome Linux: `~/.config/google-chrome/NativeMessagingHosts/`

**Connection drops during long sessions**: service worker goes idle. Run `/chrome` → "Reconnect extension".

**JavaScript dialogs (alert/confirm/prompt)** block browser events — dismiss manually then tell Claude to continue.

---

## Fast Mode

Speed up Opus 4.6 responses by 2.5x at higher per-token cost. Toggle with `/fast`.

```bash
/fast          # Toggle fast mode on/off
```

Or set in `~/.claude/settings.json`:
```json
{ "fastMode": true }
```

A `↯` icon appears next to the prompt when active. When disabled, you stay on Opus 4.6 — use `/model` to switch to another model.

### Cost

| Mode | Input (MTok) | Output (MTok) |
|---|---|---|
| Fast mode Opus 4.6 (<200K context) | $30 | $150 |
| Fast mode Opus 4.6 (>200K context) | $60 | $225 |

Fast mode tokens bill to **extra usage** even if plan usage remains — not counted against subscription limits. Switching to fast mode mid-conversation pays full uncached input price for entire context.

### When to Use

- **Use fast mode**: rapid iteration, live debugging, time-sensitive work
- **Use standard mode**: long autonomous tasks, batch processing, CI/CD, cost-sensitive workloads
- **Combine with lower effort level** for maximum speed on simple tasks

### Requirements

- Claude Code v2.1.36+
- Not available on Bedrock, Vertex, or Azure
- Extra usage must be enabled in Console billing settings
- Teams/Enterprise: admin must explicitly enable at Console → Claude Code preferences or Admin Settings → Claude Code

### Per-Session Opt-In (Admin Control)

```json
{ "fastModePerSessionOptIn": true }
```

Forces fast mode to reset each new session. Users can still enable it with `/fast` but it won't persist.

To disable entirely: `CLAUDE_CODE_DISABLE_FAST_MODE=1`

### Rate Limits

Fast mode has separate rate limits. When you hit the limit: fast mode falls back to standard Opus 4.6, `↯` icon turns gray, resumes automatically when cooldown expires. Run `/fast` to disable manually.

---

## Status Line Customization

A customizable bar at the bottom of Claude Code that runs any shell script and displays its output. Receives JSON session data via stdin.

```bash
/statusline show model name and context percentage with a progress bar
# Claude generates the script and updates settings automatically
```

### Manual Setup

Add to `~/.claude/settings.json`:
```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh",
    "padding": 2
  }
}
```

Or inline with `jq`:
```json
{
  "statusLine": {
    "type": "command",
    "command": "jq -r '[\\(.model.display_name)] \\(.context_window.used_percentage // 0)% context'"
  }
}
```

Make script executable: `chmod +x ~/.claude/statusline.sh`

**To remove**: run `/statusline delete` or remove the `statusLine` field from settings.

### Available JSON Fields (stdin)

| Field | Description |
|---|---|
| `model.id`, `model.display_name` | Current model |
| `cwd`, `workspace.current_dir` | Current working directory |
| `workspace.project_dir` | Directory where Claude Code was launched |
| `cost.total_cost_usd` | Total session cost in USD |
| `cost.total_duration_ms` | Total wall-clock time in ms |
| `cost.total_api_duration_ms` | Time waiting for API responses in ms |
| `cost.total_lines_added`, `cost.total_lines_removed` | Lines of code changed |
| `context_window.total_input_tokens`, `context_window.total_output_tokens` | Cumulative token counts |
| `context_window.context_window_size` | Max context size (200000 or 1000000) |
| `context_window.used_percentage` | Pre-calculated % used |
| `context_window.remaining_percentage` | Pre-calculated % remaining |
| `context_window.current_usage` | Token counts from last API call (`input_tokens`, `output_tokens`, `cache_creation_input_tokens`, `cache_read_input_tokens`) |
| `exceeds_200k_tokens` | Whether most recent API response exceeds 200k tokens |
| `session_id` | Unique session identifier |
| `transcript_path` | Path to conversation transcript |
| `version` | Claude Code version |
| `output_style.name` | Current output style name |
| `vim.mode` | `NORMAL` or `INSERT` (when vim mode enabled) |
| `agent.name` | Agent name (when `--agent` flag used) |
| `worktree.name`, `worktree.path`, `worktree.branch`, `worktree.original_cwd`, `worktree.original_branch` | Worktree info (only during `--worktree` sessions) |

**Updates**: runs after each assistant message, permission mode change, or vim mode toggle. Debounced at 300ms. Does NOT consume API tokens.

### Example Scripts

#### Context window progress bar (Bash)
```bash
#!/bin/bash
input=$(cat)
MODEL=$(echo "$input" | jq -r '.model.display_name')
PCT=$(echo "$input" | jq -r '.context_window.used_percentage // 0' | cut -d. -f1)
BAR_WIDTH=10
FILLED=$((PCT * BAR_WIDTH / 100))
EMPTY=$((BAR_WIDTH - FILLED))
BAR=""
[ "$FILLED" -gt 0 ] && BAR=$(printf "%${FILLED}s" | tr ' ' '▓')
[ "$EMPTY" -gt 0 ] && BAR="${BAR}$(printf "%${EMPTY}s" | tr ' ' '░')"
echo "[$MODEL] $BAR $PCT%"
```

#### Git status with ANSI colors (Bash)
```bash
#!/bin/bash
input=$(cat)
MODEL=$(echo "$input" | jq -r '.model.display_name')
DIR=$(echo "$input" | jq -r '.workspace.current_dir')
GREEN='\033[32m'; YELLOW='\033[33m'; RESET='\033[0m'
if git rev-parse --git-dir > /dev/null 2>&1; then
    BRANCH=$(git branch --show-current 2>/dev/null)
    STAGED=$(git diff --cached --numstat 2>/dev/null | wc -l | tr -d ' ')
    MODIFIED=$(git diff --numstat 2>/dev/null | wc -l | tr -d ' ')
    GIT_STATUS=""
    [ "$STAGED" -gt 0 ] && GIT_STATUS="${GREEN}+${STAGED}${RESET}"
    [ "$MODIFIED" -gt 0 ] && GIT_STATUS="${GIT_STATUS}${YELLOW}~${MODIFIED}${RESET}"
    echo -e "[$MODEL] ${DIR##*/} | $BRANCH $GIT_STATUS"
else
    echo "[$MODEL] ${DIR##*/}"
fi
```

#### Multi-line with color-coded context bar
```bash
#!/bin/bash
input=$(cat)
MODEL=$(echo "$input" | jq -r '.model.display_name')
PCT=$(echo "$input" | jq -r '.context_window.used_percentage // 0' | cut -d. -f1)
COST=$(echo "$input" | jq -r '.cost.total_cost_usd // 0')
CYAN='\033[36m'; GREEN='\033[32m'; YELLOW='\033[33m'; RED='\033[31m'; RESET='\033[0m'
if [ "$PCT" -ge 90 ]; then BAR_COLOR="$RED"
elif [ "$PCT" -ge 70 ]; then BAR_COLOR="$YELLOW"
else BAR_COLOR="$GREEN"; fi
FILLED=$((PCT / 10)); EMPTY=$((10 - FILLED))
BAR=$(printf "%${FILLED}s" | tr ' ' '█')$(printf "%${EMPTY}s" | tr ' ' '░')
echo -e "${CYAN}[$MODEL]${RESET}"
echo -e "${BAR_COLOR}${BAR}${RESET} ${PCT}% | ${YELLOW}$(printf '$%.2f' $COST)${RESET}"
```

#### Clickable OSC 8 link (requires iTerm2/Kitty/WezTerm)
```bash
#!/bin/bash
input=$(cat); MODEL=$(echo "$input" | jq -r '.model.display_name')
REMOTE=$(git remote get-url origin 2>/dev/null | sed 's/git@github.com:/https:\/\/github.com\//' | sed 's/\.git$//')
if [ -n "$REMOTE" ]; then
    REPO_NAME=$(basename "$REMOTE")
    printf '%b' "[$MODEL] \e]8;;${REMOTE}\a${REPO_NAME}\e]8;;\a\n"
else
    echo "[$MODEL]"
fi
```

#### Cache slow operations
Use a fixed cache file (NOT process-based IDs — each status run is a new process):
```bash
CACHE_FILE="/tmp/statusline-git-cache"  # Fixed name, reused across runs
CACHE_MAX_AGE=5  # seconds
```

### Windows Configuration

```json
{
  "statusLine": {
    "type": "command",
    "command": "powershell -NoProfile -File C:/Users/username/.claude/statusline.ps1"
  }
}
```

### Troubleshooting Status Line

- Script not appearing: check executable (`chmod +x`), outputs to stdout not stderr, `disableAllHooks` is not true
- Run `claude --debug` to log exit code and stderr from first invocation
- Empty values: fields may be null before first API response; use `// 0` fallbacks
- OSC 8 links not clickable: Terminal.app doesn't support them; iTerm2/Kitty/WezTerm do
- `OTEL_LOG_USER_PROMPTS` or token warnings share the same status line row

---

## Keyboard Shortcut Customization

Requires Claude Code v2.1.18+. Run `/keybindings` to create or open `~/.claude/keybindings.json`.

### Configuration File

```json
{
  "$schema": "https://www.schemastore.org/claude-code-keybindings.json",
  "$docs": "https://code.claude.com/docs/en/keybindings",
  "bindings": [
    {
      "context": "Chat",
      "bindings": {
        "ctrl+e": "chat:externalEditor",
        "ctrl+u": null
      }
    }
  ]
}
```

Set an action to `null` to unbind. Changes apply automatically without restart.

### Contexts

| Context | Description |
|---|---|
| `Global` | Applies everywhere |
| `Chat` | Main chat input area |
| `Autocomplete` | Autocomplete menu open |
| `Settings` | Settings menu |
| `Confirmation` | Permission/confirmation dialogs |
| `Tabs` | Tab navigation |
| `Help` | Help menu visible |
| `Transcript` | Transcript viewer |
| `HistorySearch` | History search (Ctrl+R) |
| `Task` | Background task running |
| `ThemePicker` | Theme picker dialog |
| `Attachments` | Image/attachment bar |
| `Footer` | Footer indicator (tasks, teams, diff) |
| `MessageSelector` | Rewind/summarize dialog |
| `DiffDialog` | Diff viewer |
| `ModelPicker` | Model picker effort level |
| `Select` | Generic select/list |
| `Plugin` | Plugin dialog |

### Key Actions by Context

**Global / App actions:**
- `app:interrupt` (Ctrl+C) — cancel operation
- `app:exit` (Ctrl+D) — exit
- `app:toggleTodos` (Ctrl+T) — toggle task list
- `app:toggleTranscript` (Ctrl+O) — toggle verbose transcript

**History:**
- `history:search` (Ctrl+R), `history:previous` (Up), `history:next` (Down)

**Chat:**
- `chat:submit` (Enter), `chat:cancel` (Escape), `chat:externalEditor` (Ctrl+G)
- `chat:stash` (Ctrl+S), `chat:imagePaste` (Ctrl+V), `chat:modelPicker` (Cmd+P)
- `chat:thinkingToggle` (Cmd+T), `chat:cycleMode` (Shift+Tab), `chat:undo` (Ctrl+_)

**Autocomplete:** `autocomplete:accept` (Tab), `autocomplete:dismiss` (Escape)

**Confirmation:** `confirm:yes` (Y/Enter), `confirm:no` (N/Escape), `confirm:cycleMode` (Shift+Tab), `confirm:toggleExplanation` (Ctrl+E)

**Task:** `task:background` (Ctrl+B)

**Diff:** `diff:dismiss` (Escape), `diff:previousFile` (Up), `diff:nextFile` (Down), `diff:viewDetails` (Enter)

**Select:** `select:next` (Down/J/Ctrl+N), `select:previous` (Up/K/Ctrl+P), `select:accept` (Enter), `select:cancel` (Escape)

**Plugin:** `plugin:toggle` (Space), `plugin:install` (I)

### Keystroke Syntax

```text
ctrl+k          # Single modifier
shift+tab       # Shift + Tab
meta+p          # Cmd/Meta + P
ctrl+shift+c    # Multiple modifiers
ctrl+k ctrl+s   # Chord: press Ctrl+K then Ctrl+S
```

Uppercase letter implies Shift: `K` = `shift+k`. Special keys: `escape`, `enter`, `tab`, `space`, `up`/`down`/`left`/`right`, `backspace`, `delete`.

### Reserved & Conflicting Shortcuts

**Cannot rebind:** Ctrl+C (interrupt/cancel), Ctrl+D (exit)

**Terminal conflicts:** Ctrl+B (tmux prefix), Ctrl+A (GNU screen), Ctrl+Z (SIGTSTP)

**Vim mode interaction:** vim mode handles text input level; keybindings handle component level. They operate independently. Run `/doctor` to see keybinding warnings.

---

## Claude Code on the Web

Run Claude Code asynchronously on Anthropic-managed cloud infrastructure. Available to Pro, Max, Team, Enterprise users with premium seats.

Access at: **claude.ai/code** (connect GitHub account → install Claude GitHub app → select default environment → submit task)

### Starting Web Sessions from Terminal

```bash
# Start a new web session for the current repo
claude --remote "Fix the authentication bug in src/auth/login.ts"

# Plan locally first, then execute remotely
claude --permission-mode plan   # Explore/plan (read-only mode)
claude --remote "Execute the migration plan in docs/migration-plan.md"

# Run tasks in parallel — each creates an independent session
claude --remote "Fix the flaky test in auth.spec.ts"
claude --remote "Update the API documentation"
claude --remote "Refactor the logger to use structured output"

# Monitor all web sessions
/tasks
```

### Teleporting Web Sessions to Terminal

```bash
# Interactive picker of web sessions
claude --teleport
# Or resume specific session
claude --teleport <session-id>
```

Also: run `/teleport` (or `/tp`) from within Claude Code, or use `/tasks` then press `t`.

**Requirements for teleport:**
- Clean git state (no uncommitted changes — teleport prompts to stash if needed)
- Correct repository (not a fork)
- Branch must exist on remote (teleport fetches and checks it out automatically)
- Same Claude.ai account as the web session

### Diff View

When Claude changes files, a diff stats indicator (e.g., `+12 -1`) appears. Click it to open the diff viewer. You can comment on specific lines and iterate before creating a PR.

### Cloud Environment

**Default image includes:** Python 3.x, Node.js LTS, Ruby (3.3.6 default), PHP 8.4, Java with Maven/Gradle, Go, Rust, C++/GCC/Clang, PostgreSQL 16, Redis 7.0

Run `check-tools` to see what's pre-installed.

**Setup scripts** — Bash scripts that run before Claude Code launches, as root on Ubuntu 24.04:

```bash
#!/bin/bash
apt update && apt install -y gh    # Install gh CLI (not in default image)
```

**Environment variables** in `.env` format:
```text
API_KEY=your_api_key
DEBUG=true
```

**Setup scripts vs SessionStart hooks:**

| | Setup scripts | SessionStart hooks |
|---|---|---|
| Attached to | Cloud environment | Your repository |
| Configured in | Cloud environment UI | `.claude/settings.json` |
| Runs | Before Claude Code, new sessions only | After Claude Code, every session |
| Scope | Cloud only | Both local and cloud |

### Network Policy

Default network: **Limited** (allowlisted domains only). Allowlist includes: Anthropic services, GitHub/GitLab/Bitbucket, major container registries (Docker Hub, GCR, GHCR), all major cloud providers (AWS, GCP, Azure, Oracle), all major package managers (npm, PyPI, RubyGems, crates.io, Go proxy, Maven, NuGet, Hex, CocoaPods), Ubuntu repos, HashiCorp, Kubernetes, Anaconda, Sentry, Datadog.

**GitHub operations** go through a secure proxy that uses scoped credentials — pushes restricted to current working branch.

All outbound traffic passes through an HTTP/HTTPS **security proxy** for abuse prevention.

**Custom access**: configure "No internet" or "Full internet" per environment.

### Session Management

- **Archive**: hover over session in sidebar → archive icon (hidden from default list but accessible via filter)
- **Delete**: archive first → hover → delete icon, or open session → dropdown → Delete (permanent, cannot undo)

### Sharing Sessions

- **Teams/Enterprise**: Private or Team visibility. Team shares with org members; repository access verification enabled by default.
- **Pro/Max**: Private or Public. Check for sensitive content (code, credentials) before sharing publicly.

Settings → Claude Code → Sharing settings to enable repository access verification or withhold your name.

### Limitations

- GitHub only (no GitLab/Bitbucket for cloud sessions)
- One-way handoff: can pull web → terminal, but cannot push terminal → web
- bun package manager has known proxy compatibility issues

---

## Desktop App

The Claude Desktop app's Code tab provides a graphical Claude Code interface with additional capabilities.

**Desktop-only features:** Visual diff review, live app preview with dev servers, GitHub PR monitoring, parallel sessions with automatic Git worktree isolation, scheduled tasks, connectors (GitHub/Slack/Linear/Notion), SSH sessions, cloud remote sessions.

### Starting a Session

Configure before sending first message:
- **Environment**: Local / Remote / SSH connection
- **Project folder**: directory or repository Claude works in
- **Model**: from dropdown (locked once session starts)
- **Permission mode**: from mode selector (can change during session)

### Permission Modes in Desktop

| Mode | Settings key | Behavior |
|---|---|---|
| Ask permissions | `default` | Asks before editing files or running commands |
| Auto accept edits | `acceptEdits` | Auto-accepts file edits, asks before terminal commands |
| Plan mode | `plan` | Analyzes and plans without modifying files or running commands |
| Bypass permissions | `bypassPermissions` | No permission prompts (equivalent to `--dangerously-skip-permissions`). Must enable in Settings → Claude Code. Enterprise admins can disable. |

### App Preview (Dev Server)

Claude starts dev servers automatically and opens an embedded browser. From preview panel:
- Interact with running app directly
- Claude auto-verifies changes: takes screenshots, inspects DOM, fills forms, fixes issues
- Start/stop servers from **Preview** dropdown in session toolbar
- **Persist sessions**: cookies and local storage survive server restarts
- Edit `.claude/launch.json` to customize server configuration

**`launch.json` configuration:**
```json
{
  "version": "0.0.1",
  "autoVerify": false,
  "configurations": [
    {
      "name": "my-app",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "port": 3000,
      "cwd": "apps/web",
      "env": { "NODE_ENV": "development" },
      "autoPort": true
    }
  ]
}
```

**Configuration fields:**

| Field | Description |
|---|---|
| `name` | Unique identifier for this server |
| `runtimeExecutable` | Command to run (`npm`, `yarn`, `node`) |
| `runtimeArgs` | Arguments to `runtimeExecutable` (e.g., `["run", "dev"]`) |
| `port` | Port server listens on (default: 3000) |
| `cwd` | Working directory relative to project root |
| `env` | Additional environment variables (don't put secrets here) |
| `autoPort` | `true`: find free port automatically; `false`: fail if port taken; unset: ask |
| `program` | Script to run with `node` directly |
| `args` | Arguments to `program` |

Use `program` + `args` for `node server.js`; use `runtimeExecutable` + `runtimeArgs` for `npm run dev`.

### PR Monitoring

After opening a PR, a CI status bar appears. Uses `gh` CLI to poll check results.
- **Auto-fix**: Claude automatically fixes failing CI checks
- **Auto-merge**: merges PR once all checks pass (squash merge; requires GitHub repo to have auto-merge enabled)

### Parallel Sessions with Git Worktrees

Click **+ New session** to work in parallel. Each session gets its own Git worktree in `<project-root>/.claude/worktrees/` (configurable in Settings → Claude Code → "Worktree location"). Set a branch prefix to keep Claude-created branches organized.

### Scheduled Tasks

Create recurring tasks that fire automatically. Desktop must be open and computer awake.

**To create**: click **Schedule** → **+ New task**, or describe it in any session: "set up a daily code review at 9am".

**Configuration fields:**

| Field | Description |
|---|---|
| Name | Unique identifier (converted to kebab-case, used as folder name) |
| Description | Short summary |
| Prompt | Instructions sent to Claude when task runs |
| Frequency | Manual / Hourly / Daily / Weekdays / Weekly |

**Frequency details:**
- Hourly: fixed offset up to 10 minutes (staggered, deterministic)
- Daily/Weekdays: defaults to 9:00 AM local time
- Custom intervals (every 15 min, monthly): ask Claude in any session
- Task files stored at `~/.claude/scheduled-tasks/<task-name>/SKILL.md`

**Missed runs**: on wake, Desktop checks last 7 days and runs exactly one catch-up for the most recent missed time. Add guardrails in prompts: "Only review today's commits. If it's after 5pm, skip the review."

**Permissions**: each task has its own permission mode. Click **Run now** first time to pre-approve tools with "always allow".

**Manage tasks**: from Schedule list → detail page: Run now, Toggle repeats, Edit, Review history, Review allowed permissions, Delete. Or ask Claude: "pause my dependency-audit task".

### SSH Sessions

Run Claude Code on a remote machine using Desktop as the interface.

**Add SSH connection**: environment dropdown → **+ Add SSH connection**

| Field | Description |
|---|---|
| Name | Friendly label |
| SSH Host | `user@hostname` or host from `~/.ssh/config` |
| SSH Port | Defaults to 22 (or from SSH config) |
| Identity File | Path to private key, e.g., `~/.ssh/id_rsa` |

Claude Code must be installed on the remote machine. SSH sessions support permission modes, connectors, plugins, and MCP servers.

### Continue in Another Surface

**Continue in → Claude Code on the Web**: pushes local session to remote (requires clean working tree, not available for SSH).

**Continue in → Your IDE**: opens project in IDE at current working directory.

**From CLI to Desktop**: run `/desktop` in terminal to move CLI session to Desktop app (macOS/Windows only).

### Desktop vs CLI

| Feature | CLI | Desktop |
|---|---|---|
| Permission modes | All including `dontAsk` | Ask/Auto accept/Plan/Bypass |
| Third-party providers | Bedrock, Vertex, Foundry | Not available |
| MCP servers | Settings files | Connectors UI + settings files |
| Plugins | `/plugin` command | Plugin manager UI |
| File attachments | Not available | Images, PDFs |
| Session isolation | `--worktree` flag | Automatic worktrees |
| Recurring tasks | Cron/CI | Scheduled tasks UI |
| Scripting | `--print`, Agent SDK | Not available |
| Linux | Yes | No (macOS and Windows only) |

### Shared Configuration

Desktop and CLI share: CLAUDE.md files, MCP servers (`~/.claude.json` / `.mcp.json`), hooks, skills, settings (`~/.claude/settings.json`).

**Note**: MCP servers in `claude_desktop_config.json` (chat app) are separate from Claude Code. Configure Claude Code MCP servers in `~/.claude.json` or `.mcp.json`.

### Enterprise Configuration

| Admin Control | Location |
|---|---|
| Enable/disable Code tab | Admin settings console |
| Disable bypass permissions mode | Admin settings console or managed settings: `"disableBypassPermissionsMode": "disable"` |
| Disable Claude Code on the web | Admin settings console |
| MDM (macOS) | `com.anthropic.Claude` preference domain via Jamf/Kandji |
| MDM (Windows) | Registry at `SOFTWARE\Policies\Claude` |

---

## GitLab CI/CD Integration (Beta)

Integrate Claude Code into GitLab CI/CD jobs. Beta — maintained by GitLab. See [GitLab issue #573776](https://gitlab.com/gitlab-org/gitlab/-/issues/573776).

**Use cases:** Instant MR creation, automated implementation from issues, bug fixes, responding to `@claude` comments in issues/MRs/review threads.

### Quick Setup

1. **Add CI/CD variable**: Settings → CI/CD → Variables → add `ANTHROPIC_API_KEY` (masked)
2. **Add to `.gitlab-ci.yml`**:

```yaml
stages:
  - ai

claude:
  stage: ai
  image: node:24-alpine3.21
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
  variables:
    GIT_STRATEGY: fetch
  before_script:
    - apk update
    - apk add --no-cache git curl bash
    - curl -fsSL https://claude.ai/install.sh | bash
  script:
    - /bin/gitlab-mcp-server || true
    - >
      claude
      -p "${AI_FLOW_INPUT:-'Review this MR and implement the requested changes'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
```

### AWS Bedrock (OIDC)

Required CI/CD variables: `AWS_ROLE_TO_ASSUME`, `AWS_REGION`

```yaml
claude-bedrock:
  stage: ai
  image: node:24-alpine3.21
  before_script:
    - apk add --no-cache bash curl jq git python3 py3-pip
    - pip install --no-cache-dir awscli
    - curl -fsSL https://claude.ai/install.sh | bash
    - export AWS_WEB_IDENTITY_TOKEN_FILE="${CI_JOB_JWT_FILE:-/tmp/oidc_token}"
    - if [ -n "${CI_JOB_JWT_V2}" ]; then printf "%s" "$CI_JOB_JWT_V2" > "$AWS_WEB_IDENTITY_TOKEN_FILE"; fi
    - >
      aws sts assume-role-with-web-identity
      --role-arn "$AWS_ROLE_TO_ASSUME"
      --role-session-name "gitlab-claude-$(date +%s)"
      --web-identity-token "file://$AWS_WEB_IDENTITY_TOKEN_FILE"
      --duration-seconds 3600 > /tmp/aws_creds.json
    - export AWS_ACCESS_KEY_ID="$(jq -r .Credentials.AccessKeyId /tmp/aws_creds.json)"
    - export AWS_SECRET_ACCESS_KEY="$(jq -r .Credentials.SecretAccessKey /tmp/aws_creds.json)"
    - export AWS_SESSION_TOKEN="$(jq -r .Credentials.SessionToken /tmp/aws_creds.json)"
  script:
    - /bin/gitlab-mcp-server || true
    - claude -p "${AI_FLOW_INPUT}" --permission-mode acceptEdits --allowedTools "Bash Read Edit Write mcp__gitlab"
  variables:
    AWS_REGION: "us-west-2"
```

### Google Vertex AI (Workload Identity Federation)

Required CI/CD variables: `GCP_WORKLOAD_IDENTITY_PROVIDER`, `GCP_SERVICE_ACCOUNT`, `CLOUD_ML_REGION`

```yaml
claude-vertex:
  stage: ai
  image: gcr.io/google.com/cloudsdktool/google-cloud-cli:slim
  before_script:
    - apt-get update && apt-get install -y git && apt-get clean
    - curl -fsSL https://claude.ai/install.sh | bash
    - >
      gcloud auth login --cred-file=<(cat <<EOF
      { "type": "external_account", "audience": "${GCP_WORKLOAD_IDENTITY_PROVIDER}",
        "subject_token_type": "urn:ietf:params:oauth:token-type:jwt",
        "service_account_impersonation_url": "https://iamcredentials.googleapis.com/v1/projects/-/serviceAccounts/${GCP_SERVICE_ACCOUNT}:generateAccessToken",
        "token_url": "https://sts.googleapis.com/v1/token" }
      EOF)
  script:
    - /bin/gitlab-mcp-server || true
    - CLOUD_ML_REGION="${CLOUD_ML_REGION:-us-east5}" claude -p "${AI_FLOW_INPUT}" --permission-mode acceptEdits --allowedTools "Bash Read Edit Write mcp__gitlab"
  variables:
    CLOUD_ML_REGION: "us-east5"
```

### Security

- Use CI/CD masked variables for `ANTHROPIC_API_KEY` — never commit keys
- Use provider-specific OIDC/WIF where possible
- Each job runs in isolated container with restricted network access
- Claude's changes flow through MRs — reviewers see every diff
- Branch protection and approval rules apply to AI-generated code

### Troubleshooting GitLab

- **Claude not responding to `@claude`**: verify pipeline triggers, check `@claude` not `/claude`, confirm CI/CD variables present
- **Job can't write MRs**: check `CI_JOB_TOKEN` permissions or use Project Access Token with `api` scope; confirm `mcp__gitlab` in `--allowedTools`
- **Auth errors**: verify API key validity; confirm OIDC/WIF configuration for Bedrock/Vertex

---

## Claude Code in Slack

Delegate coding tasks from Slack by mentioning `@Claude`. Routes coding requests to Claude Code on the Web automatically.

### Prerequisites

| Requirement | Details |
|---|---|
| Claude Plan | Pro, Max, Teams, or Enterprise with Claude Code access |
| GitHub Account | Connected to Claude Code on the web |
| Slack Auth | Slack account linked to Claude account via the Claude app |

### Setup

1. **Install Claude app**: workspace admin installs from Slack App Marketplace (A08SF47R6P4)
2. **Connect Claude account**: Claude app → App Home → Connect
3. **Configure Claude Code on the web**: visit claude.ai/code, connect GitHub, authenticate repos
4. **Choose routing mode**:
   - **Code only**: all `@claude` mentions route to Claude Code sessions
   - **Code + Chat**: Claude analyzes each message and routes intelligently (coding → Claude Code, writing/general → Claude Chat). If Claude routes wrong, click "Retry as Code".
5. **Add Claude to channels**: `/invite @Claude` in each channel

**Note**: Claude Code in Slack only works in channels (public or private), NOT in direct messages.

### Session Flow

1. `@mention` Claude with coding request
2. Claude detects coding intent
3. New Claude Code session created on claude.ai/code
4. Claude posts status updates to Slack thread
5. Completion: `@mentions` you with summary + action buttons (View Session, Create PR, Retry as Code, Change Repo)

### Access & Permissions

- Each user runs sessions under their own Claude account
- Sessions count against individual user's plan limits
- Users can only access repositories they've personally connected
- Sessions visible in user's Claude Code history on claude.ai/code
- For Teams/Enterprise: sessions automatically shared with organization visibility

**Channel-based access control**: Claude only responds in channels where it's been invited. Use this to control who can use Claude Code.

### Best Practices

- Include file names, function names, error messages in requests
- Use threads so Claude can gather full context
- Use Slack when context already exists in discussion; use web directly for file uploads or complex tasks

### Limitations

- GitHub only (no GitLab/Bitbucket)
- One PR per session
- Rate limits apply per user's plan
- Users must have Claude Code on the web access

---

## Output Styles

Adapt Claude Code's system prompt for uses beyond software engineering.

### Built-in Styles

| Style | Description |
|---|---|
| **Default** | Standard software engineering assistant |
| **Explanatory** | Adds "Insights" between tasks — explains implementation choices and codebase patterns |
| **Learning** | Collaborative learn-by-doing mode — shares Insights AND asks you to implement small pieces. Adds `TODO(human)` markers in code. |

### Change Output Style

```bash
/config   # Select "Output style" from menu
```

Or directly in settings:
```json
{ "outputStyle": "Explanatory" }
```

Saved to `.claude/settings.local.json` at project level. Changes take effect on next session start (not mid-session, to preserve prompt caching).

### Create Custom Output Style

Save as `~/.claude/output-styles/my-style.md` (user) or `.claude/output-styles/my-style.md` (project):

```markdown
---
name: My Custom Style
description: A brief description shown in /config picker
keep-coding-instructions: false
---

# Custom Style Instructions

You are an interactive CLI tool that helps users with software engineering tasks.
[Your custom instructions here...]

## Specific Behaviors
[Define how the assistant should behave in this style...]
```

**Frontmatter fields:**
- `name`: display name (defaults to file name)
- `description`: shown in `/config` picker
- `keep-coding-instructions`: whether to keep coding-specific parts of default system prompt (default: `false`)

### How Output Styles Work

- Custom styles exclude coding instructions (tests, verification) unless `keep-coding-instructions: true`
- All styles exclude "respond concisely" instructions
- Custom instructions are appended to the system prompt
- Claude is reminded to follow style instructions throughout the conversation

### Comparison to Similar Features

| Feature | Scope | Always Active | Modifies System Prompt |
|---|---|---|---|
| Output styles | Session-wide formatting/tone | Yes (once selected) | Yes (replaces coding section) |
| CLAUDE.md | Project context/rules | Yes | No (added as user message) |
| `--append-system-prompt` | Extra instructions | Yes | No (appended to end) |
| Skills | Task-specific workflows | No (invoked on demand) | No |
| Agents | Specific sub-tasks | No (invoked for tasks) | Model/tools/context |

---

## Terminal Configuration

### Line Breaks

- `\` + Enter: quick newline anywhere
- Shift+Enter: works natively in iTerm2, WezTerm, Ghostty, Kitty
- Other terminals: run `/terminal-setup` to auto-configure VS Code, Alacritty, Zed, Warp
- Option+Enter in Mac Terminal.app: Settings → Profiles → Keyboard → "Use Option as Meta Key"
- Option+Enter in iTerm2/VS Code: Settings → Profiles → Keys → set Left/Right Option to "Esc+"

### Notifications

When Claude finishes and waits for input, a notification event fires.

**Kitty and Ghostty**: desktop notifications work without setup.

**iTerm2**: Settings → Profiles → Terminal → enable "Notification Center Alerts" → "Filter Alerts" → check "Send escape sequence-generated alerts"

**Other terminals** (including macOS Terminal): use notification hooks instead.

**Notification hooks** (run custom logic alongside terminal notifications):
```json
{
  "hooks": {
    "Notification": [{ "type": "command", "command": "your-script.sh" }]
  }
}
```

### Handling Large Inputs

- Avoid direct pasting of very long content — Claude Code may struggle
- Use file-based workflows: write content to a file and ask Claude to read it
- VS Code terminal is particularly prone to truncating long pastes

### Vim Mode

Enable with `/vim` or via `/config`. Supported keybindings:

- **Mode switching**: `Esc` (→NORMAL), `i`/`I`/`a`/`A`/`o`/`O` (→INSERT)
- **Navigation**: `h`/`j`/`k`/`l`, `w`/`e`/`b`, `0`/`$`/`^`, `gg`/`G`, `f`/`F`/`t`/`T` with `;`/`,`
- **Editing**: `x`, `dw`/`de`/`db`/`dd`/`D`, `cw`/`ce`/`cb`/`cc`/`C`, `.` (repeat)
- **Yank/paste**: `yy`/`Y`, `yw`/`ye`/`yb`, `p`/`P`
- **Text objects**: `iw`/`aw`, `iW`/`aW`, `i"`/`a"`, `i'`/`a'`, `i(`/`a(`, `i[`/`a[`, `i{`/`a{`
- **Other**: `>>`/`<<` (indent), `J` (join lines)

---

## Server-Managed Settings

Centrally configure Claude Code for Teams/Enterprise organizations via web interface. Settings delivered to clients at authentication. Requires Claude Code v2.1.38+ (Teams) or v2.1.30+ (Enterprise).

### Setup

1. In Claude.ai: **Admin Settings → Claude Code → Managed settings**
2. Add configuration as JSON (all `settings.json` options supported, including managed-only settings like `disableBypassPermissionsMode`)
3. Save. Clients receive updates on next startup or hourly poll.

```json
{
  "permissions": {
    "deny": [
      "Bash(curl *)",
      "Read(./.env)",
      "Read(./.env.*)",
      "Read(./secrets/**)"
    ]
  },
  "disableBypassPermissionsMode": "disable"
}
```

**Access control**: Primary Owner and Owner roles can manage settings.

### Settings Delivery & Precedence

Server-managed settings take the **highest precedence** — override all other settings including command line args. When both server-managed and endpoint-managed settings are present, server-managed wins.

**Fetch behavior:**
- Fetches at startup and polls hourly
- First launch without cache: fetches asynchronously (brief unenforced window if fetch fails)
- Subsequent launches: cached settings apply immediately, fresh fetch in background
- Settings updates apply automatically without restart (except OTel config — requires full restart)

### Security Approval Dialogs

Settings with security risk require explicit user approval before applying:
- Shell command settings
- Custom environment variables (not in safe allowlist)
- Hook configurations

If user rejects, Claude Code exits. In non-interactive mode (`-p`), dialogs are skipped automatically.

### Limitations

- Settings apply uniformly to all users (no per-group configurations yet)
- MCP server configurations cannot be distributed via server-managed settings
- Requires direct connection to `api.anthropic.com` — not available via Bedrock, Vertex, Azure, or custom `ANTHROPIC_BASE_URL`

### Security Scenarios

| Scenario | Behavior |
|---|---|
| User edits cached settings file | Tampered file applies temporarily; correct settings restore on next server fetch |
| User deletes cached settings file | First-launch behavior; brief unenforced window |
| API unavailable | Cached settings apply; otherwise unenforced until next successful fetch |
| Different organization account | Settings not delivered |
| Non-default `ANTHROPIC_BASE_URL` | Server-managed settings bypassed |

Use `ConfigChange` hooks to detect runtime configuration changes or block unauthorized changes.

For stronger enforcement: use endpoint-managed settings via MDM.

---

## Analytics

Track team usage, adoption, and engineering velocity.

| Plan | Dashboard URL |
|---|---|
| Teams / Enterprise | claude.ai/analytics/claude-code |
| API (Console) | platform.claude.com/claude-code |

**Access**: Admins and Owners can view. Teams/Enterprise dashboard includes usage metrics, contribution metrics (with GitHub integration), leaderboard, and CSV export.

### Enable Contribution Metrics (Teams/Enterprise, Public Beta)

Not available with Zero Data Retention enabled.

1. GitHub admin installs Claude GitHub app at github.com/apps/claude
2. Claude Owner → claude.ai/admin-settings/claude-code → enable "Claude Code analytics"
3. Enable "GitHub analytics" toggle
4. Complete GitHub auth, select GitHub organizations to include

Data appears within 24 hours, with daily updates.

### Summary Metrics

- **PRs with CC**: merged PRs containing at least one Claude Code line
- **Lines of code with CC**: total lines across merged PRs written with Claude Code. "Effective lines" only: >3 chars, excluding empty lines, brackets, trivial punctuation
- **PRs with CC (%)**: percentage of all merged PRs with CC-assisted code
- **Suggestion accept rate**: % of Edit/Write/NotebookEdit tool usages accepted
- **Lines accepted**: total lines accepted (excludes rejected; doesn't track subsequent deletions)

**Note**: metrics are deliberately conservative — underestimates of actual impact.

### PR Attribution

Merged PRs labeled `claude-code-assisted` in GitHub if they contain CC-assisted lines.

**Attribution process**: extract added lines from PR diff → find CC sessions that edited matching files within time window → match PR lines to CC output using multiple strategies.

**Time window**: sessions from 21 days before to 2 days after PR merge date.

**Excluded**: lock files, generated code, build dirs (dist/, build/, node_modules/), test fixtures, lines >1000 chars, code with >20% difference (substantially rewritten by developers).

### API/Console Analytics

Available at platform.claude.com/claude-code (requires UsageView permission):
- Lines accepted, suggestion accept rate, daily active users/sessions (chart)
- **Spend**: daily API costs + user count
- **Team insights**: per-user spend and lines this month

**Note**: Console spend figures are estimates — use billing page for actual costs.

---

## Cost Management

Average: ~$6/developer/day; 90% of users stay under $12/day. Team average: ~$100-200/developer/month with Sonnet 4.6.

### Track Costs

```bash
/cost      # API token usage for current session (API users)
/stats     # Usage patterns (Max/Pro subscribers)
```

Sample `/cost` output:
```
Total cost:            $0.55
Total duration (API):  6m 19.7s
Total duration (wall): 6h 33m 10.2s
Total code changes:    0 lines added, 0 lines removed
```

A "Claude Code" workspace is auto-created in Console for centralized tracking. Cannot create API keys for this workspace.

### Team Rate Limit Recommendations

| Team size | TPM/user | RPM/user |
|---|---|---|
| 1-5 users | 200k-300k | 5-7 |
| 5-20 users | 100k-150k | 2.5-3.5 |
| 20-50 users | 50k-75k | 1.25-1.75 |
| 50-100 users | 25k-35k | 0.62-0.87 |
| 100-500 users | 15k-20k | 0.37-0.47 |
| 500+ users | 10k-15k | 0.25-0.35 |

Example: 200 users × 20k TPM = 4M total TPM.

### Reduce Token Usage

**Context management:**
- `/clear` between unrelated tasks (use `/rename` first, then `/resume` later)
- `/compact Focus on code samples and API usage` — custom compaction instructions
- In CLAUDE.md: `# Compact instructions\nWhen compacting, focus on test output and code changes`
- Keep CLAUDE.md under ~500 lines (it loads on every session)

**Model selection:**
- Sonnet for most tasks (cheaper than Opus)
- `/model` to switch mid-session; specify `model: haiku` in subagent configs

**MCP overhead reduction:**
- `/context` to see what's consuming space
- Prefer CLI tools (gh, aws, gcloud, sentry-cli) over MCP servers — no persistent tool definitions
- `/mcp` to disable unused servers
- Auto tool search: when MCP tool descriptions exceed 10% of context, Claude defers them. Set lower threshold: `ENABLE_TOOL_SEARCH=auto:5`

**Other techniques:**
- Install code intelligence plugins for typed languages (precise symbol nav vs text search)
- Use PreToolUse hooks to preprocess data before Claude sees it (e.g., filter test output to failures only):

```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{"type": "command", "command": "~/.claude/hooks/filter-test-output.sh"}]
    }]
  }
}
```

- Move detailed CLAUDE.md workflow instructions to skills (skills load on-demand)
- Lower extended thinking budget: `MAX_THINKING_TOKENS=8000` (default: 31,999)
- Delegate verbose operations (test runs, doc fetching, log processing) to subagents

**Agent team costs:**
- ~7x more tokens than standard sessions (each teammate has own context window)
- Use Sonnet for teammates, keep teams small, keep spawn prompts focused
- Clean up teams when done — active teammates consume tokens even idle
- Set `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` to enable

**Write specific prompts**: "add input validation to login function in auth.ts" vs "improve this codebase"

**Course-correct early**: press Escape immediately when Claude heads wrong. Use `/rewind` or double-tap Escape to restore to checkpoint.

### Background Token Usage

Even idle, Claude Code uses tokens for: conversation summarization (for `--resume`), some commands like `/cost`. Typically under $0.04/session.

---

## Monitoring & OpenTelemetry

Export telemetry data (metrics + events) via OpenTelemetry. Requires `CLAUDE_CODE_ENABLE_TELEMETRY=1`.

### Quick Start

```bash
export CLAUDE_CODE_ENABLE_TELEMETRY=1
export OTEL_METRICS_EXPORTER=otlp       # otlp, prometheus, console
export OTEL_LOGS_EXPORTER=otlp          # otlp, console
export OTEL_EXPORTER_OTLP_PROTOCOL=grpc
export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317
export OTEL_EXPORTER_OTLP_HEADERS="Authorization=Bearer your-token"
export OTEL_METRIC_EXPORT_INTERVAL=10000  # default 60000ms
export OTEL_LOGS_EXPORT_INTERVAL=5000     # default 5000ms
claude
```

### Admin Configuration (Managed Settings)

```json
{
  "env": {
    "CLAUDE_CODE_ENABLE_TELEMETRY": "1",
    "OTEL_METRICS_EXPORTER": "otlp",
    "OTEL_LOGS_EXPORTER": "otlp",
    "OTEL_EXPORTER_OTLP_PROTOCOL": "grpc",
    "OTEL_EXPORTER_OTLP_ENDPOINT": "http://collector.example.com:4317",
    "OTEL_EXPORTER_OTLP_HEADERS": "Authorization=Bearer example-token"
  }
}
```

### Configuration Variables

| Variable | Description | Example |
|---|---|---|
| `CLAUDE_CODE_ENABLE_TELEMETRY` | Enable telemetry (required) | `1` |
| `OTEL_METRICS_EXPORTER` | Metrics exporters (comma-separated) | `console`, `otlp`, `prometheus` |
| `OTEL_LOGS_EXPORTER` | Logs/events exporters | `console`, `otlp` |
| `OTEL_EXPORTER_OTLP_PROTOCOL` | OTLP protocol | `grpc`, `http/json`, `http/protobuf` |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | OTLP collector endpoint | `http://localhost:4317` |
| `OTEL_EXPORTER_OTLP_METRICS_ENDPOINT` | Override endpoint for metrics only | `http://metrics:4318/v1/metrics` |
| `OTEL_EXPORTER_OTLP_LOGS_ENDPOINT` | Override endpoint for logs only | `http://logs:4317` |
| `OTEL_EXPORTER_OTLP_HEADERS` | Auth headers | `Authorization=Bearer token` |
| `OTEL_METRIC_EXPORT_INTERVAL` | Metrics export interval ms (default: 60000) | `5000` |
| `OTEL_LOGS_EXPORT_INTERVAL` | Logs export interval ms (default: 5000) | `1000` |
| `OTEL_LOG_USER_PROMPTS` | Log user prompt content (default: disabled) | `1` |
| `OTEL_LOG_TOOL_DETAILS` | Log MCP server/tool names and skill names (default: disabled) | `1` |
| `OTEL_METRICS_INCLUDE_SESSION_ID` | Include session.id in metrics (default: true) | `false` |
| `OTEL_METRICS_INCLUDE_VERSION` | Include app.version in metrics (default: false) | `true` |
| `OTEL_METRICS_INCLUDE_ACCOUNT_UUID` | Include user.account_uuid (default: true) | `false` |
| `OTEL_EXPORTER_OTLP_METRICS_TEMPORALITY_PREFERENCE` | Temporality (default: `delta`) | `cumulative` |
| `CLAUDE_CODE_OTEL_HEADERS_HELPER_DEBOUNCE_MS` | Dynamic headers refresh interval (default: 1740000ms/29min) | `900000` |

**Multi-team support:**
```bash
export OTEL_RESOURCE_ATTRIBUTES="department=engineering,team.id=platform,cost_center=eng-123"
# No spaces in values; comma-separated key=value; US-ASCII only
```

**Dynamic headers** (for enterprise rotating credentials):
```json
{ "otelHeadersHelper": "/bin/generate_opentelemetry_headers.sh" }
```
Script must output valid JSON string key-value pairs: `{"Authorization": "Bearer $(get-token.sh)"}`.

### Available Metrics

| Metric | Description | Unit | Extra Attributes |
|---|---|---|---|
| `claude_code.session.count` | CLI sessions started | count | — |
| `claude_code.lines_of_code.count` | Lines modified | count | `type` (added/removed) |
| `claude_code.pull_request.count` | PRs created | count | — |
| `claude_code.commit.count` | Git commits created | count | — |
| `claude_code.cost.usage` | Session cost | USD | `model` |
| `claude_code.token.usage` | Tokens used | tokens | `type` (input/output/cacheRead/cacheCreation), `model` |
| `claude_code.code_edit_tool.decision` | Code edit tool decisions | count | `tool_name`, `decision` (accept/reject), `source`, `language` |
| `claude_code.active_time.total` | Active time | seconds | `type` (user/cli) |

### Available Events (Logs)

| Event | When Logged | Key Attributes |
|---|---|---|
| `claude_code.user_prompt` | User submits prompt | `prompt_length`, `prompt` (if `OTEL_LOG_USER_PROMPTS=1`) |
| `claude_code.tool_result` | Tool completes | `tool_name`, `success`, `duration_ms`, `error`, `decision_type`, `tool_result_size_bytes`, bash cmd details |
| `claude_code.api_request` | API request to Claude | `model`, `cost_usd`, `duration_ms`, `input_tokens`, `output_tokens`, `cache_read_tokens`, `cache_creation_tokens`, `speed` |
| `claude_code.api_error` | API request fails | `model`, `error`, `status_code`, `duration_ms`, `attempt`, `speed` |
| `claude_code.tool_decision` | Tool permission decided | `tool_name`, `decision`, `source` |

All metrics/events include: `session.id`, `user.id`, `user.email` (OAuth), `user.account_uuid`, `organization.id`, `app.version`, `terminal.type`.

Event correlation: `prompt.id` links all events from a single user prompt (excluded from metrics — too high cardinality).

### Service Info

All exports have: `service.name: claude-code`, `service.version`, `os.type`, `os.version`, `host.arch`, `wsl.version` (WSL only). Meter name: `com.anthropic.claude_code`.

### Example Configurations

```bash
# Console debugging
export CLAUDE_CODE_ENABLE_TELEMETRY=1
export OTEL_METRICS_EXPORTER=console
export OTEL_METRIC_EXPORT_INTERVAL=1000

# Prometheus
export CLAUDE_CODE_ENABLE_TELEMETRY=1
export OTEL_METRICS_EXPORTER=prometheus

# Separate backends for metrics and logs
export CLAUDE_CODE_ENABLE_TELEMETRY=1
export OTEL_METRICS_EXPORTER=otlp
export OTEL_LOGS_EXPORTER=otlp
export OTEL_EXPORTER_OTLP_METRICS_PROTOCOL=http/protobuf
export OTEL_EXPORTER_OTLP_METRICS_ENDPOINT=http://metrics.example.com:4318
export OTEL_EXPORTER_OTLP_LOGS_PROTOCOL=grpc
export OTEL_EXPORTER_OTLP_LOGS_ENDPOINT=http://logs.example.com:4317
```

### Backend Recommendations

- **Metrics**: Prometheus (rates/aggregations), ClickHouse (complex queries), Honeycomb/Datadog (visualization/alerting)
- **Events/Logs**: Elasticsearch/Loki (full-text search), ClickHouse (structured analysis), Honeycomb/Datadog (correlation)

### Privacy & Security

- Telemetry is **opt-in** — requires explicit configuration
- Raw file contents and code snippets NOT included in metrics/events
- Tool events include bash commands and file paths in `tool_parameters` — may contain secrets. Filter/redact in your telemetry backend if needed.
- User prompt content NOT collected by default (only length). Enable with `OTEL_LOG_USER_PROMPTS=1`
- MCP server/tool names NOT logged by default. Enable with `OTEL_LOG_TOOL_DETAILS=1`

For Amazon Bedrock monitoring: [Claude Code Monitoring Implementation (Bedrock)](https://github.com/aws-solutions-library-samples/guidance-for-claude-code-with-amazon-bedrock/blob/main/assets/docs/MONITORING.md)

For ROI measurement guide: [Claude Code ROI Measurement Guide](https://github.com/anthropics/claude-code-monitoring-guide)
