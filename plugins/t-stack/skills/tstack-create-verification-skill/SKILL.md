---
name: tstack-create-verification-skill
description: "Use when nothing in a project lets an agent start the real app, operate it, and show a change working as a person would see it (a web page, a CLI, an API, a desktop app), or when a reviewer keeps asking for proof you can't produce. Generates a project-local verify skill: launch, a health check, how to drive it, what evidence to capture, cleanup, and a feature map, then proves the skill by running it once. Skip when the project already has one (use tstack-maintain-verification-skill), and for unit tests (use tstack-tdd)."
---

# Create a project's verify skill

Tests prove the code; a verify skill proves the product. It is a skill in the project's own repo that tells the next agent, cold and mid-task, how to start the real app, drive it the way a person does, and capture evidence a reviewer can check. In the t-stack that evidence goes on the card. Write the skill for that agent, not for a human reader.

## Steps

1. **Read the repo, not the person.** Answer these from the code, and ask only what you can't observe:
   - **What a person touches:** a web UI, a CLI, an API, a desktop app. Pick the main one and note the rest.
   - **How it starts:** the repo's own dev command (scripts, Makefile, README), its ports, env vars, seed data and sign-in.
   - **How an agent drives it:** existing harnesses first (browser test specs, terminal scripts, a debug port, plain HTTP), then a generic recipe.
   - **What evidence exists:** what the repo can already record, such as screen captures, CLI output and exit status, HTTP responses, log lines and database rows.
   - **Whether two copies can run side by side:** separate ports, data folders, profiles. If not, the skill must refuse to drive an instance it didn't start.
2. **Make the checkout start.** If it doesn't build or start as it is, fix that or report exactly why, before you write anything. Steps worked out on a checkout that won't run are steps nobody can follow.
3. **Write the skill** in the project's skills folder, named `verify` (in a monorepo, in the package's folder). Frontmatter is `name` and a `description` naming the app, the surface and when to use it. Sections, each with the repo's real commands and handles, no placeholders: Launch, Health check, Drive, Evidence, Cleanup, Helpers. See `references/sections.md`.
4. **Seed the feature map:** a `features/README.md` index plus one file per user-facing feature, three to five to start, from the routes, commands and menus. Use the shape in `references/feature-map.md`.
5. **Prove the skill.** Follow it end to end once: launch, health check, drive one feature, capture evidence, clean up. Then check the evidence still exists where the skill says. Fix what failed, and run cleanup after every failed attempt so nothing is left running.
6. **Record it on the card:** the skill's path, the feature you drove, and where the evidence is (`sfora-board` for the round-trip). Point the team at `tstack-maintain-verification-skill` for upkeep.

## Guardrails

- A skill that was never run is a draft, not a deliverable.
- Evidence comes from the route a person takes, never from a test-only endpoint or a call that sets state behind the UI. Record what was done and what it changed, and check side effects (rows written, files saved, messages sent).
- Check a dry run's claims by observing (network, files, git refs), not by trusting its name.
- Cleanup kills only what this run started, never by process name, and never deletes the evidence.
- Headless isn't the owner's screen. A GPU-compositing bug (square corners from `backdrop-filter`) was invisible in headless browsers twice. When the change is visual, the skill names the real conditions to check: browser, GPU, display scale.

## Report

The skill's path, the features in the map, the feature you proved and the evidence's location, and anything the repo needed fixed before it would start.

Detail: `references/sections.md`, `references/feature-map.md`.
