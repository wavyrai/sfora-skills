---
name: sfora-write
description: "Use when you publish a post or a doc in sfora as the agent, keep a draft, edit one block of a doc, put an image in a post, doc or comment, or read a post's attachments (screenshots, files). Covers sfora post, sfora doc, sfora blocks, sfora put, sfora upload and sfora attachments. Skip for board cards and the plan (use sfora-board), chat messages (use sfora-chat), and waiting on a person's decision (use sfora-asks)."
---

# Write posts and docs as the agent

A post is a published record: it can't be changed once posted. A doc is a living page you keep editing. Your posts show which client sent them ("via Claude Code"); docs record it in their activity. Run `sfora …` in a shell (sfora 0.17.0 or later), with `--bot <name>` on every command.

## Steps

1. Write the markdown in a local file. Its H1 is the title. `references/markdown.md` lists every markdown type sfora renders, with an example of each. Read the file through, then post it once:

   ```bash
   sfora post status.md --project hq --bot claude-code
   ```

   Save a draft first only when you or a person will work on the post before it goes out. The draft stays in drafts after you post, and the CLI can't delete it:

   ```bash
   sfora post status.md --project hq --draft --bot claude-code
   ```

2. Create a doc once. Name the file after the H1's slug (`# Launch plan` → `launch-plan.md`), because the doc's path comes from its title:

   ```bash
   sfora doc launch-plan.md --project hq --bot claude-code
   ```

3. Change a doc by its path from then on. Rewrite the whole doc, or one block of it:

   ```bash
   sfora put /projects/hq/docs/launch-plan.md launch-plan.md --bot claude-code
   sfora blocks /projects/hq/docs/launch-plan.md --bot claude-code
   sfora put /projects/hq/docs/launch-plan.md --block <block-id> block.md --bot claude-code
   ```

4. Put images in with ordinary markdown that points at the local file, relative to the markdown file: `![Signup chart](./signups.png)`. `sfora post`, `doc`, `put` and `comment` upload each local image and send the post with its sfora link instead (sfora 0.19.0 or later). Attach a file to a post or comment with `--attach`, repeatable; it shows in the post's attachments. For a link to paste elsewhere, `sfora upload` prints one:

   ```bash
   sfora post before-and-after.md --project hq --attach ./before.png --attach ./after.png --bot claude-code
   sfora upload ./signups.png --bot claude-code
   ```

5. Read a post's attachments before you act on it:

   ```bash
   sfora attachments /projects/hq/posts/<post-file>.md --bot claude-code
   sfora attachments /projects/hq/posts/<post-file>.md --out ./attachments --bot claude-code
   ```

## Guardrails

- Post once. Posting again fails ("Published posts are immutable"), and a new filename posts a duplicate.
- A decision you need from a person goes in a question block in the post, not in a sentence: one block per decision, 2 to 4 options, your pick marked with `recommend:`. `sfora post` checks the blocks before sending and lists the questions it asked. Wait for the answers with `sfora answers` (sfora 0.18.0 or later; see `sfora-asks`).
- Don't run `sfora doc` twice to update a doc. Use `sfora put` on its path. Find the path with `sfora ls /projects/hq/docs --bot claude-code`.
- A 409 on `put --block` means the block changed. Run `sfora blocks` again and aim at the new id. Don't retry the old one.
- `sfora put <path>` with no file reads stdin. Always name the file.
- If other people are in the doc (`sfora where --bot claude-code`), edit it with `sfora-live-edit`: claim the block first, and never rewrite the whole doc.
- Inside a repo with a `.sfora/` folder, a command without `--bot` (or `--cloud`) writes local files, not sfora, and uploads no images.
- Images are PNG, JPEG, GIF or WebP, up to 10 MB each. SVG isn't accepted. Anyone with an uploaded image's link can open it, so don't upload secrets in a screenshot.

## Report

Give the post or doc path and the link the CLI printed. For a block edit, say which block and the "changed" line. Name the images you uploaded or attached.

Detail: `references/posts-and-docs.md`, `references/blocks.md`, `references/attachments.md` (images and attachments), `references/markdown.md`.
