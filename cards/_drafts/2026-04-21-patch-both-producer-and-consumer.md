---
title: Patch both producer and consumer when inserting a new upstream step
category: methodology
source: retro 2026-04-21
status: draft
---

When inserting a new step upstream of an existing consumer to produce a shared artifact (cache file, DB row, temp state), the producer patch alone is not enough — audit the downstream consumer for its own attempts to regenerate the same artifact. A consumer that still tries to produce the artifact itself will silently clobber the new upstream output, and the regression looks identical to the original bug. Concrete case: SongClub daily-digest.sh — adding Step 1.5 (`fetch-slack.js`) to write `slack-digest.md` was only half the fix; Step 2's Claude CLI prompt still instructed the LLM to call `slack_conversations_history` MCP and write the same file, which under launchd returned empty and overwrote the good data. The complete patch had to both (a) add the producer and (b) rewrite Step 2 Part A to *only read* the file and explicitly forbid re-fetching. Rule: any time you add a pre-step to populate an output, grep the rest of the pipeline for writers to that same path, and neuter them in the same commit.
