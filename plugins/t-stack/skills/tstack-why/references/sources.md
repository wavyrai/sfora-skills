# Sources, and the investigator's brief

Search every kind of source you can reach. A source you couldn't reach is a gap to name, not a choice. Skip a reachable one only when it is provably irrelevant (for example, error tracking for a build-time script), and say so in the answer.

## The kinds of source, and what each tends to hold

| Source | What it tends to explain |
|---|---|
| git history and PRs | the reasons given while the code was reviewed: PR bodies, review threads, commit messages, tests added with the change |
| sfora cards | the decision record: the owner's words quoted and dated, OWNER and PM DECISIONS sections, BLOCKED paragraphs, review verdicts, evidence |
| sfora posts and docs | briefings, designs and playbooks written before or after the work |
| sfora rooms | the discussion that never reached a card |
| an issue tracker | the product or customer reason |
| team chat outside sfora | the same, for teams that talk elsewhere |
| error tracking | the exceptions that led to a guard, a retry or a null check |
| logs and metrics | the runtime facts behind a timeout, a limit or a retry count |
| analytics or a warehouse | the data behind a threshold, a flag or a migration |

## In git

- Follow the file's history through renames, and use a search for the exact text (`git log -S`) to find the commit that added it.
- Read the diff, not only the message. "Small refactor" sometimes hides a behaviour change.
- Squash merges lose the branch's commits; read the PR body and its comments instead.
- If the pattern was copied from elsewhere in the repo, investigate where it started.
- Skip bot commits for intent.
- Test names often encode the edge case that motivated the code.

## In sfora

- Search the board for the PR number, the feature's name and the card numbers in commit messages. Done cards hold the most.
- A mission card links its numbered cards; follow them.
- Quote decisions with the date and the section they sit in.

## The investigator's brief

Give each investigator one source and this brief, with the anchor filled in.

---

You are finding out why some code has its shape. Search only the source named below. Don't write or change anything.

**Your source:** one kind from the table above.
**The question:** the question.
**The anchor:** the file, lines, symbols, commits, PR numbers and card numbers found so far.

Return every item that bears on the question, each with:

- the exact text, quoted;
- where it is (commit, PR, card path, message, link);
- who wrote it, and when;
- whether it states the reason or only bears on it.

If you found nothing, say what you searched for and where. That result goes in the answer too.
