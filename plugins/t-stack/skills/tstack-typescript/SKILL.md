---
name: tstack-typescript
description: "Use when you write, change or review TypeScript (.ts or .tsx): model states as discriminated unions, brand ids so they can't be swapped, make bad values impossible to build, treat outside data as unknown and parse it once with the project's schema library, avoid as-casts, narrow in a fixed order, check switches for exhaustiveness, and prefer satisfies, derived types and object arguments. Skip for other languages, and for the module's overall shape (use tstack-architect)."
---

# Write TypeScript the compiler can check

The type checker is the cheapest reviewer you have: it runs on every save and never gets tired. These rules move mistakes from runtime, where a person finds them, to compile time, where the build finds them. They follow from two ideas: make wrong states impossible to write down, and check outside data once, at the edge.

## Steps

1. **Read the file's own conventions first:** its schema library, its discriminant name (`kind`, `type`), its brand shape. Follow them; don't add a second way.
2. **Model states as a union with a literal tag,** not a bag of optional fields. `{ kind: "loading" } | { kind: "ready"; card: Card } | { kind: "error"; message: string }` can't be both loading and failed.
3. **Brand ids** that share a primitive (`CardId`, `ProjectId`), so one can't be passed for the other. Create them only in a parser.
4. **Make the bad value impossible to build.** A non-empty list as `[T, ...T[]]`, a range as a start and a length. Strengthen a type only where the loose one forces a `!`, a cast or a "can't happen" throw.
5. **Treat outside data as `unknown`:** request bodies, `JSON.parse`, environment variables, file contents, query results. Parse it once at the edge with the schema library the project already uses, and derive the type from the schema.
6. **Narrow in this order:** a tag check, then `in`, then `typeof` or `instanceof`, then a type guard that really checks, and only then `as`, after validation.
7. **Make switches exhaustive** with a `never` check in the default branch, so a new variant breaks the build where it must be handled.
8. **Prefer `satisfies` to `as`** for checked literals, derive types (`Pick`, `Omit`, `ReturnType`, `Awaited`, `typeof`) before you declare new ones, and pass an object when a function takes several arguments of the same type.
9. **Run the type check and the project's tests.**

## Guardrails

- No `any`. No `as` on unvalidated data. A type guard that doesn't check what it claims is worse than a cast, because its name says it's safe.
- Don't add a schema library for one guard. Use the one the codebase trusts. One schema owns a shape; a schema, an interface and a hand-written guard for the same data will disagree sooner or later, so keep only the schema.
- Validate at the edge and trust the types inside. Don't re-check deep in the call chain.
- Don't strengthen every type by reflex. Keep `T[]` while every use of it is total.
- Skip object arguments on hot paths (render loops, parsers) where the allocation matters.
- Don't mock what you can run. Use the framework's real test tools.
- No `console.log` in shipped code. Use the project's logger, with an id to debug from.
- A suppression (`@ts-expect-error`) needs a reason that is about a tool, not about our code (see `tstack-no-comments`).

## Report

The types you added or changed, the casts and `any`s removed, where outside data is now parsed, and the type check result.

Detail: `references/patterns.md`.
