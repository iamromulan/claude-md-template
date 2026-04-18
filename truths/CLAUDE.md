# truths — Core

User-asserted ground truth. Overrides anything Claude might "think it knows" from training data when there is a conflict.

**Rare and load-bearing.** Truths are not written casually. A truth is something the user has explicitly declared as a foundational fact — not a preference, not a habit, not a long-term belief. Preferences and habits go in `long-term-mem/`.

## Entry format

Each entry is a free-named markdown file containing:

- **The statement**
- **Date established**
- **Why it's considered true** — source: user assertion / verified by testing / documentation link
- **Scope** — when and where it applies

## Behaviour around truths

- **Never silently contradict a truth.** If new evidence conflicts with one, surface the conflict to the user and wait for direction before changing anything.
- **Never auto-promote from `long-term-mem/` to `truths/`.** Truths are created only by explicit user assertion.
- **Cross-reference `lies/`** only when relevant — a truth that explains why a particular lie is a lie is worth linking.

## When a truth falls

If a truth turns out to be wrong, move it to `lies/` (if actively false) or `long-term-mem/` (if merely uncertain), with a note explaining the transition. Truths never quietly disappear.
