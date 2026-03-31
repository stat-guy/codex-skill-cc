# Codex Agent — Run (New Session)

Launch a fresh Codex session. Capture its thread_id and store it as the sticky session for this workspace.

## Steps

### 1. Prepare run directory

```bash
RUN_ID=$(date +%Y%m%dT%H%M%SZ)
RUN_DIR="$HOME/.claude/codex-runs/$RUN_ID"
mkdir -p "$RUN_DIR"
```

### 2. If "new" was requested, clear sticky session first

```bash
rm -f "$HOME/.claude/codex-runs/active-thread.txt"
```

### 3. Write prompt to file

Write the full prompt (from `$ARGUMENTS` or user message) to `$RUN_DIR/prompt.md`.

### 4. Run Codex — blocks until complete, streams JSONL events

```bash
codex exec --json \
  -o "$RUN_DIR/final.md" \
  -p claude-skill \
  -C /Users/kairi \
  --skip-git-repo-check \
  "<FULL_PROMPT>" 2>&1 | tee "$RUN_DIR/events.jsonl"
```

For `$skill-name` passthrough, pass the prompt verbatim — e.g. `$polymarket https://...` — Codex's own skill system handles the `$` routing.

### 5. Extract thread_id from JSONL stream

```bash
THREAD_ID=$(python3 -c "
import sys, json
for line in open('$RUN_DIR/events.jsonl'):
    line = line.strip()
    if not line: continue
    try:
        e = json.loads(line)
        if e.get('type') == 'thread.started' or 'thread_id' in e:
            print(e.get('thread_id', e.get('id','')))
            break
    except: pass
" 2>/dev/null)
```

### 6. Store sticky session

```bash
echo "$THREAD_ID" > "$HOME/.claude/codex-runs/active-thread.txt"
echo "$THREAD_ID" > "$RUN_DIR/thread_id.txt"
```

### 7. Write status.json

```bash
python3 -c "
import json
data = {
  'run_id': '$RUN_ID',
  'thread_id': '$THREAD_ID',
  'workspace': '/Users/kairi',
  'state': 'done',
  'active': True
}
with open('$RUN_DIR/status.json', 'w') as f:
    json.dump(data, f, indent=2)
"
```

### 8. Surface result

Read `$RUN_DIR/final.md` and present it to the user. Include the thread_id so they can resume later:

> Codex session `<THREAD_ID>` — use `/codex [<THREAD_ID>] <task>` to continue this thread, or just `/codex <task>` to continue the current sticky session.
