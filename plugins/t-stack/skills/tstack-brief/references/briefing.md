# Briefings: detail

## The shape

```markdown
# Briefing: 8 Oct, evening

One thing needs you: the docs-size question. The privacy page's hosting region is now measured, and the t-stack build is with its lead.

## Waiting on you
- **"Raise /docs's ceiling to 309 KB?"** I recommend raising it: the extra size is Web Analytics, which you asked for. The other option is to lazy-load Analytics. (link to the ask)

## Live
- The privacy page's hosting region now matches the live headers.

## In progress
- #854, the t-stack build: the lead is integrating; review next.
```

When nothing is waiting:

```markdown
# Briefing: 9 Oct, morning

Nothing needs you. Two cards shipped overnight and the build card is in review.

## Waiting on you
Nothing needs you.

## Live
- …

## In progress
- …
```

## Writing it

- **Write for someone who wasn't there.** The owner didn't watch the rooms. Name what changed for a user or for them, not what the agents did.
- **One paragraph, two to four sentences, first.** It answers "what do I need to do, and where are we?". The sections are the detail.
- **Recommend.** Each ask under Waiting on you carries your recommended answer and the reason in one sentence. The owner can still pick another.
- **Plain words.** "The privacy page fix", not "#205's head". "The docs got bigger because of Analytics", not "bundle delta".
- **Short.** If a section has nothing, say so in one line.

## Checking before you publish

For each line, know where it came from:

| Section | Source |
|---|---|
| Waiting on you | `asks.md`, and cards whose `@agent_state` line starts `handed over:` and wait for the owner's review, and cards `waiting on a decision` that is the owner's |
| Live | the card's MERGED line, the deploy run, and your own look at the live site |
| In progress | the card's `@agent_state` line and its last evidence or review |

A line you can't source comes out.

The state line, in the owner's words:

| `@agent_state` | Say | Section |
|---|---|---|
| `working.` / `working (round N).` | "with its lead" | In progress |
| `handed over: READY FOR REVIEW (…)` | "in review" (or "ready for you", if the owner reviews it) | In progress, or Waiting on you |
| `changes requested (round N, …)` | "back with its lead after review" | In progress |
| `approved (…)` | "approved, waiting to merge" | In progress |
| `blocked (…)` | what blocks it | In progress, or Waiting on you if only the owner can unblock it |
| `waiting on a decision (…)` | the decision | Waiting on you, if it's the owner's |

A card's line may lag its newest verdict. If a CHANGES REQUESTED sits below a "handed over" line, the card is back with its lead.

## Posts are immutable

A published post can't be edited, and posting the same file again fails. A new filename posts a duplicate. So:

1. Draft (`sfora post … --draft`) and read the draft.
2. Publish once.
3. Read the published post.
4. If something is wrong, post a short correction: what was wrong, what is right, and the briefing it corrects.

## A dashboard instead of posts

Some owners want one page that is always current. That is a doc, not a post:

- create it once with `sfora doc`, from a file named after its title's slug;
- update it with `sfora put` on its path, as often as you like;
- if people are reading it while you write, edit block by block with `sfora-live-edit`.

`sfora doc` always creates a new doc. On 7 Oct a second `sfora doc` run made a duplicate doc instead of updating the first. Find the path with `sfora ls /projects/hq/docs`, and use `put`.
