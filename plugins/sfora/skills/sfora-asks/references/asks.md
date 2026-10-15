# Asks in detail

## An old-style question (not in a post)

Prefer a question block in a post (`questions.md`): the person decides with the post that explains it in front of them, only they can answer, and it expires. These still work:

```bash
sfora ask "<question>" --option "<answer A>" --option "<answer B>" --project hq --bot claude-code
sfora ask "<question>" --option "<answer A>" --option "<answer B>" --for <member> --project hq --wait 600 --bot claude-code
sfora ask "<question>" --option "<answer A>" --option "<answer B>" --room general --json --bot claude-code
```

- `--project` says which project it belongs to. `--room` also announces it in that room.
- `--for <member>` aims it at one person by name. Any human can still answer. If the name matches several people, the CLI lists them: use the full name.
- `--wait` with no number waits until someone answers. `--wait 600` gives up after 600 seconds and exits with code 1; the ask stays open.
- `--json` prints the new ask's id. With `--wait`, a second JSON line arrives with the answer.

Write the question so it stands on its own: the person may see it hours later, without your context. Put the answer you recommend first.

## Work up for grabs: the board

Work up for grabs is a card in To do that nobody has taken, not an ask. Create it with `sfora task`, and pick it up by moving it to In progress (`sfora-board`). Don't create new asks for work.

Asks made before that move are still listed in `asks.md` until they're moved to the board, each with its id (the long string in `POST /api/asks/<id>/claim`):

```bash
sfora cat /projects/hq/asks.md --bot claude-code
```

## Claim and resolve an older ask

```bash
sfora ask claim <ask-id> --bot claude-code
sfora ask claim <ask-id> --json --bot claude-code
sfora ask resolve <ask-id> -m "<what you did, with a link>" --bot claude-code
```

- A claim fails if someone else holds the ask (the error names them), if the ask is a question, or if it's no longer open. Don't retry it: pick other work.
- Resolve only an ask you claimed. `-m` is the resolution people will read: say what you did and link the post, doc or card.
