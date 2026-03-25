# Knowledge Base

This is [YOUR_NAME]'s knowledge base for managing [WORK_UNITS]. It contains profiles, activity logs, strategies, and meeting notes.

Claude should treat this KB as the single source of truth for all [WORK_UNIT] context.

> **Vault root:** `[VAULT_ROOT]`
> All relative paths in this file are relative to this root. Always prepend this root when constructing absolute paths.

---

## Auto-Read: Context Loading

**ALWAYS** do this when a [WORK_UNIT] is mentioned by name or abbreviation:

1. Read their `profile.md` — gives you context, contacts, services, goals
2. If the conversation involves recent work or history, also read `activity-log.md`
3. If strategy, priorities, or planning is discussed, also read `strategy.md`

Do this silently. Do not tell [YOUR_NAME] you're reading files — just have the context ready.

### Path Resolution

To resolve any [WORK_UNIT] name to an absolute file path:

1. Read `_templates/project-mapping.md` — the single source of truth for names, folder paths, and aliases.
2. Match the name (or abbreviation) to a row in the mapping table.
3. Prepend the vault root to the folder path.
4. Append the target file (`profile.md`, `activity-log.md`, `strategy.md`, or `meetings/`).

**Example:** "Acme profile" → mapping gives `[WORK_FOLDER]/Acme/` → `[VAULT_ROOT]/[WORK_FOLDER]/Acme/profile.md`

---

## Auto-Log: Activity Logging After Work

After completing meaningful work for a [WORK_UNIT] during a session, **automatically** append a dated entry to their `activity-log.md`. Do not ask for permission.

**Format:**

```markdown
## YYYY-MM-DD
**[Short title of work done]**

[2-4 sentences: what was done, why, and what's next. Include deliverable names, key decisions, and any pending items.]
```

**Newest entries go at the top** (below the frontmatter/header).

**What counts as meaningful work:**
- Creating deliverables (documents, reports, presentations, analyses)
- Completing strategic decisions or planning
- Processing meeting notes or transcripts
- Significant communication or analysis

**Do NOT log:** File formatting, quick lookups, reading files, minor edits.

Write **rich** entries — not just "did X" but "did X because Y, result was Z, next step is W." These feed into weekly reviews.

---

## Auto-Update: Strategy Changes

When strategic decisions are made during a session (new goals, changed priorities, updated metrics, scope changes), **automatically** update the relevant sections in the [WORK_UNIT]'s `strategy.md`. Keep existing content and update the relevant sections — don't overwrite the whole file.

---

## Daily Journal

At session **START**, silently read `_daily/YYYY-MM-DD.md` (today) and yesterday's if it exists. This provides cross-session continuity.

At session **END** (if meaningful work was done), append to `_daily/YYYY-MM-DD.md`:

```markdown
### HH:MM — [Brief title]
- [WORK_UNIT](s): [names]
- What: [1-2 sentences]
- Next: [pending items]
```

The daily journal complements per-[WORK_UNIT] activity logs. Activity logs are the detailed record; the daily journal is a lightweight cross-[WORK_UNIT] index of the day's work.

---

## Graceful Degradation

When a file is missing or empty:
- **profile.md missing** → note internally, continue with available context, offer to scaffold after current task
- **activity-log.md missing** → treat as new [WORK_UNIT] with no history, create the file with proper header when first logging
- **strategy.md missing** → skip strategy context, do not fail
- **meetings/ missing** → create the directory when saving the first meeting note

When a referenced file (SOP, template) does not exist:
- Log the broken reference in the daily journal
- Continue with best-effort behavior
- Do not interrupt [YOUR_NAME] about infrastructure issues mid-task

---

## Context Preservation

During long sessions, proactively write important state to the daily journal before it gets lost to context compression:
- Decisions made this session
- Context shared verbally but not yet written to KB files
- In-progress work state (what's done, what's next)

After context compression, re-read the daily journal to recover state.

---

## Procedures & References

| Topic | Where to find it |
|-------|-----------------|
| Meeting notes format | `_templates/meeting-note.md` |
| Meeting processing workflow | `SOPs/meeting-processing.md` |
| Weekly review | `SOPs/weekly-review.md` |
| Name → folder mapping | `_templates/project-mapping.md` |
| File templates | `_templates/` (profile, strategy, activity-log) |
| Available automations | `SOPs/available-automations.md` |

## Vault Structure

```
[WORK_FOLDER]/
└── {name}/
    ├── profile.md        — Context, contacts, scope, goals
    ├── activity-log.md   — Chronological work log (newest first)
    ├── strategy.md       — Current priorities, metrics, engagement details
    └── meetings/         — Individual meeting note files (YYYY-MM-DD-topic.md)
```

- `[WORK_FOLDER]/_overview.md` — Your role, tools, and workflow notes
- `SOPs/` — Standard operating procedures
- `_templates/` — File format templates
- `_daily/` — Cross-[WORK_UNIT] daily session journals

## Data Sources

Always check the KB first before external sources. The KB is the canonical view.

<!-- Data sources are configured during setup. The setup wizard adds entries here based on your connected tools. -->
