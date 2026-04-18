# lies — Core

Things that *sound* right but have been proven wrong in your context. Closest professional prior art: ADR "rejected options" sections.

**Purpose:** prevent Claude from re-suggesting things that have already been tried and failed. Before recommending a solution, cross-reference this folder (plus the active project's `lies/`) to make sure you're not about to repeat a known dead end.

The name "lies" is deliberate — more definitive than "rejected" or "dead ends." If it's here, it's been tried and it doesn't work.

## Entry format

Each entry is a free-named markdown file containing:

- **The false statement** — what sounded right
- **Why it's false** — what actually happens when you try it
- **Correct understanding** — link to a `truths/` entry if applicable
- **Date discovered**
- **Scope** — project, OS, hardware, or whatever narrows where this lie applies

## When to write

Add an entry when a solution has been tried and **provably failed**. Not for preferences — those go in `long-term-mem/`. Not for partial information or hunches — those go in `short-term-mem/` first.

## When to remove

If a lie later turns out to be conditional ("it failed because of X, which is no longer true"), move it to `long-term-mem/` with a note explaining the conditional, and leave a small stub in `lies/` pointing at the new entry so the history isn't lost.
