# Lifecycle stages and gates

This guide describes the workflow encoded in the shipped skills. The broader architecture
also describes hooks and additional stages that are not implemented in this checkout.
There are no hook scripts, hook settings, template files, or executable validators here.
The gates below are instructions to the operator and agent, not automatic filesystem
enforcement. See [Adoption](ADOPTION.md) for setup and missing prerequisites.

## Stages supported by the skill definitions

| Stage | Shipped procedure | Output and completion condition |
|---|---|---|
| Capture | [`/capture`](../.claude/skills/capture/SKILL.md) | Append one entry to existing `work/capture/inbox.md`: sequential `CAP-NNN`, date, raw wording, and source. Do not analyze or deduplicate. |
| Triage | [`/triage`](../.claude/skills/triage/SKILL.md) | Read the whole inbox, cluster, then route each item: Promote, Attach, Answer, Park, or Decline. Record route/date without deleting entries; parks need triggers, declines need reasons. |
| Requirements | [`/prd`](../.claude/skills/prd/SKILL.md) | `work/pipeline/<slug>/prd.md`; requires a problem in the user's words, a named user role, and completed triage. Ground feasibility before writing, fill the locally supplied template, and run review. |
| Review | [`/review`](../.claude/skills/review/SKILL.md) | `work/pipeline/<slug>/review-<date>.md`; ranked findings from four specialists, with blocking findings resolved or accepted with owner/reason. Review does not edit the source artifact. |
| Graduate | [`/graduate`](../.claude/skills/graduate/SKILL.md) | Per-criterion pass/fail report; only a full pass permits a graduation block in the initiative README. No file moves or gap-filling during this check. |
| Outcomes | [`/metrics-review`](../.claude/skills/metrics-review/SKILL.md) | `work/outcomes/<slug>-<date>.md` plus initiative README verdict. Every original metric has an actual and source or an explicit `UNMEASURABLE`; name the next action. |

The pipeline folder remains the address used by the shipped PRD and review procedures.
The broader layout mentions `work/initiatives/` and `work/archives/<year>/`, but there is
no shipped transition procedure into those directories. Do not infer an automatic move.

## Transitions that require a local procedure

Triage's Promote route names `/promote`, and PRD points unvalidated ideas toward `/sketch`.
Neither skill exists here. The README's sketch and archive stages describe the broader
model; `/archive` is also absent. `/exec-update`, referenced by the one-pager skill, is
not shipped. Establish and document those procedures in your fork before depending on them.

For the supported path, an operator can establish a pipeline initiative after triage,
preserve its source evidence, and then use PRD, review, and graduation. This is a local
setup action, not an implemented `/promote` command. Capture's inbox must also be
initialized before first use because capture only appends.

## Review and graduation

Review dispatches `persona-review`, `strategy-red-team`, `tech-feasibility`, and
`quality-review` independently in parallel. The orchestrator verifies returned paths,
counts, and quotations, merges duplicate findings, preserves disagreements, and ranks
findings as Blocking, Material, or Minor. Humans decide which findings to accept.

The current graduate skill checks all eight criteria:

1. `prd.md` exists with every template section filled.
2. A specific JTBD statement is present.
3. Success metrics include baseline, target, and measurement mechanism.
4. Every requirement has Gherkin acceptance criteria.
5. Open questions have owners or are explicitly closed.
6. Review has run and every blocking finding is resolved or accepted in writing.
7. Feasibility is grounded in the real codebase.
8. Dependencies on other teams have names and statuses.

On any failure, report the missing items and stamp nothing. On full pass, add the
graduation block specified in the skill: date, gate result, review reference, and accepted
risks. Graduation changes state in the README and leaves the artifact's address unchanged.
It does not ship software or create an engineering integration.

The current skill uses this explicit checklist. It does **not** invoke `/review --gates`;
that shared deterministic engine is described in [Evaluating the judges](LLM-AS-JUDGE.md)
but is absent from this checkout.

## Measurement closes the loop

PRD metrics need a measurement mechanism; if instrumentation is missing, building it
belongs in requirements. After shipping, metrics review compares actuals against the
original targets without quietly revising them. Its verdicts are Double down, Iterate,
Investigate, Sunset, or Unmeasurable. Follow the skill's criteria and record a named next
action. An unmeasurable result needs an instrumentation owner and a later re-review.
The skill recommends running reviews routinely, but no scheduler is included.

Supporting skills do not introduce stage transitions: `/rice-score` writes
`work/pipeline/<slug>/rice.md`; `/onepager` supplies a decision-document structure without
a fixed output path; `/decision-log` writes dated records under `work/decisions/`;
`/competitive-analysis` writes dated research under `work/knowledge/competitive/` and
depends on a template you must supply.

## What is enforced today

| Control | Current implementation and limit |
|---|---|
| Stage preconditions and gates | Markdown instructions in skills; no executable path guard or section validator. |
| Reviewer tool grants | Three reviewers allow `Read, Grep, Glob`; `tech-feasibility` also allows `Bash`. All are instructed not to edit, but shell access is not inherently read-only. |
| Work-product separation | `.gitignore` excludes `work/`; it does not protect already tracked files, back up artifacts, or control access. |
| Judge regression testing | `examples/evals.example.json` illustrates 12 balanced expected verdicts. Fixtures, deterministic oracle, runner, and CI are absent; the example is not an executed test result. |

Architecture describes `lifecycle-guard.sh` as a blocking pre-tool path check and
`verify-output.sh` as a warning post-tool section check. Neither file nor its hook wiring
is included. Implement and validate them in your own environment before claiming that an
out-of-stage write is blocked or an incomplete document is automatically detected.
