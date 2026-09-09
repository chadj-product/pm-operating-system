---
name: graduate
description: >
  Certify that an initiative is engineering-ready and stamp it as graduated. Use for
  "graduate", "is this ready for engineering", "hand this off". Checks a gate — it does
  not move files and it does not create anything.
---

# Graduate

The handoff gate. **This skill stamps; it never moves.** Moving artifacts between directories
breaks external links — to prototypes, to shared documents, to messages in someone's inbox
from three weeks ago. Graduation changes the initiative's *state*, not its *address*.

## The gate

Refuse to graduate unless all of these are true. Report which ones fail; do not stamp
partially.

- [ ] `prd.md` exists with every template section filled
- [ ] JTBD statement present and specific
- [ ] Success metrics carry **baseline, target, and measurement mechanism**
- [ ] Every requirement has Gherkin acceptance criteria
- [ ] Open questions are named with owners — or explicitly closed
- [ ] `/review` has run and every **blocking** finding is resolved or accepted in writing
- [ ] Technical feasibility is grounded against the real codebase, not assumed
- [ ] Dependencies on other teams are named, with a status for each

## Procedure

1. Walk the gate. Report pass or fail per line — no summary judgment.
2. On any failure: stop, list what is missing, stamp nothing. **Do not offer to fill the gaps
   yourself in this skill.** Graduation must be a check, and a checker that also fixes is not
   a check.
3. On full pass: add a graduation block to the initiative `README.md`.

```markdown
## Graduated
- **Date:** 2026-03-14
- **Gate:** all criteria passed
- **Review:** review-2026-03-11.md — 2 blocking findings resolved, 1 minor accepted
- **Accepted risks:** rollout depends on the identity migration completing first
```

## Why this is a stamp and not a move

The earlier design promoted graduated work into a separate directory tree. It broke a
prototype link that had already been shared with a client, and there was no way to know how
many other references existed in email and chat.

The general principle is worth keeping: **once an artifact's address has been shared outside
the system, that address is part of its contract.** State changes are cheap. Address changes
are not.

## Exit condition

Either a complete pass/fail report with nothing stamped, or a graduation block written to the
initiative README. Never both, never partial.
