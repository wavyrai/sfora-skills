# Writing rules, in detail

## The four modes

Two questions pick the mode. Does the text help someone act, or understand? Is the reader learning, or working?

| | Learning | Working |
|---|---|---|
| **Acting** | tutorial | how-to |
| **Understanding** | explanation | reference |

**Tutorial.** You are responsible for the learner getting there. Open with what they will build. Every step produces a result they can see; tell them what it should look like. Keep explanation to one clause and a link. Write "we", in commands: "First, create the card."

**How-to.** Solve a problem a person has. Assume competence and skip the teaching. Steps only: no background, no detours. Forks are fine: "If the card is in Triage, move it first." Name it by the task: "Move a card to Done".

**Reference.** Describe and nothing else: no instructions, no persuasion. Dry, complete and certain. Mirror the shape of the thing described. Generate it from code where you can, so it stays true.

**Explanation.** One topic, readable away from the product. Start from a real "why". Give the history, the constraints and the options that lost. Opinions belong here.

In the t-stack:

- a card's evidence is reference, with the verdict first;
- a briefing is reference with a plain summary first: Live, In progress, Waiting on you;
- a design note is explanation;
- a skill is a how-to.

## Sentences to the reader

- "You", present tense. "Will" only for what really happens later.
- Name the actor: "the CLI writes the file", not "the file is written".
- Instructions are commands: "Run the project's tests." Facts are plain statements.
- The condition comes first: "If the put fails with a 409, read the block again."
- No "please", "simply", "easy" or "quickly" in a procedure.
- Don't announce future work.
- Link text says where the link goes. Never "click here".
- Headings carry the point ("Pick the mode first"), in sentence case. One H1.
- Numbered lists for sequences, bullets otherwise. Introduce a list with a full sentence and keep the items parallel.
- Code and paths in code font. Serial commas. No "etc."

## One thought at a time

- A sentence gives a single instruction, or outside a procedure, makes a single point.
- Split instructions over about 20 words, other sentences over about 25.
- Don't drop articles. "Archive old card" is ambiguous; "archive the old card" is not.
- Give each word one meaning, and each meaning one word. If "claim" means taking an ask, don't also use it for an assertion.
- Avoid "-ing" words where you can.

## One reading only

- Put "only" and "not" right against the word they limit. "Only the check fails on size" and "the check fails only on size" say different things.
- Break up noun strings: "the script that checks the import budget", not "the import budget check script".
- Every "it", "they" and "this" points at one thing. Repeat the noun when in doubt.
- Don't drop verbs: "Card 2 builds the API and card 3 the UI" leaves card 3 without one.
- Keep small structure words: "make sure that the deploy has finished".
- In running prose, a period usually beats a semicolon. Where you'd reach for an em dash, start a new sentence.
- Parentheses hold a full unit. Never "(s)" for plurals.
- No slashes: "a, b, or both".
- One name per thing everywhere. Three names teach three things.
- No idioms or Latin abbreviations.

## An example

Before, on a card:

> The changes have been reviewed and it's important to note that the tests are all passing, which should ensure everything works as expected. Merging should only be performed once deployment has completed.

After:

> The PR's tests pass at head ab12cd3 and fail with master's source swapped in. Merge only after the running master deploy finishes. A merge cancels it.
