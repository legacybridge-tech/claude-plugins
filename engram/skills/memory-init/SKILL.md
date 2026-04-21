---
name: memory-init
description: Initialize the Engram memory system — creates configuration, memory files, and CLAUDE.md integration. Trigger when the user sets up persistent memory for a project, or when `.claude/memory-settings.json` is missing and a memory feature is requested.
---

# Memory Init

Initialize the Engram memory system for the current project.

## When to Use

Trigger this skill when:

- The user explicitly asks to set up, initialize, or configure Engram / persistent memory
- A memory-related skill (`memory-remember`, `memory-recall`, `memory-status`) is requested but `.claude/memory-settings.json` does not exist
- The user re-runs init to upgrade an existing configuration (e.g. to change preset, language, or search_mode)

## Workflow

### 1. Ask Preset Choice

Ask the user which preset they want:

- **personal-assistant** — Daily life, preferences, habits, personal interactions
- **project-assistant** — Codebase, architecture, tech decisions, team workflow
- **character-companion** — Persona, relationship, story progression, emotional bonds
- **pair-programmer** — AI self-learning, mistake tracking, user preference rules, collaboration patterns

Or offer **custom** configuration.

### 2. Ask File Name Customization

Show the default file names and ask if the user wants to change them:

```
memory_preferences.md    (User/Project/Character preferences)
memory_conversations.md  (Conversation history)
memory_longterm.md       (Long-term memories)
```

### 3. Ask Language Preference

- `en` — English CLAUDE.md section (default)
- `zh` — Chinese CLAUDE.md section

### 3.5 Ask Search Mode

- `strong` — 回覆前預設先用 `memory-recall` 檢查一次記憶 / Check memories before responding by default
- `weak` — 判斷話題可能涉及過去時才搜尋 / Search only when a topic may involve past context (default)

### 4. Create Configuration

Create `.claude/memory-settings.json` with the chosen settings:

```json
{
  "preset": "<chosen-preset>",
  "files": {
    "preferences": "<preferences-filename>",
    "conversations": "<conversations-filename>",
    "longterm": "<longterm-filename>"
  },
  "language": "<en|zh>",
  "search_mode": "<strong|weak>"
}
```

### 5. Copy Preset Templates

Copy the template files from the plugin's `templates/presets/<preset>/` directory to the project root. The plugin root is available via `${CLAUDE_PLUGIN_ROOT}` or can be found by locating the engram plugin directory.

Read each template file and write it to the project root with the configured file names.

Replace `{DATE}` placeholders with the current date (run `date '+%Y-%m-%d'` via Bash to get it).

### 5c. Append Engram Section to CLAUDE.md

Read the CLAUDE.md section template from the plugin's `templates/` directory:

- If language is `zh`: read from `templates/claude-md-section-zh.md`
- If language is `en`: read from `templates/claude-md-section-en.md`

Replace the following placeholders with the actual configured values:

- `{PREFS_FILE}` → preferences file name
- `{CONVOS_FILE}` → conversations file name
- `{LONGTERM_FILE}` → longterm file name
- `{PRESET}` → chosen preset name
- `{VERSION}` → plugin version from `.claude-plugin/plugin.json`
- `{SEARCH_MODE_INSTRUCTION}` → resolved based on search_mode and language. Emit as a standalone paragraph (no numbered prefix):
  - **strong + en**: `**Strict search mode**: By default, check memories with the \`memory-recall\` skill before responding; skip only when the topic is clearly unrelated to history (greetings, pure computation, etc.).`
  - **weak + en**: `**Judgment-based search**: When you judge that a topic may involve past discussions or established preferences, search with the \`memory-recall\` skill before responding.`
  - **strong + zh**: `**嚴格搜尋模式**：回覆前預設先用 \`memory-recall\` skill 檢查一次記憶;只有話題明顯與歷史無關（打招呼、純粹計算等)時才略過。`
  - **weak + zh**: `**判斷式搜尋**：當你判斷話題可能涉及過去討論或既有偏好時,先用 \`memory-recall\` skill 搜尋後再回覆。`

Then apply to `CLAUDE.md` in the project root:

1. **If CLAUDE.md exists and contains `<!-- ENGRAM:START -->`**: Replace everything between `<!-- ENGRAM:START -->` and `<!-- ENGRAM:END -->` (inclusive) with the new resolved content
2. **If CLAUDE.md exists but has no markers**: Append the resolved content to the end of the file (with a blank line separator)
3. **If CLAUDE.md does not exist**: Create a new CLAUDE.md with only the resolved content

The `@{PREFS_FILE}` reference inside the section causes Claude Code to auto-load preferences into context on every session start.

### 6. Upgrade Path (Re-running Init)

If `.claude/memory-settings.json` already exists:

1. Ask what the user wants to change (preset / language / search_mode / file names)
2. Update `.claude/memory-settings.json` with the new values
3. Re-generate the CLAUDE.md section (Step 5c) with the new values
4. If file names changed, leave the old files in place and tell the user to move them manually — do not delete existing memory content

### 7. Optional Customization

Ask the user: "Would you like to add or modify any sections in the preferences template?"

If yes, guide through customization — adding new sections, renaming existing ones, or removing irrelevant ones.

### 8. Optional Initial Fill

Ask the user: "Would you like to fill in your initial preferences now?"

If yes, walk through each section and populate the preferences file with the user's answers.

### 9. Output Summary

Display a summary of what was created:

```
Engram Memory System Initialized!

Preset: <preset>
Language: <language>
Search mode: <search_mode>

Files created/updated:
  - <preferences-file> (preferences — auto-loaded via @ reference in CLAUDE.md)
  - <conversations-file> (conversation history)
  - <longterm-file> (long-term memories)
  - .claude/memory-settings.json (configuration)
  - CLAUDE.md (memory instructions + preferences auto-load)

The memory system is now active. I will:
  - See your preferences every session via the @-reference in CLAUDE.md
  - Save new preferences and decisions as I discover them
  - Record conversation summaries at natural endpoints
  - Search past memories when a topic relates to previous context
```
