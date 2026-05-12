---
title: macOS Has No timeout Command
created: 2026-04-11
trigger: bash script uses `timeout` on macOS
---

# macOS Has No `timeout` Command

## Problem
`timeout 600 some_command` works on Linux (GNU coreutils) but fails on macOS with "command not found".

## Fix Options
1. **Remove it** — if the command has its own timeout mechanism (e.g. Claude CLI `--max-turns`)
2. **Install GNU coreutils** — `brew install coreutils` then use `gtimeout`
3. **Use Perl one-liner** — `perl -e 'alarm 600; exec @ARGV' -- some_command`

## Applied
`graph-seed.sh`: replaced `timeout 600` with `--max-turns 50` on the Claude CLI call.
