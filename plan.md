# claude-md — Template Plan

> A universal, file-based memory system for Claude Code. Template version.
> Hand this file to Claude Code in a fresh repo and ask it to build the skeleton.
> Claude: **run the Initial Interview below before building anything.** Fill in the placeholders with the user's answers, then execute the plan.

---

## For the Template User

This is a reusable template for building your own Claude memory system. The goal: one Claude, anywhere, that already knows you, your projects, your preferences, your dead ends, and your truths, on all your platforms.

### Setup — from zero to running

You need: a GitHub account, git installed, and Claude Code installed. Anything missing, install it first.

**Step 1 — Create a private GitHub repo.**

Go to GitHub and create a new repository. Name it `claude-md` (or anything you like — the name doesn't matter, it's just your memory repo). Make it **private**. Initialize it with a README if you want. **Do not** add a license or `.gitignore` yet — we'll handle those later if needed.

If you prefer the `gh` CLI:

```bash
gh repo create claude-md --private --clone
cd claude-md
```

If you used the web UI instead, clone it locally:

```bash
git clone https://github.com/<your-username>/claude-md.git
cd claude-md
```

**Step 2 — Place this template in the repo.**

Save `template_plan.md` (this file) into the cloned folder. Commit and push it so the repo has at least one real file:

```bash
git add template_plan.md
git commit -m "seed template plan"
git push
```

**Step 3 — Open Claude Code inside the repo.**

Make sure your terminal is `cd`'d into the `claude-md` folder, then start Claude Code.

**Step 4 — Hand the template to Claude.**

Tell Claude: *"Read `template_plan.md` and run the Initial Interview."*

**Step 5 — Answer Claude's questions.**

The interview takes 10–15 minutes. Claude collects your identity, the OSes you use, your initial projects (with per-OS paths), your secrets folder location, and any seed rules you want. Read carefully before answering — the answers get baked into your new memory system.

**Step 6 — Claude builds the skeleton.**

Using your answers, Claude creates the folder structure, writes every `CLAUDE.md`, scaffolds your initial projects, seeds Core `long-term-mem/user-profile.md` with your identity, and copies this template to `plan.md` — personalized for you.

**Step 7 — Commit, push, and you're live.**

Claude will create a first commit with the full skeleton. Push it to GitHub:

```bash
git push
```

From this moment on, your claude-md repo is your universal memory system. Clone it on any machine, run through Cross-Platform Migration for that OS, and Claude will know you there too.

### What happens to this template file

Once the skeleton is built, **this template plan becomes *your* plan**. Claude copies `template_plan.md` to `plan.md`, personalizes the examples to match your answers, and fills in the populated `My Initial Projects` section from your interview answers. `plan.md` then becomes the fidelity baseline for your instance. You can keep `template_plan.md` around as the pristine origin, or delete it — `plan.md` is the one the system will reference from now on.

The plan evolves as your system does — every amendment goes through Core Rule #1.

---

## Initial Interview

**Claude: before building anything, ask the user the following questions in order. Collect the answers. Do not proceed to the build phase until all required answers are provided. If any answer is unclear, ask a follow-up.**

### 1. Identity

- What name or handle should Claude use for you? (e.g., `alex`, `jdoe`, `firstname`)
- What is your role / background? (e.g., "senior backend dev, ten years in Go", or "hobbyist tinkerer, self-taught")
- How do you want Claude to communicate with you? (e.g., "terse and direct", "verbose with reasoning", "explain as you go")

### 2. Platforms

- Which operating systems do you use? (any combination of Linux, Windows, macOS)
- For each OS, what's the absolute path where your git repos live?
  - Example: Linux → `/home/alex/code/`, Windows → `C:\Users\alex\code\`, macOS → `/Users/alex/code/`
- Are you using WSL on Windows? (If yes, treat it as Linux in `path.md` but note the `/mnt/...` prefix.)

### 3. Secrets / priv folder

- Do you have a dedicated "priv" folder for secrets, credentials, keys, and tokens? **This must be outside this repo.**
- For each OS you use, what's the absolute path to that priv folder?
  - Example: Linux → `/home/alex/priv/`, macOS → `/Users/alex/priv/`
- If you don't have one yet, Claude will create one in a location outside this repo (so it is never git-tracked).

### 4. Initial projects

There are two kinds of memory projects:

- **Codebase projects** — have a real repo/codebase on disk. Claude needs the absolute path per OS.
- **Memory-only projects** — no codebase. Used for organizing knowledge, research, life areas, or anything that doesn't map to a single repo. No `path.md` needed.

For each project, provide:
  - Project name (short, lowercase, no spaces — e.g., `my-app`)
  - A one-line description
  - Whether it's a codebase project or memory-only
  - For codebase projects: the absolute path to the codebase on each OS you use
  - For memory-only projects: any related codebase projects (optional)
- Note: a project is where you actively work or actively gather knowledge. Backups, reference-only code, and scratch folders do not need to be projects — they can live in `knowledgebase/` as reference material.

### 5. Seed rules (optional)

- Are there any rules Claude should follow from the start beyond the six Core Rules? Examples: coding style preferences, communication habits, tools to avoid, git workflow conventions.
- These get seeded into `rules/<category>.md` files.

### 6. Anything Claude should know upfront

- Anything else that belongs in Core `long-term-mem/` from day 1? Examples: hardware details, collaborators, recurring context, strong preferences.
- This is optional — the first-run initialization flow will prompt for more later.

**Once all six sections are answered, Claude summarizes the answers, asks for confirmation, and then proceeds to build the skeleton.**

---

## North Star

One Claude you can talk to across platforms (Linux, Windows, macOS) that knows about all your projects and the personally relevant info that makes its help actually useful. A well-informed Claude is a helpful one. The corollary matters too: a Claude informed by the *wrong* stuff is a worse Claude — which is why this system has explicit gates around what gets written, promoted, and trusted.

The mechanism: this repo is the brain. Git sync is the nervous system. `path.md` files in each project point to the codebase location on each OS. You should be able to start on one machine, commit and push, sync on another machine, and pick back up from there.

---

## Workflow Model

You always `cd` into the memory repo. **You never `cd` into the actual project codebases.** Project codebases are reached via the absolute paths stored in each project's `path.md` file.

This means this repo overrides the traditional way Claude Code stores memory. The edit surfaces during any session are:

1. **Core memory** in this repo (`./`) — edits and learned things affecting all projects, including changes to the memory system itself. Universal memory used across all projects.
2. **Active project memory** in this repo (`./projects/<active-project>/`) — edits and learned things scoped to the current project.
3. **The active project's actual codebase** (codebase projects only) — at the path resolved from its `path.md` — the real work.

Memory-only projects (no codebase, no `path.md`) use only surfaces 1 and 2. Codebase projects use all three.

Everything Claude does in a session touches one of those surfaces. Nothing else, unless the user asks or approves.

### Running commands against the active project (codebase projects only)

Because the terminal is always sitting in the memory repo, any command that needs to run against the active project's codebase must be scoped to that project's path:

- **File operations:** use absolute paths from the active project's `path.md` (Read, Edit, Write, Glob, Grep all accept absolute paths).
- **Git operations on the project repo:** `git -C <project-path> <command>` — never plain `git status` (that hits the memory repo, not the project).
- **Build / test / dev / lint commands:** `cd <project-path> && <command>` in a single Bash call.
- **Never** `cd` alone as a standalone command, because shell state doesn't persist between Bash calls — always combine `cd` with the command it scopes.

Core CLAUDE.md teaches this pattern so Claude doesn't accidentally run project commands against the memory repo.

---

## Two Layers

There are two memory layers:

- **Core memory** at `./` — shared across all projects. Things that are true of the user regardless of which project is active.
- **Project memory** at `./projects/<project-name>/` — scoped to one project. Each project has its own complete memory system.

**Precedence:** Core wins on conflict. In theory Core and Project should never conflict — they describe different things. If they do, Claude flags the conflict to the user and stops; never silently picks a side.

---

## Folder Structure

Both Core and each Project have the same six folders, plus a CLAUDE.md at their root:

```
./                              ← Core layer
├── CLAUDE.md                   ← root router (thin, @-imports, core instructions)
├── rules/
├── lies/
├── truths/
├── short-term-mem/
├── long-term-mem/
├── knowledgebase/
└── projects/
    ├── <codebase-project>/     ← Codebase project (has a repo on disk)
    │   ├── CLAUDE.md
    │   ├── path.md             ← OS:PATH lines pointing to the actual codebase
    │   ├── rules/
    │   ├── lies/
    │   ├── truths/
    │   ├── short-term-mem/
    │   ├── long-term-mem/
    │   └── knowledgebase/
    └── <memory-only-project>/  ← Memory-only project (no codebase)
        ├── CLAUDE.md           ← notes Type: Memory-only, no path.md
        ├── rules/
        ├── lies/
        ├── truths/
        ├── short-term-mem/
        ├── long-term-mem/
        └── knowledgebase/
```

Each of the six memory folders has its own `CLAUDE.md` explaining its purpose, format, and how Claude should use it. The three **lazy-loaded** folders (`short-term-mem/`, `long-term-mem/`, `knowledgebase/`) additionally have an `index.md` listing their contents so Claude can decide what to open without reading the whole folder. The three **always-loaded** folders (`rules/`, `lies/`, `truths/`) do not need an `index.md` — they're fully loaded every session anyway. Fixed filenames for these always-read files; free-form markdown for individual entries inside.

The "life" project test: this same schema must work as cleanly for a memory-only project literally called "life" (goals, ambitions, job, facts of reality) as it does for a software codebase project. If it doesn't, the schema isn't universal enough.

---

## Folder Roles

### `rules/`

Prescriptive. The "must do / must not do" rules.

- Each folder's `CLAUDE.md` declares its category rules. Core `rules/CLAUDE.md` holds the **Core Rules** (see below) — these are absolute.
- Additional rule categories live as `<category>.md` files (e.g. `coding-style.md`, `communication.md`, `git-workflow.md`).
- Glob-scoped rules (e.g. "applies when editing `*.rs`") are written informally in prose as Claude discovers patterns. No formal frontmatter required — discovery and notation happen organically.
- **Project rules can override Core rules only when explicitly stated** in the project CLAUDE.md or rule file. Default: Core wins.

### `lies/`

Things that sound right but have been proven wrong in the user's context. Closest professional prior art: ADR "rejected options" sections.

- Each entry: the false statement, why it is false, what the correct understanding is (link to a `truths/` entry if applicable), date discovered.
- **Purpose:** prevent Claude from re-suggesting things that have already been tried and failed. Before recommending a solution, Claude checks `lies/` (Core + active project) to make sure it isn't about to repeat a known dead end.
- The name "lies" is deliberate — more definitive than "rejected" or "dead-ends." Each folder's CLAUDE.md conveys the spirit.

### `truths/`

User-asserted ground truth. Overrides anything Claude might "think it knows" from training data when there is a conflict.

- Each entry: the statement, when it was established (date), why it is considered true (source: user, verified by testing, documentation link).
- **Rare and load-bearing.** Claude never silently contradicts a truth — if new evidence conflicts with one, Claude raises it with the user before changing anything.
- Cross-references with `lies/` only when relevant.

### `short-term-mem/`

The volatile working layer. Two roles:

1. **Session scratchpad:** where work was left off, open questions, untested hypotheses, recent decisions not yet hardened.
2. **Research dropbox:** when Claude or the user gathers new info from online research, it lands here first for analysis and testing.

- Append-only, timestamped. Most volatile layer — never trusted blindly.
- Entries either get **promoted** to long-term-mem + knowledgebase (if proven), or pruned/archived after they age out.
- The **last-active-project pointer** lives here at `active-project.md`. Unlike other short-term-mem entries (which are batched per the hybrid commit model), this file is **committed individually and immediately** whenever it changes — it is the cross-machine handoff mechanism and must always be pushed before the next machine picks up.

### `long-term-mem/`

Promoted, stable facts. Things that survived the short-term filter via the graduation flow.

- **Topic-organized**, not date-organized. Example files: `preferences.md`, `workflow.md`, `hardware-and-environment.md`, `people-and-collaborators.md`, `user-profile.md`.
- **Date is still part of context**: each entry keeps a `date_established` and `date_last_confirmed` in frontmatter so age is visible. Long-term doesn't mean immortal.
- If an entry starts feeling uncertain or contested, it moves back to `short-term-mem/` (or to `lies/` if it turned out to be wrong).

### `knowledgebase/`

Reference material — full, organized copies of source material that Claude can consult on demand. The personal wiki / second brain.

- API references, snippets, recipes, runbooks, postmortems, project schemas, anything bulky and structured.
- **`CLAUDE.md`** explains how Claude should use the folder. **`index.md`** lists every file (and subfolder) with one-line descriptions so Claude can decide what to open without reading the whole folder. New entries must be added to `index.md` when created.
- Project knowledgebase holds project-specific reference material (database schema, deploy runbook, etc.).
- Core knowledgebase holds reference material that spans projects (general API references, recurring snippets).
- **Freely subdividable.** Add category subfolders as new kinds of knowledge accumulate (e.g. `src_code/`, `api_refs/`, `runbooks/`, `recipes/`). The `index.md` lists categories so Claude can navigate. The "life" project's knowledgebase will look nothing like a software project's — that's the point.
- **Binary files (PDFs, images, archives):** prefer extracting useful content into markdown. Store the raw original only when the full file is genuinely needed. Large binaries must use Git LFS — see the file size awareness rules in Git Sync Discipline and `knowledgebase/git-lfs.md` for details.

---

## `path.md` Format (codebase projects only)

Codebase projects have a `path.md`. Memory-only projects do not — their CLAUDE.md notes `Type: Memory-only` instead. One line per OS, using the format:

```
OS:PATH
```

Example:

```
Linux:/home/alex/code/my-app/
Windows:C:\Users\alex\code\my-app\
macOS:/Users/alex/code/my-app/
```

Based on the OS Claude Code is currently running on, the matching line is the active codebase path for all operations on that project. **If `path.md` has no entry for the current OS, Claude must ask the user to provide one and append it — never guess.** WSL counts as Linux, but its paths use the `/mnt/...` prefix.

---

## "Begin new session" — Startup Protocol

The user says **"Begin new session"** to start a full startup sequence. If they jump straight into work, Claude runs the essentials (steps 1, 3, 4) silently. The full protocol is a mini-curate that ensures clean state and continuity across sessions.

### Step 1 — Git pull + unclean state check

- `git pull` in the memory repo. Never skip.
- Check for unclean state from a previous session that didn't close properly:
  - `git status` — uncommitted memory changes? Show them, ask: commit and push, or discard?
  - `git log origin/main..main` — unpushed commits? Push them now.
- If the pull shows merge conflicts in memory folders, **stop and ask the user**. Never auto-resolve.

### Step 2 — Auto-memory audit

Claude Code's built-in auto-memory (`~/.claude/projects/*/memory/`) runs independently and can accumulate stray entries on any machine. On "Begin new session":

- Scan `~/.claude/projects/*/memory/` for `.md` files that aren't the claude-md pointer.
- If found, show them to the user. Offer to import useful ones into claude-md (usually `short-term-mem/` first).
- After import, remove the stray entries — keep only the pointer that says "defer to claude-md."
- On a new machine where no pointer exists, create one.

### Step 3 — Load enforcement layer

Always-loaded in full — Core layer:

1. Core `CLAUDE.md` — the router.
2. Core `rules/` — `CLAUDE.md` plus every category `.md` file.
3. Core `lies/` — `CLAUDE.md` plus every entry file.
4. Core `truths/` — `CLAUDE.md` plus every entry file.

### Step 4 — Active project selection + context reload

1. Read `short-term-mem/active-project.md` for the last-active project.
   - **First-run exception:** if the file doesn't exist, ask the user to pick from `projects/`. Create it with their choice, machine name, and date. Commit immediately.
2. Ask: *"Last active: `<name>` on `<machine>` `<date>`. Continue, or pick another from: `<list>`?"*
3. User confirms or picks a different project.
4. Update the pointer and commit immediately if changed.
5. Load the active project's `CLAUDE.md` + its enforcement layer (rules, lies, truths).
6. **Context reload:** read Core `short-term-mem/index.md` and the active project's `short-term-mem/index.md`. Summarize recent entries to the user so they know where things left off. This is the continuity bridge between sessions.

**Mid-session switching:** allowed via natural language ("let's work on `<other-project>` now"). Claude updates the pointer and reloads the project layer without losing Core context.

### Step 5 — Codebase CLAUDE.md assimilation (codebase projects only)

When loading a codebase project, check if the project's actual repo has a `CLAUDE.md` at its root:

- If one exists and hasn't been assimilated yet, read it and offer to import contents into the project's memory in claude-md (rules → `rules/`, known issues → `lies/` or `short-term-mem/`, architecture notes → `long-term-mem/` or `knowledgebase/`).
- The project repo's CLAUDE.md stays in place (other contributors may depend on it) — claude-md's project memory becomes the authoritative source for this user.
- Track what's been assimilated so it doesn't re-trigger every session.

### Step 6 — Lazy-load the rest

Everything inside `long-term-mem/`, `short-term-mem/` (beyond context reload), and `knowledgebase/` is lazy-loaded via each folder's `index.md`. Only open individual files when the conversation calls for them.

### Load priority summary

| What | When loaded | Why |
|---|---|---|
| Core rules, lies, truths | Always (step 3) | Enforcement — must always be in context |
| Active project rules, lies, truths | Always (step 4) | Same enforcement, project-scoped |
| `active-project.md` | Always (step 4) | Cross-machine handoff |
| `short-term-mem/index.md` (both) | On "Begin new session" (step 4) | Continuity bridge |
| `long-term-mem/index.md` | On demand | Read when conversation needs it |
| `knowledgebase/index.md` | On demand | Read when reference material needed |

**Precedence within each layer:** `rules > lies > truths`.
**Precedence across layers:** Core wins over Project on conflict. If they conflict, flag it and stop.

---

## Graduation Flow

Information moves through the system in this direction:

```
online research / new info
        │
        ▼
  short-term-mem/  ←──  scratchpad / hypothesis
        │
        │  (user + Claude agree it's solid)
        ▼
  long-term-mem/  +  knowledgebase/
        │              │
        │              └──  full cleaned-up source
        │
        └──  distilled fact
```

**Rules of graduation:**

- Promotion requires **explicit user-and-Claude agreement** (Core Rule #4). No automatic promotion.
- When a short-term entry graduates, it is **simultaneously** written to long-term-mem (the distilled fact) and knowledgebase (the full cleaned source). The short-term copy is then deleted.
- A long-term entry that turns out to be wrong moves back to `short-term-mem/` (if uncertain) or `lies/` (if proven false).
- Truths are rarely written and require user assertion. They are not automatically created from long-term-mem.

---

## Curation Model — Two Tiers

Curation is what keeps the 12 folders (6 × 2 layers) from rotting. There are two tiers, each with a different mechanism.

### Tier 1 — Inline lightweight capture (every turn)

Core CLAUDE.md instructs Claude that on every user/assistant exchange:

- Scan the exchange for **memory-worthy moments**: corrections, preferences revealed, dead ends discovered, facts confirmed, decisions made, project state changes.
- If found, write a single entry to the appropriate folder.
- If not found — which is normal on most turns — do nothing. Silence is fine. **Do not invent reasons to write.**

This is cheap (no extra reads, no extra LLM call), in-context, and piggybacks on what Claude already knows from the conversation.

### Tier 2 — Heavy curator (on demand, user-triggered)

When the user types **"curate"** (alone, or paired with "compact"), Claude runs a full curator pass:

1. Read the on-demand folders end-to-end — `long-term-mem/`, `short-term-mem/`, `knowledgebase/` for both Core and the active project. The enforcement layers (`rules/`, `truths/`, `lies/`) are already loaded from bootstrap, so the curator cross-references them rather than re-reading.
2. **Cross-project scan:** read the `index.md` of every other project's `long-term-mem/` and `short-term-mem/`. Look for entries that have outgrown their project scope — facts that apply universally belong in Core, not buried in one project. Memory-only projects like `life` especially tend to generate entries that are really Core material (personal context, goals, people, preferences). If an entry serves all projects, propose graduating it to Core memory.
3. For each folder, identify:
   - New entries warranted by this session
   - Contradictions with existing memory
   - Short-term entries ready to graduate (within the active project and up to Core)
   - Stale long-term entries (`date_last_confirmed` is ancient)
   - Knowledgebase gaps (long-term fact with no source in knowledgebase)
   - Dead ends to add to `lies/`
   - Project entries that belong in Core (universal across projects)
4. Present a **punch list** to the user.
5. Wait for explicit user approval per change (Core Rules #1 and #4).
6. Once approved writes are done, if the user also said "compact", run `/compact`. **Never run `/compact` first** — compaction is lossy and must happen *after* curation, never before.

The curator is **read-mostly** and **never auto-edits without user approval**.

This pairs naturally with `/compact` because:

- `/compact` is the natural curation moment — context is full, conversation has accumulated material worth distilling, you're already pausing.
- User-triggered means no surprises.
- Pure instruction-driven via Core CLAUDE.md. No hooks, no subagents, no `settings.json` edits — the design stays portable: `git pull` on any machine and it works.

**Curate timing:** when Claude notices context is roughly past ~70% full, or when a logical task boundary is reached, Claude should **suggest** "curate and compact" to the user. Don't wait until auto-compact is imminent — by then there isn't enough headroom left for the curator to do its work without hitting the context limit itself.

**Auto-compact caveat:** when Claude Code triggers `/compact` automatically (context limit hit), the curator does not fire. Acceptable tradeoff because (a) Tier 1 inline capture has been running on every turn, so the most important stuff is already written, and (b) the user can manually trigger curation when they sense context is getting full.

---

## Core Rules

These live in `rules/CLAUDE.md` and are absolute. Project rules cannot override them.

1. **Any edits, additions, amendments, or removal of rules must be approved by the user.**
2. **When in doubt** — if information seems outdated, incorrect, or uncertain — **research online or ask the user** for further direction, information, or context.
3. **Only one project can be selected as the focus at a time**, however the adjacent projects' codebases and memory should remain available in case the user wants to reuse elements or ideas from those.
4. **The user and Claude must reach agreement before acting on ambiguous scope or amending memory.** No silent decisions on contested or unclear points.
5. **A project must always be selected before continuing.** Core-only sessions are not a valid state.
6. **Secrets never live inside this repo.** Credentials, tokens, passwords, SSH keys, private access URLs, and other sensitive values live outside the repo in a dedicated "priv" location that is **OS-specific**, just like a project's codebase path. The per-OS location of the priv folder is recorded in a Core `long-term-mem/` entry (same `OS:PATH` format as `path.md`), resolved at runtime based on the current OS. Memory is git-synced and must be treated as if it could be made public tomorrow. Long-term-mem and project memory may contain *pointers* (relative paths inside priv, filenames, or descriptions of where a secret lives) but **never the secret values themselves**. When a session needs a secret, Claude resolves the priv path for the current OS, reads the specific file at runtime, and never writes the value back into memory, logs, or commit messages.

More Core rules may be added later, but always under Core Rule #1 (user approval).

---

## Build Scope

When Claude executes this template, the following gets built:

- The full folder skeleton (Core + `projects/`), with `CLAUDE.md` in every folder.
- Core Rules seeded in `rules/CLAUDE.md`.
- Project scaffolds for the initial projects supplied in the Initial Interview (both codebase and memory-only).
- `path.md` for each codebase project, pre-filled with the OS paths supplied in the interview. Memory-only projects get no `path.md`.
- Two-tier curation model baked into Core CLAUDE.md as instructions.
- A global pointer file on the current OS (`~/.claude/CLAUDE.md` on Linux/macOS, `%USERPROFILE%\.claude\CLAUDE.md` on Windows) telling any future Claude session: *"This user uses the claude-md memory system at `<path>`. `cd` there and read `./CLAUDE.md` before doing anything else."* Equivalent files on other OSes are created from those machines during Cross-Platform Migration.
- A personalized `plan.md` copied from this template, with placeholders filled in from the interview answers. `plan.md` becomes the fidelity baseline for the user's instance.
- Core `long-term-mem/user-profile.md` seeded with the identity answers from the interview. Other long-term-mem seeds (preferences, hardware, people, priv location) follow via the First-Run Initialization flow.

---

## My Initial Projects

*Claude: populate this section during the build phase using the answers from Initial Interview § 4. One entry per project. Leave this section as-is in `template_plan.md`; fill it in only in the copy that becomes `plan.md`.*

Format for codebase projects:

```
### <project-name>
- **Type:** Codebase
- **Description:** <one-line description>
- **Paths:**
  - Linux: <absolute path>
  - Windows: <absolute path>
  - macOS: <absolute path>
- **Notes:** <anything the user mentioned during the interview — scope, state, collaborators>
```

Format for memory-only projects:

```
### <project-name>
- **Type:** Memory-only
- **Description:** <one-line description>
- **Related projects:** <codebase projects this feeds into, if any>
- **Notes:** <anything the user mentioned during the interview — scope, purpose>
```

Skipped / not-a-project items (backups, reference-only code, logos, scratch folders) are listed here too with a one-line reason and, if relevant, a pointer into `knowledgebase/` where their content lives instead.

---

## Git Sync Discipline

Git (push/pull) is the nervous system. Without it, the cross-machine handoff silently breaks. This is the single biggest failure mode of the design, and it gets explicit rules in Core CLAUDE.md:

- **At session start**, before reading any memory, run `git pull` in the memory repo. If that's skipped, Claude reads stale state from yesterday's session on another machine.
- **After any memory write** (Tier 1 capture, Tier 2 curator-approved writes, rule amendments, graduation events), stage, commit, and push:
  - `git add <changed-files>`
  - `git commit -m "<short description>"` — these commits are the memory changelog; descriptive messages matter.
  - `git push`
- **If `git pull` shows merge conflicts in memory folders, stop and ask the user.** Never auto-resolve. Memory conflicts mean two machines learned different things about the same topic; that's a user decision, not Claude's.
- **At session end**, confirm there are no uncommitted or unpushed memory changes. If there are, commit and push before the session closes so the next machine sees them.

Commits should be small and topical — one per graduation, one per curator pass, etc. — so the memory changelog is readable as a diff history. Think of each commit as an entry in the system's audit log.

**Commit frequency — hybrid model.** Not every write needs its own commit:

- **Tier 1 lightweight captures** (memory-worthy moments during normal work) are **batched** — the files are written immediately, but committed together at task-end, session-end, or when the user says "save." This keeps history readable without losing mid-session work.
- **Tier 2 curator writes, rule amendments, truths additions, graduation events** are committed **individually** — each is a deliberate, reviewable moment and deserves its own entry in the audit log.
- **At session end**, always sweep for uncommitted memory changes and commit/push them before closing. Nothing goes home uncommitted.

**File size awareness.** GitHub rejects files over 100 MB and warns at 50 MB. The repo should stay well under 5 GB total. Before committing any non-text file (PDFs, images, archives, binaries, firmware):

- **Under 50 MB:** commit normally.
- **50–100 MB:** warn the user. Discuss whether to use Git LFS or extract content to markdown.
- **Over 100 MB:** cannot push. Must use Git LFS or keep outside the repo (e.g. in priv, with a pointer in memory).

Prefer extracting useful content into markdown over storing raw binaries — markdown is searchable, diffable, and lightweight. When the full original is genuinely needed (datasheets, schematics, firmware), use Git LFS on a case-by-case basis. No blanket file-type rules; each large file is a conversation. Core `knowledgebase/git-lfs.md` has the install/track/diagnose reference.

---

## First-Run Initialization

On day 1 the system is empty — every folder has a `CLAUDE.md` and an `index.md` and nothing else. A "well-informed Claude" doesn't materialize until the memory gets populated. To avoid weeks of slow accumulation, the first real session after the skeleton is built is dedicated to seeding Core `long-term-mem/`.

The flow, driven by Core CLAUDE.md:

1. Claude detects that Core `long-term-mem/index.md` is empty or nearly empty and triggers the first-run flow.
2. Claude interviews the user across the core topics:
   - **User profile** — role, background, expertise, preferred communication style (may already be partially seeded from the Initial Interview).
   - **Preferences** — coding style, tools, languages, workflow habits, what to avoid.
   - **Hardware and environment** — machines, OSes, shell, dev setup per machine.
   - **People and collaborators** — teams, key contacts, role relationships.
   - **Priv location** — the per-OS `priv/` path (per Core Rule #6).
3. For each topic, Claude drafts a `long-term-mem/<topic>.md` entry and presents it for user approval before writing (Core Rule #4).
4. After approval, Claude writes the files, updates `index.md`, and commits them as one initialization commit (or a few topical commits).
5. First-run flow is marked complete by a note in `long-term-mem/index.md` so it doesn't re-trigger.

The interview is not exhaustive. It's enough to get Core long-term-mem past the "empty" threshold. Everything else accumulates organically via the graduation flow.

---

## Cross-Platform Migration

The initial build happens on one machine. Any additional machines need their own one-time setup:

1. Clone the memory repo.
2. Add the OS-specific `OS:PATH` line to each project's `path.md`.
3. Add the OS-specific priv folder path to the long-term-mem entry that tracks priv locations.
4. Create the OS-specific global pointer file (`~/.claude/CLAUDE.md` on Linux/macOS, `%USERPROFILE%\.claude\CLAUDE.md` on Windows) with that machine's absolute path to the memory repo.
5. Migrate any existing local CLAUDE.md / MEMORY.md content from that machine into Core memory, if relevant.

Until those steps are complete on a given machine, the system is one-eyed there.

---

## Honest Risks

1. **Reliability comes from instructions, not infrastructure.** There is no harness backstop — no hooks, no MCP, no automation. The system works only as well as Claude follows the instructions in the CLAUDE.md files. That means the CLAUDE.md files have to be *good* — clear, unambiguous, and short enough that they actually get read. Vague or bloated CLAUDE.mds are the primary failure mode.
2. **Folder fatigue.** 12 places to write. The graduation flow needs to actually happen or `short-term-mem/` becomes a write-only graveyard. The two-tier curation model is the main defense; if Tier 2 isn't triggered often enough, revisit.
3. **Git merge conflicts** if memory is edited from two machines without syncing first. The Git Sync Discipline section is the answer — and it has to be followed.
4. **The system only works if it gets used.** A 50-file knowledgebase is useful; a 2-file knowledgebase is theater. Early on it will feel sparse — that is normal, do not abandon it.

The design is conservative: most of it borrows validated prior art (Cline Memory Bank's session-start ritual, RooCode's rules/memory split, Cursor's per-glob rule scoping, ADR-style rejection logging). Only `truths`/`lies` and the explicit graduation flow are genuinely novel — and both address real failure modes that other systems don't address.

Success depends more on usage discipline than on the architecture. There is no backstop — no hooks, no automation — so the instructions in CLAUDE.md files and the user's own habits are the whole enforcement mechanism. That's the tradeoff for portability: the design works the same on every machine with zero per-machine setup beyond the global pointer file, but every machine also has to carry the same discipline.

---

## Prior Art Worth Knowing

- **Claude Code's built-in CLAUDE.md hierarchy** (enterprise → user → project → subdir) and `@path/to/file.md` import syntax. This system layers on top by treating the root CLAUDE.md as a thin router that imports from the rest.
- **Cline "Memory Bank"** — fixed-filename discipline, session-start read ritual. Borrowed.
- **RooCode** — splits prescriptive `rules/` from descriptive memory. Validates this design.
- **Cursor `.cursor/rules/`** — per-glob rule activation. Adopted informally.
- **ADRs (Architecture Decision Records)** — the professional ancestor of `lies/`.
- **Letta / MemGPT** — tiered memory with vector storage. Heavyweight; not adopted.
- **`affaan-m/everything-claude-code`** — a full agent-harness performance system with hooks, subagents, and automation. Too heavy for this template; the simpler path of pure instruction-driven CLAUDE.md files was chosen consciously.

---

## What This Document Is

This is a template for building a universal Claude memory system. Once Claude has run the Initial Interview and built the skeleton, this file should be copied to `plan.md` with the placeholders filled in, and **`plan.md` becomes the fidelity baseline for that user's instance**.

- A fresh Claude instance can read `plan.md` and understand *why* the system is the way it is.
- The user can reference `plan.md` when amending the system.
- Future architectural decisions have a baseline to be measured against.

**This plan is a starting point, not a finished artifact.** Every time the system evolves in practice — new failure modes, new patterns, new needs — `plan.md` evolves with it under Core Rule #1. The initial version serves as a fidelity baseline: keep a copy of it so you can always measure how far the system has drifted from its original shape and decide whether that drift was an improvement or a regression.

When the design changes, the plan changes with it — always under Core Rule #1.
