# Evidence on the card

The card is the record of the work. Chat scrolls away, a session compacts, and a teammate's memory ends with its run. What's on the card survives all three, so the decision trail goes there.

## The round-trip

Every write to a card is the same five moves:

1. `sfora cat` the card into a local file. Find its path from `sfora tasks hq --json` first: the PM moves cards between columns, and an old path reads as empty. Redirect stdout only, as in `sfora cat <path> > card.md 2>/dev/null`. The card's URL footer goes to stderr; `2>&1` would write it, escape codes and all, into the card body.
2. Edit the file: append your section, or replace your own `**@agent_state:**` line. Nothing else.
3. Read the card again right before the put. Minutes or hours can pass between your first read and your write (a review round often takes an hour). If the body changed, take the new version and add your edit on top of it.
4. `sfora put` the file back to the same path.
5. `sfora cat` it again and compare it with what you wrote, from the H1 down, ignoring trailing blank lines. A difference there means someone wrote in between: read again, merge, put again.

A put rewrites the whole card. Skipping the last read is how one agent's evidence silently erases another's verdict.

What differs on every write, and isn't a conflict:

- **The frontmatter:** `lastActivityAt` changes on every put, and `columnId` after a move. That's why you compare from the H1.
- **A trailing blank line** at the end of the body is dropped.
- **"N of M block ids kept · K moved":** the put's report on block ids. "Moved" blocks kept their place in the card under a new id, usually the ones you edited and their neighbours. It is not a conflict. An "orphaned" count you didn't expect is worth a look: those blocks are gone.

## The section

Head it with your role and the date: `## Evidence (lead, 8 Oct)`, `## Evidence (implementer, 8 Oct)`. From round 2 on, add the round: `## Evidence (lead, 8 Oct, round 2)`. Facts with their proof, each one a pointer, not a story:

- **Commits and the PR:** the branch, the PR number, the head SHA you gated.
- **Tests:** the count, and the test that fails on master and passes at the head (name it and quote the failure).
- **The gate:** typecheck, build, the full suite, lint on the changed files, gitleaks with its positive control. Say where each ran (a `git archive` scratch copy at the head SHA).
- **Mutants:** each guard you broke and the test that caught it. A survivor is reported, not hidden: 4 of 8 first-round mutants survived on #815, and every gap was real.
- **Numbers:** each one with what limits it ("147 s for 31 pieces, bound by the slowest piece"). A bare number can't be checked.
- **Not done:** what's left, what's out of scope, and anything only the human can do (a click through an authenticated page, a production run). Give the exact steps.
- **Decisions you made:** one line each, with why. The reviewer and the next agent need the fork you chose, not just the result.

A zero from a probe you never saw fail is not a finding. Show the probe catching something real first (a known match, a planted key), then report its zero.

## The `@agent_state` line

One line, right under the card's H1. If the card has none, add it there. It always says the card's current state; the history is in the sections below it.

| Line | Set by | Means |
|---|---|---|
| `**@agent_state:** working.` | the lead, on pickup | A lead or implementer has the card (round 1). |
| `**@agent_state:** handed over: READY FOR REVIEW (PR #210, head ab12cd3).` | the lead, at handoff | Waiting for the reviewer. This is "handed over": there's no review column. |
| `**@agent_state:** changes requested (round 1, head ab12cd3).` | the reviewer, in the same round-trip as its verdict | Waiting on the lead to answer round 1's verdict. |
| `**@agent_state:** working (round 2).` | the lead, when it resumes on a verdict | The lead is answering round 1's items, in round 2. |
| `**@agent_state:** approved (PR #210, head ab12cd3).` | the reviewer, or the PM when the PM approves | Waiting on the merge. |
| `**@agent_state:** blocked (see BLOCKED #854).` | the lead | Waiting on what the paragraph names. |
| `**@agent_state:** waiting on a decision (see Decision for the PM).` | the lead | Waiting on the PM or the owner. |

Round numbers are defined under "Rounds" below.

Change the line only when the table says it's yours. On #851 the line kept saying READY FOR REVIEW after a CHANGES REQUESTED, so a watcher would have seen a card waiting for review that was waiting on the lead. That's why the reviewer now sets it.

**`assignees:`** in the frontmatter holds member names. Set it when you have an agent member name of your own (the PM sets the lead's at triage). Running signed in as a person, leave it as it is: your name there would be the owner's.

## Rounds

A round is one handover-and-review cycle. Round 1 is the first build, its READY line and the first review. Every skill that numbers rounds points here.

- A verdict in round N is `## Review (<role>, <date>, round N)`, with `APPROVED #n (head <sha>)` or `CHANGES REQUESTED #n (head <sha>)`. A CHANGES REQUESTED sets `changes requested (round N, head <sha>).`
- The lead's answer to it is round N+1. The lead sets `working (round N+1).`, writes `## Evidence (lead, <date>, round N+1)`, answering each item by number and each note (N1, N2) as adopted, declined or follow-up, with a reason (`tstack-lead-card`, "Answer a verdict"), and puts round N+1's READY FOR REVIEW line, with its new head, under that evidence.
- The reviewer's next verdict is `## Review (<role>, <date>, round N+1)`.

On #851, review rounds 1–4 were answered by lead rounds 2–5, and review round 5 approved. Each round adds sections and edits nothing earlier.

Earlier READY lines stay as history. The state line is the one that says where the card is now.

## Verdicts and decisions others append

- The reviewer: `APPROVED #854 (head <sha>)` or `CHANGES REQUESTED #854 (head <sha>)` with numbered items and notes, under `## Review (<role>, <date>, round N)` (`tstack-review`).
- The PM or owner: `**PM DECISIONS (8 Oct)**` or `**OWNER DECISIONS (8 Oct)**` (`tstack-decide`).
- The merger: `MERGED as <sha>; deploy <run> OK; checked <what>` (`tstack-merge-queue`).

Read all of them every time you resume a card. They overrule anything you remember from chat.
