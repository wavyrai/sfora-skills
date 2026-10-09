# Encode lessons in structure

**The rule:** when the same correction comes up a second time, turn it into a mechanism (a test, a lint, a script, a check in CI) instead of another line of instructions. A rule someone has to remember gets forgotten. A rule the build enforces doesn't.

## When it applies

- You catch yourself writing the same reminder on a second card.
- A reviewer flags the same kind of defect twice.
- A retro produces a lesson.

## How it looks in the t-stack

- **Pick the strongest mechanism that fits:** a type that can't express the bad state, then a test or lint that fails CI, then a shared helper everyone calls, then a runtime check. Agents copy what the code around them does, so a weak guard becomes the next template.
- **Tests that encode lessons from this programme:**
  - `parts.test.ts` catches colliding CSS part names;
  - `projectCapDoors.test.ts` catches a project door without the cap;
  - the hosting guard stops the EU claim from coming back.
- **A lesson that needs judgment** goes into a skill, with the failure it prevents written next to it. The PM playbook doc is updated in place with `sfora put`, and the mission card gets one line per lesson encoded. The tstack-retro skill runs this loop.

## What to do

1. Ask: could a test, a lint, a type or a script catch this next time?
2. If yes, build it, prove it fails on the old mistake, and drop the reminder.
3. If no, make the instruction prominent and add the real example of what went wrong.
4. Close the loop now, or file a card in Triage. "I'll remember" doesn't persist.
