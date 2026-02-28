---
description: Get a cross-model second opinion from OpenAI Codex CLI — code reviews, architecture feedback, idea validation
---

# Second Opinion (Codex CLI)

Get a cross-model second opinion from OpenAI Codex CLI. Use for code reviews, architecture feedback, idea validation, or any situation where an independent perspective from a different model family reduces correlated blind spots.

Two AI models from different families looking at the same code catches more issues than either alone — convergence means high confidence, divergence highlights genuine trade-offs.

## Prerequisites

Before running any command, verify Codex CLI is available:

```bash
codex --version
```

If the command fails, stop and tell the user: "Codex CLI is not installed. Install it with `npm install -g @openai/codex` and authenticate with `codex login`."

## Modes

### 1. Git Changes Review (default when no arguments provided)

Review uncommitted changes in the current project:

```bash
codex review --uncommitted
```

If on a feature branch with commits, review against the base branch:

```bash
codex review --base main
```

To review a specific commit:

```bash
codex review --commit <SHA>
```

### 2. File/Directory Code Review

When $ARGUMENTS is a file or directory path, use exec mode with a review prompt piped via stdin:

```bash
echo "Review this code for bugs, security issues, code quality, and improvement opportunities. Be specific and actionable. Focus especially on things a different reviewer might catch that the original author missed." | codex exec --full-auto --ephemeral -
```

For targeted review with custom focus:

```bash
echo "Review this code. Focus area: <user's instructions>" | codex exec --full-auto --ephemeral -
```

When the target is a subdirectory, pass `-C "<path>"` to scope Codex to that directory.

### 3. Architecture / Idea Feedback

When $ARGUMENTS is a question, idea, or design description (not a file path):

```bash
echo "<the question or idea text>" | codex exec --full-auto --ephemeral --skip-git-repo-check -
```

## Process

1. Determine which mode fits the user's request:
   - No arguments or "review my changes" → Mode 1 (git review)
   - File/directory path → Mode 2 (code review)
   - A question or description → Mode 3 (idea feedback)
2. Identify the correct working directory (current project root).
3. Run the appropriate `codex` command via Bash. Use `--ephemeral` to avoid polluting session history. Pipe prompts via stdin with `-` to avoid shell quoting issues across platforms.
4. Present Codex's full output under a `## Codex Second Opinion` header.
5. Add your own brief `## Cross-Model Delta` section noting where you **agree**, **disagree**, or see **gaps** in Codex's feedback. This is the high-value synthesis — two model families converging on the same issues means high confidence; divergence highlights genuine trade-offs.
6. If Codex found actionable issues you agree with, offer to fix them.

## Notes

- Codex CLI must be installed (`npm install -g @openai/codex`) and authenticated (`codex login`).
- The `--full-auto` flag gives Codex sandboxed read access — changes stay ephemeral.
- For large directories, Codex may take 30-120 seconds. This is expected.
- The `-o <file>` flag on `codex exec` can capture output to a file if needed.

## Target

$ARGUMENTS
