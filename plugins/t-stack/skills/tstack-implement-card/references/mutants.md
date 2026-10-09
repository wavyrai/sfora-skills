# Mutants, safely

A mutant is the code with one guard deliberately broken. If no test fails, the guard isn't tested, however green the suite looks. On #815, 4 of 8 first-round mutants survived, and each one was a real gap in the tests.

This page is the one place for mutant guidance. The implementer, the lead and the reviewer (`tstack-review`'s scratch review) all follow it.

## The scratch copy

Mutate a copy of a commit, never the worktree. Export the committed head into an empty folder outside the worktree:

```bash
git rev-parse HEAD
git archive --output <scratch>/head.tar <sha>
tar -xf <scratch>/head.tar -C <scratch>/head
```

Not a scratch copy: editing the worktree and putting the file back with `cp` or `sed`, or a copy of the working tree. On #808 both were declined. A run that stops halfway leaves the worktree broken, under a dev server the owner is previewing and teammates are editing.

If the mutant has to be served (a page, an API route), build and serve the copy on its own port. Never change files under a shared dev server.

### Link the dependencies, then a green baseline

`git archive` leaves out `node_modules`. Link the worktree's installed copy into the scratch copy, at the root and in every workspace package that has one (`ln -s <worktree>/node_modules <scratch>/head/node_modules`, then the same for each `packages/*/node_modules`). Link, never read or print, any env file a build needs.

On #851 the implementer linked only the root, and the first master run failed with `Cannot find package 'remark-frontmatter'` from `packages/markdown`: an import error, not a result. Both the implementer and the reviewer then linked 7 workspace folders.

Before the first mutant, run the tests you'll use in the unmutated copy. They must all pass. A copy that is red before you break anything can't tell you what a mutant did.

## The run on master

The proof that a test is aimed at the change: it fails without the change, for the defect it names.

1. **Use the current `origin/master`,** the one the PR will land on. After a merge from master there are two candidates (the old merge base and the new master); use the new one. Fetch first.
2. **Make the master copy from a clean archive,** with the same linking and baseline as above:

   ```bash
   git fetch origin
   git rev-parse origin/master
   git archive --output <scratch>/master.tar origin/master
   tar -xf <scratch>/master.tar -C <scratch>/master
   ```

3. **Lay only your new and changed test files over it,** never your source. A `git archive <sha>` of the test files, extracted into the master copy, does it ([the swap copy](../../tstack-review/references/scratch-review.md#2-the-swap-copy-masters-source-the-prs-tests) in `tstack-review` shows the lines).
4. **Run them and quote each failure, test by test.** A file is not "red on master" as a whole. On #851, 2 of 6 tests in one file were meant to pass on master; say which, and why each passes.

Three cases need care:

- **A copy with the new tests laid over it is not master.** To ask "does master itself fail?" (a stale generated file, a flaky test), use a clean copy with nothing overlaid. On #851 a lead's census check ran in the overlaid copy, so master looked stale too; a clean copy passed 29 of 29.
- **A test whose interface changes with the fix** (it imports a new signature) fails on master at the type or the call, not on behaviour. Write it so it reaches the behaviour on both sides, or say why it can't and what proves it instead. On #851 a new `/mcp` test imported the changed `verifyApiKey` and would have failed on its arity.
- **A test that pins an existing guard** passes on master by design (#851's "today's rule stays" checks, and the direct `sameSecret` tests, whose code came from #849). Its proof is the mutant it kills. Record its master run with the reason it passes, and name the mutant.

## Catalogue a guard in one pass

#851 took four extra review rounds on one 10-line comparison, `sameSecret`. Each round found the next survivor: the length-only return, then last-character-only, then the deleted length check, then the bit and case weakenings. Each was fixed alone, so the next round mutated around the fix. One exhaustive pass per guard in round 1 would have found them all at once.

So for each guard the card rests on, write the whole catalogue before you run anything, and run all of it. The round-5 lead's catalogue for `sameSecret` and its call site had 47 mutants.

### The syntactic catalogue

For every line of the guard:

- **each operator:** every relational operator to its neighbours (`<` to `<=`, `>`, `!==`), `|=` to `=`, `^` to `-` or `&`, `&&` to `||`, `!` dropped;
- **each bound, ±1:** `i < n` to `i < n - 1` and `i <= n`, at both ends of a range;
- **start, end and stride of a loop:** start at 1, end one early, step by 2, take the length from the other operand;
- **each operand:** swapped, replaced by a constant, replaced by the other variable of the same type;
- **the return:** `true`, `false`, the negation, an intermediate value instead of the final one;
- **each early exit:** deleted, inverted, or moved after the check it guards;
- **each call-site argument:** swapped, dropped, the same value passed twice, the caller's check removed.

### Semantic weakenings

A real bug in a guard often doesn't look like a typo. Add the weakenings that bug would take. For a secret or token comparison:

- **per bit:** mask each code unit before comparing (`& 0x7f`, `& 0xff`), so a high bit is ignored;
- **per case:** fold case (`& ~0x20`, `toLowerCase()` on both sides);
- **per length:** compare a prefix, or only the length.

On #851 four of these survived all 3,326 tests at round 4, two of them reachable over HTTP. The test that kills them flips each bit at each position and sends the secret upper-cased.

### Comparison guards: first, middle, last

For a comparison, test a mismatch at the first, the middle and the last position, not one position. A test shaped by one survivor ("the secret with its last character changed") leaves its siblings alive: on #851 `diff = …` instead of `diff |= …` and a loop starting at 1 both survived 3,298 tests, and a later `i < a.length - 1` would have survived the reviewer's own first-character fix. An `it.each` over first, middle and last killed all of them.

### Guards outside the diff

Mutate the guards the change relies on, in the diff or not. On #851 `sameSecret` was #849's code, unchanged, and the card's lockout rested on it. A survivor there is a test this PR should add, even when the code is correct.

## Apply each mutant exactly once

A `sed` that matches nothing leaves the code unchanged, and the run reads as "survived". Before you read a result:

- **Check the mutant applied once.** Use a replacement that refuses unless its pattern matches exactly once, and diff the file against the archive.
- **Check its line is reachable** for the input that should matter. On #851 an "early true for an empty proof" mutant sat after the length check, which returned first: it applied once and could never run. Record that as equivalent by placement, not as a survivor.
- **One mutant per copy,** or restore the file from the archive before the next one.

## Run the sweep

- **Run the tests that should catch it first,** then confirm every survivor against the whole relevant suite before you report it. A mutant caught only outside the PR's tests is still caught; a "survivor" judged on a narrow set may be false. On #851 each survivor was confirmed on convex, proxy and `/mcp` together (3,297 tests).
- **Run the same mutants at the previous head too,** so a kill can be attributed to the new test. On #851 all 47 ran at both heads.
- **At most 3 sweeps at once, and never beside a gate.** At load 56 on 20 cores a gate's timing test failed in an unrelated file. Re-run an unrelated timing failure alone before you count it.

## Read the result honestly

- **A crash tests nothing.** A syntax error or a missing import isn't a catch. Make the mutant run, then look for the specific failure.
- **Check which assertion failed.** It should be the one that states the harm, so put that assertion first. On #851 the first lockout test was caught at a bucket check (`expected null to match object { count: 10 }`) before it reached the lockout; reordered, it failed as `expected 429 to be 200`.
- **A mutant caught by an unrelated test** is still a weak spot: the guard's own test didn't notice.
- **A survivor is a missing test or an equivalent mutant.** Write the missing test and run the mutant again to see it caught.

### Equivalent mutants

An equivalent mutant behaves exactly like the original, so no test can kill it (`^` to `-` when only zero matters, `diff <= 0` for a diff that is never negative). It isn't an item, but the claim needs proof by running code, not an argument: a differential check of the original against the mutant over generated inputs. On #851 the reviewer checked 9 such claims over 200,005 pairs (equal, random, one bit flipped, wrong length, NUL-padded, code units up to 0xFFFF). Report equivalents in their own list, each with its proof.

### Non-functional properties

Some properties no functional test can see. An early `return false` on the first mismatch gives the same answers as an equal-time compare. Either test the observable side effect (count the reads or the calls), accept the coupling to the implementation and say so on the card, or mark the property "not tested" in the report. A timing test isn't the answer: it flakes under load. `tstack-tdd` covers the craft: [Non-functional properties](../../tstack-tdd/references/practice.md#non-functional-properties).

### Input the transport can't carry

A survivor that needs input no request can carry (a NUL in a header, an empty proof the caller filters out) is either equivalent at the boundary or killed by a direct unit test of the guard's contract. Decide, and say which. On #851 three such survivors were killed by a four-assertion direct test of `sameSecret`.

## The sweep table

Record a sweep in one table, in this shape:

```markdown
| id | mutant | previous head (<sha>) | new head (<sha>) | caught by |
| --- | --- | --- | --- | --- |
| L1 | `diff \|= …` → `diff = …` | survived | caught | lockout.test.ts "mismatch at the first position" |
| E1 | early `return false` on mismatch | equivalent (not tested) | equivalent (not tested) | n/a: read-count test, see card |
```

- One row per mutant. The id is stable across rounds, so a later round can say "L1 still caught".
- Name each head with its SHA, and say where the mutated source came from (the archive of that SHA).
- "caught by" names the test and the assertion that failed, not a line number.
- Escape `|` in a cell as `\|`, or the row splits.

## On the card

The table, or one line per mutant:

```markdown
- mutant: tenant filter dropped in listCards → caught by cards.test.ts "other org sees nothing"
- mutant: dry run ignored in backfill → survived; added backfill.test.ts "dry run writes nothing"; now caught
```

When your lead owns the card, write these in your report and the lead puts them on the card. Keep the catalogue there, so the next session or reviewer starts from it.
