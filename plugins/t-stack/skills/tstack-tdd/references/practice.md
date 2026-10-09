# Test-first in practice

## Read the failure, not the colour

Three red runs that look alike:

- the test threw before it reached the assertion (an import error, a missing fixture);
- the code returned nothing, and the test compared nothing with a literal;
- the code returned the wrong value you predicted.

Only the third proves the test measures the bug. Quote the line that shows it, trimmed: "expected 1,000 items, received 100", not "1 failed".

## Prove it at the base, not only before your edit

"It failed before my fix" is weaker than "it fails at the base". The reviewer checks the second way: the PR's tests in a scratch copy at the head (they must pass), then the same tests with the base's non-test source swapped in (they must fail). Do the same check yourself before you hand over, and both results go on the card. The base is the current `origin/master` the PR will land on, and you quote the result test by test.

A scratch copy is made from git, not from your working folder:

```bash
git archive --output <scratch>/head.tar <sha>
tar -xf <scratch>/head.tar -C <scratch>/head
```

Link node_modules at the root and in every workspace package, run the tests once unchanged (they must pass), then run yours. The full recipe, with the cases that need care (a test whose interface changes with the fix, a test that pins an existing guard), is [The run on master](../../tstack-implement-card/references/mutants.md#the-run-on-master).

## Mutants

A mutant is a small, deliberate break: delete the auth check, flip the tenant comparison, remove the rate limit, make the dry run write. Each one must make a test fail. Run them only in a scratch copy, one at a time. Report each survivor; a survivor is either a missing test or an equivalent mutant, proved by running code.

Pick the guards that would hurt most if they broke: authorization, tenant isolation, rate limits, dry runs, privacy. Then catalogue each one in a single pass, every operator, bound, operand, return and early exit, not one mutant per guard. On #851 four review rounds each found one more survivor of the same 10-line comparison. The catalogue and the rules for reading a sweep are in `tstack-implement-card`'s [mutants page](../../tstack-implement-card/references/mutants.md).

## Non-functional properties

Some properties give the same answers whether they hold or not: an equal-time comparison, a cache that saves work, a log line that must not carry a secret. A functional test can't see them, and a timing test flakes under load.

- **Count the observable work.** Count the reads, the calls or the queries the property is about, deterministically. On #851 the test for "`sameSecret` is equal-time" counted `charCodeAt` reads with a spy and asserted at least one read per character, even after a first-character mismatch.
- **Accept the coupling, and say so.** Such a test is tied to the implementation: a rewrite with `codePointAt` would break it with no defect. Name the property in the test's describe, use `>=` rather than an exact count where you can, and record the coupling on the card.
- **Or mark it "not tested".** If no side effect is worth counting, say in the report that the property is not covered by tests, and why. Don't count it as caught.

## When a verdict prescribes the test

A review item may ask for a specific test you'd write differently, such as the read-count spy above. The verdict binds: write the test it asks for. If it conflicts with these guardrails, answer the item "done, with this caveat" and state the caveat (here, the coupling to `charCodeAt`). Don't skip the item, and don't quietly write a different test.

## When a test isn't practical

Use the closest real check instead: a short script, a manual reproduction with the exact command, a browser drive from the project's verify skill, a snapshot comparison, a log line you assert on. Record it on the card the same way: before, after, and how you ran it.

Don't add a test that:

- mostly tests its own mocks;
- copies the implementation's steps;
- depends on timing or global state it doesn't control;
- needs a large harness for a small fix;
- would be deleted right after it proved the fix.

## Tests that guard lessons

Some tests exist to stop a whole class of mistake, not one bug. This programme added `parts.test.ts` for colliding CSS part names and `projectCapDoors.test.ts` for a project door without the cap. When a fix closes a class, consider one such test, and say on the card which class it closes.
