# Using the wiki skills outside Claude Code

The three skills (curator, archivist, steward) are written and tested for Claude Code. They are plain `SKILL.md` files, so other agents that load skills from a folder (for example Codex or Hermes) can run them too. This guide has not yet been tested with those agents: treat the first run as a trial and check each answer against the notes.

## Install

Pick your agent's skills folder as `DEST` (for example `~/.codex/skills` or `~/.hermes/skills`; check your agent's documentation). From the kit folder run:

```bash
DEST="$HOME/.codex/skills"
for n in curator archivist steward; do
  mkdir -p "$DEST/wiki-$n" && cp -R "plugin/skills/$n/." "$DEST/wiki-$n/" \
  && sed -i.bak "s/^name: $n\$/name: wiki-$n/" "$DEST/wiki-$n/SKILL.md" && rm "$DEST/wiki-$n/SKILL.md.bak"
done
```

The folder and the `name:` line must match, and the `wiki-` prefix keeps the names from clashing with other skills. Install all three: the curator folder holds this guide and the other two point to it. Start the agent inside the wiki folder (the skills stop at their first step if `CLAUDE.md` and `GOVERNANCE.md` are not in the current folder), then restart it so it re-reads its skill list.

## Names

In the skill text, `/wiki:curator` means "run the wiki-curator skill", `/wiki:archivist` means wiki-archivist and `/wiki:steward` means wiki-steward. Claude Code has the `/wiki:` shortcuts; elsewhere describe the task in words or use your agent's own skill command.

## Tool substitutions

| The skill says | Do this |
|---|---|
| Read a PDF | `pdftotext -layout <file>`; then render every page (`pdftoppm -r 70 -png`) and look at the images yourself, because text extraction drops tables and diagrams that are images |
| Read Word or spreadsheet files | Your file reader, or python-docx / openpyxl |
| Read slides | python-pptx for text; render slides to images when they hold diagrams |
| Run git | Through your shell tool; stage each file by path, never `git add .` or `git add -A`; commit locally and never push unless the owner asks |
| Subagents or parallel agents | Do the step yourself, one at a time |

The skills have no session hooks and no automatic sweep of `raw/`; nothing needs replacing there.

## Rules that never change

- Never edit anything in `raw/` except moving a finished file to `raw/processed/`.
- A note marked `classification: restricted` is cited by title only, never copied into an unmarked note, and left out of searches unless the task is about it.
- Approval gates stay: the steward stops at its patch list and applies nothing until the owner approves; the curator asks before any new note and before writing restricted content.
- If a note you are about to change already has the owner's uncommitted edits, say so and ask before staging it.
- `/wiki:steward` is marked manual-only for Claude Code. Other agents may ignore that flag, so start the steward only when the owner asks.
