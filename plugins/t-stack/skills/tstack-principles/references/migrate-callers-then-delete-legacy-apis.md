# Migrate callers, then delete the legacy API

**The rule:** when a new internal API replaces an old one, move every caller and delete the old one in the same wave. Don't keep both paths alive because some callers haven't moved.

## When it applies

A refactor that introduces a new function, module, component or route, where nothing outside the codebase depends on the old one.

## How it looks in the t-stack

- **The card lists every caller** of the old API, found by search, not memory.
- **One card (or one stacked series) migrates them all** and deletes the old API. A temporary adapter is the exception, with a follow-up card in Triage and a date.
- **Tests move to the new contract.** Tests that only protect the old implementation are deleted with it.
- **Public surfaces are different.** The sfora CLI, the HTTP API and the skills packet have users outside the repo. Those change with a version bump and a note in CHANGES, not a silent delete.
- **Every merge deploys,** so a removal that touches stored data or the schema stays additive until the last reader is gone.

## What to do

1. Search for every caller and list them on the card.
2. Migrate them, run the project's tests, then delete the old API in the same PR or the next one in the stack.
3. Confirm with a search that no caller is left.
