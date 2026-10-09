# Teammates

## Separate first

Before you split the work, list every file each piece will write. If two pieces write the same file, change the split until they don't: give the file to one teammate, or keep it yourself and write it after they finish. A shared file with a "please be careful" rule is still a race. Instructions aren't concurrency control.

Things that look separate and aren't:

- **Generated files.** A census or design graph changes whenever its inputs do, and adding any file can be enough. Nobody edits them. The lead runs every generator once, after integrating.
- **Index files and manifests.** A barrel `index.ts`, a marketplace JSON, a routes list. One owner, usually the lead.
- **The card.** Teammates report to the lead; the lead writes the card.
- **Git state.** Teammates commit locally on the branch you name. Only the lead switches branches, merges, reshapes commits or pushes.

When one shared writer is a real need, make it structural: one owner, or one phase after another. Not a convention.

## Worktrees

- **One teammate** may work in your worktree, if you stop writing to it while it works. Say so in the brief.
- **Two or more** each get their own worktree. Create them yourself, outside the main checkout, and give each its path. A tool that creates worktrees for you may put them inside the main checkout.
- **Before you reuse a worktree,** check that `git status` is clean and that nobody else (an agent, a dev server) is using it.

## The split, on the card

```markdown
## Split (lead, 8 Oct)
- Base: origin/master <sha>. The plan's code facts re-checked there.
- teammate "skills-a": tstack-plan-mission, tstack-brief (their folders only)
- teammate "skills-b": tstack-review, tstack-handoff
- lead: manifests, NOTICE, the export script, every generator at the end
- Design calls: <fork> → <your call>, because <reason>.
```

A restarted lead, or the reviewer, reads this to know who wrote what.

## The brief

Write it to a file and hand the teammate its path. The template, with your own values:

```markdown
# Brief: card #<n>, <slice> (from the lead, <date>)

## Read first
- The card, read-only: <the card's path, from tasks --json>. Don't write to it; the lead writes the card.
- The skills: tstack-implement-card and its references/mutants.md.

## Where you work
- Worktree <path>, branch <branch>, based on origin/master <sha>.
- Commits: local only, on <branch>, as many as you need for your mutants. Never push, switch branches, merge or rebase.
- Scratch folder for master copies and mutants: <path>.

## The files you own (write only these)
- <file>: <what changes>
- Anything else that must change: stop and ask me.

## What to build, with my design calls
- <the change>. My call on <fork>: <decision>. Push back if you disagree.

## Done when
- <test>: fails on origin/master with "<expected failure>", passes at your head.

## Mutants
- <guard broken> → must be caught by a named test.

## Hard lines
No installs, no state-changing git beyond your local commits, mutants only in a scratch copy, never touch other worktrees, never print a secret, a timeout on every long command.

## Report
Write it to <path>/REPORT.md and end your run with the same summary: the files, your commits, each fails-on-master run quoted test by test, the passing run, each mutant with its killer (survivors too), what's left, and every decision you made with why.
```

## Commits

One rule, the same as `tstack-implement-card`'s: a teammate commits locally on the branch you name and never pushes. Mutants need a committed head, so the teammate needs commits. You own what is pushed, and you may reshape it first.

To reshape without an interactive rebase, note the teammate's head, then reset softly to the base and commit again in pieces you can explain: `git reset --soft <base>`, then `git add` and `git commit` per piece, keeping the teammate's messages where they still fit. Then diff the old head against the new one: `git diff <old head> <new head>` must be empty, or show only the edits you meant. On #851 the lead re-committed the implementer's work as two pieces (Convex, /mcp), and the diff showed only a comment reflow.

Reshape only what isn't pushed. After the push, the SHAs are cited on the card and in the PR.

## Watching

A teammate that has said nothing for an hour has usually stalled. Read its last output before you wait longer. Restart it on a smaller piece, under two hours of work. See `tstack-watch`.

## Integrating

- Read each diff yourself. A teammate's summary is a claim.
- Check it wrote only its files: `git status` and `git diff --stat <base> <sha>` show every path touched.
- Run the card's tests yourself.
- Re-run its fails-on-master proof every time, in your own clean `git archive origin/master` copy, and compare the failures with the ones it quoted.
- Re-run a sample of its mutants, at least the ones on the card's main guard. On #851 the lead re-ran 2 of 7 (both caught) and the master run (5 failures, same messages).
- Then reshape its commits if you need to, and diff old head against new.
