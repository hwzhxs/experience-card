---
title: Download .vtt for meeting transcripts, never .docx
created: 2026-04-09
source: retro from COOKBOOK ch.10
---

Meeting transcripts from Microsoft Graph should be downloaded as `.vtt` (WebVTT format), never `.docx`. The `.docx` files are IRM-encrypted and unreadable without the original user's credentials. VTT is plain text with timestamps and speaker labels — always works.
