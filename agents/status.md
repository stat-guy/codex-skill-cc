# Codex Agent — Status

Poll the active Codex run and surface its current state.

## Steps

### 1. Get active thread ID

```bash
THREAD_ID=$(cat ~/.claude/codex-runs/active-thread.txt 2>/dev/null)
```

If empty, tell the user there is no active Codex session.

### 2. Find the most recent run dir for this thread

```bash
LATEST_RUN=$(ls -dt ~/.claude/codex-runs/*/thread_id.txt 2>/dev/null | while read f; do
  dir=$(dirname "$f")
  if grep -q "$THREAD_ID" "$f" 2>/dev/null; then echo "$dir"; break; fi
done)
```

Or simply find the most recent run dir:
```bash
LATEST_RUN=$(ls -dt ~/.claude/codex-runs/[0-9]* 2>/dev/null | head -1)
```

### 3. Read state files

```bash
# Status summary
cat "$LATEST_RUN/status.json" 2>/dev/null

# Final result (if done)
cat "$LATEST_RUN/final.md" 2>/dev/null

# Last few events (if still running)
tail -20 "$LATEST_RUN/events.jsonl" 2>/dev/null
```

### 4. Surface to user

Report:
- Thread ID
- Run state (running / done / error)
- Final result if present, or last checkpoint summary if still in progress
- How to continue: `/codex <followup>` to send another message to this thread
