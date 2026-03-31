# Installation — codex-skill-cc

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed and authenticated
- [Codex CLI](https://github.com/openai/codex) installed (`codex` available on PATH)
- `codex exec` must support `--json`, `-o`, and `-p claude-skill` flags

## Install the skill

Copy or symlink this repo into your Claude Code skills directory:

```bash
# Option A: Clone directly into skills
git clone https://github.com/stat-guy/codex-skill-cc.git ~/.claude/skills/codex

# Option B: Clone elsewhere and symlink
git clone https://github.com/stat-guy/codex-skill-cc.git ~/codex-skill-cc
ln -s ~/codex-skill-cc ~/.claude/skills/codex
```

## Verify installation

After installing, the skill should appear when the user types `/codex` in Claude Code. The directory structure should be:

```
~/.claude/skills/codex/
├── SKILL.md          ← Orchestrator (intent routing, session management)
└── agents/
    ├── run.md        ← Launches new Codex sessions
    ├── resume.md     ← Resumes existing sessions (sticky thread)
    └── status.md     ← Polls active run state
```

## Run state

Session data is stored at `~/.claude/codex-runs/`. Each run creates a timestamped directory with:
- `prompt.md` — the task sent to Codex
- `events.jsonl` — raw JSONL event stream
- `final.md` — Codex's final output
- `status.json` — run metadata (thread_id, state, workspace)
- `active-thread.txt` — sticky session pointer (workspace-scoped)

## Usage examples

```
/codex research the latest SEC filings for AAPL
/codex $polymarket https://polymarket.com/event/some-market
/codex status
/codex new build a FastAPI server with auth
```

## Troubleshooting

- If `/codex` doesn't appear as a skill, verify `SKILL.md` exists at `~/.claude/skills/codex/SKILL.md`
- If Codex fails to run, confirm `codex exec --help` works in your terminal
- If sticky sessions aren't resuming, check that `~/.claude/codex-runs/active-thread.txt` contains a valid thread_id
