# Separate before serializing shared state

**The rule:** when several actors could write the same file, branch, key or doc, first ask whether they need to share it at all. Usually they don't: give each its own. Only when one shared target is a real requirement, serialize access by structure (one owner, one phase at a time), not by asking people to be careful.

## When it applies

Splitting a card among teammates, running several cards at once, merging a queue of PRs, and any job that writes to something others also write.

## How it looks in the t-stack

- **Teammates work on disjoint files.** At most four per card. The lead owns every commit and integrates.
- **One worktree and one port per mission.** Two missions never share a checkout or a dev server.
- **Generated files have one writer.** PRs that touch the census or the design graph go into one integration branch, and the files are regenerated there. Check that the batch equals the union of the approved heads with `git merge-tree` before it lands.
- **Claims make shared work exclusive.** An open ask is claimed with `sfora ask claim` before anyone starts, so two agents never do the same job.
- **Experiments get their own copy.** Mutants run in a `git archive` scratch copy, not in the shared worktree.

## The incidents that taught it

- The census and design graph conflicted on every merge while several PRs each wrote them. One integration branch made them one writer's job.
- Mutating the worktree for a mutant and restoring it afterwards risks leaving it mutated if the run is interrupted. Everyone else using that worktree then builds on broken code. A scratch copy removes the shared state.

## What to do

1. List what each actor writes.
2. Where two lists overlap, split the target so each actor owns its own part, and combine only when reading.
3. Where one shared target must stay, give it one owner or one phase, and make the tooling enforce it.
