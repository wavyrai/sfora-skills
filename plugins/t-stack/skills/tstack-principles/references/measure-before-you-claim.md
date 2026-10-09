# Measure before you claim

**The rule:** a fact you publish (on a page, in a post, in a briefing, on a card the owner will act on) must come from a measurement you made, not from a doc, a config file, a memory or what "everyone knows". If you can't measure it, say it's unverified.

## When it applies

- Any statement about where something runs, how fast it is, what it costs, who can see it or what a vendor does with data.
- Privacy pages, security notes, release notes, briefings for the owner, and replies to customers.
- Any claim that was true once and may have drifted: a region, a plan limit, a dependency's version, a default.

## How it looks in the t-stack

- The claim on the card carries its source: the command or request you ran and the value you saw, with the date.
- The reviewer checks the claim against reality, not against the doc that says it. Docs describe intent; headers and hostnames describe what is.
- A claim that can drift gets a guard: a test or a check that fails when the fact changes, so the page can't quietly become false again.

## The incident that taught it

A privacy page named the region the product was hosted in. Nobody had measured it. A header check showed the request entered at an edge in that region, but the function ran on another continent, and the backend's hostname said the same. The edge alone would have "confirmed" the claim; the full header disproved it. The claim was false, and it was on a public page. The copy was fixed, and a guard test now stops the wrong region from coming back.

## What to do

1. Write the claim down as a sentence someone could check.
2. Find the measurement that would prove it false: a header, a hostname, a DNS answer, a query count, a bill.
3. Take that measurement against production, not a preview or a local build, unless the claim is about those.
4. Publish only what the measurement shows, and put the measurement on the card.
5. If the fact can drift, add a guard so it is checked again without anyone remembering to.

This page is original to the t-stack. It sits next to `references/prove-it-works.md` (is the output real?) and `references/explain-the-number.md` (does the number mean what you say?).
