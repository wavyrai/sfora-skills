---
name: tstack-watch
description: "Use while work runs unattended, as the PM or a lead (teammates, CI runs, long jobs, preview servers, cards waiting on agents): watch for stalls, not just handoffs. A job 'still running' for over an hour is usually dead: read its log or progress, restart it in chunks under two hours, and health- and age-check long-lived servers. Covers sfora watch, chat --follow on the mission room when there is one, and cards whose @agent_state hasn't moved, read as who the card waits on (working, handed over, changes requested, approved, blocked). Skip for reading one room once (use sfora-chat) and for driving approved PRs to master (use tstack-merge-queue)."
---

# Watch for stalls

A team of agents fails quietly. A teammate stops replying, a job sits at "running" for hours, a preview server dies and the owner's link shows an error. Nobody posts "I stalled". Waiting for handoffs alone finds these too late, so the watcher also looks at what hasn't changed.

## Steps

1. If the mission has a room, tail it for handoffs and questions. It prints new messages as they arrive and runs until stopped (`sfora-chat` covers rooms):

   ```bash
   sfora chat <room> --follow --json --bot claude-code
   ```

   A card with no room has its handoffs on the card and its talk on the PR (`tstack-router`, "No mission room"). Steps 2 and 3 find those.

2. Watch the project for card and doc changes, in polls of a fixed length:

   ```bash
   sfora watch hq --json --wait 30 --bot claude-code
   ```

3. On a regular beat (every 20 to 30 minutes is plenty), list the board and find cards that should be moving and aren't:

   ```bash
   sfora tasks hq --json --bot claude-code
   ```

   For each card in In progress, read its `**@agent_state:**` line (right under the H1) and the date of its last evidence or review. The line says who the card waits on:
   - `working.` or `working (round N).`: the lead;
   - `handed over: READY FOR REVIEW (…)`: the reviewer;
   - `changes requested (round N, …)`: the lead, to answer the verdict;
   - `approved (…)`: the merge (`tstack-merge-queue`);
   - `blocked (…)` or `waiting on a decision (…)`: whoever the paragraph or Decision section names.

   A card in any of these with no new evidence, review, commit or PR comment in over an hour needs a look. Nudge whoever it waits on, not the last writer. The states are defined in `tstack-handoff` ([its evidence reference](../tstack-handoff/references/evidence.md)).

4. Check what's running outside sfora: CI runs, long jobs, servers. For CI:

   ```bash
   gh run list --branch <branch> --limit 5
   gh run view <run-id> --log-failed
   ```

5. For anything that looks stuck, read its progress before you decide: the log's last lines and their times, the row counts, the teammate's last output. "Still running" isn't a status. Compare the progress with an hour ago.

6. Act on what you find (`references/stalls.md`): restart a dead job in chunks under two hours, restart a dead server on the same port and check it, nudge or replace a stalled teammate, or post BLOCKED on the card if it needs the human.

7. Write what you did on the card it affects, by round-trip (`tstack-handoff` shows the round-trip), so the next watcher doesn't repeat it.

## Guardrails

- Over an hour of "still running" with no new output means dead until shown otherwise. Read the log; don't wait another hour.
- Restart work in chunks under two hours, each one checkable. A six-hour job that dies at hour five loses five hours.
- Health-check long-lived servers by age as well as by status. A preview started with a one-hour timeout dies mid-check, and its empty results look like clean ones.
- A 200 isn't health. An error page also answers 200: check that the page is the real page (its title, a known element).
- Never re-run CI while a run on the same branch is queued or running. Runs share a concurrency group and cancel each other. Jobs queued then cancelled with no log usually mean the PR conflicts with master: check `mergeable` before any re-run.
- After a busy stretch, the event stream may have skipped events. Read the board and the room again instead of trusting the tail.
- Don't stop processes by name. Stop the one you started, by its id.
- Watching is not reviewing. A stalled card gets unstuck, not finished by the watcher.

## Report

What you watched and for how long, each stall found (what, since when, the evidence it was dead), what you did about it, and where you wrote it down.

Detail: `references/stalls.md`.
