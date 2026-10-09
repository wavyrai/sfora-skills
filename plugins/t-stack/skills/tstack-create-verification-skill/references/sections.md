# The verify skill's sections

Each section holds this repo's real commands, routes, selectors and prompts. If a section would hold an example rather than the real thing, you haven't finished reading the repo.

## Launch

Give the one command that brings the app up for a verify run, plus the sign that it is up: a line in the log, a port that accepts connections, a prompt on screen. Say how to shut it down as well.

For a short-lived CLI there's no server to keep alive. Launch means building it once; each drive then runs in its own fresh terminal session.

If an unrelated missing file blocks start-up (a sample config, an empty static folder), the skill may add it, label it as temporary scaffolding for verify runs, and delete it again during cleanup.

## Health check

A check that changes nothing and tells you whether driving this instance makes sense: the process is up, it's the right build, we own the port, the sign-in is valid. Run it first, and again whenever something looks wrong.

## Drive

The recipe for driving the app with the harness, using stable handles: accessible names and roles, data attributes, route paths, prompt strings. Never screen coordinates or tab order.

## Evidence

What to capture and where it goes. The standards:

- go the way a person goes; a test-only endpoint or a direct state setter proves nothing;
- record each step and what it changed, so the proof is more than a last screenshot;
- check side effects next to what's visible: rows written, files saved, messages sent;
- mock only where production already isolates the outside system;
- for a dry run, observe what it really skips (network, files, git refs), because some dry runs still write.

Evidence lives in a folder the skill names, outside anything cleanup removes. In the t-stack, the card gets the summary and the folder's path.

## Cleanup

How to stop what this run started. Kill by the process or session you started, never by name. Remove scratch state. Keep the evidence.

## Helpers

Ship each script with its execute bit set, and show the call in the skill body. If an agent has to read a script's source to learn how to run it, the script isn't helping.
