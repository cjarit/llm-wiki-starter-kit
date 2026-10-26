# Governance

The rulebook for this wiki. Read before adding, changing or querying anything.

## Naming

- `UPPERCASE.md` — system files (`CLAUDE.md`, `GOVERNANCE.md`, `index.md`, `log.md` are the only ones).
- `lowercase-kebab.md` — content notes, named by topic, never by date or source.
- No version suffixes (`-v2`, `-final`). Rewrite in place and log the change.
- Principles: `DP-NNN-slug.md`.

## Frontmatter

Every content note starts with:

```yaml
---
title: Descriptive title
aliases: []
type: note | project | source | decision-log | research | principle
domain: projects | areas | reference | cross-cutting
status: active | evergreen | archived | superseded
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: []
summary: One sentence — the retrieval hook used in index.md.
related: []
source: path-or-url-or-synthesized
ingested: YYYY-MM-DD
classification: open | internal | restricted
---
```

- `source` and `ingested` are required on any note built from an external source; `source: synthesized` means assembled from several sources.
- `classification` is optional; absent means `internal`.
- If a field can express it (type, domain, status), use the field, not a tag.

## Linking

- Use `[[wikilinks]]` for internal references, path-qualified when the name could repeat: `[[projects/home-garden]]`.
- Principle notes carry `aliases: ["DP-NNN"]` so `[[DP-003]]` resolves.
- Every content note is listed in `index.md` and has at least one inbound link.
- Never link from an unmarked note into a restricted note.

## Classification

Judge by what is inside a note, never by its folder.

| Tier | Content |
|---|---|
| open | Methods and public reference. |
| internal | Your own facts, decisions, notes. The default. |
| restricted | Anything that ties a named person to private data (health, pay, performance, private contact details), and anything you would not paste into a chat. |

A restricted note is never published, exported, embedded, indexed or synced out. A source that mixes tiers is split, or the whole note is marked restricted. A general insight may be promoted from a restricted note into an unmarked one only after the owner removes every name and identifier by hand; the agent proposes, the owner decides.

Secrets (passwords, keys, tokens) do not belong in the wiki. If you must keep a pointer to one, keep it in a `secrets/` folder, which is git-ignored, and never quote a value.

## Provenance

A note built from an external source records `source:` and `ingested:`. When the source file changes after `ingested:`, `/wiki:steward` flags the note for re-ingest. Point-in-time snapshots (a report, an export) set `status: archived` or say "snapshot" in `summary`.

## index.md

- The master `index.md` lists every content note under its folder heading.
- Line format: `- [Title](path/to/note.md) — one-line summary`
- Update it on every ingest. When a folder passes about 15 notes, add a hub `index.md` inside it and link the hub from the master.

## log.md

Append-only. One entry per operation, with a fixed prefix so it can be parsed:

```
## [YYYY-MM-DD] ingest | Source title
## [YYYY-MM-DD] update | note-slug — what changed
## [YYYY-MM-DD] query | question — note written back, if any
## [YYYY-MM-DD] lint | what was checked and fixed
```

Last five entries: `grep "^## \[" log.md | tail -5`

## Operations

Each lives in one place: the skill file in the `wiki` plugin. This section only says what it is for.

- `/wiki:curator` — ingest one source or a live note; integrate, don't append.
- `/wiki:archivist` — answer a question from the wiki, index first, with citations.
- `/wiki:steward` — health check that proposes a patch list for approval.
