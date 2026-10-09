---
name: tstack-principles
description: "Use when a t-stack skill names a principle, when you're unsure how to weigh speed against proof, or when you want the rule behind a t-stack step: one short page per principle, each tied to the sfora incident that taught it. Covers proof and measurement, when to ask the owner, sequencing, idempotent operations, lessons as structure, root causes, testing, shared state, context, subtraction, numbers, and the coding craft (premises, boundaries, foundations, migrations, reader load, domain models, types). Skip for sfora commands (use the sfora tool skills, such as sfora-board or sfora-asks) and for a role's step-by-step job (use that role's t-stack skill)."
---

# t-stack principles

These are the rules behind the t-stack's protocols. Each page says what the rule is, when it applies, how it looks on a sfora card, the incident from this programme that taught it, and what to do. Most are adapted from pstack's principles; measure-before-you-claim is our own. Read the one page you need, not all of them.

## Steps

1. Find the principle in the index below by the situation you're in.
2. Read its page in `references/`.
3. Apply its "What to do" steps, and name the principle on the card when it changed what you did.

## The index

**Proof and facts**

- **prove-it-works:** before you write "done" or READY FOR REVIEW. Check the real artifact, not a proxy. `references/prove-it-works.md`
- **measure-before-you-claim:** before you publish a fact (hosting, speed, cost, privacy). Measure it in production first. `references/measure-before-you-claim.md`
- **explain-the-number:** before a number goes on a card or into a briefing. Say what limits it. `references/explain-the-number.md`
- **test-behavior-not-implementation:** when you write, review or keep a test. Name the defect it catches and watch it fail. `references/test-behavior-not-implementation.md`

**Running the work**

- **never-block-on-the-human:** when you're about to ask "should I…?". Ask only for product, legal, money, brand or an irreversible step. `references/never-block-on-the-human.md`
- **sequence-verifiable-units:** planning a mission, a card, a sweep or a stack of PRs. Small steps, each checked. `references/sequence-verifiable-units.md`
- **make-operations-idempotent:** backfills, migrations, deploy steps, card and doc writes. Safe to run twice. `references/make-operations-idempotent.md`
- **separate-before-serializing-shared-state:** splitting work among teammates, or a merge queue. Give each writer its own target. `references/separate-before-serializing-shared-state.md`
- **guard-the-context-window:** big logs, many files, a long session. Delegate the reading, keep the summary. `references/guard-the-context-window.md`

**Learning and changing**

- **encode-lessons-in-structure:** the second time you give the same correction. Make it a test or a check. `references/encode-lessons-in-structure.md`
- **fix-root-causes:** debugging. Fix the cause, not the symptom. `references/fix-root-causes.md`
- **attack-the-premise:** after two failed fixes with the same assumption. Test the assumption. `references/attack-the-premise.md`
- **subtract-before-you-add:** before a feature, refactor or new skill. Remove first. `references/subtract-before-you-add.md`

**Coding craft**

- **boundary-discipline:** routes, Convex functions, CLI commands, webhooks. Validate where data enters; trust it inside. `references/boundary-discipline.md`
- **foundational-thinking:** starting a feature or schema. Data shapes and scaffold first. `references/foundational-thinking.md`
- **migrate-callers-then-delete-legacy-apis:** replacing an internal API. Move every caller, delete the old one. `references/migrate-callers-then-delete-legacy-apis.md`
- **minimize-reader-load:** code that's hard to follow. Fewer layers, less state. `references/minimize-reader-load.md`
- **model-the-domain:** branchy or stateful logic. Put the rules in a structure. `references/model-the-domain.md`
- **type-system-discipline:** designing types and signatures. Make illegal states unrepresentable. `references/type-system-discipline.md`

## Guardrails

- A principle explains a step; it doesn't replace one. Follow the role's t-stack skill for the job, and the sfora tool skills for commands.
- When two principles pull against each other (speed and proof, most often), proof wins for anything that ships or touches production.
- Cite a principle by name on the card when it changed a decision, so a reviewer can follow the reasoning.
- If a page doesn't fit your case, say so on the card and file a card in Triage to fix the page.

## Report

Name the principle you applied and what it changed: the check you ran, the ask you didn't send, the step you split.

Detail: `references/prove-it-works.md` and the other pages listed above.
