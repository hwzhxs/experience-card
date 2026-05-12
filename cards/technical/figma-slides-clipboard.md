---
title: Figma Slides text extraction via Playwright clipboard
category: technical
source: retro 2026-04-15
status: promoted
---

## Principle

Figma Slides renders ALL content in a WebGL/Canvas element. Text is not accessible through DOM, accessibility tree, or any Figma API endpoint. The only way to extract text is via the system clipboard:

1. Playwright opens the Slides file in Edge (msedge channel for Microsoft SSO)
2. Click slide thumbnail in left panel → focus canvas → Cmd+A (select all) → Cmd+C (copy)
3. `navigator.clipboard.readText()` returns the slide's full text content
4. Repeat for each slide

## What doesn't work

- **REST API `/v1/files`** → 400 "File type not supported"
- **REST API `/v1/images`** → Overview page renders, but sub-slides return solid black images
- **DOM `innerText`** → Only returns Figma editor UI text (toolbar, panels), not slide content
- **CDP Accessibility Tree** → Same UI-only text, canvas content invisible
- **Clipboard HTML format** → Contains Figma internal binary scene data, no hyperlink URLs

## What you get

- Project names, descriptions, status (✅ / ⏭️ / In Progress)
- People names (usually last line of each slide)
- Milestones, blockers, link labels
- NOT: hyperlink URLs (clipboard only has text labels, not hrefs)

## Performance

~30 seconds for 10 slides, ~3 seconds per slide.
