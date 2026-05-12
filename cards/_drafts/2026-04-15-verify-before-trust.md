---
title: Verify every automated output before trusting it
category: methodology
source: retro 2026-04-15
status: draft
---

Automated pipelines that call LLMs (Claude CLI, GPT, etc.) must verify outputs before downstream consumption. "Exit 0" does not mean "output is correct." A script can succeed technically while producing empty files, malformed JSON, or missing sections. Every LLM call in an automation pipeline needs a post-call verification gate: check the output file exists, is non-empty, has expected structure (JSON schema, marker strings, minimum size). Failures should be logged explicitly and reported in health checks — never silently swallowed. This is the Generator-Verifier pattern from multi-agent coordination applied to single-agent automation.
