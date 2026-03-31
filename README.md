# codex-skill-cc

Run OpenAI's Codex (GPT-5.4) directly from Claude Code — as a collaborator, not a competitor.

## Why

Claude Code is a strong orchestrator. Codex is a strong autonomous executor — and it comes with up to 8 parallel sub-agents built in. This skill puts all of that at Claude Code's disposal.

Once installed, Claude Code gains access to Codex's entire execution layer: its sub-agent pool, its skill system, its multi-turn memory. A single `/codex` call from your Claude Code session can fan out into 8 concurrent workers, each going 16 levels deep, and funnel the results back into your conversation. Claude Code doesn't just *call* Codex — it *commands* Codex's full infrastructure.

No tab-switching. No copy-pasting between terminals. One prompt, two engines, and a pool of sub-agents you didn't have before.

## What it does

- **One command** — `/codex <task>` dispatches work to Codex and surfaces results in your Claude Code session
- **Sticky sessions** — pick up where you left off; Codex remembers context across invocations
- **Skill passthrough** — invoke Codex's own skills (`$polymarket`, `$ezfile`, etc.) without leaving Claude Code
- **Multi-agent fan-out** — Codex automatically parallelizes across up to 8 sub-agents when the task calls for it

## Codex's sub-agents are now Claude Code's sub-agents

Without this skill, Claude Code works alone. With it, Claude Code has a bench.

When you send a task to Codex via `/codex`, Codex can internally spin up **up to 8 parallel sub-agents** with a recursion depth of 16. Those sub-agents are now effectively working *for* Claude Code. You describe what you need; Claude Code decides what to handle itself and what to ship to Codex; Codex's sub-agents do the work; results flow back into your session.

You don't manage the parallelism. Codex decides when to fan out based on the task. Ask it to "research 5 competitors and compare their pricing" and it may spawn a sub-agent per competitor, run them concurrently, and merge into a single deliverable — all from one `/codex` call.

This is especially powerful for:

- **Broad research** — scanning multiple sources, APIs, or datasets simultaneously
- **Code generation at scale** — building multiple modules or services in parallel
- **Analysis pipelines** — running independent computations and synthesizing results
- **Anything embarrassingly parallel** — if the subtasks don't depend on each other, Codex can run them all at once

### Planning hybrid workflows

The real power shows up when you use Claude Code as the **planner** and Codex as the **executor**. You can build a plan in Claude Code that deliberately farms out parts of the work to Codex:

1. Use Claude Code to break down a project into components
2. Handle the parts Claude Code is best at (architecture decisions, code review, orchestration)
3. Dispatch the heavy autonomous work to Codex via `/codex`
4. Review what Codex returns, iterate, and repeat

Think of it like a senior engineer (Claude Code) delegating tasks to a capable team (Codex + its sub-agents) and reviewing the output.

## Calling Codex skills

Codex has its own skill system at `~/.codex/skills/`. You invoke them by prefixing the skill name with `$` inside a `/codex` call. The skill name gets passed through verbatim — Codex handles the routing internally.

```
/codex $skill-name <arguments>
```

If you've built custom Codex skills (or installed community ones), they're immediately available through this bridge. No extra configuration on the Claude Code side.

## Example prompts

Here are some real-world examples to help you get started:

### Basic task dispatch
```
/codex scaffold a FastAPI app with JWT auth, SQLAlchemy models, and Alembic migrations
```
Codex builds the full project structure autonomously and returns the result.

### Research with sub-agent fan-out
```
/codex research the top 5 LLM inference providers, compare their pricing per million tokens,
      latency benchmarks, and supported model families. Return a markdown comparison table.
```
Codex may spawn a sub-agent per provider to research in parallel, then merge into a single table.

### Skill passthrough
```
/codex $polymarket what are the highest-volume markets this week?
```
Routes directly to Codex's Polymarket skill for market analysis.

### Multi-turn session (sticky)
```
/codex build a CLI tool that fetches weather data from OpenWeatherMap
```
*...Codex returns v1...*
```
/codex now add a --forecast flag that shows 5-day outlook and write tests
```
Picks up the same session — Codex has full context from the first turn.

### Hybrid plan — Claude Code plans, Codex executes
```
You (to Claude Code): I need to build a dashboard that shows real-time crypto prices,
                       a news feed, and portfolio tracking. Plan this out.

Claude Code: Here's the plan:
  1. React app with three panels (prices, news, portfolio)
  2. WebSocket connection to Binance for live prices
  3. News aggregation from multiple RSS feeds
  4. Portfolio state in localStorage with import/export

  I'll set up the project structure and component architecture.
  Let's have Codex build the WebSocket integration and news aggregator.

You: sounds good, farm out the websocket and news parts

You (to Codex): /codex build a WebSocket client module that connects to Binance's
                stream API for BTC, ETH, SOL price feeds. Also build a news aggregator
                that pulls from CoinDesk, The Block, and Decrypt RSS feeds. Return both
                as standalone TypeScript modules with types exported.
```
Claude Code owns the architecture; Codex builds the modules. You review and integrate.

### Check on a running session
```
/codex status
```
Polls the active run and shows current state, progress, or final results.

### Start fresh
```
/codex new analyze this repo's test coverage and suggest improvements
```
Clears the sticky session and starts a brand new Codex thread.

## Quick start

See [`CLAUDE.md`](./CLAUDE.md) for installation instructions — designed for Claude Code to walk you through setup.

## Author

[Kai Wright](https://github.com/stat-guy)
