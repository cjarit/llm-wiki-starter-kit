---
name: steward
description: >
  Health check of the LLM Wiki in the current folder: orphans, broken links, frontmatter, stale
  notes, stale Current state sections, provenance drift, overlapping notes, missing concept pages and
  unmarked sensitive content.
  Produces a patch list the owner approves before anything is fixed. Run manually with /wiki:steward.
disable-model-invocation: true
---
# /wiki:steward — Lint

Run every check below, build a patch list, and wait for approval before fixing. The sensitive-content scan is report-only.

## Step 0 — Find the vault

The wiki root is the current working directory and must contain `CLAUDE.md` and `GOVERNANCE.md`. The notes root is the wiki root, or its `Main/` folder when `index.md` and `log.md` are there instead (two-level layout); every other path in this skill is relative to the notes root. If either is missing, stop and tell the user to open Claude Code inside their wiki folder. Read `GOVERNANCE.md` for the allowed values and formats the checks use.

Content notes are every `.md` under the vault except `CLAUDE.md`, `GOVERNANCE.md`, `index.md`, `log.md`, `_templates/`, `raw/` and `.obsidian/`.

## Checks

1. **Orphans** — a content note neither listed in `index.md` nor linked from another note. List it with the proposed index line. A note listed in the index but with no inbound `[[wikilink]]` is "graph-invisible": info only.
2. **Broken or ambiguous links** — a `[[wikilink]]` whose target does not exist, a `[[DP-NNN]]` whose principle lacks that alias, a bare name that matches more than one file.
3. **Principle aliases** — every file in `principles/` carries `aliases: ["DP-NNN"]`.
4. **Frontmatter** — every content note has valid `type`, `domain`, `status` and a non-empty `summary`, using the values in `GOVERNANCE.md`.
5. **Stale `updated:`** — a note whose file modification date is later than its `updated:` field. Propose the correct date.
6. **Provenance drift** — a note whose `source:` is a local file newer than `ingested:`. Show both dates; the owner decides whether to re-ingest.
7. **Index coverage** — a content note missing from `index.md`, with the proposed line.
8. **Contradictions** — two notes, or a note and a principle, that state conflicting facts or decisions. Flag for the owner; never resolve.
9. **Unlogged raw files** — a file in `raw/` (outside `processed/`) with no matching `ingest` entry in `log.md`. Queue for `/wiki:curator`.
10. **Overlapping notes (DP-003)** — within the same folder, two notes whose titles, aliases or filenames share the main topic term and either share two or more H2 headings or have `summary` lines about the same subject. A hub and the pages it lists, or notes that only share vocabulary, are not hits (say why in one line). Propose merging the newer into the older and moving the newer to `_archive/` with a pointer line, or "keep — distinct because …". Never pair a restricted note with an unmarked one.
11. **Stray per-source notes (DP-002)** — a content note named after a date or a single source (for example `2026-10-20-…`, `meeting-…`, `notes-from-…`) outside `raw/`. Propose decomposing it into the owning notes.
12. **Missing concept pages** — a term, name or topic that appears in three or more notes and has no note of its own and no home in an existing note. Propose a note title; creating it follows curator Step 4.
13. **Stale Current state** — a note with a **Current state** section whose `_As of YYYY-MM-DD_` stamp is missing, or whose `status: active` and stamp is older than the limit in `GOVERNANCE.md` § Freshness. A Current state already written as an Unconfirmed status block (`_Last confirmed: … · Status unclear as of …_`, see curator Step 6) passes. Fix: convert the section to that block (a dated table of what is known, **Unclear**, **To confirm**); never just bump the date. The owner approves each conversion.
14. **Unmarked sensitive content (report-only)** — a note without `classification: restricted` that contains an email address, a phone number, an ID-like number, a credential-like string (`password`, `token`, `api key` followed by a value), or a named person next to health, pay or performance wording. Report file and category only, never the value. Owner's options per hit: mark the note restricted, split the sensitive lines into a restricted note, or de-identify. Also report the count of restricted notes by category.

## Patch list

```
## /wiki:steward Report — YYYY-MM-DD

### Check 1: Orphans (N found)
- path/note.md — not in index.md and no inbound link
  Fix: add to index.md under <folder>

### Check 14: Unmarked sensitive content
- [reported] path/note.md — email address, not marked restricted
  (options: mark restricted / split / de-identify)

### No issues found
- Check 3: Principle aliases — all clear
```

Summary line: `N checks run · N issues found · N flagged for the owner`. State what each "none found" searched (folders and note count); a check that could not run is "not checked", not a pass.

**Stop here and present the patch list. Apply nothing yet.**

## On approval

Apply only the approved patches. Check 8 is never applied; checks 6 and 9 are handed to the owner to run `/wiki:curator`. If an approved patch touches a note that already has the owner's uncommitted edits, say so and ask before staging it. For each fix, edit the file. Then append `## [YYYY-MM-DD] lint | /wiki:steward — N fixes applied` to `log.md`. If the folder is a git repository, stage each changed file by path and commit locally: `wiki(steward): N fixes applied — <checks>`. Never push.

## Constraints

- Never delete content; propose merges and `_archive/` moves only.
- Never auto-resolve a contradiction.
- Check 14 changes nothing on its own.
