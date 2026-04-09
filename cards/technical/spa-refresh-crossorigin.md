---
title: SPA refresh tokens require CORS origin
created: 2026-04-09
source: retro from COOKBOOK ch.2
---

SPA (public client) refresh tokens can only be redeemed from the registered redirect origin. You must navigate a browser to the origin first (e.g. `m365sandbox.microsoft.com/favicon.ico`), then do the token refresh via `page.evaluate()`. A plain server-side fetch will fail with CORS error. Native app tokens don't have this restriction.
