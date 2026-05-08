# AGENTS.md

## Overview

- agent-docs ships three skills (`/gather-agent-docs`, `/use-agent-docs`, `/refresh-agent-docs`) that give a coding agent a curated, on-disk reference for the project's key topics — answer locally before web-searching.
- Skills are file-based and harness-agnostic: the same `skills/` tree works in Claude Code, Gemini CLI, and GitHub Copilot.
- End-user state lives in `.agents/docs/INDEX.md` plus one `<topic>/DOC.md` per topic and optional `<SUBTOPIC>.md` sibling files. Each page opens with YAML frontmatter (`last_verified`, optional `upstream_commit`, `sources.{docs,repo,changelog,release}`); `INDEX.md`'s per-topic date is the **oldest** `last_verified` among the topic's files.

## Conventions

- **Line caps.** Skill files exist to be loaded by an agent on every run; verbosity directly costs context budget. Targets:
  - `skills/*/SKILL.md` — ≤ 110 lines
  - Project-root markdown (`README.md`, `AGENTS.md`) — uncapped, but tighten when it sprawls

  When a file approaches its cap, prefer cutting motivational/explanatory prose over removing structural rules. Each rule should be stated once, in the imperative, in the file the agent loads when it needs it. The "why" lives in commit messages and PR descriptions.

- **Lint.** Run `npm run lint` to validate changes — Prettier formatting plus markdownlint-cli2. `pre-commit` and CI run the same hook.

- **Voice.** Skill bodies address the agent in the imperative ("Read X, then write Y"). Avoid second-person flavor text aimed at the human reader — that belongs in the README.

- **Capture vs. link.** The `gather-agent-docs` rules apply to this repo's own behavior: build the documentation map by default (T1: page title + URL + one-line hook); capture paste-ready snippets only on request (T2: capped at ~5 per topic, each with a per-block source link); link out (never capture) for volatile values like model IDs and pricing (T3); copy verbatim from upstream just fetched in this session (never paraphrase, infer, or auto-complete); keep pages reusable across projects.

## Workflow

- The three skills pair into a small loop on the user's project (this repo does not consume its own skills — there is no `.agents/docs/` here):
  - `/gather-agent-docs` — seed `.agents/docs/` with a documentation map (link index) for the project's key topics; deepen a topic with paste-ready snippets on request (e.g. "deepen gemini-cli")
  - `/use-agent-docs` — read `.agents/docs/INDEX.md` and the matching `DOC.md`'s documentation map before web-searching a known topic; optionally backfill snippets on use
  - `/refresh-agent-docs` — re-verify the documentation map (link rot) and any captured snippets against upstream; bump `last_verified` dates; defaults to every topic, accepts a topic slug or `topic/subtopic` as a natural-language argument; short-circuits via `upstream_commit` / `sources.changelog` when upstream hasn't moved

## Layout

- `skills/` — Agent Skills (`gather-agent-docs`, `use-agent-docs`, `refresh-agent-docs`).
- `examples/` — worked sample of what `/gather-agent-docs` produces (two topics, one with a deepened subtopic split). Illustrative, not curated.
- `.claude-plugin/`, `gemini-extension.json`, `plugin.json` — harness manifests for Claude Code, Gemini CLI, GitHub Copilot.
- `CLAUDE.md`, `GEMINI.md` — single-line `@AGENTS.md` includes so Claude Code and Gemini CLI pick up the canonical context.
- `package.json`, `.markdownlint-cli2.jsonc`, `.prettierrc.json`, `.prettierignore` — `npm run lint` (Prettier + markdownlint-cli2) plus shared formatter config.
- `.pre-commit-config.yaml`, `.github/workflows/ci.yml` — pre-commit hooks (large files, merge conflicts, private keys, lint) run locally and in CI on push and PR.
