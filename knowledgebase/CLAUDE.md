# knowledgebase — Core

Reference material. Full, organized copies of source material Claude can consult on demand. The personal wiki / second brain.

## What goes here

- API references, command cheat sheets, recipes, runbooks, postmortems
- Full cleaned-up source of anything that graduated from `short-term-mem/` (the long-term-mem entry holds the distilled fact; the knowledgebase entry holds the full source)
- Anything bulky, structured, and worth keeping that isn't a rule, a fact, or a dead end

Core `knowledgebase/` holds material that spans projects. Project `knowledgebase/` holds project-specific material.

## Freely subdividable

Add category subfolders as new kinds of knowledge accumulate. Reasonable starting categories:

- `src_code/` — full source of reference code (e.g. vendor-provided SDKs, working examples worth keeping)
- `api_refs/` — API / protocol references
- `runbooks/` — step-by-step procedures
- `recipes/` — copy-pasteable snippets
- `postmortems/` — what went wrong and why

The "life" project test: a software-project knowledgebase and a "life" knowledgebase will look nothing alike. That's the point.

## Index discipline

**`index.md` is authoritative.** Every file and subfolder must have a one-line entry there. When creating new knowledgebase content, update `index.md` in the same write. Without the index, Claude has to read the whole folder to know what's in it — which defeats the point of lazy loading.

## Reading pattern

Lazy-loaded. On any task that might need reference material, read `index.md` first, decide what's relevant, open only those files.
