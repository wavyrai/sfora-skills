# The owner's desk: detail

## What counts as "needs the owner"

Only three things:

1. **An open ask** in `asks.md` that is a question (it has options). Asks that are work up for grabs are for agents, not the owner.
2. **A card handed over for the owner's review:** its `**@agent_state:**` line starts `handed over: READY FOR REVIEW (PR #p, head <sha>).`, and the card or the mission says the owner reviews it. Design cards always go to the owner ("1/N: design (owner review)"). Most build cards go to the PM as reviewer; mention them under In progress, not Waiting on you.
3. **A card waiting on a decision that is the owner's:** its state line reads `waiting on a decision (see Decision for the PM).`, the decision is one only the owner can make, and the card has no OWNER DECISIONS answer for it yet. The ask is the doorbell; the card is the record.

A production job waiting on the owner's go-ahead is an ask too. The PM brings it with the dry-run counts.

Anything else is not the owner's to do. Don't list it under Waiting on you.

## Reading a card fast

- `**@agent_state:**`, right under the H1, says where it is and who it waits on:
  - `working.` or `working (round N).`: its lead is on it;
  - `handed over: READY FOR REVIEW (PR #p, head <sha>).`: the reviewer;
  - `changes requested (round N, head <sha>).`: the lead, answering a review;
  - `approved (PR #p, head <sha>).`: the merge;
  - `blocked (see BLOCKED #n).`: what the paragraph names;
  - `waiting on a decision (see Decision for the PM).`: the PM, or the owner.

  Only "handed over" (for the owner's review) and "waiting on a decision" (one that is the owner's) can go under Waiting on you. "Changes requested" and "approved" go under In progress, in plain words: "back with its lead after review", "approved, waiting to merge". Blocked cards go under In progress with what blocks them, unless the BLOCKED paragraph says the owner can unblock it.
- If the line still says "handed over" but a CHANGES REQUESTED verdict sits below it, trust the verdict: the card is back with its lead.
- The evidence sections (`## Evidence (role, date)`) say what was proved.
- `APPROVED #n` or `CHANGES REQUESTED #n`, under `## Review (<role>, <date>, round N)`, is the review verdict. The newest round counts.
- `MERGED as …; deploy … OK` says it shipped.
- `OWNER DECISIONS (date)` holds the owner's earlier answers. Don't ask again what is already there.

## An example answer

> Two things need you: the docs-size question, and the t-stack design card to review.
>
> **Waiting on you**
> - Ask: "Raise /docs's ceiling to 309 KB?" The PM recommends raising it, because the extra size is Web Analytics, which you asked for. The other option is to lazy-load Analytics.
> - Card #853, the t-stack design, is ready for you. The preview link is on the card.
>
> **Live:** the privacy page's hosting region now matches the live headers.
>
> **In progress:** #854, the build, is with its lead.

And when nothing is waiting:

> Nothing needs you. Since this morning, the privacy fix went live.

## When the owner answers through you

The owner may say "approve it" or "go with the first option" to you. Repeat it back, then tell them where it goes:

- an ask: open it in sfora and pick the option;
- a card approval: write "APPROVED #n" on the card (or say it in sfora where the PM will record it).

A casual "approved" in a terminal or chat is not a card approval. On this programme the review gate held only because the card was the record.
