---
title: SPA refresh token 24h family cliff — preempt at 23h, don't react at 24h
created: 2026-04-24
source: songclub-m365 auth port from hive-agent v2.6
---

Azure AD SPA refresh tokens have a **hard 24h family lifetime** from the ORIGINAL issue time. Rotations via the `/token` endpoint issue a new refresh token but **do not reset the clock** — the new token inherits the original family's expiry. At 24h Azure returns `AADSTS700084` and `invalid_grant`.

Reacting at 24h is fragile: the failure hits under load, cascades into Playwright-based re-login, and often overlaps with other processes trying the same thing → concurrent browser spawns → timeout. **Preempt at 23h** by tracking a separate `l3_captured_at` timestamp (only the interactive/SSO capture path writes it; refresh-rotation must NOT update it), and triggering re-capture when `l3_captured_at` age > 23h while the current access token is still valid as a fallback.

Three invariants that make this work:
1. `l3_captured_at` is preserved across refresh rotations — otherwise preempt never fires
2. Post-lock cache re-check inside the re-capture path — another process may have already refreshed while you were waiting for the file lock
3. Circuit breaker (30min) if re-capture fails in automated mode — don't burn browser spawns retrying a broken network or SSO

Reference implementation: hive-agent `mcp/hive-m365/src/auth.js` @ v2.6.
