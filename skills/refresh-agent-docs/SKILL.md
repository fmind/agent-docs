---
name: refresh-agent-docs
description: Re-verify entries in `.agents/docs/` against upstream — update drifted flags/features, repair dead links, and bump `Last verified` dates. Triggers include "refresh agent docs", "update .agents/docs", "re-verify the docs".
---

# Refresh Agent Docs

Keeps `.agents/docs/` honest. Each entry carries a `Last verified: YYYY-MM-DD` footer and a matching `[YYYY-MM-DD]` prefix in `INDEX.md`; this skill re-fetches upstream, diffs captured details against current reality, applies fixes, and bumps the dates.

Pair with `gather-agent-docs` (seed new entries) and `use-agent-docs` (read them).

## Scope

- **Default: refresh every entry** in `INDEX.md` — every `*.md` file under each topic's directory.
- **Topic-targeted**: if the user names one or more slugs ("refresh gemini-cli", "refresh cloud-run and firebase"), refresh every `*.md` in those topic directories.
- **Subtopic-targeted**: if the user names a specific subtopic file ("refresh gemini-cli/extensions", "refresh just the hooks page"), refresh only that file and re-bump `INDEX.md`'s topic-level date to the new oldest.
- No-op if `.agents/docs/INDEX.md` does not exist — propose `gather-agent-docs` instead.

## Workflow

For each entry in scope, walk every `*.md` file in `.agents/docs/<topic>/` — `DOC.md` plus any sibling subtopic files (e.g. `EXTENSIONS.md`, `HOOKS.md`). Each carries its own `Last verified` footer and is refreshed independently:

1. **Read the local page.** Open the file. Note its canonical URLs and the captured flags / features / patterns.
2. **Fetch upstream.** Pull each canonical URL. For dead links (404, moved, replaced), search for the current equivalent and substitute it — being up to date is the point. If no replacement exists, drop the link and note the gap in your report.
3. **Diff and fix.** Compare upstream against the local page:
    - Captured flag/feature changed → update.
    - New flag/feature now in the "frequently used" tier → add it.
    - Captured detail no longer exists upstream → remove.
    - Concept renamed or restructured → rewrite the affected section.
   Keep the page tight; don't let it grow into a mirror of upstream.
4. **Bump the date.** Rewrite the file's footer to `_Last verified: YYYY-MM-DD against <upstream URL>._` with today's date. After all of a topic's files are processed, set `INDEX.md`'s `[YYYY-MM-DD]` for that topic to the **oldest** `Last verified` among them — that's the staleness floor agents care about.
5. **Record the outcome** for the final report.

## Per-topic report

After processing, summarize each topic. If a topic has subtopic files, list each `*.md` (DOC + subtopics) on its own line under the topic, with its own outcome:

- `unchanged` — verified, only the date moved.
- `minor edits` — small flag/feature corrections.
- `structural changes` — concepts, surfaces, or layout shifted.
- `link repaired` — at least one canonical URL replaced (always note the old → new swap).
- `link dropped` — a URL had no live equivalent; gap noted in the file.
- `needs human review` — change was ambiguous or upstream looked unstable; flag for the user.

## Ground rules

- **Verify before pinning.** Same rule as `gather-agent-docs`: every captured detail must come from upstream you just read. Don't carry stale facts forward just because they were already there.
- **Repair dead links, don't silently rewrite them.** Always surface the swap in the report so the user can sanity-check the substitute is the right page.
- **Don't expand scope sideways.** This skill maintains existing entries — adding new topics is `gather-agent-docs`'s job.
- **Hand off, don't commit.** Let the user review the diff.

## When *not* to use

- The project has no `.agents/docs/INDEX.md` — use `gather-agent-docs` first.
- The user only wants a new topic added — that's `gather-agent-docs`.
- A single ad-hoc lookup against upstream — just read upstream directly; don't run a full refresh for one fact.

## Pairing

- `gather-agent-docs` — seeds and extends `.agents/docs/`.
- `use-agent-docs` — reads `.agents/docs/` before web research; surfaces staleness that triggers this skill.
