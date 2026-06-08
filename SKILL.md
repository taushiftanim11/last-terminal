# Last Terminal — End-of-Session Power Sequence

Triggered by `/last-terminal` or "use last-terminal" or "we're done" or "end session". Runs the full shutdown sequence: memory capture → session summary → memory sync → commit & push. Leaves no loose ends.

---

## Sequence (always in this exact order)

Run each step fully before starting the next. Do not skip any step even if a previous one produces no output.

### Step 1 — Time Capsule (`/time-capsule`)

Invoke the time-capsule skill. It reviews the full conversation and saves everything worth keeping:
- Finalized architecture/tech decisions
- User preferences and feedback (corrections, confirmations)
- Future TODOs and pre-launch checklist items
- Non-obvious commands, patterns, or gotchas discovered
- Security warnings or deferred suggestions

Wait for time-capsule to finish and report before proceeding.

### Step 2 — Session Summary (`/session-summary`)

Invoke the session-summary skill. Produces a human-readable digest of what was accomplished this session: what was built, what was fixed, what was decided, what's still pending.

Wait for session-summary to finish before proceeding.

### Step 3 — Sync Memory (`/sync-memory`)

Invoke the sync-memory skill. Ensures all memory files written by time-capsule are consistent, the MEMORY.md index is up to date, and any stale or duplicate entries are resolved.

Wait for sync-memory to finish before proceeding.

### Step 4 — Gitmit (`/gitmit`)

Invoke the gitmit skill. Stages all changes, writes a detailed commit message from the actual diff, and pushes to remote.

**Rules inherited from gitmit (never override):**
- No Co-Authored-By lines. The user is the sole author.
- Never commit `.env` files or secrets — warn and stop if detected.
- Never force-push unless the user explicitly said so in this invocation.
- If nothing to commit, say so and continue — don't block the sequence.

---

## Final report

After all four steps complete, output a single terse summary:

```
Session closed.
Memory: [what was saved or "already up to date"]
Summary: [one line from session-summary]
Commit: [short hash + first line] or "Nothing to commit"
Push: [branch → remote] or "Skipped"
```

---

## Error handling

If any step fails:
- Report the failure inline with the step name and error
- Continue to the next step — a failed time-capsule doesn't block gitmit
- List failed steps in the final report

## What NOT to do

- Do not ask for confirmation before starting
- Do not add commentary between steps
- Do not invent commit messages without reading the diff
- Do not push if secrets are staged
