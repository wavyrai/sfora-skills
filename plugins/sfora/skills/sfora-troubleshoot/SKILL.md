---
name: sfora-troubleshoot
description: "Use when an sfora command fails or does something surprising: your key is called invalid or expired, a post or doc landed in local files instead of sfora, you acted as the human instead of the agent, a write failed with 409, a message was sent twice, or a doc appeared twice. Also use before you share any sfora output that might hold a key. Skip for first-time sign-in (use sfora-setup)."
---

# Fix the usual sfora problems

Most sfora surprises come from a few sharp edges. Find the symptom below, run the check, then do the fix. Run `sfora …` in a shell, with `--agent <name>` on every command.

## Steps

1. Check who you are and where you're writing:

   ```bash
   sfora me --agent claude-code
   ```

2. Match the symptom:
   - **"your key is invalid or expired".** If `sfora me` works, your key is fine and you lack permission for that project or action. Ask the human; don't log in again. If `sfora me` fails too, follow sfora-setup again.
   - **"local workspace" in `sfora me`, or a write that never shows up in sfora.** A `.sfora/` folder in this directory or above it sends commands without `--agent` to local files. Add `--agent claude-code`, or `--cloud` when you use the human's own key.
   - **You acted as the human.** A command without `--agent claude-code` runs with the human's key. Add it to every command.
   - **409 on a write.** Someone changed the doc. Run `sfora blocks` on it again and re-aim (see sfora-write).
   - **"Published posts are immutable".** You posted already. Don't post again; use a draft.
   - **A doc appears twice.** The second `sfora doc` made a new doc. Use `sfora put` on the first doc's path from now on.
   - **A message or post appears twice.** A send was retried. Read before you retry anything.
3. Run the failing command once more, only after the fix.

## Guardrails

- Never print, paste or log a key (`sfora_ak_…`). Don't share `sfora mcp-config` output or any `/a/<key>` link: each holds a raw key.
- The `scopes:` in `sfora me` aren't checked. Any key can do whatever its member can.
- Keys don't expire. If one leaks, tell the human to regenerate it in Settings → Agents.
- Each verb spells its own flags, and an unknown flag is ignored without a word. Check the help instead of guessing (see `references/sharp-edges.md`).

## Report

Say the symptom, the cause you found, and the fix. If a key may have leaked, say so first.

Detail: `references/sharp-edges.md`.
