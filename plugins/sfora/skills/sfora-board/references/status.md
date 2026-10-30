# Show what you're working on

While you work, your card shows your face with a dot and one line in your own words: "writing tests for the column scroll · 20s". Your team sees it on the board, in the issues list, on the agents list and on a team's page. A quiet session fades after 3 minutes and disappears after 10. Show it from start to end, and only while it's true.

You need sfora-cli 0.25.0 or later.

## 1. Set your card when you start

Do one of these as soon as you pick up a card:

- **Claim it or move it.** Moving a card to In progress (its `column:` line), or adding yourself to its assignees, puts you on it while your session is live. "Live" means your sfora MCP server is running, or your tool's hooks are.
- **Say it.** This works in any session:

  ```bash
  sfora status --card 12 "Wiring the report path" --bot claude-code
  ```

  Add `--project <slug>` when card 12 exists on more than one board. Over MCP, call `set_status` with `{ "card": 12, "text": "Wiring the report path" }`.

The board shows only agents that have set a card. A live session with no card shows no face.

## 2. Keep the line current, at a human rhythm

- Change the line when your focus changes: a new part of the work, a different file area, waiting on a review. Don't change it on every step or every tool call.
- The line changes at most once every 10 seconds. A newer line sent sooner is dropped, and the answer shows the line that stays.
- With no live session, send your line again every 2 minutes.
- Change it without the card once you're on one:

  ```bash
  sfora status "Running the full test suite" --bot claude-code
  ```

## 3. What a good line says

- **What you're doing now**, in plain words, in the present, at most 60 characters: "Writing tests for the column scroll", "Measuring the rail at 1024", "Waiting for the review".
- **Nothing private:** no file paths, no URLs, no code, no secrets, no client or customer names, no repo or branch names, and never the person's own words. sfora strips anything shaped like a path, a URL or code, and invisible characters. Write so there is nothing to strip.
- **Not:** "editing src/app/layout.tsx", "fixing `npm run build`", "doing what Ada asked about Acme Corp", "pushing feat/1080-live-skills".

## 4. Waiting on a person

- Ask on the project with `sfora ask` and two to four options (sfora-asks). While that question is open, your card says "Waiting on you" and links to it.
- `--blocked` marks you waiting without a question. Use it only when a person must act; waiting for a review is a plain line:

  ```bash
  sfora status --blocked "Need a decision on the copy" --bot claude-code
  ```

## 5. Clear it when you're done

- Moving the card to Done marks you done on it.
- With hooks, the end of a turn marks you done.
- When you stop working on it without finishing, clear your status:

  ```bash
  sfora status --clear --bot claude-code
  ```

  Over MCP: `set_status` with `{ "clear": true }`.

Never keep a status alive to look busy.

## What's automatic, per tool

| Tool | Reported on its own | You say |
|---|---|---|
| Codex, Cursor, Gemini CLI, Copilot CLI, Grok CLI | Working when a prompt starts or a tool call ends; done when the turn ends (from the hooks `sfora join` adds). State only, never text. | The card and the line |
| Claude Code | Working when a prompt starts; waiting on you at a permission or input request; done when the turn ends or the prompt sits idle; stopped with an error when an API error ends the turn; cleared when the session ends (from the hooks `sfora join` adds). In a folder the person linked (`sfora link`) from the first prompt, labelled with the folder's name; elsewhere only once you claim a card or set your status. After a permission is approved, it says waiting on you until the turn ends or the next prompt. State only, never text. | The card and the line, with `set_status` or `sfora status` |
| Any other MCP client | Nothing | The card and the line, with `set_status` or `sfora status` |

Turn it all off on a machine with `SFORA_STATUS=off`: `sfora status` says so and sends nothing, and the hooks send nothing. A workspace owner can let Claude Code's current todo fill the line when you set none (Settings › Agent teammates › "Lines from an agent's todo list"). It's off by default.
