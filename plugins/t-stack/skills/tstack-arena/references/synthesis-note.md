# The synthesis note

Append it to the card as `## Arena (role, date)` by the card round-trip (see the sfora-board skill), or put it in the design note's "The choice" section. Keep it short: the reader wants the decision and its reasons, not the transcripts.

## Shape

```markdown
## Arena (lead, 8 Oct)

**Brief:** one sentence, and where the full brief lives.
**Rubric:** the criteria, one line each.
**Candidates:** A (model), B (model), C (model). Dropouts: none.

| Criterion | A | B | C |
|---|---|---|---|
| ... | 2 | 3 | 1 |

**Judge:** recommended B, because ...
**Base:** B, because ...
**Grafted:** from A, the retry table (rewritten to B's types); from C, the empty-state copy.
**Dropped:** C's cache, because it needs a second writer for the same state.
**Verified:** the project's tests at the result's head; the mutant on the guard failed a test.
```

## Notes

- Score with numbers the rubric defines (for example 1 to 3), not adjectives.
- Name the model of each candidate and the judge, so a later reader can tell agreement across models from agreement within one.
- If the candidates converged, say so in one line and skip the graft list.
- Keep the candidates' folders until the card is approved, then delete them.
