---
name: tstack-arena
description: "Use when one attempt at a non-trivial piece of work would fix the wrong shape: a contested design, an API, a tricky algorithm, a page of copy. Run two to four independent candidates on the same brief, have a separate judge score them against a written rubric, pick one as the base, graft the best parts of the others into it by hand, and verify the result. Covers the brief, the rubric, the judge and the synthesis note on the card. Skip for work with one obvious answer, and for splitting work into slices (use tstack-swarm)."
---

# Run candidates and keep the best

When the shape is contested, one attempt tends to lock in whatever came to mind first. Several independent attempts at the same brief show the real range of answers. You pick the strongest as the base and borrow the one or two good ideas from each of the rest. The candidates compete; they don't cover different parts.

## Steps

1. **Write the brief.** Every candidate gets the same one, so the brief is the contract: the artifact to produce, the grounding to read (the card, the design note, the files), and a short rationale to return with it, naming the options the candidate rejected.
2. **Write the rubric.** Three to six criteria you can grade, derived from what success means for this task. Candidates don't see it; it's the judge's tool.
3. **Give each candidate its own place.** A separate scratch folder or worktree per candidate, so none sees or overwrites another. Two to four candidates. Use different models when the task is judgment-heavy, the same model when it's mostly generation.
4. **Launch them together** as subagents or teammates, in one go. If one fails to return, carry on with the rest and note the dropout.
5. **Judge after they finish.** Start one separate read-only judge, preferably a different model from yours, with the rubric and the candidates by label only. In parallel, read every candidate end to end yourself and score each criterion. Don't judge on overall feel.
6. **Pick the base.** Agreement with the judge confirms it. Disagreement means a bias or a vague rubric: read both rationales before you decide. When two are tied, pick the one a later maintainer can extend without breaking its rules, usually the smaller interface.
7. **Graft by hand.** Go through each losing candidate once more and port what is worth it, usually one or two things. Rewrite them to fit the base; never paste. The result must still read as one design.
8. **Verify the result** the way you would any other work: tests, a live check, the reviewer's mutants. If verification finds a problem, either the brief was wrong (rewrite it and run again) or a candidate had the answer and you missed the graft.
9. **Write the synthesis note** on the card, or in the design note when this ran inside `tstack-architect`. Use `references/synthesis-note.md`.

## Guardrails

- Don't launch the judge while candidates are still writing.
- When the candidates agree, that agreement is the result: ship the shared shape and say so.
- When they disagree wildly, the brief was too vague. Rewrite it and run again; don't average the results.
- Candidates that write code work in separate folders, never in the shared worktree. Nothing a candidate wrote merges until you have grafted and verified it.
- Two to four candidates. More rarely adds a new idea and always adds reading.

## Report

The base and why, each graft with its source, what was dropped and why, any dropouts, the judge's verdict next to yours, and the verification result. Give the card the note is on.

Detail: `references/synthesis-note.md`.
