---
name: memory-remember
description: Store a memory into the appropriate Engram file. Trigger when a new preference, decision, milestone, or natural conversation summary point is identified, or when the user explicitly asks to remember something.
---

# Memory Remember

Store information into the appropriate memory file.

## When to Use

Trigger proactively — do not wait for the user to ask — when:

- A new user preference or decision is discovered during conversation
- A milestone, architecture decision, or lesson learned is identified
- The conversation reaches a natural summary point or is ending
- The user explicitly asks to remember something

## Workflow

### 1. Read Configuration

Read `.claude/memory-settings.json` to get the configured file names.

### 2. Get Current Timestamp

Before writing, run `date '+%Y-%m-%d %H:%M'` via Bash to get the current timestamp. Use this for any dated entry. Use `date '+%Y-%m-%d'` when only a date is needed.

### 3. Classify Content

Determine which file the memory belongs to:

| Type | Target File | Examples |
|------|-------------|----------|
| **Preference** | preferences file | User likes, dislikes, habits, settings, personal info, project conventions |
| **Conversation Summary** | conversations file | What was discussed, tasks completed, decisions made in this session |
| **Long-Term Memory** | longterm file | Life events, milestones, architecture decisions, lessons learned, relationship changes |

### 4. Write to Appropriate File

#### Preferences File
- Read the current file
- Find the appropriate section for the new information
- Edit the existing section content (update, don't duplicate)
- Update the `Last Updated: {DATE}` header to today's date

#### Conversations File
- **Prepend** new entry to the TOP of the file (below the header, above existing entries)
- Format: `### YYYY-MM-DD HH:MM - Topic` (use the timestamp from step 2)
- Include: brief summary of what was discussed, key decisions, action items
- Keep summaries concise (3-8 lines)

#### Long-Term Memory File
- **Append** to the appropriate section within the file
- Format: `- YYYY-MM-DD: Description` (use date from step 2)
- NEVER delete existing entries — only add new ones
- Place under the correct subsection (Milestones, Decisions, Lessons Learned, etc.)

### 5. Confirmation

Briefly mention what was saved (one line), e.g.:
- "Noted your preference for TypeScript over JavaScript."
- "Saved conversation summary about the auth refactor."
- "Recorded the migration to PostgreSQL as an architecture decision."

Do NOT be verbose about the saving process — keep it natural and seamless.
