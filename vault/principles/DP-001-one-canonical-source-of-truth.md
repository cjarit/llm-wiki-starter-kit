---
title: DP-001 — One canonical source of truth
aliases: ["DP-001"]
type: principle
domain: cross-cutting
status: active
created: 2026-10-26
updated: 2026-10-26
tags: []
summary: Each fact lives in exactly one note; every other note links to it instead of copying it.
related: ["[[DP-002]]", "[[DP-003]]"]
---

## Principle

A fact is written once, in the note that owns its topic. Other notes link to that note. If two notes state the same fact, one of them is deleted from the text and replaced by a link.

## Rationale

Copies drift. When a fact changes, the copy you forgot to update becomes a second, wrong answer, and nothing tells a reader which one to trust. One home means one place to update and one place to check.

## Applies to

- `/wiki:curator` — writes an item into its owning note and links from elsewhere.
- `/wiki:steward` — flags notes that overlap.
