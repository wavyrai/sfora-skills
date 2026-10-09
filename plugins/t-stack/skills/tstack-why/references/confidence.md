# Confidence tiers

Every claim in a why answer sits in one tier. The tier decides which section it goes in and how you word it.

## 1. Stated

Someone wrote down the reason, and you can quote it: a PR body ("this fixes paging past 1,000 items"), a code comment ("clamp to 100: the upstream API rejects more"), a design doc, the owner's decision on a card ("**Owner (8 Oct):** …"), a chat message from the author.

Word it plainly: "This exists because X", with the citation next to it.

## 2. Supported

No single source says it, but several point the same way: the PR title says "perf", the commits around it all touch the same hot path, and the tests added with it all use very large inputs.

Word it as derived: "The evidence points to X: A, B and C." Cite each.

## 3. Inferred

A fair reading of the context with nothing saying so: the fix merged the same day as an incident report in the room, so it was likely a hotfix.

Hedge it and show the chain: "Given A and B, X seems likely, because C."

## 4. Speculative

A possible explanation with thin evidence, where others fit as well. These go under "Competing explanations": "One possibility is X, but we found nothing from the time that says so."

## 5. Unknown

You looked and didn't find out. Say exactly what you searched, where, and for what: "We read every PR since March that changed this file (six of them), searched the cards for 'retry' and the PR numbers, and grepped for the constant. None gave a reason."

## Wording

- "Because", "the reason is", "was designed to", "fixes" and "the team decided" claim tier 1 or 2. Use them only with a citation next to them.
- "Appears to", "likely", "suggests", "is consistent with" mark tier 3.
- Drop "obviously", "clearly", "of course" and "just". They hide a missing citation.
- When sources disagree, show both with their citations. Don't pick the tidier story.
- Absence of evidence isn't evidence of absence. "Nobody mentioned security" doesn't mean security wasn't a concern.

## Owner and PM rulings

When the exact scope of a human decision matters, find their original words: who, when, where, and enough of the wording to show the scope. "Don't call that endpoint while the migration runs" is a narrower claim than "the owner bans that endpoint". Quote the original, give any broader interpretation on its own line, and mark it as an interpretation.

## Check before you hand it over

1. Is there a citation behind each claim in "what we found"? Any claim without one drops a tier.
2. Does each claim's wording match its tier?
3. Did you cite the code as evidence of its own intent? Remove it.
4. Is "what we don't know" empty? Then either the record was unusually complete, or you missed something. Look again.
