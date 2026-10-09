---
name: tstack-retro
description: "Use at a mission's end, after a correction you've had twice, or when a reviewer or the owner catches the same kind of mistake again: turn each lesson into structure (a guard test, a lint, a script, a skill line, a card template) rather than a note, prove the guard fails on the real past mistake, and add the lesson to the PM playbook doc with sfora put. Skip for a one-off slip with no pattern, and for writing up a mission for the owner (use tstack-brief)."
---

# Turn lessons into structure

A note says "remember not to do this". The next agent never reads it. A guard test fails the build when someone does it, and the next agent can't miss that. The retro takes each lesson from a mission and moves it as far as it will go toward something that enforces itself.

## Steps

1. Collect the lessons. Read the mission's cards: every CHANGES REQUESTED item, every BLOCKED paragraph, every surviving mutant, every stall and every correction from the PM or the owner. A lesson counts once it has happened twice, or once if it took production down.

2. For each lesson, name the mistake in one line and the incident that shows it ("the privacy page named a hosting region nobody had measured").

3. Pick the strongest fix that works, in this order (`references/structure.md`):

   1. **Make it impossible:** one owner, one way to do it, the wrong path deleted.
   2. **Make it fail the build:** a guard test, a type, or a lint whose message names the right way.
   3. **Make it a step:** a script, a gate step, a card template field.
   4. **Make it a line:** in the skill that covers the moment, with the incident as the example. Only for judgment calls.

4. Build it, or file it as a card in Triage if it's more than an hour's work. A lesson with no fix and no card is lost.

5. Prove each guard on the real past mistake: put the mistake back in a scratch copy and see the guard fail with a message that says what to do.

6. Add the lesson to the PM playbook doc by round-trip. Update the existing doc with `sfora put`. Never run `sfora doc` again for it: that creates a second doc and the path stops reaching the old one.

   ```bash
   sfora cat /projects/hq/docs/pm-playbook.md --bot claude-code > pm-playbook.md
   sfora put /projects/hq/docs/pm-playbook.md pm-playbook.md --bot claude-code
   sfora cat /projects/hq/docs/pm-playbook.md --bot claude-code
   ```

7. On the mission card, one line per lesson: what was encoded, where, and how it was proven (`tstack-handoff` shows the card round-trip).

## Guardrails

- A note isn't a fix. "We'll keep that in mind" persists nowhere.
- If the fix is structural, don't also add prose for the same rule. The prose is the symptom.
- A guard that has never failed hasn't been tested. Show it fail on the real mistake.
- Fix the class, not the instance. Fixing the one project door that skipped the cap wouldn't have caught the next; a test over every door does.
- Skill lines name the incident. "Mutants only in a scratch copy (#808: an interrupted run left the worktree mutated)" is read; "be careful with mutants" isn't.
- A lesson that is a product, legal, money or brand call goes to the owner as a decision (`tstack-decide`), not into a guard.
- Drop a playbook line once its mistake can no longer happen.

## Report

Each lesson with its incident, the level you chose and why a stronger one didn't work, where it lives now (file, test, skill, template), how you proved it, and the playbook doc's path.

Detail: `references/structure.md`.
