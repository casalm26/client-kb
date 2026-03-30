# Available Automations

Automation workflows that can be installed as scheduled tasks. These run automatically via Claude Code's scheduled tasks feature.

**How to install:** Use `mcp__scheduled-tasks__create_scheduled_task` (or ask Claude to "schedule [task name]") with the taskId, cronExpression, description, and prompt from each section.

**Important:** Scheduled tasks are local to your machine. If you switch computers, re-install from this file. Each automation lists its prerequisites — only install ones whose prerequisites you have.

---

## 1. Weekly Review

- **taskId:** `weekly-review`
- **Schedule:** `0 14 * * 5` — Friday 14:00
- **Prerequisites:** None (works with just activity logs)

**What it does:** Reads all activity logs for the week, checks your calendar (if connected), identifies gaps, and compiles a structured review.

**Prompt:**
```
You are running the weekly review for [YOUR_NAME]'s knowledge base.

Read the SOP at `[VAULT_ROOT]/SOPs/weekly-review.md`.

## Steps

1. Read `_templates/project-mapping.md` to get the list of [WORK_UNIT_PLURAL].
2. For each [WORK_UNIT], read `activity-log.md` for this week's entries and check `meetings/` for notes created this week.
3. If Google Calendar is connected, check for meetings this week.
4. Identify gaps — [WORK_UNIT_PLURAL] with known activity but no KB context. Present to [YOUR_NAME] and wait for input.
5. Compile a review summary for each active [WORK_UNIT]:
   - What was done
   - Meeting outcomes
   - Key decisions or changes
   - Next steps
   - Length: 3-5 sentences for high activity, 1-2 for low
6. Present the review for [YOUR_NAME]'s approval.
7. Append review entries to each [WORK_UNIT]'s `activity-log.md`.

This task requires user interaction at steps 4 and 6.
```

---

## 2. Review Nudge

- **taskId:** `review-nudge`
- **Schedule:** `0 8 * * 5` — Friday 08:00
- **Prerequisites:** Google Calendar MCP

**What it does:** Flags [WORK_UNIT_PLURAL] that had calendar activity but no KB context, prompting you to share notes before the weekly review.

**Prompt:**
```
You are running the review nudge for [YOUR_NAME]'s knowledge base.

1. Read `[VAULT_ROOT]/_templates/project-mapping.md` for the [WORK_UNIT] list.
2. Check Google Calendar for this week's external meetings.
3. For each [WORK_UNIT] with meetings, check if `activity-log.md` has entries for this week.
4. Report:

   Ready (have context):
   - [Name] — [what's logged]

   Need context before review:
   - [Name] — meeting on [day] but no KB entries

   No activity this week:
   - [Name list]

5. Remind: "Share any meeting notes or quick summaries for the flagged items, then say 'run weekly review' when ready."
```

---

## 3. Daily Meeting Follow-up

- **taskId:** `daily-meeting-followup`
- **Schedule:** `30 8 * * 1-5` — Weekdays 08:30
- **Prerequisites:** Google Calendar MCP + meeting recorder (Fireflies, Otter, etc.) + Claude in Chrome

**What it does:** Checks yesterday's calendar for external meetings, fetches transcripts via browser automation, creates meeting notes, and drafts follow-up emails.

**Prompt:**
```
You are running the daily meeting follow-up.

1. Use Google Calendar to get yesterday's meetings (if Monday, check Friday). Filter to external meetings using the meeting filter rules in `[VAULT_ROOT]/_templates/project-mapping.md`.

2. For each external meeting, match to a [WORK_UNIT] using the mapping file.

3. For each matched meeting:
   a. Fetch the transcript via Claude in Chrome (navigate to the meeting recording platform)
   b. Create a meeting note at `[WORK_FOLDER]/{name}/meetings/YYYY-MM-DD-{topic}.md`
   c. Update `activity-log.md` with a rich meeting entry
   d. Draft a follow-up email

4. Present results for review.

If no external meetings happened yesterday, report "No external meetings yesterday" and exit.
If browser automation is unavailable, notify [YOUR_NAME].
```

---

## 4. Meeting Prep Briefs

- **taskId:** `meeting-prep`
- **Schedule:** `30 7 * * 1-5` — Weekdays 07:30
- **Prerequisites:** Google Calendar MCP

**What it does:** Generates talking points and context briefs before upcoming meetings.

**Prompt:**
```
Check Google Calendar for external meetings tomorrow and the day after.

Match to [WORK_UNIT_PLURAL] via `[VAULT_ROOT]/_templates/project-mapping.md`.

For each upcoming meeting:
1. Read profile.md, activity-log.md, strategy.md
2. Compile a brief: recent work, open items, talking points, key context
3. Save to `[WORK_FOLDER]/{name}/meetings/YYYY-MM-DD-prep.md`
4. Present the brief in chat

Skip meetings that already have a prep file.
```

---

## 5. Safety Net

- **taskId:** `safety-net`
- **Schedule:** `0 8 * * 3` — Wednesday 08:00
- **Prerequisites:** None (Google Calendar optional but recommended)

**What it does:** Mid-week scan for missing follow-ups, quiet [WORK_UNIT_PLURAL], and orphaned action items.

**Prompt:**
```
Mid-week safety check for [YOUR_NAME]'s knowledge base.

1. Read `[VAULT_ROOT]/_templates/project-mapping.md` for the [WORK_UNIT] list.

2. Gather data (in parallel where possible):
   a. If task manager connected: active tasks per [WORK_UNIT] — collect overdue, severely overdue (14+ days), and stale tasks (no due date, 21+ days old)
   b. If calendar connected: meetings from last 7 days — filter to external meetings
   c. KB meeting notes: glob for files with date prefixes matching last 7 days
   d. KB activity logs: read first 30 lines per [WORK_UNIT] to find most recent entry date

3. Detect problems:
   a. Overdue tasks — group by severity (7+ days = critical, 1-6 = normal)
   b. Meeting action items without tasks — parse Action Items sections from last 14 days, find [YOUR_NAME]-owned items with no matching task
   c. Calendar meetings without KB notes — external meetings from last 7 days with no meeting note file
   d. Calendar meetings without follow-up — meetings missing a follow-up email file
   e. Stale active tasks — 14+ days overdue and still open
   f. Quiet [WORK_UNIT_PLURAL] — no activity-log entries AND no task activity in 14+ days
   g. Pending action items — unchecked `- [ ] [YOUR_NAME]` lines in meeting notes from last 30 days

4. Report grouped by severity: URGENT → NEEDS ATTENTION → REVIEW → SUMMARY.
   If all clear: "No forgotten items found."

5. Offer to create tasks for orphaned action items.
```

---

## 6. Systems Review

- **taskId:** `systems-review`
- **Schedule:** `0 15 * * 5` — Friday 15:00
- **Prerequisites:** None (works with activity logs and automation history)

**What it does:** Analyses your automation usage, identifies patterns in manual work, checks for stale data, and proposes improvements. Keeps the system getting better every week.

**Prompt:**
```
You are running the weekly systems review for [YOUR_NAME]'s knowledge base.

1. Gather data from this week:
   - Activity logs across all [WORK_UNIT_PLURAL] — what work was done
   - Automation outcomes — which scheduled tasks ran, which failed or needed manual intervention
   - Check `_solutions/` for newly documented solutions

2. Read the previous review (if it exists) at `[VAULT_ROOT]/SOPs/systems-improvement-log.md`. Check status of last week's proposals.

3. Analyse patterns:
   - Repetitive manual work that could be automated
   - Automation failures or friction points
   - Coverage gaps — [WORK_UNIT_PLURAL] with activity but thin KB context
   - Stale data — profiles not updated in 60+ days, strategy docs with expired goals

4. Generate proposals in three tiers:
   - Quick fixes (do now) — broken references, stale data, simple improvements
   - Improvements (do this week) — automation enhancements, new workflows
   - Strategic (track for later) — larger initiatives, new integrations

5. Present findings as a structured report with concrete proposals. Compare to last week's metrics.

6. If [YOUR_NAME] approves quick fixes, implement them. Note every change.

7. Update `[VAULT_ROOT]/SOPs/systems-improvement-log.md` with this week's analysis, changes made, open proposals, and metrics.

This task requires user interaction at steps 5-6 (review report, approve fixes).
```
