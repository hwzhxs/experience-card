---
title: Playwright upgrade requires browser re-download
created: 2026-04-15
trigger: Playwright scripts return empty data silently after upgrade
---

## Principle

When Playwright is upgraded (e.g., via `npm update`), the browser binaries must be re-downloaded with `npx playwright install`. The old browser version (e.g., chromium-1208) won't be used — the new version (e.g., chromium-1217) is expected but doesn't exist yet.

## Failure Mode

**Silent failure**: Playwright's `browser.launch()` throws, but if the calling script catches errors broadly, it returns empty data without any error message. You see "0 items, null counts" and assume the data source is empty, not broken.

## Detection

- Dashboard shows empty OCV data for multiple consecutive days
- Explore mode shows `apiResponseCount: 0, domSnippet: ""` (page never loaded)
- Check `~/Library/Caches/ms-playwright/` for version mismatch

## Fix

```bash
npx playwright install chromium
```

## Related

Also check SSO flow — after browser profile is reset, the "Pick an account" page appears. Scripts must auto-click the account tile, not just wait.
