---
name: tstack-how
description: "Use when you need to know how some code works before you change it: \"how does X work\", a walkthrough of a subsystem, where a new piece should live, which module owns some state, or whether a change is at the right layer. Explores the code (with parallel read-only subagents for a large subsystem) and returns an explanation a senior engineer can start working from: overview, key concepts, the flow, where things live, gotchas. Skip for why the code has its shape or what decision led to it (use tstack-why)."
---

# Explain how the code works

Before you change a subsystem you need a working model of it: what starts it, where the data goes, who owns what. Naming the file isn't enough. This skill produces that model at the level a senior engineer needs on their first day in the area, not a line-by-line tour.

## Steps

1. **Decide the size.** If the question is vague, state how you read it and go on; the person can redirect you.
   - **Small:** one module, one utility, one function. One pass, no subagents. Go to step 3.
   - **Large:** a subsystem across several files or services, or a whole feature. Go to step 2.
   - When in doubt, treat it as small.
2. **Explore in parallel.** Split the question into two to four angles, each a separate slice (for example: the entry points, the data model, the write path, the background jobs). Start one read-only subagent per angle, all at once, with the brief in `references/explorer-brief.md`. They return facts, not prose.
3. **Write the explanation.** For a small question, explore and write in one pass. For a large one, merge the explorers' findings: where they overlap, combine; where they disagree, open the code and settle it. Use the shape in `references/explanation.md`.
4. **Hand it over.** Give the explanation to the person or put it where the work needs it. When it grounds a design, put it in the design note on the card (`tstack-architect`). When it answers a teammate, a short version goes in the room and the full one on the card.

## Guardrails

- Read the code. Don't infer behaviour from names.
- Say what you couldn't trace: "I couldn't find how X reaches Y" beats a guess. A gap goes in the explanation, not under it.
- Be concrete: "`saveCard` calls `putFile` with the card's path", not "the service delegates to the store".
- Explorers stay read-only and stay on their angle. The writer may check a detail but shouldn't explore again from scratch.
- Keep the person's context small. Explorers do the bulk reading; only their findings come back up.
- A diagram only when the flow crosses several components and prose can't carry it.

## Report

The explanation itself, in the shape from `references/explanation.md`, with the gaps named. Say whether subagents were used and which angles they took.

Detail: `references/explorer-brief.md`, `references/explanation.md`.
