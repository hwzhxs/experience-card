---
title: Figma Slides API only renders overview, not individual slides
category: technical
source: retro 2026-04-15
status: draft
---

## Principle

Figma's `/v1/images` API can render screenshots of Slides files, but only for top-level overview nodes (0:1, 0:3). Individual slide pages render as solid black images regardless of scale (tested scale=0.5, 1, 2). The `/v1/files` endpoint returns 400 "File type not supported" for Slides entirely.

## Implication

To extract content from Figma Slides, the only viable approach is browser automation (Playwright): open the Slides URL, navigate through pages, and capture screenshots of the rendered content. The Figma REST API path is a dead end for per-slide content.

## What Works

- `/v1/projects/:id/files` — list files in a project (works for all types including Slides)
- `/v1/images/:key?ids=0:1` — renders the overview/TOC page of Slides
- Batch ID requests return valid URLs but download as black images for sub-slides
