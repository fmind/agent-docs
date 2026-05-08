---
last_verified: 2026-05-08
sources:
  docs: https://google-gemini.github.io/gemini-cli/
  repo: https://github.com/google-gemini/gemini-cli
  changelog: https://github.com/google-gemini/gemini-cli/releases
---

# Gemini CLI

Google's open-source command-line agent for AI-assisted coding. Reads project context from `GEMINI.md` (or `AGENTS.md`), exposes user-defined slash commands, and integrates with MCP servers, lifecycle hooks, subagents, skills, and extensions. Configured per-project via `.gemini/settings.json`, with a global override at `~/.gemini/settings.json`.

## Key concepts

- **Settings file** — `.gemini/settings.json` (workspace) or `~/.gemini/settings.json` (global).
- **Context files** — `GEMINI.md` (preferred) or `AGENTS.md`, loaded on session start; supports `@<file>` imports.
- **Slash commands** — TOML files under `.gemini/commands/` (or global), with `{{args}}` / `!{shell}` / `@{file}` injections.
- **Subagents** — Markdown personas under `.gemini/agents/` with frontmatter.
- **MCP servers** — declared in `settings.json.mcpServers`.
- **Hooks** — declared in `settings.json.hooks`; lifecycle events with stdin/stdout JSON contract.
- **Skills** — Agent Skills (file-based, harness-agnostic) discoverable under `.agents/skills/` or `.gemini/skills/`.
- **Extensions** — bundles of MCP servers, slash commands, agents, and skills installed via `gemini extensions install`.
- **Memory** — multi-tier system spanning `MEMORY.md`, `experimental.memoryV2`, modular `@file.md` imports, and `experimental.autoMemory`.

## Documentation map

### Getting started

- [Installation](https://google-gemini.github.io/gemini-cli/docs/get-started/installation.html).
- [Quickstart](https://google-gemini.github.io/gemini-cli/docs/get-started/quickstart.html).
- [Authentication](https://google-gemini.github.io/gemini-cli/docs/get-started/authentication.html).

### Invocation & sessions

- [Interactive vs headless mode](https://google-gemini.github.io/gemini-cli/docs/cli/index.html).
- [`-p` (prompt) and JSONL output](https://google-gemini.github.io/gemini-cli/docs/cli/headless.html).
- [Sessions & history](https://google-gemini.github.io/gemini-cli/docs/cli/sessions.html).
- [Worktrees](https://google-gemini.github.io/gemini-cli/docs/cli/worktrees.html).

### Configuration

- [`settings.json` reference](https://google-gemini.github.io/gemini-cli/docs/cli/configuration.html) — every top-level key.
- [Context files (GEMINI.md, AGENTS.md)](https://google-gemini.github.io/gemini-cli/docs/cli/context.html).
- [Trust & workspace policies](https://google-gemini.github.io/gemini-cli/docs/cli/trust.html).
- [Telemetry](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html).
- [Environment variables](https://google-gemini.github.io/gemini-cli/docs/cli/environment.html).

### Commands

- [Built-in slash commands](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html).
- [Custom slash commands (TOML)](https://google-gemini.github.io/gemini-cli/docs/cli/custom-commands.html).

### Tools & extensibility

- [Built-in tools](https://google-gemini.github.io/gemini-cli/docs/tools/index.html).
- [MCP servers](https://google-gemini.github.io/gemini-cli/docs/tools/mcp.html).
- [Hooks](https://google-gemini.github.io/gemini-cli/docs/cli/hooks.html) — see [HOOKS.md](HOOKS.md) for captured snippets.
- [Subagents](https://google-gemini.github.io/gemini-cli/docs/cli/subagents.html).
- [Skills](https://google-gemini.github.io/gemini-cli/docs/cli/skills.html).
- [Extensions](https://google-gemini.github.io/gemini-cli/docs/cli/extensions.html).

### Memory

- [Memory system overview](https://google-gemini.github.io/gemini-cli/docs/cli/memory.html).
- [`save_memory` tool](https://google-gemini.github.io/gemini-cli/docs/cli/memory.html#save-memory).

### Reference

- [Repo](https://github.com/google-gemini/gemini-cli).
- [Releases / changelog](https://github.com/google-gemini/gemini-cli/releases).

## Subtopics

- [HOOKS.md](HOOKS.md) — lifecycle hooks: events, JSON contract, exit codes, paste-ready patterns.
