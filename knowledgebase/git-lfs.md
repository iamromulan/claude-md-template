# Git LFS — Quick Reference

Git Large File Storage replaces large files in the repo with small pointer files. The actual content is stored on GitHub's LFS server and downloaded on demand.

## When to use

- A file is 50+ MB (GitHub warns) or 100+ MB (GitHub rejects the push)
- A binary file that will be revised often (each revision is a full copy in git history)
- Decided on a case-by-case basis — no blanket rules by file type

## Install (once per machine)

```bash
# Debian/Ubuntu/Pop!_OS
sudo apt install git-lfs

# macOS
brew install git-lfs

# Windows
# Download from https://git-lfs.com or: winget install GitHub.GitLFS

# Then initialize in any repo:
git lfs install
```

## Track a specific file

```bash
# Track one file by path
git lfs track "knowledgebase/src_code/big-firmware.bin"

# Track by pattern (use sparingly — prefer per-file)
git lfs track "*.bin"

# This creates/updates .gitattributes — commit it
git add .gitattributes
git add knowledgebase/src_code/big-firmware.bin
git commit -m "add big-firmware.bin via LFS"
```

The `.gitattributes` entry looks like:
```
knowledgebase/src_code/big-firmware.bin filter=lfs diff=lfs merge=lfs -text
```

## Check what's tracked

```bash
git lfs ls-files          # list LFS-tracked files in current commit
git lfs track             # list patterns from .gitattributes
git lfs status            # show pending LFS changes
```

## Diagnose problems

**Push rejected for file size:**
```
remote: error: File X is 123.45 MB; this exceeds GitHub's file size limit of 100.00 MB
```
Fix: track the file with LFS, then amend or recommit.

**File was committed without LFS (already in history):**
```bash
# Migrate an existing file to LFS (rewrites history)
git lfs migrate import --include="path/to/file" --everything

# Or if it's only on the current branch:
git lfs migrate import --include="path/to/file"
```
Warning: `migrate import` rewrites history. Coordinate with anyone else using the repo.

**LFS not installed on a new machine:**
Clone will succeed but LFS files will be pointer files (tiny text). Fix:
```bash
sudo apt install git-lfs   # or brew/winget
git lfs install
git lfs pull               # downloads actual file content
```

## Free tier limits (GitHub personal)

- **10 GB** storage
- **10 GB** bandwidth per month
- **2 GB** max per individual file
- Overages are pay-per-use

## Preference order for large content

1. **Extract to markdown** — distill the useful parts, don't store the raw file at all
2. **Keep outside the repo** — store in priv or another location, put a pointer in memory
3. **Git LFS** — when the full original must live in the repo (datasheets, schematics, firmware)
