# Last Terminal — End-of-Session Power Sequence

Triggered by `/last-terminal` or "use last-terminal" or "we're done" or "end session". Runs the full shutdown sequence: session summary → commit & push. Leaves no loose ends.

---

## Pre-flight check

**Before starting the sequence:** If the conversation contains pasted context (code, task description, feature request, bug report, or any work specification) that has not yet been acted upon, **pause and ask the user to confirm** whether they want to:
1. Work on that context first (return to work mode)
2. Close the session as-is (proceed with last-terminal sequence)

Do not assume. Pasted context = potential work. If uncertain whether context is "new work" vs "historical recap", ask.

---

## Sequence (always in this exact order)

Run each step fully before starting the next. Do not skip any step even if a previous one produces no output.

### Step 1 — Session Summary (`/memento`)

Invoke the memento skill. It reviews all available context and produces a structured human-readable digest of what was built, fixed, decided, and deferred.

Wait for memento to finish and output the summary before proceeding.

### Step 2 — Gitmit (`/gitmit`)

Invoke the gitmit skill. Stages all changes, writes a detailed commit message from the actual diff, and pushes to remote.

**Rules inherited from gitmit (never override):**
- No Co-Authored-By lines. The user is the sole author.
- Never commit `.env` files or secrets — warn and stop if detected.
- Never force-push unless the user explicitly said so in this invocation.
- If nothing to commit, say so and continue — don't block the sequence.

---

## Final report

After all steps complete, output a single terse summary:

```
Session closed.
Summary: [one line digest]
Commit:  [short hash + first line] or "Nothing to commit"
Push:    [branch → remote] or "Skipped"
```

---

## Error handling

If any step fails:
- Report the failure inline with the step name and error
- Continue to the next step — a failed memento doesn't block gitmit
- List failed steps in the final report

## What NOT to do

- Do not ask for confirmation before starting
- Do not add commentary between steps
- Do not invent commit messages without reading the diff
- Do not push if secrets are staged
- Do not skip `/memento` — call it as a skill, do not inline the summary
