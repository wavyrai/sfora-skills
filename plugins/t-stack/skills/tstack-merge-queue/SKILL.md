---
name: tstack-merge-queue
description: "Use when you, the PM, land approved PRs in a sfora software factory: merge each only if its card says APPROVED for that head, on the head SHA's own checks with gh pr merge --match-head-commit, never while a master deploy runs (check right before each merge), batch PRs that touch generated files into one integration branch proven with git merge-tree, then confirm a deploy run exists for the merge SHA, check the live site, and write the MERGED line on the card. Skip for a PR whose card has no APPROVED line (use tstack-review), and for the live checks themselves (use tstack-verify-production)."
---

# Land approved work, one deploy at a time

Every merge to master deploys, and a new merge cancels a deploy that is still running. So you land one PR at a time, only on the commit that was reviewed, and you confirm each deploy before the next merge. Green checks are not an approval. The card is.

## Steps

1. Read the card. Merge only if it has `APPROVED #n` and the approval names the head you are about to merge:

   ```bash
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

2. Read the PR's head and state, then the check runs for that head SHA itself. Not `gh pr checks`, and not a status page: both can lag after a push and show an older commit's green.

   ```bash
   gh pr view <pr> --json headRefOid,mergeable,mergeStateStatus
   gh api repos/<repo>/commits/<sha>/check-runs
   ```

   `<repo>` is `owner/name`, and `<sha>` is the `headRefOid` you just read. Every required run must be `completed` with conclusion `success` for that SHA. If `headRefOid` isn't the approved SHA, stop: the card was approved for different code. If checks were queued and then cancelled, look at `mergeable` before re-running anything: it usually means a conflict.

3. Right before this merge (not just at the start of the queue), check that no master deploy is running or queued:

   ```bash
   gh run list --branch master --limit 5
   ```

   If one is in progress, wait for it to finish. Merging now cancels it.

4. Merge on the approved head only. `--match-head-commit` makes GitHub refuse the merge if the head moved:

   ```bash
   gh pr merge <pr> --merge --match-head-commit <sha>
   ```

5. Confirm a deploy run exists for the merge commit, and that it succeeded. GitHub once created no run for a merge, and nothing shipped:

   ```bash
   gh pr view <pr> --json mergeCommit
   gh run list --commit <sha>
   gh run view <run-id>
   ```

6. Check the live site for the change (headers, the page, a real request), with `tstack-verify-production`.

7. Write the result on the card, by round-trip: `MERGED as <sha>; deploy <run-id> OK; checked <what you checked>`.

   ```bash
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code > card.md
   sfora put /projects/hq/board/03-in-progress/<card-file>.md card.md --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

8. Post one line in the mission room (join it first; joining twice is harmless), then go back to step 1 for the next PR:

   ```bash
   sfora join <room> --bot claude-code
   sfora chat <room> -m "<message>" --bot claude-code
   ```

## Generated files: one integration branch

Files written by a script (the census, the design graph) conflict on every merge. Don't merge those PRs one by one, and never hand-merge a generated file:

- The lead builds one integration branch from the approved heads and regenerates the files by their scripts.
- You prove the branch holds exactly the approved work. For each approved head, `git merge-tree --write-tree <branch> <sha>` must report no conflict and print the branch's own tree (`git rev-parse <branch>^{tree}`): the head adds nothing the branch lacks. Then `git diff --stat <base> <branch>` must list only the heads' files plus the regenerated ones.
- The integration branch gets its own approval on the card, for its own head. Then it goes through steps 2 to 8 as one PR.

The commands are in `references/merge-queue.md`.

## Guardrails

- No `APPROVED #n` on the card for this head: no merge. A chat "approved" doesn't count.
- Never merge without `--match-head-commit`. Never merge with `--admin`, and never change branch protection to get a merge through.
- Check for a running master deploy before each merge, every time.
- Schema changes must be additive and optional: a new optional field, never a removed or required one in the same deploy. Old code keeps running while the new deploy rolls out.
- One merge, one confirmed deploy, then the next. Don't queue a second merge behind a running deploy.
- Never force-push a branch someone else is on.

## Report

For each PR: the card, the merged SHA, the deploy run and its result, and what you checked live. Then anything left in the queue and why it waits.

Detail: `references/merge-queue.md`.
