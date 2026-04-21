# Engram

Persistent memory system for Claude Code — three markdown files, a `CLAUDE.md` section, and four skills. No hooks, no daemons.

Engram gives Claude persistent memory across conversations by maintaining three specialized files: **preferences** (auto-loaded into context every session), **conversation history** (searchable, newest-first), and **long-term memories** (append-only archive). The `memory-init` skill wires everything up, and three more skills (`memory-remember`, `memory-recall`, `memory-status`) handle reading, writing, and diagnostics.

## Architecture: Three-File Separation

| File | Purpose | Access Strategy |
|------|---------|-----------------|
| `memory_preferences.md` | User/project preferences | Auto-loaded into context via `@` reference in `CLAUDE.md` |
| `memory_conversations.md` | Conversation summaries | On-demand Grep search, newest-first |
| `memory_longterm.md` | Permanent memories | On-demand Grep search, append-only |

This separation optimizes for both context window usage and retrieval accuracy:
- **Preferences** are small and always relevant — loaded on every session via `@`
- **Conversations** grow large over time — searched selectively by topic
- **Long-term memories** are permanent records — searched selectively, never deleted

## Installation

Install as a Claude Code plugin:

```bash
claude plugin add ./engram
```

## Getting Started

After installation, run:

```
/engram:memory-init
```

This walks through:

1. **Choose a preset**: `personal-assistant`, `project-assistant`, `character-companion`, or `pair-programmer`
2. **Customize file names** (optional): keep defaults or rename the three memory files
3. **Choose language**: `en` or `zh` for the `CLAUDE.md` instruction section
4. **Choose search mode**: `weak` (judgment-based, default) or `strong` (check by default)
5. Engram creates the memory files, writes configuration, and appends an `ENGRAM` section to `CLAUDE.md` that auto-loads your preferences.

From that point on, preferences are in Claude's context every session, and the three other skills trigger automatically based on conversation content.

## Presets

Engram ships with four preset templates optimized for different use cases:

### Personal Assistant
For daily life management, personal interactions, and habit tracking.

### Project Assistant
For software development, architecture decisions, and team workflow.

### Character Companion
For persona-based interactions, relationship development, and story progression.

### Pair Programmer
For AI self-improvement through tracking corrections, user preferences about AI behavior, and collaboration patterns.

## How It Works

### Flow

1. **Session starts** → `CLAUDE.md` loads, including the ENGRAM section + `@memory_preferences.md`. Claude sees preferences without doing anything.
2. **Topic relates to past** → Claude invokes `memory-recall` → Grep conversations / longterm files.
3. **New preference, decision, or milestone surfaces** → Claude invokes `memory-remember` → writes to the appropriate file using `date` for timestamps.
4. **Preferences file is updated mid-session** → Write tool result already shows Claude the new content (no explicit reload needed).
5. **User asks about memory state** → Claude invokes `memory-status` → displays config + file stats + health check.

No hooks, no per-turn injection, no state files to track. The entire system is: three memory files + one `CLAUDE.md` section + four skills.

### Skills

| Skill | Trigger | Action |
|-------|---------|--------|
| `memory-init` | User sets up memory, or config missing | Ask preset, create files, wire up CLAUDE.md |
| `memory-remember` | New preference/decision/milestone, or conversation endpoint | Write to appropriate memory file |
| `memory-recall` | Topic may relate to past context | Grep across conversations + longterm |
| `memory-status` | User asks about memory state | Display config, stats, health check |

## Configuration

Settings are stored in `.claude/memory-settings.json`:

```json
{
  "preset": "personal-assistant",
  "files": {
    "preferences": "memory_preferences.md",
    "conversations": "memory_conversations.md",
    "longterm": "memory_longterm.md"
  },
  "language": "en",
  "search_mode": "weak"
}
```

| Field | Description | Default |
|-------|-------------|---------|
| `preset` | Active preset template (stored for reference / re-init) | `"personal-assistant"` |
| `files.preferences` | Preferences file name | `"memory_preferences.md"` |
| `files.conversations` | Conversations file name | `"memory_conversations.md"` |
| `files.longterm` | Long-term memory file name | `"memory_longterm.md"` |
| `language` | CLAUDE.md section language (`en` or `zh`) | `"en"` |
| `search_mode` | `weak` (judgment-based) or `strong` (check by default) | `"weak"` |

### Customizing the CLAUDE.md Section

The `ENGRAM` section in `CLAUDE.md` (between `<!-- ENGRAM:START -->` and `<!-- ENGRAM:END -->`) is fully editable. Re-running `memory-init` will regenerate it, so if you make manual tweaks, keep a copy or re-apply them after upgrades.

## Memory File Formats

### Preferences

Markdown with structured sections. Updated in-place when preferences change.

```markdown
## User Preferences

### Basic Information
- Name: Frank
- Location: Taipei
```

### Conversations

Reverse chronological entries. New entries prepended to top.

```markdown
### 2025-01-29 14:30 - Auth refactor discussion

Discussed migrating from session-based to JWT authentication.
Decided on refresh token rotation strategy.
Action: implement token refresh endpoint next session.
```

### Long-Term Memories

Append-only entries under categorized sections.

```markdown
### Architecture Decisions
- 2025-01-15: Migrated from MongoDB to PostgreSQL for ACID compliance
- 2025-01-29: Adopted JWT with refresh token rotation for auth

### Milestones
- 2025-01-20: v1.0 launched to production
```

## Requirements

- `date` — timestamp generation (standard on macOS/Linux)

## Upgrading from v1.x

v2.0 removes the `UserPromptSubmit` hook entirely. If you were on v1.x:

- Re-run `/engram:memory-init` once to regenerate `.claude/memory-settings.json` (drops the obsolete `enabled`, `reload_interval`, `reminder_file` fields)
- Delete `.claude/memory_counter.txt` and `.claude/memory-reminder.md` if present — they are no longer used
- Your memory files (`memory_preferences.md` etc.) are untouched

## License

MIT
