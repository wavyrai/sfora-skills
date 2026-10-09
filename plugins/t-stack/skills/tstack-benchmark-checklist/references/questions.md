# The seven questions, in detail

Answer each with something you observed in a run. "The code looks like it would" is not an answer.

## 1. What limits it?

Ask why it isn't twice as fast. The answer is the limiter. Find it with a profiler for the runtime, CPU time per process, I/O wait and system-call counts, in a run you don't report. Then map the hot spot to the source.

Keep an eye on whatever drives the load, too. When the driver runs out of headroom before the system under test does, the number describes the driver.

A change that left the number flat is usually explained by the limiter. Name it first; only then may you call the change worthless.

## 2. Was each side tuned?

Give both sides the setup production gives them. That means production builds, production flags and environment, and the same versions and data. It also means matching settings for batching and transactions, pool sizes, and cache state: warm where production is warm, cold where it is cold.

Sometimes the limiter turns out to be a setting: a debug build, a commit after every row, an index nobody created. Then that side was never tuned, and the comparison isn't over. Fix the setting and run again. If you can't fix it, the run can't name a winner. The team will live with the option they pick, not with the settings you happened to test.

## 3. Does it break limits?

Do the arithmetic:

- bytes per second against the disk and network bandwidth;
- operations per second times the cost of one operation, against the cores you have;
- the time saved against the time the changed step took. If a step is a tenth of the run, deleting it outright buys you roughly 11% at best.

When a result beats a physical bound, the run timed the wrong thing, such as a cache hit, an empty code path or a defect.

## 4. Did it error?

Tally every failure and every response that isn't a success. Look at the outputs themselves: present is not the same as correct. Failures distort timing in both directions, since a rejected call comes back at once while a timeout or a retry drags. When the script has no error count, give it one.

## 5. Does it repeat?

Five runs per side is the floor, interleaved A, B, A, B, so neither side gets all the warm caches, the slow first start or the drift over time. Give the median plus the lowest and highest run. When the two medians sit closer together than the runs scatter, there is no measurable difference. On a close call, lean on a statistical test, such as Mann-Whitney U or whatever the benchmark harness reports.

## 6. Does it matter?

A micro benchmark never stands alone. Also time the full path a person sits through, at real data sizes and real concurrency, and state the micro gain as a fraction of that path. A function that is 1% of a page load can shave at most 1% off the page.

## 7. Did the work happen?

Prove the work fell between the start and stop of the clock. Check the server logged the request, the table holds the new rows, the file was actually read, and something consumed the result. Work that is set up but never forced (an unawaited promise, an iterator nobody drains, a value the optimiser throws away) times as instant. So does a call that timed out.

## On the card

```markdown
## Measurement (implementer, 8 Oct)

**Verdict:** faster.
**Number:** import step 8.1 s → 5.4 s, 6 runs per build, alternated; after-runs spread 5.2 to 5.7 s.
**Limiter:** gzip on a single CPU (profile in a separate run).
**Checked:** both builds on production settings and the same input file; 0 failed runs of 12; both builds imported the same row count.
**Not checked:** total CI wall time.
```
