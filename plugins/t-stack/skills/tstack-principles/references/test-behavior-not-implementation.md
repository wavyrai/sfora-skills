# Test behavior, not implementation

**The rule:** before you keep a test, name a defect it should catch and check that it does. Drive the code through its public interface and assert the result or effect that matters, including that something did not happen when that's the contract.

## When it applies

Writing a test, reviewing one, or keeping an old one through a refactor.

## How it looks in the t-stack

- **A test that passes on master proves nothing about the change.** The reviewer swaps master's non-test source into a scratch copy and runs the PR's tests: they must fail there. A test that pins an existing guard passes on master by design; the mutant it kills is its proof.
- **Mutants check the rest.** Break the guard (the auth check, the tenant check, the rate limit, the dry run) in a scratch copy and a test must fail. A test that stays green under the mutant doesn't test the guard. Catalogue a guard's mutants in one pass ([mutants](../../tstack-implement-card/references/mutants.md#catalogue-a-guard-in-one-pass)).
- **Assert the effect, not that a helper was called.** A denied request must send no email: check the outbox is empty, then make the denied path send one and watch the test fail.
- **`toBeDefined` catches a missing result but accepts a wrong one.** Assert the value when the value matters.
- **Don't compute the expected value through the code under test.** It will agree with itself.
- **A non-functional property is tested by its observable side effect.** Equal time, work saved: count the reads or calls, accept that the test is coupled to the implementation, and say so. If nothing is worth counting, report the property "not tested". On #851 "`sameSecret` is equal-time" was tested by counting `charCodeAt` reads, with the coupling recorded on the card.

## The incident that taught it

On #815, 4 of 8 first-round mutants survived. The suite was green, and each survivor was a real gap: a guard no test would have noticed losing.

On #851 a test written to kill one survivor ("the secret with its last character changed") was shaped by it, and the next round found a sibling alive. A mismatch at the first, middle and last position killed the whole family.

## What to do

1. For each test, write the defect it catches in one line.
2. Introduce that defect in a scratch copy and watch the test fail. Quote the failure.
3. Strengthen or delete tests that stay green.
4. Run mutants only in a `git archive` scratch copy, never in the worktree (see `references/separate-before-serializing-shared-state.md`).
