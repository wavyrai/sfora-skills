# The feature map

The map is the project's record of what a person can do and how to prove each thing works. It lives next to the verify skill: `features/README.md` plus one file per feature.

## The index (`features/README.md`)

- **Starting state:** how to launch with throwaway data, the seed records, the health check to pass before any drive, and the rule never to drive an instance this run didn't start.
- **Driving rules:** start every recipe from the starting state; prefer accessible names over CSS selectors; restore seed data after a change; never delete evidence.
- **Proof rules:** UI proof is a screenshot with the app visible plus an accessibility snapshot; CLI proof is the command, its output and its exit code; a change is proven by reading the stored value back a second way. Every artifact names its feature and the entry point used.
- **Skips:** an unreachable path is reported with the attempted command and the missing precondition. Never report an entry point as verified through a different one.
- **Features:** one line per feature file, with a link.

## One feature file

An H1 with the feature's name, one paragraph on what a person sees it do, then four sections in this order:

1. `Sub-features`: short ids, one line each.
2. `How to get to it`: every way a person reaches it (a menu, a keyboard shortcut, a route, a command).
3. `Driving it with <the harness>`: starts with the preconditions, then pairs each user action with the exact command and the result you should observe.
4. `Gotchas`: traps that waste or spoil a run.

Leave the internals out. The map says how a person gets somewhere, which handles stay stable, what state must exist first, which commands to run, and what you should see.

## A short example

A board app where a person creates a card:

```markdown
# Create a card

A person adds a card to a column and sees it appear at the top of that column, with its number.

## Sub-features
- create.button: the "New card" button in a column header.
- create.cli: the CLI's create command.

## How to get to it
- The board page, a column header, "New card".
- The CLI, from any folder, signed in.

## Driving it with the browser harness
Preconditions: seeded board, signed in as the test member.
- Click the button named "New card" in the To do column. See a dialog titled "New card".
- Type "Check the export" and press Enter. See a card titled "Check the export" at the top of To do.
- Read the card back with the CLI. Its column is To do.

## Gotchas
- The dialog keeps the last title typed. Clear the field before each run.
```
