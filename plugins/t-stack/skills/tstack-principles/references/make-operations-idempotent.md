# Make operations idempotent

**The rule:** an operation that changes state must reach the same end state however many times it runs and wherever a previous run stopped. Ask two questions of every write: what if this runs twice, and what if the last run died halfway?

## When it applies

Backfills, migrations, rollup rebuilds, deploy steps, secret rotation, card and doc writes, and any job that can be retried or restarted.

## How it looks in the t-stack

- **Dry run, real run, dry run.** The first dry run shows the counts (show them to the owner before anything destructive). The real run does the work. The second dry run must report nothing left to do. That last zero is the proof.
- **Dedupe keys on every backfilled row,** so a re-run updates instead of doubling.
- **Writes are round-trips.** `sfora put` a whole card or doc to its path, then `sfora cat` it back. Putting the same file twice leaves the same card.
- **Know which commands are not idempotent.** `sfora doc` creates a new doc every time; update an existing doc with `sfora put` on its path. A chat send retried blindly posts twice: read the room first.

## The incidents that taught it

- A second `sfora doc` meant to update a doc created a duplicate instead. The fix is in the tool skills: create once, then `put`.
- The 7,421-row backfill carried a dedupe key on every row, so a run stopped partway can be started again without doubling rows.

## What to do

1. For each write, name what makes it safe to repeat: a key, an upsert, a full replace, a check-then-skip.
2. When the honest answer depends on the state an earlier run left behind, start with a step that reconciles that state.
3. Prove it: run the operation, then run it again, and show the second run changed nothing.
