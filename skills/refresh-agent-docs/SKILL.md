---
name: refresh-agent-docs
description: Re-verify entries in `.agents/docs/` against upstream — repair dead links in the documentation map, update drifted captured snippets, and bump `last_verified` dates. Triggers include "refresh agent docs", "update .agents/docs", "re-verify the docs".
---

# Refresh Agent Docs

Keeps `.agents/docs/` honest. Each `DOC.md` and `<SUBTOPIC>.md` carries YAML frontmatter (`last_verified`, optional `upstream_commit`, `sources`); this skill re-verifies, applies fixes, and bumps the date.

Pair with `gather-agent-docs` (seed entries, deepen on request) and `use-agent-docs` (read them).

## Scope

- **Default**: every entry in `INDEX.md` — every `*.md` under each topic's directory.
- **Topic-targeted**: when the user names slugs ("refresh gemini-cli", "refresh cloud-run and firebase"), refresh every `*.md` in those directories.
- **Subtopic-targeted**: when the user names a specific file ("refresh gemini-cli/hooks"), refresh only that file and re-bump the topic's `INDEX.md` date to the new oldest.
- No-op if `.agents/docs/INDEX.md` does not exist — propose `gather-agent-docs` instead.

## Smart refresh

Before re-fetching upstream content, try the cheap paths in order. The richer the page's frontmatter, the more refreshes short-circuit.

1. **Commit-pin shortcut.** If `upstream_commit` is set and matches upstream HEAD (e.g. via the repo URL's HEAD SHA), the page is verified by definition. Bump `last_verified`, mark `unchanged`, done.
2. **Changelog shortcut.** Fetch `sources.changelog` (or `sources.release`) and filter entries dated after `last_verified`. None matching → bump `last_verified`, mark `unchanged`, done.
3. **Link-rot scan.** HEAD-check every URL in the Documentation map. All resolve, no nav drift, no T2 snippet sources changed → bump and done.
4. **Targeted re-verify.** Some changelog entries touch surfaces in the map (or some links 404) → re-fetch the topic's nav source to detect renamed/removed/added pages, and re-fetch each affected T2 snippet's `_Source: …_` page.
5. **Full re-fetch.** No `upstream_commit`, no changelog, smart paths inconclusive → re-fetch the topic's nav source and walk every captured T2 snippet.

## Per-file workflow

For each `*.md` in scope (`DOC.md` plus any subtopic files):

1. **Read** the local page — frontmatter, summary, key concepts, Documentation map, optional Patterns.
2. **Run smart refresh** to pick the path. For dead links (404, moved), search for the current equivalent and substitute it. If no replacement exists, drop the entry and note the gap in the report.
3. **Diff and fix** when re-verifying:
   - URL in the map moved → update.
   - Page renamed in upstream nav → update title and one-line hook.
   - New page in upstream nav covering a meaningful surface → add to map.
   - Page removed upstream → drop entry.
   - Captured T2 snippet drifted → update or remove. Add a new snippet only on user request (deepen).
   - Section restructured upstream → mirror the new grouping.
4. **Bump** the frontmatter `last_verified` to today and refresh `upstream_commit` when applicable. After all of a topic's files are processed, set `INDEX.md`'s `[YYYY-MM-DD]` for that topic — and `INDEX.md`'s own `last_verified` frontmatter — to the **oldest** `last_verified` among that topic's files.
5. **Stale banner.** When a page can't be re-verified this session (upstream unreachable, login-walled, rate-limited) or its `last_verified` is more than 6 months old after this run, insert at the top of the body (just below the frontmatter): `> ⚠ Stale (verified YYYY-MM-DD). Confirm captured details upstream before acting.` Remove the banner automatically on the next successful re-verify.
6. **Record** the outcome for the report.

## Per-topic report

Summarize each topic. With subtopic files, list each `*.md` (DOC + subtopics) on its own line under the topic, with its own outcome:

- `unchanged` — verified (smart-path or full); only the date moved.
- `links repaired` — at least one URL in the map was replaced (always note the old → new swap).
- `links dropped` — a URL had no live equivalent; gap noted in the file.
- `map extended` — new upstream pages folded in.
- `map trimmed` — entries removed because upstream removed them.
- `snippet edits` — captured T2 snippet changed.
- `structural changes` — concepts, surfaces, or grouping shifted.
- `stale banner added` — could not re-verify this session; banner inserted.
- `needs human review` — change was ambiguous or upstream looked unstable.

## Ground rules

- **Verbatim only.** Same rule as `gather-agent-docs`: every captured detail must come from upstream just fetched in this session, or a command run in this session. Don't carry stale facts forward just because they were already there. Never paraphrase, infer, or auto-complete from training memory.
- **Repair dead links, don't silently rewrite.** Always surface the swap in the report so the user can sanity-check the substitute.
- **Don't expand scope sideways.** This skill maintains existing entries — adding new topics is `gather-agent-docs`'s job.
- **Don't deepen by default.** Adding new T2 snippets is `gather-agent-docs deepen` territory; this skill only updates snippets that already exist.
- **Hand off, don't commit.** Let the user review the diff.

## When _not_ to use

- The project has no `.agents/docs/INDEX.md` — use `gather-agent-docs` first.
- The user only wants a new topic added — that's `gather-agent-docs`.
- A single ad-hoc lookup against upstream — read upstream directly; don't run a full refresh for one fact.

## Pairing

- `gather-agent-docs` — seeds and extends `.agents/docs/`; deepens with captured snippets on request.
- `use-agent-docs` — reads `.agents/docs/` before web research; surfaces staleness that triggers this skill.
