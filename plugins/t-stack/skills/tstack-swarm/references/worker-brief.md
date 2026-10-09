# The worker's brief, and the report

## The brief

Fill this in for each worker. It must make sense to someone who has seen nothing else.

```markdown
**Goal:** what the swarm as a whole is finding out, in one sentence.
**Your slice:** exactly which files, commits, options or angle are yours.
**Where:** the worktree or folder you may write in (or "read-only").
**Commits:** the exact SHAs you check. Record them in your result.
**Method:** how to verify or measure: the steps, the run count, what one run is, the order.
**Return:** PASS, ISSUES or BLOCKED on the first line, then the evidence.
  - ISSUES: every defect you can prove, one per line, with file and line or the failing output.
  - BLOCKED: what blocked you and what you tried.
**Don't:** commit, merge, deploy, post in sfora, or touch anything outside your slice.
```

## The table on the card

Append it as `## Swarm (role, date)`:

```markdown
## Swarm (lead, 8 Oct)

**Question:** (an example) do the docs, /dev and settings pages render without console errors at head ab12cd3?
**Shape:** 4 slices; read-only workers.

| Slice | Verdict | Evidence |
|---|---|---|
| /docs pages 1–8 | PASS | 8 screenshots, 0 console errors |
| /docs pages 9–16 | ISSUES | /docs/asks: hydration warning (log line) |
| /dev gallery | PASS | 7 screenshots |
| /settings | BLOCKED | needs a signed-in session; none in the brief |

**Issues:** /docs/asks logs a hydration warning on load.
**Gaps:** /settings not covered; rerun with a session.
```

## The room line

One line: the verdict and where the detail is. "Swarm on #854: 2 PASS, 1 ISSUES, 1 BLOCKED; table on the card."
