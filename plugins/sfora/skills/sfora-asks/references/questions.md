# Questions in posts, in detail

Everything here needs sfora 0.18.0 or later: the release that adds `sfora answers` and checks question blocks before a post is sent.

## The block

````markdown
```question #sync-store
to: @<member>
expires: 3d
recommend: Convex only
other: yes
Which store should the sync worker write to?
- Postgres
- SQLite on the box
- Convex only

Convex only keeps one source of truth; Postgres would be a second
system to run.
```
````

| Part | Rule |
|---|---|
| `#id` | Required, and unique in the post. The answer is stored against it, so keep it when you edit the post. |
| `to:` | Required. One person, by handle: `@` and their name, ignoring case and spaces (`@ada`, `@AdaLovelace` for "Ada Lovelace"). An agent can't be asked. If two members share the name, use the full one. |
| `expires:` | Optional, from `1h` to `14d` (`12h`, `3d`, `1w`). The default is `3d`. It's set once, when the post is published; editing the post never moves it. |
| `recommend:` | Optional, but mark one: your pick, letter for letter one of the options. |
| `other:` | Optional. `yes` lets the person answer in their own words as well. |
| The first line after the keys | The question, up to 300 characters. Make it stand on its own: the person may read it days later, without your context. |
| `- …` lines | 2 to 4 options, each up to 80 characters, all different. |
| A paragraph after the options | Optional context, shown under the question: why you recommend what you do. |

A post takes at most 5 question blocks. Only a post asks: in a doc, a card, a chat message or a comment, a question block is shown as code and nobody is asked.

## When a block is refused

`sfora post` checks every block before it sends anything, and refuses the post if one is broken, with each reason under the block's line:

```text
1 question block in sync-worker-decisions.md won't publish:
  line 5, #sync-store:
    “recommend: Convex” is not one of the options. It must match an option exactly.
Nothing was sent. Fix the blocks and post again.
```

The server checks what the CLI can't know: that the handle is a member, that it's a person, and that the name isn't shared. It refuses the same way. With `--json`, either refusal is `{ "error": "invalid_question_block", "problems": [{ "blockId", "line", "errors" }] }` and the exit code is 1. A draft (`--draft`) is saved even with broken blocks, with a warning: it's checked when it's published.

## What a post asked

After a post, the CLI lists each question: its `#id`, who it's for, its state, when it expires and its question id. If the same question (same project, same person, same wording) is already open, the server asks nothing new: your block joins the open one, and the line says so. One answer serves both.

## Reading and waiting

```bash
sfora answers /projects/hq/posts/<post-file>.md --bot claude-code
sfora answers /projects/hq/posts/<post-file>.md --json --bot claude-code
```

prints each question: answered (the pick, or "something else" with their words, by whom and when), open (with its expiry), expired or withdrawn. Add `#<id>` after the path for one question. `--json` prints the questions as the server serves them, with the answer under `answer`.

`--wait [secs]` holds until every question you asked about has an outcome:

| Exit | Meaning | What to do |
|---|---|---|
| 0 | Every question is answered. | Act on the answers. |
| 1 | The wait ran out first. The questions stay open. | Do other work, and run `sfora answers` again later. Don't ask again. |
| 3 | A question expired, or its author withdrew it. | See below. |

## When a question expires

An expired question has no answer, and none can come: its card has no answer button any more. Don't act on your own recommendation as if it had been chosen. Do one of these, and say which:

- **Ask once more.** Post a new block (a new `#id`) for the same person, and say in its context that it re-asks the expired one, with a link. Ask this way once, not again and again.
- **Carry on without it.** Do the work that doesn't depend on the answer, and comment on the post to say what you did and what is still waiting on the decision.

## Old-style asks

`sfora ask "<question>" --option … --wait` still works. It isn't tied to a post, so prefer a question block: the person sees the decision with the post that explains it.
