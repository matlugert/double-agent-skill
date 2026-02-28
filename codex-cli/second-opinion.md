# Second Opinion (Claude Code CLI)

When the user asks for a "second opinion," invokes `/second-opinion`, or requests a cross-model review, follow this process to get an independent perspective from Claude Code CLI.

Two AI models from different families looking at the same code catches more issues than either alone — convergence means high confidence, divergence highlights genuine trade-offs.

## Prerequisites

Before running any command, verify Claude Code CLI is available:

```bash
claude --version
```

If the command fails, stop and tell the user: "Claude Code CLI is not installed. See https://docs.anthropic.com/en/docs/claude-code for installation instructions."

## Modes

### 1. Git Changes Review (default when no arguments provided)

Pipe the diff to Claude for review:

```bash
git diff | claude -p "Review these code changes for bugs, security issues, code quality, and improvement opportunities. Be specific and actionable. Focus especially on things a different reviewer might catch that the original author missed."
```

For staged changes:

```bash
git diff --cached | claude -p "Review these staged changes for bugs, security issues, code quality, and improvement opportunities. Be specific and actionable. Focus especially on things a different reviewer might catch that the original author missed."
```

For changes on a feature branch vs main:

```bash
git diff main...HEAD | claude -p "Review these branch changes for bugs, security issues, code quality, and improvement opportunities. Be specific and actionable. Focus especially on things a different reviewer might catch that the original author missed."
```

### 2. File/Directory Code Review

When the user provides a file or directory path, give Claude read access so it can explore the code:

```bash
claude -p "Review the code in <path> for bugs, security issues, code quality, and improvement opportunities. Be specific and actionable. Focus especially on things a different reviewer might catch that the original author missed." --allowedTools "Read,Grep,Glob"
```

For targeted review with custom focus:

```bash
claude -p "Review the code in <path>. Focus area: <user's instructions>" --allowedTools "Read,Grep,Glob"
```

### 3. Architecture / Idea Feedback

When the user provides a question, idea, or design description (not a file path):

```bash
claude -p "<the question or idea text>"
```

## Process

1. Determine which mode fits the user's request:
   - No arguments or "review my changes" → Mode 1 (git review)
   - File/directory path → Mode 2 (code review)
   - A question or description → Mode 3 (idea feedback)
2. Identify the correct working directory (current project root).
3. Run the appropriate `claude` command. Pipe diffs via stdin for git reviews. Use `--allowedTools` for file/directory reviews so Claude can read the codebase.
4. Present Claude's full output under a `## Claude Second Opinion` header.
5. Add your own brief `## Cross-Model Delta` section noting where you **agree**, **disagree**, or see **gaps** in Claude's feedback. This is the high-value synthesis — two model families converging on the same issues means high confidence; divergence highlights genuine trade-offs.
6. If Claude found actionable issues you agree with, offer to fix them.

## Notes

- Claude Code CLI must be installed and authenticated (see https://docs.anthropic.com/en/docs/claude-code).
- The `-p` flag runs Claude in non-interactive print mode — it processes the prompt and exits.
- `--allowedTools` restricts Claude to read-only tools (no file edits, no bash commands).
- For large codebases, Claude may take 30-60 seconds. This is expected.
