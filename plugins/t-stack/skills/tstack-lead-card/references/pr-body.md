# The PR body

The card is the record. The PR body is its summary for someone reading the PR: what changed, how it's proved, and what only the owner can do. Write it after the gate, so every number in it is a number you saw at the gated head.

## The order

Gate, push, PR, evidence, handoff. Fetch master once more just before the push; if it moved, merge it and gate again. Push with `git push -u origin <branch>`, then open the PR with `gh pr create --body-file <file>`. The card's evidence comes next, because it cites the PR number and the head SHA.

## The template

With your own values. Prose follows `tstack-unslop`.

```markdown
Card #<n>: <the card's title>.

## What changes
- <one line per behaviour, in the reader's terms>

## Proof
| | origin/master <sha> | head <sha> |
|---|---|---|
| <the defect, as a scenario> | <what master does> | <what the head does> |

- Tests: <n> pass at the head. Fails on master: <test name>, "<the failure, quoted>".
- Mutants: <n> run, <n> killed. <guard broken> → <test>. Catalogue on the card.
- Gate at <sha>, in a git-archive copy: typecheck, build, <n> tests, lint on <n> files, gitleaks on <n> of <n> commits (positive control flagged), generators current.

## Not in this PR
- <what's out of scope, with its follow-up card>

## For the owner
- <a production step that needs the owner's go-ahead, with the exact command and its dry-run counts, or "none">

<the attribution line your environment gives you>
```

## After each review round

A new head makes the body's numbers stale. Bring up to date the head SHA, the test and mutant counts and the gate line, and add one line per round (`Review round 2: <what changed>`). Write the whole body to a file and replace it with `gh pr edit <pr> --body-file <file>`, then read it back:

```bash
gh pr view <pr> --json body,headRefOid
```

Don't move the round's detail into the body. It lives in the card's `## Evidence (lead, <date>, round N)` section, and the body points there.
