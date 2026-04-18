# claude-md — Core router

This repo is a universal Claude Code memory system. It replaces the traditional per-project CLAUDE.md on every machine. The working directory for any session is always this repo; project codebases are reached via the absolute paths stored in each project's `path.md`.

For the full design rationale read `plan.md`. This file is the operational entry point.

---

## "Begin new session" — the startup protocol

The user says **"Begin new session"** to start. This triggers the full startup sequence. If the user jumps straight into work without saying it, run steps 1–3 silently and pick up from wherever they left off.

### 1. Git pull + unclean state check

- **`git pull`** in this repo. Never skip — cross-machine handoff depends on it. If the pull shows merge conflicts inside memory folders, **stop and ask the user**. Never auto-resolve.
- **Check for unclean state** from a previous session that didn't close properly:
  - `git status` — any uncommitted memory changes? If so, show them and ask: commit and push, or discard?
  - `git log origin/main..main` — any unpushed commits? If so, push them now.
  - This catches the case where a session ended without proper cleanup (crash, timeout, forgot to push).

### 2. Auto-memory audit

Check for stray entries in Claude Code's built-in auto-memory (`~/.claude/projects/*/memory/`):

- Scan for `.md` files that aren't the claude-md pointer.
- If found, show them to the user. These are memories Claude Code wrote outside the claude-md system.
- Offer to import useful ones into the appropriate claude-md location (usually `short-term-mem/` for analysis first).
- After import, remove the stray entries — keep only the pointer that says "defer to claude-md."
- On a brand-new machine where no pointer exists yet, create one.

### 3. Load enforcement layer

Always-loaded in full:
- `@rules/CLAUDE.md` and every other `.md` file in `rules/`
- `@lies/CLAUDE.md` and every entry file in `lies/`
- `@truths/CLAUDE.md` and every entry file in `truths/`

### 4. Active project selection + context reload

- Read `short-term-mem/active-project.md` to find the last-active project.
  - **First-run exception:** if the file does not exist, this is a fresh install. Ask the user which project to activate from `projects/`. Create `short-term-mem/active-project.md` with their choice, machine name, and today's date. Commit it immediately.
- Ask the user: *"Last active: `<name>` on `<machine>` `<date>`. Continue, or pick another from: `<list>`?"*
- Once confirmed, load the active project's `CLAUDE.md` + its enforcement layer the same way.
- **Context reload:** read Core `short-term-mem/index.md` and the active project's `short-term-mem/index.md`. Summarize any recent entries to the user so they know where things left off. This is the continuity bridge between sessions.

### 5. Codebase CLAUDE.md assimilation (codebase projects only)

When loading a codebase project, check if the project's actual repo has a `CLAUDE.md` at its root:

- If one exists and hasn't been assimilated yet, read it and offer to import its contents into the project's memory in claude-md (rules go to `rules/`, known issues to `lies/` or `short-term-mem/`, architecture notes to `long-term-mem/` or `knowledgebase/`).
- The project repo's CLAUDE.md can stay in place (other contributors may depend on it), but claude-md's project memory becomes the authoritative source for this user.
- Track what's been assimilated in the project's `short-term-mem/` or `long-term-mem/` so it doesn't re-trigger.

### 6. Lazy-load the rest

Everything inside `long-term-mem/`, `short-term-mem/` (beyond what was loaded for context reload), and `knowledgebase/` is **lazy-loaded** via each folder's `index.md`. Only open individual files when the conversation actually calls for them.

---

## Workflow model — edit surfaces

There are two kinds of memory projects:

- **Codebase projects** have a `path.md` pointing to the actual repo. Three edit surfaces: Core memory, project memory, and the project codebase.
- **Memory-only projects** have no codebase and no `path.md`. Two edit surfaces: Core memory and project memory.

Every action in a session must touch one of these surfaces:

1. **Core memory** — this repo at `./`. Universal across all projects.
2. **Active project memory** — this repo at `./projects/<active-project>/`. Scoped to the current project.
3. **The active project's actual codebase** (codebase projects only) — at the path resolved from `projects/<active-project>/path.md` for the current OS.

Anything outside those requires explicit user approval.

### Running commands against the active project (codebase projects only)

You are always `cd`'d into `claude-md`. **Never `cd` into the project codebase.** Scope commands instead:

- **File operations (Read, Edit, Write, Glob, Grep):** use absolute paths from the active `path.md`.
- **Git operations on the project repo:** `git -C <project-path> <command>`. Plain `git status` hits `claude-md`, not the project — that's almost always a bug.
- **Build / test / dev / lint:** `cd <project-path> && <command>` in a single Bash call.
- **Never** `cd` alone as a standalone command. Shell state doesn't persist between Bash calls.

---

## Precedence

- **Within a layer:** `rules` > `lies` > `truths`.
- **Across layers:** Core wins over Project on conflict. These layers shouldn't conflict in theory. If they do, **flag it to the user and stop** — never silently pick a side.

---

## Tier 1 — Inline lightweight capture (every turn)

On every user/assistant exchange, scan for **memory-worthy moments**: corrections, preferences revealed, dead ends discovered, facts confirmed, decisions made, project state changes.

- If found, write a single entry to the appropriate folder.
- If not found — normal on most turns — do nothing. Silence is fine. **Do not invent reasons to write.**
- Tier 1 writes are **batched**: files are written immediately, but committed together at task-end, session-end, or when the user says "save."

---

## Tier 2 — Heavy curator (user-triggered)

When the user says **"curate"** (alone, or paired with "compact"), run a full curator pass:

1. Read the on-demand folders end-to-end — `long-term-mem/`, `short-term-mem/`, `knowledgebase/` for both Core and the active project. Enforcement layers are already in context from bootstrap; cross-reference them, don't re-read.
2. **Cross-project scan:** read the `index.md` of every other project's `long-term-mem/` and `short-term-mem/`. Look for entries that have outgrown their project scope — facts that apply universally belong in Core, not buried in one project. Memory-only projects like `life` especially tend to generate entries that are really Core material (personal context, goals, people, preferences). If an entry serves all projects, propose graduating it to Core memory.
3. Identify: new entries warranted, contradictions with existing memory, short-term entries ready to graduate (within the active project and up to Core), stale long-term entries, knowledgebase gaps, dead ends to add to `lies/`, project entries that belong in Core.
4. Present a **punch list** to the user.
5. Wait for explicit per-change approval (Core Rules #1 and #4).
6. Apply approved writes. Commit each meaningful change individually.
7. If the user also said "compact", run `/compact` **after** curation, never before. Compaction is lossy; curation has to finish first.

**Curate timing suggestion:** when context feels ~70% full, or at a logical task boundary, proactively suggest "curate and compact" to the user. Don't wait for auto-compact — the curator needs headroom to do its work.

**Auto-compact caveat:** if Claude Code triggers `/compact` automatically, the curator does not fire. Tier 1 should have captured the important stuff already; the user can still run a manual curate afterward.

---

## Graduation flow

Information moves: `online research / new info -> short-term-mem/ -> (user + Claude agreement) -> long-term-mem/ + knowledgebase/`.

- Promotion requires **explicit agreement** (Core Rule #4). No automatic promotion.
- Graduating means simultaneous writes to `long-term-mem/` (distilled fact) and `knowledgebase/` (full cleaned source). The short-term copy is then deleted.
- A long-term entry that turns out wrong moves back to `short-term-mem/` (if uncertain) or `lies/` (if proven false).
- Truths are created only by explicit user assertion — never auto-promoted.

---

## Git sync discipline

- **Session start:** `git pull`. Skipping this is the single biggest failure mode.
- **Tier 1 captures:** batched; commit at task-end / session-end / on "save."
- **Tier 2 writes, rule amendments, graduation events, `active-project.md` changes:** committed **individually and immediately**. Each is a deliberate, reviewable moment.
- **Merge conflicts in memory folders:** stop and ask the user. Never auto-resolve.
- **Session end:** sweep for uncommitted memory changes; commit and push before closing. Nothing goes home uncommitted. If the push fails (auth, network), warn the user — the next session's unclean state check will catch it, but they should be aware.

Commit messages describe the memory change ("add lie: X doesn't work on Y", "graduate short-term hypothesis on Z"). These commits are the memory changelog — readability matters.

### File size awareness

Before committing any non-text file (PDFs, images, archives, binaries, firmware), check its size:

- **Under 50 MB:** commit normally, no action needed.
- **50-100 MB:** warn the user. GitHub will show a warning. Discuss whether to use Git LFS or extract content to markdown instead.
- **Over 100 MB:** **cannot be pushed to GitHub.** Must use Git LFS or keep the file outside the repo (e.g. in priv, with a pointer in memory).

When in doubt, prefer extracting useful content into markdown over storing raw binaries. Markdown is searchable, diffable, and lightweight. Store the original only when the full file is genuinely needed.

See `knowledgebase/git-lfs.md` for the LFS install/track/diagnose reference when the situation arises. The repo should stay well under 5 GB total.

---

## Secrets (Core Rule #6)

Never write secret values into memory. Ever. Memory is git-synced and must be treated as if it could be made public tomorrow.

- The per-OS location of the priv folder is recorded in a Core `long-term-mem/priv-locations.md` entry (same `OS:PATH` format as `path.md`). This entry is **not** created until the user first needs a secret — it will be seeded during First-Run Initialization or on demand.
- Memory may contain *pointers* to secrets (relative paths inside priv, filenames, descriptions). Never the values themselves.
- When a session needs a secret, resolve the priv path for the current OS, read the specific file at runtime, and never write the value back into memory, logs, or commit messages.

---

## First-run initialization

If Core `long-term-mem/index.md` is empty or nearly empty, trigger the First-Run flow (see `plan.md` for the full flow). Interview the user across: user profile, preferences, hardware and environment, people and collaborators, priv location. Draft each `long-term-mem/<topic>.md` and present for approval before writing. Mark complete with a note in `long-term-mem/index.md` so it doesn't re-trigger.

---

## Core Rules

See `rules/CLAUDE.md`. They are absolute. Project rules cannot override them.
