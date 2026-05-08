---
name: gather-agent-docs
description: Seed `.agents/docs/` with short overviews and canonical URLs for the project's key topics (tools, services, frameworks, ...), so future agents read locally before web-searching. Triggers include "gather docs for X", "seed agent docs", "set up .agents/docs".
---

# Gather Agent Docs

Build a small, durable, agent-readable doc set for the key topics in the current project (tools, services, frameworks, ...). The goal is **stable pointers, not copies of upstream docs**: future agents answer from local files first and only hit the web for specifics.

Pair with `use-agent-docs`, which teaches agents to consult these files before doing web research.

## Output layout

Use kebab-case slugs for topic directories (`gemini-cli`, `cloud-run`, `firebase-app-hosting`):

```text
.agents/docs/
  INDEX.md            # one-line entry per topic, pointing to its DOC.md
  <topic>/
    DOC.md            # entry point: overview, subtopic links, canonical URLs
    <SUBTOPIC>.md     # optional deep dives (e.g. EXTENSIONS.md, HOOKS.md)
```

## What each `DOC.md` contains

Keep each page to one screen. Include:

1. **One-paragraph summary** — what the topic is and where it fits.
2. **Key concepts** — the primitives an agent needs (e.g. for a CLI: settings file, commands, hooks, plugins, MCP servers).
3. **Extensibility surfaces** (when relevant) — actual extension points, not a feature dump.
4. **Frequently used flags, features, and patterns** — the handful agents reach for repeatedly, so they don't need a web round-trip every time. Verify each detail against upstream at write time.
5. **Canonical docs** — a short list of high-value upstream URLs. Prefer the doc index over deep links when the upstream site is well-organized.
6. **Subtopics** (optional) — when the topic has spawned sibling subtopic files, list them with a one-line hook each: `- [EXTENSIONS.md](EXTENSIONS.md) — installing and authoring extensions.` Omit the section entirely if none exist.

End the page with `_Last verified: YYYY-MM-DD against <upstream URL>._` — this is what `use-agent-docs` and `refresh-agent-docs` key off to detect staleness.

## Capture vs. link

Capturing flags, options, and concrete examples is encouraged when they're stable and agents use them often — fewer web round-trips on the common path. Ground rules:

- **Verify before pinning.** Every captured detail must come from upstream at write time. If you're guessing, link instead of paraphrasing.
- **Link, don't mirror.** Exhaustive option tables, full API schemas, and version-pinned behavior belong upstream — point to them.
- **Skip the genuinely volatile.** Model IDs, pricing, preview flags, anything that churns within weeks — link out so the doc can't mislead.
- **Stay reusable.** Avoid project-specific framing; these pages should travel between repos.

## Optional subtopic files

For topics with clearly named subsystems (e.g. Gemini CLI's extensions, hooks, subagents, commands, MCP servers, memory), one screen of `DOC.md` can't fit the depth agents repeatedly need. Split a subsystem into a sibling file when **all** of the following hold:

- The subsystem has its own canonical upstream page(s) — distinct from the topic's overview.
- Agents reach for that depth repeatedly in this project, not just hypothetically.
- Capturing it in `DOC.md` would push past one screen.

Subtopic files live next to `DOC.md` in the same `<topic>/` directory, named in ALL-CAPS for visual parity (`EXTENSIONS.md`, `HOOKS.md`, `SUBAGENTS.md`). They follow the same shape as `DOC.md` — summary, key concepts, captured details, canonical URLs, and their own `_Last verified: YYYY-MM-DD against <upstream URL>._` footer (subtopics drift on their own clock).

Surface them from `DOC.md`'s "Subtopics" section. Don't pre-create files speculatively — split only when the need is real, and fold a subtopic back into `DOC.md` if it shrinks below its own weight.

## `INDEX.md` shape

A header, a list of one-liners, and a short "how to use" section. Example:

```markdown
# .agents/docs — topic reference index

High-level overviews of the topics covered in this project. Pointers to upstream docs, not copies.

## Topics

- [2026-05-01] [topic-a](topic-a/DOC.md): one-line hook.
- [2026-05-01] [topic-b](topic-b/DOC.md): one-line hook.

## How to use

- Read the relevant DOC.md before web-searching a known topic.
- Follow links into upstream docs for flags, schemas, or version-pinned details.
- The leading `[YYYY-MM-DD]` is the last-verified date. If it looks stale, confirm captured details against upstream before acting on them.
```

Each entry follows `- [YYYY-MM-DD] [topic](topic/DOC.md): one-line hook.` — the date is the **oldest** `Last verified` among the topic's files (`DOC.md` plus any subtopic files), so the index honestly reflects the staleness floor. Keep `INDEX.md` lean; it is the entry point — don't enumerate subtopic files here, let `DOC.md`'s "Subtopics" section handle that.

## Workflow

1. **Identify the topics.** Inspect the project to pick the 3–8 topics most worth documenting. Confirm the list with the user if it is non-obvious.
2. **Fetch upstream overviews.** For each topic, retrieve the canonical "overview" / "concepts" / "what is X" page, plus the pages backing any flags or features you intend to capture. Cite, don't copy: pin only what you've just read.
3. **Write each `DOC.md`.** Follow the template above. Keep it tight. Stamp the `Last verified: YYYY-MM-DD against <URL>` footer with today's date and the primary upstream page.
4. **Decide on subtopic files.** For each topic, check whether any subsystem clears the bar in "Optional subtopic files". If yes, fetch its canonical page(s), write the subtopic file with its own footer, and add a one-line entry to `DOC.md`'s "Subtopics" section. Skip otherwise — most topics won't need this.
5. **Write `INDEX.md`.** One entry per topic in the dated format, plus the "how to use" section.
6. **Hand off.** Do not commit — let the user review the diff first.

## When to update later

- A topic adds or renames a major extensibility surface.
- A canonical doc URL changes.
- A new topic becomes central to the project.
- A subsystem now clears the bar for its own subtopic file (split it out of `DOC.md`), or an existing subtopic file no longer earns its keep (fold it back in).
- Captured flags or features no longer match upstream — fix the entry and bump its `Last verified` date (or hand off to `refresh-agent-docs`).

Routine version bumps don't require a structural rewrite, but they should bump `Last verified` once the captured details are reconfirmed.

## Pairing

- `use-agent-docs` — teaches agents to consult these files before web research.
- `refresh-agent-docs` — re-verifies captured flags/features against upstream and bumps the `Last verified` dates.

## Notes

- The `.agents/docs/` path is a convention shared with `use-agent-docs` and `refresh-agent-docs`; keep it consistent so cross-skill discovery works.
- If `.agents/docs/INDEX.md` already exists, extend it rather than rewriting, and only add entries for topics that are missing.
