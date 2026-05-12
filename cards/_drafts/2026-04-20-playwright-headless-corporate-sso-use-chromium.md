---
title: playwright-headless-corporate-sso-use-chromium
category: technical
source: auto-mined 2026-04-20
status: draft
confidence: 0.8
---

In corporate environments, named browser channels (e.g., msedge, chrome) in headless Playwright trigger SSO account-picker flows that silently block automation. Fix: use default Chromium for headless runs; reserve named channels for headed/interactive sessions only. Appeared twice: OCV SSO account picker blocked msedge headless, fixed by switching to Chromium channel.
