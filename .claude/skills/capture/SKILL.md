---
name: capture
description: >
  Log a raw idea, client request, bug report, or observation in under twenty seconds
  with no structure required. Use whenever the user says "capture", "note this down",
  "don't let me forget", or dumps an unstructured thought mid-conversation. Do NOT use
  for work that already has an initiative — that is /triage or the initiative's own skill.
---

# Capture

The cheapest stage in the lifecycle, and the one that determines whether the rest of it has
anything to work on. **Optimize for speed of entry, not quality of entry.** A captured idea
in the wrong words beats a well-formed idea that was never written down.

## Procedure

1. Append to `work/capture/inbox.md`. Never create a new file — a single append-only inbox is
   what makes capture fast and triage possible.
2. Assign the next sequential ID in the form `CAP-NNN`.
3. Record, in this order:
   - The ID and today's date in `YYYY-MM-DD`.
   - **The raw input, as close to verbatim as possible.** Do not clean it up. Do not infer
     intent. The original phrasing carries information that a tidied summary destroys.
   - **Provenance** — who said it and in what setting. A request from a named account during
     a renewal conversation is a different object from the same sentence in a brainstorm, and
     six weeks later nobody will remember which it was.
4. Stop. Do not analyze, categorize, estimate, or propose a solution.

## Format

```markdown
## CAP-047 · 2026-03-14
**Raw:** "we end up re-keying the same intake twice because the queue doesn't show
what the other coordinator already picked up"
**Source:** operations lead, quarterly business review
**Tags:** #intake #duplication
```

## Exit condition

One appended entry with an ID, a date, the raw text, and a source. Nothing else.

## What this skill must not do

- **Do not deduplicate at capture time.** Two people reporting the same problem independently
  is a signal, and collapsing them at entry destroys it. Deduplication is triage's job.
- **Do not solve it.** The single most common failure is capturing a *solution* someone
  proposed instead of the *problem* they described. If the input is already a solution, record
  it verbatim and add `**Underlying problem:** unknown — ask` rather than inventing one.
- **Do not ask clarifying questions.** Capture is a write, not a conversation. If it needs a
  conversation it will get one at triage.
