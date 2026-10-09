# Prove it works

**The rule:** check the real thing, not a stand-in for it. A green tick, an agent's "done", a 200 status or "it compiles" is a proxy. Before you say a card works, look at the artifact itself: run the feature, read the value, open the page, read the diff.

## When it applies

Every time you are about to write "done", "fixed", "verified" or READY FOR REVIEW on a card. Also when you review someone else's work and their evidence is a summary instead of output you can re-run.

## How it looks in the t-stack

- **The reviewer re-runs the PR's tests in a `git archive` scratch copy.** First at the PR head, where they must pass. Then with master's non-test source swapped in, where they must fail. A test that passes on master proves nothing about the change.
- **The reviewer adds mutants.** Break the guard that matters (the auth check, the tenant check, the rate limit, the dry run) and watch a test go red.
- **After a deploy, check the live site,** not the CI badge: computed styles, response headers, a real request.
- **Evidence goes on the card by round-trip:** read the card, append the evidence, write it back, read it again. The second read is the proof the write landed.

## The incidents that taught it

- On #815, 4 of 8 first-round mutants survived. Each surviving mutant was a real gap in the tests, and the suite had been green the whole time.
- A scratch build without `.env.local` served the error boundary with a 200. The status code said "up"; the page said "something went wrong".

## What to do

1. Name the artifact the claim is about: a page, a row, a header, a test's failure on master.
2. Observe it directly and quote what you saw (the assertion's diff, the header's value), not that a check passed.
3. When an observation fails, suspect your method of looking before you suspect the system.
4. Where you can, make the check a script a reviewer can re-run, and link its output on the card.

See also `references/measure-before-you-claim.md` for facts you state outward, and `references/test-behavior-not-implementation.md` for what a test must catch.
