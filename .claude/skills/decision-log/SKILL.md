---
name: decision-log
description: >
  Record a product or architectural decision as an immutable dated record — what was decided,
  what was rejected, and what would reverse it. Use for "log this decision", "we decided",
  "ADR", or after any meeting that settled something contested.
---

# Decision log

Product organizations do not lose decisions; they lose the **reasoning behind** decisions.
Six months later the decision looks arbitrary, someone relitigates it, and the team spends a
week rediscovering a constraint that was obvious at the time.

## Format

One file per decision, `work/decisions/YYYY-MM-DD-<slug>.md`, **append-only**.

```markdown
# Use a single queue per facility rather than per service line

- **Date:** 2026-03-14
- **Status:** Accepted
- **Decider:** <role>
- **Consulted:** <roles>

## Context
What was true when this was decided. The constraints, the pressure, what was unknown.

## Decision
The decision, in the active voice, in one paragraph.

## Alternatives rejected
Each with the actual reason. "We considered per-service-line queues and rejected them
because routing rules would then need to be duplicated per line, and three of five clients
run a shared coordinator pool."

## Consequences
What this makes easy. What it makes hard. What it forecloses.

## Reversal trigger
The specific observation that should cause this to be revisited.
```

## Rules

- **Never edit a decision record.** Superseding one means writing a new record that references
  it and flipping the original's status to `Superseded by <link>`. The value of the log is
  that it shows what people believed *at the time*, and editing destroys exactly that.
- **The rejected alternatives are the most valuable section.** Anyone can see what was chosen
  by looking at the product. Only this file records what was considered and why it lost.
- **Write the reversal trigger.** A decision with no reversal condition becomes permanent by
  default, including the ones that were only ever right for one quarter.
- **Log the contested ones.** A decision everyone agreed on rarely needs a record. A decision
  someone lost an argument over always does.

## Exit condition

A dated, append-only file containing context, decision, rejected alternatives with reasons,
consequences, and a reversal trigger.
