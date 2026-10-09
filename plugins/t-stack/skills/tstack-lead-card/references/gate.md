# The gate

The gate is what the lead proves before the handoff. Each step runs at the head SHA you'll name in READY FOR REVIEW, and the evidence says where it ran.

## 0. Before you start

- **Fetch master first.** If it moved since your base, merge it (a merge commit on a pushed branch, never a rebase) and gate the merge, not the commit before it.
- **Run the gate alone.** No mutant sweeps, no other gate, no other heavy job on the box beside it. The suite has its own timing assertions: on #851 a gate run beside five mutant copies (load 56 on 20 cores) failed once in an unrelated file (`layered.property.test.ts`, "expected 32.67 to be less than 30"). The re-run on a quiet box passed. Run mutant sweeps before or after, at most three at once.
- **Name the SHA in the log.** Print `git rev-parse HEAD` in the copy as the log's first line, or name the log after the SHA. A gate log that doesn't name its SHA can't be tied to a head, and a reviewer can't carry it over (#851: the reviewer re-ran the gate because the log never printed its ref).

## 1. A scratch copy at the head

Commit first, so the head holds everything. Then export that commit, not the working tree, into an empty folder outside the worktree:

```bash
git rev-parse HEAD
git archive --output <scratch>/head.tar <sha>
tar -xf <scratch>/head.tar -C <scratch>/head
```

Link the installed dependencies into the copy: `node_modules` at the root and in every workspace package, or the first run fails on an import, which isn't a result. [mutants.md](../../tstack-implement-card/references/mutants.md) has the method. Link (never read or print) any env file the build needs. A build without its public env vars can still answer 200 and render the error page: on #796 a preview served the error boundary for days, because the pages checked never touched the backend.

A copy made from the working tree is not a scratch copy. It holds whatever is uncommitted, and it isn't what you'll hand over.

## 2. Typecheck and build

Run the project's typecheck and production build in the copy. A build in the worktree proves the worktree, not the commit.

## 3. The full tests

Run the whole suite, not the files you touched. Note the count. Run long suites with a timeout, and read the log when one stops: CI on #162 failed by one second over its time limit, and the job looked like a test failure. A timing test that fails in a file the branch doesn't touch: re-run the whole gate alone before you count it, and keep both runs on the card.

## 4. Lint on the changed files

Lint the files the branch changes against `origin/master`:

```bash
git diff --name-only origin/master...<sha>
```

## 5. gitleaks, with a positive control

Scan the branch's commits for secrets. Then prove the scan can fail, in the same mode and with the same flags as the real scan. The branch scan is `gitleaks git` over commits, so its control needs a fake key in a commit: a throwaway repo with one commit holding it. A key planted in an uncommitted folder only tests `dir` mode. A scanner that crashed looks the same as one that found nothing, and a misconfigured volume mount can make the scanner exit 1, which reads as "leak found". Report both runs.

After a merge, compare the "N commits scanned" line with the range's count, `git rev-list --count origin/master..<sha>`. `git log -p` shows no patch for a merge commit, so gitleaks skips it: on #851 it scanned 7 of 8 commits. A clean merge adds nothing of yours. A merge that resolved conflicts does: scan its diff too (`--log-opts` with `-m`), or the tree in `dir` mode.

## 6. Every generator, in write mode

Run every generator the repo has, in the mode that writes, whether or not you think the branch touched its inputs, and commit any diff. Adding one file can be enough: on #851 no generated file was touched, yet the type census counts every file under `src/`, so a new test file made it stale (fileCount 1918 → 1919), and the gate's full suite caught it.

- **Find them** in `package.json` scripts (on #851: `type:census`, and `design:graph`, which needs `generate`).
- **Find their checks** by grepping the tests for each output file's name: `git grep -l '<output file>' -- '*.test.*'`. Run those tests after you regenerate.
- **After a merge,** regenerate again. git can take one side of a generated file and be wrong for both: #851's merge kept a census of 1920 files where the right count was 1921.

Never hand-merge a generated file. A generator piped to nowhere hides a crash: on #765 the design-graph script was failing, and its silence read as "already current".

## 7. The test that fails on master

The card's new tests must fail without the change, for the reason the card names (`tstack-review` shows the swap copy).

- **"Master" is the current `origin/master`,** the one the PR targets. After merging master in, that's the new one, not your old base.
- **Ask whether master itself fails from a clean copy.** A copy with your new tests laid over it answers "do my tests fail on master's code", not "is master broken". On #851 the census looked stale on master too, until a clean `git archive origin/master` copy passed 29 of 29.
- **Quote per test, not per file.** A file can hold tests that fail on master and tests that pass there by design. Say which is which and why.
- **A test that pins a guard master already has** passes on master by design. It's proved by mutants: break the guard and see it fail ([mutants.md](../../tstack-implement-card/references/mutants.md)). Record its master run with that reason. #851's round-5 tests pinned `sameSecret`, which was #849's code.
- **A test whose interface changes with the fix** must fail on master on behaviour, not on a type or arity mismatch. Write it against what both versions accept (call through the route, not the changed function), or say why it can't be.

A test that passes on master and pins nothing proves nothing.

## On the card

Record every gate run on the way to the head, failed ones too, each with its SHA and the cause of a failure. A green run with no failed run beside it says the first try passed. The shape, with your own values:

```markdown
## Evidence (lead, <date>, round N)
- Head <sha>, PR #<p>, branch <branch>, on origin/master <sha> (fetched before the gate and before the push).
- Gate run 1 at <sha>: test step failed, census stale (fileCount 1918 → 1919). Regenerated in <sha>.
- Gate run 2 at <sha>, alone, in a git-archive copy: typecheck OK, build OK, <n> tests pass, lint clean on <n> changed files, gitleaks clean on <n> of <n> commits (positive control in git mode flagged), generators current.
- Fails on master (<origin/master sha>, clean copy): <test name>, "<the failure, quoted>"; <test name> passes by design (pins <guard>; mutant <M> kills it).
```

## Master moved after the mutant sweep

If master comes in after your mutants ran, check what the merge changed: `git diff --stat <old head> <new head>`. If it touches none of the files you mutated or the tests that killed them, carry the sweep over and write that diff on the card as the reason. Re-run the verdict's named mutants and the unmutated baseline at the final head either way. On #851 master gained #844 after the sweep; the diff showed two cli-authorize files and the census, so the sweep carried over.
