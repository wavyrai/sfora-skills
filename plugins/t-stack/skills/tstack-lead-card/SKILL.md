---
name: tstack-lead-card
description: "Use when you're assigned a card as its lead, when your card comes back CHANGES REQUESTED, or when you resume one after a handover or a compaction: read the card in full, stamp @agent_state by round-trip, work on a fresh branch from origin/master, split the work across at most four teammates on separate files, integrate and own what is pushed, run the gate (typecheck and build in a git-archive scratch copy, the full tests, changed-file lint, gitleaks with a positive control, every generator in write mode), push, open the PR, hand over once, and answer each review round item by item. Skip for building a slice as a teammate (use tstack-implement-card), for review (use tstack-review) and for merging (use tstack-merge-queue)."
---

# Lead a card

The lead owns one card from start to handoff, and through every review round after it. Teammates write files; the lead splits the work, integrates it, owns what is pushed and runs the gate. The card is the source of truth throughout, so a lead that loses its context rebuilds it from the card, the PR and the branch.

## Steps

1. Find the card and read it in full: the owner's words, the design it points to, every DECISIONS section, earlier evidence and any verdict. Take the path from `tasks --json`, not from a column you expect. The fences below show `02-todo`; a card the PM left in Triage is under `01-triage`, and you read and put it there.

   ```bash
   sfora tasks hq --json --bot claude-code
   sfora cat /projects/hq/board/02-todo/<card-file>.md --bot claude-code > card.md
   ```

2. Stamp it. In `card.md`, set `**@agent_state:** working.` right under the H1 (add the line if the card has none) and `column: In progress`. Set `assignees:` only when you run under an agent member name; signed in as the owner, leave it. Put it back and read it again. The card now lives in `03-in-progress`.

   ```bash
   sfora put /projects/hq/board/02-todo/<card-file>.md card.md --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

3. Start a fresh branch from the current master, in a worktree outside the main checkout. If you reuse a worktree, `git status` must be clean and nobody else may be using it (an agent, a dev server). `--no-track` keeps the new branch from tracking master, so a later push can't land there.

   ```bash
   git fetch origin master
   git status
   git switch --no-track -c <branch> origin/master
   ```

4. Re-check the plan's code facts at the branch base. The plan was read at some older master. On #851 master moved between the plan and the brief, and the lead re-read the three code facts the plan relied on before briefing.

5. Plan the split. Give each teammate its own files and nothing shared: one folder or module each, at most four teammates. A file two teammates need belongs to one of them, or to you after they finish. Generated files belong to nobody: you regenerate them all at the end. Write the split on the card, so a restart can see it (`references/teammates.md`).

6. Settle the design forks. A fork inside the card's scope (which status code, which hash an audit row stores) is your call: decide it, write it in the brief and on the card, and let the teammate push back. Only what is the owner's goes through `tstack-decide`.

7. Brief each teammate in writing, from the template in `references/teammates.md`: the card's path, its worktree and branch, the files it owns, your design calls, done-when, the hard lines, and where its report goes. It reports to you, never to the owner, and asks decisions only through you.

8. Watch them. A teammate that reports nothing for an hour, or a job "still running" past an hour, is usually stuck or dead (`tstack-watch`). Check its output, then restart it on a smaller piece.

9. Integrate. Read every teammate's diff and re-check its evidence (`references/teammates.md`): always its fails-on-master run, in your own clean copy, plus a sample of its mutants. Teammates commit locally on the branch you name and never push. You own what is pushed, and you may reshape their commits first.

10. Run the gate on the head you'll hand over (`references/gate.md`). Every step must pass at that exact SHA. Fetch master right before it; if master moved, merge it and gate the merge.

11. Push the gated head and open the PR, in this order: fetch master once more (if it moved, merge and re-gate), push with `git push -u origin <branch>`, then open the PR with the body from `references/pr-body.md`. Check the PR's head is the SHA you gated:

    ```bash
    git fetch origin master
    git log --oneline <sha>..origin/master
    gh pr view <pr> --json headRefOid,mergeable
    ```

12. Hand over with `tstack-handoff`: the evidence on the card, with the PR number and the gated SHA, then one READY FOR REVIEW line, then stop. The order is gate, push, PR, evidence, handoff: the evidence needs the PR number and the SHA, and both exist only after the push.

## Answer a verdict

A verdict of CHANGES REQUESTED reopens the card. A round is one handover and its review. The reviewer's verdict in round N sets `changes requested (round N, head <sha>)`, and you answer in round N+1: `working (round N+1)`, then `## Evidence (lead, <date>, round N+1)` and that round's READY line ([evidence.md](../tstack-handoff/references/evidence.md), "Rounds"). On #851 review rounds 1 to 4 were answered by lead rounds 2 to 5, each a section on the card.

1. Resume first (below): fetch, check the SHAs, read the whole card, then set `**@agent_state:** working (round N+1).` by round-trip.

2. Read the verdict in full: `## Review (<role>, <date>, round N)`, its numbered items 1, 2, … and its notes N1, N2, …. Items block; notes don't. With no mission room, read the PR's comments too:

   ```bash
   gh pr view <pr> --comments
   ```

3. Fix each item at its cause. An item names a failing assertion (a test name or the assertion's text). A line number in it is only a pointer: if your change moves the assertion, prove it's the same one and quote its text. On #851 an `it.each` moved the named assertion from line 83 to line 87.

4. An item you think is wrong is still not yours to drop. Answer it with running code (a test, a differential check) and let the reviewer decide, or raise a `Decision for the PM:`. When you do what the item asks but it costs something, "done, with this caveat" is a valid answer: on #851 the verdict asked for a test that counts `charCodeAt` reads, which couples the test to the implementation, and the lead wrote it, said so, and named the property it stands for.

5. Answer each note: adopted, declined, or a follow-up card, with a reason. A declined note needs its reason; a follow-up needs the card's number or the card you'd file.

6. Re-run, at the new head:
   - the full gate. A change after the gate means a new gate, even when the round only adds a test;
   - every mutant the verdict names, plus the review's own survivors;
   - the master run for every new test, on current `origin/master`. A test that pins a guard master already has passes there by design: its proof is the mutants (`references/gate.md`).

   If master moved during the round, see `references/gate.md` for what carries over.

7. Put any mutant catalogue on the card: one line per mutant, with the file, the exact edit and the test that kills it. A sweep script in your session's scratchpad dies with the session. #851's round-5 lead had to grep every old scratchpad to find round 4's.

8. Bring the PR body up to date: the head SHA, the test and mutant counts, the gate run, one line for the round (`references/pr-body.md`). The card stays the record; the body is its summary.

9. Write one new section, `## Evidence (lead, <date>, round N+1)`, below the verdict. Never edit an earlier round's evidence. Answer each item by number and each note by its N-number, then the gate, the mutants and the master runs, then this round's READY line under it. The state line becomes `handed over: READY FOR REVIEW (PR #p, head <sha>).` Then stop.

   ```markdown
   ## Evidence (lead, <date>, round 2)
   - Item 1: <what you changed>. Proof: <test name> fails at <old sha> ("<assertion>"), passes at <sha>; mutant <M> killed by it.
   - Item 2: done, with this caveat: <the cost, and why you accept it>.
   - N1: declined, because <reason>. N2: follow-up #<n>.
   - Gate at <sha>: <the runs, failed ones too>. Mutants: <named ones, each with its killer>. Master runs: <each new test, with its reason>.

   READY FOR REVIEW #<n> (PR #<p>, head <sha>)
   ```

## Resume after a handover, a verdict or a compaction

Don't trust chat memory, yours or a summary of it. Rebuild from what's recorded:

1. The card: its `@agent_state`, the split, the evidence so far, every DECISIONS section and verdict.
2. The room's last messages since your last evidence (`sfora-chat`). A card with no mission room has the PR's comments instead (`tstack-router`).
3. The branch. Fetch first, then check that your branch, `origin/<branch>` and the PR's head are one SHA, and see what master gained since your base:

   ```bash
   git fetch origin
   git status
   git rev-parse HEAD
   git rev-parse origin/<branch>
   gh pr view <pr> --json headRefOid,mergeable
   git log --oneline <sha>..origin/master
   ```

If master moved, bring it in with a merge commit (`git merge origin/master`), never a rebase and force-push: a rebase orphans the SHAs the card's evidence cites. Then run every generator in write mode and commit any diff, because git can take one side of a generated file and be wrong for both. On #851 the merge kept a census of 1920 files where the right count was 1921.

Then write a short "Resumed (date)" line on the card, with what's done, what's open and your next step, and carry on. If the card and your memory disagree, the card wins.

## Guardrails

- At most four teammates, on disjoint files. Two writers on one file is a race, however careful they are.
- One teammate may share your worktree if you stop writing to it while it works. Two or more get their own, which you create outside the main checkout and pass in the brief.
- Teammates commit locally on the branch you name and never push, switch branches or rewrite history. You own what is pushed.
- Generated files are regenerated by their scripts, never hand-merged, every one, at the end. Run each in the mode that writes: the design graph needs `generate`, and without it the script only validates (three heads once failed the gate that way).
- Mutants run in a `git archive` scratch copy, never by editing and restoring the worktree. An interrupted run leaves the worktree mutated under someone's dev server. The method is in [mutants.md](../tstack-implement-card/references/mutants.md).
- The gate runs on the SHA you hand over, alone on the box. A change after the gate means a new gate.
- A pushed branch is never rebased or force-pushed. Master comes in by a merge commit.
- A teammate's "done" is a claim. Check its files and its evidence before you build on it.
- One handoff per round. Don't start the next card while this one waits for review.

## Report

The card, the branch and head SHA, the PR, who did which files, every gate run with where it ran and its SHA, the round, and the handoff line.

Detail: `references/gate.md`, `references/teammates.md`, `references/pr-body.md`.
