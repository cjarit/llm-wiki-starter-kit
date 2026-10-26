---
title: DP-002 — Integrate, don't append
aliases: ["DP-002"]
type: principle
domain: cross-cutting
status: active
created: 2026-10-26
updated: 2026-10-26
tags: []
summary: A source is raw material, not a page — split it into items and merge each into the note that owns the topic.
related: ["[[DP-001]]", "[[DP-003]]"]
---

## Principle

A source (an article, a meeting, a message, a report) is not filed as a note of its own. It is split into atomic items — a fact, a decision, a status change, an open question — and each item is merged into the existing note that owns that topic. The note's current-state text is rewritten to match; what it replaced is kept as one short history line.

## Rationale

A pile of per-source notes holds fragments of the truth and none of them is the current state, so every question means opening them all. Integrating keeps one page that says what is true now, with history underneath.

## Applies to

- `/wiki:curator` — decomposes every source before writing.
- `/wiki:archivist` — reads the owning note first and falls back to history only for past-period questions.
- `/wiki:steward` — flags per-date or per-source notes outside `raw/`.
