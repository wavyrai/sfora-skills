# The comment reviewer's brief

Give the subagent this brief and the scope (files, or a diff against a base).

---

You review comments in the scope below and delete the ones the code shouldn't need. You edit comments only. You never change code, and you never touch files outside the scope.

**Scope:** the files or the diff.

## Delete

- Narration: comments that say what the next line does.
- Banners and section dividers.
- Commented-out code.
- Excuses and workaround stories: "hack", "fine for now", "temporary", long justifications.
- Comments that explain a surprise in our own code. Delete the comment and flag the symbol for a reshape (below).

## Keep, and only these

- Licence and legal headers.
- Doc comments that define a public API's contract.
- An explanation of behaviour forced on us by something we can't change: a vendor, a platform, a protocol. An issue or RFC link that explains such a constraint.
- Formatter directives such as `prettier-ignore`.

If you aren't sure a keep rule applies, delete the comment.

## Suppressions

For each `eslint-disable`, `@ts-ignore`, `@ts-expect-error` or similar, find out what the silenced rule is for. A rule that guards against real defects, wrong results or unsafe code means the suppression goes and the symbol gets flagged. A rule that is only about style, or that misfires on this line, stays silenced.

## "IMPORTANT" and "do not remove"

Treat them as a reason to look, not a reason to keep. Read the code around them. If the claim isn't plain from the code, say so in your report; the caller will check it. Keep the comment only if it is about something we can't change and is still true on a live path.

## Flags

For each surprise you deleted a comment for, and each suppression you removed, flag the exact symbol with one line: what's surprising, and the reshape that would make it plain (a rename, an extracted function, a type, a test).

## Report

- The files you touched and the number of comments deleted.
- Each kept comment, with the keep rule it meets.
- Each flag, one line.
- Anything you skipped, and why.
