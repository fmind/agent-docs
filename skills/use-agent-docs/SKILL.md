---
name: use-agent-docs
description: Before web-searching or fetching docs for a project topic, check `.agents/docs/INDEX.md` and the matching `DOC.md` for a navigation map of curated upstream URLs. No-op if `INDEX.md` is missing.
---

# Use Agent Docs

A project may carry a small curated doc set under `.agents/docs/`, seeded by `gather-agent-docs`. Each `DOC.md` is a **navigation map** — a structured index of upstream URLs grouped by section — plus a brief summary and key concepts. **Consult it before doing web research** for any topic that's part of the project: you'll land on the right upstream page in one hop instead of N web searches.

## Workflow

1. **Check the index.** `cat .agents/docs/INDEX.md`. If missing, this skill is a no-op — fall through to normal research. If the project has several undocumented topics worth covering, propose `gather-agent-docs`.
2. **Open the matching `DOC.md`.** Read summary + key concepts to orient. **The Documentation map is the main body** — scan its sections for the entry that matches the question. Read sibling subtopic files (e.g. `HOOKS.md`) when `DOC.md`'s "Subtopics" points at one that covers your specific question.
3. **Check the verification date.** Each file's YAML frontmatter has `last_verified`; the `[YYYY-MM-DD]` in `INDEX.md` is the oldest among that topic's files. If the date is more than ~6 months old, or the file carries a `> ⚠ Stale` banner just below the frontmatter, treat captured links as hints — confirm against upstream before acting on them, and propose `refresh-agent-docs` when the gap is large.
4. **Fetch upstream for depth.** When the question needs flag-level detail, schema fields, or a paste-ready snippet, fetch the URL directly from the map. If a Patterns section exists and covers your case, use the captured snippet (each carries an inline `_Source: …_`).
5. **Backfill on use** _(optional, on user request)_. After fetching upstream for depth, offer to add the captured detail to `DOC.md`'s Patterns section — progressive enrichment, capped at ~5 entries per topic. The user opts in.
6. **Fall back to web search** only when the index has no entry, the upstream link is dead, or the page is too stale to trust. When you find something the local map missed, offer to extend it via `gather-agent-docs` (or `refresh-agent-docs` if the entry exists but is stale).

## Failure modes

- **`INDEX.md` lists a topic but `<topic>/DOC.md` is missing** → fall through to web research; flag the broken entry for the user and propose `gather-agent-docs` to repair.
- **`DOC.md` has no frontmatter** → treat as undated. Propose `refresh-agent-docs` so the verification metadata gets reinstated.
- **A subtopic file is referenced from `DOC.md` but absent on disk** → fall through to web research and flag for repair.
- **A URL in the documentation map is dead (404, moved)** → web-search for the current equivalent, use that upstream, and propose `refresh-agent-docs` to repair the map.
- **`last_verified` is in the future** → likely a manual edit error; treat as undated and surface to the user.

## When **not** to use local docs

- Questions about the user's own code or recent changes — read the source or `git log` instead.
- Genuinely fresh information (today's release notes, a breaking change in the last week) — go to upstream directly.
- Topics the project does not actually use — local docs scope to project topics.

## Pairing

- `gather-agent-docs` — seeds and extends `.agents/docs/`; deepens with captured snippets on request.
- `refresh-agent-docs` — re-verifies the documentation map against upstream and bumps `last_verified` dates.
