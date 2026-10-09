# Sequence verifiable units

**The rule:** cut work into small steps that each end in a state you can check. Check it before you start the next one. Order the delivery so a reviewer can watch the argument unfold.

## When it applies

Planning a mission, splitting a card, running a sweep of similar edits, a migration, a backfill, or stacking commits and PRs.

## How it looks in the t-stack

- **A mission is a card plus numbered cards:** "<Mission> 1/N: <step>". The order is design (owner review), then build, then verify. Each card ends in a gate someone can check: a mockup on a preview link, a PR with green checks on its head, a live check after deploy.
- **Stacked PRs are each gated.** Each lands on its own and is reviewed on its own head.
- **Inside a PR, the failing test comes first,** then the fix on top. A removal comes before the rebuild. A baseline capture comes before the change it measures.
- **Production jobs run in chunks,** each followed by a check of counts and health, never one long run you check at the end.

## The incidents that taught it

- #162's CI job was cancelled one second past its 35-minute limit. A job that long, that close to its limit, is one unit too big: split it so each part has room and fails on its own.
- The 7,421-row backfill ran with a rollup rebuild in one go and tripped the Convex plan limit. In chunks, each with a health check, the limit would have shown up at the first chunk.

## What to do

1. Write the steps down before you start, each with its check.
2. Start each step from a known-good state: rebase on a clean master first.
3. Run the check after each step, even when it seems obvious it passed.
4. Stop at the first red, and fix it there before building further.
