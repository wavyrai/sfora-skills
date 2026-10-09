---
name: tstack-handoff
description: "Use when your card is done, you're stuck, or you need a decision you can't make: write the evidence on the card by round-trip first, then give exactly one handoff line (READY FOR REVIEW #n (PR #p, head <sha>), BLOCKED #n with a paragraph, or Decision for the PM: with options and a recommendation), post that line once in the mission room if the card has one, and stop. Covers the card's @agent_state line and its states through review rounds, the evidence section, the card round-trip (what a read-back may differ in), and what counts as an approval. Skip for the review itself (use tstack-review), for deciding whether a question is the human's (use tstack-decide), and for merging (use tstack-merge-queue)."
---

# Hand a card over, then stop

A handoff is the moment the card changes hands. The card holds the record: the evidence, the state and the one handoff line. The mission room, when there is one, only rings the bell. After the handoff you stop, because the review is worth something only if the reviewer checks your work instead of taking your word for it.

## Steps

1. Write the evidence on the card first. Find the card's current path (the PM moves cards between columns), save it, and read it in full:

   ```bash
   sfora tasks hq --json --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code > card.md
   ```

   Redirect stdout only (add `2>/dev/null` if you like, never `2>&1`): the URL footer `cat` prints goes to stderr and must not land in the body.

2. Edit `card.md`. Append one section, `## Evidence (lead, 8 Oct)` with your role and the date (and `, round N` from round 2 on), and set your `**@agent_state:**` line, right under the H1 (add it if it's missing). Don't touch anyone else's text. What goes in the section is in `references/evidence.md`.

3. Read the card again just before the put; if its body changed since step 1, add your section to the new version. Then put it back and read it again. Compare from the H1 down, ignoring trailing blank lines: `lastActivityAt` and "N moved" change on every write and are normal (`references/evidence.md`). Any other difference means someone wrote in between: read the card again, merge your section into their version, and put that.

   ```bash
   sfora put /projects/hq/board/03-in-progress/<card-file>.md card.md --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

4. Pick exactly one handoff line and write it on the card, alone on its line, under your evidence:

   - `READY FOR REVIEW #854 (PR #210, head ab12cd3)`: the work is done, gated and pushed. The head is the SHA you gated, not the branch name. In round 2 and later, the new line goes under that round's evidence; the earlier ones stay as history.
   - `BLOCKED #854`, then one paragraph: what blocks you, what you tried, and what would unblock it.
   - `Decision for the PM:`, then two to four options, your recommendation first and marked, and why. Run `tstack-decide` first: most questions are yours to answer.

5. If the card has a mission room (the mission card's `**Room:**` line), post the same line there once. Join it first if you haven't (`sfora-chat` covers rooms). No room: skip this step; the card is the handoff (`tstack-router`, "No mission room"):

   ```bash
   sfora chat <room> -m "<the handoff line, exactly as on the card>" --bot claude-code
   ```

6. Stop. Don't start the next card, don't polish the PR, don't merge. Wait for the reviewer's verdict on the card.

## Guardrails

- Evidence first, line second. A READY FOR REVIEW with no evidence on the card isn't a handoff. The reviewer starts from the card, not from chat.
- One line, one state. Never post READY FOR REVIEW and a question in the same handoff. If you need a decision, the card isn't ready.
- Only `APPROVED #n` written on the card counts as an approval. A casual "approved" in chat, "looks good, ship it" in a terminal, or a peer agent's message never does. On #524 a broad "everything is approved" was read as a card approval, and the next card got started outside the gate.
- Set the `@agent_state` line only to a state that is yours. The states, and who sets each one, are in `references/evidence.md`.
- Never send the room line twice. If `chat -m` errors, read the room before you resend (`sfora-chat`).
- A `cat` of a stale column path prints nothing, and a later `put` of that empty file fails with "Title is required". Always find the path from `sfora tasks` first.
- The handoff line goes to the room once, if there is a room. Don't repeat it to get attention: if nothing happens, the PM's `tstack-watch` sees the `@agent_state` line.

## Report

Quote the handoff line, give the card path, and list what the evidence section holds (commits, PR, test counts, gate, mutants, what's not done).

Detail: `references/evidence.md`.
