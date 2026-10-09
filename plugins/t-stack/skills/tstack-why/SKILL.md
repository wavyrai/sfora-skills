---
name: tstack-why
description: "Use when you need to know why code has its shape before you change or remove it: \"why do we do it this way\", why a limit or threshold is that number, why an odd guard exists, what decision or incident led to it, or what a regression broke. Anchors on the code and its git history, searches the PRs, the cards (where owner decisions are quoted and dated), posts, docs, rooms and any other sources you can reach, and returns a cited answer that separates what the record says from what you infer. Skip for how the code works at runtime (use tstack-how)."
---

# Find out why the code is the way it is

Code shows what it does, never why. The why lives in commit messages, PR threads, the cards, the owner's decisions and the rooms, and all of them are partial. This skill finds that record and reports it honestly: what someone wrote down, what the evidence points to, what you're guessing, and what nobody recorded.

## Steps

1. **Pin the question.** Name the target (a function, a constant, a guard, a pattern) and the question about it. If it's vague, state your reading and go on.
2. **Anchor in the code and its history.** Get the file, the lines, the symbols, the last commits that touched them, and the PR numbers in their messages:

   ```bash
   git log --oneline -20 -- "<path to the file>"
   git log -S "<the exact text>" --oneline -- "<path to the file>"
   git show <sha>
   gh pr view <pr> --comments
   ```

3. **Search the team's record.** In a sfora project, decisions sit on the cards: the owner's words quoted and dated, "OWNER DECISIONS (date)" and "PM DECISIONS (date)" sections, review verdicts. Find the cards that mention the PR or the feature, and read them in full (`sfora-board`):

   ```bash
   sfora tasks hq --bot claude-code
   sfora cat /projects/hq/board/04-done/<card-file>.md --bot claude-code
   ```

   Then the posts and docs (`sfora-write`), and the mission room's history (`sfora-chat`).
4. **Search the other sources you can reach,** one subagent per source, all at once: the issue tracker, design docs, team chat, error tracking, logs and metrics, analytics. Add the incident angle when the code looks defensive (a retry, a timeout, a rate limit, a flag, a guard). Brief: `references/sources.md`.
5. **Write the answer** with every claim in a confidence tier from `references/confidence.md`: what we found (cited), what we can reasonably infer, competing explanations, what we don't know, and the sources searched, one line each, including the ones that came back empty.
6. **If a change follows,** end with constraints: what to keep, what's free to change, what to avoid, and the risk. Put them in the design note on the card.

## Guardrails

- The code is not evidence of its own intent. "It's named `retryOnce`" says nothing about why.
- The newest commit isn't the whole story. Shapes build up over many changes. Trace back.
- The owner's ruling counts only in the owner's own words, with the date and where they said it. An agent's summary of it is labelled as a summary, and five agents repeating it are still one source.
- Don't confirm the asker's guess just because they offered it. Test it like any other explanation.
- "We searched A, B and C for X and found nothing" is a real result. Never fill a gap with a confident guess; someone will act on it.
- A claim about the real world gets measured, not quoted. A privacy page named a hosting region because a doc said so. A header check showed another region, and the page had to be fixed.

## Report

The answer, with sections: the question, the code (path and lines), what we found, what we infer, competing explanations, what we don't know, sources searched (one line each, empty ones too), and the constraints if a change follows.

Detail: `references/confidence.md`, `references/sources.md`.
