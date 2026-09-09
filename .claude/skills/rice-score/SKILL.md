---
name: rice-score
description: >
  Score and rank a set of candidate initiatives using RICE. Use for "prioritize", "rank
  these", "what should we build first", "RICE score". Produces a defensible ordering with
  the assumptions exposed.
---

# RICE scoring

```
        Reach × Impact × Confidence
RICE = ─────────────────────────────
                  Effort
```

The arithmetic is trivial. **The value is entirely in forcing the inputs to be stated**, and
the discipline is in refusing to let any of them be a vibe.

## The inputs

| Factor | Unit | Rule |
|---|---|---|
| **Reach** | Users or events **per quarter** | A real count from a real source. Name the source inline. If you are estimating, say so and state the basis. |
| **Impact** | 3 massive · 2 high · 1 medium · 0.5 low · 0.25 minimal | Per user who is reached — not aggregate. |
| **Confidence** | 100% / 80% / 50% | 100% needs data. 80% needs evidence. 50% means informed guess. **Below 50%, do not score it — go get data.** |
| **Effort** | Person-months, all functions | Include design, QA, and rollout. Engineering-only effort estimates systematically favor engineering-light, coordination-heavy work. |

## Procedure

1. Score every candidate against the same Reach window. Mixing per-quarter and per-year reach
   silently ranks one item four times too high, and it is the most common error in practice.
2. **Cite the source for every Reach figure, inline in the table.** An uncited reach number is
   the single most common way a prioritization exercise launders someone's preference into an
   apparently objective ranking.
3. Compute, rank, and write to `work/pipeline/<slug>/rice.md`.
4. **Run the sensitivity check.** For the top five, recompute with Confidence dropped one
   level. Any item whose rank collapses is not a priority — it is a bet, and it should be
   labeled as one.
5. Record the ordering *and* the two or three inputs the ordering is most sensitive to.

## What RICE is bad at

Say this out loud when presenting the output, because a ranked table projects more authority
than it has earned:

- **It cannot see dependencies.** A low-scoring item that unblocks three high-scoring items is
  mis-ranked by construction. Check the graph separately.
- **It cannot see strategic necessity.** Table-stakes work to stay in a market scores terribly
  and must be built anyway.
- **It punishes long-horizon work**, because Effort is a denominator and Reach is measured
  this quarter.
- **It is trivially gameable** by whoever supplies the Reach numbers. This is exactly why the
  citation rule exists.

Use it to expose disagreement about *inputs*, which is productive, rather than to settle
disagreement about *conclusions*, which it cannot do.

## Exit condition

A scored table with every Reach figure cited, a sensitivity pass on the top five, and a
written note of which assumptions the ranking depends on most.
