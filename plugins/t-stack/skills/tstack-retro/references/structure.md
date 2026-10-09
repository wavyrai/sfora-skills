# From lesson to structure

Agents copy the nearest example and take the shortest path that works. So the fix that lasts is the one that makes the wrong path fail, not the one that asks politely.

## The ladder

Go as high as the lesson allows:

| Level | What it is | When it fits |
|---|---|---|
| Impossible | One owner for the state, one supported way, the old way deleted | The mistake comes from two ways to do one thing |
| Fails the build | A guard test, a type, a lint with a message naming the fix | The mistake shows up in code or text the repo holds |
| A step | A script, a gate step, a field in the card template | The mistake is something skipped |
| A skill line | One line in the skill for that moment, with the incident | It's a judgment call no check can make |

Say in the retro why a higher level didn't work.

## Examples from this programme

**`parts.test.ts`: one stylesheet per part.** On #816's first preview, two component stylesheets styled the same `data-insights-part` name, and the one that loaded later restyled the other component (a ranked row lost its subgrid and its columns wrapped). The test reads every component stylesheet and fails on a part name styled in two of them, naming both files; deliberately shared parts are listed in the test. Level: fails the build.

**`projectCapDoors.test.ts`: every door that creates a project enforces the cap.** One HTTP route that creates a project skipped the plan's project cap. Fixing that one route wouldn't catch a sixth doing the same, so the fix added a test that finds every `insert("projects"` in the backend and fails unless it calls the cap check first, or is named in the test with the reason it's exempt. Level: fails the build.

**`privacy-facts.test.ts`: the hosting claim.** A privacy page named a hosting region from a doc; a header check and the backend's hostname showed a different one. The page now renders its facts (the hosting region, the processors, the retention periods) from one module, and the test pins each one, holds the retention figures to the code that enforces them, and fails if the region goes back to the wrong one. Level: fails the build, plus "measure before you claim" in `tstack-verify-production`, because the test can only pin what someone measured.

The first two also carry an anti-vacuity check: each first asserts it found the files it scans. A guard that silently scans nothing passes forever.

**The design graph's `generate` argument.** A regenerate pass ran the script without `generate`; it only validated, and three PR heads failed the gate. The design-graph test runs the generator in check mode and fails on a stale file, and the gate runs the full suite. Level: fails the build, plus a gate step (regenerate by the script, in the mode that writes).

**Mutants in a scratch copy.** Edit-and-restore mutants in the worktree were stopped twice. Level: a skill line in `tstack-implement-card` and `tstack-review`, plus a line in every teammate brief, because it's a habit no test sees.

**`sfora doc` twice.** Republishing a draft with `doc` made a second doc and changed the URL the PM had. Level: a skill line in `sfora-write` and here, with the incident.

**Merging during a deploy.** A merge cancelled a running master deploy. Level: a step in `tstack-merge-queue`, checked right before each merge.

## Proving a guard

1. Make a scratch copy at the commit with the guard.
2. Put the original mistake back: the duplicate part name, the door without the cap, the EU sentence.
3. Run the guard. It must fail, and its message must say what to do instead.
4. Write the failure on the card.

A guard that passes on the mistake is a broken probe, not a fix.

## The playbook doc

The PM playbook is a living doc. Each lesson is one bullet: the rule in bold, then the incident, then where it is enforced.

```markdown
- **Mutants run only in a git-archive scratch copy.** #808: an interrupted edit-and-restore run left the worktree mutated. Enforced: tstack-implement-card, tstack-review, every teammate brief.
```

Update it by round-trip with `sfora put` on its path. Find the path with `sfora ls /projects/hq/docs`. If someone else is in the doc, edit one block instead of the whole doc (`sfora-live-edit`).
