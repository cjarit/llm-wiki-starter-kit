# LLM Wiki Starter Kit

A starter for a personal knowledge wiki that Claude maintains and you read in Obsidian. You drop sources into `raw/`; Claude merges them into linked markdown notes; you ask questions and run health checks. Based on the "LLM Wiki" idea (https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

## What you need

- Claude Code (the `claude` command works in a terminal)
- Obsidian (free)
- git, with your name and email set (`git config --global user.name` and `user.email`), because the wiki commits every change
- macOS or Linux (the commands below use `cp` and `~`); on Windows use WSL
- Claude Code signed in (run `claude` once and complete login)

## What is in the kit

- `vault/` — an empty wiki: schema (`CLAUDE.md`), rules (`GOVERNANCE.md`), `index.md`, `log.md`, folders, templates, one sample source.
- `plugin/` — the `wiki` plugin with three skills: `/wiki:curator` (ingest), `/wiki:archivist` (query), `/wiki:steward` (lint).

## Install

Get the kit first: download the release zip and unzip it, or clone the repository. Then pick a place for your wiki, for example `~/wiki`, and run these in a terminal from the kit folder (the one that contains this README, `vault/` and `plugin/`).

1. Copy the vault and start a git history:

```bash
cp -R vault ~/wiki && cd ~/wiki && git init && git add -A && git commit -m "init wiki"
```

2. Install the plugin by copying it into your Claude Code skills folder:

```bash
mkdir -p ~/.claude/skills/wiki && cp -R plugin/. ~/.claude/skills/wiki/
```

3. In Obsidian choose "Open folder as vault" and select `~/wiki`.

4. Open a new Claude Code session inside the vault folder. Skills only work when Claude Code is opened in the wiki folder; they read `CLAUDE.md` there to know the rules.

```bash
cd ~/wiki && claude
```

5. Type `/wiki:` and check that `curator`, `archivist` and `steward` are listed. If they are not, quit Claude Code and start it again.

The plugin is installed for every Claude Code session, not only this wiki. Outside a wiki folder the skills stop at their first step and tell you to open Claude Code inside the wiki.

## First run

The vault includes `raw/sample-source.md` so you can try all three operations.

1. **Ingest.** Run:

```
/wiki:curator raw/sample-source.md
```

Claude prints a routing table showing which items go where and proposes a new note, because the wiki is empty. Answer yes. Then open `index.md` and the new note in Obsidian: the note has a Current state, a decision, an open question, and `source:` / `ingested:` set. `log.md` has one new line.

2. **Query.** Run:

```
/wiki:archivist How often should a starter kept in the fridge be fed?
```

The answer cites the note. Ask something the sample does not cover and it should say "not found" rather than guess.

3. **Lint.** Run:

```
/wiki:steward
```

Claude prints a patch list and stops. Nothing is changed until you approve.

When you are done, delete the sample note and its log line, or keep them as an example.

## Daily use

- Put a file in `raw/`, then run `/wiki:curator raw/<file>`; or run `/wiki:curator` with text pasted in the chat.
- Ask `/wiki:archivist <question>` whenever you want an answer from your own notes.
- Run `/wiki:steward` now and then, for example monthly. It also flags notes whose Current state no source has confirmed for 60 days.
- If you edit notes yourself, commit your edits (or let Obsidian Git do it) before asking Claude to change the same notes, so Claude's commits stay separate from yours.
- Claude never edits what is in `raw/`; it only moves finished files to `raw/processed/`.

## Make it yours

- Folders: `projects/`, `areas/`, `reference/` are defaults. Rename or add folders, then update `CLAUDE.md` and the `domain` list in `GOVERNANCE.md` together.
- Structure: single level by default. `CLAUDE.md` § Structure choice explains the two-level alternative; ask Claude to convert if you want it.
- Freshness: the 60-day limit for a Current state is in `GOVERNANCE.md` § Freshness; change it there.
- Sensitive notes: mark a note `classification: restricted` and read `GOVERNANCE.md` § Classification. Never keep passwords in the wiki.
- Optional Obsidian plugins: Dataview (query your frontmatter), Obsidian Git (automatic commits), and the Obsidian Web Clipper browser extension (clip web pages into `raw/`). None are needed for the first run.
- Do not push the wiki to a public repository without checking what it holds.
