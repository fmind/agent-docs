---
last_verified: 2026-05-08
sources:
  docs: https://google-gemini.github.io/gemini-cli/docs/cli/hooks.html
  repo: https://github.com/google-gemini/gemini-cli
  changelog: https://github.com/google-gemini/gemini-cli/releases
---

# Gemini CLI — Hooks

User-defined shell commands that run on lifecycle events. Block dangerous tools, auto-format files, inject context, or enforce project policy without writing a custom subagent.

## Key concepts

- **Event** — a lifecycle moment the harness emits: `PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `SessionStart`, `Stop`, `Notification`.
- **Matcher** — a regex on the tool name (for `PreToolUse` / `PostToolUse`) or omitted for non-tool events.
- **Hook command** — a shell command, with optional `timeout` (ms).
- **JSON contract** — stdin payload from harness; stdout JSON parsed as a directive (additional context, refusal reason, modified args). Non-JSON stdout is ignored.
- **Exit code** — `0` allows the action; non-zero blocks `PreToolUse` / `UserPromptSubmit`, surfaces a warning on `PostToolUse`.

## Documentation map

- [Hooks reference](https://google-gemini.github.io/gemini-cli/docs/cli/hooks.html) — events, matcher syntax, JSON contract, examples.
- [`settings.json` `hooks` schema](https://google-gemini.github.io/gemini-cli/docs/cli/configuration.html#hooks) — top-level shape.
- [Repo source](https://github.com/google-gemini/gemini-cli/tree/main/packages/cli/src/hooks).

## Patterns

Block dangerous Bash invocations:

```json
"hooks": {
  "PreToolUse": [
    {
      "matcher": "Bash",
      "hooks": [{ "type": "command", "command": "./scripts/guard-bash.sh", "timeout": 5000 }]
    }
  ]
}
```

_Source: <https://google-gemini.github.io/gemini-cli/docs/cli/hooks.html>_

Auto-format on write:

```json
"hooks": {
  "PostToolUse": [
    {
      "matcher": "Write|Edit",
      "hooks": [{ "type": "command", "command": "npx prettier --write" }]
    }
  ]
}
```

_Source: <https://google-gemini.github.io/gemini-cli/docs/cli/hooks.html>_

Session-start context injection:

```json
"hooks": {
  "SessionStart": [
    { "hooks": [{ "type": "command", "command": "./scripts/inject-context.sh" }] }
  ]
}
```

_Source: <https://google-gemini.github.io/gemini-cli/docs/cli/hooks.html>_

## Pitfalls

- **Silent failures** — a misconfigured command can hang or be ignored without a clear error; test in isolation.
- **Performance budget** — hooks run synchronously on every matched event; slow hooks compound across a session. Set `timeout` (ms) to bound them.
- **Path scoping** — workspace hooks override global hooks for the same event/matcher.
