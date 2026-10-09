---
name: tstack-review
description: "Use when a card is handed over for review (READY FOR REVIEW #n on the card), or comes back for round N after CHANGES REQUESTED: check the head against current master with git merge-tree, run the PR's tests in a git-archive scratch copy at the head (they must pass), then with master's non-test source swapped in (they must fail), make one exhaustive mutant pass over each guard the card rests on (auth, tenant, rate limit, dry run, privacy, secret comparison), find what the change could break beyond the diff, read the risky code yourself (authorization, SSRF and redirects, DNS rebinding, secrets, privacy), and write APPROVED #n or CHANGES REQUESTED #n on the card by round-trip, setting its state line in the same put. Skip if you wrote the code, for merging (use tstack-merge-queue) and for production checks (use tstack-verify-production)."
---

# Review a card

The review is an independent verdict. The reviewer didn't write the code, doesn't take the evidence on trust, and checks the head that was handed over. Green CI and a confident summary are not a review. The tests must be shown to catch the defect, and the risky code must be read by a person or agent who wasn't its author.

A reviewer needs the card the handover names, not plan.md or the board. `hq` and `--bot claude-code` below are placeholders (`tstack-router` says how to fill them).

## Steps

1. Find the card's current path, then read it in full: what it asked for, the decisions, the evidence and the handoff line. Note the PR and the head SHA it names. If an earlier `## Review` section is on the card, this is a re-review: do the steps in `## Re-review` below first.

   ```bash
   sfora tasks hq --json --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

2. Check the PR's head is the SHA on the card, see what it changes, and check it against master as it is now, not as it was at the branch point:

   ```bash
   gh pr view <pr> --json headRefOid,baseRefName,mergeable
   gh pr diff <pr> --name-only
   git fetch origin
   git merge-tree --write-tree origin/master <sha>
   ```

   A different head means the evidence is for another commit. Review the head the card names, or send it back. `mergeable` only says the text merges. If master moved past the merge base, the merged tree is what lands: run the generated-file tests and the generators on it (`references/scratch-review.md`, "Current master"). On #851 both sides bumped the type census to 1919; git merged it clean and GitHub said MERGEABLE, yet the merged tree failed "is current" (it needed 1920).

3. Run the PR's tests in a scratch copy at the head. They must **pass** (`references/scratch-review.md`).

4. Swap master's non-test source in under the same tests. They must **fail**, and for the reason the card is about. Tests that pass on master prove nothing, with one exception: a test that pins a guard the PR doesn't change passes on master by design. Prove those with mutants (step 5) and say so in the verdict.

5. Make one exhaustive mutant pass over each guard the card rests on: auth, tenant, rate limit, dry run, privacy, a secret comparison, and whatever the card says must never happen. That includes guards outside the diff that the change relies on. Catalogue every mutant of the guard in one pass, not one per round: #851 took five review rounds because each round found one more survivor of the same 10-line `sameSecret`. How to catalogue, apply, check and judge mutants is in the implementer's [mutants.md](../tstack-implement-card/references/mutants.md); what differs for a reviewer is in `references/scratch-review.md`, "Mutants".

6. Look beyond the diff. Find what else reads or depends on what changed: a JSON shape another client parses, a column a job reads, a flag, a generated file. Find the one fact the change is safe because of, and prove it by running code, not by argument (`references/risky-code.md`).

7. Read the risky code yourself, line by line: authorization, SSRF (redirects followed, DNS rebinding, private addresses), secrets in argv, logs, output or a custom header that a redirect would carry, and privacy. Tests can't prove "no redirect was followed".

8. Write the verdict on the card and set its state line, in one round-trip. Read the card again right before the put, not at step 1: the lead or the PM may have written since. Append your section, change only the `**@agent_state:**` line under the H1 (add it if it's missing), put, and read it back. The read-back check is in `tstack-handoff`.

   ```bash
   sfora tasks hq --json --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code > card.md
   sfora put /projects/hq/board/03-in-progress/<card-file>.md card.md --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

   The section and the state line, by verdict:

   | Verdict line | State line |
   |---|---|
   | `CHANGES REQUESTED #854 (head ab12cd3)` | `changes requested (round 1, head ab12cd3).` |
   | `APPROVED #854 (head ab12cd3)` | `approved (PR #210, head ab12cd3).` |

   The section's heading is `## Review (<role>, <date>, round N)`. Under it go what you checked, then numbered items (1, 2, …) that block and notes (N1, N2, …) that don't. The template is in `references/scratch-review.md`, "In the verdict".

9. If the card has a mission room, post the verdict line there once, then stop:

   ```bash
   sfora chat <room> -m "<the verdict line, exactly as on the card>" --bot claude-code
   ```

   With no room, the state line you set in step 8 is the handoff, and the PR's comments stand in for the room (`tstack-router` says this once). Post nothing else unless your brief asks for it. Stop.

## Re-review

Round 2 and later: the card has your earlier verdict and a new `## Evidence (lead, <date>, round N)` section that answers it. Before any new work:

1. **Re-run each earlier item's proof,** the exact test or mutant that showed it, on the new head. An item is closed when its proof now fails the way it should, not when the lead's answer says it's fixed.
2. **Re-run every survivor the last verdict named,** and the mutants the lead's evidence names.
3. **Diff the reviewed heads:** `git diff <last-reviewed-sha> <sha>`, and `git log` over the same range. Anything outside the items' scope gets the full steps 3–7.
4. **Re-run steps 2–5 on the new head,** with one exhaustive pass per guard the new commits touch. A test written to kill one named survivor tends to be shaped by it. On #851 round 1 suggested "the secret with its last character changed", and the sibling mutants (`diff =` for `diff |=`, a loop from 1) survived 3,298 tests. List every survivor of the round at once.
5. **Say what you carried over.** Only the lead's gate can be carried over, on the terms in the guardrail below. A gate log from an older head, or one that names no SHA, gets re-run.
6. **If the branch gained a merge commit,** check it carries nothing of its own (`references/scratch-review.md`, "Current master").

The verdict section gets the next round number, and so does the state line. A round is one handover and its review: the first review is round 1, and the lead's answer and the review after it are round 2 ([evidence.md](../tstack-handoff/references/evidence.md), "Rounds").

## Guardrails

- If you wrote any of the code, you can't review it. Say so, and hand it to someone who didn't.
- The verdict names the head SHA. A push after your verdict needs a new review of the new head (`## Re-review`).
- Run everything in scratch copies. Never mutate the worktree, and never edit the PR's branch to "fix it while you're there".
- An item is a reachable wrong result or a broken documented property. A preference, a style point or an unreachable path is a note.
- Every non-equivalent survivor of a security guard (auth, tenant, secret, rate limit, privacy) blocks. State its severity on the item: a bypass, or a contract gap that needs more than the attacker has. An equivalent mutant is not an item, but the equivalence is shown by running code, not by argument.
- An item names the failing assertion (a test name or the assertion text). A line number is only a pointer.
- Don't approve on a claim you didn't check. "Not checked" with the reason is a fine thing to write.
- CI green, a bot's approval, or the author's own mutants don't replace any step.
- Re-run these yourself, every round: the master failure (step 4), the mutants (step 5) and gitleaks. You may verify the lead's full gate from its log instead of re-running it, when both hold: the log's first line (`== ref <ref> = <sha>`) names the head you review, and the changed files in the gate's copy match the head (`cmp` each against your head copy). Otherwise re-run the gate. Either way, the verdict says which you did.
- Each CHANGES REQUESTED item is something the author can act on: what's wrong, how you showed it, what would fix it. Before you suggest a fix for a survivor, check that it kills the survivor's siblings too.

## Report

The card, the round, the head you reviewed and the master you checked it against, pass at head and fail on master (with counts), each mutant and its result, what you read and what you found, what you carried over and at which SHA, and the verdict and state lines as written on the card.

Detail: `references/scratch-review.md`, `references/risky-code.md`.
