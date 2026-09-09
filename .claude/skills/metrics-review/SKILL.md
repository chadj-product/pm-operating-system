---
name: metrics-review
description: >
  Grade a shipped initiative against the success metrics it declared before it shipped.
  Use for "did it work", "post-launch review", "metrics review". This is the honesty
  mechanism of the whole lifecycle — run it whether or not anyone asks.
---

# Metrics review

The counterweight to every optimistic PRD. Reads the targets that were declared *before*
shipping, reads what actually happened, and returns a verdict.

## Procedure

1. Open the initiative's `prd.md` and extract the success metrics **exactly as they were
   written at the time**. Do not restate them charitably. If the target was 60 and someone
   later described it as "around 50", the target was 60.
2. Collect actuals. For each metric, record the value and where it came from.
3. **If a metric cannot be measured, that is the finding.** Record it as
   `UNMEASURABLE — instrumentation absent` and do not substitute a proxy. A proxy quietly
   converts a process failure into a product result and destroys the point of the exercise.
4. Compute the gap. Return one verdict per initiative:

   | Verdict | Criterion | Action |
   |---|---|---|
   | **Double down** | Met or exceeded target | Where is the next increment? |
   | **Iterate** | 50–100% of target, direction right | What is the specific blocker? |
   | **Investigate** | Under 50%, or moved the wrong way | Adoption problem or value problem? They need opposite responses. |
   | **Sunset** | No measurable value, no path to it | Name the removal cost. |
   | **Unmeasurable** | Instrumentation never existed | Fix instrumentation, re-review in one cycle. |

5. Write to `work/outcomes/<slug>-<date>.md` and update the initiative README with the verdict.

## The finding that justifies this skill

The first time this review ran across a full cohort of shipped work, **six of six initiatives
came back unmeasurable.** Four of them had named custom analytics events as the measurement
method, and those events had never been built. Nobody had lied; the metric was written in the
planning document, everyone assumed it was live, and no one owned making it so.

Publishing that internally was more valuable than any single feature in the cohort. It is why
`/prd` now requires the measurement *mechanism*, not just the metric.

## Rules

- **Grade against the original target, not a revised one.** Moving the target after seeing the
  result is the most comfortable and most corrosive habit in product management.
- **An honest miss is a good outcome for this skill.** The purpose is a true signal, not a
  flattering one. A review process that never returns bad news is not measuring anything.
- **Name the instrumentation owner** on anything unmeasurable, or it will be unmeasurable next
  quarter too.

## Exit condition

Every declared metric has an actual or an explicit `UNMEASURABLE`. One verdict per initiative,
with a named next action.
