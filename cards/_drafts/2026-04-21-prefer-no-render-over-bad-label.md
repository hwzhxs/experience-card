---
title: Prefer showing nothing over a meaningless label
category: design-craft
source: retro 2026-04-21
status: draft
---

When rendering data from a source you don't fully control, **don't synthesize labels from raw column names when the upstream didn't provide a human label** — drop the card instead. On 2026-04-21 the Search/NTP dashboard tabs showed SQL-derived fallbacks like `sumIf(InferredMetricValueNr, MetricCode = 'OMNIB` and `COUNT(*)` for charts where Superset UI didn't name the widget. Iteratively softening the SQL parser just produced less ugly garbage. The right move was to **require a DOM-scraped title** — if Superset's UI didn't give the chart a title, that chart isn't user-facing (it's a filter box, debug widget, or upstream glitch). The rule: *upstream-labeled = show it; upstream-unlabeled = hide it*. This prevents the dashboard from educating the user on your data pipeline's limitations. Same principle applies anywhere you aggregate third-party data into your own UI — defer to the source's naming, and when it's silent, be silent.
