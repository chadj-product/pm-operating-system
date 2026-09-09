---
name: competitive-analysis
description: >
  Research a competitor or market segment and produce a sourced teardown with a
  recommended response. Use for "competitive analysis", "how does X compare", "teardown",
  "who else does this". Every claim must be dated and sourced.
---

# Competitive analysis

## The rule that matters more than any other

**Every factual claim about another company carries a dated primary source.** Ownership,
funding, customer counts, capabilities, pricing — all of it.

Vendor landscapes churn constantly through acquisition, rebranding, and partnership
announcements that read like acquisitions. Stating that Company A owns Product B without a
citation is the single fastest way to destroy your credibility with a sales team, because
somebody in that room talks to those competitors every week and will know.

If a claim cannot be sourced, write `unverified` next to it. That is a useful, honest output.
An unsourced assertion is not.

## Procedure

1. **Scope it.** Which segment, which buyer, which job. "Competitors" is not a scope — many
   products overlap on features while competing for entirely different budgets, and those are
   not competitors in any way that affects your roadmap.
2. **Gather**, preferring in this order: the vendor's own documentation and pricing pages →
   regulatory or public filings → analyst coverage → third-party reviews → press. Record the
   URL and the access date for each.
3. **Map capability, not marketing.** Build the comparison on jobs the buyer is trying to get
   done. A feature checklist rewards whoever writes the longest feature list.
4. **Rate honestly, and separate rating sources.** Never chart your own assessment of a
   competitor against that competitor's self-assessment — the scales are incomparable and the
   resulting chart is worse than no chart, because it looks rigorous.
5. **Recommend a response.** Match, differentiate, ignore, or partner. Ignoring is a legitimate
   answer and is frequently correct.

## Output

`work/knowledge/competitive/<competitor>-<date>.md`, using `templates/competitive-analysis.md`.
Date it in the filename — competitive intelligence has a half-life measured in months, and an
undated teardown will be quoted as current two years later.

## Anti-patterns

- **Chart-driven conclusions.** Building the visual first and then finding numbers to fill it.
- **Averaging your own scoring with the vendor's.** See rule 4.
- **Treating an integration partner as a competitor** because their logo appears in the same
  diagram.
- **Assuming a capability exists because it is on the website.** Marketing pages describe
  roadmaps in the present tense. Where it matters, find a customer-facing artifact — release
  notes, documentation, a support article.

## Exit condition

Every claim sourced and dated, or marked `unverified`. A recommended response with a reason.
Filename carries the date.
