# Setup Wizard

This file is read by Claude, not by the user. CLAUDE.md auto-triggers this when the system is unconfigured.

---

## Behavior Rules

Follow these strictly throughout the entire wizard:

1. **One question at a time.** Never ask multiple things in one message. Use the AskUserQuestion tool for structured choices. Use plain chat only for free-text input (like names).
2. **Show progress.** Use TodoWrite at the start of the chosen flow. Mark tasks complete as you go.
3. **Validate immediately.** After connecting any tool, test it right away and report the result.
4. **Be concise.** Short sentences. No lectures. If you can say it in one line, don't use three.
5. **Skip gracefully.** If something fails or the user doesn't have a tool, say "Skipping — you can add this later" and move on. Never block the wizard on a failure.
6. **Batch silent work.** When replacing placeholders or creating files, do it all at once without narrating each file. Just confirm the result.
7. **Re-entry safe.** If some placeholders are already replaced (partial setup), skip completed steps. Check before replacing.

---

## Entry Point

When triggered by CLAUDE.md, start with this welcome message:

> Welcome! This vault is a knowledge base that works with Claude Code. It automatically remembers context about your work, logs what you do, and helps you stay on top of meetings and reviews.
>
> Let's get you set up.

Then immediately ask the mode question:

**Use AskUserQuestion:**
- Question: "How would you like to set up?"
- Options:
  - **Quick setup** — "Sensible defaults, done in 2 minutes. English, 'Clients' folder, no integrations. You can customize later."
  - **Custom setup** — "I'll ask what you do, what tools you use, and tailor everything. Takes 5-10 minutes."

---

## Quick Path

If Quick setup is chosen:

### Progress
Create TodoWrite:
```
□ Set up your profile
□ Configure your vault
□ Ready to go
```

### Step 1: Name
Ask in chat: "What's your first name?"

Mark "Set up your profile" as in_progress.

### Step 2: Apply defaults
Read `_setup/quickstart-defaults.md` for the default values.

Detect the vault root (the directory containing this file's parent — i.e., the repo root).

Replace all placeholders across every file in the vault:
- `[YOUR_NAME]` → their name
- `[VAULT_ROOT]` → detected absolute path
- `[WORK_UNIT]` → "client"
- `[WORK_UNITS]` → "clients"
- `[WORK_UNIT_PLURAL]` → "clients"
- `[WORK_FOLDER]` → "Clients"
- `[WORK_UNIT_PLURAL]` → "Clients" (in headers/titles context)

Files to update: CLAUDE.md, _templates/project-mapping.md, _templates/_overview.md, _templates/client-profile.md, _templates/solution.md, SOPs/meeting-processing.md, SOPs/weekly-review.md, SOPs/available-automations.md, _setup/settings.local.json

Mark "Set up your profile" as completed.

### Step 3: Create structure
Mark "Configure your vault" as in_progress.

- Create `Clients/` directory
- Create `_solutions/` directory (for solution documentation)
- Copy `_templates/_overview.md` → `Clients/_overview.md` (with their name and "Clients" filled in)
- Copy `_setup/settings.local.json` → `.claude/settings.local.json` (create `.claude/` if needed)
- Create `.env` from `.env.example`

Mark "Configure your vault" as completed.

### Step 4: Done
Mark "Ready to go" as completed.

Send:
> You're all set, [name]!
>
> **How to use this:**
> - Mention a client name and I'll load their context
> - After I do meaningful work, I'll log it automatically
> - Share a meeting transcript and I'll turn it into structured notes
> - Say "weekly review" on Fridays for a summary
>
> **To add clients:** Say "add clients" and give me names — I'll create the folders.
> **To connect tools:** Say "add tools" anytime to connect Google Calendar, meeting recorders, or task managers.

---

## Custom Path

If Custom setup is chosen:

### Progress
Create TodoWrite:
```
□ Set up your profile
□ Configure vault structure
□ Connect your tools
□ Add your [work units]
□ Choose automations
□ Verify everything works
```

---

### Phase 1: Profile

Mark "Set up your profile" as in_progress.

**Step 1.1 — Name**
Ask in chat: "What's your first name?"

**Step 1.2 — Work type**
Use AskUserQuestion:
- Question: "What do you manage in your day-to-day work?"
- Options:
  - **Clients** — "Freelancers, consultants, agencies — you serve clients"
  - **Projects** — "Developers, designers, PMs — you run projects"
  - **Accounts** — "Sales, account management — you manage accounts"
  - (Other will be available as free text)

Map the answer:
- Clients → WORK_UNIT="client", WORK_FOLDER="Clients"
- Projects → WORK_UNIT="project", WORK_FOLDER="Projects"
- Accounts → WORK_UNIT="account", WORK_FOLDER="Accounts"
- Other → use whatever they type, derive folder name by capitalizing

**Step 1.3 — Language**
Use AskUserQuestion:
- Question: "What language should generated content be in? (Follow-up emails, reviews, etc.)"
- Options:
  - **English (Recommended)**
  - **Swedish**
  - (Other as free text)

Note the language — if not English, add an instruction to CLAUDE.md's auto-log section specifying the language for generated content.

**Step 1.4 — Apply configuration**
Detect vault root. Replace all placeholders across all files (same list as Quick path, but with their chosen values).

If not English, append to CLAUDE.md after the Data Sources section:
```markdown

## Language

Generate all client-facing content (follow-up emails, weekly reviews, meeting summaries) in [chosen language]. Internal KB entries (activity logs, meeting notes) can mix English and [chosen language] as natural.
```

Mark "Set up your profile" as completed.

Confirm: "Profile configured for [name] — you manage [work units]."

---

### Phase 2: Tools

Create `_solutions/` directory if it doesn't exist.
Copy `_setup/settings.local.json` → `.claude/settings.local.json` (create `.claude/` if needed).
Create `.env` from `.env.example`.
Mark "Configure vault structure" as completed.
Mark "Connect your tools" as in_progress.

**Step 2.1 — Detect existing connections**
Silently check:
- Claude in Chrome: try `mcp__Claude_in_Chrome__tabs_context_mcp`
- Google Calendar: `mcp__mcp-registry__search_mcp_registry` with ["google", "calendar"]
- Google Drive: `mcp__mcp-registry__search_mcp_registry` with ["google", "drive"]
- Node.js: `node --version`

**Step 2.2 — Show status and ask**
Present what's detected, then ask:

Use AskUserQuestion (multi-select):
- Question: "Which of these do you use? I'll connect them now. (Select all that apply)"
- Options:
  - **Google Calendar** — "Meeting detection, prep briefs, and follow-up reminders"
  - **Meeting recorder** — "Fireflies, Otter, Fathom, etc. — auto-process recordings into notes"
  - **Task manager** — "Todoist, Linear, Asana — create tasks from action items"
  - **None right now** — "Skip this — the KB works fine without integrations"

**Step 2.3 — Connect each selected tool**

**Google Calendar:**
1. Search MCP registry
2. If found and not connected → `mcp__mcp-registry__suggest_connectors`
3. Wait for user to connect
4. Test: fetch this week's events
5. Report: "✓ Google Calendar connected — I can see [N] events this week"
6. Add MCP permissions to `.claude/settings.local.json` (detect UUID from the connected tool)
7. Ask: "Do you have a company email domain? (e.g., @yourcompany.com) This helps me identify external meetings."
   - If yes → update meeting filter in `_templates/project-mapping.md`
   - If no → "I'll flag all meetings and you tell me which are relevant."

**Meeting recorder:**
1. Ask: "Which recording tool do you use?" (free text or AskUserQuestion: Fireflies / Otter / Fathom / Other)
2. Check if Claude in Chrome is connected
3. If not connected: "To auto-fetch transcripts, you need the Claude in Chrome browser extension. Want to set it up?"
   - If yes: guide install → test → add permissions
   - If no: "No problem — you can paste transcripts manually. I'll still create structured notes."
4. Report result

**Task manager:**
1. Ask: "Which task manager?" (free text)
2. If Todoist: "Grab your API token from Settings > Integrations > Developer. Paste it here."
   - Add to `.env`
   - Add curl permission to settings.local.json
   - Test: fetch projects
   - Report: "✓ Todoist connected — [N] projects found"
3. Other: "I don't have a built-in integration for [tool], but you can always ask me to create tasks and I'll tell you what to add."

**None selected:**
"No problem. The core system works great on its own — context loading, activity logging, and the daily journal all work without integrations. You can always say 'add tools' later."

Mark "Connect your tools" as completed.

---

### Phase 3: Add Work Units

Mark "Add your [work units]" as in_progress.

Ask in chat: "Let's add your [work units]. What are their names? List as many as you want, or say 'skip' to add them later."

If they provide names:
1. For each name:
   - Create `[WORK_FOLDER]/{Name}/` with profile.md, activity-log.md, strategy.md, meetings/
   - Fill in the `{{title}}` placeholders with the name
   - Add to `_templates/project-mapping.md`

2. Ask: "Any short names or nicknames you use for these? (e.g., 'Acme' for 'Acme Corporation')"
   - If yes → add to aliases table

3. Ask using AskUserQuestion:
   - Question: "Want to fill in any profiles now?"
   - Options:
     - **Yes, let's do one** — "I'll walk you through the first one as an example"
     - **No, I'll do it as I go** — "When you mention a name, I'll load whatever context exists"

   If yes: walk through profile.md for their first work unit — ask about overview, contacts, tools, current status.

If they say "skip":
"No problem. Just say 'add clients' (or whatever their term is) anytime."

Mark "Add your [work units]" as completed.

---

### Phase 4: Automations

Mark "Choose automations" as in_progress.

Explain briefly: "The KB can run tasks automatically on a schedule — like compiling a weekly review every Friday. I'll only show ones that work with your connected tools."

Build the list based on what was connected in Phase 2:

**Always show:**
- Weekly Review (Friday 14:00) — "Compiles a summary of your week's work"
- Safety Net (Wednesday 08:00) — "Catches forgotten follow-ups and quiet [work units]"
- Systems Review (Friday 15:00) — "Weekly analysis of what's working and what to improve"

**Show if Google Calendar connected:**
- Review Nudge (Friday 08:00) — "Flags [work units] that need context before the weekly review"
- Meeting Prep (Weekday mornings) — "Generates talking points before meetings"

**Show if calendar + recorder + Claude in Chrome:**
- Daily Meeting Follow-up (Weekday 08:30) — "Processes yesterday's meetings into notes and emails"

Use AskUserQuestion (multi-select):
- Question: "Which automations do you want? (You can add more anytime)"
- Show only the relevant options from above
- Include: **None for now** — "I'll use the KB manually for a while first"

For each selected:
1. Read the prompt from `SOPs/available-automations.md`
2. Install with `mcp__scheduled-tasks__create_scheduled_task`
3. Confirm: "✓ [Name] — runs [schedule]"

Mark "Choose automations" as completed.

---

### Phase 5: Verify

Mark "Verify everything works" as in_progress.

**Test 1: Context loading**
Pick one of their work units (or use a hypothetical if none were added).
"Let me test context loading..." → read the profile.md → "✓ I can load [name]'s context."

**Test 2: Connected tools** (for each)
- Calendar: "✓ Calendar — [N] events this week"
- Claude in Chrome: "✓ Browser — connected"
- Task manager: "✓ [Tool] — connected"
- Or "Skipped — no tools connected" if none

**Test 3: Daily journal**
Write to `_daily/YYYY-MM-DD.md`:
```
### HH:MM — Knowledge Base Setup
- What: Set up KB with [N] [work units], [N] tools connected, [N] automations
- Next: Fill in profiles as you go
```
"✓ Daily journal works."

Mark "Verify everything works" as completed.

---

### Summary

```
All done, [name]!

[WORK_UNIT_PLURAL]: [list or "none yet — say 'add [units]' to create some"]
Tools: [list or "none — say 'add tools' anytime"]
Automations: [list or "none — say 'add automations' anytime"]

Day-to-day:
- Mention a [work unit] → I load their context
- After work → I log what was done
- Share a meeting link → I create structured notes
- "weekly review" on Fridays → I compile a summary
- "reconfigure" → change settings or add tools
```

---

## Re-entry

When triggered by CLAUDE.md for reconfiguration:

1. Check what the user asked for:
   - "add tools" / "connect tools" → run Phase 2 only
   - "add [work units]" → run Phase 3 only
   - "add automations" → run Phase 4 only
   - "reconfigure" / "change settings" → offer choice of which phase to re-run

2. Use AskUserQuestion if "reconfigure":
   - Question: "What do you want to change?"
   - Options:
     - **Connect more tools**
     - **Add [work units]**
     - **Change automations**
     - **Full reconfigure** — "Start the setup from scratch"
