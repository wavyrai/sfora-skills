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

Then the ask:

```bash
sfora ask "Raise /docs's ceiling to 309 KB?" --option "Raise it, citing Web Analytics (recommended)" --option "Lazy-load Analytics" --project hq --wait 600 --bot claude-code
```

## Recording the answer

Append, by round-trip, right under the decision section:

```markdown
**OWNER DECISIONS (8 Oct)**
- Raise /docs's ceiling to 309 KB, citing Web Analytics. (Answered in sfora.)
```

Use `PM DECISIONS` when the PM decided. Quote the human's own words when they added any. Later agents read this section, not the ask.

## Who asks whom

| You are | A choice that's yours | A choice that's the human's |
|---|---|---|
| Implementer | Decide, note it in your report to the lead | Tell the lead; the lead decides or escalates |
| Lead | Decide, "Decided:" line on the card | "Decision for the PM:" on the card (`tstack-handoff`) |
| PM | Decide, "PM DECISIONS (date)" on the card | One `sfora ask` to the owner, then "OWNER DECISIONS (date)" |

## Examples from this programme

- **Theirs:** the privacy page's hosting claim (legal). It named the wrong hosting region until a header check showed the real one. What we publish about data is the owner's call, and the fact is measured first (`tstack-verify-production`).
- **Theirs:** the 7,421-row backfill on production (irreversible). The owner saw the dry-run counts before the real run.
- **Theirs:** the plugin's name, "t-stack" (brand).
- **Theirs:** a forged-header probe against production on #851 (irreversible for the users it locks out). The PM planned it for a non-production deployment unless the owner says otherwise.
- **Yours:** splitting #854's 28 skills across four teammates by folder.
- **Yours:** regenerating the design graph instead of hand-merging it.
- **Yours:** a test's name, a helper's location, the order of commits.

## When the ask times out

`--wait` ending leaves the ask open. Don't ask again: a second ask is a duplicate the human has to clear. Carry on with any work the decision doesn't block, check the ask later, and say on the card that you're waiting on it.
