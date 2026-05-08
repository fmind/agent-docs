# Gemini CLI — Hooks

Hooks are user-defined shell commands that run on lifecycle events (e.g. before/after a tool call, on session start, on stop). They let you block dangerous tools, auto-format files, inject context, or enforce project policy without writing a custom subagent.

## Key concepts

- **Configuration** — declared in `.gemini/settings.json` under `hooks`. Each event maps to one or more matchers; each matcher carries a list of commands.
- **Stdin/stdout JSON contract** — the hook receives a JSON event payload on stdin and may emit JSON on stdout to influence the harness (e.g. additional system context, a refusal, modified tool arguments).
- **Exit codes** — `0` allows the action to proceed; non-zero signals a failure. Specific codes can deny a tool call or abort the turn depending on the event type.
- **Lifecycle events** — `PreToolUse`, `PostToolUse`, `SessionStart`, `Stop`, `Notification`, and others. Match on event + tool name (regex supported).

## Common patterns

- **Block dangerous tools.** A `PreToolUse` hook that exits non-zero on `Bash` calls containing `rm -rf /` or `git push --force`.
- **Auto-format on write.** A `PostToolUse` hook on `Write|Edit` that runs Prettier / Black against the changed paths.
- **Session-start context injection.** A `SessionStart` hook that prints repo-specific system context to stdout — adds it to the model's context for the session.
- **Audit log.** A `PostToolUse` hook that appends a JSON line to `.gemini/audit.log` for every tool call.

## Pitfalls

- **Silent failures.** A misconfigured hook command can hang the harness or be ignored without a clear error — test hooks in isolation first.
- **Performance budget.** Hooks run synchronously on every matched event; slow hooks compound across a session.
- **Path scoping.** Workspace hooks override global hooks for the same event/matcher — confirm which file is winning before debugging behavior.

## Canonical docs

- <https://google-gemini.github.io/gemini-cli/docs/cli/configuration.html>
- <https://github.com/google-gemini/gemini-cli>

_Last verified: 2026-05-08 against <https://google-gemini.github.io/gemini-cli/>._
