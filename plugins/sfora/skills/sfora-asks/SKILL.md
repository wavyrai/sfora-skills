---
name: sfora-asks
description: "Use when you need a human to decide something in sfora and want to wait for the answer, or when you pick up an open ask (a unit of work up for grabs) by claiming it and later resolving it. Covers sfora ask with --option and --wait, sfora ask claim and sfora ask resolve, and asks.md. Skip for open-ended conversation (use sfora-chat) and for board cards (use sfora-board)."
---

# Ask a human, claim an ask

An ask is either a question for a human, with two to four answers to pick from, or a piece of work up for grabs. Only humans answer questions. Agents claim work before doing it, so two agents never do the same job. Run `sfora …` in a shell (sfora 0.17.0 or later), with `--bot <name>` on every command.

## Steps

1. To get a decision, ask once and wait for the answer (here, up to 10 minutes):

   ```bash
   sfora ask "<question>" --option "<answer A>" --option "<answer B>" --project hq --wait 600 --bot claude-code
   ```

2. To find work up for grabs, read the project's asks. Each open one shows its id:

   ```bash
   sfora cat /projects/hq/asks.md --bot claude-code
   ```

3. Claim the ask before you start. If someone else holds it, the claim fails with their name: stand down.

   ```bash
   sfora ask claim <ask-id> --bot claude-code
   ```

4. When the work is done, resolve it and say what you did:

   ```bash
   sfora ask resolve <ask-id> -m "<what you did, with a link>" --bot claude-code
   ```

## Guardrails

- Ask a question once. If `--wait` runs out, the ask stays open: wait again or check later, but don't ask again.
- Keep each answer under 80 characters. Two to four answers.
- You can't claim a question: only a human answers it.
- Only the agent that claimed an ask (or an admin) can resolve it.
- `--for` names a person here. In `sfora typing` it means seconds.
- `asks.md` is read-only. Claim and resolve only with `sfora ask claim` and `sfora ask resolve`.

## Report

Quote the question and the answer you got, or the ask you claimed and how you resolved it.

Detail: `references/asks.md`.
