# SOP: Weekly Review

## Purpose

Generate a summary of work done across all [WORK_UNIT_PLURAL] each week. The output is a structured review you can use for reporting, CRM updates, team standups, or your own records.

## When

Every Friday (or when [YOUR_NAME] asks for a weekly review).

## The Two-Layer System

### Layer 1 — Continuous Accumulation (during the week)

The KB `activity-log.md` for each [WORK_UNIT] is the **running buffer** where context accumulates throughout the week:

| Trigger | What happens | Where it lands |
|---------|-------------|----------------|
| **Meeting processed** | Transcript → meeting note + activity-log entry | `meetings/` + `activity-log.md` |
| **Work done in Claude session** | Auto-log per CLAUDE.md rules | `activity-log.md` |
| **Data or report shared** | Extract key points | `activity-log.md` |
| **Strategy decision made** | Update strategy + log | `strategy.md` + `activity-log.md` |

The key is writing **rich** activity-log entries — not just "did X" but "did X because Y, result was Z, next step is W."

### Layer 2 — Friday Compilation

When the weekly review runs:

1. **Read activity logs** — rich context from meetings, work sessions
2. **Check calendar** (if connected) — meetings that happened this week
3. **Identify gaps** — [WORK_UNIT_PLURAL] with activity but no KB context
4. **Nudge [YOUR_NAME]** — "These need context: share notes or a quick summary"
5. **Compile review** — structured summary per [WORK_UNIT]
6. **Present for approval** — [YOUR_NAME] reviews and edits
7. **Update KB** — append weekly review entries to activity logs

## Review Quality Checklist

A good weekly review entry includes (where applicable):

- [ ] What was done (concrete deliverables or progress)
- [ ] Meeting outcomes and relationship context
- [ ] Key metrics or data points discussed
- [ ] Strategic decisions or direction changes
- [ ] Forward-looking next steps
- [ ] Sentiment and working relationship notes

## Gap Identification

[WORK_UNIT_PLURAL] that have activity but thin KB context typically need one of:

1. **Meeting transcript** — share a recording link, Claude processes it
2. **Data or report** — share relevant data, Claude extracts key points
3. **Quick verbal summary** — [YOUR_NAME] dictates context, Claude logs it

Also check: calendar meetings this week that have no corresponding meeting note file in `meetings/`.
