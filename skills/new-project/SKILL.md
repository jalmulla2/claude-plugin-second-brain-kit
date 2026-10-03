---
name: new-project
description: "Interviews the user, then creates a project folder and PROJ overview hub and registers it so later sessions know it exists. Use when the user says new project or wants to start something new."
compatibility: "Claude Code, Cowork, claude.ai · needs read/write access to the vault folder · Sonnet or Opus"
---

# New Project

If `personal.md` exists in this folder, read it first; it overrides the defaults above.

Interviews the user about a new project, creates its folder and hub file, and registers it so `good-morning` and future sessions know it exists. Nothing is written until the interview is done.

## When to use

- The user says "new project", "start a project", "create a project", "add a project" or "I want to work on something new".
- `good-morning` hands over because the user wants to start something new.
- Not for an ongoing responsibility with no end state. That is an area, not a project (Step 0).

## Language

Follow the language rule in `CLAUDE.md` (its `## Language` block, if it has one). Write the prose (goal, why, outcomes, problems) in the conversation's current language. Frontmatter keys, section headings, the folder name and the `PROJ <Name> Overview.md` filename pattern stay English regardless.

## Checklist

```
- [ ] 0. Is it a project? Does it already exist?
- [ ] 1. Interview, one question at a time
- [ ] 2. Create the folder and the hub
- [ ] 3. Register the project
- [ ] 4. Verify (Step 4); fix and re-check if anything fails
- [ ] 5. Confirm and offer to start
```

## Step 0 — Is it a project, and is it new?

**Projects end; areas don't.** A project is time-bounded and has an end state, something that will be *finished*. If there is no answer to "what does done look like?", it is an ongoing area of responsibility. Say so instead of creating a folder that can never close. Ask this of yourself after the "done" question, not out loud.

**Already started?** The user may have started it in another conversation. Search the vault for the keyword before creating anything (`find . -iname '*<keyword>*'` from the vault root, or the area's index note if the vault has one).

**Name collision.** No `PROJ <Project Name> Overview.md` may already exist anywhere in the vault. Two notes with the same basename make every `[[link]]` to them ambiguous. If it collides, tell the user and ask for a different name.

## Step 1 — Interview

**One question per message.** Send it, stop, wait for the answer. Don't batch the questions or list them up front. If the user answers several at once, confirm what you captured and resume from the first unanswered one.

1. **Name** — what's it called?
2. **Goal** — what is it trying to accomplish? One sentence is fine.
3. **Why** — why does it matter? The real reason.
4. **Done** — what will exist when it has succeeded?
5. **Open problems** — anything they already know they'll have to solve? Fine if not.
6. **Where it belongs** — only if the vault has more than one project root. With a `03 Life/` folder: work or personal? With area folders: which area? Infer it from the answers and confirm rather than asking cold.

## Step 2 — Create the folder and the hub

Where it goes:

- work, or the vault has no `03 Life/` → `02 Projects/<Project Name>/`
- personal → `03 Life/Projects/<Project Name>/` (create `03 Life/Projects/` if it isn't there yet)

The folder starts with one file, the project's **hub**: `PROJ <Project Name> Overview.md`. It is what Claude reads first whenever the project comes up, and it ties the project's files together. Never start the filename with `[`; Obsidian can't link to it.

Template:

```markdown
---
title: <Project Name> Overview
description: <one sentence: what this project is and what finishing it looks like>
author: claude
type: overview
date: YYYY-MM-DD
update date: YYYY-MM-DD
status: active
tags: []
---

## Goal
<their goal answer>

## Why
<their why answer>

## Tangible Outcomes
- <outcome 1>
- <outcome 2>

## Open Problems
1. <problem>
<or, if they had none: 1. (to be defined — we'll work these out as we go)>

## Key Files
| File | Purpose |
|------|---------|

_No files yet — a row is added here every time a file is created in this project folder._

## Links Out
_Nothing yet._
```

- **`description`** is what an agent (and any index) reads to judge relevance without opening the note. Make it say what the project is, not the title restated.
- **No `project` key on the hub.** Every *other* file in the folder points at it with `project: "[[PROJ <Project Name> Overview]]"`; that is how the hub collects them as backlinks. A file can't belong to itself.
- **`## Key Files` starts empty and doesn't stay empty.** Whenever a file is created in the project folder, by any skill or by ordinary work, set its `project` key and add a row in the same action: `| [[Filename Without Extension]] | <one phrase: why this file exists> |`. Delete the placeholder line with the first row. `end-of-day` adds any row that was missed, as a safety net.
- **`## Links Out`** is a labelled list of deliberate relationships to notes outside the folder (`- Depends on: [[PROJ Other Project Overview]]`, `- Background: [[Some Note]]`, external URLs). Filename-only wikilinks. Add one only when a real relationship exists.

## Step 3 — Register the project

This step is what makes future sessions aware of the project. Follow whichever applies:

- **`CLAUDE.md` has an `## Active Projects` table:** add a row, `| <Project Name> | [[PROJ <Project Name> Overview]] |`. The link is filename-only, so the row survives the project moving between roots. Don't change the table's structure, and don't add a folder map anywhere in `CLAUDE.md`; a stale map is worse than none.
- **`MEMORY.md` says it is generated from `## State` blocks:** add a State block to the hub directly after the frontmatter, then run the generator its header names. Don't edit `MEMORY.md` or `CLAUDE.md` by hand.

  ```markdown
  ## State
  - **Status:** active
  - **Where it stands:** <one sentence: what this is for, where it is>
  - **Blocked on:** nothing
  - **Next:** <the single next concrete action>
  ```

  State, never events: four lines, no past-tense history.
- **The vault keeps generated index notes:** regenerate the owning one with the tool that builds it. Never hand-edit an index.

## Step 4 — Verify before confirming

- The hub exists at the path from Step 2, and its basename is unique in the vault.
- Its frontmatter parses (the `---` fences are intact) and it has no `project` key.
- The project is registered by every route in Step 3 that applies: the table row, or the State block plus a rebuilt `MEMORY.md` with the project's line in it.

If any check fails, return to the step that produced it, fix it, and check again.

## Step 5 — Confirm and offer to dive in

Tell the user, briefly: the folder and hub are created and where, and the project is registered so later sessions will know it. Then ask: "Want to dive into one of the open problems now, or save it for later?"
