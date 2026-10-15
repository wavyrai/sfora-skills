# Asking well

## The card section before the ask

Write the why on the card first, so the human can read the context in one place. Keep it short:

```markdown
## Decision for the owner (PM, 8 Oct)

/docs's first-load JS is 309 KB against a 300 KB ceiling. The extra 9 KB is Web Analytics.

1. **Raise the ceiling to 309 KB, citing Web Analytics (recommended).** No user-facing change; the number is explained.
2. **Lazy-load Analytics.** Keeps the ceiling, loses the first page view in the stats.

Asked in sfora on 8 Oct.
```

Then the question, in a post (`# Docs ceiling decision`, so `docs-ceiling-decision.md`), and the wait for its answer (sfora 0.18.0 or later):

````markdown
```question #docs-ceiling
to: @<member>
recommend: Raise it, citing Web Analytics
Raise /docs's ceiling to 309 KB?
- Raise it, citing Web Analytics
- Lazy-load Analytics
```
````

```bash
sfora post docs-ceiling-decision.md --project hq --bot claude-code
sfora answers /projects/hq/posts/docs-ceiling-decision.md --wait 600 --bot claude-code
```

## Recording the answer

Append, by round-trip, right under the decision section:

```markdown
**OWNER DECISIONS (8 Oct)**
- Raise /docs's ceiling to 309 KB, citing Web Analytics. (Answered on the decision post.)
```

Use `PM DECISIONS` when the PM decided. Quote the human's own words when they added any. Later agents read this section, not the post.

## Who asks whom

| You are | A choice that's yours | A choice that's the human's |
|---|---|---|
| Implementer | Decide, note it in your report to the lead | Tell the lead; the lead decides or escalates |
| Lead | Decide, "Decided:" line on the card | "Decision for the PM:" on the card (`tstack-handoff`) |
| PM | Decide, "PM DECISIONS (date)" on the card | One question block to the owner in a post, then "OWNER DECISIONS (date)" |

## Examples from this programme

- **Theirs:** the privacy page's hosting claim (legal). It named the wrong hosting region until a header check showed the real one. What we publish about data is the owner's call, and the fact is measured first (`tstack-verify-production`).
- **Theirs:** the 7,421-row backfill on production (irreversible). The owner saw the dry-run counts before the real run.
- **Theirs:** the plugin's name, "t-stack" (brand).
- **Theirs:** a forged-header probe against production on #851 (irreversible for the users it locks out). The PM planned it for a non-production deployment unless the owner says otherwise.
- **Yours:** splitting #854's 28 skills across four teammates by folder.
- **Yours:** regenerating the design graph instead of hand-merging it.
- **Yours:** a test's name, a helper's location, the order of commits.

## When the wait runs out, or the question expires

`sfora answers --wait` exiting 1 leaves the question open. Don't ask again: the server would join a second block to the open question anyway. Carry on with any work the decision doesn't block, run `sfora answers` later, and say on the card that you're waiting on it.

Exit 3 means the question expired (after 3 days unless `expires:` said otherwise) or was withdrawn. No answer will come. Ask once more in a new block that links the expired one, or carry on without the decision and say so on the card. Never act on your own recommendation as if it had been chosen.
