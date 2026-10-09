---
name: tstack-brief
description: "Use when the owner asks for a briefing or a mission reaches a milestone, and you, the PM, write where things stand: one plain paragraph first, then Live, In progress and Waiting on you (each ask linked, with your recommendation), and \"Nothing needs you\" when that is true. Covers sfora post --draft, reading it back, publishing once, correcting with a follow-up post, and a running dashboard kept as one doc updated with sfora put. Skip for card-level evidence (use tstack-handoff or the card round-trip) and for answering the owner's \"what needs me?\" in chat (use tstack-owner-desk)."
---

# Brief the owner

A briefing tells the owner, in a minute of reading, what shipped, what's moving and what waits on them. It is written for a person who wasn't watching: plain words, no SHAs, every claim one you checked. A briefing is a post, and posts can't be edited, so you draft it, read it, and publish it once.

## Steps

1. Gather the facts from sfora, not from memory: the board, the open asks, and each card that changed since the last briefing (its MERGED line, its verdict, its `@agent_state`):

   ```bash
   sfora tasks hq --bot claude-code
   sfora cat /projects/hq/asks.md --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

   For anything you'll call "live", check it is: a deploy run for the merge, and the live page (`tstack-verify-production`).

2. Write `briefing.md`. H1 "Briefing: <date>". Then:
   - **One plain paragraph.** The answer to "where are we?" in two to four sentences. Lead with what the owner must do, or with "Nothing needs you."
   - **## Waiting on you.** Each ask quoted, linked, with your recommendation and why. Each card handed over for the owner's review. If there is none, write "Nothing needs you." here too.
   - **## Live.** What shipped, in the owner's words for it, with the card number.
   - **## In progress.** One line per card that is moving, and what it waits on. Read that from the state line: "changes requested" is with its lead, "approved" waits on the merge (`references/briefing.md` has the plain words for each state).

3. Save it as a draft, and read the draft through as the owner would:

   ```bash
   sfora post briefing.md --project hq --draft --bot claude-code
   ```

   Check each claim against step 1. Cut anything the owner can't act on or doesn't need.

4. Publish it once, then read the published post back:

   ```bash
   sfora post briefing.md --project hq --bot claude-code
   sfora cat /projects/hq/posts/<post-file>.md --bot claude-code
   ```

5. If the published post is wrong, write a short correction as a new post that names the briefing and the fix. Never repost the briefing:

   ```bash
   sfora post briefing-correction.md --project hq --bot claude-code
   ```

6. If the owner wants a dashboard they can open at any time, keep one doc. Create it once, then update it in place by its path:

   ```bash
   sfora doc tstack-dashboard.md --project hq --bot claude-code
   sfora put /projects/hq/docs/tstack-dashboard.md tstack-dashboard.md --bot claude-code
   ```

## Guardrails

- The plain paragraph comes first. If the owner reads only that, they know what to do.
- Say "Nothing needs you" when it's true, and don't invent a question to fill the section.
- Only checked facts. "Live" means you saw it live, not that the PR merged.
- Posts are immutable. Draft, read, then publish once. A mistake gets a follow-up post, never a second copy of the briefing.
- `sfora doc` always creates a new doc. Run it once per dashboard; every later update is `sfora put` on its path. On 7 Oct a second `sfora doc` made a duplicate doc instead of an update.
- No SHAs, run ids, file paths or jargon in the paragraph. Card numbers are fine.
- One briefing per request or milestone. Don't post a briefing to show you're busy.

## Report

The briefing's link and its opening paragraph, and whether anything is waiting on the owner.

Detail: `references/briefing.md`.
