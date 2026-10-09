# Subtract before you add

**The rule:** before building on a system, remove what it doesn't need. Less code shows the real structure and often makes the next step obvious. Leave the design a little simpler than you found it.

## When it applies

Planning a feature, a refactor or a rewrite, writing a skill, and adding a check or a guard.

## How it looks in the t-stack

- **Removal lands first,** as its own commit or card, before the new work builds on top.
- **Build for what was observed,** not for what might happen. No validators, parsers or options beyond what the card asks for.
- **Skills and cards get the same treatment.** Cut a redundant instruction instead of adding a clarifying one. A reference page with nothing new in it gets deleted, not kept as a stub.
- **t-stack skills point to the tool skills** for sfora mechanics instead of teaching them a second time.
- **Follow-ups go in Triage,** not into the current card's scope.

## What to do

1. Before you add, list what you could delete: dead code, unused flags, duplicate checks, stale docs.
2. Delete it, and run the project's tests.
3. Then build the smallest thing that does the job.
4. In review, ask of every new line: does the card need it?
