# AGENTS.md

## Overview

- agent-docs ships three skills (`/gather-agent-docs`, `/use-agent-docs`, `/refresh-agent-docs`) that give a coding agent a curated, on-disk reference for the project's key topics — answer locally before web-searching.
- Skills are file-based and harness-agnostic: the same `skills/` tree works in Claude Code, Gemini CLI, and GitHub Copilot.
- End-user state lives in `.agents/docs/INDEX.md` plus one `<topic>/DOC.md` per topic and optional `<SUBTOPIC>.md` sibling files. Each page ends with `_Last verified: YYYY-MM-DD against <upstream URL>._`; `INDEX.md`'s per-topic date is the **oldest** `Last verified` among the topic's files.

## Conventions

- **Line caps.** Skill files exist to be loaded by an agent on every run; verbosity directly costs context budget. Targets:
  - `skills/*/SKILL.md` — ≤ 110 lines
  - Project-root markdown (`README.md`, `AGENTS.md`) — uncapped, but tighten when it sprawls

  When a file approaches its cap, prefer cutting motivational/explanatory prose over removing structural rules. Each rule should be stated once, in the imperative, in the file the agent loads when it needs it. The "why" lives in commit messages and PR descriptions.

- **Lint.** Run `npm run lint` to validate changes — Prettier formatting plus markdownlint-cli2. `pre-commit` and CI run the same hook.

- **Voice.** Skill bodies address the agent in the imperative ("Read X, then write Y"). Avoid second-person flavor text aimed at the human reader — that belongs in the README.

- **Capture vs. link.** The `gather-agent-docs` rules apply to this repo's own behavior: pin only what was just verified upstream, link out for exhaustive option tables and version-pinned details, skip the genuinely volatile (model IDs, pricing, preview flags), and keep pages reusable across projects.

## Workflow

- The three skills pair into a small loop on the user's project (this repo does not consume its own skills — there is no `.agents/docs/` here):
  - `/gather-agent-docs` — seed `.agents/docs/` with overviews and canonical URLs for the project's key topics
  - `/use-agent-docs` — read `.agents/docs/INDEX.md` before web-searching a known topic
  - `/refresh-agent-docs [<topic> | <topic>/<subtopic>]` — re-verify captured flags/features against upstream and bump `Last verified` dates

## Layout

- `skills/` — Agent Skills (`gather-agent-docs`, `use-agent-docs`, `refresh-agent-docs`).
- `.claude-plugin/`, `gemini-extension.json`, `plugin.json` — harness manifests for Claude Code, Gemini CLI, GitHub Copilot.
- `.github/workflows/ci.yml` — lint via pre-commit on push and PR.
