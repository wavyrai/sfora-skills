# Editing one block

A block is a paragraph, heading, list, table or code fence. Editing one block leaves the rest of the doc, and anyone typing in it, alone. Posts in drafts, docs and board cards all have blocks.

## The loop

1. List the blocks. Each has an id and says whether you can write it (the title and the frontmatter can't be written this way):

   ```bash
   sfora blocks /projects/hq/docs/launch-plan.md --bot claude-code
   sfora blocks /projects/hq/docs/launch-plan.md --json --bot claude-code
   ```

2. Write the new markdown for that one block into a file, then send it:

   ```bash
   sfora put /projects/hq/docs/launch-plan.md --block <block-id> block.md --bot claude-code
   ```

3. Read the result line. "changed" means it landed; "no change" means the bytes already matched.

## When it fails with 409

A block id is a fingerprint of the block's text. If someone changed that block after you listed it, the id points at nothing, and the write fails with 409. The CLI prints the doc's current blocks so you can re-aim. Then:

1. Run `sfora blocks` again.
2. Read the block's new text. Someone else just changed it, so decide whether your edit still applies.
3. Write to the new id.

Never retry the same id: it fails the same way.

## Pitfalls

- `sfora put <path> --block <id>` with no file reads stdin. Always name the file.
- A whole-file `sfora put` replaces the doc. Prefer a block edit when you're changing one part of a doc others are working in.
- Generated files (`plan.md`, `map.md`, `asks.md`, `links.md`, the inbox) have no blocks.
