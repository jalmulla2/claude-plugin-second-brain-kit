---
name: good-morning
description: "Starts a work session: reads recent logs, MEMORY.md and open tasks, leads with what is still open, then recommends what to work on. Use when the user says good morning or starts their day."
compatibility: "Claude Code, Cowork, claude.ai · needs read access to the vault folder; a task-manager connector (e.g. Todoist) is optional · Sonnet or Opus"
---

# Good Morning

If `personal.md` exists in this folder, read it first; it overrides the defaults above.

Orients a new session: what is still open, what happened lately, and one clear call on what to do next. Open items come first, because they are the only part of the briefing that needs a decision today; finished work is context.

## When to use

- The user says "good morning", "morning", "let's get to work", "ready to start", "start my day", or "what should I work on?".
- The user opens a session with a greeting and nothing else. Run this before doing anything else.
- Not for a mid-day question about one project. Answer that directly.

## Language

Follow the language rule in `CLAUDE.md` (its `## Language` block, if it has one). Don't restate or reinterpret it here.

## Checklist

Copy this into your working notes and tick it off:

```
- [ ] 1. Read CLAUDE.md, MEMORY.md, the open-items source, the last 3 logs
- [ ] 2. Build the open list: stale, due/in progress, waiting
- [ ] 3. Two-or-three-line recap, then one recommendation
- [ ] 4. Verify the briefing (Step 4); fix and re-check if anything fails
- [ ] 5. Ask: jump into a project, or start something new?
```

## Step 1 — Read the workspace

Read these before saying anything:

1. **`CLAUDE.md`** — who the user is, the language rule, the conventions. If it doesn't exist, `vault-setup` hasn't run: say so and offer to run it instead of continuing.
2. **`MEMORY.md`** — current state per project: what is true now, what is blocked, what is next. Read it; never edit it from this skill. Some vaults generate it from each project note's `## State` block, and its header will say so.
3. **The open-items source.** Where open items live is set by `CLAUDE.md`:
   - **A task manager** (e.g. a Todoist connector) if `CLAUDE.md` says tasks live there. Pull exactly three slices: active tasks due today or overdue; tasks labelled in progress; and waiting-on-someone tasks whose chase date has arrived. Set the result limit high (e.g. 100) so nothing is cut off. Drop anything in a deferred tier (`on-hold`, `someday` or the vault's equivalent).
   - **Otherwise, the vault itself:** every `### Still Open` item and `### Start Here` line in the logs that a later log hasn't resolved, plus any `## Tasks` section in a project hub.
4. **The last 3 daily logs** — in `01 Daily Logs/`, newest first. Each log may hold **several `## <Project Name>` sections**, one per conversation that day. Read all of them, not just the first.
5. **Project hubs, only as needed.** Don't open every hub up front. Open one when its line in `MEMORY.md` is blocked, when it has something due, or when the user picks it. Hubs are linked by filename (`PROJ <Name> Overview.md`), not by path, so find the file by name rather than assuming a folder. Basenames are unique, so the search resolves to one file.

A brand-new vault may have none of this yet. That's fine; work with what's there.

## Step 2 — Brief, open items first

**The open list.** One flat list, not split by project, so nothing hides under a heading the user skims past. In this order:

1. **Stale** — overdue by 3 or more days, or carried across more than one log. At the very top. Say plainly that each has to be done, rescheduled or dropped today. An item that keeps reappearing is a signal in itself, and saying so out loud is the point.
2. **Due today, overdue by 1–2 days, and in progress.** One line each.
3. **Waiting on someone** — grouped by person, so it reads "chase Sam" / "raise with the vendor". Only what has waited long enough to be worth chasing. These are follow-ups, not the user's own work.

Format each item as `project · item — (overdue N days)` where it applies. If the list is empty, say so in one line and move on; that is a good morning, not a problem.

**Deferred work stays out.** Items marked `on-hold` or `someday` (or the vault's equivalent) are not stale, not behind, and not part of a morning briefing. Don't count them, total them or mention that they exist. Undated active tasks are the backlog, browsed when the user picks up a project, never pushed at them.

**Not yet tracked.** In a vault that uses a task manager, an older log may still carry a `### Still Open` or `## Carried Forward` section whose items never reached the task manager. Check each one. Name any that are missing once, under a short **Not yet tracked** heading after the list, and offer to add them. Never blend them into the main list.

**Finished.** Then, briefly, what got done: two or three lines for the whole recap, not per project. Skip anything already in the open list.

**Recommendation.** One sentence on what matters most today: hard deadlines first, then stale items, then blockers in `MEMORY.md`, then momentum. Make a real call. Don't hedge and don't offer a menu; that's the next step's job.

Brand-new vault with no logs and an empty list: skip to Step 3.

## Step 3 — Ask what they want to do

> "Want to jump into a project, or start something new?"

**If they pick a project:** list the active projects one line each, with the current next action from `MEMORY.md` or the hub's `## Open Problems`. Once they name one, read its hub for the goal and open problems, and pull its open tasks: active first, dated before undated, then any deferred ones marked as such. This is the one place deferred tiers are fair to show. Then read whatever else is needed and get to work.

**If they want something new:** tell them to say "new project" and the `new-project` skill will walk them through it.

## Step 4 — Verify the briefing before sending it

Check the draft against these. If any fails, return to Step 2 and fix it, then check again.

- Every overdue item from the open-items source appears, and the stale ones sit at the top.
- Nothing from a deferred tier is mentioned or counted.
- Every waiting item names the person it waits on.
- No item appears in both the open list and the recap.
- The recommendation is one sentence and names one thing.

## During the day — close items as they finish

When the session finishes something from the morning list, close it **in the same turn** and say so: `complete` it in the task manager, or tick it in the hub's `## Tasks`. Don't leave it for end-of-day. If the work only moves an item forward, update it instead. Move its date only if the user names a day or an external deadline changed; don't roll a lapsed date forward just because it lapsed.

## Tone

Conversational and brief. The user is starting their day, not reading a report. Open items first, a couple of lines of recap, one clear recommendation, then into action.
