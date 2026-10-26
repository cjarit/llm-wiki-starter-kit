---
name: curator
description: >
  Ingest one source into the LLM Wiki in the current folder — a file (markdown, text, PDF, Word,
  spreadsheet), pasted text, or a decision or outcome — by splitting it into items and merging each
  into the existing note that owns it; a new note only when nothing fits and the owner approves.
  Use when the user asks to add, save, record, capture, file or ingest anything into the wiki or
  knowledge base, drops a file in raw/, or is about to create or edit a note without naming this
  skill. Not for questions about what the wiki says (archivist) or health checks (steward).
argument-hint: "[path/to/file | live text]"
---
# /wiki:curator — Ingest one source

> Not using Claude Code? Read `references/other-agents.md` in the curator skill first.

Integrate one source into the wiki. Rules: `CLAUDE.md`, `GOVERNANCE.md`, `principles/`.

## Step 0 — Find the vault

The wiki root is the current working directory and must contain `CLAUDE.md` and `GOVERNANCE.md`. The notes root is the wiki root, or its `Main/` folder when `index.md` and `log.md` are there instead (two-level layout); every other path in this skill is relative to the notes root. If either is missing, stop and tell the user to open Claude Code inside their wiki folder. Read `GOVERNANCE.md` (frontmatter, classification, index and log formats) before writing anything.

## Input

A file path (absolute, or relative to the notes root such as `raw/report.pdf`), or live text pasted or described in chat. If none is given, ask what to ingest.

## Step 1 — Classify

Using `GOVERNANCE.md` § Classification, decide open, internal or restricted by content, not folder. Restricted content: say so, ask for confirmation, and write it only into a note marked `classification: restricted`. A source that mixes tiers is split. When unsure, treat it as restricted and ask.

## Step 2 — Extract

Read `.md` and `.txt` directly. For other formats use whichever skill or tool is available to read them (PDF, Word, spreadsheet, slides). For a PDF or slides, text extraction can drop tables and diagrams that are images: also render the pages to images and look at them, and say what could not be read. If nothing can read the file, ask the user to paste the text. Live text is used as given. Never edit the source's content.

## Step 3 — Decompose

Split the source into atomic items and tag each with `kind`:

| kind | Goes to |
|---|---|
| fact / definition / how-to | the note that owns the topic, in its body |
| decision | the owning note's **Decisions** section as `D-NN` (next number); a project with a `type: decision-log` note uses that instead. A reversed decision is marked `Superseded by D-NN`, never deleted |
| status change | the owning note's **Current state** (rewrite, stamp `_As of YYYY-MM-DD_`) plus one line in **History** |
| open question or risk | the owning note's **Open questions** (remove when resolved, note it in History) |
| minor (logistics, side mentions) | the owning note's **History** |

Drop only exact duplicates and empty placeholders. Keep everything durable, ranked by importance, not discarded for being small.

## Step 4 — Match before create (DP-003)

For every item, before proposing a new note:

1. Read `index.md`.
2. Search the destination folder and its subfolders by title, aliases, H2 headings, `summary` and 3–5 key terms (`grep -ril`). List the candidate files first and leave out notes marked `classification: restricted` unless the item itself is restricted; open a restricted note's body only when the task needs it.
3. Fit test: merge into an existing note when the item would sit there as a section, an update, or the same subject at another depth, year or version. A note that shares only vocabulary but serves a different purpose is not a fit.
4. Propose a new note only when at least two durable items share a topic that fits no existing note. Name it by topic, never by date or source.
5. For every proposed new note print: `closest existing note: <path> — why not merged: <reason>` or `closest existing note: none found in <folders searched>`.

## Step 5 — Preview

Print a routing table (item → destination → new or update) and the Step 4 lines. Wait for the owner's OK before writing when any row is a new note, the source touches more than one topic area, or anything is restricted. Writes that only update existing notes may proceed without waiting.

## Step 6 — Write

- Before the first write, if the folder is a git repository, run `git status --short`. If a note you are about to change already has uncommitted edits from the owner, say so and ask: commit the owner's edits first as their own commit, or leave that note out for now. Never fold the owner's unfinished edits into your commit.
- Integrate into the owning note's text, in the voice of the note. Do not paste the source.
- Current state: overwrite and stamp `_As of YYYY-MM-DD_`; the superseded state becomes one History line. History is newest first: `- **YYYY-MM-DD** — what changed (src: <source file or title>)`.
- Conflicts: the latest-dated source wins. Record the change in History; do not adjudicate.
- Absence is not a fact: if a risk was raised and later sources are silent, write "no further record in sources as of <date>", never "resolved".
- Unconfirmed status: when you change an active note whose Current state `_As of_` date is older than the freshness limit in `GOVERNANCE.md` § Freshness and the source you are ingesting does not confirm that state, do not restate it as current and do not just bump the date. Write the Current state as:

  ```
  _Last confirmed: YYYY-MM-DD (src) · Status unclear as of YYYY-MM-DD — no record in sources since_

  | Date | What is known | Confidence |
  |---|---|---|
  | YYYY-MM-DD | fact | confirmed / planned — not confirmed |

  **Unclear:** what cannot be stated from sources.
  **To confirm:** the question to ask the owner.
  ```

  Keep `status: active`. Replace the block with a normal `_As of_` stamp once a newer source confirms the state.
- Frontmatter on every written note: all fields from `GOVERNANCE.md`, `source:` set to the file path, URL or `synthesized`, `ingested:` and `updated:` set to today.
- New notes start from `_templates/` (`note.md`, `source.md`, `decision-log.md` or `research.md`).
- Link new notes to related notes with `[[wikilinks]]`, and add backlinks in the most related existing notes.

## Step 7 — Index and log

1. Add a line to `index.md` for each **new** note, in the right folder section; for updated notes refresh the summary only if the current state changed.
2. Append to `log.md`: `## [YYYY-MM-DD] ingest | <source title>`. If a restricted note was written, add `Restricted notes updated: <category, no names>` to the same entry.

## Step 8 — Commit and file the source

If the folder is a git repository, stage every changed file by path (never `git add .` or `git add -A`) and commit locally: `wiki(curator): ingest "<title>" → <destination path>`. Never push.

Then move a source file that lives in `raw/` to `raw/processed/`. A source outside `raw/` stays where it is.

## Output

Notes updated or created, the routing table, the log entry, and anything left for the owner to decide.

## Failure modes

- Restricted content and the owner declines: stop; ingest nothing.
- Ambiguous topic or folder: ask before filing.
- Duplicate of an existing note: merge by default; ask only when the two disagree.
- One-off item that fits no note: put it in the nearest project or area note's History; do not create a note for it.
