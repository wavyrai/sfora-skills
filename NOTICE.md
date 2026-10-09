# Notice

## pstack

- **Project:** pstack by Lauren Tan (https://github.com/cursor/plugins/tree/main/pstack), via the pstack-claude port by Michael Denyer (https://github.com/michael-denyer/pstack-claude), commit `6a2d5e0` (v0.9.76).
- **Licence:** MIT (full text below).
- **Adapted, not installed.** These skills follow pstack's structure and rules: one `SKILL.md` per folder with only `name` and a "Use when… / Skip when…" `description`, short skills with detail in `references/`, and one skills tree with a thin manifest per harness, versioned from a root `VERSION` with a `CHANGES.md` entry per release. The text is sfora's own. No pstack skill, hook, script or tool is included.

```text
MIT License

Copyright (c) 2026 Lauren Tan
Copyright (c) 2026 Michael Denyer

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## pstack: the t-stack plugin

The t-stack is named after Thijs Verreck, the way pstack is named after Poteto (Lauren Tan).

- **Project:** pstack by Lauren Tan (https://github.com/cursor/plugins/tree/main/pstack), via the pstack-claude port by Michael Denyer (https://github.com/michael-denyer/pstack-claude), commit `6a2d5e0` (v0.9.76).
- **Licence:** MIT (the full text above; Copyright (c) 2026 Lauren Tan, Copyright (c) 2026 Michael Denyer).
- **Adapted, rewritten:** on the owner's decision (card #853, O2), the `t-stack` plugin adapts pstack's coding skills and principles into skills of its own. The ideas and structure come from the files below; the text is sfora's own, tied to sfora's cards, asks, rooms and docs. No pstack skill, hook, script, agent or tool ships as is.
- **No Cursor team-kit skill** is included or adapted: not `deslop`, `thermo-nuclear-code-quality-review`, `make-pr-easy-to-review`, `fix-ci`, `fix-merge-conflicts`, `get-pr-comments` or `what-did-i-get-done`.

Each t-stack skill, and the pstack files it adapts (paths in the pstack-claude repository), or "original":

| Skill | Adapted from |
| --- | --- |
| `tstack-router` | `plugins/pstack/skills/poteto-mode/SKILL.md` (routing a situation to a playbook) |
| `tstack-owner-desk` | original |
| `tstack-plan-mission` | `plugins/pstack/skills/principle-sequence-verifiable-units/SKILL.md` |
| `tstack-merge-queue` | `plugins/pstack/skills/babysit/SKILL.md`, `plugins/pstack/skills/poteto-mode/playbooks/shipping.md` |
| `tstack-verify-production` | `plugins/pstack/skills/principle-prove-it-works/SKILL.md`, `plugins/pstack/skills/principle-make-operations-idempotent/SKILL.md` |
| `tstack-brief` | original (not derived from any Cursor team-kit skill) |
| `tstack-lead-card` | `plugins/pstack/skills/recall/SKILL.md`, `plugins/pstack/skills/principle-separate-before-serializing-shared-state/SKILL.md` |
| `tstack-implement-card` | `plugins/pstack/skills/tdd/SKILL.md`, `plugins/pstack/skills/principle-test-behavior-not-implementation/SKILL.md` |
| `tstack-review` | `plugins/pstack/skills/interrogate/SKILL.md`, `plugins/pstack/skills/blast-radius/SKILL.md`, `plugins/pstack/skills/poteto-mode/playbooks/shipping.md` |
| `tstack-decide` | `plugins/pstack/skills/principle-never-block-on-the-human/SKILL.md` |
| `tstack-watch` | `plugins/pstack/skills/babysit/SKILL.md` |
| `tstack-handoff` | `plugins/pstack/skills/show-me-your-work/SKILL.md` |
| `tstack-retro` | `plugins/pstack/skills/reflect/SKILL.md`, `plugins/pstack/skills/correct/SKILL.md`, `plugins/pstack/skills/principle-encode-lessons-in-structure/SKILL.md` |
| `tstack-principles` | the index and `measure-before-you-claim` are original; each other page adapts the `plugins/pstack/skills/principle-<name>/SKILL.md` of the same name: `plugins/pstack/skills/principle-prove-it-works/SKILL.md`, `plugins/pstack/skills/principle-never-block-on-the-human/SKILL.md`, `plugins/pstack/skills/principle-sequence-verifiable-units/SKILL.md`, `plugins/pstack/skills/principle-make-operations-idempotent/SKILL.md`, `plugins/pstack/skills/principle-encode-lessons-in-structure/SKILL.md`, `plugins/pstack/skills/principle-fix-root-causes/SKILL.md`, `plugins/pstack/skills/principle-test-behavior-not-implementation/SKILL.md`, `plugins/pstack/skills/principle-separate-before-serializing-shared-state/SKILL.md`, `plugins/pstack/skills/principle-guard-the-context-window/SKILL.md`, `plugins/pstack/skills/principle-subtract-before-you-add/SKILL.md`, `plugins/pstack/skills/principle-explain-the-number/SKILL.md`, `plugins/pstack/skills/principle-attack-the-premise/SKILL.md`, `plugins/pstack/skills/principle-boundary-discipline/SKILL.md`, `plugins/pstack/skills/principle-foundational-thinking/SKILL.md`, `plugins/pstack/skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md`, `plugins/pstack/skills/principle-minimize-reader-load/SKILL.md`, `plugins/pstack/skills/principle-model-the-domain/SKILL.md`, `plugins/pstack/skills/principle-type-system-discipline/SKILL.md` |
| `tstack-architect` | `plugins/pstack/skills/architect/SKILL.md`, `plugins/pstack/skills/architect/references/design-red-flags.md`, `plugins/pstack/skills/architect/references/rationale-template.md`, `plugins/pstack/skills/architect/references/runner-prompt.md` |
| `tstack-arena` | `plugins/pstack/skills/arena/SKILL.md` |
| `tstack-benchmark-checklist` | `plugins/pstack/skills/benchmark-checklist/SKILL.md` |
| `tstack-code-tidy` | original (in place of the excluded Cursor team-kit `deslop`, which was not consulted) |
| `tstack-create-verification-skill` | `plugins/pstack/skills/create-verification-skill/SKILL.md`, `plugins/pstack/skills/create-verification-skill/references/feature-map-example/README.md` |
| `tstack-maintain-verification-skill` | `plugins/pstack/skills/maintain-verification-skill/SKILL.md` |
| `tstack-how` | `plugins/pstack/skills/how/SKILL.md`, `plugins/pstack/skills/how/references/explorer-prompt.md`, `plugins/pstack/skills/how/references/explainer-prompt.md` |
| `tstack-why` | `plugins/pstack/skills/why/SKILL.md`, `plugins/pstack/skills/why/references/epistemics.md`, `plugins/pstack/skills/why/references/source-playbook.md`, `plugins/pstack/skills/why/references/sources/code-archaeology.md` |
| `tstack-no-comments` | `plugins/pstack/skills/no-comments/SKILL.md`, and `plugins/pstack/agents/comment-sicko.md` (the idea only) |
| `tstack-swarm` | `plugins/pstack/skills/swarm/SKILL.md` |
| `tstack-tdd` | `plugins/pstack/skills/tdd/SKILL.md` |
| `tstack-technical-writing` | `plugins/pstack/skills/technical-writing/SKILL.md` |
| `tstack-typescript` | `plugins/pstack/skills/typescript-best-practices/SKILL.md`, `plugins/pstack/skills/typescript-best-practices/references/patterns.md` |
| `tstack-unslop` | `plugins/pstack/skills/unslop/SKILL.md` |
