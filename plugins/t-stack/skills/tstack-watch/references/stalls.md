# Stalls: what they look like and what to do

## The signs

| What you see | What it usually is |
|---|---|
| A teammate silent for over an hour | Stuck on a prompt, looping, or its run ended without a report |
| A job "running" for hours, log unchanged | Dead, or hung on a lock or a network call |
| A card "working" with no evidence, commit or room line in an hour | Its agent stalled, compacted, or forgot to hand over |
| A card "changes requested" for an hour, no `working (round N)` | No lead picked up the verdict; the card waits on the lead, not the reviewer |
| A card "handed over" for an hour, no review section | The reviewer stalled, or was never told |
| A card "approved" and not merged | It waits on the merge queue (`tstack-merge-queue`) |
| A card still "handed over" with a CHANGES REQUESTED below it | The reviewer didn't set the state line; read the verdict, not the line |
| CI jobs queued, then cancelled together, no log | The PR conflicts with master; no runner ever picked it up |
| CI failing on a timeout by a second or two | The job is at its time limit, not a broken test |
| A preview link answering 200 with an error page | The server's build is broken, or it's missing its env |
| Zeros in a measurement run | The server died mid-run, or the probe broke |

## What to read

- **A job:** the last lines of its log and their times. The row counts or pieces done, now and an hour ago.
- **A teammate:** its last output. Whether its files changed since (`git status` in its worktree, or the folder's modification times).
- **A card:** `sfora cat` it. The `@agent_state` line, the newest evidence section, any BLOCKED line.
- **CI:**

  ```bash
  gh pr view <pr> --json mergeable,headRefOid
  gh pr checks <pr>
  gh run view <run-id> --log-failed
  ```

- **A server:** how long it has run, and what timeout it was started with. Request a page that touches the backend, not only a static one, and check the content is real.

## What to do

**A dead job.** Restart it in chunks under two hours each, with progress written as it goes, so a stop costs one chunk. Production jobs go through the PM and the owner (`tstack-verify-production`): dry run first, headroom checked.

**A dead server.** Restart it on the same port, with no timeout shorter than the work it serves. Check the page is real. Tell whoever had the link (the owner's preview was down for 40 minutes, unnoticed, on #764 after a rebase left a conflict marker in the config).

**A stalled teammate.** Read its last output. If it's waiting on a question, answer it or route it (`tstack-decide`). If it's dead, start a new teammate on the remaining files, with a smaller piece and the same brief.

**A card with no movement.** Message whoever the state line says it waits on, once, naming the card: in the mission room, or as a PR comment when there is no room. If the lead is gone, the PM reassigns the card and the new lead resumes from the card, the room and the branch (`tstack-lead-card`).

**A conflicting PR.** Re-runs can't help. Merge master in, regenerate the generated files by their scripts, push, and check `mergeable` again.

## Cadence

- An active CI run: check when it should be done, not every minute.
- Teammates working: every 20 to 30 minutes.
- Nothing running: hourly is enough. The room tail catches handoffs in between.

Write each stall and its fix on the card. The next watcher, or the retro, needs to see it happened (`tstack-retro`).
