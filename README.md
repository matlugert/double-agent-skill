# double-agent

Two AI coding CLIs. One terminal. Whichever agent runs your session calls in the other for an independent second opinion. Both agents work for you.

| Your session | Calls in | Command |
|---|---|---|
| Claude Code | OpenAI Codex CLI | `/second-opinion` |
| Codex CLI | Claude Code CLI | `/second-opinion` or "get a second opinion" |

## Why two agents?

AI models have correlated blind spots — same training approach, similar failure modes. A second agent from a *different model family* has uncorrelated blind spots: what one misses, the other catches.

After the adversary returns its analysis, your session agent synthesizes a **Cross-Model Delta** — where both agents agree (high confidence), where they disagree (genuine trade-offs), and what gaps remain.

## What it does

| Mode | Trigger | Example |
|---|---|---|
| **Git review** | No arguments | `/second-opinion` |
| **Code review** | File or directory path | `/second-opinion src/auth/` |
| **Idea feedback** | A question or description | `/second-opinion Should I use JWT or sessions?` |

## Prerequisites

You need both CLIs installed. You only pay for the one you're calling — your session agent uses your existing subscription.

| Tool | Install | Auth |
|---|---|---|
| [Claude Code](https://docs.anthropic.com/en/docs/claude-code) | `npm install -g @anthropic-ai/claude-code` | `claude` (follow prompts) |
| [Codex CLI](https://github.com/openai/codex) | `npm install -g @openai/codex` | `codex login` |

## Installation

### For Claude Code users (Codex as adversary)

Copy the command file to your Claude Code commands directory:

**macOS / Linux:**

```bash
mkdir -p ~/.claude/commands
cp claude-code/commands/second-opinion.md ~/.claude/commands/
```

**Windows (PowerShell):**

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\commands"
Copy-Item claude-code\commands\second-opinion.md "$env:USERPROFILE\.claude\commands\"
```

Then restart Claude Code. The `/second-opinion` command will be available.

### For Codex CLI users (Claude as adversary)

Append the instructions to your global `AGENTS.md`:

**macOS / Linux:**

```bash
cat codex-cli/second-opinion.md >> ~/.codex/AGENTS.md
```

**Windows (PowerShell):**

```powershell
Get-Content codex-cli\second-opinion.md | Add-Content "$env:USERPROFILE\.codex\AGENTS.md"
```

Then restart Codex CLI. Ask for a "second opinion" or type `/second-opinion` and Codex will call Claude.

### Quick install (both directions)

```bash
git clone https://github.com/matlugert/double-agent.git
cd double-agent

# Claude Code side:
mkdir -p ~/.claude/commands
cp claude-code/commands/second-opinion.md ~/.claude/commands/

# Codex CLI side:
mkdir -p ~/.codex
cat codex-cli/second-opinion.md >> ~/.codex/AGENTS.md
```

## Usage

### From Claude Code

```
/second-opinion                          # Review uncommitted git changes
/second-opinion src/utils/               # Review a specific directory
/second-opinion src/auth/login.ts        # Review a specific file
/second-opinion Should we use Redis?     # Get architecture feedback
```

### From Codex CLI

```
> get a second opinion on my changes
> second opinion on src/auth/
> ask claude if we should use JWT or sessions
```

### Example output (from either direction)

```
## [Adversary] Second Opinion

[The other agent's full analysis appears here]

## Cross-Model Delta

**Agree:** Both agents flag the SQL injection risk in `buildQuery()`.
**Disagree:** Codex suggests extracting a base class; Claude thinks composition
is simpler here since there's only one shared method.
**Gaps:** Neither agent checked the error handling path when the DB
connection drops mid-transaction.
```

## How it works

```
┌─────────────────┐         ┌─────────────────┐
│  Your session    │  calls  │  Adversary       │
│  (Claude Code    │ ──────► │  (Codex CLI      │
│   or Codex CLI)  │         │   or Claude CLI) │
│                  │ ◄────── │                  │
│  Synthesizes     │  returns│  Independent     │
│  Cross-Model     │  review │  analysis        │
│  Delta           │         │                  │
└─────────────────┘         └─────────────────┘
```

1. You ask for a second opinion (with optional arguments)
2. Your session agent determines the mode (git review, code review, or idea feedback)
3. Your agent invokes the adversary CLI in non-interactive/ephemeral mode
4. The adversary analyzes the code independently
5. Your agent presents the adversary's findings alongside its own **Cross-Model Delta**
6. If both agents agree on actionable issues, your agent offers to fix them

## Customization

Both command files are plain markdown. You can:

- Change the default review prompt
- Add project-specific focus areas
- Adjust the Cross-Model Delta format
- Add additional modes for your workflow

## Terms of service note

This tool has one AI coding CLI invoke the other and process its output at runtime. Before using, be aware of the relevant terms:

- **Anthropic / Claude Code:** No restriction on invoking external CLI tools — that's what Bash commands and tool permissions are for. Anthropic's restrictions concern third-party tools accessing Claude's OAuth tokens, not the other direction.
- **OpenAI / Codex CLI:** The CLI itself is [Apache 2.0 open source](https://github.com/openai/codex). OpenAI's [Services Agreement](https://openai.com/policies/services-agreement/) prohibits using API output "to develop AI models that compete with OpenAI." Whether a competing AI processing Codex output at runtime (not for training) falls under this clause is ambiguous.
- **Both directions apply.** The same ambiguity exists symmetrically — each provider could argue the other's output shouldn't be processed by a competitor at runtime.

**In short:** This is a developer productivity tool, not model training. You're automating what any developer does when they compare answers from two AI tools. But the terms are broadly worded — read them yourself and make your own judgment. This section is informational, not legal advice.

## License

MIT
