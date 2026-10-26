---
title: DP-003 — Match before create
aliases: ["DP-003"]
type: principle
domain: cross-cutting
status: active
created: 2026-10-26
updated: 2026-10-26
tags: []
summary: Before any new note is created, search the destination folder for the note that already owns the topic; create only with a stated reason and the owner's OK.
related: ["[[DP-001]]", "[[DP-002]]"]
---

## Principle

Before a new note is created in any folder, that folder and its subfolders are searched by title, aliases, headings, summary and key terms. If the content would fit an existing note as a section, an update, or the same subject at another depth or version, it is merged there. A new note is created only when nothing fits, with the closest existing note and the reason it was not used stated, and with the owner's OK.

## Rationale

Merging is only a habit if the check happens every time. Without a search step, new material lands as a standalone note beside an older note on the same subject, and the wiki slowly fills with overlaps.

## Applies to

- `/wiki:curator` — search, fit test and preview before any write.
- `/wiki:steward` — proposes merging overlapping notes; never deletes.
- Any edit made to the wiki without a skill follows the same rule.
