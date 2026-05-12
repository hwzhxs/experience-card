---
title: Stash Before git checkout HEAD
created: 2026-04-15
trigger: restoring files to committed version
---

# Stash Before git checkout HEAD

## Principle
`git checkout HEAD -- file` **permanently destroys** uncommitted working directory changes. There is no undo — no reflog, no recovery. Always preserve changes before restoring.

## What Happened
- Used `git checkout HEAD -- css/oneline.css` to restore committed version
- Lost all of the previous session's liquid glass upgrades, icon swap CSS, and 11 files of work
- Had to rebuild everything from memory (took hours)

## Rule
Before any `git checkout HEAD --`:
```bash
# Option 1: stash everything
git stash push -m "backup before restore"

# Option 2: save specific file diff
git diff path/to/file > /tmp/backup.patch

# Option 3: copy the file
cp path/to/file /tmp/file.backup
```

## Also Applies To
- `git restore --source=HEAD`
- `git checkout -- .` (nuclear version — destroys ALL changes)
- Any destructive git operation on uncommitted work
