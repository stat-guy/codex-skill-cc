---
name: codex
description: Invoke Codex (GPT-5.4 agentic CLI) to work on tasks autonomously. Codex can use its own skills ($skill-name syntax), spawn up to 8 parallel sub-agents, and work through multi-turn tasks before returning results. Use when the user wants Codex to research, build, analyze, code, or run multi-step tasks. Supports sticky session continuity across /codex invocations.
argument-hint: <task>, [session-id] <task>, $skill-name <args>, "status", or "new <task>"
user-invocable: true
thinking: adaptive
---

# Codex — Orchestrator

Invoke Codex (GPT-5.4) as an autonomous agentic collaborator. Codex can use its own skills, spawn sub-agents, run tools, and work through multi-turn tasks before surfacing results back here.

## Initial Input

**User request:** $ARGUMENTS

Route this through the Intent Routing table below. If `$ARGUMENTS` is blank, use the current user message instead.

## Sticky Session

Before routing, check for an active sticky session:

```bash
cat ~/.claude/codex-runs/active-thread.txt 2>/dev/null
```

- If a thread_id is present → default to resume (unless user requests new)
- If empty/missing → start new session
- Sticky session is workspace-scoped to `/Users/kairi` — if workspace changes, start new

## Intent Routing

Parse the input above (or user message if blank):

| Pattern | Agent | Notes |
|---|---|---|
| `[uuid-here] <task>` | `agents/resume.md` | Strip `[` `]`, resume that exact thread |
| `status` or `check` | `agents/status.md` | Poll active run dir for progress/results |
| `new <task>` or user says "fresh session" / "new session" / "start over" | `agents/run.md` | Clear active-thread.txt first, then start new |
| `$skill-name <args>` | `agents/run.md` | Pass verbatim — Codex's own skill system handles `$` routing |
| anything else + active sticky thread | `agents/resume.md` | Continue current session |
| anything else + no sticky thread | `agents/run.md` | Start fresh, store as sticky |

## Codex Skills

Codex has its own skill system at `~/.codex/skills/`. Invoke them by passing `$skill-name` verbatim in the prompt:

- `$polymarket <query or URL>` — Polymarket research
- `$ezfile <task>` — Tax filing tools
- Any other skill in `~/.codex/skills/`

Example: `/codex $polymarket https://polymarket.com/event/some-market`

## Multi-Agent

Codex automatically fans out to sub-agents when appropriate (`max_threads = 8`, `max_depth = 16`). No special flags needed — just give it a task and it decides internally.

## Workspace

`/Users/kairi`

## Run State Location

`~/.claude/codex-runs/` — see README.md for full contract.
