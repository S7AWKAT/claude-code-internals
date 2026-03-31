# Rewriting Project Claw Code

<p align="center">
  <strong>Better Harness Tools, not merely storing the archive of leaked Claude Code</strong>
</p>

---

## Backstory

At 4 AM on March 31, 2026, I woke up to my phone blowing up with notifications. The Claude Code source had been exposed, and the entire dev community was in a frenzy. My girlfriend in Korea was genuinely worried I might face legal action from Anthropic just for having the code on my machine — so I did what any engineer would do under pressure: I sat down, ported the core features to Python from scratch, and pushed it before the sun came up.

The whole thing was orchestrated end-to-end using [oh-my-codex (OmX)](https://github.com/Yeachan-Heo/oh-my-codex) by [@bellman_ych](https://x.com/bellman_ych) — a workflow layer built on top of OpenAI's Codex ([@OpenAIDevs](https://x.com/OpenAIDevs)). I used `$team` mode for parallel code review and `$ralph` mode for persistent execution loops with architect-level verification. The entire porting session — from reading the original harness structure to producing a working Python tree with tests — was driven through OmX orchestration.

The result is a clean-room Python rewrite that captures the architectural patterns of Claude Code's agent harness without copying any proprietary source. I'm now actively collaborating with [@bellman_ych](https://x.com/bellman_ych) — the creator of OmX himself — to push this further. The basic Python foundation is already in place and functional, but we're just getting started. **Stay tuned — a much more capable version is on the way.**

## Repository Layout

```text
.
├── src/                                # TypeScript source from Claude Code CLI
│   ├── main.tsx                        # Entry point — bootstraps CLI and terminal UI
│   ├── QueryEngine.ts                  # Core model query pipeline
│   ├── query.ts                        # Query loop with compaction, retry, streaming
│   ├── Tool.ts                         # Tool base class and registration
│   ├── commands/                       # Slash command implementations
│   ├── tools/                          # Built-in tools (Bash, Read, Write, Edit, Glob, Grep, etc.)
│   ├── hooks/                          # Hook event system (PreToolUse, PostToolUse, Stop, etc.)
│   ├── skills/                         # Bundled skill loading and discovery
│   ├── plugins/                        # Plugin system — loading, scoping, credential management
│   ├── coordinator/                    # Multi-agent orchestrator mode
│   ├── buddy/                          # Virtual pet companion system (/buddy command)
│   ├── services/
│   │   ├── autoDream/                  # Background memory consolidation
│   │   ├── SessionMemory/              # Per-session memory extraction
│   │   ├── extractMemories/            # Durable memory extraction from transcripts
│   │   ├── compact/                    # Context compaction (auto, micro, snip, reactive)
│   │   ├── mcp/                        # Model Context Protocol client
│   │   └── analytics/                  # GrowthBook feature flags + telemetry
│   ├── context/                        # Context window management
│   ├── state/                          # Session state, conversation history
│   ├── screens/                        # Terminal UI screens (React/ink)
│   ├── server/                         # Remote control / API server mode
│   ├── vim/                            # Vim mode implementation
│   ├── voice/                          # Voice input handling
│   └── constants/                      # System prompts, feature flags, limits
└── README.md
```

## What's Inside

### 97 Build-Time Feature Flags

Dead-code-eliminated at compile time. The major unreleased ones:

| Flag | What it does |
|------|-------------|
| `KAIROS` | Autonomous agent scheduling platform (cron, dream loop, memory, proactive mode) |
| `ULTRAPLAN` | Remote parallel planning sessions |
| `ULTRATHINK` | Extended thinking mode |
| `COORDINATOR_MODE` | Multi-agent orchestrator system prompt |
| `FORK_SUBAGENT` | Fork yourself instead of spawning fresh (shares prompt cache) |
| `BUDDY` | Virtual pet companion with gacha rarity system |
| `PROACTIVE` | Agent acts before being asked |
| `DAEMON` | Background daemon mode |
| `WEB_BROWSER_TOOL` | Built-in web browser tool |
| `VOICE_MODE` | Voice input |

### Notable Internals

- **Perfetto tracing**: `CLAUDE_CODE_PERFETTO_TRACE=1` records Chrome DevTools flame charts
- **Client attestation**: Zig-based hash injection at the network layer (`cch=` placeholder in billing header)
- **autoDream**: Background memory consolidation after 24h + 5 sessions
- **Session Memory**: Separate background agent extracting conversation notes
- **23 bash security validators**: Catches Zsh module attacks, IFS injection, Unicode whitespace, brace expansion
- **Compaction**: 9-section structured extraction with hidden `<analysis>` scratchpad
- **Command semantics**: Exit code maps per command (grep 1 = "no matches", not error)
- **180+ undocumented `CLAUDE_CODE_` env vars**
- **500+ GrowthBook feature flags** (`tengu_` prefix)

### Fleet-Scale Numbers (from source comments)

- 34M+ Explore agent spawns per week
- Removing CLAUDE.md from Explore saves 5-15 Gtok/week
- Agent list caching was 10.2% of fleet cache_creation tokens
- Autocompact failures cost 250K API calls/day before circuit breaker
- Cache bust from wrong system prompt = 12x input token cost penalty

## Ownership / Affiliation Disclaimer

- This repository does **not** claim ownership of the original Claude Code source material.
- This repository is **not affiliated with, endorsed by, or maintained by Anthropic**.
