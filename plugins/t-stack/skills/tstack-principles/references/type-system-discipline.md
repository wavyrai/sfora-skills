# Type system discipline

**The rule:** use the type checker to rule out impossible states, mixed-up values and unhandled cases before the code runs. Any case the compiler lets you skip will eventually ship as a bug.

## When it applies

Designing a type, reviewing a signature, and any TypeScript in the repo: the app, Convex functions, the CLI.

## How it looks in the t-stack

- **Make illegal states unrepresentable.** `{ done: boolean; doneAt?: number }` allows "done with no time". Use `{ kind: "open" } | { kind: "done"; at: number }`, or derive `done` from `doneAt`.
- **Brand values that mean different things.** A card id, a project id and a member id are all strings; branded types stop one going where another belongs. Convex's `Id<"table">` already does this for documents: use it.
- **External data is untyped until parsed.** Request bodies, env vars, CLI args, webhook payloads and JSON from a file get a parse step at the boundary (see `references/boundary-discipline.md`).
- **Don't lie to the compiler.** An `as`, a `!` or an `any` is a crash waiting to happen. Prove the fact (validate, narrow) or treat the cast as a hazard and say so.
- **Make matches exhaustive.** A `never` check in the default branch makes the compiler name every place a new variant needs handling.
- **Derive types from the source of truth:** the Convex schema, the API's types, the design tokens. Don't hand-write a parallel copy.
- **Strengthen a type only where something would otherwise fail.** Precision for its own sake adds work for every caller.

## What to do

1. If you can write a comment explaining when a combination of fields is valid, split the type into a union.
2. Trace each `as`, `!` and `any` back to the boundary, and parse there instead.
3. After adding a variant, check that the compiler points to every place that must handle it.
