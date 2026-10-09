---
name: tstack-swarm
description: "Use when one task splits into many independent pieces that can run at once: reading or checking every file in a set, verifying several commits, measuring several options, hunting a bug from several angles, or racing workers on the same brief. Frame the done condition and the shape (slices, a race, or both), give each worker a brief that stands alone, collect PASS, ISSUES or BLOCKED with evidence, and report one table on the card plus one line in the room. Skip for competing designs to pick from (use tstack-arena), and for splitting a card's build among teammates (use tstack-lead-card)."
---

# Fan out workers and return one report

Some work is wide rather than deep: thirty files to check, five commits to verify, four ways a bug might happen. Workers can cover the slices at once while you keep your own context for the result. They may cover separate slices, race on the same brief, or both. You wait, gather, and return one report.

## Steps

1. **Frame it.** Write the done condition and what the swarm returns (a table, a verdict, a ranked list).
2. **Pick the shape.**
   - **Slices:** each worker covers a different part; every part needs a result.
   - **A race:** workers get the same brief; say up front how you'll pick: the first to pass, rank them all, or the best one.
   - **Both:** a race inside each slice.
3. **Set the number of workers** from the task or the person. Use a different model per arm only when the race is about models.
4. **Write each brief so it stands alone:** the goal, the exact slice or arm, the way to check the result, and the shape of the reply. When workers check commits, the brief names the exact SHAs. When they measure, it names the method (runs, what one run is, the order). Workers that write get their own output folder or worktree. Template: `references/worker-brief.md`.
5. **Launch them all at once,** as background subagents or teammates. If one drops out, carry on and note it.
6. **Gather.** Read each result. Drop one that doesn't record the SHAs and method its brief named, and give that worker one more try. If it misses again, write the slice down as a gap, which never counts as a pass. Apply the race rule you declared.
7. **Report.** Put the table on the card by the round-trip (`sfora-board`), and one line in the mission room (`sfora-chat`):

   ```bash
   sfora chat <room> -m "<one line: the verdict and the card number>" --bot claude-code
   ```

## Guardrails

- Every worker answers `PASS`, `ISSUES` or `BLOCKED` and attaches its evidence. Finding one provable defect doesn't end the job: the worker reports all of the ones it can prove.
- Workers that write never share files. Read-only workers can be as many as the work needs; workers that write follow the t-stack's limit of four teammates on separate files, and the lead owns every commit.
- Don't paste workers' raw output into the card or the room. Summarise: one row each, one line per issue.
- A swarm that measures still follows `tstack-benchmark-checklist`. Each worker's number needs its run count and range.
- Never let a worker merge, deploy or post on the card itself. Its result comes back to you.

## Report

One table (slice or arm, verdict, evidence), one line per proven issue, the gaps and dropouts, and the race rule if you used one. On the card, plus the room line.

Detail: `references/worker-brief.md`.
