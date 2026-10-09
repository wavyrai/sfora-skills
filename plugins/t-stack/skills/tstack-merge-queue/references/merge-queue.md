# The merge queue: detail

## Why each rule exists

- **Merge on the head's own checks.** A status page can show green for an older commit. `gh pr checks` lags after a push too. Read `headRefOid` from the PR, then the check runs for that SHA (`gh api repos/<repo>/commits/<sha>/check-runs`), and merge with `--match-head-commit <sha>`, so a push after your last look makes the merge fail instead of landing unreviewed code.
- **Check for a running deploy before each merge.** The master deploy uses cancel-in-progress: a merge that lands while a deploy runs cancels it. A queue takes long enough that the deploy from your last merge is often still running when you reach the next PR, so a check at the start of the queue is not enough.
- **Batch generated files.** The census and the design graph are written by scripts. Two PRs that each regenerate them conflict, and a hand-merged generated file is wrong in ways no reviewer sees. Regenerate on the integration branch by the scripts.
- **Confirm the deploy run exists.** GitHub once created no workflow run for a merge commit. The PR said merged; nothing had shipped. Look for the run by the merge SHA.
- **Additive schema.** Every merge deploys, and the old code serves traffic while the new deploy rolls out. A removed or newly required field breaks the running version.
- **Check the live site.** A green deploy run proves the build, not the change. Look at the page, the headers or a real request (see `tstack-verify-production`).

## Commands

Reading a PR:

```bash
gh pr view <pr> --json headRefOid,mergeable,mergeStateStatus
gh api repos/<repo>/commits/<sha>/check-runs
gh pr diff <pr>
```

Master's deploys (look for a run that is `in_progress` or `queued`):

```bash
gh run list --branch master --limit 5
gh run view <run-id>
```

The merge:

```bash
gh pr merge <pr> --merge --match-head-commit <sha>
```

After the merge:

```bash
gh pr view <pr> --json mergeCommit,state
gh run list --commit <sha>
gh run view <run-id>
```

## Proving an integration branch

The lead builds `<branch>` from the approved heads and regenerates the generated files. You check, without touching the worktree, that it holds the approved work and nothing else.

Fetch, so every head is local:

```bash
git fetch origin
```

Find the branch's tree:

```bash
git rev-parse <branch>^{tree}
```

For each approved head, simulate merging it into the branch in object space. A clean result whose tree equals the branch's tree means the head adds nothing the branch lacks:

```bash
git merge-tree --write-tree <branch> <sha>
```

Then see everything the branch changes against master, and match each file to an approved head or a generator:

```bash
git merge-base origin/master <branch>
git diff --stat <base> <branch>
```

A file in that list that no approved head touched, and no generator wrote, stops the merge. Send it back to the lead.

## When something goes wrong

- **Checks queued, then cancelled:** usually a conflict, not a flaky runner. Read `mergeable` first. Re-running overlapping workflows cancels them again.
- **The merge is refused (head moved):** someone pushed after the approval. The card needs a new review for the new head.
- **No deploy run for the merge SHA:** say so on the card and in the mission room, and tell the owner in the next briefing. Don't merge more until a deploy of master has run and passed.
- **The deploy failed:** stop the queue. The fix is a new card or a revert PR, reviewed like any other.
