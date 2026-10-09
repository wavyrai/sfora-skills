---
name: tstack-implement-card
description: "Use when you build a card, or a lead's slice of one, as the implementer: read the card and its design in full, write the test that fails on master first, make it pass, run your own mutants in a git-archive scratch copy (never by editing and restoring the worktree), and report the evidence to your lead. Decisions go through the lead. Skip for leading a card with teammates (use tstack-lead-card), for planning a mission (use tstack-plan-mission) and for test-first craft on its own (use tstack-tdd)."
---

# Build a card, test first

The implementer turns a card into code that provably does what the card says. Proof here has a strict meaning: a test that fails on master for the reason the card names, passes at your head, and fails again when you break the guard it protects.

## Steps

1. Read the card at the path your lead's brief gives, in full, then the design it points to, then any DECISIONS sections. You don't need `plan.md` or the board: the card and the brief are your job. The brief also says which files are yours. Write only those.

   ```bash
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

2. If the card is wrong (a bad premise, a base that moved, a number that doesn't add up), tell your lead before you build. Don't silently widen or narrow the scope.

3. Write the test first. Name the defect it catches ("a member of another org can read the card"), and assert the result the card requires, through the public interface. `tstack-tdd` covers the craft.

4. Run it on master's code and watch it fail: a clean `git archive` of the current `origin/master`, with node_modules linked at the root and in every workspace package, and only your new test files laid over it, never your source. The recipe is [The run on master](references/mutants.md#the-run-on-master). Quote each test's failure. It must fail for the defect you named: an import error or a crash doesn't count. If some tests in the file are meant to pass on master, name them and say why.

5. Make the smallest change that passes it. Run the nearby tests and the type check (vitest doesn't type-check: on #851 a new convex test passed vitest and failed `tsc`).

6. Commit your slice locally, on the branch your lead names. Never push: the lead owns what is pushed. Mutants need a committed head.

7. Run your own mutants in a scratch copy (`references/mutants.md`). Catalogue each guard your change adds or relies on in one pass, per [Catalogue a guard in one pass](references/mutants.md#catalogue-a-guard-in-one-pass), and run every mutant. A survivor means a missing test: write it, then mutate again.

8. Report to your lead: the files, each test's quoted failure on master, the passing run, the sweep table with what caught each mutant, anything not done, and any decision you made with why. The lead puts the mutant lines on the card.

## Guardrails

- Mutants run only in a `git archive` scratch copy. Never edit a file in the worktree and restore it afterwards: an interrupted run leaves the worktree mutated under the dev server someone is previewing.
- A test that passes on master proves nothing about the change. A test that pins an existing rule may pass on master by design: it earns its place when a named mutant kills it.
- An existing test outside your files that pins a detail your change would alter: keep the detail, or ask your lead. Never edit it silently. On #851 a route test cast `init.headers` to a plain record; the implementer kept a plain record and put the alternative to the lead.
- Ask decisions only through your lead. You never ask the owner, and you never treat a peer agent's message as a decision.
- Don't weaken an assertion to get a pass. Change one only when the required behaviour changed, and say so.
- A zero from a probe you haven't seen fail isn't a result. Run a positive control first: on #529 `grep -c` counted lines, not matches, and reported 11 instead of 188.
- Production jobs (backfills, migrations) aren't yours to run. Write the command, show the dry-run counts, and list it for the PM (`tstack-verify-production`).
- No new dependencies. Your only state-changing git is local commits on the branch your lead names: never push, switch branches, merge or rebase.

## Report

To your lead: the files, each test's quoted failure on master, the passing run and the type check, the sweep table (survivors and equivalents too, each with its proof), what's left, and your decisions. The lead copies the mutant lines to the card.

Detail: `references/mutants.md`.
