# Examples

A worked example of what `/gather-agent-docs` produces in an end-user project. The path layout matches what the skill writes into your own repo (`.agents/docs/`).

## `docs/` — two topics, one with a deepened subtopic

A small reference set for a fictional project that uses Gemini CLI and Cloud Run. The shape demonstrates the conventions:

- `INDEX.md` — one dated entry per topic, plus a brief "How to use" section. The `[YYYY-MM-DD]` prefix is the **oldest** `last_verified` among that topic's files, so the index honestly reflects the staleness floor.
- `gemini-cli/DOC.md` — entry point in the **default shape**: frontmatter, one-paragraph summary, key concepts, a structured **Documentation map** (link index of upstream pages, grouped by upstream nav), and a "Subtopics" section. No captured snippets.
- `gemini-cli/HOOKS.md` — a subtopic split demonstrating the **deepened shape**: link map plus a small Patterns section with paste-ready snippets, each tied to an inline `_Source: …_`. Justified because hooks have their own canonical upstream page, get reached for repeatedly, and the deepened content would push `DOC.md` past the soft cap.
- `cloud-run/DOC.md` — a topic that does **not** warrant a subtopic split: the documentation map fits comfortably within the soft cap, no patterns captured by default.

## Caveat — illustrative, not curated

These pages exist to demonstrate the layout, not to ship truth. The map structure and URLs are correct in spirit but not actively re-verified on a schedule — copying them into a real project without re-running `/gather-agent-docs` (or `/refresh-agent-docs` afterwards) would mislead future agents about the staleness floor. Use the structure, regenerate the content.
