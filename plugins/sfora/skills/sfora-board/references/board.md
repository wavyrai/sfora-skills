# The board in detail

## Where cards live

```bash
sfora ls /projects/hq/board --agent claude-code
sfora ls /projects/hq/board/03-in-progress --agent claude-code
sfora tasks hq --json --agent claude-code
```

The four columns are fixed: `01-triage`, `02-todo`, `03-in-progress`, `04-done`. Their display names are Triage, To do, In progress and Done. A card's file is its number and title slug, such as `0012-fix-login.md`.

## A card file

```markdown
---
column: In progress
status: active
priority: high
labels: [auth]
assignees: [claude-code]
blocked-by: [9]
---

# Fix login

What's wrong, what done looks like, and links to the evidence.
```

- `column:` moves the card. Crossing into Done closes it; leaving Done reopens it.
- `status:` is `drafted`, `active` or `closed`.
- `priority:` is `none`, `low`, `medium`, `high` or `urgent`.
- `assignees:` are member names. A name that matches nobody is dropped.
- `blocked-by:` takes card numbers, not titles.
- `sfora cat` on a card prints more frontmatter (id, number, dates). Putting it back unchanged is fine.

## Moving a card

1. `sfora cat` the card into a local file.
2. Change only the `column:` line.
3. `sfora put` it back to the path you read it from. sfora finds the card by its number, so the old column in the path is fine.

```bash
sfora cat /projects/hq/board/02-todo/<card-file>.md --agent claude-code > card.md
sfora put /projects/hq/board/02-todo/<card-file>.md card.md --agent claude-code
```

To change just the description, edit one block instead (see sfora-write). A block edit never moves the card.

```bash
sfora blocks /projects/hq/board/03-in-progress/<card-file>.md --agent claude-code
sfora put /projects/hq/board/03-in-progress/<card-file>.md --block <block-id> block.md --agent claude-code
```

## Keeping it moving

- Pick up a card by moving it to In progress and assigning yourself, in one put.
- Say what changed in the card's body as you go, so a human can follow without asking.
- Close a card by moving it to Done. If it's no longer needed, say so in the body first.
- Check the board after every change with `sfora tasks`.
