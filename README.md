# /second-opinion — Cross-Model Code Review for Claude Code

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) slash command that gets a second opinion from [OpenAI Codex CLI](https://github.com/openai/codex). Two AI models from different families reviewing the same code catches more issues than either alone.

## Why?

AI models have correlated blind spots — the same training approach produces similar failure modes. A review from a *different* model family has uncorrelated blind spots, so issues one misses, the other catches.

After Codex returns its analysis, Claude synthesizes a **Cross-Model Delta** — where both models agree (high confidence), where they disagree (genuine trade-offs), and what gaps remain.

## What it does

| Mode | Trigger | Example |
|------|---------|---------|
| **Git review** | No arguments | `/second-opinion` |
| **Code review** | File or directory path | `/second-opinion src/auth/` |
| **Idea feedback** | A question or description | `/second-opinion Should I use JWT or sessions?` |

## Prerequisites

1. [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed and working
2. [OpenAI Codex CLI](https://github.com/openai/codex) installed and authenticated:
   ```bash
   npm install -g @openai/codex
   codex login
   ```

## Installation

Copy the command file to your Claude Code commands directory:

**macOS / Linux:**

```bash
mkdir -p ~/.claude/commands
cp commands/second-opinion.md ~/.claude/commands/
```

**Windows (PowerShell):**

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\commands"
Copy-Item commands\second-opinion.md "$env:USERPROFILE\.claude\commands\"
```

**Or with git clone:**

```bash
git clone https://github.com/YOUR_USERNAME/claude-second-opinion.git
# macOS/Linux:
cp claude-second-opinion/commands/second-opinion.md ~/.claude/commands/
# Windows:
Copy-Item claude-second-opinion\commands\second-opinion.md "$env:USERPROFILE\.claude\commands\"
```

Then restart Claude Code (or start a new session). The `/second-opinion` command will be available.

## Usage

```
/second-opinion                          # Review uncommitted git changes
/second-opinion src/utils/               # Review a specific directory
/second-opinion src/auth/login.ts        # Review a specific file
/second-opinion Should we use Redis?     # Get architecture feedback
```

### Example output

```
## Codex Second Opinion

[Codex's full analysis appears here]

## Cross-Model Delta

**Agree:** Both models flag the SQL injection risk in `buildQuery()`.
**Disagree:** Codex suggests extracting a base class; I think composition
is simpler here since there's only one shared method.
**Gaps:** Neither model checked the error handling path when the DB
connection drops mid-transaction.
```

## How it works

1. You invoke `/second-opinion` with optional arguments
2. Claude determines the right mode (git review, code review, or idea feedback)
3. Claude runs the Codex CLI with appropriate flags (`--ephemeral` to keep things sandboxed)
4. Codex analyzes the code using OpenAI's model
5. Claude presents Codex's findings and adds its own **Cross-Model Delta** — agreeing, disagreeing, or flagging gaps
6. If both models agree on actionable issues, Claude offers to fix them

## Customization

The command file is plain markdown. You can:

- Change the default review prompt in Mode 2
- Add project-specific focus areas
- Adjust the Cross-Model Delta format
- Add additional modes for your workflow

## Terms of service note

This command has Claude Code invoke OpenAI's Codex CLI and process its output at runtime. Before using, you should be aware of the relevant terms:

- **Anthropic / Claude Code:** No restriction on invoking external CLI tools — that's what Bash commands and MCP servers are for. Anthropic's restrictions concern the reverse direction (third-party tools accessing Claude's OAuth tokens).
- **OpenAI API terms:** OpenAI's [Services Agreement](https://openai.com/policies/services-agreement/) prohibits using API output "to develop AI models that compete with OpenAI." Whether a competing AI reading and synthesizing Codex output at runtime (not for training) falls under this clause is ambiguous. The clause targets model development, not end-user tooling — but it's broadly worded.
- **Codex CLI itself:** [Apache 2.0 open source](https://github.com/openai/codex) — no usage restrictions on the software.

**In short:** This is a developer productivity tool, not model training. You're doing what any developer does when they compare answers from two AI tools — just automated. But the terms are broad enough that you should read them yourself and make your own judgment. This section is informational, not legal advice.

## License

MIT
