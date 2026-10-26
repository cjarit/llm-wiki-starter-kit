---
name: archivist
description: >
  Answer a question from the LLM Wiki in the current folder with citations, reading index.md first
  and then only the relevant notes. Use when the user asks what the wiki or knowledge base says,
  why something was decided, what the current state of something is, or whether the wiki covers a
  topic. Read-only unless the user approves a write-back. Not for adding content (curator).
argument-hint: "<question>"
---
# /wiki:archivist — Query the wiki

Answer a question from the wiki, index first, with citations.

## Step 0 — Find the vault

The wiki root is the current working directory and must contain `CLAUDE.md` and `GOVERNANCE.md`. The notes root is the wiki root, or its `Main/` folder when `index.md` and `log.md` are there instead (two-level layout); every other path in this skill is relative to the notes root. If either is missing, stop and tell the user to open Claude Code inside their wiki folder.

## Step 1 — Read the index

If no question was given, ask for one. Read `index.md` to find the relevant notes. Do not scan the whole vault. If the wiki has many notes, `grep -ril` on key terms narrows the list. List candidate files first and leave out notes marked `classification: restricted` unless the question is about them.

## Step 2 — Read targeted notes

Read only the notes that match. Read a note's **Current state** and **Open questions** before its History. Read a principle (`DP-NNN`) when the question is about a rule.

## Step 3 — Answer

Give a direct answer with a citation after each claim: `([[areas/sourdough-baking]])`. Keep the answer shorter than the notes it draws on.

- State the `_As of_` date of anything time-sensitive. If a Current state is older than the limit in `GOVERNANCE.md` § Freshness, or is written as an Unconfirmed status block, say that the note has no record since its date.
- If notes disagree, say so and cite both; do not pick silently.
- If the wiki does not cover the question, say "not found" and list the folders and terms searched. Never answer from general knowledge as if it were in the wiki; if you add outside knowledge, label it as such.
- A `classification: restricted` note is cited by title only; its details are not repeated unless the user asked about it directly.

## Step 4 — Offer a write-back

If the answer is a useful synthesis (a comparison, an analysis, a connection between notes), offer to file it. Only on the user's yes, hand it to `/wiki:curator` as live text so the same match-before-create rules apply. Append `## [YYYY-MM-DD] query | <question>` to `log.md` only when a write-back happened.

## Constraints

Read-only. Never edit a note, `index.md` or `log.md` without the user's approval.
