# Setup: Knowledge Base

Open this file in Claude Code. Claude will interview you and configure everything.

---

## Instructions for Claude

You are setting up a personal knowledge base (KB) system built on Obsidian. This system works for anyone — freelancers, consultants, project managers, developers, designers, lawyers, anyone who manages ongoing work for clients or projects.

**Your approach:** Run this as an interactive setup wizard. Ask questions, wait for answers, then configure. Move through phases one at a time. Be friendly and concise — the user may not be technical.

**Important rules:**
- After each configuration step, briefly confirm what you did
- When replacing placeholders in files, do all files silently in one batch
- If something fails or the user doesn't have a tool, skip gracefully and move on
- Never assume what tools or platforms someone uses — ask
- Keep explanations short. This should feel like a quick onboarding, not a lecture

---

## Phase 1: Who Are You?

Start with a brief welcome:

> "Welcome! This is a knowledge base that helps you keep track of your work. It automatically remembers context about your clients/projects, logs what you do, and helps you prepare for meetings and reviews. Let me set it up for you."

Then ask these questions (you can use AskUserQuestion or ask conversationally):

1. **"What's your first name?"**

2. **"What do you do?"** — Get a short description: freelance designer, account manager, consultant, developer, etc. This helps you tailor examples.

3. **"What do you call the things you manage?"** — Offer options:
   - Clients (freelancers, consultants, agencies)
   - Projects (developers, designers, PMs)
   - Accounts (account managers, sales)
   - Cases (lawyers, support)
   - Or something else

4. **"What language do you mostly work in?"** — English is default. If they say another language, note it — follow-up emails and reviews will be generated in that language.

**After getting answers:**

Detect the vault root path (the directory this file is in).

Replace placeholders across ALL vault files:
- `[YOUR_NAME]` → their first name
- `[VAULT_ROOT]` → the absolute vault path
- `[WORK_UNIT]` → their chosen term, singular (e.g., "client", "project")
- `[WORK_UNIT_PLURAL]` → plural form (e.g., "clients", "projects")
- `[WORK_FOLDER]` → a sensible top-level folder name based on their term (e.g., "Clients", "Projects", "Accounts")

Files that contain placeholders:
- `CLAUDE.md`
- `_templates/project-mapping.md`
- `_templates/_overview.md`
- `_templates/client-profile.md` (rename to match their term if needed, e.g., `project-profile.md`)
- `SOPs/meeting-processing.md`
- `SOPs/weekly-review.md`
- `SOPs/available-automations.md`
- `_setup/settings.local.json`
- `Welcome.md`

Also create the top-level work folder (e.g., `Clients/`) and move `_templates/_overview.md` into it as `_overview.md`.

Confirm: "Done — your KB is set up for [name]. You manage [work units] in the `[folder]/` folder."

---

## Phase 2: Connect Your Tools

Explain: "Now let's see what tools you use. The KB works fine on its own, but connecting tools makes it more powerful."

### 2.1 Check what's already available

Silently check:
- Is Claude in Chrome connected? (try `mcp__Claude_in_Chrome__tabs_context_mcp`)
- Search MCP registry for Google Calendar
- Search MCP registry for Google Drive
- Is Node.js installed? (`node --version`)

### 2.2 Present options

Ask: **"Which of these do you use?"** (multi-select)

- **Google Calendar** — "Lets me detect your meetings, generate prep briefs, and flag missed follow-ups"
- **A meeting recorder** (Fireflies, Otter, Fathom, etc.) — "I can turn recordings into structured notes automatically"
- **A task manager** (Todoist, Linear, Asana, etc.) — "I can create tasks from meeting action items"
- **Google Drive** — "I can access shared documents for context"
- **A CRM** — "I can sync data if your CRM has an API"
- **None of these / I'll add tools later**

### 2.3 Set up selected tools

For each tool they selected:

**Google Calendar:**
1. Search MCP registry for a Google Calendar connector
2. If found, suggest the connector for the user to install
3. After connected, test with a quick calendar query
4. Add MCP permission entries to `.claude/settings.local.json`:
   ```
   "mcp__{uuid}__gcal_list_events",
   "mcp__{uuid}__gcal_get_event",
   "mcp__{uuid}__gcal_list_calendars"
   ```
5. Ask: "Do you have a company email domain? (e.g., @yourcompany.com) This helps me tell the difference between internal and external meetings."
   - If yes: update the meeting filter section in `_templates/project-mapping.md`
   - If no: "No problem — I'll flag all meetings and you tell me which ones are relevant."

**Meeting recorder:**
1. Ask which tool (Fireflies, Otter, Fathom, other)
2. If Claude in Chrome isn't connected: "To automatically fetch transcripts, you'll need the Claude in Chrome browser extension. Want to set it up now?"
   - If yes: guide through Chrome Web Store install → connect to Claude Code → test
   - If no: "No problem — you can always paste transcripts manually instead."
3. Add Claude in Chrome permissions to settings.local.json if connected

**Task manager:**
1. Ask which tool
2. If it has an API (Todoist, Linear): ask if they want to connect it, offer guidance
3. If they provide an API token, add it to `.env`
4. Add relevant permissions to settings.local.json

**Google Drive:**
1. Search MCP registry for Google Drive connector
2. Suggest and guide through connection
3. Add permissions

**CRM:**
1. Ask which CRM and whether it has an API
2. If yes: ask for API details, add to `.env`, add curl permissions to settings.local.json
3. If no: "No problem — the KB itself serves as your source of truth."

**None selected:**
"That's totally fine. The core system works with just the vault — context loading, activity logging, and daily journals all work without any integrations. You can connect tools anytime later."

### 2.4 Install permissions

```bash
mkdir -p .claude
cp _setup/settings.local.json .claude/settings.local.json
```

The placeholders should already be replaced. Add any tool-specific permissions discovered above.

Confirm what was connected.

---

## Phase 3: Add Your [WORK_UNIT_PLURAL]

Ask: **"Let's add your [work units]. What are their names? List as many as you want."**

For each name:

1. Create the folder:
   ```
   [WORK_FOLDER]/{Name}/
   ├── profile.md      (from template, name filled in)
   ├── activity-log.md  (from template)
   ├── strategy.md      (from template)
   └── meetings/
   ```

2. Add to `_templates/project-mapping.md`

3. If a CRM was connected, try to look up the entry and link it

Ask: **"Any short names or nicknames you use for these?"** — add to the aliases table.

Then ask: **"Want to fill in any profiles now, or just do it as you go?"**

- If now: walk through profile.md and strategy.md for their first [WORK_UNIT]
- If later: "No problem. When you mention a [work unit] by name, I'll load whatever context exists. You can fill things in naturally as you work."

---

## Phase 4: Automations

Explain: "The KB has automations that can run on a schedule. I'll only show you ones that work with the tools you've connected."

Build the list dynamically based on Phase 2:

**Always available:**
- Weekly Review — "Every Friday, compiles a summary of your week's work across all [work units]"
- Safety Net — "Mid-week check for forgotten follow-ups and quiet [work units]"

**Available if Google Calendar connected:**
- Review Nudge — "Friday morning heads-up: which [work units] need context before the weekly review"
- Meeting Prep Briefs — "Auto-generates talking points before upcoming meetings"

**Available if calendar + meeting recorder + Claude in Chrome:**
- Daily Meeting Follow-up — "Each morning, processes yesterday's meetings into notes and drafts follow-up emails"

Present only the relevant ones. Ask: **"Which of these would you like to activate?"**

For each selected: read the prompt from `SOPs/available-automations.md`, replace remaining placeholders, install with `mcp__scheduled-tasks__create_scheduled_task`.

If they choose none: "The core auto-behaviors (context loading, activity logging, daily journal) work without any scheduled tasks. You can add automations later."

---

## Phase 5: Quick Test

"Let's make sure everything works."

### Test 1: Context loading
Pick one of their [WORK_UNIT_PLURAL]: "Let me try loading [name]'s context..."
Read the profile.md. Confirm: "Context loaded. I can see [name]'s profile."

### Test 2: Connected tools
For each connected tool, run a quick test:
- Calendar: fetch today's events
- CRM: run a test query
- Claude in Chrome: test the connection

Report results.

### Test 3: First journal entry
Write to `_daily/YYYY-MM-DD.md`:
```
### HH:MM — Knowledge Base Setup
- What: Set up KB with [X] [work units] and [Y] connected tools
- Next: Fill in profiles, process first meeting
```

---

## Phase 6: You're All Set

Summarize:

```
Setup complete!

Name: [name]
[WORK_UNIT_PLURAL]: [list]
Connected tools: [list, or "None yet"]
Active automations: [list, or "None yet"]

How to use this day-to-day:
- Mention a [work unit] name → I load their context
- After work → I log what was done
- Share a meeting link or transcript → I create structured notes
- Say "weekly review" on Fridays → I compile a summary

Tips:
- The more you use it, the better it gets — activity logs build up over time
- Rich entries ("did X because Y, next step is Z") make better reviews
- Check SOPs/available-automations.md anytime to add more automations
```

---

## Reference: File Map

| File | Purpose |
|------|---------|
| `CLAUDE.md` | Core automation rules |
| `_templates/project-mapping.md` | Name → folder mapping |
| `_templates/client-profile.md` | Profile template |
| `_templates/strategy.md` | Strategy template |
| `_templates/activity-log.md` | Activity log template |
| `_templates/meeting-note.md` | Meeting note template |
| `SOPs/meeting-processing.md` | Meeting → KB workflow |
| `SOPs/weekly-review.md` | Weekly review process |
| `SOPs/available-automations.md` | Automation catalog |
| `_setup/settings.local.json` | Permission whitelist template |
| `.env.example` | Credential template |
| `Welcome.md` | Quick-start guide |
