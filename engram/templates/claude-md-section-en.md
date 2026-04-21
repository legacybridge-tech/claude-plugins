<!-- ENGRAM:START -->
<!-- Engram Memory System v{VERSION} | Preset: {PRESET} | Do not manually edit between markers — re-run memory-init to update -->

## Engram Memory System

This project has persistent memory across conversations, stored in three markdown files:

- **`{PREFS_FILE}`** — User/project preferences. Already auto-loaded into context via the `@` reference below; no need to read it manually.
- **`{CONVOS_FILE}`** — Conversation summary history, newest first. Grep by topic keywords when relevant.
- **`{LONGTERM_FILE}`** — Permanent records (decisions, milestones, key events), append-only. Grep by topic keywords when relevant.

### How to work with it

- When a topic could touch the user's past preferences, decisions, or prior discussions, search first with the `memory-recall` skill before responding.
- When you discover something worth keeping long-term (a new preference, decision, milestone), or when a conversation reaches a natural summary point, use the `memory-remember` skill to write to the appropriate file.

{SEARCH_MODE_INSTRUCTION}

## Memory Preferences
@{PREFS_FILE}
<!-- ENGRAM:END -->
