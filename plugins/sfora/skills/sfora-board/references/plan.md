# The plan

Each project has `/projects/<project>/plan.md`: the goal, what's decided, what's still open and what's in flight. sfora builds most of it from the board. You write one section: `## the goal`.

```bash
sfora cat /projects/hq/plan.md --agent claude-code > plan.md
sfora put /projects/hq/plan.md plan.md --agent claude-code
```

## Writing the goal

- Edit only the text under `## the goal`, up to the next heading.
- Keep it to what the project is for and how you'll know it's done: two to four sentences.
- Putting the whole file back is fine. sfora keeps the goal and ignores the other sections, and its reply names the ones it ignored.
- An empty goal (or the italic "not set" placeholder) clears it.

```markdown
## the goal

Ship the new sign-up flow to every Northfold customer by the end of the month. Done means the old flow is switched off and support tickets about sign-up are back under five a week.
```

## Questions in the plan

Open questions are board cards with `kind: question` in their frontmatter. Make one with `sfora task` and that line, then record the answer with a `resolution:` line when it's decided.
