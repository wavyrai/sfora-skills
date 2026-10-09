# Foundational thinking

**The rule:** get the data shape right before the logic. Decide the core types and structures first, trace how they'll be read and written, and build the scaffold every later step needs before the features that use it.

## When it applies

Starting a feature or a mission, choosing a schema or a core type, and deciding what comes first in a plan.

## How it looks in the t-stack

- **The design card comes first** for a reason: the shape of the data and the screens is cheaper to change on a mockup than in wired code.
- **Scaffold before features.** Tests, CI checks, shared types and the export or gate scripts help every later card, so they land first.
- **Schema changes are additive.** Every merge deploys, so a new shape must work beside the old one until callers move.
- **DRY the structure, not every line.** Shared types should converge; three similar statements still beat an early abstraction.
- **Ask what concurrent actors share** before they share it (see `references/separate-before-serializing-shared-state.md`).
- **Subtract before you scaffold.** Clear dead code first (see `references/subtract-before-you-add.md`).

## What to do

1. Write the core types or the schema first, and list each way they'll be read and written.
2. Choose the structure that fits the most common paths.
3. Sequence the plan: removals, then scaffold, then features.
4. Keep each commit to one purpose, landing or deepening one clear abstraction.
