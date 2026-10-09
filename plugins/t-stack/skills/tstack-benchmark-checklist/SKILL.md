---
name: tstack-benchmark-checklist
description: "Use before you report or act on a performance number you measured: a speedup or regression in a PR, a CI step's time, a job's duration, a choice between two libraries or settings. Seven questions to answer with evidence from runs: what limits it, was each side tuned, does it break physical limits, did it error, does it repeat, does it matter end to end, and did the work happen at all. Covers how to state the number on a card. Skip for numbers you didn't measure, and for finding the slow code in the first place (use tstack-how, then measure)."
---

# Vet a number before you report it

A performance number goes on a card, gets quoted in a briefing and decides what gets merged. A wrong one costs more than no number. So before you report one, answer the seven questions below with evidence from runs, not from reading the code. The programme's own numbers were treated this way: "147 s for 31 pieces" and "an 8 s CI step" each came with what limited them.

## Steps

1. **Write the claim first,** in the words you would put on the card: "a nightly export job now takes 52 s instead of 80 s". The questions test that sentence.
2. **Open the script that takes the measurement.** Write down where the clock starts and stops, which events it tallies, and which work falls outside the timer.
3. **Check the machine.** Look at the load and the core count. When another process is busy and you can't stop it, run the before and after builds in turns, so the noise lands on both equally, and mention it in the report.
4. **Answer the seven questions** (detail in `references/questions.md`):
   1. **What limits it?** Name the limiter: one core, the disk, the network, a lock, the load generator. Profile in a run you don't report.
   2. **Was each side tuned?** Production builds, flags, pools, caches and data on both sides. A side on defaults means you compared settings, not code.
   3. **Does it break limits?** Do the arithmetic against bandwidth and cores. Removing a step that takes 10% of the run can save at most 10% of the time.
   4. **Did it error?** Count failures and check the outputs are right. Rejections are fast; timeouts are slow.
   5. **Does it repeat?** At least five runs a side, alternated. Give the median and the range. A gap smaller than the spread is no difference.
   6. **Does it matter?** Measure the path a person actually waits on, and give the micro result as a share of it.
   7. **Did the work happen?** Confirm the rows were written, the request arrived, the result was used.
5. **Write it on the card** with the shape in Report. Use `tstack-technical-writing` for the sentence and the card round-trip from `sfora-board` to append it.

## Guardrails

- A quick ballpark someone asked for needs one run, but still answer questions 4 and 7, and say it was one run. Picking between two options never counts as a ballpark.
- Call the verdict inconclusive when you can't name the limiter, when one side ran untuned, or when you couldn't check questions 4 and 7. Say which gap.
- Time limits are numbers too. #162's CI job hit its limit by one second; a limit set without the run's spread in view will fail on an ordinary slow day.
- Profilers slow the work down. Never report a number from a profiled run.
- One primary number in a PR body or a briefing. Every run, the spread and the proof of the limiter belong on the card or in a linked doc.

## Report

Open with one of four verdicts: faster, slower, no measurable difference, inconclusive. On the same line follow it with the figure and its unit, how many runs, the spread, and what limited it: "import step 8.1 s → 5.4 s; 6 alternated runs per build, after-runs spread 5.2 to 5.7 s; limited by gzip on a single CPU."

Detail: `references/questions.md`.
