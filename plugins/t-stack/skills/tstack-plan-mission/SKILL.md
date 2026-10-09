---
name: tstack-plan-mission
description: "Use when the owner gives you, the PM, a goal that takes more than one card: write the mission card with the owner's words quoted and dated, split the goal into numbered cards that each end in a check (design for the owner's review first, then build, then the PM's verify), put the goal in plan.md, and record later owner decisions on the card. Also use when the goal is one card, often one already in Triage: write a plan section on that card, triage it and name its lead (\"One-card goal\"). Covers the mission, card and one-card plan templates, the /dev mockup on a preview link for any UI change, the docs a change makes false, which production checks need the owner, and OWNER DECISIONS by round-trip. Skip for leading or building the card (use tstack-lead-card) and for the question itself (use tstack-decide)."
---

# Plan a mission as numbered cards

A mission is one card that holds the owner's goal, plus numbered cards that deliver it. Each numbered card ends in a state someone can check, and none starts until the one before it is proved. For a mission, and for any UI or product change, the design card comes first, so the owner sees the shape before anyone wires it. Card commands are in `sfora-board`.

## Steps

1. Quote the owner. Copy their words exactly, with the date: `**Owner (8 Oct):** "…"`. Don't paraphrase the goal; a paraphrase drifts. A goal with no owner words (a follow-up card an agent filed) is quoted from the card: see "One-card goal".

2. Split the goal into cards, in an order that proves itself:
   - **1/N: design (owner review).** The plan, the options and the decisions only the owner can make. Any UI change starts here as a `/dev` mockup on a preview link.
   - **2/N … (N-1)/N: build.** One card per piece a reviewer can check alone. A card that can't name its check is too big or too vague.
   - **N/N: verify (PM).** The PM reviews the build against the design and checks production. A check that sends hostile traffic at production (forged headers, bad keys) is the owner's call: plan it against a non-production deployment, and ask before it touches production (`tstack-decide`).

   Each card says what "done" means as a check: a test that fails on master, a page that renders, a count that matches. List the docs the change makes false (grep `docs/` for the claim), so the lead updates them in the same PR. On #851 a security-controls doc still listed the gap the card closed, and nobody had planned to update it.

3. Write the mission card in `mission.md`, with its H1 "Mission: <goal>": the owner's words, the inputs, the numbered cards and the rules (one lead, at most four teammates on separate files, the lead owns every commit). Create it, then each numbered card from its own file:

   ```bash
   sfora task mission.md --project hq --bot claude-code
   sfora task card-1-design.md --project hq --bot claude-code
   ```

   Title each "<Mission> 1/N: <step>". File follow-ups in Triage, not in the mission.

4. Put the goal in `plan.md`, under `## the goal`, by round-trip:

   ```bash
   sfora cat /projects/hq/plan.md --bot claude-code > plan.md
   sfora put /projects/hq/plan.md plan.md --bot claude-code
   sfora cat /projects/hq/plan.md --bot claude-code
   ```

5. Before the owner sees a design card's preview link, open it yourself in a browser and check it shows what the card says. A link that 404s, or renders the error page with a 200, wastes the owner's review.

6. When the owner decides something later (an answered ask, a comment, a reply on the card), append it to the mission card as `**OWNER DECISIONS (8 Oct)**`, quoted, by round-trip. Never overwrite an earlier decision; add the new one below it:

   ```bash
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code > card.md
   sfora put /projects/hq/board/03-in-progress/<card-file>.md card.md --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

   Read the card again right before the put, and compare the last read with what you wrote, from the H1 down. `lastActivityAt` changes on every put and a trailing blank line is dropped; those are normal. Any other difference means someone wrote in between: read again, merge, and put again. The full rules are in `tstack-handoff` ([its evidence reference](../tstack-handoff/references/evidence.md)).

## One-card goal

The goal is one card, often one already in Triage. You don't write a mission; you plan on the card itself.

1. Read the card in full (`sfora tasks hq --json` gives its path).
2. Quote the goal. If the owner said something, quote it with its date. If not (an agent filed it as a follow-up, as #851 was from #849), quote the card's title, its author and its date, and write "No owner words; the goal is the card's own Done when." If the scope is unclear, that is a question for the owner (`tstack-decide`); otherwise go on.
3. Decide whether it needs a design review. UI or a product change: yes, plan a `/dev` mockup first. Otherwise its "Proposed fix" is the design.
4. Append `## Plan (PM, <date>)` from the template in `references/mission-card.md`: done when, lead, steps, docs to update, what needs the owner, what isn't in this card.
5. In the same round-trip, triage it: set `column: To do` and `assignees: [<lead>]` with the lead's member name. If the lead has no member name of its own (it runs signed in as a person), leave `assignees:` empty and name the lead in the plan.

## Guardrails

- In a mission, and for any UI or product change, the design card comes first. No build card starts before the owner has seen the design. A single card that changes no UI and no product (a backend fix) has its design in its own "Proposed fix"; it needs no owner design review. #851 was planned that way.
- Never wire a UI before the owner has reviewed its `/dev` mockup.
- One lead per mission. The PM plans and reviews; it doesn't build.
- Run `sfora task` once per card. Edit a card afterwards with the round-trip.
- Only `## the goal` in plan.md is yours to write.
- A hostile-traffic probe against production is the owner's call. It can lock out real users (#851's probe would have locked a real key bucket for 15 minutes).
- Put the owner's decisions on the card, not only in chat. The card is the record; chat scrolls away.
- If a step has no check, it isn't a card yet. Split it, or ask what "done" means.

## Report

Give the mission card's number, the numbered cards with their titles and columns, the goal as written in plan.md, and what you need from the owner first (usually: review card 1/N). For a one-card goal: the card, its column and lead, and what needs the owner, if anything.

Detail: `references/mission-card.md` (the mission, numbered-card and one-card plan templates).
