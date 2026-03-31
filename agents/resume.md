# Codex Agent — Resume Existing Session

Continue an existing Codex thread. Used for sticky session continuity and explicit thread_id handoffs.

## Determine Thread ID

**Explicit ID** (user passed `[uuid]`):
- Strip `[` and `]` from the argument
- Use that UUID as `THREAD_ID`

**Sticky session** (no explicit ID):
```bash
THREAD_ID=$(cat ~/.claude/codex-runs/active-thread.txt 2>/dev/null)
```

If `THREAD_ID` is empty, route to `agents/run.md` instead.

## Steps

### 1. Prepare run directory (new dir per resume turn for clean state tracking)

```bash
RUN_ID=$(date +%Y%m%dT%H%M%SZ)-resume
RUN_DIR="$HOME/.claude/codex-runs/$RUN_ID"
mkdir -p "$RUN_DIR"
echo "$THREAD_ID" > "$RUN_DIR/thread_id.txt"
```

### 2. Resume Codex with the follow-up prompt

```bash
codex exec resume "$THREAD_ID" \
  --json \
  -o "$RUN_DIR/final.md" \
  -p claude-skill \
  --skip-git-repo-check \
  "<FOLLOWUP_PROMPT>" 2>&1 | tee "$RUN_DIR/events.jsonl"
```

Note: `codex exec resume` does not accept `-C` — the resumed thread is tied to its original workspace context.

### 3. Update sticky session (keep thread_id current)

```bash
echo "$THREAD_ID" > "$HOME/.claude/codex-runs/active-thread.txt"
```

### 4. Surface result

Read `$RUN_DIR/final.md` and present it to the user.

Include a note showing the active thread:
> Continuing Codex thread `<THREAD_ID>` — use `/codex new <task>` to start a fresh session.
