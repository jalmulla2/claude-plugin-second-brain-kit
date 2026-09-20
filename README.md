# second-brain-kit

A small Claude Code plugin that sets up a personal "vault" workspace: a `CLAUDE.md` and `MEMORY.md` that give every session context, plus skills for running your day.

Claude Code forgets everything between conversations. This plugin fixes that with plain markdown files in a folder you own — a context file Claude reads at the start of every session, a daily log it writes at the end of one, and a project folder structure both of those point at. No database, no sync service, no lock-in.

## What's inside

| Skill | What it does |
|---|---|
| `vault-setup` | One-time setup. Asks five questions, one at a time, then creates `CLAUDE.md`, `MEMORY.md`, `01 Daily Logs/`, `02 Projects/`, and — if you want them — `03 Life/` and `raw/`. |
| `good-morning` | Start-of-day orientation. Leads with what's still open, then what got finished, then one recommendation for what to work on. |
| `end-of-day` | Writes a short handoff log for the current session, updates `MEMORY.md` with what is still true, and reconciles each project's Key Files table so nothing goes unlinked. |
| `new-project` | Interviews you one question at a time, then creates the project folder, its hub file, and an entry in `CLAUDE.md`. |
| `vault-import` | Optional, run once. Reads your Gmail, Drive and Calendar history and builds a profile of you — interests, working style, the people around you. Proposes every finding for approval and writes only what you accept. |

You can work in **Arabic or English** — pick during `vault-setup`. That choice is the default for everything Claude says and everything it writes into your logs and project files. If you write to Claude in the other language it follows you for the rest of that conversation; the next session starts from your default again.

Folder names, file names, frontmatter keys and section headings always stay English, whatever language you're speaking, so paths and links keep working the same way either way.

---

## Install

Everything below happens in the terminal, in Claude Code.

### 0. Prerequisite — Claude Code

If you don't have it yet:

```bash
npm install -g @anthropic-ai/claude-code
```

Check it's there:

```bash
claude --version
```

### 1. Pick the folder that will be your vault

The plugin writes into whatever directory Claude Code was started in, so decide this first. Make a folder anywhere you like — an existing Obsidian vault works well, since everything here is plain markdown:

```bash
mkdir -p ~/Vault
```

### 2. Start Claude Code in that folder

```bash
cd ~/Vault && claude
```

This matters more than it looks: `cd` into the vault every time. A session started somewhere else will read and write the wrong `CLAUDE.md`.

### 3. Add the marketplace

The simplest way is to copy this repo's link and paste it into the plugin menu.

At the Claude Code prompt, run:

```
/plugin
```

Choose the option to add a marketplace, then paste:

```
https://github.com/jalmulla2/second-brain-kit
```

That's the same URL as the address bar on this page — copy it from there if it's easier.

If you'd rather type one line than click through the menu, this does exactly the same thing:

```
/plugin marketplace add jalmulla2/second-brain-kit
```

Either way, Claude Code now knows where to find the plugin — it reads `.claude-plugin/marketplace.json` from this repo. Nothing is installed yet.

### 4. Install the plugin

Still in `/plugin`, pick **second-brain-kit** from the marketplace you just added and install it.

Or by command:

```
/plugin install second-brain-kit@second-brain-kit
```

The first `second-brain-kit` is the plugin, the second is the marketplace it came from — they happen to share a name.

### 5. Restart Claude Code

Skills are loaded at startup. Exit and start again from the same folder:

```
/exit
```

```bash
cd ~/Vault && claude
```

### 6. Verify

Run `/plugin` and confirm `second-brain-kit` shows as installed, or just say **"set up my vault"** — if the setup interview starts, you're good.

---

## Manual install (no marketplace fetch)

If you'd rather grab the files yourself — offline, behind a firewall, or you just prefer having the folder on disk — download the repo instead of adding it by name.

On GitHub, hit **Code → Download ZIP**, then unzip it. GitHub appends the branch name, so you get `second-brain-kit-main`:

```bash
unzip ~/Downloads/second-brain-kit-main.zip -d ~/
```

Or clone it, if you'd rather be able to `git pull` later:

```bash
git clone https://github.com/jalmulla2/second-brain-kit.git ~/second-brain-kit
```

Then start Claude Code in your vault and add the **folder** as the marketplace:

```
/plugin marketplace add ~/second-brain-kit-main
```

```
/plugin install second-brain-kit@second-brain-kit
```

`/exit`, relaunch with `cd ~/Vault && claude`, and you're in the same place as the normal install.

Three things to know:

- **The path must be the unzipped folder, not the `.zip`** — it needs to contain `.claude-plugin/marketplace.json`. If Finder unzipped it for you, that's `~/Downloads/second-brain-kit-main`.
- **Don't delete or move the folder afterwards.** The marketplace points at that directory; it isn't a copy. Put it somewhere permanent *before* you add it.
- **A ZIP install can't update itself.** `/plugin marketplace update` has nothing to pull from. To upgrade, download the ZIP again, replace the folder, and restart Claude Code. Clone instead if you want `git pull` to work.

### Or skip plugins entirely

The skills are just folders with a `SKILL.md` in them. Copying them into your personal skills directory works too:

```bash
cp -r ~/second-brain-kit-main/skills/* ~/.claude/skills/
```

Restart Claude Code and all four are available. You lose plugin-managed updates and `/plugin uninstall` — you'd add and remove these folders by hand.

---

## Getting started

Once installed, say:

```
set up my vault
```

That runs `vault-setup`. It asks five questions — **one per message**, so you answer each before the next arrives:

1. What should I call you?
2. Arabic or English?
3. What is this vault for? (one loose line)
4. Do you want a separate space for personal things?
5. Do you want a `raw/` folder for source material you didn't write?

Then it creates:

```
your-vault/
├── CLAUDE.md          ← read at the start of every session
├── MEMORY.md          ← current state that has to survive between sessions
├── 01 Daily Logs/     ← one file per day of work
├── 02 Projects/       ← one folder per project
├── 03 Life/           ← optional: personal projects, kept apart from work
└── raw/               ← optional: source material you didn't write
    └── processed/     ← sources you've already written a note from
```

The last two only appear if you said yes. `raw/` has a rule that makes it work: nothing in it is ever edited or deleted, and once you've written a note from a source you move the source into `processed/`. Whatever's in `raw/` but not `processed/` is your unprocessed queue — no list to maintain. It is not an inbox.

Then say **"good morning"** to start your first real session, or **"new project"** to create your first project right away.

---

## Starting from your history

A vault set up on Monday knows nothing about you until Friday. If you'd rather not begin from a blank page, say:

```
learn about me
```

That runs `vault-import`. It reads accounts you already have — Gmail, Google Drive, Calendar — and works out the things a vault would otherwise take months to learn: what you keep returning to, who you actually work with, when you work, what you say yes and no to.

It asks four questions first, one per message:

1. **Which accounts** should it read? Only the ones you name, only the ones actually connected.
2. **How far back?** A date, or "the last two years".
3. **What should stay out of it?** It names the categories — money, health, legal, family, friction, anything told to you in confidence — and you strike out what you don't want. Whatever you exclude is skipped *while reading*, so it never reaches the conversation at all.
4. **How deep?** Facts only — people, projects, organisations — or facts plus inference about preferences, working style and personality.

Then it scans one source at a time and shows you what it found after each, as a numbered list with the evidence attached to every line:

```
Working style
1. Prefers async over meetings — declined 61 of 94 ad-hoc invites, replied on the thread instead
2. Writes early — 70% of your sent mail goes out before 08:00
```

**Nothing is written until you approve it, line by line.** Accept, reject, or reword each finding — rewording is the usual outcome, and your wording is what gets saved. What you approve lands in:

```
04 Profile/
├── PROFILE Overview.md   ← identity, interests, working style, communication
└── PERSON <Name>.md      ← one per significant relationship
```

Two things are never written regardless of what you allow: secrets (passwords, keys, account numbers), and anything verbatim from someone else — the profile summarises what a thread shows about *you*, it doesn't copy what they wrote.

Run it again in six months and it picks up where it left off; the hub keeps an `## Import Log` of what each run covered, so there's no state file and nothing to maintain.

It is not part of the daily loop. Most people run it once.

---

## Using it day to day

The whole thing is three moments in a day. You never call a skill by name — you just talk, and the phrasing triggers the right one.

### Morning — "good morning"

Say **"good morning"** (or "morning", "let's get to work", "what should I work on?") right after `cd ~/Vault && claude`.

Claude Code reads `CLAUDE.md`, `MEMORY.md`, your last three daily logs, and every active project hub. Then it briefs you — **open items first**, because that's the part that needs a decision today, with an age on anything that's been carried for days. Finished work comes second, in a couple of lines. Then one clear recommendation. It ends by asking whether you want to jump into an existing project or start something new.

Do this *before* asking Claude for anything else. It's what loads the context.

### Whenever you start something new — "new project"

Say **"new project"** (or "start a project", "I want to work on something new").

Claude interviews you **one question per message**: name, goal, why it matters, what "done" looks like, and any problems you already know you'll hit. If you have a `03 Life/` folder it also asks whether this one is work or personal. Then it creates:

```
02 Projects/<Project Name>/PROJ <Project Name> Overview.md
```

— or `03 Life/Projects/<Project Name>/...` for a personal one — and adds a row to the `## Active Projects` table in `CLAUDE.md`, which is what makes `good-morning` aware of it tomorrow. The row looks the same either way, because it links by filename rather than path.

That overview file is the project's **hub**: goal, why, outcomes, open problems, a `## Key Files` table linking every file in the project, and `## Links Out` for related notes elsewhere.

One check it will push back on: if you can't say what "done" looks like, it's an ongoing area of responsibility, not a project, and Claude will say so rather than create a folder that can never close.

### End of a session — usually automatic

`end-of-day` writes the handoff note. Most of the time you don't have to ask: **any session that actually changed something in the vault gets logged automatically** when the conversation closes. Sessions that only read or discussed things write nothing, and that's correct — a missing day means there was no work to hand off.

To write one explicitly, say **"end of day"** ("wrap up", "we're done", "done for the day") before you `/exit`.

Logs land in `01 Daily Logs/YYYY-MM-DD.md`, one `## [Project Name]` section per project you touched that day, each with what was worked on, what changed, what's still open, and where to pick up next time. Work past midnight and it still writes to the day the session started — anything before 06:00 goes to the previous day's file.

It also does two bits of housekeeping. For every file it just listed as built or changed, it makes sure that file points at its project hub and appears in the hub's `## Key Files` table — files created outside a session get picked up here. And it rewrites each touched project's block in `MEMORY.md` so that file says what is true *now*, not what happened: open questions, decisions later work depends on, what's blocking and on whom. Five lines per project, and a line is deleted the moment it stops being true.

### A typical week

```bash
cd ~/Vault && claude
```

```
Monday    "good morning"        → recap + recommendation
          "new project"         → Client Onboarding created
          ...work...            → log written automatically on close

Tuesday   "good morning"        → picks up Client Onboarding from Monday's log
          ...work...
          "end of day"          → explicit log before you close the laptop

Wednesday "good morning"        → sees both days, recommends what's most urgent
```

---

## How your notes link up

Open the vault in Obsidian and the graph is not a pile of disconnected files. Three links do the work:

| From | To | Written when |
|---|---|---|
| `CLAUDE.md`'s Active Projects table | the project hub | a project is created |
| the hub's `## Key Files` table | every file in the project | each file is created, and reconciled at end of session |
| every file's `project:` frontmatter | back to its hub | each file is created |

That third one is the quiet workhorse — it means the hub's backlinks pane lists the whole project without anyone maintaining a list.

**Daily logs deliberately link to nothing.** A log names a project with a `## Project Name` heading and no wikilink at all. This is on purpose: nothing should link to a Tuesday. Logs are where events go, so they are written once and never need repairing when a note is renamed or moved. If your graph shows the logs sitting off to one side, unconnected, that's the design working — not a bug.

**The profile is its own small cluster.** If you've run `vault-import`, `PROFILE Overview.md` and its `PERSON` notes link to each other in both directions, and `CLAUDE.md` carries a single line pointing at the hub. Neither file gets a `project` key — they don't belong to a project, and the pointer stays one line because `CLAUDE.md` is read at the start of every session.

Every wikilink is written as a **filename only** — `[[PROJ Client Onboarding Overview]]`, never a path. Path-bearing links are the only kind that break when you move a folder, so the vault simply doesn't create any. The tradeoff is that note names have to be unique across the vault, which is why `new-project` checks for a collision before creating anything.

---

## The files you own

| File | What it's for | Edit it yourself? |
|---|---|---|
| `CLAUDE.md` | Who you are, your language, the active-projects table, skills available. Read first in every session. | Yes — keep it short. It's read every time. |
| `MEMORY.md` | Current state only, not a history — open questions, pending decisions. Maintained by `end-of-day`, one block per project, five lines each. | Yes — and it gets rewritten per project as work moves. |
| `01 Daily Logs/*.md` | Session handoff notes, written by `end-of-day`. | Rarely — let the skill own these. |
| `02 Projects/*/PROJ * Overview.md` | The project hub: goal, why, tangible outcomes, open problems, `## Key Files`, `## Links Out`, and an optional `## Tasks` section. | Yes — this is the file to keep current. |
| `03 Life/` | Same shape as `02 Projects/`, for things that aren't work. Optional. | Yes. |
| `raw/` | Source material you didn't write. Never edited, never deleted; move a source to `raw/processed/` once you've written from it. Optional. | Add files, don't edit them. |
| `04 Profile/PROFILE Overview.md` | Who you are, as derived from your own history and approved by you — interests, working style, communication, and the people table. Optional. | Yes — editing it directly beats re-running the import. |
| `04 Profile/PERSON *.md` | One note per significant relationship: who they are to you, how you know them, and how often you're in contact. Optional. | Yes. |

Tasks live inside each project's own hub file. There is no central task list, by design.

Since it's all markdown in a folder, you can put the vault in git or sync it with anything that moves files. It's shaped for Obsidian in particular — the wikilinks and frontmatter mean graph view and the backlinks pane work properly from day one.

---

## Updating and removing

Pull the latest version of the plugin:

```
/plugin marketplace update second-brain-kit
```

Then `/exit` and start Claude Code again. Your vault files are never touched by an update — the plugin only ships skills.

To remove it:

```
/plugin uninstall second-brain-kit@second-brain-kit
```

Your `CLAUDE.md`, logs, and projects stay exactly where they are.

---

## Upgrading to v1.3

Nothing to do. `end-of-day` now maintains `MEMORY.md` — before v1.3 the file was created at setup and then never written to again, so it sat at "Nothing here yet." while `good-morning` dutifully read it every morning. Update the plugin, restart, and the next `end-of-day` starts filling it.

If your `MEMORY.md` has been collecting hand-written notes, leave them — the skill replaces a project's block only when that project's state changes, and anything under a heading it doesn't recognise is left alone.

---

## Upgrading to v1.2

Nothing to do. `vault-import` is new in v1.2 and it creates everything it needs on first run, including the one-line pointer it adds to `CLAUDE.md`. Update the plugin, restart, and say "learn about me" whenever you want it.

The only cosmetic gap is that `## Skills Available` in an older `CLAUDE.md` won't list the new skill. Add the line yourself if you want it there:

```markdown
- **vault-import** — reads your mail, calendar and drive and builds a profile of you from what's already there, one approved finding at a time
```

---

## Upgrading a vault set up on v1.0

An update ships new skills but never touches your files — which means a vault created before v1.1 still has the old `CLAUDE.md`, and the skills will look for rules that aren't in it. Nothing breaks, but the language rule won't stick and new files won't link themselves until you add the missing blocks.

**Do not re-run `vault-setup` to fix this.** It rewrites `CLAUDE.md` from scratch, and your `## Active Projects` table goes with it.

### 1. Update `CLAUDE.md`

Open it and replace the old one-line `## Language` section with the block below, then add the two sections after it. Leave `## About`, `## Active Projects` and `## Skills Available` alone.

```markdown
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
```

Or just ask Claude to do it — with the plugin updated, "add the v1.1 blocks to my CLAUDE.md" is enough, since the skills carry the same text.

### 2. Backfill your existing projects

Existing hub files have no `## Key Files` or `## Links Out`, and the files already sitting in those folders have no `project` frontmatter. Ask Claude once per project:

```
Backfill the Key Files table in this project's hub and add the project frontmatter to its files
```

Or skip it — `end-of-day` reconciles a project the next time you touch a file in it. Backfilling is only worth doing for projects you want linked *now*.

### 3. Add the optional folders, if you want them

There's nothing to run. `03 Life/` and `raw/` are detected by their existence:

```bash
mkdir -p "03 Life/Projects" raw/processed
```

Once `03 Life/` exists, `new-project` starts asking whether a project is work or personal. Nothing else needs to change.

---

## Troubleshooting

**"Nothing happens when I say good morning."**
The skills didn't load. `/exit`, start Claude Code again, then check `/plugin` shows `second-brain-kit` as installed.

**"Claude Code says it can't find CLAUDE.md."**
The session wasn't started in your vault folder, or setup never ran. Run `pwd` — if it isn't your vault, `/exit` and start again with `cd ~/Vault && claude`. If the path is right, say "set up my vault".

**"It created the files in the wrong place."**
Skills write relative to the directory Claude Code was started in. Move the files to your real vault folder and always launch with `cd ~/Vault && claude`.

**"good-morning doesn't see my project."**
It reads the `## Active Projects` table in `CLAUDE.md`. If the project folder exists but the row doesn't, add the row.

**"My project's Key Files table is empty."**
Rows are added as files are created, and reconciled when a session ends. If you added files outside a session — copied them in from the shell, say — say "end of day" and they'll be picked up.

**"Claude answered me in the wrong language."**
It follows whichever language you write in, and stays there for the rest of the conversation. Start a new session to get back to your default, or edit the `## Language` block in `CLAUDE.md` to change the default itself.

**"Setup ran twice."**
`vault-setup` checks for an existing `CLAUDE.md` and asks before overwriting. If you said yes by mistake, your logs and project folders are untouched — only `CLAUDE.md` and `MEMORY.md` were rewritten.

---

## License

MIT
