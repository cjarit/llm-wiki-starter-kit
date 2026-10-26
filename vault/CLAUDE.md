# Wiki — Schema

This folder is a personal knowledge wiki maintained by an LLM and read by a human. It follows the "LLM Wiki" pattern: raw sources go in, the LLM integrates them into a persistent, interlinked set of markdown notes, and every answer is built from those notes instead of re-reading the sources.

> Rulebook: [GOVERNANCE.md](GOVERNANCE.md)
> Skills (plugin `wiki`): `/wiki:curator` · `/wiki:archivist` · `/wiki:steward`

## Three layers

| Layer | Where | Owner | Rule |
|---|---|---|---|
| Raw sources | `raw/` | Human | Immutable. The LLM reads them and moves finished ones to `raw/processed/`; it never edits their content. |
| Wiki | `projects/ areas/ reference/ principles/` + `index.md`, `log.md` | LLM | The LLM writes and maintains every note here. The human reads, corrects and approves new notes. |
| Schema | `CLAUDE.md`, `GOVERNANCE.md` | Both | Co-evolved. Change a rule here, not in a chat. |

## Folders

- `projects/` — bounded efforts with an end: one note per project or per workstream.
- `areas/` — ongoing interests and responsibilities with no end date.
- `reference/` — methods, how-tos, facts that outlive any project.
- `principles/` — the rules this wiki lives by (`DP-NNN-slug.md`). Edit with care.
- `_templates/` — Obsidian note templates.
- `index.md` — catalog of every note. `log.md` — append-only history.

Rename or add domain folders to fit your life, then update this section and the `domain` list in GOVERNANCE.md in the same change.

## Structure choice

Default: single level — this folder is both the repo and the Obsidian vault. Alternative: two levels, with the schema, governance and logs in an outer folder and the notes in an inner `Main/` folder that Obsidian opens, so system files stay out of the graph. To switch, tell Claude "convert this wiki to the two-level layout"; it moves `index.md`, `log.md`, `raw/` and the note folders into `Main/`, fixes links, tells you to open `Main/` in Obsidian, and commits. The skills find `Main/` on their own.

## For agents

1. Read `index.md` first. Never scan the whole vault unless the question needs it.
2. Any write to a note follows `/wiki:curator`: split the source into items, search the destination folder for the note that already owns each topic, merge there, and create a new note only with a stated reason and the owner's OK ([DP-003](principles/DP-003-match-before-create.md)).
3. Never edit anything under `raw/` except moving a finished file into `raw/processed/`.
4. A note marked `classification: restricted` is never published, exported, embedded, indexed or synced out, and its details are never copied into an unmarked note.
5. Append one line to `log.md` for every ingest, update and lint.
6. Commit to git locally after each change, staging files by path. Never push unless the owner asks.
7. Never restate an old status as current: a Current state that no source has confirmed within the freshness limit ([GOVERNANCE.md](GOVERNANCE.md) § Freshness) is written as unconfirmed.
