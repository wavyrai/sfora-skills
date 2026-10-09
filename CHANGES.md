# CHANGES

One `## <version> - <title>` entry per release, newest first. The first heading always matches `VERSION`, because plugin installs update only when the version goes up.

## 0.3.3 - every markdown type, explained

- **A markdown reference:** `sfora-write` gains a reference, `plugins/sfora/skills/sfora-write/references/markdown.md`. It lists every markdown type sfora renders, from GFM basics to callouts, math, footnotes, mentions, links to cards and docs, embeds and the structured blocks (`status`, `board`, `chat`, `sheet`, `map`). Each type has a minimal example, the places it renders (doc, post, chat, card) and its limits, such as Mermaid showing as code in a card and footnotes not surviving a person's edit in the rich editor. It also lists the Mermaid diagram types sfora draws and the ones it doesn't.
- `sfora-write`, `sfora-live-edit`, `sfora-chat` and `sfora-board` link to it.
- A packet test renders every example through sfora's real renderer, on each surface, and fails when the reader gains a type the reference doesn't cover.

## 0.3.2 - a better t-stack intro

- The README's t-stack section now says what the t-stack is for: how Thijs Verreck runs his company with agent teams, packaged so your agents can work the same way. Its first line points anyone unsure where to start at `tstack-router`.
- NOTICE.md keeps the credit factual: the t-stack is named after Thijs Verreck, in the spirit of pstack by Poteto (Lauren Tan).

## 0.3.1 - README image and CLI install line

- **The README opens with a picture** of the skills at work, in light and dark (`assets/readme-light.png`, `assets/readme-dark.png`, shown with a `prefers-color-scheme` `<picture>`).
- **The install section adds the sfora CLI:** `npx sfora-cli@latest skills packet install --harness claude-code` (or `codex`, or `agents`), with sfora-cli 0.17.0 or later. It installs the `sfora` plugin's skills that the CLI ships with; install the t-stack with the lines above.

## 0.3.0 - t-stack

The second plugin is renamed from `sfora-factory` to `t-stack`, named after Thijs Verreck the way pstack is named after Poteto (Lauren Tan).
- **The plugin:** `sfora-factory` is now `t-stack`. Install it as `t-stack@sfora-skills`; uninstall `sfora-factory@sfora-skills` if you had it.
- **The skills:** every `factory-*` skill is now `tstack-*` (for example `factory-review` is `tstack-review`), and the router `factory` is now `tstack-router`.
- **Fixed from a dogfood run (#855):** one real card, #851, went through the t-stack with nothing else to go on, over five review rounds. The 121 gaps it logged are fixed:
  - **Mutants:** one exhaustive pass per guard: a catalogue of operators, bounds, operands, returns and early exits, plus per-bit and per-case weakenings for a secret comparison. Equivalent mutants are proved by code.
  - **Review:** a re-review procedure, a check against current master with `git merge-tree`, and verdicts that name the assertion and keep to real defects.
  - **Leads:** answering a verdict round by round; resuming with a fetch and a SHA check; a moved master taken in by a merge commit; and the gate's generators, gitleaks counts and load.
  - **The card:** its state line through a review loop (`changes requested`, `approved`, and what a round is), and the card round-trip (what a read-back may differ in).
  - **The router:** rows for a one-card goal, a card sent back, and a re-review; placeholders (`hq`, `--bot`); and what to do with no mission room.
  - **Planning:** one-card plans, and which production probes are the owner's call.
- **Before going public (#856):** the skills need sfora 0.17.0 or later (`npx sfora-cli@latest` when it isn't installed) and use the documented `--bot <name>`. Production secrets and data jobs need the owner's go-ahead for each one, after the dry-run counts; rotating credentials is always the owner's. The merge queue reads the head SHA's check runs instead of `gh pr checks`. Examples from our own security and hosting history are now generic.

The `sfora` plugin's skills now use `--bot <name>` and name sfora 0.17.0 or later; nothing else in them changed.

## 0.2.0 - sfora factory

A second plugin, `sfora-factory`: skills for running a software factory on sfora.
- **The router and twelve role skills:** the owner's assistant; the PM (plan a mission, the merge queue, verify production, brief the owner); the lead; the implementer; the reviewer; and the shared decide, watch, handoff and retro.
- **`factory-principles`:** nineteen short principles.
- **Fourteen coding-craft skills.**

The craft skills and principles are adapted from pstack under the MIT licence; `NOTICE.md` lists each file. No Cursor team-kit skill is included. The `sfora` plugin is unchanged apart from its version.

## 0.1.0 - the first eight skills

The first release: `sfora-setup`, `sfora-write`, `sfora-live-edit`, `sfora-board`, `sfora-chat`, `sfora-asks`, `sfora-skills` and `sfora-troubleshoot`. They cover editing a doc block, editing a doc live alongside people (block-level presence with `sfora watch --block`, and what happens when a person edits the same block), posting as the agent with its client named, joining a room and replying, showing typing, claiming an ask, reading attachments, keeping the board moving, writing the plan's goal and working with a project's skills. Every command they teach was checked against the sfora CLI. `sfora ask claim`, `sfora ask resolve`, `sfora watch --block` and `sfora skills packet install` need sfora-cli 0.17.0 or later.
