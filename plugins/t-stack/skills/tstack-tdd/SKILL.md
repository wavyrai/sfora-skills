---
name: tstack-tdd
description: "Use when you fix a bug or add a guard and a cheap, local test can catch it, or when a card or a person asks for a failing test or a regression test. Write the test first, watch it fail for the right reason at the base, make the smallest fix, watch it pass, and keep the before-and-after as evidence for the card. Skip when the only test would be slow, brittle, mostly mocks or production-only (use the closest real check instead and say why), and for the card-level flow around it (use tstack-implement-card)."
---

# Test first, then fix

A fix without a test that failed before it is a claim, not evidence. So make the bug executable first: a small test that fails on the current code for the reason you expect, then the fix that makes it pass. The before-and-after is what the reviewer will check. In the t-stack, the reviewer swaps the base's source back in and runs your tests; a test that still passes there proves nothing.

## Steps

1. **Understand the bug.** What should happen, what happens now, which path, and the smallest way to see it.
2. **Pick the narrowest check.** Use the kind of test the project already uses for that code: unit, component, integration. If there's no cheap path, go to "When a test isn't practical" in `references/practice.md`; don't build a harness just to follow this skill.
3. **Write the failing test.** The smallest test that would have caught the bug. It states what the code should do, not how it does it today.
4. **Run it and read the failure.** Run the project's tests for that file. Quote the failure's content, not just its red line: an exception, an empty result and the mismatch you predicted all show red, and only the last shows the test is aimed at the bug. A test that goes green, or goes red for some other reason, needs fixing before any code changes.
5. **Fix it.** The smallest change to production code that gives the intended behaviour and keeps the contracts around it.
6. **Run it again.** It passes. Run the tests near it too.
7. **Break the fix on purpose.** In a scratch copy of the tree (never by editing and restoring your worktree), revert or weaken the guard and confirm the test fails. Catalogue the guard's mutants in one pass, not one at a time: on #851 four review rounds each found the next survivor of one 10-line comparison. The catalogue lives in `tstack-implement-card`'s [mutants page](../tstack-implement-card/references/mutants.md#catalogue-a-guard-in-one-pass).
8. **Record the evidence** for the card: the test's name, the failure quoted at the base, the pass at the head, and the mutant result. `tstack-handoff` puts it on the card.

## Guardrails

- Never change a test to match a wrong implementation, and never weaken an assertion unless the intended behaviour changed and you say why.
- A test that mostly tests mocks, copies the implementation, depends on timing or shared state, or needs costly setup for a small fix is worse than none. Prefer no new test to a bad one, and say so.
- Keep the test on the bug. No fixture churn, no unrelated coverage.
- For a flaky bug, make the test deterministic and say what signal it locks down.
- For a property no functional test can see (equal time, work saved), count its observable side effect and say the test is coupled to the implementation, or report it "not tested". See "Non-functional properties" in `references/practice.md`.
- When a verdict prescribes a test that clashes with these rules, write it and answer "done, with this caveat".
- When the bug is one of a family, land the focused test first; cover the siblings after.
- Don't run mutants in the worktree. An interrupted run leaves it mutated.

## Report

The test's name, the quoted failure at the base, the pass at the head, the mutant you tried and whether a test caught it. If no test was practical, the check you used instead and why.

Detail: `references/practice.md`.
