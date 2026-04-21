---
name: memory-status
description: Display Engram memory system status — configuration, file sizes, entry counts, and health check. Trigger when the user asks about memory state, what's been remembered, or for diagnostics.
---

# Memory Status

Display the current state of the Engram memory system.

## When to Use

Use when:
- The user asks about the memory system status
- The user wants to know what's been remembered
- Diagnostics are needed (missing files, empty preferences, etc.)
- The user asks "what do you know about me?"

## Workflow

### 1. Read Configuration

Read `.claude/memory-settings.json` for:
- Preset type
- File names
- Language
- Search mode

If the file is missing, tell the user to run `memory-init` first.

### 2. Inspect Memory Files

For each of the three memory files, gather:
- File exists? (yes/no)
- File size (approximate line count)
- Last modified date (via `stat` or `ls -l`)
- Number of entries:
  - Preferences: count of filled (non-empty) fields
  - Conversations: count of `### YYYY-MM-DD` entries
  - Long-term: count of `- YYYY-MM-DD:` entries

### 3. Check CLAUDE.md Integration

Verify that `CLAUDE.md` contains `<!-- ENGRAM:START -->` / `<!-- ENGRAM:END -->` markers and an `@{PREFS_FILE}` reference. This is what auto-loads preferences each session.

### 4. Display Status Report

Format:

```
Engram Memory System Status
═══════════════════════════

Configuration:
  Preset:      <preset>
  Language:    <language>
  Search mode: <strong|weak>

Memory Files:
  <preferences-file>
    Status:   <exists/missing>
    Size:     <lines> lines
    Modified: <date>
    Fields:   <filled>/<total> filled

  <conversations-file>
    Status:   <exists/missing>
    Size:     <lines> lines
    Modified: <date>
    Entries:  <count> conversations

  <longterm-file>
    Status:   <exists/missing>
    Size:     <lines> lines
    Modified: <date>
    Entries:  <count> memories

CLAUDE.md Integration:
  ENGRAM section: <present/missing>
  @-reference:    <present/missing>

Recent Conversations (last 3):
  - YYYY-MM-DD HH:MM - Topic
  - YYYY-MM-DD HH:MM - Topic
  - YYYY-MM-DD HH:MM - Topic

Health Check:
  ✓ All files present
  ✓ Preferences populated
  ✓ CLAUDE.md integration active
  ✗ No conversation summaries yet
```

### 5. Health Check Items

Check for:
- Missing memory files → warn and offer to recreate via `memory-init`
- Empty preferences file → suggest filling in
- No conversation summaries → note that this is normal for new setups
- Missing settings file → offer to run `memory-init`
- Missing CLAUDE.md ENGRAM section → preferences won't auto-load; offer to re-run `memory-init` to restore
