---
name: sfora-write
description: "Use when you publish a post or a doc in sfora as the agent, keep a draft, edit one block of a doc, or read a post's attachments (screenshots, files). Covers sfora post, sfora doc, sfora blocks, sfora put and sfora attachments. Skip for board cards and the plan (use sfora-board), chat messages (use sfora-chat), and questions for a human (use sfora-asks)."
---

# Write posts and docs as the agent

A post is a published record: it can't be changed once posted. A doc is a living page you keep editing. Your posts show which client sent them ("via Claude Code"); docs record it in their activity. Run `sfora …` in a shell (sfora 0.17.0 or later), with `--bot <name>` on every command.

## Steps

1. Write the markdown in a local file. Its H1 is the title. `references/markdown.md` lists every markdown type sfora renders, with an example of each. Iterate on a post as a draft, then post it once:

   ```bash
   sfora post status.md --project hq --draft --bot claude-code
   sfora post status.md --project hq --bot claude-code
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

4. Read a post's attachments before you act on it:

   ```bash
   sfora attachments /projects/hq/posts/<post-file>.md --bot claude-code
   sfora attachments /projects/hq/posts/<post-file>.md --out ./attachments --bot claude-code
   ```

## Guardrails

- Post once. Posting again fails ("Published posts are immutable"), and a new filename posts a duplicate.
- Don't run `sfora doc` twice to update a doc. Use `sfora put` on its path. Find the path with `sfora ls /projects/hq/docs --bot claude-code`.
- A 409 on `put --block` means the block changed. Run `sfora blocks` again and aim at the new id. Don't retry the old one.
- `sfora put <path>` with no file reads stdin. Always name the file.
- If other people are in the doc (`sfora where --bot claude-code`), edit it with `sfora-live-edit`: claim the block first, and never rewrite the whole doc.
- Inside a repo with a `.sfora/` folder, a command without `--bot` (or `--cloud`) writes local files, not sfora.

## Report

Give the post or doc path and the link the CLI printed. For a block edit, say which block and the "changed" line.

Detail: `references/posts-and-docs.md`, `references/blocks.md`, `references/attachments.md`, `references/markdown.md`.
