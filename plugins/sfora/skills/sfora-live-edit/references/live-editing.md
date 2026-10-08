# Being in a doc

## What people see

The doc's avatar stack shows everyone in it, people and agents alike, as viewing or editing. Someone who is editing a block gets a marker on that block. Here is how each side gets there:

- **You** show as editing while `sfora watch <doc> --block <id>` runs. Any write to a doc also puts you in it as editing, on the block you wrote. `sfora put` prints "you are visible as editing this document" once to say so.
- **A person** shows as editing for a minute after their last keystroke and as viewing after that. While their editor has focus, they show on the block their caret is in.

Presence is a heartbeat with no history. It lasts 90 seconds after the last beat, and the watch beats on every long-poll (25 seconds by default, `--wait` up to 50). Stop beating and you drop off. No activity row or event records that you were there.

## Who's here

```bash
sfora where --agent claude-code
sfora where --json --agent claude-code
sfora where Ada --agent claude-code
```

`sfora where` prints one sentence per person per doc ("Ada is editing launch-plan.md (block k7f3a2c) — <link>"). It only reads, so asking never puts you in a doc. `--json` prints one record per line, with `path`, `kind` and `block`.

The watch prints the doc's own roster when you join, and again whenever it changes:

```text
here: Ada (editing block k4m9x2p), claude-code [agent] (editing block k7f3a2c)
```

For machine-readable output, add `--json`. Each roster change is then a `{"type":"presence", …}` line, and write pings are lines of their own:

```bash
sfora watch /projects/hq/docs/launch-plan.md --block <block-id> --json --agent claude-code
```

## Claiming a block

- `--block` works on docs (`/projects/<slug>/docs/<file>.md`) only. Posts and board cards have no avatar stack, so the watch refuses them.
- If the id names no block, the watch refuses to start and tells you to run `sfora blocks`.
- If the block is edited through the CLI or API while you watch (your own `put --block`, or another agent's), its id changes and the server moves your claim to the new id. The watch follows it and prints "following block <old> → <new>" (on stderr; the roster line, or the `{"type":"presence"}` record under `--json`, shows the new block). Take the new id from that line or from `sfora blocks` for your next `put`.
- If the block is removed, or a person changes it in the app (the app's saves don't move claims), the watch warns once that the block "is gone" and keeps you in the doc with no block. Treat that as a sign someone is working there: run `sfora blocks`, read the block again, and start a watch on a current id.
- A whole-doc `sfora put <path> <file.md>` clears your block claim. The watch claims its id again on the next beat, which works if that block's text didn't change.
- The title and the frontmatter are listed as read-only blocks. You can't claim or write them this way.

## Writing a block

- The file holds the block's markdown only, with no frontmatter and no `# Title`. Trailing newlines are trimmed.
- It may hold more than one block. Replacing a paragraph with a heading and a list is a normal edit, and the blocks around it keep their ids.
- An empty file is refused (422). To delete a block, you have to rewrite the whole doc without it, so do that only when nobody else is in the doc.
- `sfora put <path> --block <id> -` reads the block from stdin instead of a file, so you can pipe it in. With no file and no `-`, it also reads stdin, so always name one or the other.
- The result line says "changed" (and how many block ids were kept) or "no change" (your bytes matched what was there).
