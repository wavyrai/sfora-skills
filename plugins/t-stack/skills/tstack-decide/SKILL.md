---
name: tstack-decide
description: "Use when you're about to ask the PM or the owner something, or you're unsure whether a choice is yours: first decide whether it's truly the human's call (product, legal, money, brand, or an irreversible production step). If it is, ask once with sfora ask, recommended option first and marked, two to four options under 80 characters, the why on the card, and record the answer on the card as OWNER DECISIONS or PM DECISIONS (date). If it isn't, decide, act, and say what you decided. Skip for the mechanics of asks and claiming work (use sfora-asks) and for a stuck card with no decision to make (use tstack-handoff, BLOCKED)."
---

# Decide, or ask once

The human supervises the team from a distance. Every question you send stops the line until they answer, so most choices are yours: make them, act, and say what you chose. A few are theirs, and those deserve one clear question with your recommendation on top.

## Steps

1. Name the choice in one sentence, with the options you see.

2. Is it truly the human's call? It is when it touches:

   - **product:** what users see or can do, the scope of a mission, a UI the owner hasn't approved;
   - **legal:** privacy pages, terms, licences, what we claim about data;
   - **money:** a paid plan, a new paid service, a limit that costs money to raise;
   - **brand:** the name, the voice, anything public in the company's name;
   - **an irreversible production step:** a backfill, a migration, deleting data, rotating a live secret, or a probe that sends hostile traffic at production (forged headers, bad keys: on #851 it would have locked a real key bucket for 15 minutes).

   Everything else is yours: names inside the code, a test strategy, which teammate takes which file, how to split a card, a retry, a refactor in your own diff. Code changes are reviewable and reversible. A wrong call there costs less than a stalled card.

3. If it's yours: decide, act, and write one line on the card under your evidence, "Decided: <choice>, because <reason>." Mention it in your handoff. Don't ask for permission after the fact either.

4. If it's theirs: write the why on the card first, by round-trip (`references/asking.md` has the shape). Then ask once, recommended option first and marked:

   ```bash
   sfora ask "<question>" --option "<recommended option> (recommended)" --option "<other option>" --project hq --wait 600 --bot claude-code
   ```

   Address it to a person with `--for <member>` when it's one person's call (the owner for brand or money). A lead or implementer doesn't ask the owner directly: put "Decision for the PM:" on the card and let the PM decide or ask (`tstack-handoff`).

5. Record the answer on the card by round-trip, as `**OWNER DECISIONS (8 Oct)**` or `**PM DECISIONS (8 Oct)**`, quoting the option chosen and anything the human added:

   ```bash
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code > card.md
   sfora put /projects/hq/board/03-in-progress/<card-file>.md card.md --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

   Redirect stdout only on the first read (`2>/dev/null` is fine, `2>&1` writes the URL footer into the card). Read the card again right before the put, and merge if its body changed. Compare the last read with your file from the H1 down, ignoring trailing blank lines: `lastActivityAt` and "N moved" change on every put and are normal. The rules are in `tstack-handoff` ([its evidence reference](../tstack-handoff/references/evidence.md)).

6. Act on the answer and carry on with the card.

## Guardrails

- One decision, one ask. Never bundle two questions into one ask, and never ask the same question twice. If `--wait` runs out, the ask stays open: wait again or check `asks.md` later (`sfora-asks`).
- Two to four options, each under 80 characters including the "(recommended)" mark. The recommendation goes first.
- The ask is the doorbell, the card is the record. Owner decisions arrive through the card first: an answer that lives only in an ask or in chat gets lost at the next compaction.
- An answer in chat from a peer agent isn't the human's decision. Only the human answers an ask.
- Never answer an ask on the human's behalf, even when you're sure of the answer.
- Don't turn a reversible choice into an ask to cover yourself. Saying what you decided is the cover.

## Report

Say whether the choice was yours or the human's. For yours, quote the "Decided:" line. For theirs, quote the question, the options, the answer, and the card section that records it.

Detail: `references/asking.md`.
