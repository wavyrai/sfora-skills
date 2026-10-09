# Mission and card templates

## The mission card

```markdown
# Mission: the t-stack

**Owner (8 Oct):** "<the owner's words, copied exactly>"

**Room:** <room>

## Inputs
- The PM playbook doc.
- #845's tool skills.

## Cards
1. #853 The t-stack 1/4: design (owner review)
2. #854 The t-stack 2/4: build
3. #855 The t-stack 3/4: dogfood
4. #856 The t-stack 4/4: verify (PM)

## Rules
- One lead. At most four teammates, each on separate files. The lead owns every commit.
- Each card hands over with READY FOR REVIEW #n (PR #p, head <sha>) and stops.
- Only APPROVED #n on the card is an approval.
```

When the owner decides something, append below the rules:

```markdown
**OWNER DECISIONS (8 Oct)**
- The name: "t-stack"; plugin `t-stack`.
- pstack's coding skills are adapted into ours, not installed beside them.
```

## A numbered card

```markdown
# The t-stack 2/4: build

Part of the mission card. Read it first.

## What
Build the plugin from the approved design on 1/4.

## Done when
- Every sfora command in the skills runs in the harness against convex-test.
- The packet test passes with the approved skill names.

## Not in this card
- The dogfood run (3/4).
```

"Done when" is the check. If you can't write it, the card isn't ready.

The `**Room:**` line names the mission room, so every agent finds it from the card. A card with no mission card has no room; `tstack-router`'s "No mission room" section says what stands in for it.

## A plan on a one-card goal

Append it to the card itself, by round-trip, in the same put that triages it (`column: To do`, `assignees: [<lead>]`):

```markdown
## Plan (PM, 8 Oct)

**Goal:** the card's own Done when. "<the card's title>". No owner words: <author> filed it on <date>, from #849's open item.

**Done when**
- A test that fails on master and passes at the head: <name it>.
- <any other check>.

**Lead:** <member name, or "a lead agent" when it has none>. No design review: the Proposed fix is the design (no UI, no product change).

**Steps**
1. The lead builds the fix and its tests (`tstack-lead-card`).
2. Review, then merge (`tstack-review`, `tstack-merge-queue`).
3. Verify on production, if the change is observable there (`tstack-verify-production`).

**Docs to update:** <a doc and the sentence this card makes false, e.g. a security-controls doc that lists this gap as "not covered">, or "none".

**Needs the owner**
- Is the secret the fix depends on set in production? (Checked by name only, or by a probe the owner approves.)
- A forged-header probe against production: owner's go-ahead, or run it on a non-production deployment.

**Not in this card**
- <follow-ups, filed in Triage>.
```

"Docs to update" can be "none found", with the grep you ran. On #851 that line was missing, and the lead found the stale doc only after a gate had started.

## Why design first

The rule covers a mission and any UI or product change. A backend fix on one card, with no UI and no product change, has its design in its own "Proposed fix", and the owner doesn't review it.

Every UI change in this programme started as a `/dev` mockup the owner opened on a preview link, before any wiring. Changing a mockup costs minutes. Changing a wired feature after the owner sees it costs a card.

Check the link yourself first, in a real browser. A preview can return 200 and still show the error page, and a preview server can time out between when you post the link and when the owner opens it.

## Ordering that proves itself

Order the cards so each one checks the one before it:

- the design before the build;
- a guard test before the fix it guards (it fails on master, then passes);
- a removal before a rebuild on the simpler base;
- the PM's verify card last, so the mission ends on proof, not on "merged".

A break caught on the card that caused it is cheap to find. A break found after three more cards were built on top of it is not.

## When the goal changes mid-mission

Quote the new words on the mission card with their date, under OWNER DECISIONS. Then change the cards that haven't started. Don't rewrite a card that is in progress without telling its lead in the mission room (or on the card and the PR, when there is no room).
