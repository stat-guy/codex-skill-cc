# codex-skill-cc

Run OpenAI's Codex (GPT-5.4) directly from Claude Code — as a collaborator, not a competitor.

## Why

Claude Code is great at orchestrating work. Codex is great at autonomous multi-step execution. This skill bridges them: you stay in Claude Code's interface while Codex handles the heavy lifting behind the scenes — research, builds, analysis, multi-turn tasks — and reports back when it's done.

No tab-switching. No copy-pasting between terminals. One prompt, two engines.

## What it does

- **One command** — `/codex <task>` dispatches work to Codex and surfaces results in your Claude Code session
- **Sticky sessions** — pick up where you left off; Codex remembers context across invocations
- **Skill passthrough** — invoke Codex's own skills (`$polymarket`, `$ezfile`, etc.) without leaving Claude Code
- **Multi-agent fan-out** — Codex automatically parallelizes across up to 8 sub-agents when the task calls for it

## Quick start

See [`CLAUDE.md`](./CLAUDE.md) for installation instructions — designed for Claude Code to walk you through setup.

## Author

[Kai Wright](https://github.com/stat-guy)
