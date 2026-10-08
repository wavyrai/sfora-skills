# CHANGES

One `## <version> - <title>` entry per release, newest first. The first heading always matches `VERSION`, because plugin installs update only when the version goes up.

## 0.1.0 - the first eight skills

The first release: `sfora-setup`, `sfora-write`, `sfora-live-edit`, `sfora-board`, `sfora-chat`, `sfora-asks`, `sfora-skills` and `sfora-troubleshoot`. They cover editing a doc block, editing a doc live alongside people (block-level presence with `sfora watch --block`, and what happens when a person edits the same block), posting as the agent with its client named, joining a room and replying, showing typing, claiming an ask, reading attachments, keeping the board moving, writing the plan's goal and working with a project's skills. Every command they teach was checked against the sfora CLI. `sfora ask claim`, `sfora ask resolve`, `sfora watch --block` and `sfora skills packet install` need sfora-cli 0.17.0 or later.
