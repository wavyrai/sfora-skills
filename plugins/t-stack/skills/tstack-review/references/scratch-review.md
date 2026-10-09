# The scratch review

Two copies, one question each. At the head: do the PR's tests pass? With master's source under them: do they fail? A test suite that passes both ways tests nothing the PR changed. A third copy, of the head merged with current master, answers what will actually land.

How to make a scratch copy (empty folders outside any worktree, `node_modules` linked at the root and in every workspace package, env files linked and never read, a green baseline first) is in the implementer's [mutants.md](../../tstack-implement-card/references/mutants.md). The same rules hold here.

## 1. The head copy

```bash
git fetch origin
git archive --output <scratch>/head.tar <sha>
tar -xf <scratch>/head.tar -C <scratch>/head
```

Run the PR's tests in `<scratch>/head` (the test files the PR adds or changes, then the nearby suite). They must pass. Note the count.

## 2. The swap copy: master's source, the PR's tests

Find the base, and the files the PR touches:

```bash
git merge-base origin/master <sha>
git diff --name-status <base> <sha>
```

Build the swap copy from master, then lay the PR's test files over it:

```bash
git archive --output <scratch>/base.tar <base>
tar -xf <scratch>/base.tar -C <scratch>/swap
git archive --output <scratch>/tests.tar <sha> "*.test.ts"
tar -xf <scratch>/tests.tar -C <scratch>/swap
```

Use the project's own test patterns in the `git archive` line (test folders, `.spec` files, fixtures the tests need). `git archive` stops with an error when a pattern matches nothing, so name only patterns the PR has. The result is the PR's tests over master's non-test source.

Run the same tests in `<scratch>/swap`. They must fail.

## Read the failures

The failure has to be the defect, not a side effect:

- **Good:** an assertion that names the behaviour ("expected 0 cards for the other org, got 3").
- **Weak:** "cannot find module './newThing'", or a type error because the function's signature changed. It shows the test needs the new code, not that it checks the behaviour. Look for a test that fails on an assertion, or ask for one.
- **Bad:** the tests pass. They prove nothing about this change. CHANGES REQUESTED.
- **By design:** a test that pins a guard the PR doesn't change passes here. On #851 the direct tests of #849's `sameSecret` passed on master. Their proof is the mutant pass; say so in the verdict.

Quote one failure per test file in the verdict.

## Current master

The swap copy uses the merge base. If `origin/master` has moved past it, also check the tree that will land:

```bash
git fetch origin
git merge-base origin/master <sha>
git rev-parse origin/master
git merge-tree --write-tree origin/master <sha>
```

`merge-tree` prints the merged tree's id on its first line, and any conflicts after it. A conflict is a CHANGES REQUESTED item: the lead merges master in. With no conflict, archive that tree into an empty folder (`git archive --output <scratch>/merged.tar <tree-id>`, then extract it) and, in that copy:

- run the generated-file tests (grep the test folders for each generated file's name);
- run every generator in write mode (find them in `package.json`) and compare each output with the file in the tree. A diff means it's stale;
- run the PR's tests and the nearby suite.

A clean textual merge of a generated file is not a current one. On #851 both sides bumped the type census to 1919, git merged it without a conflict, and the merged tree failed "is current" because the right count was 1920.

**A merge commit on the branch** can carry content no other commit shows: a conflict resolution, or an edit made during the merge. gitleaks in git mode scans no patch for a merge either. Re-make it and compare trees:

- `git merge-tree --write-tree <merge>^1 <merge>^2` prints a tree id;
- `git rev-parse <merge>^{tree}` must print the same id.

If they differ, read `git diff` between the two trees line by line, as you would any commit. On #851 round 3 the merge commit matched, so it carried nothing of its own.

## Mutants

The method is the implementer's [mutants.md](../../tstack-implement-card/references/mutants.md): the catalogue of one guard in one pass, checking that each mutant applied once and is reachable, confirming a survivor against the whole relevant suite, equivalence proved by code, and at most 3 sweeps at once. A reviewer adds:

- **Choose from the card, not the diff.** Mutate each guard the card's guarantee rests on, in or out of the diff. On #851 the round-1 survivor was in #849's unchanged `sameSecret`. A survivor there is a test the PR should add, even when the code is right.
- **Don't take the author's mutants on trust.** Re-run the ones the evidence names, then make your own pass. The lead's 48 syntactic mutants of `sameSecret` missed the semantic weakenings (a bit mask, a case fold), and 4 of those survived 3,326 tests.
- **Check an equivalence claim with code.** For each mutant the author calls equivalent, run original and mutant side by side over generated inputs (#851 round 4 used 200,005 pairs: equal, random, one bit flipped, wrong length, NUL-padded). A "side effect only" claim (timing, logging) needs a test of the side effect, such as a count of reads or calls, not a timing test.
- **On a re-review, mutate around the fix.** Run the siblings of each survivor the last round named, at the previous head and the new one, so a kill can be attributed to the new test.
- **Before you suggest a fix, run it.** Check that the test you propose kills the survivor and its siblings. A fix that kills one mutant invites the next round.
- **State the severity.** A survivor that allows a bypass, or one that needs the secret modulo case or one bit, both block a security guard. Say which it is.

## In the verdict

The shape, with your own values:

```markdown
## Review (<role>, <date>, round <N>)
- Head <sha>, checked against origin/master <sha>. PR tests: <n> pass at head; <n> fail on master's source ("<one failure, quoted>").
- Mutants: <guard>: <k> mutants, <k> caught, <k> equivalent (shown by <check>), <k> survived.
- Read: <what you read and what you found fine>.
- Re-run by me: master failure, mutants, gitleaks (<n> commits scanned).
- Gate: verified from the lead's log (ref <sha>, changed files match the head), not re-run by me. (Or: re-run at <sha>.)

CHANGES REQUESTED #<n> (head <sha>)
1. <the failing assertion, or the survivor and the test that should catch it>. Severity: <bypass / contract gap>. How shown: <command or test>. Fix: <what would close it, checked against its siblings>.

Notes, not blocking:
- N1. <an observation the lead adopts, declines or turns into a follow-up>.
```

For an approval, the verdict line is `APPROVED #<n> (head <sha>)` and there are no items. Notes may remain. Then set the state line in the same put (SKILL.md step 8).
