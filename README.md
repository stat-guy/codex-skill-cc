# codex-skill-cc

A Claude Code skill that invokes **Codex (GPT-5.4 agentic CLI)** as an autonomous collaborator. Codex can use its own skills (`$skill-name` syntax), spawn up to 8 parallel sub-agents, and work through multi-turn tasks before returning results.

## Features

- **Sticky sessions** — resume where you left off across `/codex` invocations
- **Intent routing** — automatically routes to run, resume, or status agents
- **Codex skill passthrough** — invoke Codex's own skills (e.g. `$polymarket`, `$ezfile`)
- **Multi-agent** — Codex fans out to up to 8 sub-agents with depth 16

## Usage

Install the skill into your Claude Code skills directory:

```
~/.claude/skills/codex/
```

Then invoke via:

```
/codex <task>
/codex $polymarket <query>
/codex status
/codex new <task>
```

## Structure

```
SKILL.md          — Main orchestrator / intent router
agents/
  run.md          — Launch a new Codex session
  resume.md       — Continue an existing session
  status.md       — Poll active run state
```

## Author

[Kai Wright](https://github.com/stat-guy)
