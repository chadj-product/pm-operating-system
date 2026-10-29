# Adopting the operating model

Start with the procedures that are shipped, then add organization-specific context in
your fork. This repository is a set of Markdown skills, reviewer definitions, and rules;
it is not a complete automated lifecycle engine. See [Lifecycle](LIFECYCLE.md) for the
current gates and implementation gaps, and [Architecture](ARCHITECTURE.md) for the design.

## 1. Make a working copy

Fork the repository on GitHub, then clone your fork:

```bash
git clone https://github.com/YOUR-ACCOUNT/pm-operating-system.git
cd pm-operating-system
```

Open the directory in Claude Code. Skill definitions live in `.claude/skills/` and
specialist reviewers in `.claude/agents/`. This repository does not bundle Claude Code,
credentials, integrations, or a target product codebase.

Keep work artifacts separate from the public operating model. The root `.gitignore`
excludes `work/`, `.claude/settings.local.json`, and `CLAUDE.local.md`. Ignoring files is
not a backup or an access control: arrange private storage/versioning separately, and
check `git status` before publishing changes.

## 2. Supply local context and working directories

Adapt [terminology](../.claude/rules/terminology.md) to your vocabulary, distinctions, and
exceptions. Retain the [output standards](../.claude/rules/output-standards.md) and review
the [safety rules](../.claude/rules/safety.md) against your organization's requirements.
Keep always-loaded rules short; put stage-specific instructions in skills.

Provide evidence-backed user roles, problem statements, strategy, and the location of the
actual product codebase to the relevant task. The feasibility reviewer needs real source
files; this operating-model repository is not evidence of your product's capabilities.
Use synthetic data for a first pilot.

Create the working area before capture: `/capture` explicitly appends to an existing
inbox and must not create a new file. The following initialization preserves an existing
inbox:

```bash
mkdir -p work/capture work/pipeline work/outcomes work/decisions work/knowledge/competitive
touch work/capture/inbox.md
```

## 3. Close the gaps before relying on them

| Dependency | What this checkout provides | Adoption action |
|---|---|---|
| PRD template | `/prd` references `templates/prd.md`, but the file is absent | Supply a local template covering the skill's requirements and graduation checklist before running `/prd`. |
| Competitive analysis template | The skill references absent `templates/competitive-analysis.md` | Supply it before using that workflow. |
| Additional commands | `/promote`, `/sketch`, `/archive`, and `/exec-update` are referenced but have no shipped skills | Define your local procedure or add and validate a skill; do not assume these commands work. |
| Lifecycle enforcement | No `.claude/hooks/` scripts or `.claude/settings.json` wiring | Treat gates as operator-reviewed checklists until you implement and test enforcement. |
| Judge evaluations | Example JSON with expected verdicts | Supply fixtures, an oracle, and a runner before claiming measured judge accuracy. |

The PRD skill calls for JTBD, feasibility before requirements, baseline/target/measurement
mechanism for metrics, owned open questions, Gherkin criteria, and review. Include scope
and explicit dependencies with status so reviewers can check consistency and graduation.
These are requirements to satisfy locally, not a shipped template to copy.

Reviewers are instructed to report rather than edit. Three grant only `Read, Grep, Glob`;
`tech-feasibility` also grants `Bash`. If you need a mechanically read-only review, assess
and restrict that shell capability in your environment. The prose instruction alone does
not prevent a shell write.

## 4. Pilot one initiative

1. Run `/capture` on a synthetic observation. Check the inbox entry has a sequential
   `CAP-NNN` ID, date, raw input, and provenance.
2. Run `/triage`. Verify a route and date for every processed item, reasons for declines,
   and revisit triggers for parks. Preserve the original entries.
3. For a promoted item, use an explicit local procedure to create
   `work/pipeline/<slug>/README.md`, retaining the capture evidence. `/promote` is absent.
4. Once the problem, user role, and decision to proceed are established, use `/prd` with
   your supplied template and product codebase. If the idea needs a prototype first,
   arrange that locally; `/sketch` is absent.
5. Run `/review`, resolve or explicitly accept findings, then run `/graduate`. Inspect
   the per-criterion report and README stamp. Graduation must leave artifact paths intact.
6. After shipping and collecting actuals, use `/metrics-review`. Missing instrumentation
   must remain an explicit unmeasurable finding, not a substituted proxy.

## 5. Validate your adaptation

Check output paths, evidence citations, metric mechanisms, Gherkin criteria, and accepted
risks against the skills. Try a deliberately incomplete PRD: graduation should report
failed criteria and add no stamp. Confirm reviewers do not edit the source document.
If you add hooks, separately test both allowed and disallowed writes and confirm the
hooks are wired into the tool calls you use. A checklist pass does not establish hook
coverage. No runnable test suite or hook-validation command is shipped here.

Keep adoption changes reviewable and document local choices. Add integrations only when
needed; the on-disk artifact remains the interface between stages.
