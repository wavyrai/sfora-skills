# Attack the premise

**The rule:** when two fixes that rest on the same assumption both fail, stop fixing. Write the assumption down, and find an observation that could prove it wrong before you try a third fix that depends on it.

## When it applies

Debugging that has gone round twice: a timeout raised twice, a retry added twice, a cache cleared twice, and the gate is still red.

## How it looks in the t-stack

- **Name the shared assumption on the card.** "Both fixes assumed the request reaches the right host." Writing it down makes it testable.
- **Pick an experiment that matches the assumption.** If you've raised a timeout twice, check where the request actually goes before raising it again. If you assume a step runs, check that it does anything.
- **For a claim about uneven load or ownership,** count the work per actor before you redistribute it. An even count weakens the theory; it doesn't clear a defect every actor shares.
- **Keep the experiment** if it will be useful again: a small script beats a one-time check.

## The incident that taught it

The design graph stayed stale after each commit's regeneration. Re-running the regeneration assumed the script generated anything. It didn't: without the `generate` argument it only validates. Checking the premise ("does this step write a file?") found the bug in one look.

## What to do

1. After the second failed fix, stop and write the assumption both fixes share.
2. Choose one observation that would show the assumption is false.
3. Make it, and quote the result on the card.
4. Fix the root cause the observation points to (see `references/fix-root-causes.md`).
