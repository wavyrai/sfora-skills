# Risky code and blast radius

Tests show what the author thought of. Reading the risky code shows what they didn't. Some properties can't be tested from outside at all: a test can't prove that no redirect was ever followed.

## Read these yourself

**Authorization**
- Every new route, mutation or query checks who is calling, and which org and project they belong to.
- The check happens on the server, before any read or write, not in the UI.
- An id from the request is never trusted as the tenant: the record's own org is compared with the caller's.

**SSRF and outbound requests**
- Any URL a user can influence is checked before the request: scheme, host, and the resolved address (no private, loopback or link-local ranges).
- Redirects are refused, or each hop is checked again. A check before the first hop is bypassed by a redirect.
- A request that carries a secret in a custom header refuses redirects (`redirect: "manual"`), whoever controls the URL. `fetch` drops `Authorization` on a cross-origin redirect but keeps every custom header, so the secret goes to wherever the redirect points. For example, a server-side check that sends a proxy's shared secret to a backend URL from config follows any redirect that URL answers with, unless the fetch refuses it.
- DNS rebinding: the address checked is the address connected to. Resolving once to check and again to connect lets a hostname switch to an internal address in between.
- Timeouts and size limits on responses.

**Secrets**
- Never in argv (visible in the process list), never in logs, never in error messages or output.
- Read from a file with tight permissions or from stdin.
- Set in the order that never breaks the running system (the receiving side first).

**Privacy**
- What leaves the server: fields in API responses, analytics events, logs, error reports.
- Claims in public text (privacy page, docs) match what is measured. A hosting claim copied from a doc can name the wrong region; a header check and the backend's hostname settle it.

## Beyond the diff

Grep finds the callers. The job is the breakage grep doesn't show:

- **Wire shapes:** a JSON field another client or the CLI parses; a renamed key breaks it silently.
- **Stored data:** a column or document field a job, a backfill or a migration reads.
- **Generated files:** a census or design graph that must be regenerated. Check it is current on the tree that will land, the head merged with current master, by running the generators there (`references/scratch-review.md`, "Current master").
- **Timing:** what runs on teardown, retries, concurrent writes.
- **Deploys:** every merge deploys. A schema change must be additive and optional, or the running version breaks during the deploy.

## Find the one fact

Most risky-looking changes are safe because of one fact ("this only drops entries that are already expired"). Find that fact, then prove it as strongly as you cheaply can, moving down this list:

1. Someone said so. Worth nothing alone.
2. You pointed at the line (`file:line`).
3. You walked the failure case and showed it can't happen.
4. You ran code: a test or a script that calls the real function and fails loudly when the fact is false.
5. You made it happen in the app itself, running.

Say in the verdict where it stopped. "Unproven" is an honest answer, and a CHANGES REQUESTED item when the unproven fact is what keeps a documented property true.

## Clear what you checked

List what you checked and found fine, separately from the problems. The next reviewer and the merger need both.
