---
name: sfora-live-edit
description: "Use when you edit a sfora doc that people may have open right now: join it as a live editor, claim one block, edit it while humans keep typing in the rest, and handle a collision when someone changes the same block. Covers sfora where, sfora blocks, sfora watch --block and sfora put --block. Skip for publishing a new post or doc (use sfora-write), board cards (use sfora-board) and chat (use sfora-chat)."
---

# Edit a doc live, alongside people

You can be in a doc while people are in it: you show in its avatar stack, on the block you're editing, and you change one block at a time while they keep typing everywhere else. Presence and edits are per block, not per character. You have no cursor. A block is one top-level piece of the markdown: a paragraph, a heading, a whole list, a table, a code fence or a quote. One list item is not a block; the list it sits in is. Run `sfora …` in a shell (sfora 0.17.0 or later), with `--bot <name>` on every command.

## Steps

1. See who's in the doc, and on which block:

   ```bash
   sfora where --bot claude-code
   ```

2. List the blocks and pick the one you'll change:

   ```bash
   sfora blocks /projects/hq/docs/launch-plan.md --bot claude-code
   ```

3. Claim it before you write. Run this in the background and leave it running. It shows you as editing that block, prints who else is here (again whenever that changes) and leaves when you stop it (see Guardrails for how):

   ```bash
   sfora watch /projects/hq/docs/launch-plan.md --block <block-id> --bot claude-code
   ```

4. Write the block's new markdown in a file and send only that block. The rest of the doc isn't touched:

   ```bash
   sfora put /projects/hq/docs/launch-plan.md --block <block-id> block.md --bot claude-code
   ```

5. Check that your text is there. The block now has a new id, because ids come from the text. `sfora blocks` shows the new id at once; use it for your next `put`. The watch follows the block by itself and, within about 5 seconds, prints "following block <old> → <new>" on stderr. Don't wait for that line:

   ```bash
   sfora blocks /projects/hq/docs/launch-plan.md --bot claude-code
   ```

   Restart the watch only if it says the block "is gone". That means it was removed, or a person changed it in the app. Read it again with `sfora blocks` before you touch it, then watch a current id.

6. When you're done, stop the watch and say what changed where the people are:

   ```bash
   sfora chat general -m "<message>" --bot claude-code
   ```

## Guardrails

- Don't write a block someone else is on. If `here:` or `sfora where` shows them on your block, pick another one or ask.
- A 409 ("that block is gone") means someone changed the block after you read it. Nothing was written. Read the block again, fold your change into their text, and write to the new id. Never retry the old id.
- While anyone is in the doc, never run `sfora put <path>` without `--block`. It replaces the whole doc.
- If a person has unsaved typing in the same block, their screen keeps their text and offers yours as "Keep mine / Take theirs", and their next save can put their text back. That's why step 5 checks.
- Keep each edit to one block and a few sentences.
- Stop the watch gently, so it leaves the avatar stack at once: stop the background task in your harness, or send that one process `kill -INT <pid>` (`kill <pid>` works too). Note the pid when you start it, for example `$!` after a `&`. Never use `pkill -f`: the pattern can match your own shell and kill it. A hard kill (`kill -9`) leaves you in the avatar stack for up to 90 seconds.

## Report

Name the doc and each block you changed (old and new id), say who else was in the doc, and quote what you said in chat.

Detail: `references/live-editing.md`, `references/collisions.md`, `references/http.md`. The markdown a block can hold, and what a person's edit in the rich editor keeps: `../sfora-write/references/markdown.md`.
