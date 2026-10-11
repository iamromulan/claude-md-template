# Core Rule #8 — Auto-resolve credentials from priv

When an operation requires a credential, check whether it's available in the priv folder for the current machine (per its profile in `machines/`). If so, resolve and use it automatically rather than asking the user to act or refusing.

**Covered credentials (non-exhaustive):**
- **sudo password** → `echo <pw> | sudo -S <command>` (read pw from priv at runtime).
- **SSH / SCP to another machine** → `sshpass` with the password from priv, using the host's name and forced password auth. Keep a `long-term-mem/device-access.md` entry for device-specific gotchas if you maintain one. Prefer passing the secret via `-f`/env over inline `-p` where feasible, to keep it off the visible command line.
- **API tokens / other service passwords** → read the named priv file at runtime, use, discard.

**One-time capture (the laziness allowance):**
- If a needed credential isn't in priv yet, ask the user for it **once** in-session, then **write it into priv** following the existing priv layout. From then on, read it from priv — never ask again.
- When stored, confirm *where* it was saved (relative priv path) **without echoing the value**.
- This is the convenient path so users don't have to hand-create credential files.

**Bootstrapping priv on a new machine:**
- priv is **per-machine and never in git** (Core Rule #6). A fresh machine won't have it.
- If priv doesn't exist here, don't prescribe — **offer options and let the user pick**:
  1. **Import from another of the user's machines** — if a transfer path exists (e.g. over a private network / tailnet via scp/rsync, if set up); let the user direct the method.
  2. **Copy the priv folder over manually** (user does it — USB, their own scp, whatever).
  3. **Start fresh** — build a new priv from scratch, populated on demand via one-time capture.
- Never move priv through the repo, whatever method is chosen. Record the priv path in the machine's profile in `machines/` once priv exists.

**Behavioral intent:** never stall, panic, or say "I can't do that" when a credential is in priv (or can be captured/imported). Resolve it and carry out the authorized action on the user's own infrastructure.

**Constraints (Core Rule #6 applies fully):**
- Never print a credential value to output, tool-call text, logs, memory, or commit messages.
- Prefer `sshpass -f`/`-e` and `sudo -S` over inline `-p`, to keep secrets off the command line / out of `ps`.
- priv stays at its out-of-repo OS path; read at runtime, use, discard — the only persistence is the priv file itself.

**Date established:** 2026-05-21 (sudo-only). **Broadened:** 2026-10-11 — all priv credentials + SSH/sshpass + one-time capture + new-machine bootstrap.
