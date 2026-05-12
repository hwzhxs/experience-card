---
title: Verify Server Working Directory Before Editing
created: 2026-04-15
trigger: editing files served by a local dev server
---

# Verify Server Working Directory Before Editing

## Principle
Before editing any file served by a local dev server, **always verify the server's working directory matches the directory you're editing**. Multiple copies of the same repo can exist on disk, and editing the wrong one means changes never appear in the browser.

## Check Command
```bash
lsof -p $(lsof -i :PORT -t) | grep cwd
```

## What Happened
- Server ran in `~/EdgeMobileOnLine/`, edits went to `~/Desktop/agentic_projects/EdgeMobileOnLine/`
- "Changed a million times with no effect" — because browser was reading from the other copy
- Hours of debugging CSS/JS when the real issue was a path mismatch

## Rule
1. Start of session: check server cwd
2. If mismatch: restart server in correct directory, or edit the correct directory
3. After restart: `curl` a changed file from localhost to confirm it's the new version
