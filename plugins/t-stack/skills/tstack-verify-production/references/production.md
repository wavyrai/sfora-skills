# Production checks: detail

## Measuring a fact

Ask "what would I see if the claim were false?" and look for exactly that.

| Claim | Measure | Not enough |
|---|---|---|
| Where we host | the header on a real response that names the serving regions (on Vercel, `x-vercel-id` names the edge and the function region), and the backend's hostname and region | the provider's dashboard, a config file, an old doc |
| A style shipped | the computed style of the element on the live page, in a real browser | the CSS in the diff, a screenshot from a headless browser |
| A page works | a real request, and the content of the response (a 200 can be the error boundary) | the status code alone, a green deploy run |
| A limit or quota | the plan page and the current usage, read today | memory, last week's number |

Write each measurement on the card with the command, the raw result and the date. A reader should be able to rerun it.

### Example: a hosting claim from a doc

A privacy page stated where the product was hosted. The claim came from a doc, not a measurement. A header check showed the functions ran in another region, and the backend's hostname was in another country again. The page had to be corrected after it shipped. Hence the rule: no hosting claim ships without the header and the hostname on the card.

## Data jobs: dry → real → dry

The job must be safe to run twice and safe to stop halfway. Before the first run, answer:

1. What happens if this runs twice? (It should change nothing the second time: dedupe keys, upserts, "skip if done" checks.)
2. What happens if it crashes after any chunk? (The next run should pick up where it stopped.)
3. What else does each write trigger? (Rollups, indexes, rebuilds, emails.)
4. What are the plan's limits, and how much is already used today?

Then:

1. **Dry run.** No writes. It prints what it would do: rows to insert, update, skip. Record the counts.
2. **The owner's go-ahead for this job.** One ask, with the counts, the chunk size, the expected time and the headroom left. A yes covers this job only.
3. **The real run,** in chunks, by the owner or by you once the owner has said yes. Watch usage between chunks. Stop if headroom runs short.
4. **Dry run again.** It must report nothing to do. If it finds work, the job isn't idempotent or didn't finish: stop and find out why before anything else runs.

### Example: a backfill that took production down

A backfill of a few thousand rows, plus the rollup rebuild it triggered, hit the backend's plan limit and took production down. Chunks with headroom, and a usage check first, would have kept it up.

## Secrets

- Generate the value straight into a file with mode 600. Don't echo it.
- Pass it to the provider's CLI on stdin, from that file. A value in argv shows in process lists and shell history.
- Never print it, post it, put it on a card or paste it in chat. Report that it was set, not what it is.
- Before you start, work out what the running system sees between the first and the second change. Pick the order where nothing that is serving traffic sees a value it rejects. For a secret shared by a proxy and the backend it calls, where the backend checks it only once its copy is set, that is the proxy first, then the backend.
- Delete the file when both sides are set and checked.
- **The owner's go-ahead, one at a time.** An agent sets a production secret or runs a production data job only after the owner has said yes to that specific one, having seen the dry-run counts first (for a secret: the plan and the order). A yes covers that one step, not the next one. Rotating credentials is always the owner's. Bring the plan and the order in one ask.
- **Is it set?** Ask the provider for its list of names, without values (for example the hosting or backend project's env list). The name being there answers it. If you can't read the list, the next check is a behavioural one: a request whose response differs with and without the secret. When that request is hostile traffic, the owner approves it first (below). Never print a value to check it.

## Hostile-traffic probes

Some fixes are only proved by attacking them: forged headers, bad keys, a burst past a rate limit. Against production, that traffic hits real users. For example, proving that a forged client-address header can't lock out a real API key means sending bad keys with that header, which would lock a real key's bucket for its whole window.

- The default is a non-production deployment that runs the same code.
- Against production, it is the owner's call, under the same rule as a production job: one ask with what you'll send, what it can lock or break, and for how long (`tstack-decide`).
- Put the plan, and later the result, on the card.

## Probes you can trust

A check that finds nothing proves nothing until you've seen it find something. Before you trust a zero, run the probe once where the answer is known to be yes.
