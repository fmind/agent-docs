# Agent Docs

[![CI](https://github.com/fmind/agent-docs/actions/workflows/ci.yml/badge.svg)](https://github.com/fmind/agent-docs/actions/workflows/ci.yml) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE) [![Claude Code](https://img.shields.io/badge/Claude%20Code-supported-D97706)](https://github.com/anthropics/claude-code) [![Gemini CLI](https://img.shields.io/badge/Gemini%20CLI-supported-4285F4)](https://github.com/google-gemini/gemini-cli) [![GitHub Copilot](https://img.shields.io/badge/GitHub%20Copilot-supported-181717)](https://github.com/features/copilot)

> **Project docs that answer locally before the web.**
>
> ![An AI coding agent reading from a small, neatly labeled bookshelf of project docs sitting at hand, while a closed web browser waits in the background.](./hero.jpg)

Agent Docs is a small set of Agent Skills — file-based, harness-agnostic — that give a coding agent a curated, on-disk reference for the tools, services, and frameworks the project actually uses. The same `skills/` tree runs in Claude Code, Gemini CLI, and GitHub Copilot.

## Why this exists

Agents waste a lot of turns web-searching for things the project already commits to.

- Web searches are slow, lossy, and often grab the wrong version of a doc.
- The "right" answer for a given repo is narrower than upstream — flags actually used, patterns this project follows, the canonical URL the team trusts.
- Without curation, two agents researching the same topic on the same repo come back with two different answers.

Agent Docs flips the default: the project ships a tiny, dated reference set under `.agents/docs/`, and agents read it **before** reaching for the web.

## What you get

- **Local-first lookups.** `use-agent-docs` teaches the agent to consult `.agents/docs/INDEX.md` before web research — fewer round-trips, more reproducible answers.
- **Curated, not copied.** Each `DOC.md` is one screen: summary, key concepts, frequently used flags, canonical upstream URLs. Exhaustive option tables stay upstream.
- **Honest staleness.** Every page ends with `_Last verified: YYYY-MM-DD against <upstream URL>._` and `INDEX.md` propagates the oldest date per topic — agents see the staleness floor at a glance.
- **Re-verifiable.** `refresh-agent-docs` re-fetches upstream, fixes drift, repairs dead links, and bumps the dates — keeping the set honest over time.
- **Subtopic split when warranted.** Large surfaces (e.g. a CLI's hooks, extensions, MCP servers) get sibling files like `HOOKS.md`, `EXTENSIONS.md` — each with its own footer and refresh clock.
- **Install once, use anywhere.** The same `skills/` tree drives Claude Code, Gemini CLI, and GitHub Copilot.

## The three skills

- **`/gather-agent-docs`** — Seed `.agents/docs/` with short overviews and canonical URLs for the project's key topics (tools, services, frameworks). Writes `INDEX.md` and one `DOC.md` per topic.
- **`/use-agent-docs`** — Before web-searching for a project topic, check `.agents/docs/INDEX.md` and read the matching `DOC.md`. No-op when the file is missing.
- **`/refresh-agent-docs`** — Re-verify entries against upstream: update drifted flags, repair dead links, bump `Last verified` dates. Default scope is every topic; can be targeted at one topic or one subtopic file.

The three pair into a small loop:

```text
gather → use → (drift detected) → refresh → use → ...
```

## Walkthrough

```text
$ /gather-agent-docs
  → inspects the project, picks 3–8 topics worth documenting
  → fetches each topic's canonical "overview" page (and any pages backing captured flags)
  → writes .agents/docs/<topic>/DOC.md with summary, key concepts, flags, canonical URLs, footer
  → writes .agents/docs/INDEX.md with one dated line per topic
  → "Seeded 5 topics under .agents/docs/. Review the diff before committing."

$ /use-agent-docs       # invoked implicitly whenever the agent is about to web-search
  → reads INDEX.md, opens the matching DOC.md, follows canonical URLs for specifics
  → falls back to web search only if the topic is missing or the upstream link is dead

$ /refresh-agent-docs gemini-cli
  → re-fetches every canonical URL under .agents/docs/gemini-cli/
  → diffs captured flags, fixes drift, repairs dead links, bumps Last verified
  → "gemini-cli: minor edits (DOC.md), link repaired (HOOKS.md). INDEX.md date set to 2026-05-08."
```

## Install

### Claude Code

```text
/plugin marketplace add fmind/agent-docs
/plugin install agent-docs@agent-docs
```

For local development, point the marketplace at a clone:

```text
/plugin marketplace add /path/to/agent-docs
/plugin install agent-docs@agent-docs
```

### Gemini CLI

```bash
gemini extensions install fmind/agent-docs
```

For local development (live-link, edits reload on next session):

```bash
gemini extensions link /path/to/agent-docs
```

### GitHub Copilot

Copilot CLI:

```bash
copilot plugin marketplace add fmind/agent-docs
copilot plugin install agent-docs@agent-docs
```

For local development, point the marketplace at a clone:

```bash
copilot plugin marketplace add /path/to/agent-docs
copilot plugin install agent-docs@agent-docs
```

VS Code — point `chat.pluginLocations` at a local clone:

```jsonc
// settings.json
"chat.pluginLocations": {
  "/path/to/agent-docs": true
}
```

## Reference

### Layout

```text
agent-docs/
├── AGENTS.md                          # canonical context — read by Copilot; @-included by CLAUDE.md / GEMINI.md
├── skills/                            # Agent Skills (open standard)
│   ├── gather-agent-docs/SKILL.md     # user-facing: seed .agents/docs/
│   ├── use-agent-docs/SKILL.md        # user-facing: read .agents/docs/ before web research
│   └── refresh-agent-docs/SKILL.md    # user-facing: re-verify entries against upstream
├── .claude-plugin/                    # Claude Code plugin manifest + bundled marketplace
├── gemini-extension.json              # Gemini CLI extension manifest
├── plugin.json                        # GitHub Copilot manifest
└── .github/workflows/ci.yml           # lint via pre-commit
```

In an end-user project after `/gather-agent-docs`:

```text
.agents/docs/
├── INDEX.md                           # one dated entry per topic, pointing to its DOC.md
└── <topic>/                           # kebab-case slug (e.g. gemini-cli, cloud-run)
    ├── DOC.md                         # entry point: summary, key concepts, flags, canonical URLs, footer
    └── <SUBTOPIC>.md                  # optional deep dive (e.g. EXTENSIONS.md, HOOKS.md)
```

`INDEX.md`'s per-topic date is the **oldest** `Last verified` among the topic's files (`DOC.md` plus any subtopic files), so the index honestly reflects the staleness floor.

### When to split a subtopic

`gather-agent-docs` keeps each `DOC.md` to one screen. Split a subsystem into a sibling `<SUBTOPIC>.md` when **all** of the following hold:

- The subsystem has its own canonical upstream page(s) — distinct from the topic's overview.
- Agents reach for that depth repeatedly in this project, not just hypothetically.
- Capturing it in `DOC.md` would push past one screen.

Fold a subtopic back into `DOC.md` if it shrinks below its own weight.

## Contributing

Issues and PRs welcome. Two house rules to keep the skills lean:

- **Skill files have line caps** (see `AGENTS.md` §Conventions) — they load into agent context on every run, so verbosity costs budget.
- **Voice is imperative.** Skill bodies address the agent ("Read X, then write Y."), not a human reader — that prose belongs in this README.

## License

MIT — see [LICENSE](./LICENSE).
