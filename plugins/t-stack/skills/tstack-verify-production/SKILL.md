---
name: tstack-verify-production
description: "Use after a deploy, before you publish a fact about production (hosting region, a header, a style, a limit), before a production data job (backfill, rebuild, migration), or before setting a production secret: measure the real thing and cite the measurement on the card; run data jobs dry → real → dry, in chunks, with plan-limit headroom, and only after the owner's go-ahead for that job; keep secrets in a 600-mode file passed on stdin, set in the order that never breaks the running system; check a secret is set by its name only, never its value; leave hostile-traffic probes against production to the owner. Covers live headers and hostnames, computed styles on the live page, dry-run counts, the owner's go-ahead ask. Skip for reviewing a PR (use tstack-review) and for merging (use tstack-merge-queue)."
---

# Verify production before you claim it

Production is the only place a production fact is true. Docs, configs and memory describe what someone meant; the live site, its headers and its rows say what is. Measure first, write the claim second, and cite the measurement. For data jobs and secrets, assume the run can crash halfway and must be safe to run again.

## Steps

1. **Name the fact and how you'll measure it** before you look. "We host in the EU" is checked by the response headers and the backend's hostname, not by the provider's settings page.

2. **Measure the real thing.** Read the response headers of a real request (for example `curl -sI` on the live page: `x-vercel-id` names the regions that served it), the backend's hostname and region, the live page's computed styles in a browser, or a real request and its response. For visual changes, check under the owner's real conditions: a GPU-compositing bug (square corners from `backdrop-filter`) was invisible in headless browsers twice.

3. **Put the measurement on the card,** by round-trip, as `## Evidence (PM, date)`: what you ran, what it returned, and what it proves:

   ```bash
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code > card.md
   sfora put /projects/hq/board/03-in-progress/<card-file>.md card.md --bot claude-code
   sfora cat /projects/hq/board/03-in-progress/<card-file>.md --bot claude-code
   ```

   A public claim (a privacy page, a docs page) cites that evidence. If the measurement contradicts what's published, the fix is a new card.

   A probe that sends hostile traffic at production (forged headers, bad keys, a flood) is the owner's call, not yours: it can lock out real users. A lockout check sends bad keys under a forged client address, and against production that locks a real key out for the limiter's window. Run it against a non-production deployment, or ask the owner first (`tstack-decide`).

4. **Before a data job,** check the plan's limits and today's usage. Size the job and its side effects (a backfill also triggers rollups and rebuilds). Plan chunks that leave headroom.

5. **Dry run first.** The implementer writes the job with a dry-run mode and dedupe keys, so a second run changes nothing. Run it dry and record the counts on the card.

6. **Ask the owner** for the real run of this job, with the counts and the plan. **The owner's go-ahead, one at a time.** An agent sets a production secret or runs a production data job only after the owner has said yes to that specific one, having seen the dry-run counts first (for a secret: the plan and the order). A yes covers that one step, not the next one. Rotating credentials is always the owner's.

   ```bash
   sfora ask "<question>" --option "<run it in chunks (recommended)>" --option "Not yet" --project hq --wait 600 --bot claude-code
   ```

7. **After the real run,** run it dry again. It must find nothing left to do; that proves the job is idempotent and finished. Record the before, real and after counts on the card.

8. **Secrets:** generate the value into a file only you can read (mode 600), and pass it to the provider's CLI on stdin. Never put it in argv, a log, a card, a chat or your output. Set it in the order that never breaks the running system: for a secret shared by a proxy and the backend behind it, usually the proxy first. Work the order out before you start, write it on the card, and set it only after the owner's go-ahead for that secret (step 6's rule).

9. **Is a secret set?** Check by name only: the provider's env list, which shows names without values. If you can't read that list, the check is a behavioural probe the owner approves, or a question to the owner. Never read a value to find out. A fix that only works once a secret is set is not live until the secret is: if you can't tell, that is a question for the owner.

## Guardrails

- Never publish a production fact you haven't measured this session. A privacy page once named the wrong hosting region; one header and one hostname would have shown it.
- Never set a production secret or run a production data job without the owner's go-ahead for that specific one, given after they saw the dry-run counts. Rotating credentials is always the owner's.
- Never run a bulk job in one go. A backfill plus the rollup rebuild it triggered once hit the backend's plan limit and took production down.
- A secret never appears in argv, output or sfora. If one leaks, say so at once: rotating it is the owner's. Checking a secret means checking its name is set, never reading its value.
- Hostile-traffic probes against production are the owner's call.
- Measure the real artifact, not a proxy: a 200 can still be the error page, and a deploy run can be green while the change isn't live.
- A suspicious reading may be the probe's fault. Check the probe on a case where you know the answer before you trust a zero.

## Report

The fact, the measurement (command and result), and where it's recorded on the card. For a data job: the dry, real and dry counts, and who ran each. For a secret: that it was set, where, and in what order, never its value.

Detail: `references/production.md`.
