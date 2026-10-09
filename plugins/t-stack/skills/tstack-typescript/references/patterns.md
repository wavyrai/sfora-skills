# TypeScript patterns, with examples

The examples use a board app's domain: cards, columns, asks. Swap in your own.

## A union with a tag

```ts
// Avoid: three flags allow contradictory states.
type AskView = { open: boolean; answer?: string; expired?: boolean };

// Prefer: only real states exist.
type AskView =
  | { kind: "open" }
  | { kind: "answered"; answer: string; by: MemberId }
  | { kind: "expired"; at: Date };
```

Use one tag name across the codebase.

## Branded ids

```ts
type CardId = string & { readonly __brand: "CardId" };
type ProjectId = string & { readonly __brand: "ProjectId" };

function parseCardId(raw: string): CardId {
  if (!/^\d{4}$/.test(raw)) throw new Error(`not a card number: ${raw}`);
  return raw as CardId; // the only cast, right after the check
}

function moveCard(card: CardId, to: ColumnName): void {
  /* card is trusted here */
}
```

Passing a `ProjectId` to `moveCard` now fails to compile.

## Bad values that can't be built

```ts
type NonEmpty<T> = [T, ...T[]];

// Avoid: every caller must remember the length check.
function firstReviewer(reviewers: MemberId[]): MemberId {
  return reviewers[0]!;
}

// Prefer: an empty list can't reach this function.
function firstReviewer(reviewers: NonEmpty<MemberId>): MemberId {
  return reviewers[0];
}

const hasReviewers = <T>(xs: T[]): xs is NonEmpty<T> => xs.length > 0;
```

A window of time as a start and a length can't be negative; a start and an end can.

```ts
type Window = { start: Date; ms: number };
```

Keep a plain `T[]` when every use is total: summing an empty list is 0, and that's fine.

## Outside data: unknown, then one schema

```ts
import { z } from "zod";

const CardFrontmatter = z.object({
  id: z.string(),
  column: z.enum(["Triage", "To do", "In progress", "Done"]),
});
type CardFrontmatter = z.infer<typeof CardFrontmatter>;

function readFrontmatter(raw: unknown): CardFrontmatter {
  return CardFrontmatter.parse(raw);
}
```

Reach for `safeParse` when bad input is a normal outcome the caller handles. If the type exists first, annotate the schema with it (`const S: z.ZodType<CardFrontmatter> = …`) so a schema that checks less fails to compile. Use whatever schema library the project already has; the pattern is the same.

## Narrowing

```ts
function label(view: AskView): string {
  if (view.kind === "answered") return `Answered: ${view.answer}`; // tag first
  if (view.kind === "expired") return "Expired";
  return "Waiting";
}
```

When you remove an existing `as`, find why the compiler couldn't infer: a missing tag (add one), a type that's too wide (narrow it), an unparsed edge (parse it), or something the type system truly can't say (brand it, or use `satisfies`).

## Exhaustive switches

```ts
function icon(view: AskView): string {
  switch (view.kind) {
    case "open":
      return "clock";
    case "answered":
      return "check";
    case "expired":
      return "x";
    default: {
      const unreachable: never = view;
      return unreachable;
    }
  }
}
```

In a switch that returns nothing, write `void unreachable;` instead of returning it.

## `satisfies`, not `as`

```ts
// Avoid: widens the literals and skips the check.
const columns = { triage: "01-triage", todo: "02-todo" } as Record<string, string>;

// Prefer: checked, and the literals stay literal.
const columns = { triage: "01-triage", todo: "02-todo" } satisfies Record<string, string>;
```

## Derived types

```ts
// Avoid: a copy of a generated type that drifts.
type CardSummary = { id: string; title: string; column: string };

// Prefer: derive it.
type CardSummary = Pick<Card, "id" | "title" | "column">;
```

## Object arguments

```ts
// Avoid: swap two strings and it still compiles.
moveCard("0854", "In progress", "lead");

// Prefer: each value is named at the call site.
moveCard({ card, to: "In progress", by: "lead" });
```
