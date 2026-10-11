# Core Rules

These are absolute. Project rules **cannot** override them. They are loaded into context in full at every session start.

1. **Any edits, additions, amendments, or removal of rules must be approved by the user.** Claude drafts, the user approves. Never write a rule silently.

2. **When in doubt — research online or ask the user.** If information seems outdated, incorrect, or uncertain, do not guess. Surface the uncertainty, gather more context, and confirm before acting.

3. **Only one project can be selected as the focus at a time.** However, the adjacent projects' codebases and memory remain available in case the user wants to reuse elements or ideas from them.

4. **The user and Claude must reach agreement before acting on ambiguous scope or amending memory.** No silent decisions on contested or unclear points. When the scope of a task is unclear, stop and clarify.

5. **A project must always be selected before continuing.** Core-only sessions are not a valid state. If the active-project pointer is missing, create it (first run) or ask the user to pick one.

6. **Secrets never live inside this repo.** Credentials, tokens, passwords, SSH keys, private URLs, and any other sensitive values live outside the repo in a dedicated "priv" location that is **OS-specific**, just like a project's codebase path. The priv location is recorded in the current machine's profile in `machines/` (see `machines/CLAUDE.md`), resolved at runtime for the current machine.

   Memory is git-synced and must be treated as if it could be made public tomorrow. Memory may contain *pointers* to secrets (relative paths inside priv, filenames, short descriptions of where a secret lives) but **never the secret values themselves**. When a session needs a secret: resolve the priv path for the current OS, read the specific file at runtime, use it, and never write it back into memory, logs, or commit messages.

7. **Never let research go to waste.** When online research or deep investigation is performed, the findings must be captured in the memory system (typically `knowledgebase/`) before moving on. Include references to where the information came from (URLs, repo paths, file names, forum posts) so the source can be revisited if needed. Research that stays only in conversation context is lost at compaction.

8. **Auto-resolve credentials from priv.** When an operation needs a credential that lives in priv — a `sudo` password, an SSH/SCP password, an API token — resolve it for the current machine (per its profile in `machines/`) and use it automatically instead of asking the user or claiming you can't (`sudo -S`; `sshpass` for SSH/SCP). If a needed credential isn't in priv yet, ask the user once, then store it in priv so future sessions read it automatically. If priv doesn't exist on this machine, offer options and let the user choose: import it from another of their machines (e.g. over a private network / tailnet if set up — user directs the method), copy it over manually, or start a fresh priv and fill it on demand — never via git. Never expose a credential value in output, logs, memory, or commit messages — Core Rule #6 applies fully. See `rules/credentials-via-priv.md`.

---

Additional rule categories (coding style, communication, git workflow, etc.) live as separate `<category>.md` files in this folder as they accumulate. Glob-scoped rules (e.g. "applies when editing `*.rs`") are written informally in prose — no frontmatter required.

More Core rules may be added later, but always under Core Rule #1 (user approval).
