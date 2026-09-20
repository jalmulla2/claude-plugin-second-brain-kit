---
name: end-of-day
description: Writes the daily log entry for a work session, and updates MEMORY.md with what is still true, so the next Claude session can pick up where things left off. Trigger whenever the user says "end of day", "wrap up", "we're done", "that's it for today", "log today", "done for the day", "goodnight", "shutdown", or anything that signals they're finishing a work session. Also triggers automatically at the end of any session that changed something in the vault, and when a conversation is closing — silently write or update the daily log without asking the user. Sessions run past midnight, so the log date is not always today's date.
---

# End of Day

This skill writes a handoff note from the current session — something Claude can read at the start of the next session to quickly understand what happened and where to pick up.

## Why this matters

Without this log, the next Claude session starts with no memory of what you did today. This note is what bridges sessions together and keeps momentum going.

## The log is a byproduct, not a ritual

Do not wait for the end of the day. **Any session that changed something in the vault gets logged** — files created or edited, a decision taken, a plan advanced. Write the 2–4 lines when the work is done, not when the user remembers to ask.

A session that only read, searched or discussed writes nothing. Gaps in the log are correct: a log exists for days with vault work, and a missing day means there was none. Nothing to catch up on.

## Language

Follow the `## Language` block in `CLAUDE.md`. Do not restate or reinterpret the rule here.

## Conversation-Close Behaviour (automatic, silent)

When this skill is triggered by a conversation closing (not an explicit user request), run silently:

1. Scan the conversation for meaningful work — if the conversation was purely exploratory chat with no real output or decisions, skip logging entirely.
2. Work out which day's log to write to (see "Which day" below), and check whether it exists.
3. If it **does not exist** — create it (full format in Step 3).
4. If it **does exist** — read the existing content first. If this conversation's project/topic is already covered, check whether there is new content to add (new tasks done, new files built, new open items). If so, append to the existing section or add a new section. If nothing new, skip.
5. Reconcile the project hubs (Step 2) and update `MEMORY.md` (Step 4) either way — both run whether the log was created, appended to, or skipped for having nothing new.
6. Do NOT ask the user questions. Do NOT confirm with the user. Just write and close.

## Which day — the after-midnight rule

Work sessions sometimes cross midnight. Logging strictly by wall-clock date would split one continuous session across two files.

**If the current time is between 00:00 and 05:59, write to the *previous* calendar day's log.** From 06:00 onwards, use today's date.

```bash
# The log date, accounting for after-midnight sessions.
# Tries BSD date (macOS) first, falls back to GNU date (Linux).
if [ "$(date +%H)" -lt 06 ]; then
  date -v-1d +%F 2>/dev/null || date -d yesterday +%F
else
  date +%F
fi
```

Applies to both the filename and the `date:` frontmatter.

## Step 1 — Review the session

Look back through the conversation and pull out what matters:

- **What we worked on** — which tasks were tackled in this session
- **What was built or changed** — specific files created or edited, decisions made
- **Still open** — anything mid-flight, unresolved, or left for later
- **Start here next time** — 1–2 sentences on the best place to pick up next session

Write it like a note to a colleague taking over the shift — enough to orient them fast, not so much they have to read an essay. If nothing is clearly "still open," skip that section. Don't pad.

Note: Tasks live inside each project's own hub file under a `## Tasks` section, if it has one. There is no central task list — don't create or look for one. The hub's other sections are `## Key Files` and `## Links Out`; don't invent a second home for either.

## Step 2 — Reconcile the project hubs

You just listed, under "What was built or changed", the files this session created or edited. For each of those that lives inside a project folder:

1. Check its frontmatter carries `project: "[[PROJ <Project Name> Overview]]"`. Add it if missing.
2. Check the project hub's `## Key Files` table has a row for it. If not, add:
   `| [[Filename Without Extension]] | <short phrase: what this file is for> |`
   Delete the `_No files yet_` placeholder line when you add the first row.
3. If you touched the hub, set its `update date` to the log date.

This is a safety net, not the primary mechanism — the row should already have been added when the file was created, per `## Project Files` in `CLAUDE.md`. Add only what is missing. Never rewrite an existing row, and never add a row for a file this session didn't touch.

**The log itself gets no links.** Don't put a `project` key in the log's frontmatter and don't put a `[[wikilink]]` anywhere in it. A log names a project with its `## <Project Name>` heading, in plain text. Logs are terminal: they point at nothing, so nothing in them can go stale or break when a note is renamed.

## Step 3 — Save the log

Save to:

```
01 Daily Logs/YYYY-MM-DD.md
```

where `YYYY-MM-DD` is the log date from the after-midnight rule above — **not** blindly today's date.

Note: `01 Daily Logs/` is at the **root** of the workspace.

**Important:** the user may work on different projects in separate conversations throughout the day. The same day's log file may already exist. Always check first:

- **If the file does NOT exist** — create it with the full header and the project section.
- **If the file ALREADY exists** — open it and read it. Then:
  - If this project/topic **already has a section**, check for new content and append within that section if needed.
  - If this project/topic **does not have a section yet**, append a new `## [Project Name]` section to the bottom.
  - Never recreate the frontmatter or top-level heading.

Each project session gets its own clearly labeled `## [Project Name]` section so good-morning can scan all projects from a single file.

### Format for a NEW file:

Logs use this reduced frontmatter deliberately — no `project`, no `description`. See `## Project Files` in `CLAUDE.md` for why.

```markdown
---
author: claude
type: log
date: YYYY-MM-DD
---

# Session Log — [Weekday, Month DD YYYY]

## [Project Name]

### What We Worked On
- [task — what was done]
- [task — what was done]

### What Was Built or Changed
- [specific file or decision]

### Still Open
- [thing that's mid-flight or unresolved]

### Start Here Next Time
[1–2 sentences on the best place to pick up next session]
```

### Format when APPENDING to an existing file:

Add only the project section — no new frontmatter or top-level heading:

```markdown

## [Project Name]

### What We Worked On
- [task — what was done]

### What Was Built or Changed
- [specific file or decision]

### Still Open
- [thing that's mid-flight or unresolved]

### Start Here Next Time
[1–2 sentences on the best place to pick up next session]
```

Only include "Still Open" if there's actually something unresolved. Don't invent open items.

## Step 4 — Update MEMORY.md

The log records what happened. `MEMORY.md` records what is **true now**. They are different files because they answer different questions, and the log alone can't answer the second one — reconstructing current state means reading every log backwards and working out what a later one cancelled.

Open `MEMORY.md` at the workspace root. For each project this session touched, write or replace its block:

```markdown
## [Project Name]
- [something still true and still undecided]
- [a decision taken that later work depends on]
- [what is blocking, and on whom]
```

Rules that keep it from turning into a second log:

- **Replace the block, don't append to it.** A project has one block, and it says what is true now. If nothing about the project's state changed this session, leave its block alone.
- **Five lines at most per project.** If a sixth line is worth having, one of the five has stopped being worth having — delete that one.
- **State, never events.** "Scope settled: autogenous and bacterial only, 2010+" is state. "Wrote the scope note" is an event and belongs in the log. If a line would read naturally under a `### What We Worked On` heading, it's in the wrong file.
- **Delete a line the moment it stops being true.** A stale line is worse than a missing one — `good-morning` reads this file and will act on it.
- **Delete the whole block when the project closes.**
- Remove the `Nothing here yet.` placeholder when the first block lands.

Do not add frontmatter, wikilinks or a `project` key. `MEMORY.md` is read at the start of every session, so its length is a recurring cost — the five-line cap is the whole point of the file.

## Step 5 — Confirm (explicit user request only)

If triggered by the user explicitly, tell them where the file was saved and give them the "Start Here Next Time" line so they know the handoff is solid. Keep it to one or two lines — they're done for the day.

If triggered automatically on conversation close, skip this step entirely.
