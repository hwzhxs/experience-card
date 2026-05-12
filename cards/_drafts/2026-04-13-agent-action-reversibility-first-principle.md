---
title: Agent action reversibility-first principle
category: methodology
source: auto-mined 2026-04-13
status: draft
confidence: 0.92
---

When designing agent automation, the primary decision rule is reversibility: take action automatically only when the action is reversible; require explicit confirmation when it is not. Reversibility is a user-context property, not an action property — canceling a ticket may be technically possible but practically irreversible if the user has already committed socially. The blast radius concept extends forgiving design from single-action undo (ctrl+z) to high-stakes multi-step consequences. Design rule: before shipping any agent action, answer 'how does the user undo this?' — if the answer is unclear, force confirmation.
