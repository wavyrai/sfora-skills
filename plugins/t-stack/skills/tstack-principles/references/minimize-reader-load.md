# Minimize reader load

**The rule:** code is easy to maintain when a reader can answer "where does this come from?" and "what can change it?" quickly. Keep the layers between a question and its answer few, and the state a reader must hold in their head small.

## When it applies

Writing or reviewing code that's hard to follow, adding a wrapper or a layer, and adding state.

## How it looks in the t-stack

- **Collapse layers that don't earn their keep:** a wrapper with one caller, an adapter with one implementation, a pass-through that repeats the same arguments.
- **Each layer should change the abstraction.** If two adjacent layers have the same methods, one of them is noise.
- **Prefer small, deep interfaces** that hide a real decision over wide ones that hide little.
- **Shrink state:** hand back a value rather than mutate one; keep it in a local before a field, and in a field before module scope. Derive a value instead of keeping two copies in sync.
- **State an invariant once, at the boundary,** not in every consumer.
- **The same holds for prose.** A card, a post or a skill is code for a reader too: lead with the answer, keep sentences short, cut what doesn't help.

## What to do

1. Each new layer or new piece of state must save the reader at least the effort it adds. Check that before you add it.
2. In review, pick one value and time how long it takes to find where it's set. If it's slow, cut a layer or cut the state.
