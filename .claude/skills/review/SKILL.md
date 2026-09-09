---
name: review
description: >
  Run adversarial multi-agent review on a PRD, spec, one-pager, or strategy document before
  it advances. Use for "review this", "poke holes in", "is this ready". Required before
  /graduate. Not a copy-edit — this is a search for the reason the plan fails.
---

# Review

Four specialists read the artifact with incompatible priorities and report independently.
**Nothing is rewritten.** The output is a findings list; a human decides what is real.

## Procedure

1. Confirm the artifact exists and identify its type. Review criteria differ by type — a
   one-pager is not judged on acceptance criteria.
2. **Dispatch all four agents in parallel, in a single message.** They are independent and
   running them serially wastes wall-clock for no benefit.

   | Agent | Reads for | The question it is really asking |
   |---|---|---|
   | `persona-review` | Day-one workflow gaps | "What breaks the first morning a real user tries this?" |
   | `strategy-red-team` | Business case | "What would a competitor do the day after we ship?" |
   | `tech-feasibility` | Buildability against real code | "What does this assume exists that doesn't?" |
   | `quality-review` | Completeness against standard | "Is this document actually finished?" |

3. **Verify before relaying.** Spot-check every file path, count, and quotation an agent
   returns. Agents report plausible numbers confidently, and a plausible wrong number that
   reaches a stakeholder is worse than no number at all.
4. Consolidate into one ranked list — most severe first. Merge duplicates, but **preserve
   disagreements between agents rather than averaging them.** Where feasibility says "cheap"
   and red-team says "strategically pointless", that tension is the most valuable output of
   the whole review.
5. Write findings to `work/pipeline/<slug>/review-<date>.md`. Do not edit the source artifact.

## Ranking findings

| Severity | Meaning |
|---|---|
| **Blocking** | Ship this as written and it fails. A missing requirement, an unbuildable assumption, an unvalidated reach estimate the business case depends on. |
| **Material** | Will cost a rework cycle. Wrong but recoverable. |
| **Minor** | Should be fixed; nothing breaks if it is not. |

**Be honest about an empty result.** If the document is genuinely sound, say so in one line.
A review that manufactures three minor findings to look thorough teaches the reader to ignore
reviews.

## Exit condition

A ranked findings file exists. Every blocking finding is resolved or explicitly accepted with
a named owner and a reason. The source artifact is unmodified by this skill.
