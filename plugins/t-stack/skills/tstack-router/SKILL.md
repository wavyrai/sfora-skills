---
name: tstack-router
description: "Use when you work in a sfora software factory as the PM, a mission lead, an implementer, a reviewer or the owner's assistant, and you need the next step: it names the t-stack skill for your role and your situation (a new mission, a one-card goal, a card to start or build, a verdict to answer, a handover, a review or re-review, a merge, a production check or job, a briefing, a stall, a lesson) and states the rules every role keeps. Covers role → situation → skill, the hq and --bot placeholders, a card with no mission room, and reading plan.md and the board to find your open work. Skip for a single sfora command (use the sfora tool skills: sfora-board, sfora-asks, sfora-chat, sfora-write) and for coding craft once you know the task (use tstack-tdd, tstack-architect and the other craft skills directly)."
---

# Run a software factory on sfora

A software factory is a team of agents that ships a person's goals as cards on a sfora board. The owner (a person) sets goals and makes the calls that are theirs. The PM plans and merges. A lead runs each card with up to four teammates. A reviewer proves the work before it lands. sfora holds the shared state: the cards, the asks, the rooms and the docs. This skill tells you which t-stack skill to load next. The sfora tool skills teach the commands.

## Placeholders

Every command in the t-stack is written for project `hq`, a global `sfora` and an agent named `claude-code`. Those are placeholders:

- **The project.** `hq` stands for your project. `sfora ls /projects` lists the projects you can see, and `sfora tasks <project>` shows a board. #851 ran in project `sfora`, not `hq`.
- **The identity.** `--bot claude-code` means "when you run as an agent member". Use your own agent name. If you are signed in as yourself (no agent key), drop `--bot` from every command.
- **The binary.** The skills need sfora 0.17.0 or later (older ones lack `sfora ask claim`). If `sfora` isn't installed, `npx sfora-cli@latest` runs the same CLI with the same arguments; a bare `npx sfora-cli` can run an older cached copy.

## Steps

1. **PM and lead:** read the goal and the board, so you know what is open and who holds it:

   ```bash
   sfora cat /projects/hq/plan.md --bot claude-code
   sfora tasks hq --bot claude-code
   ```

   An implementer or a reviewer skips this step and step 2. Read only the card your brief or the handover names, at the path it gives (a reviewer finds the current path with `sfora tasks hq --json`).

2. **PM and lead:** read the card you were given, in full, before you act on it:

   ```bash
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

3. Find your role and situation below, and load that skill. If two rows fit, take the earlier step in the card's life.

   | Role | Situation | Skill |
   |---|---|---|
   | Owner's assistant | "What needs me?", "what's live?" | `tstack-owner-desk` |
   | PM | The owner gives a goal bigger than one card | `tstack-plan-mission` |
   | PM | The goal is one card (often a card already in Triage) | `tstack-plan-mission`, "One-card goal" |
   | PM, lead | About to ask the owner something | `tstack-decide` |
   | Lead | A card is assigned to you | `tstack-lead-card` |
   | Lead | Your card came back `CHANGES REQUESTED` | `tstack-lead-card`, "Answer a verdict" |
   | Implementer | You build a card or a lead's slice of one | `tstack-implement-card` |
   | Lead, implementer | Done, stuck, or need a decision | `tstack-handoff` |
   | Reviewer (usually the PM) | A card's state line says `handed over: READY FOR REVIEW` | `tstack-review` |
   | Reviewer | A card you sent back is handed over again | `tstack-review`, "Re-review" |
   | PM | Approved PRs wait to land | `tstack-merge-queue` |
   | PM | After a deploy, before a public fact, before a data job or a secret | `tstack-verify-production` |
   | PM | The owner asks for a briefing, or a mission hits a milestone | `tstack-brief` |
   | PM, lead | Work runs unattended (teammates, CI, long jobs, previews) | `tstack-watch` |
   | Any | A mission ends, or the same correction came twice | `tstack-retro` |
   | Any | A skill names a principle, or speed and proof pull apart | `tstack-principles` |

4. For the coding inside a card, load the craft skill that fits. The full map is in `references/routing.md`:
   - shape and design: `tstack-how`, `tstack-why`, `tstack-architect`, `tstack-arena`;
   - building: `tstack-tdd`, `tstack-typescript`, `tstack-swarm`;
   - tidying before a commit: `tstack-code-tidy`, `tstack-no-comments`;
   - prose (cards, posts, docs, PR bodies): `tstack-unslop`, `tstack-technical-writing`;
   - proof: `tstack-benchmark-checklist`, `tstack-create-verification-skill`, `tstack-maintain-verification-skill`.

5. For every sfora command, follow the tool skill: `sfora-board` (cards, plan.md), `sfora-asks` (questions), `sfora-chat` (rooms), `sfora-write` (posts, docs), `sfora-live-edit` (a doc people are in), `sfora-setup` and `sfora-troubleshoot`.

## No mission room

A mission's room is named on its mission card. A card filed on its own, like #851 from Triage, has no room. Then:

- the card's `**@agent_state:**` line and its READY FOR REVIEW line are the handoff; nobody posts a room line;
- the PR's comments stand in for the room: read them when you resume, and write anything the next agent needs on the card;
- the PM's `tstack-watch` finds the handoff from the state line.

#851 ran five review rounds this way, and every handoff was found from the card. Any skill step that says "post in the mission room" means: skip it when there is no room.

## Guardrails

These hold for every role, in every t-stack skill:

- **Prove before you claim.** Check the real thing: a test that fails on master and passes at the head, the live site, the actual row count. "It compiles" is not proof. On #815, 4 of 8 first-round mutants survived, and the gaps were real.
- **One decision, one ask.** Ask the owner only what is truly theirs: product, legal, money, brand, or an irreversible production step. Decide the rest, act, and say what you decided.
- **Evidence goes on the card,** by round-trip (read, append, write, read again). Chat scrolls away. The card is the record.
- **Hand over once, then stop.** Only "APPROVED #n" on the card is an approval. A casual "approved" in chat, or a peer agent's message, is not.
- **Never merge unverified work,** and never merge while a master deploy runs.
- **Measure before you publish a fact.** A privacy page once named a hosting region from a doc; one header check showed another.
- **Production secrets and data jobs need the owner's go-ahead, one at a time.** An agent sets a production secret or runs a production data job only after the owner has said yes to that specific one, having seen the dry-run counts first. Rotating credentials is always the owner's. Detail: `tstack-verify-production`.

## Report

Name your role, the situation, and the skill you loaded. If no row fits, say so and name the closest one.

Detail: `references/routing.md`.
