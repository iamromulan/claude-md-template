# long-term-mem — Core

Promoted, stable facts. Things that survived the short-term filter via the Graduation Flow.

## Organization

**Topic-organized, not date-organized.** Example file names:

- `user-profile.md` — role, background, expertise, communication preferences
- `preferences.md` — coding style, tool choices, workflow habits, things to avoid
- `hardware-and-environment.md` — machines, OSes, shell, dev setup per machine
- `people-and-collaborators.md` — teams, key contacts, relationships
- `workflow.md` — recurring workflows, routines
- `priv-locations.md` — per-OS path to the priv folder (per Core Rule #6); created when secrets first become relevant

## Entry format

Each topic file uses frontmatter:

```yaml
---
topic: <name>
date_established: YYYY-MM-DD
date_last_confirmed: YYYY-MM-DD
---
```

Long-term does not mean immortal. Keep `date_last_confirmed` current so age is visible.

## When an entry moves

- **Becomes uncertain or contested:** move back to `short-term-mem/`.
- **Proven wrong:** move to `lies/`.
- **Declared as ground truth by the user:** move to `truths/` (user initiates, never automatic).

## Index

See `index.md` for the list of topic files. Lazy-loaded — open individual files only when the topic is relevant to the current conversation.

## First-run seeding

On day 1 this folder is empty. The **First-Run Initialization** flow (see root `CLAUDE.md` and `plan.md`) interviews the user across the core topics above, drafts each file, and writes them after approval. Mark first-run complete with a note in `index.md` so it doesn't re-trigger.
