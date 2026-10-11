# machines — Core

One **profile per machine** the user runs Claude + a claude-md memory system from. Parallel to `projects/`: `projects/` tracks *what* is worked on; `machines/` tracks *where* it's worked from.

A profile holds that box's hardware, OS, shell, **priv location + status**, cloned repos + `path.md` fit, toolchain (vs the baseline below), available Claude Code skills/MCP, tailnet status, and freeform notes. Priv locations live **in each profile** (not in a separate per-OS file).

## Startup behavior — Device check & onboarding

At session start, after the enforcement layer loads, identify the **current machine** and look for its profile here.

- **Known machine** → load `machines/<name>.md` into the session, so Claude starts already knowing this box's priv path, available skills, and quirks.
- **New / unrecognized machine** → onboard it:
  1. Gather the machine's basics (see *What to gather*).
  2. **Check `gh` auth** — if `gh` isn't authenticated, help the user set it up **first**; the repo can't be administered without it.
  3. Ask the user: **"Is there anything else I should know about this machine?"**
  4. Create `machines/<name>.md` (user confirms, per Core Rules #1/#4), add it to `index.md`, and commit.
  5. If **priv is absent**, run the Core Rule #8 bootstrap (offer import from another machine / manual copy / start fresh).

A machine counts as "new" when no profile here matches its name — or, as a heuristic, when the global `~/.claude/CLAUDE.md` pointer is absent or no `path.md` convention matches the current OS.

## What to gather (the *what*, not the *how*)

Collect this data using whatever method fits the OS — Linux/macOS shell, Windows PowerShell / `systeminfo`, Termux, etc. The profile defines the **data**, never a hardcoded command (don't assume `uname`):

- hostname, OS + version, arch, kernel
- CPU / RAM / storage
- shell + login user
- toolchain presence vs the baseline (below)
- cloned repos + whether `path.md` OS conventions match
- tailnet status (if any)
- **priv path + presence**
- available Claude Code skills + MCP servers

## Recommended tools baseline

- **Essential (every machine):** `git` and `gh` — a Claude session administers the repo through them (clone, pull, commit, push, PRs). If `gh` isn't authed, fixing that is the first onboarding step.
- **Strongly recommended:** `sshpass` — needed for the Core Rule #8 password-auth SSH/SCP pattern to reach other machines.
- **Nice-to-have (never assumed):** Tailscale — convenient for reaching other machines / importing priv, but not every device is on a tailnet.
- **Optional, per need:** node/npm, python, docker, adb, language toolchains.

Each profile's Toolchain section records present/missing against this baseline; onboarding flags any missing essentials.

## Profile format

**Filename = hostname.** Name each profile `machines/<hostname>.md` — the device check looks it up by the machine's hostname, so they should match most of the time. (If a box's hostname isn't known yet, a descriptive placeholder name is fine temporarily — rename to the real hostname once captured.) A profile's content comes from **two sources**: details gathered **from the machine itself** (OS, specs, toolchain, tailnet… via the OS-appropriate method) and details gathered **from the user** ("anything else I should know about this machine?").

`machines/<hostname>.md`, with frontmatter (`machine`, `os`, `arch`, `added`, `last_confirmed`) and these sections:

- **Hardware** — model, CPU, RAM, storage
- **OS & shell** — OS/version, kernel, arch, shell, login user
- **Priv** — location on THIS machine (`<path>` or "not present") + status
- **Repos & paths** — which repos are cloned + whether `path.md` OS convention matches
- **Toolchain** — git/gh/sshpass/node/python/docker/adb/… present vs missing (against the baseline)
- **Claude Code env** — skills installed/available, MCP servers, notable config (statusline/hooks)
- **Tailnet** — tailnet hostname/IP, which tailnet(s), switch quirks
- **Notes** — "anything else I should know about this machine?" (freeform)

## Index

See `index.md` for the roster. Lazy-loaded — the current machine's profile is loaded at startup (device check); others only when relevant.
