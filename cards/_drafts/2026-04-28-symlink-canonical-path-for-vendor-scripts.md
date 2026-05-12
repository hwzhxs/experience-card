---
title: Symlink to vendor's canonical path instead of forking their scripts
category: methodology
source: retro 2026-04-28
status: draft
---

When a team's automation hardcodes a layout you don't follow (e.g. HIVE-SETUP.md assumes `~/Library/Hive/team-kb`, but your clone lives at `~/SongClub/personal/projects/team-kb`), don't fork their scripts to match your paths — symlink their canonical path to your real location. The vendor's `auto-push.sh` / wrapper / plist stay byte-identical with their repo, so future pulls of their tooling Just Work; your data stays where your other tools expect it. The only thing you own is the symlink + (if needed) a thin local-only wrapper that chains your additions after their script exits. This keeps the diff between you and the team at zero in their codebase, surfaces all your customizations in one tiny owned shim, and avoids the maintenance debt of a long-running fork. Applies any time you're adapting team-shared automation to a personal directory layout: prefer redirection over duplication, and reserve modifications for files outside the upstream repo.
