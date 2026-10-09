# Explain the number

**The rule:** a measured number is a claim. Before you quote it or decide anything from it, name its limiter and show the run wasn't timing some other thing. If you can't say why it isn't twice as good, you don't yet know what you measured.

## When it applies

Durations, speedups, row counts, error rates, test counts, costs, and any number that goes on a card or into a briefing.

## How it looks in the t-stack

- **Every number on a card says what bounds it.** "147 s for 31 pieces" needs its limiter beside it: the network, one slow piece, a lock, the machine. Then a reader can tell whether 31 more pieces take 147 s or 294 s.
- **Small numbers get checked too.** "An 8 s CI step" may be fast because it did the work, or because a cache skipped it or it ran nothing. Look at what it ran.
- **Counts need a cross-check.** "7,421 rows" from a dry run should match the source query, and the real run should report the same number.
- **A number near a limit is a warning.** #162's CI job was cancelled one second past its 35-minute limit. A job that takes 34 minutes is already failing; it just hasn't happened yet.
- **Before a bulk job, compare its size with the plan's limits.** The 7,421-row backfill plus a rollup rebuild tripped the Convex plan limit. The numbers were knowable in advance.

## What to do

1. Name the limiter, from evidence taken during the run, not from reading the code.
2. Write down the other things the run might have timed instead (failed calls, work skipped or served from a cache, noise, a step too small to matter), and eliminate each one.
3. Report the number with its run count, its spread and its limiter.

This is not `references/prove-it-works.md`, which checks the output is real. This checks the number means what you say.
