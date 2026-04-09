---
title: Teams renders HTML only, not Markdown
created: 2026-04-09
source: retro from COOKBOOK ch.7
---

Teams chat messages only render HTML (`<b>`, `<br>`, `<a href>`). Markdown (`**bold**`, `[link](url)`, `` `code` ``) shows as raw text. Always use `contentType: "html"` with `send_chat_message`. No code blocks, no markdown tables, no raw URLs.
