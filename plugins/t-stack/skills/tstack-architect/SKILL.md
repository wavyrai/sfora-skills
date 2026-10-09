---
name: tstack-architect
description: "Use when a change is big enough that writing code first would fix the wrong shape: a new module, a new data model, a change to who owns some state, or a card whose design the owner must see. Sketch the caller's usage, the types and the signatures first, compare at least two shapes, put the chosen sketch on the card or a doc, then build against it and scrap it when it keeps fighting you. Skip for a one-file fix with an obvious shape (just build it, with tstack-tdd), and for comparing finished candidates (use tstack-arena)."
---

# Sketch the shape before the code

A design is cheap to change while it is types, signatures and a page of text, and expensive once a hundred lines depend on it. So the shape comes first: how a caller uses it, what the types are, which module owns what. The sketch then becomes the contract you build against, and it lives where the team can see it: on the card, or in a doc linked from the card.

## Steps

1. **Ground it.** Learn every system the change touches before you draw anything. Run `tstack-how` on them. If the change moves ownership or layering, run `tstack-why` on the current shape too, so its reasons become constraints and not guesses. Skip this only for code with nothing around it.
2. **Write the caller's usage first.** Sketch two or three places in the caller's own code that would use it, showing the import line, the call and the value it gets back. The types follow from the usage. When the two disagree, change the types.
3. **Sketch twice.** Draw at least two shapes that differ as wholes, not one shape with a tweak. Bodies stay `not implemented`; tricky logic is pseudocode; each signature says its invariant. For a contested design, hand the sketch task to `tstack-arena` and let candidates compete.
4. **Screen each shape** against `references/red-flags.md`. Picture the next change made by an agent that reads a single file, imitates whatever example sits closest, and stops at the first version the compiler accepts. Pick the shape in which an edit that is correct locally is also correct everywhere else.
5. **Pick on depth.** Prefer the shape that hides more behind a smaller public surface. Write the design note from `references/design-note.md`: problem, usage, shape, the choice and what lost, the trade-offs you accept, open questions.
6. **Put it where the team sees it.** Append the note to the card by the round-trip (`sfora-board`), or create a doc once and update it with `sfora put` from then on (`sfora-write`). A design card for the owner waits for the owner's review; otherwise carry on.
7. **Build against the sketch.** Fill the bodies in. A parameter the sketch didn't expect is a question: was the sketch wrong, was a need missed, or is the code overreaching? Record each accepted deviation in the note, with who accepted it, before the next slice starts.
8. **Scrap it when it fights you.** One awkward edge case is normal. A pattern is not: see Guardrails. Then re-ground, redesign as if the new facts had been known on day one, make the new sketch smaller than the old one, and go back to step 3.

## Guardrails

- No code before step 5, except a throwaway spike to answer one question. Delete the spike.
- The sketch is the contract for teammates too. A lead who splits a card hands each teammate the sketch, and teammates never change a shared signature alone.
- Signs the shape is wrong:
  - the same workaround appears in unrelated places;
  - several unrelated edge cases each need a special branch;
  - the types need `any`, casts, or optional fields that are always set;
  - you add a lock although the sketch had no state that two callers touch;
  - callers have to know the module's internal rules to use it.
- Don't edit the design note to match code nobody accepted. Keep open disagreements visible on the card.
- Data shape first. Trace each common access through the structure. "We'll add an index later" means the structure is wrong.
- Validate at the boundary, trust the types inside, and give every piece of state one owner.

## Report

Name the card or doc that holds the design note, the shape you chose and the one that lost (one line each), the deviations accepted so far, and any open question for the owner.

Detail: `references/red-flags.md`, `references/design-note.md`.
