---
name: tstack-no-comments
description: "Use before you hand over a change, or when asked to clean up comments: have a fresh subagent go through the comments and lint or type suppressions the diff touches, delete narration, banners, commented-out code and excuses, keep only the few kinds that earn their place, and turn each surprise in our own code into a rename, an extracted function, a type or a test instead of prose. Skip for public API doc comments, licence headers and generated files, and for removing dead code or debug output (use tstack-code-tidy)."
---

# Delete the comments the code shouldn't need

A comment that explains our own code is usually a sign the code could say it: a better name, a smaller function, a type. Comments also drift. Nobody's test fails when one goes stale. So in the diff, most comments go, and each surprise they were covering for becomes a change to the code. A fresh pair of eyes does this best, because the author can't see which comments they needed only while writing.

## Steps

1. **Set the scope:** the files or diff you were given, or else the branch's diff against its base, including uncommitted work.
2. **Start one subagent** with the brief in `references/reviewer-brief.md` and the scope. It edits comments only, never code, and reports what it deleted, what it kept and why, and the symbols that need a reshape.
3. **Check its work.** Reject any edit to code, any change outside the scope, and any deletion of a comment that a keep rule protects. A "kept" comment survives only with proof that it's about something we can't change. For a thin "IMPORTANT" or "do not remove", run `tstack-how` or `tstack-why` on the symbol before you accept the keep or the kill. If a kill is unclear, leave it deleted. If a keep is unclear, delete it.
4. **Fix the flagged symbols.** Small ones directly: rename, drop the dead branch, use the real API. If a fix needs a new shape, sketch it once with `tstack-architect` for the whole set, then build it. Fix the cause in scope. Never add a guard that hides the symptom.
5. **Handle constraint comments** ("do not change this wording", "talk to X first"). Offer the cheapest way to enforce it in the code: a type, a runtime check, a test, a lint rule. With approval, add it and delete the comment. Without approval, delete the comment and list the constraint as open on the card. This is the retro's rule in small: a lesson goes into structure, not a note.
6. **Run the project's tests and type check** after the fixes.

## Guardrails

- What may stay:
  - licence and legal headers;
  - doc comments that define a public API's contract;
  - an explanation of behaviour forced on us by something we can't change (a vendor, a platform, a protocol), ideally with its issue or RFC link;
  - formatter directives such as `prettier-ignore`.
- A lint or type suppression (`eslint-disable`, `@ts-expect-error`) stays only when the rule is wrong or about style. When the rule protects correctness or safety, remove the suppression and fix the code.
- A long justification is a warning sign, not a defence. Don't shorten it into a smaller excuse; fix what it excuses.
- If the subagent gets it wrong twice, revert its edits, do the pass yourself, and say so.
- Comments only. Behaviour changes come from the fixes in step 4, each covered by a test.

## Report

The number of comments deleted, any restored and why, the symbols reshaped, the constraints now enforced in code, and the ones left open on the card.

Detail: `references/reviewer-brief.md`.
