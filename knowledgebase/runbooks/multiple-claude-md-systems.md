# Multiple claude-md systems on one machine

A person can run **more than one claude-md memory system** on the same computer — e.g. their own personal `claude-md` (built from the template) **and** a shared team system like `claude-md-fenixstar`. Each is a separate git repo with its own memory, rules, and startup protocol. This runbook is how they coexist without stepping on each other. It is identical in every claude-md system.

## The global pointer is a dispatcher

`~/.claude/CLAUDE.md` (Windows: `%USERPROFILE%\.claude\CLAUDE.md`) is loaded into **every** Claude Code session on the machine. With more than one system, it lists them all and says how to pick:

```markdown
# Claude Code — Global Pointer (claude-md dispatcher)

This machine has one or more **claude-md memory systems**. Each session uses exactly ONE.

| System | Repo path | Notes |
|---|---|---|
| claude-md (personal) — **default** | /abs/path/to/claude-md | personal + all non-team projects |
| claude-md-fenixstar | /abs/path/to/claude-md-fenixstar | shared FenixStar Tech team memory |

**Which system:**
1. If the session's starting directory is **inside** one of the repo paths above, that system is authoritative — follow ITS `CLAUDE.md` startup protocol and ignore every other system's.
2. Otherwise use the **default** system: `cd` there and follow its `CLAUDE.md`.
3. If the user names a system ("work in fenixstar", "my personal memory"), switch to it.
4. Unsure which one the user means → ask.

**Never mix systems:** memory captured in a session goes to that session's system only. Moving content between systems is an explicit, user-requested action ("cherry-pick X into fenixstar"), and the *destination's* rules apply (e.g. no personal content or secrets in a shared repo).
```

- **Exactly one row is the default.** A person with only one system has a one-row table.
- **Matching is by path containment, done carefully:** `/x/claude-md-fenixstar` is *not* inside `/x/claude-md` even though the string starts the same — compare whole path components.

## Setting up / adding a system on a machine

- **No pointer yet** → create it from the block above with this system as the only (default) row.
- **Pointer already exists** (any older format — e.g. the template's single-system "Before doing anything else, `cd` to X" pointer) → **don't overwrite it.** Convert it to the dispatcher format, keeping the existing system as the **default** row, and **add** the new system as a row. Show the person the result before saving.
- **Removing a system** → delete its row; if it was the default, ask which remaining one becomes default.
- Each system's **machine profile** (`machines/<hostname>.md` → *Claude Code env*) notes which other claude-md systems share the machine.

## Startup-protocol interactions

- **Auto-memory audit scope:** Claude Code keeps per-directory auto-memory in `~/.claude/projects/<encoded-path>/memory/`, where `<encoded-path>` is the session directory with `/` → `-` (e.g. `-Users-cam-Documents-GitHub-claude-md-fenixstar`). Each system's audit **skips the auto-memory directories that belong to another registered system's repo** (exact encoded-path match, or that path + `-…` for its subdirectories — and again, `…-claude-md` is not the same repo as `…-claude-md-fenixstar`). Those belong to the other system's audit. Pointer files in each directory name the system they defer to.
- **priv can be shared.** Several systems on one machine may use the same priv folder (Core Rule #6/#8); each records the path in its own machine profile.
- **Nothing else is shared.** Rules, active-project pointers, people/machines profiles, and git history are per-system.

## Scenarios

| Situation | Pointer |
|---|---|
| Teammate with **only** the team system | One row: the team system (default). |
| Person with a **personal system + the team system** | Personal = default row; team system = second row. Start Claude inside the team repo to use it, or say "work in fenixstar". |
| Teammate who **already** has a personal claude-md from the template | Same as above — convert their existing single-system pointer, keep personal as default, add the team row. Their personal system stays untouched. |
| Teammate who later **creates** a personal system | Add it as a row; they choose which is default. |
