---
name: end-of-day
description: "Closes a work session: closes finished tasks, captures open ones, updates project state and writes the daily log. Use when the user wraps up, says end of day, goodnight or 'sent', or a session ends."
compatibility: "Claude Code, Cowork, claude.ai · needs read/write access to the vault folder; a task-manager connector (e.g. Todoist) is optional · Sonnet or Opus"
---

# End of Day

If `personal.md` exists in this folder, read it first; it overrides the defaults above.

Two jobs, in this order:

1. **Open items go to their home.** Anything the session finished gets closed; anything it left unresolved becomes or updates a tracked item. Nothing should depend on a log being re-read to stay alive.
2. **The daily log carries the narrative.** What was worked on, what was built or changed, what went out, where to pick up. It is a record, not a task list.

Without the log, the next session starts with no memory of today. Without step 1, the work that matters most quietly disappears.

## When to use

- The user says "end of day", "wrap up", "we're done", "that's it for today", "log today", "log this", "done for the day", "goodnight" or "shutdown".
- The user replies "sent" (or similar) after sending something drafted in the session.
- Automatically, when a session that changed something in the vault is closing. Run silently (see below).

## Language

Follow the language rule in `CLAUDE.md` (its `## Language` block, if it has one). Don't restate or reinterpret it here.

## Where open items live

`CLAUDE.md` decides:

- **Task-manager vault** — `CLAUDE.md` says tasks live in a task manager (e.g. Todoist through its connector). Open items go there. Logs carry no `### Still Open` section, and a project hub's `## Tasks` section is only a pointer.
- **Plain vault** — no task manager. Open items go in the log's `### Still Open` section, and in the hub's `## Tasks` section if it has one. `good-morning` carries them forward from there.

## The log is a byproduct, not a ritual

Don't wait for the end of the day. **Any session that changed something in the vault gets logged**: files created or edited, a decision taken, a plan advanced. Write the lines when the work is done, not when the user remembers to ask.

A session that only read, searched or discussed writes no log. Gaps are correct: a missing day means there was no vault work. Nothing to catch up on.

## Checklist

```
- [ ] 1. Close what the session finished (completion signals, verification pass)
- [ ] 2. Capture what it left open
- [ ] 3. Update project state: hubs, MEMORY.md
- [ ] 4. Work out the log date; write or append the log section
- [ ] 5. Verify (Step 5); return to the failing step if anything is off
- [ ] 6. Confirm in two lines (explicit request only)
```

## Conversation-close behaviour (automatic, silent)

When triggered by a conversation closing rather than an explicit request:

1. Run Steps 1–2 first. **They run even when there is nothing to log**: a discussion that ended with an agreed next action still produces that item.
2. If the conversation was purely exploratory with no output or decisions, stop there.
3. Otherwise run Steps 3–5. If today's log already covers this conversation and there is nothing new, skip the write.
4. Ask nothing and confirm nothing. Use judgement; when unsure whether something earns a task, don't create it.

## Which day — the after-midnight rule

Sessions often cross midnight, and logging by wall-clock date splits one session across two files. **Between 00:00 and 05:59, write to the previous calendar day's log.** From 06:00, use today.

```bash
# Log date. Tries BSD date (macOS) first, falls back to GNU date (Linux).
if [ "$(date +%H)" -lt 06 ]; then
  date -v-1d +%F 2>/dev/null || date -d yesterday +%F
else
  date +%F
fi
```

It applies to the filename, the `date:` frontmatter and the `# Session Log —` heading. If it is before 06:00 and a log already exists for the current calendar date, that file is misdated: merge its content into the previous day's log and remove the misdated file (ask the user if deletion is blocked).

## Step 1 — Close what the session finished

Re-read the conversation from the top looking for completion signals. People mark things done in passing, in very few words, and expect that to count. Treat these as "finished, close it":

- `sent` · `sent it` · `emailed` · `shared it` · `forwarded` · `replied` · `submitted` · `uploaded` · `filed it` · `signed` · `approved` · `paid` · `booked` · `ordered`
- `done` · `finished` · `handled` · `sorted` · `taken care of` · `closed it` · `no longer needed`
- The same words in the user's other language (e.g. Arabic `تم`, `ارسلته`, `خلصت`, `انرسل`).
- Past tense about their own action inside a longer message ("I sent the letter this morning, now about the dashboard…"). The clause counts even when it wasn't the point of the message.

For each signal, find the matching item (search by keyword across all projects, not just today's dated items; it may be undated or overdue) and close it. If no item exists, that's fine: it still goes in the log.

Don't leave an item open, reschedule it or mark it waiting on the strength of a completion signal. The user's part is done; that closes it.

**Sent, with a reply expected.** Sending is a completion, not a wait. Close the item first. On an explicit trigger, ask (in the Step 2 question pass) whether they want a follow-up; create a waiting item with a chase date only on a yes. On an automatic trigger, create nothing and let the log carry it.

**Verification pass.** List every completion signal you found and confirm each ended in either a closed item or a deliberate "no item existed". A missed close is the failure this step exists to prevent.

## Step 2 — Capture what the session left open

Every genuinely unresolved item (mid-flight work, a decision not taken, something waiting on a person, a next action named in the conversation) must have a home by the end of this step.

**Be selective.** An item is a next action the user personally takes, roughly in one sitting, not already tracked elsewhere. Project phases are not items. Checklists inside a document are content and stay there. A review that produced eleven findings produces **one** item, the next move. When in doubt, don't create it: an unnecessary item costs more than a missing one, because the user has to read and dismiss it. Don't create one for something the user explicitly dropped.

**Keep it short.** Verb plus object, under ten words. Detail stays in the vault, linked by name.

**Dates only for a reason.** Set a due date only when there is an external deadline, the user named the day, or it is a waiting item and the date is when chasing becomes fair. Undated is the normal state. Never date an item to make it visible, and never roll a lapsed date forward just because it lapsed.

**In a task-manager vault:**

- If an item already covers the thread, update it; don't add a second. Use the connector's reschedule tool to move a date if it has one (a full due-string update can destroy recurrence). If an update replaces the whole label set, send the full set. Never send an item's existing project or section back on update; many connectors treat that as a move.
- Otherwise add it to the right project and section; look sections up rather than guessing names.
- Mark status with the vault's labels (in progress, waiting plus the person it waits on, deferred tiers). Don't invent new labels without asking.
- An older log with a `### Still Open` or `## Carried Forward` section: migrate it once when you open it. Close what is done, add what is still open and not yet tracked, then delete the section.

**In a plain vault:** the item goes in this session's `### Still Open` (Step 4), and in the hub's `## Tasks` if it has one.

**Ambiguous items.** On an explicit trigger, put every ambiguous item (done or open? task or not? which day? follow up that send?) in **one** short question with the items as a list. The user is finishing their day, not starting a review. On an automatic trigger, ask nothing.

## Step 3 — Update project state

**Project hubs.** For each file the session created or edited inside a project folder:

1. Its frontmatter carries `project: "[[PROJ <Project Name> Overview]]"`. Add it if missing.
2. The hub's `## Key Files` table has a row for it: `| [[Filename Without Extension]] | <what this file is for> |`. Add it if missing, deleting the `_No files yet_` placeholder with the first row.
3. If you touched the hub, set its `update date` to the log date.

This is a safety net; the row should have been added when the file was created. Add only what is missing; never rewrite an existing row.

**`MEMORY.md`** records what is true *now*; the log records what happened. For each project whose state changed this session (status, a new blocker, a different next action):

- **If `MEMORY.md` says it is generated** (from `## State` blocks or similar): update the source block in the project note, then run the generator its header names. Never edit `MEMORY.md` by hand. If the generator can't run here, update the blocks and say a rebuild is needed.
- **Otherwise**, write or replace the project's block by hand:
  - Replace, don't append. One block per project, saying what is true now.
  - Five lines at most. A sixth line worth having means one of the five has stopped being worth having.
  - State, never events. "Scope settled: two vendors shortlisted" is state; "wrote the scope note" is an event and belongs in the log.
  - Delete a line the moment it stops being true, and the whole block when the project closes. Remove the `Nothing here yet.` placeholder when the first block lands.
  - No frontmatter, wikilinks or `project` key. `MEMORY.md` is read every session, so its length is a recurring cost.

## Step 4 — Write the log

Pull out what matters:

- **What we worked on** — which tasks were tackled.
- **What was built or changed** — files created or edited, decisions made.
- **Sent / Dispatched** — anything that left the user's desk: emails, letters, submissions, approvals, payments. One line each: what, to whom, the date. Include it whether or not an item existed. This is the record for "when did you send that?" Omit the heading if nothing went out.
- **Still Open** — plain vaults only, and only if something is genuinely unresolved. Never invent open items.
- **Start here next time** — one or two sentences on the best place to pick up.

Write it like a note to a colleague taking over the shift: enough to orient them fast, not an essay.

Save to `01 Daily Logs/YYYY-MM-DD.md` at the workspace root, using the log date from the after-midnight rule. The user may work in several conversations a day, so the file may already exist:

- **Doesn't exist:** create it with the header and this project's section.
- **Exists:** read it first. If this project already has a section, append within it; otherwise append a new section at the bottom. Never recreate the frontmatter or the top-level heading.

Each project gets its own `## <Project Name>` section, so `good-morning` can scan the whole day from one file. By default the log is terminal: no `project` key, no `description`, and no wikilinks, so nothing in it can break when a note is renamed.

New file:

```markdown
---
author: claude
type: log
date: YYYY-MM-DD
---

# Session Log — [Weekday, Month DD YYYY]

## <Project Name>

### What We Worked On
- [task — what was done]

### What Was Built or Changed
- [specific file or decision]

### Sent / Dispatched
- [what went out — to whom — date]

### Still Open
- [plain vaults only: what is mid-flight]

### Start Here Next Time
[1–2 sentences on the best place to pick up]
```

Appending: the same `## <Project Name>` section, with no frontmatter and no top-level heading.

## Step 5 — Verify before finishing

Re-read what you wrote and check:

- Every completion signal from Step 1 ended in a closed item or a deliberate "no item existed".
- Every unresolved item has a home (task manager, or `### Still Open` in a plain vault), and no `### Still Open` section was written in a task-manager vault.
- The log file name, `date:` and heading all use the after-midnight log date.
- An existing log kept its frontmatter and heading, and gained no duplicate section.
- `MEMORY.md` was changed only the way Step 3 allows.

If any check fails, return to the step that produced it, fix it, and check again.

## Step 6 — Confirm (explicit request only)

In one or two lines: **name** the items closed (not just a count, so a wrong close is easy to spot), how many were created or updated, anything now overdue by 3+ days, where the log was saved, and the "Start here" line. They're done for the day; keep it short.

On an automatic trigger, skip this step.
