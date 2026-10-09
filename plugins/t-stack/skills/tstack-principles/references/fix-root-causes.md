# Fix root causes

**The rule:** trace a symptom back to what caused it and fix it there. Reproduce first, keep asking why, and look for the same pattern elsewhere. A guard that hides the symptom leaves the bug in place.

## When it applies

Debugging, a flaky check, a step that "works sometimes", and any fix that needs a long comment to justify it.

## How it looks in the t-stack

- **Reproduce it in a scratch copy** before you change anything.
- **When a step silently does nothing,** read the script, not the log line that says it ran.
- **Grep for the pattern,** not just the instance. If one call site was wrong, check the rest.
- **When something breaks after a restart,** suspect saved state first (a cache, a lock file, a stale env file) before the code.

## The incidents that taught it

- The design-graph script only validates unless it gets the `generate` argument. A per-commit regeneration ran it without the argument, so it silently did nothing, and the graph went stale. Patching the stale graph by hand would have hidden the bug; the fix was the missing argument.
- Generated files (the census, the design graph) conflicted on every merge. Hand-merging them treats the symptom. The root cause is that they are outputs: regenerate them from the merged source, never hand-merge.

## What to do

1. Reproduce the symptom on demand.
2. Ask "why?" until the answer is a line of code, a config value or a missing step.
3. Fix that, and check every other place the same pattern appears.
4. When stuck, add logging or read the real error. Don't guess.
