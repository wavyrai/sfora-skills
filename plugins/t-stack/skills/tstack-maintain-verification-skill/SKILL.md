---
name: tstack-maintain-verification-skill
description: "Use for a periodic pass over a project's verify skill and its feature map, after a mission changed what people can do, or when a drive from the verify skill fails for a reason that isn't the product. Read each feature's source with read-only subagents, drive every feature live yourself, fix the skill's own drift, file real product regressions as cards, and end clean, changed or blocked. Skip when the project has no verify skill yet (use tstack-create-verification-skill), and for fixing product code (file a card)."
---

# Keep the verify skill honest

A feature map starts going stale the day the app changes. This pass checks every feature twice, once from the source and once live, and fixes only the verify skill itself. The unit is the feature, not each sentence: every feature gets source coverage and a live drive.

## Steps

1. **Find the target:** the project-local verify skill with Launch and Drive sections and a `features/` folder. If there are several, ask which. If there's none, stop and use `tstack-create-verification-skill`.
2. **Tidy the index.** Read `features/README.md` and list the feature files beside it. Fix missing, extra, duplicate and dead entries.
3. **Read the source in parallel.** Start one read-only subagent per feature file, all at once. Each explains how the feature works from the code, flags likely drift with file and line, and returns one live recipe. They never drive the app and never edit. Brief: `references/source-reader.md`.
4. **Reconcile.** Every feature file has a summary back. Merge recipes that need the same app state. Spot-check the drift they cite. Look through recent merges for user-facing features the map lacks, and name a source path before you call one missing.
5. **Drive every feature live,** yourself, even when the source looks clean. Start the app the way the skill says to: a server or UI comes up once and every feature is driven against it in turn; a short-lived CLI gets a new session for each drive. Hold the three rules in Guardrails for the whole pass.
6. **Sort what you found:**
   - the map describes it wrongly → drift: fix the map;
   - it works but the harness can't drive it → a harness gap: fix the harness, then drive it live again;
   - the app is broken → a product regression: file a Triage card with the evidence (`sfora-board`), and keep it out of this change.
7. **End with one outcome** and say which:
   - **clean:** every feature covered, nothing to change, no branch;
   - **changed:** one PR of proven fixes to the skill's own folder, every changed file re-read first;
   - **blocked:** coverage couldn't finish, or a fix couldn't ship safely, and exactly why.

## Guardrails

- Edit only the verify skill's own folder: its SKILL.md, `features/` and its scripts. Never product code. When the map promises a behaviour the app has lost, that is either drift or a regression. Never hide a regression by editing the map.
- Three rules for the live drive:
  - run the health check before the first drive, after any failed drive, and on each fresh session; when the health check can't see the problem, reset or relaunch;
  - evidence survives every cleanup; check it at its location;
  - nothing a drive started outlives it; clean up after failed attempts too.
- A health-check failure caused by the skill's own drift: fix it, restart only what the fix affects, retry once, then call the pass blocked.
- A feature you can't reach is "verified unreachable" only with the missing prerequisite (sign-in, plan, OS) and the route you tried. If the map doesn't name that prerequisite, that's drift.
- Keep run notes in a scratch folder. Don't commit them.

## Report

The outcome, the features covered (source and live), anything unreachable and why, the drift fixed, the cards filed for regressions, and the PR if there is one. In a mission, append this to the card.

Detail: `references/source-reader.md`.
