---
name: vault-setup
description: One-time first-run setup that creates a personal vault workspace — CLAUDE.md, MEMORY.md, the daily-log and project folders, and optionally a personal space and a raw source-material folder — and asks the user which language they want to work in. Trigger whenever the user installs this plugin for the first time and says things like "set up my vault", "get started", "initial setup", "set this up for me", or when good-morning / end-of-day / new-project can't find a CLAUDE.md and the user needs onboarding first.
---

# Vault Setup

This skill runs once, the first time someone uses this plugin. It interviews the user, then scaffolds the files and folders that `good-morning`, `end-of-day`, and `new-project` all depend on.

If `CLAUDE.md` already exists in the workspace root, tell the user setup already ran and ask if they want to redo it before touching anything — don't silently overwrite an existing vault.

## Step 1 — Interview

**Ask one question per message.** Send the question, stop, and wait for the answer before sending the next one. Do not batch the questions, do not list them all up front, and do not skip ahead. If the user answers several at once, tell them what you captured and resume from the first question they haven't answered.

Keep each question to a line or two. No preamble between them.

1. **Name** — what should I call you?
2. **Language** — do you want to work in **Arabic** or **English**? This is the default for everything I say and everything I write into your files. Folder names, file names, frontmatter keys and section headings stay English either way.
   → From this answer onward, ask the remaining questions in the language they chose.
3. **What is this vault for?** — one loose line (e.g. "freelance work and personal projects"). Used as flavour text in `CLAUDE.md`. If they don't have an answer yet, move on.
4. **A separate space for personal things?** — yes or no. If yes, I create `03 Life/`, and when you start a project I'll ask whether it's work or personal and file it accordingly. If no, everything lives in `02 Projects/`.
5. **A `raw/` folder for source material?** — yes or no. `raw/` holds material you did **not** write: PDFs, transcripts, exports, articles, someone else's notes. Nothing in it is ever edited or deleted; once you've written a note from a source, the source moves to `raw/processed/`. It is not an inbox.

Create nothing until every question is answered.

## Step 2 — Create CLAUDE.md

This is the most important file this skill writes. Every future session in this vault reads it first, and it is the only place the language rule and the file conventions live — the other skills point here rather than restating them.

Create `CLAUDE.md` at the workspace root:

````markdown
# <Name>'s Vault

## About
<their one-line answer, or "Personal workspace for projects and daily work.">

## Language
Default language: <Arabic | English>.

- Speak in the default language, and write the body of every file you create in it — daily logs, project overviews, notes.
- If the user writes a message in the other language, follow them: switch to that language and stay in it for the rest of this conversation. Don't drift back mid-conversation.
- A new conversation starts from the default again. Language does not carry over between sessions.
- Always English, whatever the conversation language: folder names, file names, YAML frontmatter keys, and Markdown section headings. Only prose and values change language.

## File Conventions
- Frontmatter on every note you create: `title`, `description`, `author: claude`, `type`, `project`, `date`, `update date`, `status`, `tags: []`.
- `description` is one sentence. Its job is to let an agent judge whether the note is relevant without opening the body — write it for that, not as the title restated.
- `project` applies only to files inside a project folder. Elsewhere, omit the key rather than leaving it blank.
- Wikilinks address a note by **filename only** — `[[PROJ Example Overview]]`, never a path. A path-bearing link is the only kind a folder move can break.
- Never start a filename with `[`. Obsidian cannot link to it.
- Never create two notes with the same basename anywhere in the vault. Duplicate basenames make every link to them ambiguous.

## Project Files
Any file you create inside a project folder — in the same action that creates it:
1. Set `project: "[[PROJ <Project Name> Overview]]"` in its frontmatter. This is what makes the hub collect it as a backlink.
2. Add a row to that project's `## Key Files` table in `PROJ <Project Name> Overview.md`, and set the hub's `update date`.

The hub itself is the exception to (1): a file cannot belong to itself, so `PROJ ... Overview.md` carries no `project` key.

Daily logs are the exception to both. A log carries no `project` key and no wikilinks — it names a project with a `## <Project Name>` heading and nothing more. Logs are terminal: nothing links out of them, so nothing in them can break when a note is renamed.

## Active Projects
| Project | Key File |
|---------|----------|

## Skills Available
- **good-morning** — start-of-day orientation: open items first, then what got finished, then one recommendation
- **end-of-day** — writes the session handoff log and reconciles the project hubs
- **new-project** — interviews you and creates a project folder, its hub file, and a row above
- **vault-import** — reads your mail, calendar and drive and builds a profile of you from what's already there, one approved finding at a time
````

Write the prose in the user's chosen language; keep every heading, key and path above in English.

Keep `CLAUDE.md` to these sections. It is read at the start of every session, so its length is a recurring cost. In particular, **do not add a folder map** — a stale map is worse than no map, and the file system can be read directly.

## Step 3 — Create MEMORY.md

Create `MEMORY.md` at the workspace root:

```markdown
# Memory

State, never events. What is true *right now* — open questions, pending decisions, things that have to survive into the next session. What *happened* belongs in `01 Daily Logs/`.

One block per project, five lines at most. Delete a block when the project closes or the state stops being true. A line that has stopped being true is worse than no line at all.

Nothing here yet.
```

## Step 4 — Create folders

Always, relative to the workspace root:

- `01 Daily Logs/` — one file per day of work, written by `end-of-day`
- `02 Projects/` — one folder per project, created by `new-project`

Only if they said yes in the interview:

- `03 Life/` — personal projects and notes. `new-project` creates `03 Life/Projects/<Name>/` under it on demand; don't pre-create that.
- `raw/` and `raw/processed/` — create both, so the rule is real from the first file.

Create only what they accepted. Don't add a folder "just in case" — an empty folder the user didn't ask for is a suggestion they'll feel obliged to obey.

If `raw/` was accepted, also write `raw/README.md`:

````markdown
# raw

Source material you did not write.

- Only things that came from somewhere else belong here: PDFs, transcripts, exports, screenshots, articles, someone else's notes. Anything you wrote yourself does not.
- Nothing in here is ever edited or deleted. It is the record of what the source actually said.
- When you compile a source into a note of your own, move the source into `raw/processed/`.
- Whatever sits in `raw/` but not in `raw/processed/` is the unprocessed queue. That is the entire queue mechanism — there is no list to maintain.

This is not an inbox and not a "to sort" folder.
````

Write that README's prose in the user's chosen language; keep the folder names and the `raw/processed/` path in English.

No `.gitkeep` needed — the folders can sit empty until the other skills write into them.

## Step 5 — Confirm

Tell the user, briefly:
- `CLAUDE.md` and `MEMORY.md` are set up, and which folders were created
- Their language default is recorded in the `## Language` block of `CLAUDE.md` — editing that block changes it later
- The next step is to say **"good morning"** to start their first session, or **"new project"** to create their first project right away
- If they'd rather not start from an empty page, **"learn about me"** reads their mail, calendar and drive and builds a profile from what's already there — nothing gets written without their approval

Keep it to five lines — they're ready to go.
