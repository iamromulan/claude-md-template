# claude-md

A universal, file-based memory system for Claude Code. One Claude, anywhere, that already knows you.

## What is this?

This template lets you build a personal memory system that replaces Claude Code's built-in per-project memory with a single, git-synced repo that works across all your machines and all your projects. Clone it on any machine, point Claude at it, and you pick up exactly where you left off.

**This is a template.** Fork or clone it, run the Initial Interview, and Claude builds your personalized memory system from the skeleton included here.

## Setup — from zero to running

You need: a GitHub account, git installed, and Claude Code installed.

### 1. Create your private memory repo

```bash
gh repo create claude-md --private --clone
cd claude-md
```

Or use the GitHub web UI to create a private repo, then clone it.

### 2. Copy the template files into your repo

Copy everything from this template repo into your new private repo. The key files:

- `plan.md` — the full design document and Initial Interview
- `CLAUDE.md` — the root router (operational instructions for Claude)
- All folder scaffolding (`rules/`, `lies/`, `truths/`, `short-term-mem/`, `long-term-mem/`, `knowledgebase/`, `projects/`)

```bash
# If you cloned this template separately:
cp -r /path/to/claude-md-template/* /path/to/claude-md-template/.* your-claude-md/
```

Commit and push:

```bash
git add -A
git commit -m "seed from claude-md template"
git push
```

### 3. Run the Initial Interview

Open Claude Code inside your repo and say:

> *"Read `plan.md` and run the Initial Interview."*

Claude will ask you about:
- Your identity and communication preferences
- Which OSes you use and where your repos live
- Your projects (both codebase and memory-only)
- Where you keep secrets/credentials
- Any seed rules

This takes ~10 minutes. Claude builds the full skeleton from your answers.

### 4. Create the global pointer

Create `~/.claude/CLAUDE.md` so Claude finds your memory system from anywhere:

```markdown
# Global pointer

This user uses the **claude-md universal memory system**.

**Before doing anything else in any session:**

1. `cd /path/to/your/claude-md`
2. Read `./CLAUDE.md` and follow its session-start ritual
3. All memory reads and writes go through that repo.
```

### 5. You're live

From now on, open Claude Code and say **"Begin new session"**. Claude will pull, clean up any unfinished business, load your memory, and ask which project to work on.

## How it works

### Begin new session

Say this at the start of every session. Claude will:

1. `git pull` and clean up uncommitted/unpushed changes from previous sessions
2. Audit Claude Code's built-in auto-memory for stray entries and import them
3. Load your enforcement layer (rules, lies, truths)
4. Ask which project to continue or switch to
5. Reload recent context so you know where things left off
6. For codebase projects: check for repo CLAUDE.md files to assimilate

### Switching projects

Say *"let's work on `<project-name>` now"* at any time.

### Importing external knowledge

> *"Import this into `<project>` short-term-mem for analysis"*

Works with any markdown — someone else's CLAUDE.md, API docs, research notes. Good parts graduate to long-term memory through the normal flow.

### Saving and syncing

- **Automatic:** Claude commits at task boundaries and session end
- **Manual:** Say *"save"* for an immediate commit
- **Always:** Claude pushes before the session closes

### Curating memory

> *"curate and compact"*

Claude scans all memory folders (including across projects), proposes cleanups, waits for approval, then compacts. Say just *"curate"* if you don't need to free up context.

## Project types

### Codebase projects

Have a real repo on disk. Claude reaches the codebase via `path.md` (one `OS:PATH` line per platform).

### Memory-only projects

No codebase, no `path.md`. Used for organizing knowledge, research, life areas, or anything that doesn't map to a single repo. Examples: `life` (general life organization), `research` (topic-specific investigation).

Both types share the same folder scaffold.

## Folder structure

```
./
├── CLAUDE.md              <- Root router (session-start protocol, workflow rules)
├── plan.md                <- Design rationale, Initial Interview, architecture
├── rules/                 <- Core rules (always loaded, absolute)
├── lies/                  <- Proven dead ends (always loaded)
├── truths/                <- User-asserted ground truth (always loaded)
├── short-term-mem/        <- Session scratchpad, research dropbox (lazy-loaded)
├── long-term-mem/         <- Promoted stable facts (lazy-loaded)
├── knowledgebase/         <- Reference material, personal wiki (lazy-loaded)
└── projects/
    └── <project>/         <- Each project has the same 6 folders
        ├── CLAUDE.md
        ├── path.md        <- (codebase projects only)
        └── ...
```

## Key concepts

### Information flow

```
New info / research  -->  short-term-mem/  -->  long-term-mem/ + knowledgebase/
                          (volatile)            (stable, graduated)
```

Promotion requires explicit agreement between you and Claude. Nothing auto-promotes.

### Enforcement layer (always loaded)

- **rules/** — prescriptive ("must do / must not do")
- **lies/** — things that sound right but are proven wrong in your context
- **truths/** — user-asserted facts that override Claude's training data

### Lazy-loaded layer (on demand)

- **short-term-mem/** — volatile working memory, indexed
- **long-term-mem/** — stable promoted facts, indexed
- **knowledgebase/** — full reference material, indexed

## Core rules

1. Rule changes require user approval
2. When in doubt, research or ask
3. One active project at a time (others remain accessible)
4. User and Claude must agree before amending memory
5. A project must always be selected
6. Secrets never live in this repo (use the priv folder)

## Adding a new machine

1. Clone your private memory repo
2. Create `~/.claude/CLAUDE.md` with the global pointer (see setup step 4)
3. Open Claude Code and say "Begin new session" — it will ask for any missing OS-specific paths
4. Claude will detect and import any existing local auto-memory

## Tips

- **Say "Begin new session" every time** — it handles cleanup, sync, and context reload
- **Say "curate" before context gets full** — don't wait for auto-compact
- **Use memory-only projects liberally** — life, career, health, research topics, anything
- **Import external docs into short-term-mem first** — analyze before promoting
- **Commit messages matter** — they're your memory changelog
- **Keep the repo private** — it contains your personal context and preferences

## Design rationale

See `plan.md` for the full architecture, prior art, trade-offs, graduation flow, curation model, and honest risks.

## Credits

Designed by [iamromulan](https://github.com/iamromulan). Built with Claude Code.
