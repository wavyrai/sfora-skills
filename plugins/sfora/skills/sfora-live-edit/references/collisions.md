# When you and a person edit the same block

sfora merges by block, not by character. Two writers in different blocks never collide. When two writers change the same block, nothing is interleaved: one version wins, and the other writer is told. Which case you're in depends on whether the person's edit had been saved yet.

## They saved first: your write gets a 409

A block id is a fingerprint of the block's text. Once their change is saved, your id names nothing, and `sfora put --block` fails with nothing written:

```text
that block is gone — somebody changed it since you read it
`k7f3a2c` is not in this document any more — it was edited, replaced or removed since you read it. …

the document has these blocks now:
  k1q8r3d  line  3  ## Timeline
  k9z2m6w  line  5  We launch on the 14th, after the …

re-aim with: sfora put /projects/hq/docs/launch-plan.md --block <id>
```

The table is the doc as it stands, with each block's id, the line it starts on and its first line of text. Then:

1. Read their version of the block:

   ```bash
   sfora blocks /projects/hq/docs/launch-plan.md --bot claude-code
   ```

2. Decide whether your edit still applies. If it does, write it on top of their text, not in place of it.
3. Write to the new id:

   ```bash
   sfora put /projects/hq/docs/launch-plan.md --block <block-id> block.md --bot claude-code
   ```

Retrying the old id fails the same way every time.

## You saved first: their screen keeps their text

Their editor saves about once a second, so this is the rarer case. It happens when your write lands while they have unsaved typing in the same block:

- Blocks only you changed appear on their screen in place. Their cursor doesn't move.
- In the block you both changed, their own text stays on screen. A note offers yours: "claude-code wrote here", with **Keep mine** and **Take theirs**.
- Their editor then saves what's on screen, so until they pick **Take theirs**, the stored block is their text again.

So after every write, read the blocks again (step 5 of the skill). If your text is gone, someone was typing there. Don't write it again over them. Ask in chat, or wait until they've left the block.

## The whole-doc guard

A whole-doc PUT can carry the doc's revision: `If-Match`, or `?expectedRevision=`, over HTTP (see `http.md`). It fails with 409 if anything in the doc was saved since you read it. That guard suits a whole-doc rewrite. For live editing it's the wrong one, because any keystroke anywhere in the doc trips it. The block id is the guard for one block.

## Never

- Don't run `sfora put <path>` without `--block` while anyone is in the doc. It replaces every block, theirs included. Check `sfora where` first.
- Don't loop a write until it sticks. A 409 or a reverted block means a person is working there.
