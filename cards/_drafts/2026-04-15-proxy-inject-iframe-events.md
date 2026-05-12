---
title: Proxy-Inject for Cross-Origin iframe Event Detection
category: technical
source: retro 2026-04-15
status: draft
---

Cross-origin iframes completely swallow wheel, scroll, and pointer events — parent page cannot listen via addEventListener, overlay divs, or any DOM trick. The only reliable solution is a **server-side proxy** that fetches the target HTML and injects a reporter script before serving it to the iframe. The injected script uses `window.top.postMessage()` to relay scroll position, direction, and edge colors back to the parent. This makes the iframe effectively same-origin while preserving all original page behavior.
