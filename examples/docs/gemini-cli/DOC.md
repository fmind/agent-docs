# Gemini CLI

Google's open-source command-line agent for AI-assisted coding. Reads project context from `GEMINI.md` (or `AGENTS.md`), exposes user-defined slash commands, and integrates with MCP servers, lifecycle hooks, subagents, and extensions. Configured per-project via `.gemini/settings.json`, with a global override at `~/.gemini/settings.json`.

## Key concepts

- **Settings file** — `.gemini/settings.json` (workspace) and `~/.gemini/settings.json` (global). Schema covers model, MCP servers, tools, telemetry, trust, and hooks.
- **Context files** — `GEMINI.md` is the canonical project context, loaded on session start; falls back to `AGENTS.md`. `@<file.md>` imports are supported for modular context.
- **Slash commands** — TOML files under `.gemini/commands/` (or global). Body is a prompt with `{{args}}`, `!{shell}`, `@{file}` injections.
- **Subagents** — Markdown files under `.gemini/agents/` with frontmatter (`name`, `description`, `kind`, `tools`, `mcp_servers`).
- **MCP servers** — declared in the `mcpServers` block of `settings.json`.
- **Hooks** — JSON in `settings.json.hooks`; lifecycle events with stdin/stdout JSON contract and exit-code semantics.
- **Extensions** — bundles of MCP servers, slash commands, agents, and skills installed via `gemini extensions install <repo>`.
- **Memory** — multi-tier system spanning `MEMORY.md`, `experimental.memoryV2`, modular `@file.md` imports, and `experimental.autoMemory`.

## Frequently used flags

- `gemini` — start an interactive session.
- `gemini -p "<prompt>"` — headless / scripted mode (one-shot or JSONL).
- `gemini extensions install <repo>` — install an extension.
- `gemini extensions link <path>` — live-link a local extension during development.
- `/init` — generate a `GEMINI.md` from the codebase.
- `/memory` — manage memory entries.

## Canonical docs

- <https://github.com/google-gemini/gemini-cli>
- <https://google-gemini.github.io/gemini-cli/>

## Subtopics

- [HOOKS.md](HOOKS.md) — lifecycle hooks: events, JSON contract, exit codes, common patterns.

_Last verified: 2026-05-08 against <https://github.com/google-gemini/gemini-cli>._
