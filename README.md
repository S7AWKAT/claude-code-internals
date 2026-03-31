# claude-code-internals

TypeScript source code extracted from the Claude Code CLI (`@anthropic-ai/claude-code`). Useful for understanding how Claude Code works under the hood.

## What's in here

The `src/` directory contains the full CLI source, organized by concern:

| Directory / File | What it does |
|---|---|
| `main.tsx` | Entry point — bootstraps the CLI and renders the terminal UI |
| `cli/` | Argument parsing, flag handling, session init |
| `commands/` | Slash command implementations (`/help`, `/clear`, `/compact`, etc.) |
| `tools/` | Built-in tool implementations (Bash, Read, Write, Edit, Glob, Grep, etc.) |
| `hooks/` | Hook event system — PreToolUse, PostToolUse, Stop, Notification, etc. |
| `skills/` | Bundled skill loading and discovery |
| `plugins/` | Plugin system — loading, scoping, credential management |
| `agents/` / `coordinator/` | Multi-agent orchestration and task delegation |
| `context/` | Context window management, compaction, summarization |
| `state/` | Session state, conversation history, memory |
| `screens/` | Terminal UI screens (main REPL, dialogs, etc.) |
| `server/` | Remote control / API server mode |
| `keybindings/` | Keyboard shortcut handling |
| `vim/` | Vim mode implementation |
| `voice/` | Voice input handling |
| `cost-tracker.ts` | Token usage and cost tracking |
| `QueryEngine.ts` | Core model query pipeline |
| `schemas/` | Zod schemas for tool inputs, hook payloads, config |

## Notable internals

- **Perfetto tracing**: Set `CLAUDE_CODE_PERFETTO_TRACE=1` to record a Chrome DevTools flame chart of your session. Open in `chrome://tracing`.
- **Hook system**: Hooks can do more than allow/deny — `PermissionRequest` hooks can return `updatedPermissions` to rewrite the entire permission system. `PostToolUse` hooks can return `updatedMCPToolOutput` to intercept MCP responses before the model sees them.
- **Sandboxing**: Uses Apple seatbelt (macOS) and bubblewrap/seccomp (Linux) with runtime-generated kernel profiles.
- **OpenTelemetry**: Full OTEL tracing with 16 span types including cost, token, commit, and PR counters.

## Source

Extracted from the published npm package: `npm install -g @anthropic-ai/claude-code`
