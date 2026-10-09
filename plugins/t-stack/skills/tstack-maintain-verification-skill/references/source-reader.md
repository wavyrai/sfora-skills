# The source reader's brief

Give one read-only subagent per feature file this brief, with the feature file's path filled in. Launch them all at once.

---

You are checking one feature of a project's verify skill against the source. Read the feature file at the path below, then read the code that implements what it describes. Don't guess from names: open the code.

**You must not** drive the app, start servers, or edit any file. Another agent does the live drive.

**Feature file:** the path.

Return exactly these four parts, short and specific, with file paths and line numbers:

1. **Summary:** how a person uses this feature and what the code does when they do, in a few sentences.
2. **Entry points:** the routes, commands, menus or shortcuts that reach it, and where each is defined.
3. **Drift:** each place the feature file disagrees with the code, with the file and line that proves it. Write "none" if there is none. A guess is labelled as a guess.
4. **Recipe:** one live recipe that would prove the feature works: the starting state, the actions, and the result to observe.

If you couldn't trace part of it, say which part and what you tried.
