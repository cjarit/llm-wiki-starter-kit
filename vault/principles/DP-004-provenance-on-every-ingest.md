---
title: DP-004 — Provenance on every ingest
aliases: ["DP-004"]
type: principle
domain: cross-cutting
status: active
created: 2026-10-26
updated: 2026-10-26
tags: []
summary: A note built from a source records where it came from and when, so drift can be detected and claims traced.
related: ["[[DP-001]]"]
---

## Principle

Every note built from an external source carries `source:` (path, URL, or `synthesized`) and `ingested:` (date). Every written claim can be traced to a source or is marked as an assumption.

## Rationale

A wiki that cannot say where a fact came from cannot be checked, and cannot tell when its source has moved on. Provenance lets `/wiki:steward` compare the source's modified date with `ingested:` and flag stale notes instead of trusting them.

## Applies to

- `/wiki:curator` — stamps `source` and `ingested` on every write.
- `/wiki:steward` — provenance drift check.
