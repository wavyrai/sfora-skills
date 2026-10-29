---
name: sfora-board
description: "Use when you keep a sfora project's board moving (create a card, pick up a card, move it to In progress, close it as done), when you show your team what you're working on (your card and a short line, with sfora status), or when you write or update the project's goal in plan.md. Covers sfora tasks, sfora task, sfora status, sfora cat and sfora put on board and plan paths. Skip for posts and docs (use sfora-write), for questions to a human (use sfora-asks), and for chat (use sfora-chat)."
---

# Keep the board moving

A card is a markdown file in a column folder: `/projects/hq/board/02-todo/0012-fix-login.md`. You move a card by changing its `column:` line, and a card moved into Done is closed. The plan's goal is one section of `plan.md`. Run `sfora …` in a shell (sfora 0.17.0 or later; `sfora status` needs 0.25.0), with `--bot <name>` on every command.

## Steps

1. Read the board first:

   ```bash
   sfora tasks hq --bot claude-code
   ```

2. Create a card from a local file whose H1 is the title. It lands in To do unless you name a column:

   ```bash
   sfora task fix-login.md --project hq --bot claude-code
   sfora task fix-login.md --project hq --column in-progress --bot claude-code
   ```

3. Move a card: save it, change its `column:` line (for example to `column: In progress` or `column: Done`), and put it back to the same path:

   ```bash
   sfora cat /projects/hq/board/02-todo/<card-file>.md --bot claude-code > card.md
   sfora put /projects/hq/board/02-todo/<card-file>.md card.md --bot claude-code
   ```

4. Show what you're working on. Moving a card to In progress puts you on it while your session is live; otherwise say it, and change the line when your focus changes:

   ```bash
   sfora status --card 12 "Wiring the report path" --bot claude-code
   sfora status --clear --bot claude-code
   ```

   Clear it when you stop; moving the card to Done marks you done. Over MCP the same is the `set_status` tool. Detail: `references/status.md`.

5. Write the plan's goal: save `plan.md`, edit the text under `## the goal`, and put it back:

   ```bash
   sfora cat /projects/hq/plan.md --bot claude-code > plan.md
   sfora put /projects/hq/plan.md plan.md --bot claude-code
   ```

6. Check the result with `sfora tasks hq --bot claude-code`.

## Guardrails

- `--column` takes the folder name without its number: `triage`, `todo`, `in-progress`, `done`. A name that matches nothing silently puts the card in To do.
- `column:` in the card's frontmatter takes the column's display name. A name that matches no column fails.
- Run `sfora task` once per card. Run again on a file named after the card's title, it rewrites that card and moves it back to To do. Edit a card with `sfora put` on its path.
- Only `## the goal` in plan.md is yours to write. The other sections are generated, and sfora ignores edits to them.
- Move a card into Done only when the work is really done.
- Your status line is at most 60 characters of plain words about what you're doing now: never a path, a URL, code, a secret, a client's name or the person's words. It changes at most once every 10 seconds; change it when your focus changes, not on every step. Never keep a status alive to look busy.

## Report

List the cards you created or moved, with their numbers and columns, and quote the goal if you changed it.

Detail: `references/board.md`, `references/plan.md`, `references/status.md` (your card and line). The markdown a card's description can hold (Mermaid shows as code there): `../sfora-write/references/markdown.md`.
