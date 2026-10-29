# Architecture

How the pieces compose, and why each one exists where it does.

> **Implementation status:** This document describes the broader operating-model design.
> This checkout ships Markdown skills, rules, and reviewer definitions. Hook scripts,
> `.claude/settings.json`, `templates/`, `standards/`, and the promote, sketch, archive,
> and exec-update skills are absent. See [Adoption](ADOPTION.md) and
> [Lifecycle](LIFECYCLE.md) for the supported setup and procedural gates.

---

## The context budget is the design constraint

Everything below follows from one fact: **an agent's context window is a scarce, shared
resource, and every instruction you load is an instruction that competes with the user's
actual problem.**

A naive version of this system puts all the conventions in one enormous always-on
instruction file. That works until the file is 4,000 words, at which point three things
happen: the model starts missing instructions in the middle, every unrelated turn pays the
full cost, and nobody wants to add to the file because it is already too long.

So the system is tiered by *how often a rule is actually needed*:

| Tier | Load behavior | What belongs here | Budget |
|---|---|---|---|
| **Rules** | Always, every turn | Safety boundaries, terminology, non-negotiable output standards | Keep it under ~500 words total. Ruthlessly. |
| **Skills** | On trigger — path match, keyword, or explicit invocation | Stage procedures, templates, checklists | As long as it needs to be. It only loads when relevant. |
| **Agents** | On dispatch, in a separate context | Specialist review, broad search, parallel work | Unlimited — the parent never sees the agent's intermediate work |
| **Hooks** | On tool call | Mechanical enforcement | Shell scripts. No context cost at all. |

**The agent tier is the one people underuse.** A subagent runs in its own context and returns
only its conclusion. That means an expensive operation — reading forty files to answer one
question — costs the parent conversation a paragraph instead of forty files. Delegation here
is a context-management strategy first and a parallelism strategy second.

---

## Skills

A skill is a directory containing `SKILL.md` with YAML frontmatter:

```yaml
---
name: prd
description: >
  Produce an engineering-ready product requirements document from a validated idea.
  Use when the user asks for a PRD, requirements, or a spec for a feature that has
  already passed triage.
---
```

The `description` is load-bearing. It is what the model reads when deciding whether this
skill is relevant to the current turn, and it is the *only* part of the skill that is always
in context. Write it as a decision aid — when to use this, and just as importantly, when not
to.

### What makes a skill good

1. **It states its preconditions.** "This skill assumes the idea has been through triage and
   has an initiative folder." A skill that runs on garbage produces confident garbage.
2. **It names its output path.** Not "save the document somewhere sensible" — the exact
   directory and filename convention. This is what makes the corpus navigable a year later.
3. **It carries its own template** rather than describing one. A template inline in the skill
   cannot drift from the skill's instructions.
4. **It declares its exit condition.** What has to be true for this stage to be complete.
   Without this, an agent will happily declare victory on a half-finished artifact.
5. **It is honest about what it cannot do.** The most useful line in several of these skills
   is the one that says "if X is unknown, stop and ask rather than assuming."

### Anti-patterns

- **The kitchen-sink skill.** If a skill covers three stages it will be loaded for all three
  and be wrong for two of them. Split it.
- **The skill that duplicates a rule.** If it is true always, it is a rule. If it is true
  during one activity, it is a skill.
- **The skill with no template.** Then it is advice, and advice produces inconsistent
  artifacts, and inconsistent artifacts are the thing this system exists to prevent.

---

## Agents

Agents are specialists with one job each. They run in an isolated context and return a
report.

```
                    ┌── tech-feasibility ──┐
                    │   (reads the code)   │
  orchestrator ─────┼── persona-review ────┼──▶ findings ──▶ human decides
   (has the doc)    │   (is the user)      │
                    ├── strategy-red-team ─┤
                    │   (attacks the case) │
                    └── quality-review ────┘
                        (checks the form)
```

Three design rules:

**1. Reviewers should be read-only.** All four are instructed not to edit. Three grant only
`Read, Grep, Glob`; `tech-feasibility` also grants `Bash`. That shell capability means the
current tool grants do not mechanically guarantee read-only behavior for all four.
A reviewer that can edit becomes a co-author, and a co-author has an interest in the document being good — which is precisely
the interest that makes them stop finding problems in it.

**2. One point of view per agent.** The temptation is to build a single "review this PRD"
agent. Don't. A single reviewer averages its critiques and returns something bland. Four
narrow reviewers with incompatible priorities return four sharp, contradictory reports, and
the contradictions are the most useful output — they are where the real tradeoff lives.

**3. The orchestrator verifies before relaying.** Agent output is a claim, not a fact. File
paths, counts, and quotes get spot-checked against source before they reach a deliverable.
This is not paranoia; agents confidently report plausible numbers, and a plausible wrong
number that reaches a stakeholder is worse than no number.

---

## Hooks

The enforcement layer. Everything above is a convention an agent might miss on turn 200 of a
long session. A hook is a shell script that runs on a tool call and can refuse it.

```
PreToolUse   ── can block the call        ── "this path is outside the lifecycle"
PostToolUse  ── runs after, can warn      ── "this document has no success metrics"
SessionStart ── environment preparation   ── refresh indexes, check for stale state
```

**Use hooks for the invariants you cannot afford to have violated once.** Not for style. A
hook that fires constantly gets disabled within a week, and then you have neither the hook
nor the discipline it replaced.

The two hooks described by the design (neither is included in this checkout):

- `verify-output.sh` — a `PostToolUse` check that a written artifact matches its template's
  required sections. Warns rather than blocks, because the failure mode of a false positive
  here is an operator who turns the hook off.
- `lifecycle-guard.sh` — a `PreToolUse` check that blocks writes to a stage directory that
  the artifact has not reached. This one blocks, because an artifact in the wrong stage
  corrupts the whole navigability premise.

---

## Rules

Three, and only three, because the budget is small and every rule is paid for on every turn.

| Rule | Enforces |
|---|---|
| `terminology.md` | Consistent vocabulary across every artifact. Boring and disproportionately valuable — a document corpus that calls the same thing three names is not searchable. |
| `output-standards.md` | Success metrics and open questions on every planning artifact; Gherkin acceptance criteria on every story. |
| `safety.md` | What must never appear in generated output — credentials, personal data, anything client-identifying. |

If you find yourself adding a fourth, first check whether it is really a skill.

---

## Directory layout

The intended full layout is shown below; it is not an inventory of shipped files.

```
.claude/
  rules/          always-on, keep small
  skills/<name>/SKILL.md
  agents/<name>.md
  hooks/*.sh
  settings.json   hook wiring and tool permissions

templates/        the shape of each artifact type
standards/        the methods — JTBD, RICE, Gherkin, document conventions
docs/             this file, the lifecycle, the adoption guide

work/             (gitignored) where your actual artifacts land
  capture/
  pipeline/<slug>/
  initiatives/<slug>/
  archives/<year>/<slug>/
```

`work/` is gitignored on purpose. **The system is the repository; your work product is not.**
Fork this, point it at a private repo for the artifacts, and keep the operating model
separable from the things it produced — otherwise you can never share the model without
sharing the work.

---

## Why lifecycle stages are directories

The design uses directories for broad routing and metadata for graduation. The current
skills write PRDs and reviews to `work/pipeline/<slug>/`; `/graduate` stamps the initiative
README without moving artifacts. No path guard enforces these conventions in this checkout.

The motivation for the broader directory-based design follows.

A document that says `status: in-review` in its frontmatter is one careless edit away from
saying something false, and nothing checks it. A document in `initiatives/` **is** in the
initiatives stage — the filesystem is the source of truth, `ls` is the status report, and
moving between stages is an explicit, reviewable, revertible act.

In the broader design, only `promote` and `archive` would move artifacts and repair inbound
references. Those skills are not shipped, so neither moves nor reference repairs are
implemented here. The shipped graduation procedure leaves files in place to preserve links.

This is the single most contested design decision in the system, and I would make it the same
way again. Status fields decay; directories cannot.
