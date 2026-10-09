# Skills in detail

## Skills folders

| Harness | User folder | Project folder |
| --- | --- | --- |
| Claude Code | `~/.claude/skills` | `.claude/skills` |
| Codex | `~/.codex/skills` | `.codex/skills` |
| Cursor | `~/.cursor/skills` | `.cursor/skills` |
| Any agent that reads the shared folder | `~/.agents/skills` | `.agents/skills` |

Ask the human which one before you install, if it isn't clear.

## Installing a project skill

```bash
sfora skills plan-install <skill-name> --project hq --skills-target <skills-folder> --bot claude-code
sfora skills install <skill-name> --project hq --skills-target <skills-folder> --bot claude-code
sfora skills install <skill-name> --project hq --skills-target <skills-folder> --version <version> --bot claude-code
```

- `plan-install` shows what would happen and writes nothing.
- `install` takes the latest published version unless you name one with `--version`.
- sfora writes a small receipt beside each skill it installs. That's how it knows the folder is its own.
- It refuses a folder it didn't install, or one with local edits.

```bash
sfora skills uninstall <installed-folder>
```

removes a skill sfora installed, only if it hasn't changed since.

## Publishing a skill

```bash
sfora skills list --project hq --json --bot claude-code
sfora skills diff <skill-folder> <skill-name> --project hq --bot claude-code
sfora skills push <skill-folder> --project hq --expected-version 0 --bot claude-code
sfora skills push <skill-folder> --project hq --expected-version <version> --expected-revision <revision> --bot claude-code
```

- The folder's name is the skill's name: lowercase letters and digits, words joined by single hyphens.
- SKILL.md opens with frontmatter holding `name` and `description`. Make the description say when to use the skill and when not to.
- A new skill: `--expected-version 0` and no revision. An existing skill: `--expected-version` is its `version` and `--expected-revision` its `draftRevision`, both from `skills list --json`. Together they stop you overwriting someone else's push; without the revision, a push to an existing skill always fails.

## Keeping a local copy in sync

For a skill you edit locally and also publish, bind the two and compare:

```bash
sfora skills inventory --json
sfora skills bind <location-id> <skill-name> --project hq --bot claude-code
sfora skills status <binding-id> --bot claude-code
sfora skills plan <binding-id> push --bot claude-code > plan.json
sfora skills apply plan.json --bot claude-code
```

- `inventory` lists the skills on this machine; each has a location id.
- `status` says whether the local copy, the published one, or both changed since you bound them.
- `plan` previews a `push` or a `pull`, file by file, and writes nothing. `apply` runs a plan you've read.
- If an apply is interrupted, list it and finish it:

```bash
sfora skills operations
sfora skills recover <operation-id> --bot claude-code
```

## sfora's own skills

```bash
sfora skills packet install --harness claude-code --dry-run
sfora skills packet install --harness claude-code
sfora skills packet install --harness codex
sfora skills packet install --harness agents
```

- It copies the skills that ship with your sfora-cli into that harness's user folder. `--dry-run` shows the plan and writes nothing.
- Run it again after updating sfora-cli to update the skills. It never overwrites a skill you changed.
