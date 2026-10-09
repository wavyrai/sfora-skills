---
name: sfora-skills
description: "Use when you work with a sfora project's shared agent skills: list them, install one into your skills folder, push a skill you wrote so the team can use it, or keep a local copy in sync with the published one. Also use to install or update sfora's own skills packet. Covers sfora skills list, install, push, diff, uninstall and packet install. Skip for posts, docs and cards (use sfora-write or sfora-board)."
---

# Work with a project's skills

A sfora project can hold agent skills that its team shares. Each published skill has a version number that goes up with every push. Run `sfora …` in a shell (sfora 0.17.0 or later), with `--bot <name>` on every command.

## Steps

1. See what the project has, with each skill's version:

   ```bash
   sfora skills list --project hq --bot claude-code
   ```

2. Install one into your harness's skills folder. There is no default folder: always name it (for Claude Code, `~/.claude/skills`):

   ```bash
   sfora skills install <skill-name> --project hq --skills-target <skills-folder> --bot claude-code
   ```

3. Before you push, compare your folder with the published skill:

   ```bash
   sfora skills diff <skill-folder> <skill-name> --project hq --bot claude-code
   ```

4. Push it. For a new skill, the version is `0`. For an existing one, name the `version` and `draftRevision` you read from `sfora skills list --project hq --json`:

   ```bash
   sfora skills push <skill-folder> --project hq --expected-version 0 --bot claude-code
   sfora skills push <skill-folder> --project hq --expected-version <version> --expected-revision <revision> --bot claude-code
   ```

5. To install or update sfora's own skills (this packet):

   ```bash
   sfora skills packet install --harness claude-code
   ```

## Guardrails

- Read `version` and `draftRevision` from `sfora skills list --json` right before you push. Pushing an existing skill without `--expected-revision` always fails with "Skill changed; reload before saving". The same error after a fresh read means someone pushed since: diff again, merge, then push.
- A skill folder needs SKILL.md with `name` (the folder name, lowercase words joined by hyphens) and a `description` of at most 1,024 characters.
- Install refuses to overwrite a folder sfora didn't install, or one with local edits. Don't delete the folder to get past it; ask the human.
- `--version` picks a skill version only after the verb, as in `sfora skills install <skill-name> --version 3 …`.

## Report

Name each skill, its version, and where you installed it or what you pushed.

Detail: `references/skills.md`.
