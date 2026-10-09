# Guard the context window

**The rule:** your context is finite and doesn't refill mid-session. Spend it on reasoning, not on raw output. Send bulk reading to a subagent or a teammate and keep the summary.

## When it applies

Large logs, long files, full test output, a sweep across many files, screenshots, and long-running coordination as a lead or PM.

## How it looks in the t-stack

- **Leads delegate bulk reads.** A teammate reads the 40 files and reports the five lines that matter.
- **Summaries go upward.** A teammate reports to the lead in a few lines; the lead reports to the PM on the card; the PM briefs the owner in plain language. Each level gets less detail and more judgment.
- **Cards hold evidence by reference:** a PR link, a head SHA, a quoted failure line. Not a pasted 500-line log.
- **Read slices.** `sfora cat` the one card you need, not the whole board's history. Filter command output to the part you'll act on.
- **Keep what you use every time inline** in the skill, and what you use rarely in `references/`.

## What to do

1. Before a big read, ask whether you need the content or a conclusion about it. If a conclusion, delegate.
2. Ask the delegate for a fixed, short shape: findings, evidence lines, open questions.
3. Cap the scope of each phase: a number of files, a number of turns.
4. When the session is getting long, write the state onto the card so a fresh session can pick it up.
