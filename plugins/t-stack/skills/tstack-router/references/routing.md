# Routing: role → situation → skill

The t-stack skills cover one job each. This page maps the situations you meet to the skill that handles them, and says where each card's life hands you on.

## The roles

- **The owner** is a person. They set goals, answer asks, approve cards and run production jobs. They read briefings, not logs. Their assistant agent uses `tstack-owner-desk`.
- **The PM** is an agent. It plans missions, reviews cards (unless another reviewer is named), runs the merge queue, verifies production and writes briefings.
- **A lead** is an agent that owns one card at a time. It splits the work across at most four teammates on separate files, integrates, owns every commit and hands over once.
- **An implementer** is a teammate under a lead. It builds a slice and reports to the lead, not to the owner.
- **The reviewer** didn't write the code. Usually the PM.

## A card's life, and the skill at each step

1. The owner states a goal. → `tstack-plan-mission` writes the mission card and the numbered cards, design first for a mission or any UI or product change. A goal that is one card gets a plan section on that card ("One-card goal").
2. A card is picked up. → `tstack-lead-card` (the lead), then `tstack-implement-card` (each teammate).
3. A question comes up. → `tstack-decide`: is it truly the owner's call? If so, one ask.
4. The card is done, stuck, or needs a decision. → `tstack-handoff`: one of READY FOR REVIEW, BLOCKED, or Decision for the PM, then stop.
5. The card is handed over. → `tstack-review`: tests at the head and against master, mutants, the risky code read by hand. APPROVED or CHANGES REQUESTED on the card, and the reviewer sets the state line to match.
   - On CHANGES REQUESTED, the lead answers with `tstack-lead-card` ("Answer a verdict"), hands over again with `tstack-handoff`, and the reviewer re-reviews (`tstack-review`, "Re-review"). Each round adds its own sections; nothing earlier is edited.
   - #851 went round this loop five times. Most of the cost was one guard mutated a little more each round, which is why the review makes one exhaustive pass per guard.
6. The card is approved. → `tstack-merge-queue`: merge on the head's own checks, one deploy at a time.
7. It deployed. → `tstack-verify-production`: check the live site, and run any data job dry → real → dry.
8. The owner wants to know where things stand. → `tstack-brief`.
9. Something stops moving. → `tstack-watch`.
10. The mission ends, or a mistake repeats. → `tstack-retro`, and the lesson becomes a test, a lint or a skill line.

`tstack-principles` holds the reasoning behind these steps, one page per principle, each with the incident that taught it.

## The craft skills

Use these inside a card, once you know what to build.

| Situation | Skill |
|---|---|
| How does this part of the code work, and where should a change live? | `tstack-how` |
| Why was it built this way? What changed, and when? | `tstack-why` |
| The change crosses module boundaries: sketch types and signatures first | `tstack-architect` |
| One attempt would lock in the wrong shape: build a few candidates and pick | `tstack-arena` |
| Write the failing test first, then the fix | `tstack-tdd` |
| Editing TypeScript | `tstack-typescript` |
| Many independent reads or checks to run at once | `tstack-swarm` |
| Before a commit: cut dead code and noise | `tstack-code-tidy` |
| Before review: comments that only restate the code | `tstack-no-comments` |
| Any prose: cards, posts, briefings, PR bodies | `tstack-unslop` |
| Docs, design docs, READMEs | `tstack-technical-writing` |
| Reporting a number you measured | `tstack-benchmark-checklist` |
| The project has no scripted way to drive its UI or CLI | `tstack-create-verification-skill` |
| That verification skill has drifted from the app | `tstack-maintain-verification-skill` |

## Who reads what

- **The PM and a lead** read `plan.md` and the board to find their open work.
- **An implementer** reads the card at the path its lead's brief gives, and the brief. Not `plan.md`, not the board.
- **A reviewer** reads the card the handover names, at its current path from `sfora tasks hq --json`.

## The tool skills

t-stack skills never re-teach a sfora command. When a step names one, the tool skill has the detail:

- `sfora-board`: cards, columns, `plan.md`, the card round-trip.
- `sfora-asks`: a question for the owner, and claiming or resolving work.
- `sfora-chat`: rooms, the READY FOR REVIEW line, `sfora watch`.
- `sfora-write`: posts (immutable), docs (updated with `sfora put`).
- `sfora-live-edit`: a doc someone is in.
- `sfora-setup`, `sfora-troubleshoot`: connecting, and the sharp edges.

## When two skills fit

Take the one for the earlier step. A card with no approval is not ready to merge, however green its checks. A fact you haven't measured is not ready for a briefing.
