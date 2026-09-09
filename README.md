# PM Operating System

**An AI-augmented operating model for product management, built on [Claude Code](https://claude.com/claude-code).**

Not a prompt library. A structured system in which product work moves through defined
stages, each stage has a skill that knows how to do it, and specialist agents review the
output before it advances. Every artifact lands on disk in a known location, in a known
format, with the evidence that produced it.

I built this because the parts of product management that consume the most time are also
the most rule-governed: writing a PRD, scoring a backlog, checking a spec against the
codebase, drafting a stakeholder update. Those are not creative acts. They are craft with
conventions, and craft with conventions is exactly what an agent can be held to — *if*
someone bothers to write the conventions down.

---

## What it does

```
capture ──▶ triage ──▶ sketch ──▶ PRD ──▶ review ──▶ graduate ──▶ archive
   │           │          │         │        │           │
   │           │          │         │        │           └─ engineering-ready handoff
   │           │          │         │        └─ adversarial review by 4 specialist agents
   │           │          │         └─ PRD + feasibility + prototype, in one pass
   │           │          └─ throwaway prototype to make the idea arguable
   │           └─ refine, dedupe, route to an initiative
   └─ 20-second capture, no structure required
```

Each arrow is a skill. Each skill knows what its stage requires, where the output belongs,
and what has to be true before the work advances. The lifecycle is enforced, not suggested
— a hook fails the write if an artifact lands outside its stage.

---

## The idea worth stealing

Most "AI for PMs" tooling is a chat window with a good prompt in it. The output is only as
good as the person's memory of what a good PRD contains, and it evaporates when the tab
closes.

This inverts that. **The convention lives in the repository, not in the operator's head.**

| Layer | What it is | Why it exists |
|---|---|---|
| **Rules** | Always-loaded constraints | Terminology, output standards, safety boundaries. Non-negotiable and cheap — they cost context on every turn, so there are only a few. |
| **Skills** | Stage-scoped procedures | Load only when relevant (by path, by trigger, or by explicit invocation). A skill is a checklist an agent cannot skip, plus the template it must fill. |
| **Agents** | Specialist reviewers | Each has one job, one point of view, and no authority to rewrite the artifact. They report; the orchestrator decides. |
| **Hooks** | Mechanical enforcement | The part that makes it real. A convention nobody can violate is a convention; a convention in a style guide is a suggestion. |
| **Templates** | The shape of the output | Every artifact type has one. Consistency is what makes a portfolio of documents reviewable. |

The load-bearing insight is the fourth row. **Documentation does not enforce anything.**
The first version of this system was a very good style guide, and compliance with it decayed
to roughly nothing within a month. Compliance became permanent the day a hook started
failing the write.

---

## Adversarial review as a first-class stage

The stage I would defend hardest is `review`. Before any initiative graduates to
engineering, it is read by four agents that are explicitly *not* trying to be helpful:

- **`persona-review`** embodies one user persona and grills the requirements against that
  person's actual daily workflow. Its job is to find the day-one gap.
- **`strategy-red-team`** attacks the business case — inflated reach, unvalidated market
  assumptions, weak build-versus-buy reasoning, the competitive counter-move nobody modeled.
- **`tech-feasibility`** reads the actual codebase and reports what the proposal would really
  cost, including the thing the PRD assumed already existed and does not.
- **`quality-review`** checks the document against its own standard — success metrics
  present, acceptance criteria in Gherkin, open questions named rather than buried.

Running four critics costs a few minutes. Shipping a PRD with an unvalidated reach estimate
costs a quarter. The asymmetry is the entire argument.

**None of them can edit the document.** A reviewer with write access becomes a co-author, and
a co-author stops being a critic. They return findings; a human decides which ones are real.

### The judges are themselves evaluated

Using a model to grade documents raises the question most "AI reviewer" systems never answer:
how do you know the judge works? This one is regression-tested against a **balanced** corpus of
documents with known verdicts, backed by a deterministic oracle, with a standing rule that
changing a judge's model and re-running the corpus happen in the same change.

Balanced on purpose — against a 1-READY / 11-NOT-READY corpus, a classifier that returns NOT
READY unconditionally scores 91.7% and carries no information at all. **Before trusting any eval
score, ask what the degenerate classifier scores on the same set.**

Full argument in [`docs/LLM-AS-JUDGE.md`](docs/LLM-AS-JUDGE.md); corpus shape in
[`examples/evals.example.json`](examples/evals.example.json).

---

## What it measures

Two rules keep this from becoming theater:

1. **Every PRD declares success metrics and open questions.** No exceptions — the template
   will not validate without them.
2. **Every shipped initiative is graded against the metrics it declared.** A separate
   `metrics-review` skill reads the targets, reads the actuals, and returns a verdict:
   double down, iterate, investigate, or sunset.

Rule 2 exists because of an uncomfortable finding. When I first ran a review of my own
shipped work, six initiatives came back **unmeasurable** — four of them had named custom
analytics events as the measurement method, and those events had never been built. A metric
you write in a planning document is not a metric. It is an intention, until someone owns
building it.

That finding is the reason the instrumentation check is now a gate rather than a good habit.

---

## Results

I ran a version of this system as the working operating model for a B2B healthcare SaaS
product organization for about eighteen months. Measured against the same team's prior
baseline:

| Activity | Before | After |
|---|---|---|
| Authoring a requirements document for one epic | 4–5 hours | ~30 minutes |
| Standing up a clickable prototype for a concept review | 2–3 days | ~30 minutes |
| Time to a first-pass competitive teardown | ~1 day | ~1 hour |

The productivity numbers are the least interesting part. The durable change was that
**artifacts stopped being lost.** Work products lived in one place, in one shape, discoverable
by the next person, with the reasoning attached — which turns out to matter more over a year
than any single hour saved.

> Adapting this to your own organization is the point, not a caveat. See
> [`docs/ADOPTION.md`](docs/ADOPTION.md).

---

## Getting started

```bash
git clone https://github.com/<your-account>/pm-operating-system
cd pm-operating-system
```

Open the directory with Claude Code. The skills are discovered automatically from
`.claude/skills/`. Start with:

```
/capture   an idea you do not want to lose
/triage    to sort what you captured
/prd       to take one idea to an engineering-ready document
```

Read [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) before customizing. Read
[`docs/LIFECYCLE.md`](docs/LIFECYCLE.md) to understand why the stages are gated the way
they are.

---

## What is deliberately not here

- **No integrations.** Jira, Confluence, Figma and Slack connectors are the first thing
  people ask for and the first thing that makes a system unportable. Add your own; the skills
  are written so that the artifact is the interface.
- **No company specifics.** No product names, no client data, no personas from a real
  research program, no design tokens from a real design system. Those all belong in your fork,
  not in the shared base.
- **No claim that this replaces judgment.** It removes the part of the work that was never
  judgment in the first place, which is most of it, and leaves the operator more time for the
  part that is.

---

## License

MIT — see [LICENSE](LICENSE). Use it, fork it, strip it for parts.

---

*Built by Chad Johnston. Written from scratch for public release; contains no code, content,
or confidential material from any employer.*
