---
name: tstack-unslop
description: "Use on any text before someone else reads it: a card's evidence, a handoff line, a briefing post, a doc, a PR body, a commit message, a skill. Scans for the tells of machine-written text (stock AI words, filler, hedging, em dashes, bold labels, inflated verbs, mannered phrasing, vague claims) and rewrites each sentence to say the plain, specific thing. Keep the meaning and the tone. Skip for code, and for a document's structure and mode (use tstack-technical-writing)."
---

# Cut the tells of machine-written text

The owner reads a lot of agent text: cards, briefings, handoffs. Every stock phrase costs attention and trust, and a sentence that says how something feels instead of what it does gives the reader nothing to act on. This pass finds those sentences and rewrites them plainly, without changing what they mean.

## Steps

1. **Scan** the text for the patterns in `references/patterns.md`. The common ones:
   - stock AI words: delve, robust, seamless, crucial, pivotal, leverage, showcase, landscape, tapestry, testament, underscore;
   - "serves as" or "stands as" for "is";
   - "not just X, but Y", forced groups of three, and synonyms swapped to avoid repeating a word;
   - em dashes, colons used to join clauses, bold labels that repeat the line after them, Title Case headings, decorative emoji;
   - filler ("in order to", "it's worth noting that"), stacked hedges, and closing lines like "the future looks bright";
   - chatbot phrases: "Great question", "Hope that clears it up", "Happy to dig into anything else";
   - abstract metaphor nouns: north star, flywheel, substrate, paradigm.
2. **Rewrite each hit.** Say the mechanism or the number. "The merge queue is now much more robust" becomes "the merge queue checks for a running deploy before each merge". If a sentence can't be restated as a fact, an instruction or a number, cut it.
3. **Test each sentence:** would it fit, word for word, into some other project's text? Then it tells the reader nothing here. Cut it or tie it to this work.
4. **Read it once more, aloud in your head.** Fix sentences you'd have to read twice. Put back articles and verbs that over-compression dropped.
5. **Check it still says the same thing.** The pass changes the wording, never the claims.

## Guardrails

- Keep the meaning and the register. A dry reference stays dry; a briefing stays plain.
- Don't swap one tell for another ("utilize" to "harness").
- Real names are not jargon. Keep the code's and sfora's names: card, post, ask, room, `@agent_state`.
- Short isn't the goal. Clear is. Whole sentences with their articles beat clipped fragments and arrows.
- Do this pass before anything immutable. A sfora post can't be edited; a correction is a second post (`sfora-write`).
- A bold lead-in that names an item and is followed by new detail is fine. A bold label that repeats the sentence is not.

## Report

The rewritten text. On request, the hits by pattern, with each before and after.

Detail: `references/patterns.md`.
