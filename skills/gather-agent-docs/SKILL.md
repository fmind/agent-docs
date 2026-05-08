---
name: gather-agent-docs
description: Seed `.agents/docs/` with a navigation map of the project's key topics (tools, services, frameworks, ...) — a structured link index to upstream pages, plus a brief summary and key concepts. Future agents read locally to find the right upstream URL in one hop. Triggers include "gather docs for X", "seed agent docs", "set up .agents/docs", "deepen agent docs".
---

# Gather Agent Docs

Build a small, durable, agent-readable doc set for the project's key topics. **Navigation map first, captured detail on request** — future agents read the local map to land on the right upstream URL in one hop, then fetch upstream when they need depth.

## Output layout

Use kebab-case slugs for topic directories (`gemini-cli`, `cloud-run`, `firebase-app-hosting`):

```text
.agents/docs/
  INDEX.md            # one-line entry per topic, pointing to its DOC.md
  <topic>/
    DOC.md            # entry point: summary, key concepts, documentation map, optional patterns
    <SUBTOPIC>.md     # optional deep dive (rare — see below)
```

## Frontmatter

`DOC.md` and every `<SUBTOPIC>.md` open with YAML frontmatter the other skills key off:

```yaml
---
last_verified: 2026-05-08 # required, ISO date
upstream_commit: <sha> # optional; commit at verification — enables smart refresh
sources: # required, at least one URL; richer set = more refresh short-circuits
  docs: https://... # main documentation page
  repo: https://github.com/... # source repository
  changelog: https://... # changelog / release-notes — enables smart refresh
  release: https://github.com/.../releases
---
```

`INDEX.md` carries `last_verified` only — set to the **oldest** date among topic files.

## What each `DOC.md` contains

1. **Summary** — one paragraph: what the topic is and where it fits.
2. **Key concepts** — the primitives an agent needs, one terse line each. No config snippets, no flag tables.
3. **Documentation map** — structured list of upstream pages, grouped by upstream nav section. Each entry: `[Page title](url) — one-line hook on what's there`.
4. **Patterns** _(optional, deepen-only)_ — paste-ready snippets for surfaces agents reach for repeatedly. Capped at ~5. Each block ends with an inline `_Source: …_` link to the upstream anchor that backs it.
5. **Subtopics** (optional) — list sibling files with a one-line hook each.

Soft cap: ~100 lines for `DOC.md`, ~150 for a subtopic deep-dive.

## Capture rules

- **T1 — link map.** Page title + URL + one-line hook. Default tier for every entry in the upstream nav.
- **T2 — snippet.** Small, paste-ready block tied to a single upstream anchor. Captured **only when deepening**, capped at ~5 per topic, each with an inline `_Source: …_`.
- **T3 — volatile.** Model IDs, pricing, preview/beta flags, version numbers, deprecation timers. Linked only, never captured.
- **Verbatim only.** Captured details must come from upstream just fetched in this session, or a command run in this session. Never paraphrase, infer, or auto-complete from training memory.
- **Stay reusable.** Avoid project-specific framing; pages should travel between repos.

## Topic discovery

Pick topics from what the project actually declares as a dependency, not what it might plausibly use:

- Package manager manifests (`package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, ...).
- CI / build configs (`.github/workflows/`, `cloudbuild.yaml`, ...).
- Infrastructure / runtime manifests (`Dockerfile`, `terraform/*.tf`, `firebase.json`, `apphosting.yaml`, ...).
- Tool-version pins (`mise.toml`, `.tool-versions`, `.nvmrc`, ...).
- _Skip_: the project's own code, generic standards (Markdown, JSON), anything not wired into the build/runtime. Confirm the shortlist with the user when non-obvious.

## Building the documentation map

For each topic, fetch **one** upstream nav source — try in order until one resolves:

1. Docs sidebar / table-of-contents page (e.g. a `/docs/` index, a `_sidebar` file).
2. The repo's `README.md` "Documentation" section, or the `docs/` folder structure on the default branch.
3. The docs home page when it links to every section.
4. `sitemap.xml` as a last resort (raw, needs grouping).

Extract section headings, page titles, and URLs; preserve the upstream grouping so the map mirrors how upstream organises itself. If a heading has no children, list the section as a single entry.

## Optional subtopic files

Split a subsystem into a sibling file (`HOOKS.md`, `EXTENSIONS.md`) only when **all** of:

- It has its own canonical upstream page distinct from the topic's overview.
- The deepened version (with captured snippets) would push past the soft cap.
- Agents reach for that depth repeatedly in this project, not just hypothetically.

The link map alone rarely justifies a split. Subtopic files share `DOC.md`'s shape and drift on their own clock; surface them from `DOC.md`'s "Subtopics" section. Fold back into `DOC.md` if a subtopic shrinks below its own weight.

## `INDEX.md` shape

`INDEX.md` carries `last_verified` frontmatter (the **oldest** date among all topic files), a brief intro line, then one entry per topic: `- [YYYY-MM-DD] [topic-slug](topic-slug/DOC.md): one-line hook.`. The leading date is the oldest `last_verified` among that topic's files. Don't enumerate subtopic files; `DOC.md`'s Subtopics section handles that. If `INDEX.md` already exists, extend it; only add entries for missing topics.

## Workflow

**Default** (no args, or "gather docs"):

1. **Identify topics** using the discovery signals; confirm shortlist when non-obvious.
2. **Build each link map.** Fetch one nav source per topic; extract title + URL + one-line hook per page, preserving upstream grouping.
3. **Write each `DOC.md`** — frontmatter, summary, key concepts, documentation map. No Patterns section.
4. **Write `INDEX.md`** — frontmatter + dated topic entries.
5. **Hand off** — don't commit; let the user review.

**Deepen** (args like "deepen gemini-cli", "expand all"): for each topic in scope, capture up to ~5 paste-ready snippets (T2) — fetch the linked upstream page, copy a small block, add an inline `_Source: …_`. Add or extend a Patterns section. Bump `last_verified`. Hand off.

## Pairing

- `use-agent-docs` — reads these files before web research; can backfill T2 snippets when fetching upstream for depth.
- `refresh-agent-docs` — re-verifies against upstream; smart path uses `upstream_commit` / `sources.changelog` for cheap no-op, falls through to a link-rot scan.
