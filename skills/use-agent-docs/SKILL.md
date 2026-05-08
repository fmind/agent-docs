---
name: use-agent-docs
description: Before web-searching or fetching docs for a project topic, check `.agents/docs/INDEX.md` for curated local overviews and canonical URLs. No-op if that file is missing.
---

# Use Agent Docs

A project may carry a small curated doc set under `.agents/docs/`, seeded by `gather-agent-docs`. **Consult it before doing web research** for any topic that's part of the project — it's faster, curated, offline-friendly, and the upstream URLs there are chosen to outlive flag-level details.

## Workflow

1. **Check the index.** `cat .agents/docs/INDEX.md`. If missing, this skill is a no-op — fall through to normal research. If the project has several undocumented topics worth covering, propose `gather-agent-docs`.
2. **Open the matching `DOC.md`.** If an entry matches the topic, read `.agents/docs/<topic>/DOC.md`. The "Key concepts", "Frequently used flags/features", and "Canonical docs" sections often answer the question outright. If `DOC.md` has a "Subtopics" section pointing to sibling files (e.g. `EXTENSIONS.md`, `HOOKS.md`) and one of them covers your specific question, read that file too — each subtopic file carries its own captured details and `Last verified` footer.
3. **Check the verification date.** `INDEX.md` carries `[YYYY-MM-DD]` per entry (the oldest among that topic's files), and every `DOC.md` and subtopic file ends with its own `Last verified` footer. If the date is more than ~6 months old, or the page mentions versions clearly behind current, treat captured flags and examples as hints — confirm against the upstream link before acting on them, and propose `refresh-agent-docs` when the gap is large.
4. **Follow links for specifics.** For exhaustive option tables, schema fields, or version-pinned behavior, follow the canonical URLs from `DOC.md` instead of running a fresh web search.
5. **Fall back to web search** only when the index has no entry or the upstream link is dead. When you find something the local docs missed, offer to extend them via `gather-agent-docs` (or `refresh-agent-docs` if the entry exists but is stale).

## When **not** to use local docs

- Questions about the user's own code or recent changes — read the source or `git log` instead.
- Genuinely fresh information (today's release notes, a breaking change in the last week) — go to upstream directly.
- Topics the project does not actually use — local docs scope to project topics.

## Pairing

- `gather-agent-docs` — seeds and extends `.agents/docs/`.
- `refresh-agent-docs` — re-verifies aged entries against upstream and bumps `Last verified` dates.
