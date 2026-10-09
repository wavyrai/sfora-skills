---
name: tstack-owner-desk
description: "Use when you assist the owner of a sfora software factory and they ask what needs them, what's live, or what's waiting: gather the open asks, the cards handed over for review, and the latest briefing, and answer in plain language, leading with \"Nothing needs you\" when that is true. Covers reading asks.md, the board, each card's @agent_state line (handed over, changes requested, approved, blocked, waiting on a decision, working) and the latest briefing post. Skip for doing the work itself (use tstack-router), for writing the briefing (use tstack-brief), and never use it to answer an ask or approve a card for the owner."
---

# Tell the owner what needs them

The owner is a person, and you are the agent beside them. Your job is the answer to "what needs me?": the questions waiting for them, the cards handed over for their review, and what shipped. You read; you never decide for them. Asks and approvals are theirs to give in sfora.

## Steps

1. Read the open asks. A question with options waits for the owner:

   ```bash
   sfora cat /projects/hq/asks.md --bot claude-code
   ```

2. Read the board, then each In progress card's `**@agent_state:**` line. There is no review column; the line says where the card is:
   - `handed over: READY FOR REVIEW (PR #p, head <sha>).`: waiting for a review, the owner's if it is a design card;
   - `waiting on a decision (see Decision for the PM).`: may be waiting on the owner;
   - `blocked (see BLOCKED #n).`: stuck; say what blocks it;
   - `changes requested (round N, head <sha>).`: back with its lead after a review; not the owner's;
   - `approved (PR #p, head <sha>).`: waiting on the merge; not the owner's;
   - `working.` or `working (round N).`: moving.

   ```bash
   sfora tasks hq --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

   List every card that is handed over, blocked or waiting on a decision. Read its evidence, and its BLOCKED paragraph or Decision section. Note what, if anything, it asks of the owner.

3. Find the latest briefing and read it:

   ```bash
   sfora ls /projects/hq/posts --bot claude-code
   sfora cat /projects/hq/posts/<post-file>.md --bot claude-code
   ```

4. Answer in this order:
   - **One plain paragraph first.** What needs them, in a sentence or two. If nothing does, say "Nothing needs you." and stop there unless they asked for more.
   - **Waiting on you:** each ask, quoted, with the recommended option and why, and the other options. Then each card handed over for their review, and each card waiting on a decision that is theirs, with its number and what it needs.
   - **Live:** what shipped since the last briefing, from the briefing or the cards marked MERGED.
   - **In progress:** one line per card that is moving, and one per blocked card with what blocks it.

5. Tell the owner how to act on each item: answer the ask in sfora, or write "APPROVED #n" (or their changes) on the card. If they tell you their answer, repeat it back and point them to where it goes.

## Guardrails

- Never answer an ask for the owner, and never write "APPROVED #n" on a card. Only the owner's own action in sfora counts.
- Never claim or resolve an owner's ask. `sfora ask claim` is for work up for grabs, not for questions.
- Plain language. No SHAs, run ids or file paths unless the owner asks. Say "the privacy page fix", not "#205's head".
- Don't pad. If nothing needs them, the whole answer can be "Nothing needs you." plus one line on what's live.
- Don't trust a stale briefing for what's waiting: read asks.md and the cards each time. Briefings can be hours old.
- An ask's wording is what the agent asked. If it looks wrong or unclear, say so to the owner; don't rewrite it.

## Report

The answer itself: the plain paragraph, then Waiting on you, Live, In progress. Name every ask and card you mention by its number, so the owner can find it.

Detail: `references/desk.md`.
