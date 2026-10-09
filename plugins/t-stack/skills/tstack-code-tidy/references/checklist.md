# The tidy checklist

For each kind: what to look for, how to prove it's safe to remove, and what to leave.

## Debug output

**Look for:** `console.log`, `console.debug`, `print`, `dbg!`, temporary timers, `debugger` statements, dumped objects, `TODO remove` lines.

**Prove:** nothing reads the output. A log line a test asserts on, or one the project's logger owns, is behaviour.

**Leave:** structured logging the project uses on purpose, with its level and context.

## Dead code

**Look for:** functions, exports, constants, types, branches and parameters the diff added or left behind that nothing reaches.

**Prove:** search for every use, including the indirect ones: string keys, route tables, dynamic imports, re-exports from an index file, test files, scripts, other packages in the repo. A parameter is dead only when no caller passes it and no implementation of the same interface needs it.

**Leave:** public API another package or a user may call. Removing that is a change, not a tidy.

## Commented-out code

**Look for:** blocks of code turned into comments.

**Prove:** nothing; it doesn't run. git keeps the history.

**Leave:** nothing. If it's needed later, it's in git.

## Needless defensive checks

**Look for:** a null check on a value whose type can't be null; a `try` around code that can't throw what it catches; a fallback for a case the only caller never sends; the same check repeated deeper in the call chain.

**Prove:** the type rules the state out, or every caller already guarantees it. Read the callers.

**Leave:** checks at a boundary: user input, network responses, file contents, environment variables, data from another service. Those are validation, and they stay.

## One-caller wrappers

**Look for:** a function the diff added that has one caller and only forwards its arguments to another function, with the same shape.

**Prove:** it adds no rule, no conversion, no name that makes the call site clearer.

**Fix:** inline it into its caller.

**Leave:** a wrapper that is the module's public boundary, or that a test needs as a seam.

## Stray files

**Look for:** scratch scripts, notes, local config, snapshot files nothing reads, output folders.

**Prove:** nothing imports, reads or runs them. Check the build config and the CI config too.

**Leave:** files the change truly needs. If a scratch file holds evidence for the card, move the evidence to the card.

## Style that breaks the file's own

**Look for:** a new name in a different case or pattern from its neighbours; an import style the file doesn't use; a different error-handling style; new formatting on lines the change didn't need to touch.

**Fix:** match the file. The file's style wins over the general guide; a repo-wide style change is its own card.

## After the tidy

Run the type check, the linter on the changed files and the project's tests. The same tests pass as before the tidy. If one changed outcome, a removal changed behaviour: put it back and look again.
