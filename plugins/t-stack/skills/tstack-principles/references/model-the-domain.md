# Model the domain

**The rule:** put the domain's rules into a data structure (a state machine, a typed model, a lookup table, a union) instead of spreading them across conditionals in many files.

## When it applies

Stateful logic, code with many branches, and the same shape assumption repeated across files.

## How it looks in the t-stack

- **A card's life is a small domain:** its column (Triage, To do, In progress, Done) and its `@agent_state` line. Code that handles it should read from one definition, not from string checks in each place.
- **Reach for a state machine** the moment two flags have to agree with each other for the code to be right.
- **Reach for a lookup table or a discriminated union** when a new feature means one more branch in an existing if/else chain.
- **Organize modules around one body of knowledge,** not around the steps of a pipeline (load, validate, save). Phase-named modules tend to repeat the same rules.
- **Don't force it.** If the code is clear, local and unlikely to grow, plain code is fine. An abstraction must remove branches, duplicated rules or invalid states, or it's just indirection.

## What to do

1. List the states the code must make impossible, and the ways the data gets read.
2. Pick the structure that encodes exactly that.
3. Move the scattered checks into it, and delete them from the call sites.
