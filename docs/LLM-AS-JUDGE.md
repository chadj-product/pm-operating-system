# Evaluating the judges

The review stage uses language models to grade product documents. That raises the obvious
question, and it is the one most "AI reviewer" systems never answer: **how do you know the
judge is any good?**

This is the part of the system I would defend hardest, and it is the part that took longest to
get right.

---

## Two tiers, because they fail differently

```
Part A — deterministic lint      16 checks, no model, emits file:line
Part B — model judgment           3 specialist agents, run in parallel
Part C — verdict                  A + B → READY / NOT READY
```

**Part A spawns no agents.** Required section present. Metric has a baseline. Every requirement
has a Given/When/Then. No `[TBD]` markers survive. These are decidable by a script, and anything
decidable by a script should never be handed to a model — it costs more, takes longer, and
introduces variance into an answer that has none.

**Part B is for the questions with no mechanical answer.** Is this reach estimate credible? Would
a real coordinator actually use this on a Tuesday morning? Those need judgment, and judgment is
what a model can offer.

Sorting checks into the right tier is most of the work. The instinct is to send everything to the
model because the model can do everything. Resist it — every check you move into Part A is a
check that becomes free, fast, and perfectly repeatable.

---

## The enforcement path calls the review path

`/graduate` does not define its own checklist. Its second step is literally *run `/review
--gates` and use that verdict*.

This looks like a small refactor. It is the most important structural decision in the system.

The earlier version had `/graduate` implement its own gate checks — the same criteria, written
out a second time. They drifted, as duplicated logic always does, and an initiative graduated
with unresolved `[TBD]` markers still in it because the graduation copy had fallen behind the
review copy.

**One engine, called from both places, cannot disagree with itself.**

---

## The corpus, and why it is balanced on purpose

A judge that is never evaluated is just a confident opinion. So the judges are regression-tested
against a corpus of documents with known verdicts, re-run on a schedule, and diffed against
expectation.

The corpus is **deliberately balanced** — half READY, half NOT READY. This is not tidiness. It is
the whole point, and here is the argument:

> An earlier corpus was 1 READY / 11 NOT READY, because that is what happened to be lying around.
> Against that corpus, **a classifier that returns NOT READY unconditionally scores 11 out of 12
> — 91.7%.** It looks excellent. It carries zero information.

An unbalanced eval set does not measure your judge. It measures your base rate, and it will
happily award a high score to a component that has stopped working entirely. If you take one
thing from this repository, take this: **before you trust an eval score, ask what the degenerate
classifier scores on the same set.** If the answer is close, your eval is broken, not your model.

`examples/evals.example.json` shows the shape.

---

## Ground truth has to be reproducible

The corpus's expected verdicts are not typed in by hand. They are computed by a script — the same
deterministic Part A logic — so anyone can regenerate them and see whether they still hold.

An earlier version derived expected results from the live filesystem with no committed script.
Nobody could check whether the corpus had drifted from reality, and it silently rotted for over a
month before anyone noticed. A golden set nobody can reproduce is not a golden set. It is a
memory of one.

---

## Changing a judge means recalibrating

**Swapping the model behind a judge and re-running the eval belong in the same change.** Not the
next sprint. The same change.

A model swap is a silent behavior change to a component whose output nobody diffs, and "we'll
validate it after" reliably becomes "we never validated it." Treat the eval run as part of the
diff, the way you would treat a test suite.

---

## What this does not prove

- **The corpus is small.** A dozen cases catches gross regression. It does not characterize the
  judge's behavior across the space of real documents, and it should not be described as if it
  did.
- **The judges agree with the corpus, not necessarily with a good product manager.** The
  expected verdicts encode one person's standard. That standard could be wrong, and the eval
  would faithfully confirm it.
- **Part A is only as good as the templates.** A check that a section exists is not a check that
  the section is any good.

Naming these is not hedging. A measurement system that overstates its own coverage is worse than
no measurement, because it converts an open question into a settled one.
