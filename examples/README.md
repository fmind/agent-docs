# Examples

A worked example of what `/gather-agent-docs` produces in an end-user project. The path layout matches what the skill writes into your own repo (`.agents/docs/`).

## `docs/` — two topics, one with a subtopic split

A small reference set for a fictional project that uses Gemini CLI and Cloud Run. The shape demonstrates the conventions:

- `INDEX.md` — one dated entry per topic, plus the standard "How to use" section. The `[YYYY-MM-DD]` prefix is the **oldest** `Last verified` among that topic's files, so the index honestly reflects the staleness floor.
- `gemini-cli/DOC.md` — entry point for a topic: one-paragraph summary, key concepts, frequently used flags, canonical URLs, and a "Subtopics" section pointing at sibling files. Footer dates the page against its primary upstream URL.
- `gemini-cli/HOOKS.md` — a subtopic split: the hooks subsystem has its own canonical page and is reached for repeatedly enough to deserve its own file. Same shape as `DOC.md`, with its own footer (subtopics drift on their own clock).
- `cloud-run/DOC.md` — a topic that does **not** warrant a subtopic split: the captured surface fits one screen.

## Caveat — illustrative, not curated

These pages exist to demonstrate the layout, not to ship truth. The captured flags, concepts, and URLs are correct in spirit but not actively re-verified on a schedule — copying them into a real project without re-running `/gather-agent-docs` (or `/refresh-agent-docs` afterwards) would mislead future agents about the staleness floor. Use the structure, regenerate the content.
