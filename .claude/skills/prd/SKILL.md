---
name: prd
description: >
  Produce an engineering-ready product requirements document, with feasibility grounded in
  the actual codebase and acceptance criteria a developer can implement without asking a
  follow-up question. Use for "write a PRD", "requirements for", "spec out". Assumes the idea
  has been through triage. For an unvalidated idea, use /sketch first.
---

# PRD

The flagship skill. A PRD produced here should be handed to an engineering team with no
accompanying meeting.

## Preconditions

Stop and say so if any of these are missing:

- The problem is described in the words of someone who has it, not only in yours.
- There is at least one named user role the change is for.
- The idea has cleared triage, so the "should we?" question is already settled. **A PRD is not
  where you decide whether to build something.** If that is still open, you want `/sketch` and
  a one-pager.

## Procedure

### 1. Frame the job

Open with a Jobs To Be Done statement, in the canonical form:

> When **[situation]**, I want to **[motivation]**, so I can **[expected outcome]**.

If you cannot write this without inventing the situation, the problem is not understood well
enough to specify. Go back.

### 2. Ground the feasibility

Dispatch the `tech-feasibility` agent against the real codebase **before** writing
requirements, not after. This ordering matters enormously: a PRD written first and
feasibility-checked second becomes a negotiation about which requirements to drop. A PRD
written against known constraints is buildable on the first pass.

What comes back is specifically: what already exists that this can reuse, what does not exist
that the proposal silently assumes, and where the integration seams are.

### 3. Write the document

Fill `templates/prd.md`. Every section is required. The two that are usually skipped and must
not be:

- **Success metrics** — with a *baseline*, a *target*, and **the mechanism that will measure
  it**. "We'll track adoption" is not a metric. "Weekly active coordinators, baseline 34,
  target 60 by day 90, measured by the existing session event already emitted at login" is.
  If the measurement mechanism does not exist yet, **building it is in scope and belongs in
  the requirements.**
- **Open questions** — named, owned, and dated. An open question written down is a managed
  risk. The same question left implicit is a mid-sprint surprise.

### 4. Acceptance criteria in Gherkin

Every requirement gets Given/When/Then. Not because the format is magic, but because it forces
a concrete precondition and a concrete observable outcome, and it is very hard to write vague
Gherkin.

```gherkin
Given a coordinator is viewing the intake queue
  And another coordinator has already opened referral #4471
When the queue refreshes
Then referral #4471 shows an "in review by" indicator naming the other coordinator
  And the coordinator cannot open it for editing
```

### 5. Review before handoff

Run `/review`. A PRD that has not been through adversarial review is a draft, whatever the
document says at the top.

## Exit condition

- `work/pipeline/<slug>/prd.md` exists and every template section is filled.
- Success metrics carry baseline, target, and measurement mechanism.
- Open questions are named with owners.
- Every requirement has Gherkin acceptance criteria.
- `/review` has run and its findings are either resolved or explicitly accepted in writing.

## Failure modes

- **Writing requirements for a solution nobody validated.** The PRD is downstream of the
  decision, not a substitute for it.
- **Feasibility as an afterthought.** See step 2.
- **Success metrics that measure output.** "Ship the queue redesign" is not a success metric;
  it is the work. Measure what changes for the user.
- **The requirement that says "intuitive".** Unimplementable and untestable. Replace with the
  specific observable behavior, or cut it.
