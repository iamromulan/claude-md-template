# short-term-mem — Core

The volatile working layer. Two roles:

1. **Session scratchpad** — where work was left off, open questions, untested hypotheses, recent decisions not yet hardened.
2. **Research dropbox** — when Claude or the user gathers new info from online research, it lands here first for analysis and testing before it gets trusted.

Append-only, timestamped. **Never trust this layer blindly** — it's the most volatile in the system.

## Flow

Entries have two possible fates:

- **Graduate** to `long-term-mem/` + `knowledgebase/` via the Graduation Flow (requires explicit user-and-Claude agreement). Once graduated, the short-term copy is deleted.
- **Prune** after they age out or are no longer relevant.

If neither happens, the curator (Tier 2) sweeps stale entries.

## Special file: `active-project.md`

The last-active-project pointer. Contains the active project name, the machine it was last touched on, and the date.

- **First-run exception:** on a fresh install this file does not exist. Create it during the first session as part of Active Project Selection, with the user's chosen project.
- **Committed individually and immediately** whenever it changes — it is the cross-machine handoff mechanism and must always be pushed before the next machine picks up. Unlike other short-term entries, it is **not** batched.

## Index

Other short-term entries are indexed in `index.md`. Lazy-loaded — only open individual files when the topic matches the current conversation. Add new entries to `index.md` when created.

## Entry format

Free-named markdown file. Include a timestamp and a one-line summary at the top. Beyond that, whatever shape the content needs.
