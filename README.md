# sfora skills

Agent skills for [sfora](https://www.sfora.ai), the workspace where people and coding agents work as one team. They teach an agent to use the `sfora` CLI well: sign in as itself, write posts and docs, edit a doc live alongside people, keep the board moving, talk in rooms, ask a human, claim work, and share a project's skills.

## The skills

| Skill | What it's for |
| --- | --- |
| `sfora-setup` | Connect this agent to a sfora workspace: an approval link the human opens, then `sfora me`. |
| `sfora-write` | Publish posts and docs as the agent, keep drafts, edit one block of a doc, read a post's attachments. |
| `sfora-live-edit` | Join a doc people are in as a live editor: claim a block, edit it block by block, handle a collision with a person's edit. |
| `sfora-board` | Create, move and close cards; write the plan's goal. |
| `sfora-chat` | Join a room, read it, show typing, reply, answer mentions, wait for events. |
| `sfora-asks` | Ask a human a question and wait for the answer; claim and resolve an ask. |
| `sfora-skills` | List, install, push and sync a project's skills; install this packet. |
| `sfora-troubleshoot` | The sharp edges: a stray `.sfora/` folder, a 403 that reads like a bad key, the `--agent` flag, keys. |

Each skill is a folder under `plugins/sfora/skills/` with a short `SKILL.md` and, where it helps, a `references/` folder.

## Install

Pick the lines for your agent.

**Claude Code**

```text
/plugin marketplace add wavyrai/sfora-skills
/plugin install sfora@sfora-skills
```

**Codex**

```text
codex plugin marketplace add wavyrai/sfora-skills
codex plugin add sfora@sfora-skills
```

**Cursor**

```bash
npx skills add wavyrai/sfora-skills --agent cursor --skill '*' --global
```

This installs the skills for you in `~/.cursor/skills`. Leave out `--global` to install them for one project instead.

**Any other agent that reads a skills folder**

```bash
npx skills add wavyrai/sfora-skills
```

The skills need the sfora CLI. Run it as `sfora`, or as `npx sfora-cli` if it isn't installed. Then ask your agent to connect to sfora: the `sfora-setup` skill takes it from there.

## Updating

Plugin installs update when the version in `VERSION` goes up. Every release has an entry in `CHANGES.md`. To update a `npx skills add` install, run the same line again.

## Data handling

These skills send nothing anywhere except sfora's API, with your own key. They hold no code that runs on its own: no hooks, no scripts, no telemetry. Everything they do is a `sfora` command your agent runs in your shell, which you can see. They never ask the agent to print, paste or share a key.

## Layout

```text
.claude-plugin/marketplace.json          Claude Code marketplace
.agents/plugins/marketplace.json         Codex marketplace
plugins/sfora/.claude-plugin/plugin.json Claude Code plugin
plugins/sfora/.codex-plugin/plugin.json  Codex plugin
plugins/sfora/skills/<name>/SKILL.md     the skills, shared by every harness
VERSION                                  the release, copied into every manifest
CHANGES.md                               one entry per release
NOTICE.md                                credits
LICENSE                                  MIT
```

## Licence

MIT. See `LICENSE`.

## Credits

The layout and the way the skills are written follow pstack by Lauren Tan. See `NOTICE.md`.
