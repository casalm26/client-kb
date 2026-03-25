# SOP: Meeting Processing

## Purpose

Turn meeting recordings, transcripts, or notes into structured KB data. This is the primary way meeting context enters the knowledge base.

## When

Whenever [YOUR_NAME] shares a meeting link, pastes a transcript, or says "I had a meeting with [name]."

## Supported Sources

- **Fireflies.ai** — Extract via Claude in Chrome browser automation
- **Otter.ai** — Extract via Claude in Chrome or pasted transcript
- **Manual notes** — [YOUR_NAME] dictates or types key points
- **Any transcript** — Pasted text from any recording tool

## Workflow

### Step 1: Extract Meeting Data

From whatever source is available, extract:

- Attendees and their roles
- Key discussion topics
- Decisions made
- Action items (grouped by owner)
- Sentiment and relationship context
- Any data or metrics discussed
- Strategic direction or changes discussed

### Step 2: Create Meeting Note

Save to `[WORK_FOLDER]/{name}/meetings/YYYY-MM-DD-topic.md` using the meeting note template:

```markdown
---
name: [Name]
date: YYYY-MM-DD
attendees: [names]
source: [Fireflies/Otter/Manual/etc.]
---

# Meeting: [Topic]

## Key Takeaways
- [3-5 most important points]

## Decisions Made
- [Specific decisions]

## Action Items
- [ ] [Owner] — [Task] — [Deadline]

## Notes
[Detailed summary]
```

### Step 3: Update Activity Log

Add a rich entry to `activity-log.md`:

```markdown
## YYYY-MM-DD
**Meeting with [contact name]: [topic]**

[Include: what was discussed, sentiment, decisions made, relevant data/context, and next steps. Write it so someone reading just this entry has enough context for a weekly review.]
```

### Step 4: Update Strategy (if applicable)

If the meeting included strategic decisions (new goals, changed priorities, metric updates, scope changes), update `strategy.md`.

### Step 5: Generate Follow-up (if requested)

Draft a follow-up email summarizing the meeting:
- Thank you for the meeting
- Summary of key points
- Action items for both parties
- Next steps

## Why Rich Activity-Log Entries Matter

The weekly review compiles summaries primarily from activity-log entries. A thin entry like "Had a meeting" produces a thin review. A rich entry like:

> Met with the client. Project is on track, they're happy with the prototype. Decided to push the launch from March to April to allow for more user testing. Budget stays the same. Next step: schedule user testing sessions and share the revised timeline by Friday.

...produces a review that captures what actually happened.
