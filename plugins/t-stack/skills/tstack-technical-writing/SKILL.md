---
name: tstack-technical-writing
description: "Use when you write or review text other people will act on: a card's evidence, a design note, a briefing post, a doc, a README, a PR body, a commit message, a skill. Pick one mode for the document (tutorial, how-to, reference or explanation), write sentences to the reader in the present tense, one thought per sentence, with nothing that reads two ways, and use the codebase's and sfora's own names. Skip for product UI copy (use the product's copy rules); for cutting AI tells from finished text, also run tstack-unslop."
---

# Write so a tired engineer gets it first time

In the t-stack, text is how work moves between people and agents: the card says what's done, the briefing says what's live, the PR body says what to check. A reader who misreads acts wrongly. Aim for text that an engineer at the end of a long day gets in one pass. Settle four things: the document's mode, the voice you use toward the reader, the load on each sentence, and whether any sentence allows a second meaning.

## Steps

1. **Pick the mode.** One document, one mode (detail in `references/rules.md`):
   - **Tutorial:** someone learning by doing. Every step shows a result.
   - **How-to:** a competent reader with a goal. Steps only, forks allowed.
   - **Reference:** facts to look up. Describe, only describe.
   - **Explanation:** understanding and the reasons. The only mode with opinions.

   A card's evidence and a briefing are reference with a verdict on top; a design note is explanation. Don't mix modes; split and link.
2. **Write to the reader.** "You", present tense. Say who does what. Instructions as commands. The condition before the instruction: "To move a card, change its `column:` line." The common case first.
3. **Load one thought at a time.** One instruction per sentence. Split instructions over about 20 words and other sentences over about 25. Warnings come before the step they guard.
4. **Leave one reading.** Put "only" and "not" right against the word they limit. Make every "it" and "this" point at one thing. No slashes and no em dashes; prefer periods to semicolons. Call each thing by one name throughout.
5. **Use the real names.** The real symbol, file, flag and command, in code font. sfora's own words: card, post, doc, ask, room, board, plan.md. Not "ticket", "channel" or "thread".
6. **Vary the rhythm.** Short sentences land a point; a longer one can carry a fact with its condition. Be specific: not "merging now could cause problems" but "a merge now cancels the running deploy".
7. **Run `tstack-unslop`** over the result.

## Guardrails

- Delete words that carry nothing. Write "to" for "in order to", and drop "it is worth mentioning that" entirely.
- Pick the plain word a colleague would say: "start", not "commence".
- If following a rule here hurts the sentence, rewrite the sentence instead. The reader matters more than the rule.
- Don't invent jargon. If a named pattern is needed, say what it means the first time.
- Lead with the verdict or the plain summary, then the detail. Say plainly when nothing is needed.
- A PR body is a briefing a reviewer reads in a minute. Link the logs and tables; don't paste them.
- A number or a count must hold at the commit that ships it, and must say how it was measured.
- Posts can't be edited. Draft, read it back, then publish (`sfora-write`). A correction is a follow-up post.
- Don't reword unchanged sentences between drafts. Readers re-read what changed.

## Report

The text, and on request a short list of what you changed and why. For a review, quote each problem sentence with its fix.

Detail: `references/rules.md`.
