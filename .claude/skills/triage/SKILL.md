---
name: triage
description: >
  Process the capture inbox — dedupe, cluster, decide what each item becomes, and route it.
  Use when the user says "triage", "process the inbox", "what's in capture", or asks to
  clear a backlog of raw notes. Run it on a cadence, not on demand.
---

# Triage

Turns an append-only inbox into decisions. The output of triage is never "a better-worded
idea" — it is a **routing decision** with a reason attached.

## Procedure

1. Read `work/capture/inbox.md` end to end before touching anything. Clustering requires
   seeing the whole set.
2. **Cluster before you judge.** Group items describing the same underlying problem, even when
   the proposed solutions differ. Note the cluster size — three independent reports of one
   problem is the strongest prioritization signal available and it is only visible here.
3. For each item or cluster, choose exactly one route:

   | Route | When | Where it goes |
   |---|---|---|
   | **Promote** | Real problem, worth a shaped effort | `work/pipeline/<slug>/` via `/promote` |
   | **Attach** | Belongs to work already in flight | Append to that initiative's `README.md` |
   | **Answer** | Already solved, or a support question | Reply, record the answer, close |
   | **Park** | Real, not now | `work/capture/parked.md` with a revisit trigger |
   | **Decline** | Out of strategy | Close with the reason. **Always record the reason.** |

4. Mark every processed item in the inbox with its route and date. Do not delete — the inbox
   is the audit trail of what was considered and rejected, which is as valuable as what was
   accepted.

## The rule that makes triage worth doing

**A parked item needs a revisit trigger, not a revisit date.** "Revisit in Q3" produces a list
nobody reads in Q3. "Revisit when a second client asks" produces an item that resurfaces
exactly when it has become real. Write the condition, not the calendar.

## Declining well

A decline with no reason is indistinguishable from an item that was lost, and the person who
raised it will raise it again. Record which strategic bet it conflicts with, or which
constraint makes it uneconomic. Two sentences is enough, and it is the difference between a
backlog people trust and one they route around.

## Exit condition

Every unprocessed inbox item carries a route, a date, and — for declines and parks — a reason
or a trigger. Cluster sizes recorded for anything promoted.
